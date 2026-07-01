# 🤖 AI Automation Stack

> **Automação de IA Local Pronta para Uso: n8n + Agent Zero + ComfyUI**

Uma stack implantável com um único comando para automação de workflows movida por IA, com capacidades de geração de imagens.

---

## 📚 Documentação

**👉 [COMECE AQUI: Índice da Documentação](INDEX.md)** - Escolha o guia certo para você!

- **🚀 [INÍCIO RÁPIDO](QUICK-START.md)** - 3 passos simples para começar
- **📖 [GUIA DE INSTALAÇÃO FÁCIL](EASY-INSTALL.md)** - Passo a passo para Windows/Mac
- **🐧 [GUIA DE INSTALAÇÃO NO UBUNTU](UBUNTU-INSTALL.md)** - Guia completo para Ubuntu/Linux
- **🔧 [SOLUÇÃO DE PROBLEMAS](TROUBLESHOOTING.md)** - Resolver problemas comuns
- **📋 [RESUMO](SUMMARY.md)** - Visão geral e trilha de aprendizado
- **🎯 [FOLHA DE CONSULTA RÁPIDA](CHEAT-SHEET.md)** - Referência rápida (imprima isto!)
- **📘 Documentação Completa** - Você está lendo agora!

---

## 🎯 O Que Está Incluído

| Serviço | Finalidade | Porta | URL |
|---------|---------|------|-----|
| **n8n** | Motor de automação de workflows (o maestro) | 5678 | http://localhost:5678 |
| **Agent Zero** | Runtime de agente de IA e UI de planejamento | 50080 | http://localhost:50080 |
| **ComfyUI** | Geração de imagens/vídeos com IA | 8188 | http://localhost:8188 |

---

## 🚀 Início Rápido

### Pré-requisitos

