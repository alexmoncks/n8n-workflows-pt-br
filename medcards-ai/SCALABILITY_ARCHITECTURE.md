# MEDCARDS.AI - Arquitetura de Escalabilidade & Infraestrutura Técnica

## 🎯 Filosofia de Escalonamento

**Construa para 10 mil usuários, arquitete para 1 milhão de usuários.**

Este documento descreve como o MEDCARDS.AI escala do MVP (1 mil usuários) até plataforma (1 milhão+ de usuários) sem grandes reescritas.

---

## 📊 Estágios de Crescimento & Evolução da Infraestrutura

### Estágio 1: MVP (0-10 mil usuários)
**Usuários Ativos Mensais**: 0-10.000
**Interações Diárias**: 0-100 mil
**Custo de Infraestrutura**: US$ 500-1.000/mês

**Stack:**
```
Frontend: Vercel Edge Network
Backend: Next.js Server Actions (Vercel Serverless)
Database: Supabase Free/Pro (PostgreSQL)
AI: Anthropic Claude API (pagamento por uso)
Cache: Nenhum (apenas banco de dados)
CDN: Vercel automático
```

**Por que funciona:**
- Serverless escala automaticamente
- Nenhum DevOps necessário
- Pague apenas pelo uso
- Deploy em minutos

**Gargalos:**
- Nenhum nesta escala
- O banco de dados tem limite de 10GB (suficiente para 10 mil usuários)

---

### Estágio 2: Crescimento (10 mil-100 mil usuários)
**Usuários Ativos Mensais**: 10.000-100.000
**Interações Diárias**: 100 mil-1 milhão
**Custo de Infraestrutura**: US$ 2.000-5.000/mês

**Upgrades da Stack:**
```
Frontend: Vercel Edge Network (igual)
Backend: Next.js Server Actions (igual)
Database: Supabase Pro → plano Team
  - Connection pooling (pgBouncer)
  - Read replicas para analytics
  - 100GB de armazenamento
AI: Anthropic Claude API + Cache de respostas
Cache: Upstash Redis (Vercel KV)
  - Cache das respostas de IA (TTL de 24h)
  - Cache das sessões de usuário
  - Rate limiting
CDN: CloudFlare na frente do Vercel (opcional)
Monitoring: Vercel Analytics + Sentry
```

**Padrão de Arquitetura:**

```typescript
// lib/cache/redis.ts
import { Redis } from '@upstash/redis';

const redis = Redis.fromEnv();

export async function getCachedAIResponse(cacheKey: string) {
  return await redis.get(cacheKey);
}

export async function setCachedAIResponse(
  cacheKey: string,
  response: any,
  ttlSeconds: number = 86400 // 24 horas
) {
  await redis.setex(cacheKey, ttlSeconds, JSON.stringify(response));
}

// Uso na geração de feedback de IA
export async function generateFeedback(context: FeedbackContext): Promise<AIFeedback> {
  const cacheKey = `feedback:${context.case.id}:${context.student_answer.selected_answer_id}`;

  // Tenta o cache primeiro
  const cached = await getCachedAIResponse(cacheKey);
  if (cached) {
    console.log('Cache hit for feedback');
    return JSON.parse(cached as string);
  }

  // Gera novo feedback
  const feedback = await callClaudeAPI(context);

  // Faz cache para futuros estudantes
  await setCachedAIResponse(cacheKey, feedback);

  return feedback;
}
```

**Otimizações do Banco de Dados:**

