# 🔧 Guia de Solução de Problemas

## Problemas Comuns e Como Resolvê-los

---

## Problema 1: "Docker is not installed"

### O que você vê:
```
✗ Docker is not installed or not in PATH
```

### Como resolver:
1. **Instale o Docker Desktop**
   - Windows: https://www.docker.com/products/docker-desktop
   - Mac: https://www.docker.com/products/docker-desktop
2. **Reinicie o computador**
3. **Tente novamente**

---

## Problema 2: "Docker daemon is not running"

### O que você vê:
```
✗ Docker daemon is not running
```

### Como resolver:
1. **Procure o ícone da baleia do Docker** 🐳
   - Windows: Canto inferior direito (bandeja do sistema)
   - Mac: Canto superior direito (barra de menu)
2. **Se você não vir o ícone:**
   - Abra o "Docker Desktop" a partir dos seus aplicativos
   - Aguarde 30 segundos para ele iniciar
3. **Quando você vir o ícone da baleia, tente novamente**

---

## Problema 3: "Permission denied" (somente Mac)

### O que você vê:
```
Permission denied
```

### Como resolver:
1. **Abra o Terminal**
2. **Digite estes comandos, um de cada vez:**
   ```bash
   cd ~/Downloads/n8n-workflows-main/ai-stack
   chmod +x start.sh
   ./start.sh
   ```
3. **Pressione Enter após cada linha**

---

## Problema 4: "Port already in use"

### O que você vê:
```
Error: Port 5678 is already in use
```

### Como resolver:

**Opção 1 - Feche outros programas:**
1. Feche todos os navegadores
2. Feche quaisquer outros programas
3. Tente novamente

**Opção 2 - Reinicie o computador:**
1. Reinicie o computador
2. Abra o Docker Desktop
3. Tente novamente

**Opção 3 - Pare a stack antiga:**

Windows:
```powershell
.\start.ps1 -Stop
```

Mac:
```bash
./start.sh --stop
```

Depois inicie novamente.

---

## Problema 5: A janela do script fecha imediatamente (Windows)

### O que você vê:
- A janela do PowerShell abre e fecha em 1 segundo
- Você não consegue ler nenhuma mensagem

### Como resolver:
1. **Abra o PowerShell como Administrador:**
   - Clique no botão Iniciar do Windows
   - Digite "PowerShell"
   - Clique com o botão direito em "Windows PowerShell"
   - Clique em "Executar como administrador"
   - Clique em "Sim" quando perguntado

2. **Digite este comando:**
   ```powershell
   Set-ExecutionPolicy RemoteSigned
   ```
   Pressione Enter

3. **Digite "Y" e pressione Enter**

4. **Agora tente executar o start.ps1 novamente**

---

## Problema 6: "Cannot connect to localhost:5678"

### O que você vê:
- O navegador diz "This site can't be reached"
- Ou "Connection refused"

### Como resolver:
1. **Aguarde 2 minutos** - os serviços precisam de tempo para iniciar
2. **Verifique se o Docker está em execução** (procure o ícone da baleia 🐳)
3. **Verifique se a stack está em execução:**
   
   Windows:
   ```powershell
   .\start.ps1 -Status
   ```
   
   Mac:
   ```bash
   ./start.sh --status
   ```

4. **Procure por estes serviços:**
   - ai-stack-n8n (deve dizer "Up")
   - ai-stack-agent-zero (deve dizer "Up")
   - ai-stack-comfyui (deve dizer "Up")

5. **Se algum disser "Exited" ou "Error":**
   - Pare tudo: `.\start.ps1 -Stop` ou `./start.sh --stop`
   - Inicie novamente: `.\start.ps1` ou `./start.sh`

---

## Problema 7: "No space left on device"

### O que você vê:
```
Error: No space left on device
```

### Como resolver:
1. **Libere espaço em disco:**
   - Apague arquivos antigos de que você não precisa
   - Esvazie a Lixeira
   - Você precisa de pelo menos 10 GB livres

2. **Limpe o Docker:**
   ```bash
   docker system prune -a
   ```
   Digite "y" e pressione Enter quando perguntado

3. **Tente novamente**

---

## Problema 8: Os downloads estão muito lentos

### O que você vê:
- "Pulling images..." leva mais de 30 minutos

### Como resolver:
1. **Verifique sua conexão com a internet**
2. **Tenha paciência** - o primeiro download é de 5 a 10 GB
3. **Da próxima vez será mais rápido** (ele lembra o que já baixou)

### Pule o download se você já o tiver:
Windows:
```powershell
.\start.ps1 -NoPull
```

Mac:
```bash
./start.sh --no-pull
```

---

## Problema 9: "GPU not detected", mas eu tenho uma GPU

### O que você vê:
```
ℹ No NVIDIA GPU detected
```

### Como resolver (somente GPUs NVIDIA):

**Windows:**
1. Instale os drivers NVIDIA em: https://www.nvidia.com/download/index.aspx
2. Instale o NVIDIA Container Toolkit
3. Reinicie o Docker Desktop
4. Tente novamente

**Mac:**
- O Mac não suporta GPUs NVIDIA no Docker
- A stack usará o modo CPU automaticamente
- Será mais lento, mas ainda funcionará

**Não tem uma GPU NVIDIA?**
- Tudo bem! Use o modo CPU:
  
  Windows: `.\start.ps1 -CPU`
  
  Mac: `./start.sh --cpu`

---

## Problema 10: Tudo parece bem, mas nada funciona

### Tente esta "opção nuclear":

**Windows:**
```powershell
# Para tudo
.\start.ps1 -Stop

# Remove todos os containers e dados
docker compose down -v

# Começa do zero
.\start.ps1
```

**Mac:**
```bash
# Para tudo
./start.sh --stop

# Remove todos os containers e dados
docker compose down -v

# Começa do zero
./start.sh
```

**⚠️ Aviso:** Isto apaga todos os seus dados! Você começará completamente do zero.

---

## Ainda Não Funciona?

### Verifique estes pontos básicos:

- [ ] O Docker Desktop está instalado?
- [ ] O Docker Desktop está em execução? (veja o ícone da baleia 🐳)
- [ ] Você tem conexão com a internet?
- [ ] Você tem pelo menos 10 GB de espaço livre em disco?
- [ ] Você reiniciou o computador depois de instalar o Docker?
- [ ] Você está na pasta correta (ai-stack)?

### Obtenha os logs:

Windows:
```powershell
.\start.ps1 -Logs
```

Mac:
```bash
./start.sh --logs
```

**Tire uma captura de tela de quaisquer mensagens de erro em vermelho e peça ajuda!**

---

## Referência Rápida de Comandos

| O que você quer | Windows | Mac |
|---------------|---------|-----|
| Iniciar | `.\start.ps1` | `./start.sh` |
| Parar | `.\start.ps1 -Stop` | `./start.sh --stop` |
| Verificar status | `.\start.ps1 -Status` | `./start.sh --status` |
| Ver logs | `.\start.ps1 -Logs` | `./start.sh --logs` |
| Modo CPU | `.\start.ps1 -CPU` | `./start.sh --cpu` |
| Pular download | `.\start.ps1 -NoPull` | `./start.sh --no-pull` |

---

## 📞 Obtendo Ajuda

Ao pedir ajuda, forneça:

1. **Seu sistema operacional** (Windows 10, Windows 11, Mac, etc.)
2. **O que você estava tentando fazer**
3. **A mensagem de erro exata** (tire uma captura de tela)
4. **O que você já tentou**

Isso ajuda as pessoas a te ajudarem mais rápido! 🚀
