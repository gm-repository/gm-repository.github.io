---
layout: post
title: "Estação de trabalho com Ubuntu 24.04"
date: 2025-02-04 10:00:00 -0300
categories: [Ubuntu]
tags: [Ubuntu]
excerpt: "Estação de trabalho com Ubuntu 24.04"
---


## **Configuração de uma Estação de Trabalho com Ubuntu 24.04**

A configuração de uma estação de trabalho eficiente no Ubuntu envolve **atualização do sistema**, **instalação de ferramentas essenciais** e **personalização do ambiente**. Este guia ajudará a preparar um sistema pronto para desenvolvimento e administração de servidores.

### **Atualização do Sistema**

Antes de instalar qualquer coisa, é **boa prática atualizar os pacotes existentes** para garantir que todas as versões sejam as mais recentes e livres de vulnerabilidades conhecidas.

```bash
sudo apt update && sudo apt upgrade -y
```

#### **O que esses comandos fazem?**
- `sudo apt update` → Atualiza a lista de pacotes disponíveis nos repositórios do Ubuntu.
- `sudo apt upgrade -y` → Instala as versões mais recentes dos pacotes já instalados no sistema. O `-y` responde "sim" automaticamente para todas as confirmações.

#### **Por que é importante atualizar primeiro?**
- Evita conflitos de versões entre pacotes.
- Garante que você está instalando as versões mais seguras e estáveis.
- Reduz possíveis problemas de dependências quebradas.

---

### **Instalação de Ferramentas de Desenvolvimento e Utilitários**

Agora que o sistema está atualizado, instalamos um conjunto de ferramentas essenciais que **otimizam o trabalho no terminal**, ajudam na **resolução de problemas de rede** e facilitam **o desenvolvimento**.

```bash
sudo apt install vim fzf ipcalc wget nmap \
    tcpdump git traceroute plocate \
    whois dnsutils grc tmux eza -y
```

#### **Explicação das ferramentas:**
- **vim** → Editor de texto poderoso, ideal para programadores e administradores de sistemas.
- **fzf** → Um buscador fuzzy para melhorar a experiência de navegação no terminal.
- **ipcalc** → Calculadora de sub-redes IP, útil para administradores de rede.
- **wget** → Ferramenta para baixar arquivos via HTTP, HTTPS e FTP diretamente do terminal.
- **nmap** → Scanner de redes para descoberta de hosts e serviços ativos.
- **tcpdump** → Ferramenta para captura e análise de pacotes de rede.
- **git** → Sistema de controle de versão essencial para desenvolvedores.
- **traceroute** → Diagnostica a rota que os pacotes percorrem até um destino.
- **plocate** → Alternativa rápida ao `locate` para busca de arquivos no sistema.
- **whois** → Obtém informações sobre domínios e IPs.
- **dnsutils** → Conjunto de ferramentas para consultas DNS (`dig`, `nslookup`).
- **grc** → Adiciona cores aos outputs de comandos comuns, tornando-os mais legíveis.
- **tmux** → Multiplexador de terminal para gerenciar várias sessões em uma única janela.
- **eza** → Alternativa moderna ao `ls`, com suporte a cores e exibição melhorada de arquivos.

---

## **Configuração do Bash**  

O Bash é o interpretador de comandos padrão na maioria das distribuições Linux, incluindo o Ubuntu. Personalizá-lo pode **aumentar a produtividade**, **melhorar a experiência no terminal** e **tornar tarefas repetitivas mais rápidas e eficientes**.

---

### **Criando um Arquivo de Configuração Personalizado (`~/.bash_custom`)**  

Vamos criar um arquivo separado para configurações personalizadas do Bash. Isso ajuda a manter o `.bashrc` principal mais organizado e facilita a portabilidade das configurações para outras máquinas.

```bash
vim ~/.bash_custom
```

Agora, adicionamos as seguintes configurações:

```bash
# FZF - Atalhos de busca
if [ -f "$(dpkg -L fzf | grep key-bindings.bash)" ]; then
    source "$(dpkg -L fzf | grep key-bindings.bash)"
fi
```
**O que isso faz?**
- Ativa os atalhos do `fzf`, um poderoso mecanismo de busca interativa no terminal.
- Antes de ativá-los, o script verifica se o arquivo de configurações do `fzf` realmente existe para evitar erros.

---

### **Configuração do Histórico do Terminal**  

```bash
# Configurações de Histórico
export HISTSIZE=5000
export HISTCONTROL=ignoredups:erasedups
export HISTIGNORE="ls:cd:exit"
export HISTTIMEFORMAT="%d/%m/%Y %T "
```

**Explicação**
- `HISTSIZE=5000` → Mantém um histórico de até **5000 comandos**.
- `HISTCONTROL=ignoredups:erasedups` → Evita entradas duplicadas no histórico, tornando a navegação mais eficiente.
- `HISTIGNORE="ls:cd:exit"` → Não armazena comandos irrelevantes no histórico (como `ls`, `cd` e `exit`).
- `HISTTIMEFORMAT="%d/%m/%Y %T "` → Exibe data e hora nos comandos do histórico, facilitando auditorias.

---

### **Definindo o Editor Padrão**  

```bash
# Configurações do Editor Padrão
export EDITOR=vim
export VISUAL=vim
```

**O que isso faz?**
- Define o `vim` como editor padrão para edições em linha de comando, útil em ferramentas como `git commit`.