```sql
-- Particiona a tabela interactions por mês (reduz o tempo de consulta)
CREATE TABLE interactions_2025_01 PARTITION OF interactions
FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');

CREATE TABLE interactions_2025_02 PARTITION OF interactions
FOR VALUES FROM ('2025-02-01') TO ('2025-03-01');

-- Índices para consultas quentes
CREATE INDEX CONCURRENTLY idx_interactions_user_recent
ON interactions(user_id, created_at DESC)
WHERE created_at > NOW() - INTERVAL '30 days';

-- View materializada para estatísticas do dashboard (atualiza a cada hora)
CREATE MATERIALIZED VIEW user_stats_cache AS
SELECT
  user_id,
  COUNT(*) as total_cases,
  AVG(CASE WHEN is_correct THEN 1.0 ELSE 0.0 END) as success_rate,
  MAX(created_at) as last_activity
FROM interactions
GROUP BY user_id;

CREATE UNIQUE INDEX ON user_stats_cache(user_id);

-- Atualização automática via pg_cron
SELECT cron.schedule('refresh-user-stats', '0 * * * *',
  'REFRESH MATERIALIZED VIEW CONCURRENTLY user_stats_cache');
```

**Desempenho Esperado:**
- Tempo de resposta da API: <200ms (p95)
- Tempo de consulta ao banco: <50ms (p95)
- Tempo de resposta da IA: 1-3s (dependendo da Claude API)
- Taxa de cache hit: 70-80% para operações comuns

---

### Estágio 3: Escala (100 mil-1 milhão de usuários)
**Usuários Ativos Mensais**: 100.000-1.000.000
**Interações Diárias**: 1 milhão-10 milhões
**Custo de Infraestrutura**: US$ 10.000-30.000/mês

**Principais Mudanças de Arquitetura:**

#### 1. **Estratégia de Sharding do Banco de Dados**

**Shard por User ID** (a maioria das consultas tem escopo de usuário):

```sql
-- Shard 1: Usuários com hash do ID % 4 = 0
-- Shard 2: Usuários com hash do ID % 4 = 1
-- Shard 3: Usuários com hash do ID % 4 = 2
-- Shard 4: Usuários com hash do ID % 4 = 3

-- Lógica de roteamento na aplicação
function getShardForUser(userId: string): number {
  const hash = hashUserId(userId);
  return hash % 4;
}

// Pool de conexões por shard
const shardConnections = {
  0: createSupabaseClient(SHARD_0_URL),
  1: createSupabaseClient(SHARD_1_URL),
  2: createSupabaseClient(SHARD_2_URL),
  3: createSupabaseClient(SHARD_3_URL),
};

export function getDbForUser(userId: string) {
  const shard = getShardForUser(userId);
  return shardConnections[shard];
}
```

**Consultas entre shards** (rankings, analytics) vão para read replicas ou data warehouse.

#### 2. **Otimização da Infraestrutura de IA**

**Problema**: Os custos da Claude API escalam linearmente (1 milhão+ de usuários = US$ 50 mil+/mês em custos de IA)

**Solução**: Estratégia de IA multicamada

```typescript
// Tier 1: Respostas pré-computadas (instantâneas, gratuitas)
// Para combinações comuns de caso + resposta (80% do tráfego)
const precomputedFeedback = await db
  .from('precomputed_feedback')
  .select('*')
  .eq('case_id', caseId)
  .eq('selected_answer', answerId)
  .single();

if (precomputedFeedback) return precomputedFeedback;

// Tier 2: Respostas em cache (rápidas, baratas)
// Para combinações menos comuns (15% do tráfego)
const cached = await redis.get(`feedback:${caseId}:${answerId}`);
if (cached) return JSON.parse(cached);

// Tier 3: Geração de IA em tempo real (lenta, cara)
// Para combinações raras ou usuários premium (5% do tráfego)
const feedback = await generateWithClaude(context);
await redis.setex(`feedback:${caseId}:${answerId}`, 86400, JSON.stringify(feedback));
return feedback;
```

**Impacto no Custo:**
- Antes: 1 milhão de chamadas de API/dia × US$ 0,003 = US$ 3.000/dia = US$ 90.000/mês
- Depois: 50 mil chamadas de API/dia × US$ 0,003 = US$ 150/dia = US$ 4.500/mês
- **Economia**: US$ 85.500/mês (redução de 95%)

