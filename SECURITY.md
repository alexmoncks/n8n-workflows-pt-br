# Política de Segurança

## Reportando Vulnerabilidades de Segurança

Se você descobrir uma vulnerabilidade de segurança neste projeto, por favor reporte-a de forma responsável enviando um e-mail diretamente aos mantenedores. Não crie issues públicas para vulnerabilidades de segurança.

## Correções de Segurança Aplicadas (Novembro de 2025)

### 1. Vulnerabilidade de Path Traversal (Corrigida)
**Issue #48**: Anteriormente, o servidor da API era vulnerável a ataques de path traversal em sistemas Windows.

**Correção Aplicada**:
- Adicionada validação abrangente de nome de arquivo com a função `validate_filename()`
- Bloqueia todos os padrões de path traversal, incluindo:
  - Referências a diretório pai (`..`, `../`, `..\\`)
  - Tentativas de traversal codificadas em URL (`..%5c`, `..%2f`)
  - Caminhos absolutos e letras de drive
  - Caracteres especiais de shell e wildcards
- Usa `Path.resolve()` e `relative_to()` para defesa em profundidade
- Aplicada a todos os endpoints de acesso a arquivos:
  - `/api/workflows/{filename}`
  - `/api/workflows/{filename}/download`
  - `/api/workflows/{filename}/diagram`

### 2. Configuração Incorreta de CORS (Corrigida)
**Anteriormente**: O CORS estava configurado com `allow_origins=["*"]`, permitindo que qualquer site acessasse a API.

**Correção Aplicada**:
- Restringiu as origens CORS a domínios permitidos específicos:
  - Portas de desenvolvimento local (3000, 8000, 8080)
  - GitHub Pages (`https://zie619.github.io`)
  - Implantação da comunidade (`https://n8n-workflows-1-xxgm.onrender.com`)
- Restringiu os métodos permitidos apenas a `GET` e `POST`
- Restringiu os cabeçalhos permitidos a `Content-Type` e `Authorization`

### 3. Endpoint de Reindexação Não Autenticado (Corrigido)
**Anteriormente**: O endpoint `/api/reindex` podia ser chamado por qualquer pessoa, potencialmente causando DoS.

**Correção Aplicada**:
- Adicionado requisito de autenticação por meio do parâmetro de query `admin_token`
- O token deve corresponder à variável de ambiente `ADMIN_TOKEN`
- Se nenhum token for configurado, o endpoint fica desabilitado
- Adicionado rate limiting para prevenir abuso
- Registra em log todas as tentativas de reindexação com o IP do cliente

### 4. Rate Limiting (Adicionado)
**Novo Recurso de Segurança**:
- Implementado rate limiting (60 requisições por minuto por IP)
- Aplicado a todos os endpoints sensíveis
- Previne ataques de força bruta e DoS
- Retorna HTTP 429 quando o limite é excedido

## Configuração de Segurança

### Variáveis de Ambiente
```bash
# Necessário para o endpoint de reindexação
export ADMIN_TOKEN="your-secure-random-token"

# Opcional: configurar o rate limiting (padrão: 60)
# MAX_REQUESTS_PER_MINUTE=60
```

### Configuração de CORS
Para adicionar origens permitidas adicionais, modifique a lista `ALLOWED_ORIGINS` em `api_server.py`:

```python
ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "http://localhost:8000",
    "https://your-domain.com",  # Adicione seu domínio de produção
]
```

## Boas Práticas de Segurança

1. **Variáveis de Ambiente**: Nunca faça commit de tokens ou credenciais sensíveis no repositório
2. **Somente HTTPS**: Sempre use HTTPS em produção (HTTP é apenas para desenvolvimento local)
3. **Atualizações Regulares**: Mantenha todas as dependências atualizadas para corrigir vulnerabilidades conhecidas
4. **Monitoramento**: Monitore os logs em busca de padrões de atividade suspeita
5. **Backup**: Backups regulares do banco de dados de workflows

## Checklist de Segurança para Implantação

- [ ] Definir uma variável de ambiente `ADMIN_TOKEN` forte
- [ ] Configurar as origens CORS para o seu domínio específico
- [ ] Usar HTTPS com certificado SSL válido
- [ ] Habilitar regras de firewall para restringir o acesso
- [ ] Configurar monitoramento e alertas
- [ ] Revisar e rotacionar os tokens de admin regularmente
- [ ] Manter o Python e todas as dependências atualizados
- [ ] Usar um reverse proxy (nginx/Apache) com cabeçalhos de segurança adicionais

## Cabeçalhos de Segurança Adicionais (Recomendado)

Ao implantar por trás de um reverse proxy, adicione estes cabeçalhos:

```nginx
add_header X-Frame-Options "SAMEORIGIN";
add_header X-Content-Type-Options "nosniff";
add_header X-XSS-Protection "1; mode=block";
add_header Content-Security-Policy "default-src 'self'";
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
```

## Cronograma de Divulgação de Vulnerabilidades

| Data | Issue | Status | Versão Corrigida |
|------|-------|--------|---------------|
| Out 2025 | Path Traversal (#48) | Corrigida | 2.0.1 |
| Nov 2025 | Configuração Incorreta de CORS | Corrigida | 2.0.1 |
| Nov 2025 | Reindexação Não Autenticada | Corrigida | 2.0.1 |

## Créditos

Problemas de segurança reportados por:
- Path Traversal: Contribuidor da comunidade via Issue #48

## Contato

Para questões de segurança, entre em contato com os mantenedores de forma privada.