---

### **Personalizando o Prompt do Bash**  

```bash
# Prompt Personalizado
export PS1="\[\e[38;5;81m\]\w\[\e[0m\] \[\e[38;5;215m\]\[\e[0m\] \[\e[38;5;204m\]→\[\e[0m\] "
```

**O que isso faz?**
- Personaliza o prompt para exibir o diretório atual (`\w`) com **cores customizadas** e um **símbolo de seta estilizado (→)**.
- Isso melhora a visualização e dá um toque pessoal ao terminal.



---

### **Criando Atalhos com Aliases**  

Agora, vamos configurar atalhos úteis para comandos frequentes.

```bash
vim ~/.bash_aliases
```

Adicione os seguintes aliases:

```bash
# Aliases para melhorar a produtividade
alias tail='grc tail'
alias ping='grc ping -c3'
alias ps='grc ps'
alias traceroute='grc traceroute'
alias dig='grc dig'
alias ss='grc ss'
alias ipinfo='curl http://ipinfo.io/ip'
alias ls="eza --long --group-directories-first --icons"
alias update='sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y && sudo apt autoclean'
```

**Explicação** 
- `tail`, `ping`, `ps`, `traceroute`, `dig`, `ss` → Todos esses comandos agora **exibirão saída colorida** usando o `grc` para melhor visualização.
- `ipinfo` → Retorna rapidamente o IP público da máquina.
- `ls` → Usa o `eza`, um substituto moderno para `ls`, com formatação melhorada e ícones.
- `update` → Executa uma atualização completa do sistema, incluindo limpeza de pacotes obsoletos.


---

### **Criando Funções Úteis no Bash**  

Agora, adicionamos uma função avançada para gerenciar conexões SSH de forma interativa usando `fzf`.

```bash
vim ~/.bash_functions
```

Adicione a seguinte função:

```bash
# Menu SSH com fzf
ssh_menu() {
    local hosts_file="$HOME/.ssh/hosts_list"
    local ssh_config="$HOME/.ssh/config"

    # Garante que os arquivos existam
    touch "$hosts_file" "$ssh_config"

    # Função para adicionar um novo host
    add_ssh() {
        echo -n "Digite o alias do host (ex: vm-01): "
        read alias
        echo -n "Digite o IP do host (ex: 192.168.200.100): "
        read ip
        echo -n "Digite a porta SSH (default: 22): "
        read port
        port=${port:-22}
        echo -n "Digite o usuário (default: seu_usuario): "
        read user
        user=${user:-"seu_usuario"}
        echo -n "Digite o caminho da chave SSH (default: ~/.ssh/id_rsa): "
        read key
        key=${key:-"$HOME/.ssh/id_rsa"}

        # Validação de IP e porta
        if [[ ! $ip =~ ^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
            echo "IP inválido."
            return
        fi

        # Adiciona o host ao ssh_config e ao arquivo de hosts
        {
            echo -e "\nHost $alias"
            echo "    HostName $ip"
            echo "    Port $port"
            echo "    User $user"
            echo "    IdentityFile $key"
        } >> "$ssh_config"
        echo "$alias:$ip" >> "$hosts_file"
        echo "Host $alias adicionado com sucesso."
    }

    # Exibe o menu e realiza ações com base na escolha do usuário
    local choice=$( (echo "Adicionar novo host"; echo "Remover host"; echo "Listar hosts"; cat "$hosts_file") | fzf --prompt="Selecione a ação: " )

    case "$choice" in
        "Adicionar novo host") add_ssh ;;
        "Remover host") echo "Removendo ainda não implementado." ;;
        "Listar hosts") cat "$hosts_file" ;;
        *) ssh "$choice" ;;
    esac
}
```

**O que essa função faz?**
- Cria um menu interativo para **gerenciar conexões SSH** usando `fzf`.
- Permite **adicionar, listar e conectar-se rapidamente** a servidores.
- Valida entradas para evitar erros de configuração.

---

### **Ativando as Configurações no `.bashrc`**  

Para garantir que as configurações personalizadas sejam carregadas sempre que o terminal for aberto, adicione estas linhas ao `.bashrc`:

```bash
echo 'if [ -f ~/.bash_custom ]; then source ~/.bash_custom; fi' >> ~/.bashrc
echo 'if [ -f ~/.bash_aliases ]; then source ~/.bash_aliases; fi' >> ~/.bashrc
echo 'if [ -f ~/.bash_functions ]; then source ~/.bash_functions; fi' >> ~/.bashrc
```

---

## **Instalando e Configurando NerdFont**

O **NerdFont** é uma coleção de fontes personalizadas projetadas para **melhorar a experiência visual no terminal e em editores de código**. Ele adiciona **ícones extras**, tornando ferramentas como `eza`, `tmux`, `neovim` e `powerline` mais legíveis e agradáveis visualmente.

### **Baixando a Fonte NerdFont**  
Antes de instalar, é necessário fazer o download do arquivo `.zip` que contém as fontes:

```bash
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.3.0/SourceCodePro.zip
```

**O que esse comando faz?**
- Usa `wget` para baixar a versão mais recente da fonte **SourceCodePro NerdFont** diretamente do repositório oficial no GitHub.