#### 3. **Processamento de Jobs em Background**

**Mova operações pesadas para fora do caminho da requisição:**

```typescript
// lib/jobs/queue.ts
import { Inngest } from 'inngest';

const inngest = new Inngest({ name: 'MedCards' });

// Operações pesadas rodam de forma assíncrona
export const calculateUserMetrics = inngest.createFunction(
  { name: 'Calculate User Metrics' },
  { event: 'user/interaction.created' },
  async ({ event }) => {
    const userId = event.data.userId;

    // Recalcula todas as estatísticas do usuário
    const stats = await computeComprehensiveStats(userId);

    // Atualiza o banco de dados
    await db.from('users').update({ progress: stats }).eq('id', userId);

    // Verifica desbloqueios de badges
    await checkBadgeUnlocks(userId, stats);

    // Atualiza os rankings
    await updateLeaderboards(userId, stats);
  }
);

// Notificações de desbloqueio de badge
export const notifyBadgeUnlock = inngest.createFunction(
  { name: 'Notify Badge Unlock' },
  { event: 'badge/unlocked' },
  async ({ event }) => {
    // Enviar email
    // Push notification
    // Atualizar UI via WebSocket
  }
);
```

**Benefícios:**
- Tempo de resposta da API: 2s → 200ms
- Melhor experiência do usuário
- Pode reprocessar jobs que falharam
- Escala os workers de forma independente

#### 4. **Separação de Leitura/Escrita**

```typescript
// lib/db/routing.ts

// Operações de escrita → Banco de dados primário
export async function writeInteraction(data: InteractionData) {
  return await primaryDb.from('interactions').insert(data);
}

// Operações de leitura → Read replicas (distribui a carga)
const readReplicas = [replicaDb1, replicaDb2, replicaDb3];
let currentReplica = 0;

export async function getUser Interactions(userId: string) {
  const db = readReplicas[currentReplica % readReplicas.length];
  currentReplica++;

  return await db
    .from('interactions')
    .select('*')
    .eq('user_id', userId)
    .order('created_at', { ascending: false })
    .limit(20);
}
```

#### 5. **Otimização de CDN & Assets Estáticos**

```typescript
// next.config.ts
export default {
  images: {
    loader: 'cloudinary', // Ou imgix, cloudflare
    domains: ['res.cloudinary.com'],
  },
  // Serve assets pesados a partir da CDN
  assetPrefix: process.env.CDN_URL,
};
```

**Estratégia de Assets:**
- Imagens de casos → CloudFlare R2 (compatível com S3, mais barato)
- Avatares de usuário → CloudFlare Images (auto-otimização)
- Explicações em vídeo → Mux (CDN de streaming de vídeo)

---

### Estágio 4: Plataforma (1 milhão+ de usuários)
**Usuários Ativos Mensais**: 1 milhão+
**Interações Diárias**: 10 milhões+
**Custo de Infraestrutura**: US$ 50.000-100.000/mês

**Arquitetura Completa de Microsserviços:**

```
┌─────────────────────────────────────────────────────────┐
│                    CloudFlare CDN                        │
└─────────────────────┬───────────────────────────────────┘
                      │
        ┌─────────────┴─────────────┐
        │     Load Balancer         │
        └─────────────┬─────────────┘
                      │
        ┌─────────────┴─────────────────────────────┐
        │                                             │
┌───────▼────────┐                          ┌────────▼────────┐
│  Web Frontend  │                          │   Mobile API    │
│  (Vercel Edge) │                          │   (Dedicated)   │
└───────┬────────┘                          └────────┬────────┘
        │                                             │
        └─────────────┬───────────────────────────────┘
                      │
        ┌─────────────▼──────────────────────┐
        │         API Gateway                │
        │    (Rate limiting, Auth)           │
        └─────────────┬──────────────────────┘
                      │
        ┌─────────────┴──────────────────────────────┐
        │                                              │
┌───────▼──────────┐  ┌────────────┐  ┌─────────────▼────────┐
│  User Service    │  │   Cache    │  │   Case Service       │
│  (Supabase)      │  │  (Redis)   │  │   (Dedicated DB)     │
└──────────────────┘  └────────────┘  └──────────────────────┘
        │                                              │
        │              ┌─────────────┐                 │
        └──────────────►   AI Service ◄────────────────┘
                       │  (Claude +   │
                       │   Fine-tune) │
                       └──────┬───────┘
                              │
                    ┌─────────▼──────────┐
                    │  Analytics Service │
                    │   (ClickHouse)     │
                    └────────────────────┘
```

