# Plataforma de Documentação de Workflows N8N - Guia de Implantação

Este guia aborda a implantação da Plataforma de Documentação de Workflows N8N em diversos ambientes.

## Início Rápido (Docker)

### Ambiente de Desenvolvimento
```bash
# Clone o repositório
git clone <repository-url>
cd n8n-workflows-1

# Inicie o ambiente de desenvolvimento
docker compose -f docker-compose.yml -f docker-compose.dev.yml up --build
```

### Ambiente de Produção
```bash
# Implantação de produção
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build

# Com monitoramento
docker compose --profile monitoring up -d
```

## Opções de Implantação

### 1. Docker Compose (Recomendado)

#### Desenvolvimento
```bash
# Inicie o ambiente de desenvolvimento com auto-reload
docker compose -f docker-compose.yml -f docker-compose.dev.yml up

# Com ferramentas de desenvolvimento adicionais (admin de BD, monitor de arquivos)
docker compose --profile dev-tools up
```

#### Produção
```bash
# Implantação básica de produção
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d

# Com reverse proxy e SSL
docker compose --profile production up -d

# Com stack de monitoramento
docker compose --profile monitoring up -d
```

### 2. Docker Standalone

```bash
# Construa a imagem
docker build -t workflows-doc:latest .

# Execute o container
docker run -d \
  --name n8n-workflows-docs \
  -p 8000:8000 \
  -v $(pwd)/database:/app/database \
  -v $(pwd)/logs:/app/logs \
  -e ENVIRONMENT=production \
  workflows-doc:latest
```

### 3. Implantação Direta com Python

#### Pré-requisitos
- Python 3.11+
- pip

#### Instalação
```bash
# Instale as dependências
pip install -r requirements.txt

# Modo de desenvolvimento
python run.py --dev

# Modo de produção
python run.py --host 0.0.0.0 --port 8000
```

#### Produção com Gunicorn
```bash
# Instale o gunicorn
pip install gunicorn

# Inicie com o gunicorn
gunicorn -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000 api_server:app
```

### 4. Implantação com Kubernetes

#### Implantação Básica
```bash
# Aplique os manifests do Kubernetes
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

#### Helm Chart
```bash
# Instale com o Helm
helm install n8n-workflows-docs ./helm/workflows-docs
```

## Configuração de Ambiente

### Variáveis de Ambiente

| Variável | Descrição | Padrão | Obrigatória |
|----------|-------------|---------|----------|
| `ENVIRONMENT` | Ambiente de implantação | `development` | Não |
| `LOG_LEVEL` | Nível de log | `info` | Não |
| `HOST` | Host de bind | `127.0.0.1` | Não |
| `PORT` | Porta de bind | `8000` | Não |
| `DATABASE_PATH` | Caminho do banco de dados SQLite | `database/workflows.db` | Não |
| `WORKFLOWS_PATH` | Diretório de workflows | `workflows` | Não |
| `ENABLE_METRICS` | Habilitar métricas do Prometheus | `false` | Não |
| `MAX_WORKERS` | Máximo de processos worker | `1` | Não |
| `DEBUG` | Habilitar modo de debug | `false` | Não |
| `RELOAD` | Habilitar auto-reload | `false` | Não |

### Arquivos de Configuração

Crie uma configuração específica para cada ambiente:

#### `.env` (Desenvolvimento)
```bash
ENVIRONMENT=development
LOG_LEVEL=debug
DEBUG=true
RELOAD=true
```

#### `.env.production` (Produção)
```bash
ENVIRONMENT=production
LOG_LEVEL=warning
ENABLE_METRICS=true
MAX_WORKERS=4
```

## Configuração de Segurança

### 1. Configuração do Reverse Proxy (Traefik)

```yaml
# traefik/config/dynamic.yml
http:
  middlewares:
    auth:
      basicAuth:
        users:
          - "admin:$2y$10$..."  # Gere com htpasswd
    security-headers:
      headers:
        customRequestHeaders:
          X-Forwarded-Proto: "https"
        customResponseHeaders:
          X-Frame-Options: "DENY"
          X-Content-Type-Options: "nosniff"
        sslRedirect: true
```

### 2. Configuração de SSL/TLS

#### Let's Encrypt (Automático)
```yaml
# Em docker-compose.prod.yml
command:
  - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
  - "--certificatesresolvers.myresolver.acme.email=admin@yourdomain.com"
```

#### Certificado SSL Personalizado
```yaml
volumes:
  - ./ssl:/ssl:ro
```

### 3. Autenticação Básica

```bash
# Gere a entrada htpasswd
htpasswd -nb admin yourpassword

# Adicione aos labels do Traefik
- "traefik.http.middlewares.auth.basicauth.users=admin:$$2y$$10$$..."
```

## Otimização de Desempenho

### 1. Limites de Recursos

```yaml
# docker-compose.prod.yml
deploy:
  resources:
    limits:
      memory: 512M
      cpus: '0.5'
    reservations:
      memory: 256M
      cpus: '0.25'