- [Docker Desktop](https://www.docker.com/products/docker-desktop) instalado e em execução
- (Opcional) GPU NVIDIA com o [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) para aceleração por GPU

### Inicialização com Um Comando

**Windows (PowerShell):**
```powershell
.\start.ps1
```

**Linux/macOS:**
```bash
chmod +x start.sh
./start.sh
```

É só isso! O script irá:
1. ✅ Verificar se o Docker está instalado e em execução
2. ✅ Detectar a disponibilidade de GPU
3. ✅ Criar todos os diretórios necessários
4. ✅ Baixar as imagens mais recentes
5. ✅ Iniciar todos os serviços
6. ✅ Exibir as URLs de acesso

---

## 📁 Estrutura de Diretórios

```
ai-stack/
├── docker-compose.yml      # Configuração principal da stack
├── .env                    # Variáveis de ambiente
├── start.ps1               # Script de inicialização do Windows
├── start.sh                # Script de inicialização do Linux/macOS
├── README.md               # Este arquivo
│
├── data/                   # Dados persistentes (criados automaticamente)
│   ├── n8n/                # Workflows e credenciais do n8n
│   └── agent-zero/         # Dados do Agent Zero
│
├── shared/                 # Compartilhado entre todos os serviços
│   ├── comfyui/
│   │   ├── models/         # Modelos de IA (checkpoints, LoRAs, etc.)
│   │   │   ├── checkpoints/
│   │   │   ├── loras/
│   │   │   ├── vae/
│   │   │   ├── controlnet/
│   │   │   └── embeddings/
│   │   ├── output/         # Imagens geradas
│   │   ├── input/          # Imagens de entrada
│   │   └── custom_nodes/   # Extensões do ComfyUI
│   └── workflows/          # Arquivos de workflow compartilhados
│
└── workflows/              # Workflows do n8n prontos para uso
    ├── comfyui-image-generation.json
    └── comfyui-simple-test.json
```

---

## 🔧 Comandos

### Windows (PowerShell)

```powershell
.\start.ps1              # Iniciar a stack
.\start.ps1 -Stop        # Parar a stack
.\start.ps1 -Logs        # Ver os logs
.\start.ps1 -Status      # Verificar o status
.\start.ps1 -NoPull      # Iniciar sem baixar imagens
.\start.ps1 -CPU         # Forçar modo CPU (sem GPU)
```

### Linux/macOS

```bash
./start.sh               # Iniciar a stack
./start.sh --stop        # Parar a stack
./start.sh --logs        # Ver os logs
./start.sh --status      # Verificar o status
./start.sh --no-pull     # Iniciar sem baixar imagens
./start.sh --cpu         # Forçar modo CPU (sem GPU)
```

### Docker Compose (Direto)

```bash
docker compose up -d     # Iniciar
docker compose down      # Parar
docker compose logs -f   # Ver os logs
docker compose ps        # Status
```

---

## 🎨 Adicionando Modelos ao ComfyUI

Coloque seus modelos nos diretórios apropriados:

| Tipo de Modelo | Diretório |
|------------|-----------|
| Checkpoints do Stable Diffusion | `shared/comfyui/models/checkpoints/` |
| Modelos LoRA | `shared/comfyui/models/loras/` |
| Modelos VAE | `shared/comfyui/models/vae/` |
| Modelos ControlNet | `shared/comfyui/models/controlnet/` |
| Modelos de Upscale | `shared/comfyui/models/upscale_models/` |
| Embeddings | `shared/comfyui/models/embeddings/` |

### Modelo Inicial Recomendado

Baixe o [SD 1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5/blob/main/v1-5-pruned-emaonly.safetensors) e coloque-o em `shared/comfyui/models/checkpoints/`.

---

## 📊 Workflows Prontos para Uso

### 1. Pipeline de Geração de Imagens do ComfyUI

**Arquivo:** `workflows/comfyui-image-generation.json`

Um pipeline completo de geração de imagens acionado por webhook:

1. Importe o workflow no n8n (menu de três pontos ⋮ → "Import from File...")
2. Ative o workflow
3. Envie uma requisição POST:

```bash
curl -X POST http://localhost:5678/webhook/generate-image \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "a cyberpunk city at night, neon lights, rain, highly detailed",
    "negative_prompt": "blurry, low quality",
    "steps": 20,
    "cfg": 7,
    "width": 512,
    "height": 512
  }'
```

### 2. Teste Simples do ComfyUI

**Arquivo:** `workflows/comfyui-simple-test.json`

Um teste simples de conectividade:

1. Importe e ative no n8n
2. Acesse: http://localhost:5678/webhook/comfyui-status
3. Deve retornar as estatísticas do sistema do ComfyUI

---

## 🔗 Arquitetura de Integração

```
┌─────────────────────────────────────────────────────────────────┐
│                         n8n (Conductor)                         │
│                      http://localhost:5678                      │
└─────────────────────┬───────────────────────┬───────────────────┘
                      │                       │
                      ▼                       ▼
┌─────────────────────────────┐ ┌─────────────────────────────────┐
│       Agent Zero            │ │           ComfyUI               │
│   http://localhost:50080    │ │     http://localhost:8188       │
│                             │ │                                 │
│  • AI planning/reasoning    │ │  • POST /prompt (queue job)     │
│  • Tool use decisions       │ │  • GET /history/{id} (results)  │
│  • Prompt optimization      │ │  • GET /view (retrieve images)  │
└─────────────────────────────┘ └─────────────────────────────────┘
                      │                       │
                      └───────────┬───────────┘
                                  ▼
                    ┌─────────────────────────┐
                    │    Shared Volume        │
                    │      ./shared/          │
                    │                         │
                    │  • ComfyUI outputs      │
                    │  • Shared workflows     │
                    │  • Cross-service data   │
                    └─────────────────────────┘
```

### Loop Típico de Workflow

1. **Gatilho**: o n8n recebe um webhook/agendamento/evento
2. **Planejar** (opcional): o n8n chama o Agent Zero para a tomada de decisão
3. **Gerar**: o n8n envia o workflow ao ComfyUI via `POST /prompt`
4. **Consultar**: o n8n verifica `GET /history/{prompt_id}` até concluir
5. **Entregar**: o n8n recupera a saída e a envia ao destino

---

## 🌐 Referência da API do ComfyUI

### Enfileirar uma Geração

```bash
POST http://comfyui:8188/prompt
Content-Type: application/json

{
  "prompt": { /* ComfyUI workflow JSON */ }
}
```

**Resposta:**
```json
{
  "prompt_id": "abc123-def456-..."
}
```

### Verificar Status

```bash
GET http://comfyui:8188/history/{prompt_id}
```

### Obter Status da Fila

```bash
GET http://comfyui:8188/queue
```

### Recuperar Imagem Gerada

```bash
GET http://comfyui:8188/view?filename={name}&subfolder=&type=output
```

### Estatísticas do Sistema

```bash
GET http://comfyui:8188/system_stats
```

---

## ⚙️ Configuração

### Variáveis de Ambiente (.env)

```bash
# Fuso horário
TZ=America/Los_Angeles

# Autenticação básica do n8n (opcional)
N8N_BASIC_AUTH_ACTIVE=false
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=changeme

# Chaves de API para o Agent Zero (opcional)
OPENAI_API_KEY=sk-your-key-here
ANTHROPIC_API_KEY=sk-ant-your-key-here
```

### URL de Webhook Personalizada

Se estiver rodando por trás de um proxy reverso:

```bash
WEBHOOK_URL=https://your-domain.com
```

---

## 🔒 Notas de Segurança

- Por padrão, todos os serviços são acessíveis apenas em localhost
- Para implantação em produção, adicione um proxy reverso (Traefik/Caddy/nginx)
- Habilite a autenticação básica do n8n para qualquer implantação não local
- Mantenha as chaves de API no arquivo `.env` (não faça commit para o git)

---

## 🐛 Solução de Problemas

### O ComfyUI não enxerga a GPU

1. Certifique-se de que os drivers NVIDIA estão instalados
2. Instale o [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
3. Reinicie o Docker Desktop
4. Execute `docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi` para testar

### Os serviços não iniciam

```bash
# Verifica os logs
docker compose logs -f

# Reinicia um serviço específico
docker compose restart n8n

# Reset completo
docker compose down -v
docker compose up -d
```

### O n8n não consegue conectar ao ComfyUI

- Use `http://comfyui:8188` (rede interna do Docker), não `localhost`
- Certifique-se de que o container do ComfyUI está saudável: `docker compose ps`

### Conflitos de porta

Edite o `docker-compose.yml` para alterar os mapeamentos de porta:
```yaml
ports:
  - "NEW_PORT:INTERNAL_PORT"
```

---

## 📚 Recursos

- [Documentação do n8n](https://docs.n8n.io/)
- [GitHub do Agent Zero](https://github.com/frdel/agent-zero)
- [GitHub do ComfyUI](https://github.com/comfyanonymous/ComfyUI)
- [Exemplos de API do ComfyUI](https://github.com/comfyanonymous/ComfyUI/blob/master/script_examples/basic_api_example.py)

---

## 📄 Licença

Licença MIT - Use livremente para projetos pessoais e comerciais.