**Detalhamento dos Serviços:**

| Serviço | Tecnologia | Objetivo |
|---------|------|---------|
| User Service | Supabase | Perfis de usuário, auth, progresso |
| Case Service | PostgreSQL dedicado | Casos clínicos, interações |
| AI Service | Claude API + Modelos customizados | Feedback, coaching, adaptativo |
| Analytics | ClickHouse | Analytics em tempo real, dashboards |
| Search | Elasticsearch | Busca de casos, busca de usuários |
| Notifications | Pusher / Socket.io | Atualizações em tempo real |
| Jobs | Temporal | Processamento em background |
| Cache | Redis Cluster | Cache multicamada |

---

## 💰 Detalhamento de Custos por Estágio

### Estágio 1: MVP (10 mil usuários)
```
Vercel Pro:              US$ 20/mês
Supabase Pro:           US$ 25/mês
Anthropic API:         US$ 300/mês  (100 mil chamadas de IA)
Domínio + SSL:          US$ 15/mês
Monitoring:             US$ 50/mês
──────────────────────────────────
TOTAL:                 US$ 410/mês
Custo por usuário:     US$ 0,041/mês
```

### Estágio 2: Crescimento (100 mil usuários)
```
Vercel Enterprise:     US$ 500/mês
Supabase Team:         US$ 599/mês
Anthropic API:       US$ 1.500/mês  (500 mil chamadas de IA, 70% em cache)
Upstash Redis:         US$ 200/mês
CloudFlare Pro:         US$ 20/mês
Sentry:                US$ 100/mês
──────────────────────────────────
TOTAL:               US$ 2.919/mês
Custo por usuário:    US$ 0,029/mês
```

### Estágio 3: Escala (1 milhão de usuários)
```
Vercel Enterprise:   US$ 2.000/mês
Supabase (4 shards): US$ 2.400/mês  (US$ 600 cada)
Anthropic API:       US$ 4.500/mês  (95% em cache)
Redis Cluster:       US$ 1.000/mês
CloudFlare:            US$ 200/mês
Sentry:                US$ 500/mês
Inngest (jobs):        US$ 300/mês
Datadog:               US$ 800/mês
──────────────────────────────────
TOTAL:              US$ 11.700/mês
Custo por usuário:   US$ 0,012/mês
```

**Insight-Chave**: O custo por usuário DIMINUI conforme você escala (economias de escala).

---

## 🔥 Metas de Desempenho

### Tempos de Resposta da API (p95)
- **Carregamento da homepage**: <500ms
- **Carregamento do dashboard**: <800ms
- **Apresentação do caso**: <300ms
- **Enviar resposta**: <400ms
- **Feedback de IA**: <2s (com streaming)
- **Mensagem de chat**: <500ms (streaming)

### Tempos de Consulta ao Banco de Dados (p95)
- **SELECT simples**: <10ms
- **JOIN complexo**: <50ms
- **Consulta de analytics**: <200ms
- **Ranking**: <100ms (em cache)

### Disponibilidade
- **SLA de uptime**: 99,9% (8,76 horas de downtime/ano)
- **Deploys sem downtime**: Obrigatório
- **Recuperação de desastres**: RPO/RTO < 15 minutos