> **Dica**:
Caso queira baixar outra fonte da coleção NerdFont, basta acessar o repositório oficial e escolher outra versão: 
[NerdFonts Releases](https://github.com/ryanoasis/nerd-fonts/releases)

---

### **Extraindo os Arquivos de Fonte**  

```bash
unzip SourceCodePro.zip
```

**Explicação:** 
- O comando `unzip` extrai os arquivos `.ttf` da fonte compactada.
- Se o `unzip` não estiver instalado, você pode adicioná-lo com:
  ```bash
  sudo apt install unzip -y
  ```

> **Dica**: Caso precise extrair para um local específico, use:  
```bash
unzip SourceCodePro.zip -d ~/.fonts
```
Isso colocará os arquivos diretamente na pasta de fontes do usuário.

---

### **Movendo as Fontes para o Diretório Correto**  

```bash
mkdir -p ~/.local/share/fonts
mv *.ttf ~/.local/share/fonts/
```

**O que acontece aqui?**
- Criamos a pasta `~/.local/share/fonts/` (se ainda não existir) para armazenar as fontes do usuário.
- Movemos os arquivos `.ttf` extraídos para essa pasta.

**Dica**: Se quiser instalar a fonte para **todos os usuários do sistema**, use: 
```bash
sudo mv *.ttf /usr/share/fonts/
```

---

### **Atualizando o Cache de Fontes**  

```bash
fc-cache -fv
```

**Explicação:**
- O `fc-cache` força a atualização do cache de fontes do sistema.
- O parâmetro `-f` (force) força a reconstrução do cache, e `-v` (verbose) exibe informações sobre a atualização.

> **Dica**: 
Se precisar testar se a fonte foi instalada corretamente, rode:
```bash
fc-list | grep "Source Code Pro"
```
Se a fonte estiver listada na saída do comando, significa que foi instalada com sucesso!

---

### **Ativando a Fonte no Terminal**  

Para garantir que a nova fonte seja usada no terminal, siga estas etapas:

#### **🔹 GNOME Terminal (Ubuntu padrão)**
1. Abra o terminal e vá até **Preferências**.
2. Selecione o perfil padrão.
3. Vá para a aba **Texto**.
4. Ative a opção **Fonte Personalizada** e selecione **Source Code Pro Nerd Font**.

#### **Alacritty**
Se você usa **Alacritty**, edite o arquivo de configuração:
```bash
vim ~/.config/alacritty/alacritty.yml
```
E adicione:
```yaml
font:
  normal:
    family: "Source Code Pro Nerd Font"
    style: Regular
```

#### **Kitty Terminal**
No **Kitty**, edite o arquivo de configuração:
```bash
vim ~/.config/kitty/kitty.conf
```
E adicione:
```ini
font_family Source Code Pro Nerd Font
```

#### **Tmux & Powerline**
Se estiver usando `tmux` ou `powerline`, adicione esta linha ao `.tmux.conf`:
```bash
set -g status-left "❯ #S"
```
Agora os **ícones do NerdFont funcionarão corretamente!**

---

## **Conclusão** 

Agora seu terminal está configurado com uma fonte moderna e estilizada, permitindo uma experiência mais visual e organizada. Isso facilita a leitura e uso de ferramentas que utilizam ícones e símbolos especiais.


---

## **Criando um Novo Perfil no GNOME Terminal** 

O **GNOME Terminal** permite criar perfis personalizados para alterar cores, transparência, fonte e outros aspectos visuais. Aqui, vamos criar um **perfil chamado "Qogir"** com um esquema de cores baseado no tema **Nordic**.

### **Criando um Perfil Personalizado**  

Primeiro, definimos um **identificador único** (`UUID`) para o novo perfil:

```bash
PROFILE_ID=45efd52c-9923-444f-b099-ff8016d307a9
```

**Por que isso é necessário?**
- O **GNOME Terminal armazena configurações no `dconf`**, e cada perfil tem um **UUID** único.
- Se quiser criar outro perfil, basta gerar um novo UUID com:
  ```bash
  uuidgen
  ```

Agora aplicamos as **configurações visuais** ao perfil:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/background-color "'#2E3440'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/foreground-color "'#D8DEE9'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/cursor-color "'#88C0D0'"
```

**Explicação:**
- **`background-color`** → Define o fundo do terminal como `#2E3440` (azul escuro do tema Nordic).
- **`foreground-color`** → Define a cor do texto como `#D8DEE9` (branco azulado suave).
- **`cursor-color`** → Define a cor do cursor como `#88C0D0` (azul claro).

---

### **Configurando a Paleta de Cores**  

A paleta de cores melhora a experiência no terminal, tornando comandos e saídas mais legíveis:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/palette "['#3B4252', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#88C0D0', '#E5E9F0', '#4C566A', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#8FBCBB', '#ECEFF4']"
```

**O que essa paleta faz?**
- Define **cores para comandos, saídas e destaques** no terminal.
- Segue a estética do tema **Nordic**, que é **suave e confortável aos olhos**.

> **Dica**: Se quiser testar as cores antes de aplicá-las, pode usar um terminal como **Tilix** ou testar interativamente no GNOME Terminal.

---

### **Definindo o Perfil como Padrão**  

Para que o novo perfil "Qogir" seja usado automaticamente, basta rodar:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/default "'$PROFILE_ID'"
```

**Explicação:** 
- Esse comando faz com que **todas as novas janelas do terminal usem esse perfil por padrão**. 
- Se quiser alternar manualmente entre perfis, abra o GNOME Terminal e vá até **Editar → Preferências de Perfil**. 

---

## **Baixando Wallpapers do Tema Nordic** 

Para combinar o visual do terminal com a interface gráfica, podemos usar os **wallpapers do tema Nordic**:

```bash
git clone https://github.com/linuxdotexe/nordic-wallpapers
```

**O que isso faz?**
- Baixa uma coleção de wallpapers minimalistas e frios, combinando com a paleta do terminal.  

> **Dica**: Para definir um wallpaper automaticamente no GNOME, rode:

```bash
gsettings set org.gnome.desktop.background picture-uri "file://$HOME/nordic-wallpapers/wallpaper.jpg"
```

Você pode substituir `"wallpaper.jpg"` pelo nome do arquivo desejado dentro da pasta baixada.

---

## Criar um perfil no **GNOME Terminal** via linha de comando usando `dconf`. 

Criar um perfil no **GNOME Terminal** via linha de comando usando `dconf`. Isso permite **automatizar a criação e configuração de perfis** sem precisar abrir a interface gráfica.
---

## **Criando um Novo Perfil no GNOME Terminal via `dconf`**  

O GNOME Terminal armazena perfis dentro de **`dconf`**, e cada perfil possui um **UUID único**. O processo para criar um novo perfil consiste em **gerar um UUID**, adicioná-lo à lista de perfis existentes e, em seguida, configurar suas propriedades.

---

### **1️⃣ Criando um Novo Perfil com UUID**  

Antes de definir as configurações, é necessário gerar um **identificador único** para o novo perfil:

```bash
PROFILE_ID=$(uuidgen)
```

Agora adicionamos esse novo perfil à lista de perfis do GNOME Terminal:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/list \
  "$(dconf read /org/gnome/terminal/legacy/profiles:/list | sed "s/]$/, '$PROFILE_ID']/")"
