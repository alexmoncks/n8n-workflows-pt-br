# MEDCARDS.AI 🏥

> Plataforma de preparação para provas de residência médica com IA para estudantes de medicina brasileiros

O **MEDCARDS.AI** não é uma plataforma de cursos tradicional. É um companheiro de estudos inteligente que se adapta à jornada de aprendizagem de cada estudante, entregando treinamento personalizado com casos clínicos, impulsionado pela Claude AI.

---

## 🎯 Visão do Produto

Os estudantes não acessam módulos ou aulas. Eles interagem com um coach de IA que sabe:
- Exatamente onde eles estão na preparação
- Quais padrões clínicos precisam dominar
- Como levá-los até a aprovação

**Três telas principais:**
1. **Battle Dashboard** - Métricas de competência clínica em tempo real
2. **Training Arena** - Apresentações adaptativas de casos com feedback de IA
3. **War Room** - Tutor de IA pessoal com memória completa

---

## 🏗️ Arquitetura

### Stack Brutalmente Simples

```
Frontend:  Next.js 14 (App Router) + Tailwind CSS + Shadcn UI
Backend:   Next.js Server Actions (sem backend separado)
Database:  Supabase (PostgreSQL com RLS)
AI:        Anthropic Claude Sonnet 4 API
Deploy:    Vercel (deploy com um clique)
```

### Por Que Esta Stack?

- **Next.js 14**: Server Components + Server Actions = full-stack em uma única base de código
- **Supabase**: PostgreSQL com auth embutido, RLS, assinaturas em tempo real
- **Shadcn UI**: Componentes bonitos de copiar e colar, personalizáveis instantaneamente
- **Claude AI**: Raciocínio de ponta para educação médica
- **Vercel**: Push para fazer deploy, escalonamento automático, zero DevOps

**Tempo de deploy**: 30 minutos do zero à produção.

---

## 📁 Estrutura do Projeto

```
medcards-ai/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (auth)/            # Rotas de autenticação
│   │   ├── dashboard/         # Battle Dashboard
│   │   ├── arena/             # Training Arena
│   │   ├── war-room/          # Chat do Tutor de IA
│   │   └── layout.tsx         # Layout raiz
│   ├── components/            # Componentes React
│   │   ├── ui/               # Componentes Shadcn UI
│   │   ├── dashboard/        # Específicos do dashboard
│   │   ├── arena/            # Específicos da arena
│   │   └── shared/           # Componentes compartilhados
│   ├── lib/                   # Lógica de negócio principal
│   │   ├── ai/               # Integração com Claude AI
│   │   │   └── claude.ts     # Funções de IA (coach, feedback, tutor)
│   │   ├── supabase/         # Utilitários de banco de dados
│   │   │   └── client.ts     # Clientes Supabase
│   │   ├── adaptive/         # Lógica do motor adaptativo
│   │   ├── gamification/     # Badges & progressão
│   │   └── utils/            # Helpers
│   └── types/                # Tipos TypeScript
│       └── database.ts       # Tipos do schema do banco de dados
├── supabase/
│   ├── schema.sql            # Schema do banco de dados
│   ├── seed-cases.sql        # Casos clínicos iniciais
│   └── migrations/           # Migrações do banco de dados
├── prompts/
│   ├── coach-prompt.md       # Instruções do Coach de IA
│   ├── feedback-prompt.md    # Gerador de Feedback de IA
│   └── tutor-prompt.md       # Tutor de IA (chat)
├── public/                   # Assets estáticos
├── .env.example              # Template de ambiente
├── package.json
├── tsconfig.json
├── tailwind.config.ts
└── README.md
```

---

## 🚀 Início Rápido

### Pré-requisitos

- Node.js 18+
- npm ou yarn
- Conta Supabase (o plano gratuito funciona)
- Chave de API da Anthropic (acesso ao Claude)

### 1. Clonar e Instalar

```bash
git clone <repository-url>
cd medcards-ai
npm install
```

### 2. Configurar o Supabase