---

## 🛡️ Confiabilidade & Monitoramento

### Orçamento de Erros
```
Meta de Uptime Mensal: 99,9%
Orçamento de Erros: 0,1% = 43 minutos de downtime/mês

Semana 1: 5 minutos → 37 minutos restantes
Semana 2: 10 minutos → 27 minutos restantes
Semana 3: 30 minutos → -3 minutos (EXCEDIDO!)
  → Congelar lançamentos de recursos
  → Focar em estabilidade
  → Análise de causa raiz
```

### Stack de Monitoramento

```typescript
// lib/monitoring/metrics.ts
import * as Sentry from '@sentry/nextjs';
import { track } from '@vercel/analytics';

// Rastreia todas as chamadas de API
export async function monitoredAPICall<T>(
  operation: string,
  fn: () => Promise<T>
): Promise<T> {
  const startTime = Date.now();

  try {
    const result = await fn();
    const duration = Date.now() - startTime;

    // Métricas de sucesso
    track('api_call_success', {
      operation,
      duration,
    });

    return result;
  } catch (error) {
    // Rastreamento de erros
    Sentry.captureException(error, {
      tags: { operation },
      extra: { duration: Date.now() - startTime },
    });

    // Métricas de erro
    track('api_call_error', {
      operation,
      error: error.message,
    });

    throw error;
  }
}

// Uso
export async function submitAnswer(data: AnswerData) {
  return monitoredAPICall('submit_answer', async () => {
    // ... implementação real
  });
}
```

### Configuração de Alertas

```yaml
alerts:
  - name: High Error Rate
    condition: error_rate > 5%
    window: 5 minutes
    severity: critical
    notify: pagerduty

  - name: Slow API Responses
    condition: p95_latency > 2 seconds
    window: 10 minutes
    severity: warning
    notify: slack

  - name: Database Connection Pool Exhaustion
    condition: available_connections < 10
    severity: critical
    notify: pagerduty

  - name: AI API Rate Limit Approaching
    condition: anthropic_remaining_requests < 100
    severity: warning
    notify: slack

  - name: Daily Active Users Drop
    condition: dau_vs_yesterday_decrease > 20%
    severity: warning
    notify: slack
```

---

## 📈 Planejamento de Capacidade

### Projeções de Crescimento de Usuários

```
Mês 1:      100 usuários
Mês 3:    1.000 usuários  (crescimento de 10x)
Mês 6:   10.000 usuários  (crescimento de 10x)
Mês 12:  50.000 usuários  (crescimento de 5x)
Mês 18: 150.000 usuários (crescimento de 3x)
Mês 24: 500.000 usuários (crescimento de 3,3x)
```

### Gatilhos de Escalonamento de Infraestrutura

| Métrica | Gatilho | Ação |
|--------|---------|--------|
| CPU do Banco | >70% por 1h | Adicionar read replica |
| Armazenamento do Banco | >80% usado | Fazer upgrade do plano OU arquivar dados antigos |
| Taxa de Erro da API | >5% por 5min | Escalar serverless OU fazer rollback |
| Memória do Redis | >80% usada | Fazer upgrade OU implementar LRU eviction |
| Custos da AI API | >US$ 10 mil/mês | Implementar cache agressivo |

### Checklist de Escalonamento

**Em 10 mil usuários:**
- [ ] Habilitar cache Redis
- [ ] Adicionar índices ao banco de dados
- [ ] Configurar monitoramento
- [ ] Implementar rate limiting

**Em 50 mil usuários:**
- [ ] Adicionar read replicas
- [ ] Implementar fila de jobs
- [ ] Cache agressivo das respostas de IA
- [ ] CloudFlare Pro

**Em 100 mil usuários:**
- [ ] Sharding do banco de dados
- [ ] Arquitetura de microsserviços
- [ ] Banco de dados dedicado para analytics
- [ ] Otimização de entrega de conteúdo