```

**O que acontece aqui?** 
- `uuidgen` gera um **UUID único** para o perfil. 
- O comando `dconf write` adiciona esse UUID à lista de perfis disponíveis no GNOME Terminal. 

> **Dica**: Se quiser ver a lista de perfis existentes, use: 
```bash
dconf read /org/gnome/terminal/legacy/profiles:/list
```

---

### **2️⃣ Nomeando o Perfil**  

Agora podemos definir um nome amigável para o perfil, por exemplo, **Qogir**:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/visible-name "'Qogir'"
```

✅ **Isso define o nome do perfil** no GNOME Terminal.

> **Dica**: Para listar todos os perfis e seus UUIDs, use:
```bash
dconf list /org/gnome/terminal/legacy/profiles:/
```

---

### **3️⃣ Configurando Cores e Estilos**  

Agora aplicamos o esquema de cores baseado no tema **Nordic**:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/background-color "'#2E3440'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/foreground-color "'#D8DEE9'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/cursor-color "'#88C0D0'"
```

**O que acontece aqui?** 
- `background-color` → Cor de fundo do terminal. 
- `foreground-color` → Cor do texto no terminal. 
- `cursor-color` → Cor do cursor. 

Agora, aplicamos a **paleta de cores**:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/palette \
  "['#3B4252', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#88C0D0', '#E5E9F0', '#4C566A', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#8FBCBB', '#ECEFF4']"
```

> **Dica**: Se quiser um esquema de cores diferente, pode **experimentar outras paletas** disponíveis [aqui](https://terminal.sexy/). 

---

### **4️⃣ Configurando a Fonte NerdFont**  

Para garantir que o terminal use a fonte **Source Code Pro Nerd Font**, aplicamos a seguinte configuração:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/font "'Source Code Pro Nerd Font 12'"
```

**Isso define a fonte e o tamanho do texto no terminal**. 

> **Dica**: Se quiser listar todas as fontes disponíveis, use: 
```bash
fc-list :family | sort | uniq
```

---

### **5️⃣ Definindo como Padrão**  

Agora, para garantir que o novo perfil seja **o padrão do GNOME Terminal**, basta rodar:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/default "'$PROFILE_ID'"
```

**O que isso faz?** 
- Todas as novas janelas do terminal usarão esse perfil **automaticamente**.

> **Dica**: Para verificar se o perfil foi definido corretamente, rode:
```bash
dconf read /org/gnome/terminal/legacy/profiles:/default
```

---

### **6️⃣ Configurações Opcionais**  

Se quiser aumentar a transparência do terminal:

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/use-transparent-background "true"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/background-transparency-percent "15"
```

**Isso faz com que o fundo do terminal fique levemente transparente**. 
(O valor **15** significa 15% de transparência. Pode ajustar conforme preferir). 

> **Dica**: Se quiser **desativar a transparência**, basta rodar: 
```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/use-transparent-background "false"
```

---

## **Resumo dos Comandos**

Caso queira **automatizar todo o processo**, pode copiar e colar este **script completo**:

```bash
#!/bin/bash

# Gerando um novo UUID para o perfil
PROFILE_ID=$(uuidgen)

# Adicionando o novo perfil ao GNOME Terminal
dconf write /org/gnome/terminal/legacy/profiles:/list \
  "$(dconf read /org/gnome/terminal/legacy/profiles:/list | sed "s/]$/, '$PROFILE_ID']/")"

# Nomeando o perfil
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/visible-name "'Qogir'"