1. Crie um projeto em [supabase.com](https://supabase.com)
2. Execute o schema:
   ```bash
   # Copie o schema para o SQL Editor do Supabase e execute
   cat supabase/schema.sql
   ```
3. (Opcional) Faça o seed dos casos iniciais:
   ```bash
   cat supabase/seed-cases.sql
   ```
4. Obtenha suas credenciais em Project Settings → API

### 3. Configurar o Ambiente

```bash
cp .env.example .env.local
```

Edite o `.env.local`:
```env
NEXT_PUBLIC_SUPABASE_URL=your-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
ANTHROPIC_API_KEY=sk-ant-your-api-key
```

### 4. Rodar o Servidor de Desenvolvimento

```bash
npm run dev
```

Abra [http://localhost:3000](http://localhost:3000)

### 5. Fazer Deploy para Produção

```bash
# Conectar ao Vercel
npx vercel

# Deploy
npx vercel --prod
```

Adicione as variáveis de ambiente no dashboard do Vercel.

**Pronto.** Sua plataforma está no ar.

---

## 🧠 Sistemas Principais

### 1. Motor Adaptativo

**Objetivo**: Selecionar o próximo caso ideal para cada estudante

**Algoritmo**:
```
1. Calcular competência por especialidade (ponderada pela recência)
2. Identificar lacunas (success_rate < 65%)
3. Selecionar o próximo caso:
   - 60%: Abordar lacunas críticas
   - 30%: Reforçar pontos fortes
   - 10%: Explorar novas áreas
4. Claude AI valida a seleção e prepara o coaching
```

**Localização**: `src/lib/adaptive/engine.ts`

### 2. Sistema de Coach de IA

**Três Prompts Especializados**:

1. **Coach Prompt** (`prompts/coach-prompt.md`)
   - Analisa o histórico do estudante
   - Seleciona o próximo caso ideal
   - Prepara dicas graduadas
   - Retorna JSON estruturado

2. **Feedback Prompt** (`prompts/feedback-prompt.md`)
   - Analisa a resposta do estudante
   - Identifica lacunas de raciocínio
   - Fornece explicação clínica detalhada
   - Sugere próximos passos de prática

3. **Tutor Prompt** (`prompts/tutor-prompt.md`)
   - Coaching conversacional
   - Memória completa da jornada do estudante
   - Conselhos específicos e orientados por dados
   - Apoio motivacional

**Integração**: `src/lib/ai/claude.ts`

### 3. Sistema de Gamificação

**Badges**:
- Primeira Vitória, Sequências, Velocidade, Domínio de Especialidade
- Desbloqueio automático via triggers do Supabase
- Comemorações animadas (Framer Motion)

**Progressão**:
- Pontos de experiência por caso
- Sistema de subida de nível
- Desbloqueio de casos avançados em níveis mais altos

**Localização**: `src/lib/gamification/`

---

## 📊 Schema do Banco de Dados

### Tabelas Principais

**users**
- Perfil, progresso (JSONB), preferências, assinatura

**clinical_cases**
- Conteúdo do caso, opções, explicações
- Dificuldade, especialidade, tags
- Estatísticas globais (taxa de acerto, tempo médio)

**interactions**
- Cada resposta do estudante é registrada
- Feedback de IA armazenado como JSONB
- Usado pelo algoritmo adaptativo

**chat_history**
- Conversas do Tutor de IA
- Contexto completo mantido

**badges** + **user_badges**
- Conquistas de gamificação

### Recursos-Chave

- **Row Level Security (RLS)**: Usuários só veem seus próprios dados
- **Triggers automáticos**: Atualizam as estatísticas dos casos a cada interação
- **Campos JSONB**: Rastreamento flexível de progresso sem alterações de schema
- **Índices**: Otimizados para consultas comuns

**Schema completo**: `supabase/schema.sql`

---

## 🎨 Design System

### Cores

- **Azul Primário** (`#0A2463`): Confiança médica, ações principais
- **Verde Cirúrgico** (`#06D6A0`): Sucesso, respostas corretas
- **Vermelho de Alerta** (`#EF4444`): Erros, alertas críticos
- **Escala de Cinza**: Neutros da interface

### Tipografia

- **Interface**: Inter (excelente legibilidade)
- **Conteúdo Clínico**: Crimson Pro (sensação médica séria)

### Espaçamento

Escala matemática (base de 8px): 8, 16, 24, 32, 48, 64
Cria uma consistência visual subconsciente.

### Animações

- Fade in: Carregamento de conteúdo
- Slide up: Novos casos
- Pulse success: Feedback de resposta correta
- Confetti: Badge desbloqueada

**Config**: `tailwind.config.ts`

---

## 🛠️ Fluxo de Desenvolvimento

### Implementação Baseada em Sprints

**8 semanas até o MVP** (seguindo a spec em `CLAUDE.md`):

1. **Semana 1**: Fundação (schema, auth, deploy)
2. **Semana 2**: Battle Dashboard
3. **Semana 3**: Training Arena (básica)
4. **Semana 4**: Integração de IA (feedback do Claude)
5. **Semana 5**: War Room (chat)
6. **Semana 6**: Motor Adaptativo
7. **Semana 7**: Gamificação
8. **Semana 8**: Polimento & Analytics

### Comandos-Chave

```bash
# Desenvolvimento
npm run dev              # Iniciar servidor de dev
npm run build            # Build de produção
npm run type-check       # Validação de TypeScript
npm run lint             # ESLint

# Banco de Dados
npm run db:migrate       # Rodar migrações (se usar o Supabase CLI)
npm run db:seed          # Fazer seed dos casos iniciais

# Deploy
npx vercel --prod        # Deploy para produção
```

---

## 📈 Métricas Que Importam

Acompanhe **apenas estas quatro** semanalmente:

1. **Retenção Dia 7**: % de usuários que retornam após 1 semana
   - Meta: 40% (inicial) → 60% (pós-PMF)

2. **Casos por Sessão**: Quantos casos por sessão de estudo
   - Meta: 8-12 casos

3. **Tempo até a Primeira Vitória**: Minutos até a primeira sequência de 3 casos
   - Meta: < 15 minutos

4. **Conversão Free→Paid**: % que paga após 50 casos
   - Meta: 10% (inicial) → 25% (otimizado)

**Ignore**: Total de usuários, page views, tempo no app (métricas de vaidade)

---

## 🔐 Segurança & Privacidade

- **Autenticação**: Supabase Auth (email/senha, login social)
- **Autorização**: Row Level Security (RLS) em todas as tabelas
- **Chaves de API**: Apenas no lado do servidor (nunca expostas ao cliente)
- **Privacidade de Dados**: Dados dos estudantes nunca compartilhados, em conformidade com a LGPD

---

## 🧪 Estratégia de Testes

### Estado Atual
Testes manuais durante o desenvolvimento (abordagem de MVP indie hacker)

### Futuro (Pós-PMF)
- Testes unitários: Lógica de negócio crítica (motor adaptativo)
- Testes de integração: Parsing das respostas da IA
- Testes E2E: Fluxos críticos de usuário (Playwright)

**Filosofia**: Lançar rápido, testar o que quebra em produção e depois adicionar testes.

---

## 📚 Referência de Arquivos-Chave

| Arquivo | Objetivo |
|------|---------|
| `supabase/schema.sql` | Schema completo do banco de dados |
| `prompts/coach-prompt.md` | Instruções de seleção de casos da IA |
| `prompts/feedback-prompt.md` | Instruções de análise de resposta da IA |
| `prompts/tutor-prompt.md` | Instruções de conversa do chat de IA |
| `src/lib/ai/claude.ts` | Integração com a API do Claude |
| `src/lib/supabase/client.ts` | Utilitários de banco de dados |
| `src/types/database.ts` | Definições de tipo TypeScript |
| `tailwind.config.ts` | Configuração do design system |

---

## 🤝 Contribuindo

Este é um projeto indie hacker otimizado para desenvolvimento solo. Contribuições são bem-vindas, mas mantenha estes princípios:

1. **Simplicidade acima de recursos**: Sem complexidade desnecessária
2. **Lançar rápido**: Código que funciona > código perfeito
3. **Orientado por dados**: Todo recurso deve mover as métricas principais
4. **Usuário em primeiro lugar**: Se os estudantes não precisam, não construa

---

## 📄 Licença

[Adicione sua licença aqui]

---

## 🙋 Suporte

- **Issues**: GitHub Issues
- **Discussões**: GitHub Discussions
- **Email**: [seu-email]

---

## 🎓 Para Estudantes de Medicina

O **MEDCARDS.AI** é feito por desenvolvedores que entendem:
- O estresse das provas de residência
- A necessidade de aprendizagem personalizada e adaptativa
- Que o seu tempo é precioso

Nossa missão: **Aprovar você com o mínimo de tempo de estudo e o máximo de confiança.**

Comece a treinar: [Faça o deploy da sua instância ou visite medcards.ai]

---

**Feito com ❤️ para residentes médicos brasileiros**

*Stack: Next.js 14 • Supabase • Claude AI • Vercel*