---

## 🚀 Estratégia de Deploy

### Deploys Sem Downtime

```bash
# Deploy Blue-Green no Vercel
1. Fazer deploy da nova versão em staging
2. Rodar smoke tests
3. Fazer deploy em produção (o Vercel cuida do canary rollout)
4. Monitorar taxas de erro por 15 minutos
5. Se os erros dispararem: rollback automático
6. Se estável: rollout completo
```

### Migrações de Banco de Dados

```typescript
// migrations/0015_add_community_cases.ts
export async function up() {
  // Migração segura: apenas aditiva
  await db.schema
    .createTable('community_cases')
    .addColumn('id', 'uuid', (col) => col.primaryKey())
    .addColumn('created_at', 'timestamp')
    // ... outras colunas
    .execute();
}

export async function down() {
  // Rollback (mas nunca rode em produção!)
  await db.schema.dropTable('community_cases').execute();
}
```

**Regras de Migração:**
1. Nunca remova colunas (marque como deprecated em vez disso)
2. Adicione novas colunas como nullable
3. Faça o backfill dos dados de forma assíncrona
4. Teste em staging com um snapshot de dados de produção

---

## 🔒 Segurança em Escala

### Rate Limiting

```typescript
// middleware.ts
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(100, '1 m'), // 100 requisições por minuto
});

export async function middleware(request: Request) {
  const ip = request.headers.get('x-forwarded-for') ?? 'unknown';
  const { success, limit, remaining } = await ratelimit.limit(ip);

  if (!success) {
    return new Response('Rate limit exceeded', { status: 429 });
  }

  return NextResponse.next();
}
```

### Proteção contra DDoS

```
CloudFlare WAF → Vercel → Aplicação

- CloudFlare: Bloqueia IPs maliciosos, rate limit por IP
- Vercel: Proteção na edge, mitigação de DDoS
- Aplicação: Rate limits em nível de usuário
```

### Criptografia de Dados

```
- Em Repouso: O Supabase criptografa todos os dados (AES-256)
- Em Trânsito: TLS 1.3 em todos os lugares
- Backups: Criptografados, distribuídos geograficamente
- Segredos: Gerenciados via variáveis de ambiente do Vercel
```

---

## 📊 Arquitetura de Analytics

### Analytics em Tempo Real

```sql
-- Tabela ClickHouse para analytics em tempo real (melhor que PostgreSQL para OLAP)
CREATE TABLE analytics.interactions (
    user_id UUID,
    case_id UUID,
    is_correct Boolean,
    time_to_answer Int32,
    created_at DateTime,
    specialty String
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(created_at)
ORDER BY (created_at, user_id);

-- Agregações rápidas
SELECT
    specialty,
    COUNT(*) as total,
    AVG(is_correct) as success_rate
FROM analytics.interactions
WHERE created_at > now() - INTERVAL 7 DAY
GROUP BY specialty;

-- Executa em <50ms em 100 milhões de linhas
```

### Estratégia de Data Warehouse

```
DB Operacional (PostgreSQL) → CDC → Data Warehouse (ClickHouse)
                                   ↓
                            Dashboard de Analytics (Metabase/Looker)
```

---

## 🎯 Resumo: Caminho de Escalonamento

```
MVP (0-10k):        Stack simples, processos manuais, bom o suficiente
Crescimento (10-100k): Adicionar cache, otimizar banco de dados, automatizar
Escala (100k-1M):   Sharding, microsserviços, jobs em background
Plataforma (1M+):   Distribuição total, serviços dedicados, ML ops

Filosofia: Escale progressivamente, não prematuramente.
Construa o que você precisa HOJE, arquitete para AMANHÃ.
```

**Próximos Passos**: Implementar a stack do MVP, monitorar métricas, escalar quando os gatilhos forem atingidos.