```

### 2. Otimização do Banco de Dados

```bash
# Force a reindexação para melhor desempenho
python run.py --reindex

# Ou via API
curl -X POST http://localhost:8000/api/reindex
```

### 3. Cabeçalhos de Cache

```yaml
# Middleware do Traefik para arquivos estáticos
http:
  middlewares:
    cache-headers:
      headers:
        customResponseHeaders:
          Cache-Control: "public, max-age=31536000"
```

## Monitoramento e Logging

### 1. Health Checks

```bash
# Health check do Docker
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8000/api/stats || exit 1

# Health check manual
curl http://localhost:8000/api/stats
```

### 2. Logs

```bash
# Visualize os logs da aplicação
docker compose logs -f workflows-docs

# Visualize os logs de um serviço específico
docker logs n8n-workflows-docs

# Localização dos logs no container
/app/logs/app.log
```

### 3. Métricas (Prometheus)

```bash
# Inicie o stack de monitoramento
docker compose --profile monitoring up -d

# Acesse o Prometheus
http://localhost:9090
```

## Backup e Recuperação

### 1. Backup do Banco de Dados

```bash
# Backup do banco de dados SQLite
cp database/workflows.db database/workflows.db.backup

# Ou usando docker
docker exec n8n-workflows-docs cp /app/database/workflows.db /app/database/workflows.db.backup
```

### 2. Backup da Configuração

```bash
# Backup de toda a configuração
tar -czf n8n-workflows-backup-$(date +%Y%m%d).tar.gz \
  database/ \
  logs/ \
  docker-compose*.yml \
  .env*
```

### 3. Restauração

```bash
# Pare os serviços
docker compose down

# Restaure o banco de dados
cp database/workflows.db.backup database/workflows.db

# Inicie os serviços
docker compose up -d
```

## Escalabilidade e Balanceamento de Carga

### 1. Múltiplas Instâncias

```yaml
# docker-compose.scale.yml
services:
  workflows-docs:
    deploy:
      replicas: 3
```

```bash
# Escale para cima
docker compose up --scale workflows-docs=3
```

### 2. Configuração do Balanceador de Carga

```yaml
# Balanceamento de carga do Traefik
labels:
  - "traefik.http.services.workflows-docs.loadbalancer.server.port=8000"
  - "traefik.http.services.workflows-docs.loadbalancer.sticky=true"
```

## Solução de Problemas

### Problemas Comuns

1. **Erro de banco de dados bloqueado (database locked)**
   ```bash
   # Verifique as permissões de arquivo
   ls -la database/
   
   # Corrija as permissões
   chmod 664 database/workflows.db
   ```

2. **Porta já em uso**
   ```bash
   # Verifique o que está usando a porta
   lsof -i :8000
   
   # Use uma porta diferente
   docker compose up -d -p 8001:8000
   ```

3. **Sem memória (out of memory)**
   ```bash
   # Verifique o uso de memória
   docker stats
   
   # Aumente o limite de memória
   # Edite os recursos em docker-compose.prod.yml
   ```

### Logs e Depuração

```bash
# Logs da aplicação
docker compose logs -f workflows-docs

# Logs do sistema
docker exec workflows-docs tail -f /app/logs/app.log

# Logs do banco de dados
docker exec workflows-docs sqlite3 /app/database/workflows.db ".tables"
```

## Migração e Atualizações

### 1. Atualizar a Aplicação

```bash
# Baixe as últimas alterações
git pull origin main

# Reconstrua e reinicie
docker compose down
docker compose up -d --build
```

### 2. Migração do Banco de Dados

```bash
# Backup do banco de dados atual
cp database/workflows.db database/workflows.db.backup

# Force a reindexação com o novo schema
python run.py --reindex
```

### 3. Atualizações sem Downtime

```bash
# Implantação blue-green
docker compose -p n8n-workflows-green up -d --build

# Direcione o tráfego (atualize o balanceador de carga)
# Verifique a nova implantação
# Encerre a implantação antiga
docker compose -p n8n-workflows-blue down
```

## Checklist de Segurança

- [ ] Usar usuário não-root no container Docker
- [ ] Habilitar HTTPS/SSL em produção
- [ ] Configurar regras de firewall adequadas
- [ ] Usar credenciais de autenticação fortes
- [ ] Atualizações de segurança regulares
- [ ] Habilitar logs de acesso e monitoramento
- [ ] Fazer backup de dados sensíveis de forma segura
- [ ] Revisar e auditar as configurações regularmente

## Suporte e Manutenção

### Tarefas Regulares

1. **Diariamente**
   - Monitorar a saúde da aplicação
   - Verificar os logs de erro
   - Verificar a conclusão dos backups

2. **Semanalmente**
   - Revisar as métricas de desempenho
   - Atualizar dependências se necessário
   - Testar os procedimentos de recuperação de desastres

3. **Mensalmente**
   - Auditoria de segurança
   - Otimização do banco de dados
   - Atualizar a documentação