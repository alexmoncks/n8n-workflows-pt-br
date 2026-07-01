# 🎯 Folha de Consulta Rápida da AI Stack
**Imprima esta página e mantenha à mão!**

---

## 🚀 Iniciar e Parar

### Windows
```powershell
# Iniciar
.\start.ps1

# Parar
.\start.ps1 -Stop

# Verificar Status
.\start.ps1 -Status

# Ver Logs
.\start.ps1 -Logs
```

### Mac/Linux
```bash
# Iniciar
./start.sh

# Parar
./start.sh --stop

# Verificar Status
./start.sh --status

# Ver Logs
./start.sh --logs
```

---

## 🌐 URLs dos Serviços

Copie estes endereços no seu navegador:

```
n8n:         http://localhost:5678
Agent Zero:  http://localhost:50080
ComfyUI:     http://localhost:8188
```

---

## 📂 Pastas Importantes

```
ai-stack/
├── data/n8n/              ← Seus workflows do n8n
├── data/agent-zero/       ← Dados do Agent Zero
└── shared/
    └── comfyui/
        ├── models/        ← Coloque os modelos de IA aqui
        ├── output/        ← Imagens geradas aqui
        └── input/         ← Imagens de entrada aqui
```

---

## 🎨 Referência Rápida da API do ComfyUI

### Enfileirar uma Imagem
```bash
POST http://localhost:8188/prompt
```

### Verificar Status
```bash
GET http://localhost:8188/history/{prompt_id}
```

### Obter Imagem
```bash
GET http://localhost:8188/view?filename={name}&type=output
```

---

## ⚡ Comandos Rápidos

### Comandos do Docker
```bash
# Ver todos os containers em execução
docker ps

# Parar todos os containers
docker stop $(docker ps -q)

# Remover todos os containers
docker rm $(docker ps -aq)

# Limpar o Docker
docker system prune -a
```

### Verificar se os Serviços Estão em Execução
```bash
# Windows
curl http://localhost:5678/healthz
curl http://localhost:8188/system_stats

# Mac/Linux
curl http://localhost:5678/healthz
curl http://localhost:8188/system_stats
```

---

## 🔧 Correções Comuns

| Problema | Solução |
|---------|----------|
| **Docker não está em execução** | Abra o Docker Desktop, aguarde o ícone da baleia 🐳 |
| **Porta em uso** | Execute o comando de parar e inicie novamente |
| **Permissão negada** | Windows: Executar como Admin<br>Mac: `chmod +x start.sh` |
| **Não consegue conectar** | Aguarde 2 minutos, verifique se o Docker está em execução |
| **Sem espaço** | Apague arquivos antigos, execute `docker system prune -a` |

---

## 📝 Importação de Workflow no n8n

1. Abra http://localhost:5678
2. Abra o workflow (ou crie um novo)
3. Clique no menu de três pontos (⋮) no canto superior direito
4. Clique em **"Import from File..."**
5. Selecione o arquivo JSON do workflow
6. Clique em **"Save"**
7. Clique no botão **"Active"** para habilitar

> 💡 Como alternativa, via linha de comando: `n8n import:workflow --input=arquivo.json`

---

## 🎨 Adicionando Modelos ao ComfyUI

1. Baixe o arquivo do modelo (`.safetensors` ou `.ckpt`)
2. Coloque na pasta correta:
   - **Checkpoints:** `shared/comfyui/models/checkpoints/`
   - **LoRAs:** `shared/comfyui/models/loras/`
   - **VAE:** `shared/comfyui/models/vae/`
3. Reinicie o ComfyUI (ou atualize o navegador)

### Fontes Populares de Modelos
- Hugging Face: https://huggingface.co/models
- Civitai: https://civitai.com
- Stable Diffusion: https://huggingface.co/runwayml/stable-diffusion-v1-5

---

## 🆘 Reset de Emergência

**⚠️ Isto apaga tudo e começa do zero!**

### Windows
```powershell
.\start.ps1 -Stop
docker compose down -v
.\start.ps1
```

### Mac/Linux
```bash
./start.sh --stop
docker compose down -v
./start.sh
```

---

## 📊 Verificação do Sistema

Antes de iniciar, confirme:

- [ ] Docker Desktop instalado
- [ ] Docker Desktop em execução (ícone da baleia visível)
- [ ] Pelo menos 10 GB de espaço livre em disco
- [ ] Conexão com a internet funcionando
- [ ] Você está na pasta `ai-stack`

---

## 🎓 Recursos de Aprendizado

### n8n
- Docs: https://docs.n8n.io
- Comunidade: https://community.n8n.io
- YouTube: Pesquise "n8n tutorial"

### ComfyUI
- GitHub: https://github.com/comfyanonymous/ComfyUI
- Wiki: https://github.com/comfyanonymous/ComfyUI/wiki
- Reddit: r/comfyui

### Agent Zero
- GitHub: https://github.com/frdel/agent-zero
- Docs: Consulte o README no GitHub

---

## 💾 Faça Backup do Seu Trabalho

### Arquivos Importantes para Backup
```
data/n8n/           ← Seus workflows
shared/workflows/   ← Arquivos de workflow compartilhados
.env                ← Suas configurações
```

### Backup Rápido (Copie estas pastas)
```bash
# Windows
xcopy /E /I data backup\data
xcopy /E /I shared backup\shared

# Mac/Linux
cp -r data backup/data
cp -r shared backup/shared
```

---

## 🔐 Lembretes de Segurança

- ✅ Seguro para uso local (somente localhost)
- ❌ Não exponha à internet sem segurança
- ✅ Mantenha o Docker Desktop atualizado
- ❌ Não compartilhe seu arquivo .env
- ✅ Use senhas fortes se habilitar autenticação

---

## 📞 Recursos de Ajuda

1. **QUICK-START.md** - 3 passos simples
2. **EASY-INSTALL.md** - Guia detalhado
3. **TROUBLESHOOTING.md** - Resolver problemas
4. **README.md** - Documentação completa
5. **SUMMARY.md** - Visão geral e trilha de aprendizado

---

## ✅ Indicadores de Sucesso

Você sabe que está funcionando quando:

- ✅ Ícone da baleia 🐳 visível na barra de tarefas/menu
- ✅ O terminal exibe "🎉 AI Stack is running!"
- ✅ Todas as três URLs abrem no navegador
- ✅ O n8n exibe a tela de boas-vindas
- ✅ O ComfyUI exibe a interface de nós
- ✅ O Agent Zero exibe a interface de chat

---

**🎉 Tudo pronto! Boas automações!**

---

*Imprima esta página e mantenha perto do seu computador para consulta rápida!*