# Configurando cores
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/background-color "'#2E3440'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/foreground-color "'#D8DEE9'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/cursor-color "'#88C0D0'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/palette \
  "['#3B4252', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#88C0D0', '#E5E9F0', '#4C566A', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#8FBCBB', '#ECEFF4']"

# Configurando fonte NerdFont
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/font "'Source Code Pro Nerd Font 12'"

# Definindo o novo perfil como padrão
dconf write /org/gnome/terminal/legacy/profiles:/default "'$PROFILE_ID'"

echo "Perfil 'Qogir' criado e definido como padrão!"
```

**Salve esse script como `config_gnome_terminal.sh` e execute com:** 
```bash
chmod +x config_gnome_terminal.sh
./config_gnome_terminal.sh
```

---

## **Conclusão**

Agora você pode **criar e configurar um perfil do GNOME Terminal inteiramente via linha de comando**!

**Personalizações feitas:** 
 * Criação automática do perfil via **UUID**. 
 * Aplicação de **paleta de cores do tema Nordic**. 
 * Definição da **Fonte NerdFont**. 
 * Configuração como **perfil padrão do terminal**. 
 * Opção para **fundo transparente**. 

---

## **Configurando o `tmux`**

O **`tmux`** é um **multiplexador de terminal**, permitindo dividir a tela, alternar entre sessões e aumentar a produtividade no terminal.

Vamos criar um arquivo de configuração:

```bash
vim ~/.tmux.conf
```

E adicionar:

```bash
# Exibir barra de status com cores personalizadas
set -g status-bg black
set -g status-fg green
set -g status-left '[#S]'
set -g status-right '[%Y-%m-%d %H:%M]'

# Definir layout padrão como tiled
setw -g main-pane-height 60

# Permite que o usuário envie o mesmo comando simultaneamente para todos os painéis da janela ativa.
bind-key a set-window-option synchronize-panes\; display-message "synchronize-panes is now #{?pane_synchronized,on,off}"
```

**Explicação:**
- **Barra de status personalizada:** 
  - Fundo preto (`status-bg black`), texto verde (`status-fg green`). 
  - Exibe **nome da sessão à esquerda** (`[#S]`) e **data/hora à direita**. 
- **Layout de janelas (`main-pane-height`)** → Define um painel principal maior. 
- **Sincronização de comandos (`synchronize-panes`)** → Permite executar o mesmo comando em **todos os painéis simultaneamente**. 

> **Dica**: Para carregar as novas configurações do `tmux` sem reiniciar, rode: 
```bash
tmux source-file ~/.tmux.conf
```

---

## **Conclusão**

Agora você tem um **terminal estilizado e altamente produtivo** com: 
 * Um perfil visual baseado no tema **Nordic**. 
 * **Paleta de cores suave e confortável aos olhos**. 
 * **Wallpapers combinando com o tema**. 
 * **`tmux` configurado para produtividade no terminal**. 


### vim
**Configurando o Vim para um Ambiente Produtivo** 

O Vim é um dos editores de texto mais poderosos e customizáveis. Vamos configurá-lo com **suporte a cores, melhor navegação, atalhos eficientes e um visual moderno** usando o **vim-plug** para gerenciamento de plugins.

---

**Instalando o Gerenciador de Plugins (`vim-plug`)**

Antes de configurar o Vim, instalamos o **vim-plug**, um gerenciador de plugins para facilitar a instalação e atualização de extensões.

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

**Criando e Editando o Arquivo de Configuração (`.vimrc`)**  

Agora, abrimos o arquivo de configuração do Vim: 

```
vim ~/.vimrc
```

```
" ======== Suporte a Cores ========
" Ativar suporte a cores de 24 bits (necessário para cores em hexadecimal)
set termguicolors

" ======== Configurações Gerais ========
filetype plugin indent on
syntax on
set title
set encoding=utf-8
set backspace=indent,eol,start " Não precisa no Debian
set noerrorbells
set confirm
set hidden
set splitbelow
set splitright

" ======== Caminhos ========
set path+=**
set noswapfile
set nobackup
set undodir=~/.vim/undodir
set undofile

" ======== Numeração de Linhas ========
set number 
set relativenumber
set scrolloff=2
set cursorline

" ======== Mudança do Cursor ========
let &t_SI="\e[6 q"
let &t_EI="\e[2 q"

" ======== Indentação ========
set autoindent
set smartindent

" ======== Tabulações ========
set expandtab
set tabstop=2
set softtabstop=2
set shiftwidth=2

" ======== Configurações de Busca ========
set ignorecase
set smartcase
set incsearch
set hls     " Será alternado no mapeamento de teclas...
let @/ = "" " Será redefinido no mapeamento de teclas...

" ======== Menu e Barra de Status ========
set wildmenu
set wildmode=longest,full
set wildoptions=pum
set noshowmode   " Negado para as definições da barra de status...
set laststatus=2

" ======== Quebra de Linha ========
set nowrap       " Negado para o mapeamento da quebra de linhas...
set linebreak

" ======== Caracteres Invisíveis ========
set nolist       " Negado para o mapeamento dos caracteres invisíveis...
set listchars=tab:›-,space:·,trail:⋯,eol:↲
set fillchars=vert:│,fold:\ ,eob:~,lastline:@

" ======== Estilo e Aparência ========
" Esquema de cores
" https://github.com/sainnhe/edge.git
call plug#begin('~/.vim/plugged')
 
" Gerenciador de plugins: temas e recursos
Plug 'sainnhe/edge'             " Tema Edge
call plug#end()

set background=dark
colorscheme edge
"colorscheme habamax

" Fundo transparente
hi Normal guibg=NONE ctermbg=NONE

" Linha do cursor
hi CursorLine guibg=#3E4452

" Linha de coluna
hi ColorColumn guibg=#202130

" Comentários em itálico
hi Comment cterm=italic gui=italic

" Divisão vertical de janelas
hi VertSplit ctermbg=NONE guibg=NONE ctermfg=7 guifg=#2c324d

" Barra de abas
hi TabLine      guifg=#9192a0 guibg=#2c324d gui=none
hi TabLineSel   guifg=#a1a2b0 guibg=NONE gui=bold
hi TabLineFill  guifg=#9192a0 guibg=#2c324d gui=none

" Seleção (modo visual)
hi Visual guifg=NONE guibg=#303140

" Cores das dobras (folding)
hi Folded guibg=NONE guifg=#505160 gui=italic cterm=italic

" ======== Configurações de Statusline ========
hi statusline   gui=bold guibg=#98C379 guifg=#101120
hi statuslinenc gui=NONE cterm=NONE guibg=#3E4452 guifg=#B0B1C0

" ======== Função: ActiveStatusLine ========
function! ActiveStatusLine()
    " Definir mapeamento do modo atual
    let g:currentmode={
        \ 'n'      : 'Normal',
        \ 'no'     : 'Normal-Operator Pending',
        \ 'v'      : 'Visual',
        \ 'V'      : 'V-Line',
        \ '<C-v>'  : 'V-Block',
        \ 's'      : 'Select',
        \ 'S'      : 'S-Line',
        \ '<C-s>'  : 'S-Block',
        \ 'i'      : 'Insert',
        \ 'R'      : 'Replace',
        \ 'Rv'     : 'V-Replace',
        \ 'c'      : 'Command',
        \ 'cv'     : 'Vim Ex',
        \ 'ce'     : 'Ex',
        \ 'r'      : 'Prompt',
        \ 'rm'     : 'More',
        \ 'r?'     : 'Confirm',
        \ '!'      : 'Shell',
        \ 't'      : 'Terminal'
    \}

    " Configurar statusline
    set statusline=%0*\ %{toupper(g:currentmode[mode()])}
    set statusline+=\ %1*\ [%n]\ %t%{&modified!=''?'\ \|\ +':''}
    set statusline+=\ %2*%=
    set statusline+=%{&ff}\ \|\ %{&fenc!=''?&fenc:&enc}
    set statusline+=\ \|\ %{&filetype!=''?tolower(&filetype):'no\ ft'}
    set statusline+=\ %1*\ %p%%\ %0*\ \ %l:%c\ \ 

    " Definir cores personalizadas para statusline
    hi User1 gui=NONE cterm=NONE guifg=#b0b1c0 guibg=#3E4452
    hi User2 gui=NONE cterm=NONE guifg=#b0b1c0 guibg=#3E4452
endfunction

" Inicializar a configuração do statusline
call ActiveStatusLine()
```

**Instalando os Plugins**

Após adicionar os plugins ao `.vimrc`, **instale-os** executando:

```vim
:PlugInstall
```

Isso **baixa e instala** o tema **Edge** e quaisquer outros plugins adicionados ao Vim.

---

### **Instalando Aplicativos Essenciais**

Agora que o terminal e o Vim estão configurados, vamos instalar alguns aplicativos essenciais para **produtividade, navegação e acesso remoto**.

---

#### **Instalando o Joplin (Aplicativo de Notas)**

O **Joplin** é um **gerenciador de notas open-source**, uma alternativa ao Evernote. Ele permite **sincronizar notas com serviços de nuvem** como Nextcloud, Dropbox e OneDrive. 

Para instalá-lo via **Snap**, basta rodar: 

```bash
sudo snap install joplin-desktop
```

**Explicação:**
- O `snap install` baixa e instala o Joplin **diretamente dos repositórios oficiais**. 
- O **Joplin suporta Markdown**, tornando-o ótimo para **documentação e anotações técnicas**. 

> **Dica**: Se preferir instalar via **AppImage**, rode: 
```bash
wget -O joplin.AppImage https://github.com/laurent22/joplin/releases/latest/download/Joplin.AppImage
chmod +x joplin.AppImage
./joplin.AppImage
```

> **Dica Extra**: Para sincronizar com **Nextcloud/Dropbox**, vá até:
**Ferramentas → Opções → Sincronização** e configure o serviço desejado.

---

#### **Instalando o Navegador Brave** 

O **Brave** é um navegador focado em **privacidade**, bloqueando **anúncios e rastreadores** automaticamente. Além disso, é baseado no **Chromium**, o que significa **compatibilidade total** com extensões do Google Chrome. 

Para instalá-lo via **Snap**, use: 

```bash
sudo snap install brave
```

**Explicação:** 
- Snap garante **atualizações automáticas** e sandboxing para maior segurança. 
- O **Brave consome menos memória RAM** do que o Google Chrome, ideal para máquinas com poucos recursos. 
- Ele possui suporte nativo para **Tor**, permitindo navegação anônima.

> **Dica**: Para definir o Brave como **navegador padrão**, rode: 
```bash
xdg-settings set default-web-browser brave.desktop
```

> **Dica Extra**: Se quiser **ativar recompensas do Brave** e ganhar BAT (Basic Attention Token), vá até:
**Configurações → Brave Rewards**.

---

#### **Instalando o Remmina (Acesso Remoto a Servidores)** 

O **Remmina** é um **cliente de acesso remoto** para protocolos como **RDP, SSH e VNC**, essencial para administradores de sistemas.

Para instalá-lo, basta rodar: 

```bash
sudo apt install remmina -y
```

**Explicação:** 
- Instalado via `apt`, garantindo compatibilidade total com o Ubuntu. 
- **Permite conexão remota a servidores Windows (via RDP)** e Linux (via SSH e VNC). 
- Suporta **armazenamento de credenciais seguras** para acesso rápido. 

---

### **Configurando o KVM no Ubuntu 24.04**

O **KVM (Kernel-based Virtual Machine)** é uma solução de virtualização nativa do Linux que permite rodar **máquinas virtuais (VMs) com alto desempenho** e suporte para vários sistemas operacionais.

#### **Instalando o KVM e Ferramentas de Gerenciamento**  

O primeiro passo é instalar o **gerenciador de máquinas virtuais (`virt-manager`)** e algumas ferramentas essenciais:

```bash
sudo apt install virt-manager libosinfo-bin mkisofs -y
```

**Explicação:** 
- **`virt-manager`** → Interface gráfica para gerenciar VMs de forma intuitiva. 
- **`libosinfo-bin`** → Biblioteca que fornece informações sobre sistemas operacionais para facilitar a criação de VMs. 
- **`mkisofs`** → Ferramenta para criar imagens ISO, útil para personalizar instalações de VMs. 

> **Dica**: Para verificar se a virtualização está ativada no hardware, rode: 
```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```
Se o retorno for **maior que 0**, significa que seu processador suporta virtualização. 

---

#### **Adicionando o Usuário ao Grupo `kvm`**  

Por padrão, **somente usuários com privilégios administrativos podem criar e gerenciar VMs**. Para evitar a necessidade de `sudo` toda hora, adicione seu usuário ao grupo `kvm`:

```bash
sudo usermod -aG kvm $USER
```

**Isso permite que o usuário gerencie máquinas virtuais sem precisar de permissões de superusuário.**

> **Dica**: Para aplicar a mudança imediatamente, faça logout e login novamente ou rode: 
```bash
newgrp kvm
```

---

#### **Reiniciando o Sistema para Aplicar as Configurações** 

```bash
sudo reboot
```

**Isso garante que todas as permissões e serviços sejam recarregados corretamente.** 

---

#### **Criando Diretórios para Armazenamento de VMs**  

Para manter o ambiente organizado, criamos diretórios específicos para **imagens de VMs, templates e ISOs**: 

```bash
mkdir -p ~/kvm/images
mkdir -p ~/kvm/templates
mkdir -p ~/kvm/isos
```

**O que cada pasta armazena?**
- **`~/kvm/images/`** → Arquivos de disco (`.qcow2`, `.img`) das VMs criadas. 
- **`~/kvm/templates/`** → Modelos pré-configurados para criação rápida de VMs. 
- **`~/kvm/isos/`** → Imagens ISO de sistemas operacionais para instalação. 

> **Dica**: Se tiver outro disco dedicado para VMs, pode montar os diretórios lá: 
```bash
sudo mount /dev/sdb1 ~/kvm
```

---

#### **Criando Pools de Armazenamento no `virsh`**  

O **`virsh`** é a ferramenta de linha de comando do `libvirt`, usada para gerenciar VMs. Definimos pools de armazenamento para organizar os recursos:

```bash
virsh pool-define-as --name default --type dir --target ~/kvm/images
virsh pool-define-as --name templates --type dir --target ~/kvm/templates
virsh pool-define-as --name isos --type dir --target ~/kvm/isos
```

**Isso permite que o `libvirt` gerencie automaticamente os arquivos nesses diretórios.** 

> **Dica**: Para listar pools disponíveis, use: 
```bash
virsh pool-list --all
```

---

#### **Configurando Pools para Inicializar Automaticamente**  

Para garantir que os pools de armazenamento sejam ativados **sempre que o sistema iniciar**, rodamos: 

```bash
virsh pool-autostart default
virsh pool-autostart templates
virsh pool-autostart isos
```

**Isso evita que você precise iniciar manualmente os pools toda vez que reiniciar o sistema.** 

---

#### **Iniciando os Pools de Armazenamento**  

Agora, ativamos os pools definidos:

```bash
virsh pool-start default
virsh pool-start templates
virsh pool-start isos
```

> **Dica**: Para exibir detalhes de um pool específico, use:
```bash
virsh pool-info default
```

---

#### **Melhorando a Segurança com AppArmor**

O **AppArmor** é um sistema de controle de segurança que protege aplicativos no Linux. Podemos criar um perfil personalizado para restringir o acesso das VMs a apenas diretórios específicos:

```bash
sudo tee /etc/apparmor.d/libvirt/TEMPLATE.qemu > /dev/null <<EOF
#include <tunables/global>

profile LIBVIRT_TEMPLATE flags=(attach_disconnected) {
  #include <abstractions/libvirt-qemu>
  /home/$USER/kvm/images/** rwk,
  /home/$USER/kvm/templates/** rwk,
}
EOF
```

**Isso impede que as VMs acessem arquivos fora dos diretórios definidos, aumentando a segurança.**

Para ativar o perfil AppArmor:

```bash
sudo apparmor_parser -r /etc/apparmor.d/libvirt/TEMPLATE.qemu
sudo systemctl restart libvirtd
```

> **Dica**: Para verificar logs do AppArmor, rode:
```bash
sudo journalctl -xe | grep apparmor
```

---

#### **Ajustando Permissões no `libvirt` (Opcional, para Testes)**  

Em ambientes de **teste**, pode ser necessário desativar algumas restrições de segurança no `libvirt`. 

> **Atenção:** **Essa configuração reduz a segurança do sistema** e deve ser usada apenas para testes locais. 

```bash
sudo cp -p /etc/libvirt/qemu.conf{,.dist}
sudo sed -i '/#user = "root"/a user = "'$USER'"' /etc/libvirt/qemu.conf
sudo sed -i '/#group = "root"/a group = "'$USER'"' /etc/libvirt/qemu.conf
sudo systemctl restart libvirtd
```

**Isso permite que VMs rodem como usuário normal, sem precisar de `root` para criação e execução.**

> **Dica**: Para reverter as configurações, restaure o backup original: 
```bash
sudo mv /etc/libvirt/qemu.conf.dist /etc/libvirt/qemu.conf
sudo systemctl restart libvirtd
```

---

### **Instalando o Terraform no Ubuntu 24.04** 

O **Terraform** é uma ferramenta de **Infraestrutura como Código (IaC)** que permite **provisionar, gerenciar e versionar** recursos em provedores de nuvem como **AWS, GCP, Azure** e outros.

Este guia cobre **a instalação automática da versão mais recente** diretamente do repositório oficial do Terraform. 

---

#### **Instalando Dependências** 

Antes de instalar o Terraform, precisamos garantir que o sistema tenha as ferramentas necessárias para baixar e extrair os arquivos: 

```bash
sudo apt update
sudo apt install wget curl unzip -y
```

**Explicação:** 
- `wget` → Usado para baixar arquivos da internet. 
- `curl` → Necessário para consultar a API do GitHub e obter a versão mais recente do Terraform. 
- `unzip` → Utilizado para extrair o arquivo `.zip` baixado. 

> **Dica**: Para verificar se essas ferramentas já estão instaladas, rode: 
```bash
dpkg -l | grep -E 'wget|curl|unzip'
```

---

#### **Obtendo a Última Versão do Terraform** 

```bash
TER_VER=`curl -s https://api.github.com/repos/hashicorp/terraform/releases/latest | grep tag_name | cut -d: -f2 | tr -d \"\,\v | awk '{$1=$1};1'`
```

**O que acontece aqui?** 
- `curl -s` → Faz uma requisição silenciosa para a API do GitHub. 
- `grep tag_name` → Filtra a linha que contém a versão mais recente. 
- `cut -d: -f2` → Remove a chave `"tag_name":` e mantém apenas o valor da versão. 
- `tr -d \"\,\v` → Remove aspas e vírgulas desnecessárias. 
- `awk '{$1=$1};1'` → Remove espaços em branco extras. 

> **Dica**: Se quiser conferir manualmente qual versão será baixada, rode: 
```bash
echo $TER_VER
```

---

#### **Baixando o Terraform**

Agora usamos a versão capturada para baixar o arquivo correto: 

```bash
wget https://releases.hashicorp.com/terraform/${TER_VER}/terraform_${TER_VER}_linux_amd64.zip
```

**Isso baixa a versão mais recente do Terraform para sua máquina.** 

> **Dica**: Se precisar de uma versão específica, substitua `${TER_VER}` por uma versão fixa, como: 
```bash
wget https://releases.hashicorp.com/terraform/1.5.5/terraform_1.5.5_linux_amd64.zip
```

---

#### **Extraindo e Movendo para `/usr/local/bin`** 

```bash
unzip terraform_${TER_VER}_linux_amd64.zip
sudo mv terraform /usr/local/bin/
rm terraform_${TER_VER}_linux_amd64.zip
```

**Explicação:** 
- `unzip` → Extrai o executável `terraform`.
- `sudo mv terraform /usr/local/bin/` → Move o binário para `/usr/local/bin`, tornando-o acessível globalmente. 
- `rm terraform_${TER_VER}_linux_amd64.zip` → Remove o arquivo `.zip` baixado para economizar espaço. 

> **Dica**: Para verificar se o Terraform foi movido corretamente, rode: 
```bash
which terraform
```

---

#### **Verificando a Instalação** 

```bash
terraform --version
```

---

#### **Configuração Inicial do Terraform**

Agora que o Terraform está instalado, criamos um diretório de trabalho e inicializamos o ambiente:

```bash
mkdir -p ~/terraform-projects/my-first-project
cd ~/terraform-projects/my-first-project
terraform init
```

**Explicação:**
- `mkdir -p ~/terraform-projects/my-first-project` → Cria um diretório organizado para armazenar os arquivos do Terraform. 
- `cd ~/terraform-projects/my-first-project` → Acessa o diretório recém-criado. 
- `terraform init` → Inicializa o projeto Terraform, baixando os plugins necessários. 

> **Dica**: Para testar o funcionamento, crie um arquivo básico `main.tf`: 
```bash
echo 'terraform { required_version = ">= 1.0.0" }' > main.tf
terraform validate
```
Se a saída for `Success! The configuration is valid.`, significa que o Terraform está pronto para uso.

---