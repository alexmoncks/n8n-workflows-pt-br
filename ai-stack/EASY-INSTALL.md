# 🎮 Guia de Instalação Super Fácil
## AI Automation Stack - Passo a Passo

---

## 📋 O Que Você Precisa Antes de Começar

### ✅ Passo 1: Verifique se você tem o Docker

**Windows:**
1. Clique no botão Iniciar do Windows (canto inferior esquerdo)
2. Digite "Docker Desktop"
3. Você vê "Docker Desktop" na lista?
   - ✅ **SIM** → Vá para o Passo 2
   - ❌ **NÃO** → Vá para a seção "Instalando o Docker" abaixo

**Mac:**
1. Clique na lupa (🔍) no canto superior direito
2. Digite "Docker"
3. Você vê "Docker" na lista?
   - ✅ **SIM** → Vá para o Passo 2
   - ❌ **NÃO** → Vá para a seção "Instalando o Docker" abaixo

---

## 🔽 Instalando o Docker (Se Você Não o Tiver)

### Para Windows:

1. **Abra seu navegador** (Chrome, Edge, Firefox)
2. **Acesse este site:** https://www.docker.com/products/docker-desktop
3. **Clique no botão azul grande** que diz "Download for Windows"
4. **Aguarde o download** (é um arquivo grande, pode levar de 5 a 10 minutos)
5. **Encontre o arquivo baixado** (geralmente na pasta Downloads)
6. **Dê um duplo clique no arquivo** para instalar
7. **Clique em "Sim"** quando o Windows perguntar se você quer instalar
8. **Siga o instalador** - basta continuar clicando em "Next" e "OK"
9. **Reinicie o computador** quando ele pedir
10. **Após reiniciar**, o Docker Desktop abrirá automaticamente

### Para Mac:

1. **Abra seu navegador** (Safari, Chrome, Firefox)
2. **Acesse este site:** https://www.docker.com/products/docker-desktop
3. **Clique no botão azul grande** que diz "Download for Mac"
4. **Aguarde o download** (é um arquivo grande, pode levar de 5 a 10 minutos)
5. **Encontre o arquivo baixado** (geralmente na pasta Downloads)
6. **Dê um duplo clique no arquivo** para abri-lo
7. **Arraste o ícone do Docker** para a pasta Applications
8. **Abra a pasta Applications** e dê um duplo clique no Docker
9. **Clique em "Open"** quando o Mac perguntar se você tem certeza
10. **Aguarde o Docker iniciar** (você verá um ícone de baleia na barra de menu superior)

---

## 📥 Passo 2: Baixe a AI Stack

### Windows:

1. **Abra seu navegador**
2. **Acesse:** https://github.com/insomniakin/n8n-workflows
3. **Clique no botão verde "Code"**
4. **Clique em "Download ZIP"**
5. **Aguarde o download terminar**
6. **Vá para sua pasta Downloads**
7. **Clique com o botão direito no arquivo ZIP**
8. **Clique em "Extrair Tudo"**
9. **Clique em "Extrair"**
10. **Abra a pasta extraída**
11. **Encontre a pasta chamada "ai-stack"**
12. **Lembre-se de onde está essa pasta!**

### Mac:

1. **Abra seu navegador**
2. **Acesse:** https://github.com/insomniakin/n8n-workflows
3. **Clique no botão verde "Code"**
4. **Clique em "Download ZIP"**
5. **Aguarde o download terminar**
6. **Vá para sua pasta Downloads**
7. **Dê um duplo clique no arquivo ZIP** (ele extrai automaticamente)
8. **Abra a pasta extraída**
9. **Encontre a pasta chamada "ai-stack"**
10. **Lembre-se de onde está essa pasta!**

---

## 🚀 Passo 3: Inicie a AI Stack

### Windows:

1. **Abra o Explorador de Arquivos** (o ícone de pasta na barra de tarefas)
2. **Vá para a pasta "ai-stack"** que você encontrou no Passo 2
3. **Encontre o arquivo chamado "start.ps1"**
4. **Clique com o botão direito em "start.ps1"**
5. **Clique em "Run with PowerShell"**
6. **Se o Windows perguntar "Do you want to allow this?"** → Clique em "Sim"
7. **Aguarde e observe a janela** - você verá muito texto rolando
8. **Procure por estas mensagens:**
   ```
   ✓ Docker found
   ✓ Docker daemon is running
   ✓ All images pulled successfully
   🎉 AI Stack is running!
   ```
9. **Quando você vir "AI Stack is running!" → Está pronto!**

### Mac:

