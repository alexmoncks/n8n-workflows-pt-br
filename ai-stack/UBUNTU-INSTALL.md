# 🐧 Guia de Instalação no Ubuntu
## AI Automation Stack para Ubuntu/Linux

---

## ✅ Passo 1: Instale o Docker no Ubuntu

### Abra o Terminal
Pressione `Ctrl + Alt + T` para abrir o Terminal

### Verifique se o Docker Já Está Instalado
```bash
docker --version
```

Se você vir um número de versão, pule para o Passo 2. Caso contrário, continue:

### Instale o Docker
Copie e cole estes comandos, um de cada vez:

```bash
# Atualiza a lista de pacotes
sudo apt update

# Instala os pacotes necessários
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# Adiciona a chave GPG oficial do Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Adiciona o repositório do Docker
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Atualiza a lista de pacotes novamente
sudo apt update

# Instala o Docker
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Adiciona seu usuário ao grupo docker (para não precisar de sudo)
sudo usermod -aG docker $USER

# Aplica a nova associação de grupo
newgrp docker
```

### Verifique a Instalação do Docker
```bash
docker --version
docker compose version
```

Você deverá ver números de versão para ambos.

---

## 📥 Passo 2: Baixe a AI Stack

### Opção A: Usando Git (Recomendado)
```bash
# Instala o git caso não o tenha
sudo apt install -y git

# Clona o repositório
git clone https://github.com/insomniakin/n8n-workflows.git

# Entra na pasta ai-stack
cd n8n-workflows/ai-stack
```

### Opção B: Baixar o ZIP
```bash
# Instala o wget e o unzip caso não os tenha
sudo apt install -y wget unzip

# Baixa o repositório
wget https://github.com/insomniakin/n8n-workflows/archive/refs/heads/feature/ai-automation-stack.zip

# Descompacta
unzip feature/ai-automation-stack.zip

# Entra na pasta ai-stack
cd n8n-workflows-feature-ai-automation-stack/ai-stack
```

---

## 🚀 Passo 3: Inicie a AI Stack

### Torne o Script Executável
```bash
chmod +x start.sh
```

### Execute a Stack
```bash
./start.sh
```

### O Que Você Verá
O script irá:
1. ✓ Verificar se o Docker está instalado
2. ✓ Verificar se o Docker está em execução
3. ✓ Detectar sua GPU (se você tiver uma NVIDIA)
4. ✓ Criar as pastas necessárias
5. ✓ Baixar as imagens do Docker (isto leva de 5 a 10 minutos na primeira vez)
6. ✓ Iniciar todos os serviços

### Aguarde a Mensagem de Sucesso
```
🎉 AI Stack is running!
```

---

## 🌐 Passo 4: Abra os Serviços

Abra seu navegador (Firefox, Chrome, etc.) e acesse:

### n8n (Automação de Workflows)
```
http://localhost:5678
```

### Agent Zero (Assistente de IA)
```
http://localhost:50080
```

### ComfyUI (Geração de Imagens)
```
http://localhost:8188
```

---

## 🎮 Comandos Rápidos

### Iniciar a Stack
```bash
./start.sh
```

### Parar a Stack
```bash
./start.sh --stop
```

### Verificar Status
```bash
./start.sh --status
```

### Ver Logs
```bash
./start.sh --logs
```

### Forçar Modo CPU (Sem GPU)
```bash
./start.sh --cpu
```

---

## 🔧 Solução de Problemas Específica do Ubuntu

### Problema: "Permission denied" ao executar o docker

**Solução:**
```bash
# Adiciona você ao grupo docker
sudo usermod -aG docker $USER

# Faça logout e login novamente, ou execute:
newgrp docker

# Tente novamente
./start.sh
```

### Problema: "Cannot connect to Docker daemon"

**Solução:**
```bash
# Inicia o serviço do Docker
sudo systemctl start docker

# Habilita o Docker para iniciar no boot
sudo systemctl enable docker

# Verifica o status
sudo systemctl status docker
```

### Problema: Porta já em uso

**Solução:**
```bash
# Descobre o que está usando a porta
sudo lsof -i :5678
sudo lsof -i :8188
sudo lsof -i :50080

# Encerra o processo (substitua PID pelo número real)
sudo kill -9 PID

# Ou simplesmente reinicie
sudo reboot
```

### Problema: Espaço em disco insuficiente

**Solução:**
```bash
# Verifica o espaço em disco
df -h

# Limpa o Docker
docker system prune -a

# Limpa o cache do apt
sudo apt clean
sudo apt autoremove
```

### Problema: GPU NVIDIA não detectada

**Solução:**
```bash
# Instala os drivers NVIDIA
sudo ubuntu-drivers autoinstall

# Instala o NVIDIA Container Toolkit
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt update
sudo apt install -y nvidia-container-toolkit

# Reinicia o Docker
sudo systemctl restart docker

# Testa a GPU
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```

---

## 🔥 Configuração de Firewall (Opcional)

Se você quiser acessar a partir de outros computadores na sua rede:

```bash
# Libera as portas no firewall
sudo ufw allow 5678/tcp
sudo ufw allow 8188/tcp
sudo ufw allow 50080/tcp

# Habilita o firewall caso ainda não esteja habilitado
sudo ufw enable

# Verifica o status
sudo ufw status
```

---

## 📊 Requisitos de Sistema

### Mínimo (Modo CPU)
- Ubuntu 20.04 ou mais recente
- 8 GB de RAM
- 20 GB de espaço livre em disco
- Qualquer CPU moderna

### Recomendado (Modo GPU)
- Ubuntu 20.04 ou mais recente
- 16 GB de RAM
- 50 GB de espaço livre em disco
- GPU NVIDIA com 6+ GB de VRAM
- Drivers NVIDIA instalados

---

## 🆘 Comandos de Ajuda Rápida

### Verificar o Status do Docker
```bash
sudo systemctl status docker
```

### Verificar os Containers em Execução
```bash
docker ps
```

### Verificar os Logs do Docker
```bash
docker compose logs -f
```

### Reiniciar Tudo
```bash
./start.sh --stop
docker system prune -f
./start.sh
```

### Reset Completo (Apaga Todos os Dados!)
```bash
./start.sh --stop
docker compose down -v
rm -rf data/ shared/
./start.sh
```

---

## 💡 Dicas Profissionais para Ubuntu

### 1. Criar Atalhos na Área de Trabalho

Crie um arquivo `~/Desktop/ai-stack.desktop`:
```bash
cat > ~/Desktop/ai-stack.desktop << 'EOF'
[Desktop Entry]
Type=Application
Name=AI Stack
Comment=Start AI Automation Stack
Exec=gnome-terminal -- bash -c "cd ~/n8n-workflows/ai-stack && ./start.sh; exec bash"
Icon=applications-internet
Terminal=true
EOF

chmod +x ~/Desktop/ai-stack.desktop
```

### 2. Iniciar Automaticamente no Boot

```bash
# Cria o serviço systemd
sudo nano /etc/systemd/system/ai-stack.service
```

Cole isto:
```ini
[Unit]
Description=AI Automation Stack
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/home/YOUR_USERNAME/n8n-workflows/ai-stack
ExecStart=/home/YOUR_USERNAME/n8n-workflows/ai-stack/start.sh
ExecStop=/home/YOUR_USERNAME/n8n-workflows/ai-stack/start.sh --stop
User=YOUR_USERNAME

[Install]
WantedBy=multi-user.target
```

Substitua `YOUR_USERNAME` pelo seu nome de usuário real e, em seguida:
```bash
sudo systemctl enable ai-stack
sudo systemctl start ai-stack
```

### 3. Monitorar Recursos

```bash
# Instala o htop para melhor monitoramento
sudo apt install -y htop

# Acompanha as estatísticas do Docker
docker stats

# Acompanha o uso da GPU (se NVIDIA)
watch -n 1 nvidia-smi
```

---

## ✅ Checklist de Sucesso

- [ ] Docker instalado e em execução
- [ ] Repositório baixado
- [ ] Você está na pasta ai-stack
- [ ] start.sh é executável
- [ ] Script executado com sucesso
- [ ] Todas as três URLs abrem no navegador
- [ ] n8n exibe a tela de boas-vindas
- [ ] ComfyUI exibe a interface
- [ ] Agent Zero exibe o chat

---

## 🎉 Tudo Pronto!

Sua AI Automation Stack agora está em execução no Ubuntu!

### Próximos Passos:
1. Importe o workflow de teste: `workflows/comfyui-simple-test.json`
2. Tente gerar sua primeira imagem
3. Leia o SUMMARY.md para saber mais
4. Construa seus próprios workflows!

### Precisa de Mais Ajuda?
- **Guia Geral:** README.md
- **Solução de Problemas:** TROUBLESHOOTING.md
- **Referência Rápida:** CHEAT-SHEET.md

**Boas automações! 🚀**