1. **Abra o Finder**
2. **Vá para a pasta "ai-stack"** que você encontrou no Passo 2
3. **Clique com o botão direito (ou Control+clique) em "start.sh"**
4. **Clique em "Open With" → "Terminal"**
5. **Se o Mac disser "Permission denied":**
   - Digite: `chmod +x start.sh`
   - Pressione Enter
   - Digite: `./start.sh`
   - Pressione Enter
6. **Aguarde e observe a janela** - você verá muito texto rolando
7. **Procure por estas mensagens:**
   ```
   ✓ Docker found
   ✓ Docker daemon is running
   ✓ All images pulled successfully
   🎉 AI Stack is running!
   ```
8. **Quando você vir "AI Stack is running!" → Está pronto!**

---

## 🌐 Passo 4: Abra os Programas

Agora você pode usar as ferramentas de IA! Abra seu navegador e acesse estes endereços:

### 1. n8n (Criador de Workflows)
- **Digite isto no seu navegador:** `http://localhost:5678`
- **Pressione Enter**
- **Você deverá ver:** Uma tela de boas-vindas pedindo para criar uma conta
- **Crie uma conta** com qualquer e-mail e senha (é só no seu computador)

### 2. Agent Zero (Assistente de IA)
- **Digite isto no seu navegador:** `http://localhost:50080`
- **Pressione Enter**
- **Você deverá ver:** Uma interface de chat

### 3. ComfyUI (Criador de Imagens)
- **Digite isto no seu navegador:** `http://localhost:8188`
- **Pressione Enter**
- **Você deverá ver:** Uma interface baseada em nós para criar imagens

---

## ❓ Solução de Problemas (Se Algo Der Errado)

### Problema: "Docker is not running"

**Solução:**
1. Procure o ícone do Docker (uma baleia) na barra de tarefas (Windows) ou barra de menu (Mac)
2. Se você não o vir, abra o Docker Desktop
3. Aguarde até ver "Docker Desktop is running"
4. Tente executar o script de inicialização novamente

### Problema: "Port already in use"

**Solução:**
1. Feche quaisquer programas que possam estar usando a internet
2. Reinicie o computador
3. Tente novamente

### Problema: A janela do script fecha imediatamente

**Solução para Windows:**
1. Abra o PowerShell como Administrador:
   - Clique em Iniciar
   - Digite "PowerShell"
   - Clique com o botão direito em "Windows PowerShell"
   - Clique em "Executar como administrador"
2. Digite: `Set-ExecutionPolicy RemoteSigned`
3. Pressione Enter
4. Digite "Y" e pressione Enter
5. Tente executar o start.ps1 novamente

### Problema: "Cannot find Docker"

**Solução:**
1. Certifique-se de que você instalou o Docker Desktop (veja a seção "Instalando o Docker")
2. Certifique-se de que o Docker Desktop está aberto e em execução
3. Reinicie o computador
4. Tente novamente

---

## 🛑 Como Parar a AI Stack

### Windows:
1. Abra o PowerShell (da mesma forma que antes)
2. Vá para a pasta ai-stack:
   - Digite: `cd ` (com um espaço após cd)
   - Arraste a pasta ai-stack para a janela do PowerShell
   - Pressione Enter
3. Digite: `.\start.ps1 -Stop`
4. Pressione Enter

### Mac:
1. Abra o Terminal
2. Vá para a pasta ai-stack:
   - Digite: `cd ` (com um espaço após cd)
   - Arraste a pasta ai-stack para a janela do Terminal
   - Pressione Enter
3. Digite: `./start.sh --stop`
4. Pressione Enter

---

## 📞 Precisa de Mais Ajuda?

Se você ainda estiver com problemas:

1. **Tire uma captura de tela** de quaisquer mensagens de erro
2. **Anote exatamente em qual passo você está**
3. **Peça ajuda a alguém** e mostre:
   - Este guia
   - Sua captura de tela
   - Em qual passo você está travado

---

## ✅ Checklist Rápido

Antes de começar, certifique-se de que:
- [ ] O Docker Desktop está instalado
- [ ] O Docker Desktop está em execução (você consegue ver o ícone da baleia)
- [ ] Você baixou a pasta ai-stack
- [ ] Você sabe onde está a pasta ai-stack no seu computador
- [ ] Sua internet está funcionando

---

## 🎉 Sucesso! E Agora?

Assim que tudo estiver em execução, você pode:

1. **Aprender o n8n:** Acesse http://localhost:5678 e tente criar um workflow simples
2. **Conversar com o Agent Zero:** Acesse http://localhost:50080 e faça perguntas a ele
3. **Criar imagens:** Acesse http://localhost:8188 e tente gerar uma imagem

**Divirta-se! 🚀**
