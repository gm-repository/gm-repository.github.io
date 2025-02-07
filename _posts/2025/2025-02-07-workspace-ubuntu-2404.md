---
layout: post
title: "Configurando o Ubuntu 24.04 para SysAdmins"
date: 2025-02-07 00:00:01 -0300
categories: [Linux, Ubuntu]
tags: [Ubuntu, KVM, Terraform, Bash, Terminal, Workspace, Linux]
excerpt: "Estação de trabalho com Ubuntu 24.04"
---


## Introdução  

O Ubuntu 24.04 é uma excelente escolha para desenvolvedores, administradores de sistemas e entusiastas de tecnologia. Com sua estabilidade, segurança e flexibilidade, ele pode ser configurado para atender às mais diversas necessidades, desde um ambiente de desenvolvimento até um servidor de virtualização.

Neste guia completo, você aprenderá a: 
 * Personalizar a interface do **GNOME** para melhorar a usabilidade. 
 * Instalar e configurar **ferramentas essenciais** para produtividade e desenvolvimento. 
 * Configurar o **KVM** para criação e gerenciamento de máquinas virtuais. 
 * Automatizar a infraestrutura com **Terraform** e **Cloud-Init**. 
 * Ajustar o **Bash, Vim e Tmux** para um ambiente de terminal eficiente. 

Seja você um usuário avançado ou alguém configurando seu ambiente pela primeira vez, este tutorial fornecerá uma base para transformar seu Ubuntu 24.04 em uma estação de trabalho!

---

## Atualizando o Sistema  

Antes de qualquer configuração, é importante garantir que o sistema esteja atualizado. Para isso, execute:

```bash
sudo apt update && sudo apt upgrade -y
```

Isso garantirá que você tenha os pacotes mais recentes e melhor segurança no sistema.

---

## Instalando Ferramentas Essenciais  

Agora, vamos instalar um conjunto de ferramentas úteis para o dia a dia:

```bash
sudo apt install vim fzf ipcalc wget nmap \
    tcpdump git traceroute plocate \
    whois dnsutils grc tmux eza curl -y
```

### O que cada ferramenta faz?  
- **vim** → Editor de texto avançado.
- **fzf** → Pesquisa fuzzy para encontrar arquivos rapidamente.
- **ipcalc** → Cálculo de sub-redes IP.
- **wget** → Baixar arquivos da internet via terminal.
- **nmap** → Scanner de rede para análise de hosts e serviços.
- **tcpdump** → Analisador de tráfego de rede.
- **git** → Controle de versão para projetos.
- **traceroute** → Rastreia a rota dos pacotes na rede.
- **plocate** → Alternativa rápida ao `locate` para busca de arquivos.
- **whois** → Consulta informações de domínios e IPs.
- **dnsutils** → Ferramentas para análise de DNS, como `dig` e `nslookup`.
- **grc** → Adiciona cores à saída de comandos no terminal.
- **tmux** → Multiplexador de terminal, útil para gerenciar sessões.
- **eza** → Alternativa moderna ao `ls`, com exibição colorida e detalhada.
- **curl** → Transferência de dados via linha de comando.

---

## Configurando o Bash  

Agora vamos configurar o Bash para melhorar a produtividade no terminal, adicionando **atalhos**, **aliases** e um **menu SSH interativo**. 

### Criando um Arquivo de Configuração Personalizado  

Crie o arquivo `~/.bash_custom`: 

```bash
vim ~/.bash_custom
```

Adicione o seguinte conteúdo: 

```bash
# FZF - Atalhos de busca
if [ -f "$(dpkg -L fzf | grep key-bindings.bash)" ]; then
    source "$(dpkg -L fzf | grep key-bindings.bash)"
fi

# Configurações de Histórico
export HISTSIZE=5000
export HISTCONTROL=ignoredups:erasedups
export HISTIGNORE="ls:cd:exit"
export HISTTIMEFORMAT="%d/%m/%Y %T "

# Configurações do Editor Padrão
export EDITOR=vim
export VISUAL=vim

# Prompt Personalizado
export PS1="\[\e[38;5;81m\]\w\[\e[0m\] \[\e[38;5;215m\]\[\e[0m\] \[\e[38;5;204m\]→\[\e[0m\] "
```

---

###  Criando Atalhos com Aliases  

Crie o arquivo `~/.bash_aliases`:

```bash
vim ~/.bash_aliases
```

Adicione os seguintes atalhos:

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

---

### Criando Funções Úteis no Bash  

Crie o arquivo `~/.bash_functions`: 

```bash
vim ~/.bash_functions
```

Adicione a seguinte função para um **menu SSH interativo**:

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
     port=${port:-22}  # Porta padrão é 22
     echo -n "Digite o usuário (default: gean): "
     read user
     user=${user:-"gean"}
     echo -n "Digite o caminho da chave SSH (default: ~/.ssh/vms): "
     read key
     key=${key:-"$HOME/.ssh/vms"}

     # Valida IP e porta
     if [[ ! $ip =~ ^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
         echo "IP inválido."
         return
     elif [[ ! $port =~ ^[0-9]+$ ]] || (( port < 1 || port > 65535 )); then
         echo "Porta inválida."
         return
     fi

     # Verifica duplicidade no SSH config e hosts_list
     if grep -q "Host $alias" "$ssh_config" || grep -q "^$alias:" "$hosts_file"; then
         echo "Alias $alias já existe."
         return
     fi

     # Adiciona ao config e hosts_list
     {
         echo -e "\nHost $alias"
         echo "    HostName $ip"
         echo "    Port $port"
         echo "    User $user"
         echo "    IdentityFile $key"
     } >> "$ssh_config"
     echo "$alias:$ip" >> "$hosts_file"
     echo "Host $alias adicionado."
 }

 # Função para remover um host
 remove_ssh() {
     echo -n "Digite o alias do host para remover: "
     read alias
     sed -i "/^$alias:/d" "$hosts_file"
     sed -i "/^Host $alias$/,/^$/d" "$ssh_config"
     echo "Host $alias removido."
 }

 # Função para listar hosts
 list_hosts() {
     echo "Hosts disponíveis:"
     cat "$hosts_file"
 }

 # Verifica se fzf está instalado
 if ! command -v fzf &> /dev/null; then
     echo "fzf não está instalado."
     return 1
 fi

 # Menu interativo com fzf
 local choice=$( (echo "Adicionar novo host"; echo "Remover host"; echo "Listar hosts"; cat "$hosts_file") | fzf --prompt="Selecione a ação: " )

 case "$choice" in
     "Adicionar novo host")
         add_ssh
         ;;
     "Remover host")
         remove_ssh
         ;;
     "Listar hosts")
         list_hosts
         ;;
     *)
         local alias=$(echo "$choice" | cut -d':' -f1)
         if [[ -n "$alias" ]]; then
             echo "Conectando ao host $alias..."
             ssh -q "$alias" || echo "Erro ao conectar."
         else
             echo "Nenhuma opção válida selecionada."
         fi
         ;;
 esac
}
```

---

### Ativando as Configurações no `~/.bashrc`  

Para garantir que os arquivos criados sejam carregados ao abrir o terminal, execute: 

```bash
echo 'if [ -f ~/.bash_custom ]; then source ~/.bash_custom; fi' >> ~/.bashrc
echo 'if [ -f ~/.bash_aliases ]; then source ~/.bash_aliases; fi' >> ~/.bashrc
echo 'if [ -f ~/.bash_functions ]; then source ~/.bash_functions; fi' >> ~/.bashrc
```

Em seguida, aplique as mudanças: 

```bash
source ~/.bashrc
```

---

## Customizando a Interface do Ubuntu  

Este passo ensina como personalizar a aparência e o comportamento do Ubuntu, tornando a experiência mais agradável e produtiva.

### 1. Alterando a Aparência para o Modo Dark  

Para ativar o modo escuro no Ubuntu:

1. **Abra as Configurações** (Settings).
2. Vá até **Aparência** (Appearance).
3. Selecione **Dark** para ativar o tema escuro.

---

### 2. Personalizando o Ubuntu Desktop  

#### **Desativando ícones na área de trabalho**  

Se você deseja ocultar a pasta "Home" da área de trabalho:

1. Vá até **Configurações** (Settings).
2. Acesse **Desktop Icons**.
3. Desative a opção **Show Home Folder**.

---

### 3. Ajustando a Dock  

#### **Habilitando ocultação automática e desativando o modo painel**  

1. Vá para **Configurações** (Settings).
2. Selecione **Dock**.
3. Ative **Auto-hide the dock** para ocultá-la automaticamente.
4. Desative **Panel Mode** para manter a Dock compacta.

---

### 4. Instalando Ferramentas para Personalização  

Para mais opções de personalização, instale as seguintes ferramentas:

```bash
sudo apt install gnome-shell-extension-manager gnome-tweaks
```

- **GNOME Tweaks** permite ajustes avançados na interface.
- **GNOME Shell Extension Manager** gerencia extensões do GNOME.

---

### 5. Abrindo Janelas Sempre no Centro da Tela  

1. Abra o **GNOME Tweaks**.
2. Vá até **Windows**.
3. Em **Click Actions**, selecione **Center New Windows**.

Agora, toda nova janela será aberta centralizada na tela.

---

### 6. Alterando o Comportamento do Ubuntu Dock  

Por padrão, ao clicar em um ícone na Dock, o Ubuntu não minimiza a janela. Para mudar esse comportamento:

1. Abra **Extension Manager**.
2. Vá até **Ubuntu Dock**.
3. Acesse **Preferences** > **Behavior**.
4. Em **Click action**, selecione **Minimize**.

Agora, ao clicar no ícone de um aplicativo já aberto, ele será minimizado e maximizado conforme necessário.

---

##  Criando um Novo Perfil no GNOME Terminal  

Neste passo, vamos **criar e personalizar um novo perfil no GNOME Terminal**, incluindo a instalação da fonte **NerdFont**, ajustes de cores e configuração de um perfil padrão. 

---

### 1. Instalando e Configurando NerdFont  

O **NerdFont** melhora a exibição de símbolos e ícones no terminal. Vamos instalar a fonte **Source Code Pro Nerd Font**. 

#### Baixando a Fonte  

```bash
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.3.0/SourceCodePro.zip
```

#### Instalando a Fonte  

```bash
mkdir -p ~/.local/share/fonts
unzip SourceCodePro.zip -d ~/.local/share/fonts
```

#### Atualizando o Cache de Fontes  

```bash
fc-cache -fv
fc-list | grep -iE "Code Pro"
```

Se a fonte aparecer na lista, a instalação foi bem-sucedida.

---

### 2. Criando um Perfil Personalizado  

#### Fazendo Backup das Configurações  

Antes de modificar qualquer configuração, é recomendável fazer um backup:

```bash
dconf dump /org/gnome/terminal/ > gnome-terminal-backup.dconf
```

---

#### Criando um Novo Perfil  

1. **Abra as preferências do GNOME Terminal**:

   ```bash
   gnome-terminal --preferences
   ```

2. **Crie um novo perfil**: 
   - Na aba **Profiles**, clique no **sinal de "+"** para adicionar um novo perfil. 
   - Escolha um nome para o perfil e salve. 

3. **Copie o Profile ID**: 
   - Com o novo perfil selecionado, copie o **Profile ID** que aparece na parte inferior direita. 
   - Esse ID será usado para personalizar o perfil.

---

#### Armazenando o UUID  

No terminal, defina o **Profile ID** copiado como uma variável para facilitar os comandos: 

```bash
PROFILE_ID=0391c148-ee62-4c07-806f-4ccbd324e20b
```

(Substitua pelo ID real do seu perfil.) 

---

#### Personalizando o Novo Perfil  

##### **Definindo Cores Principais**  

Alteramos o **fundo**, **texto** e **cursor**: 

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/background-color "'#1C1C1E'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/foreground-color "'#DADADA'"
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/cursor-color "'#F4A623'"
```

---

##### **Definindo uma Paleta de Cores Personalizada**  

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/palette \
   "['#1C1C1E', '#E53E3E', '#76C893', '#F4A623', '#159BEE', '#A24BCF', '#56C7D6', '#EAEAEA', \
     '#3A3A3C', '#FF6B6B', '#88E4A2', '#FFC857', '#50B7F5', '#BB6BD9', '#70D8E0', '#FFFFFF']"
```

---

##### **Desativando o Tema de Cores Padrão**  

Para usar as novas cores, desative o esquema de cores padrão: 

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/use-theme-colors "false"
```

---

##### **Aplicando a Fonte Personalizada**  

Agora, configuramos o terminal para usar a **Source Code Pro Nerd Font** com tamanho **14**: 

```bash
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/font "'SauceCodePro Nerd Font Mono 14'"
```

---

#### Definindo o Novo Perfil como Padrão  

Se quiser que esse perfil seja carregado automaticamente ao abrir o GNOME Terminal, defina-o como **padrão**: 

```bash
dconf write /org/gnome/terminal/legacy/profiles:/default "'$PROFILE_ID'"
```

> Dica: Se quiser um esquema de cores diferente, pode experimentar outras paletas disponíveis aqui.

---

## Criando um Perfil GNOME Terminal Usando um Script  

Se você deseja **automatizar a criação de um novo perfil** no GNOME Terminal, pode usar um **script Bash** para configurar tudo de forma rápida e prática.

### Instalando a Fonte **Fira Code**  

Antes de criar o perfil, vamos instalar a fonte **Fira Code**, que é otimizada para programadores: 

```bash
sudo apt install fonts-firacode -y
```

---

### Criando o Script de Configuração  

Crie um arquivo de script chamado `perfil-qogir.sh`: 

```bash
vim perfil-qogir.sh
```

Adicione o seguinte conteúdo ao arquivo: 

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
  "['#3B4252', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#88C0D0', '#E5E9F0', \
    '#4C566A', '#BF616A', '#A3BE8C', '#EBCB8B', '#81A1C1', '#B48EAD', '#8FBCBB', '#ECEFF4']"

# Configurando fonte NerdFont
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/font "'Fira Code 14'"

# Para usar as novas cores, desative o esquema de cores padrão
dconf write /org/gnome/terminal/legacy/profiles:/:$PROFILE_ID/use-theme-colors "false"

# Definindo o novo perfil como padrão
dconf write /org/gnome/terminal/legacy/profiles:/default "'$PROFILE_ID'"

echo "Perfil 'Qogir' criado e definido como padrão!"
```

---

### Executando o Script  

Após salvar o arquivo, torne-o executável e execute-o:

```bash
chmod +x perfil-qogir.sh
./perfil-qogir.sh
```

Se preferir rodar sem alterar as permissões, use:

```bash
bash perfil-qogir.sh
```

---

Aqui está a seção formatada para o seu tutorial, deixando a configuração do **tmux** mais organizada e clara:  

---

## Configurando o tmux  

O **tmux** é um multiplexador de terminal que permite dividir a tela em painéis, alternar entre janelas e manter sessões persistentes. Nesta seção, vamos personalizá-lo para melhorar a produtividade.

---

### 1. Criando o Arquivo de Configuração  

Abra o arquivo de configuração do `tmux`: 

```bash
vim ~/.tmux.conf
```

Adicione o seguinte conteúdo: 

```bash
# Exibir barra de status com cores personalizadas
set -g status-bg black
set -g status-fg green
set -g status-left '[#S]'
set -g status-right '[%Y-%m-%d %H:%M]'

# Definir layout padrão como tiled
setw -g main-pane-height 60

# Permite que o usuário envie o mesmo comando simultaneamente para todos os painéis da janela ativa.
bind-key S set-window-option synchronize-panes\; display-message -F "#[fg=green]sync #{?pane_synchronized,ON,OFF}"
```

---

### 2. Aplicando as Configurações Sem Reiniciar  

Após salvar o arquivo, recarregue as configurações sem precisar reiniciar o `tmux`: 

```bash
tmux source-file ~/.tmux.conf
```

---

### 3. O Que Essas Configurações Fazem?  

 * **Barra de status personalizada** com cores e informações úteis. 
 * **Layout padrão como tiled**, organizando os painéis automaticamente. 
 * **Sincronização de comandos** (`S` ativa ou desativa o modo de comando simultâneo para todos os painéis). 

---

## Configurando o Vim  

O **Vim** é um editor poderoso e altamente personalizável. Nesta seção, vamos instalar um **gerenciador de plugins**, criar um arquivo de configuração (`.vimrc`) e aplicar **temas e melhorias visuais**.

---

### 1. Instalando o Gerenciador de Plugins (**vim-plug**)  

Para gerenciar temas e extensões no Vim, usaremos o **vim-plug**. Instale-o com o seguinte comando: 

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

---

### 2. Criando e Editando o Arquivo de Configuração  

Abra o arquivo de configuração do Vim: 

```bash
vim ~/.vimrc
```

Adicione o seguinte conteúdo para configurar cores, atalhos e personalizações: 

```vim
" ======== Suporte a Cores ========
" Ativar suporte a cores de 24 bits (necessário para cores em hexadecimal)
set termguicolors

" ======== Configurações Gerais ========
filetype plugin indent on
syntax on
set title
set encoding=utf-8
set backspace=indent,eol,start
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
set hls
let @/ = ""

" ======== Menu e Barra de Status ========
set wildmenu
set wildmode=longest,full
set wildoptions=pum
set noshowmode
set laststatus=2

" ======== Quebra de Linha ========
set nowrap
set linebreak

" ======== Caracteres Invisíveis ========
set nolist
set listchars=tab:›-,space:·,trail:⋯,eol:↲
set fillchars=vert:│,fold:\ ,eob:~,lastline:@

" ======== Estilo e Aparência ========
" Gerenciador de plugins: temas e recursos
call plug#begin('~/.vim/plugged')

Plug 'sainnhe/edge'             " Tema Edge
"Plug 'joshdick/onedark.vim'    " Tema One Dark

call plug#end()

set background=dark
colorscheme edge
"colorscheme onedark
"colorscheme habamax

" ======== Ajustes Visuais ========
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
    " Configurar statusline sem o modo atual
    set statusline=%1*\ [%n]\ %t%{&modified!=''?'\ \|\ +':''}
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

---

### 3. Aplicando as Configurações  

Após salvar o `.vimrc`, abra o Vim e instale os plugins com o **vim-plug**: 

1. Abra o Vim: 

   ```bash
   vim
   ```

2. Digite o comando dentro do Vim: 

   ```vim
   :PlugInstall
   ```

Isso instalará o **tema Edge** e qualquer outro plugin adicionado ao `.vimrc`. 

---

## Instalando Aplicativos Essenciais  

Nesta seção, vamos instalar alguns aplicativos úteis para produtividade, navegação e gerenciamento remoto no Ubuntu.

---

### 1. Instalando Aplicativos via Snap  

O **Snap** é um gerenciador de pacotes universal que facilita a instalação e atualização de aplicativos. Para instalar o **Joplin** (anotações) e o **Brave** (navegador focado em privacidade), execute:

```bash
sudo snap install joplin-desktop brave
```

---

### 2. Instalando Aplicativos via APT  

O **APT** (Advanced Package Tool) é o gerenciador de pacotes padrão do Ubuntu. Para instalar o **Remmina** (acesso remoto) e o **VLC** (player de mídia), use:

```bash
sudo apt install remmina vlc -y
```

---

### Conclusão  

 * **Joplin** → Aplicativo de anotações e organização.
 * **Brave** → Navegador seguro e focado em privacidade.
 * **Remmina** → Cliente para conexões remotas (RDP, VNC, SSH).
 * **VLC** → Reprodutor de mídia versátil.

---

## Configurando o KVM no Ubuntu  

O **KVM (Kernel-based Virtual Machine)** permite criar e gerenciar máquinas virtuais com desempenho quase nativo. Aqui vamos configurar o **Virt-Manager** e definir pools de armazenamento para melhor organização das VMs.

---

### 1. Instalando os Pacotes Necessários  

Primeiro, instale os pacotes principais para gerenciar VMs no KVM:

```bash
sudo apt install virt-manager libosinfo-bin mkisofs -y
```

Adicione seu usuário ao grupo `kvm` para permitir o uso sem privilégios de root:

```bash
sudo usermod -aG kvm $USER
```

Agora, **reinicie o sistema** para aplicar as mudanças:

```bash
sudo systemctl reboot
```

---

### 2. Criando Diretórios para Armazenamento  

Organize os arquivos das máquinas virtuais criando os seguintes diretórios: 

```bash
mkdir -p ~/kvm/images
mkdir -p ~/kvm/templates
mkdir -p ~/kvm/isos
```

- **images** → Onde as VMs serão armazenadas. 
- **templates** → Para imagens pré-configuradas. 
- **isos** → Para armazenar imagens de instalação de SOs. 

---

### 3. Configurando Pools de Armazenamento  

Crie pools de armazenamento para facilitar o gerenciamento das VMs no KVM: 

```bash
virsh pool-define-as --name default --type dir --target ~/kvm/images
virsh pool-define-as --name templates --type dir --target ~/kvm/templates
virsh pool-define-as --name isos --type dir --target ~/kvm/isos
```

Defina os pools para iniciarem automaticamente: 

```bash
virsh pool-autostart default
virsh pool-autostart templates
virsh pool-autostart isos
```

Inicie os pools de armazenamento manualmente (caso necessário): 

```bash
virsh pool-start default
virsh pool-start templates
virsh pool-start isos
```

Para verificar se os pools foram criados corretamente: 

```bash
virsh pool-list --all
virsh pool-info default
```

---

### 4. Ajustando Permissões no **AppArmor**  

Para evitar problemas de acesso ao armazenamento das VMs, aplique as seguintes permissões: 

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

Verifique o usuário e grupo usados pelo **libvirt**:

```bash
sudo grep -E '#user|#group' /etc/libvirt/qemu.conf
```

Se a saída for algo como: 
```plaintext
#user = "libvirt-qemu"
#group = "kvm"
```
Então aplique a permissão correta: 

```bash
sudo chown libvirt-qemu:kvm ~/kvm/ -R
```

Agora, **recarregue as regras do AppArmor** e reinicie o `libvirtd`: 

```bash
sudo apparmor_parser -r /etc/apparmor.d/libvirt/TEMPLATE.qemu
sudo systemctl restart libvirtd
```

Verifique se há erros no AppArmor: 

```bash
sudo journalctl -xe | grep apparmor
```

---

### 5. Baixando uma Imagem ISO do Ubuntu  

Para instalar uma máquina virtual, baixe a ISO do Ubuntu 20.04: 

```bash
wget https://releases.ubuntu.com/20.04/ubuntu-20.04.6-desktop-amd64.iso -P ~/kvm/isos
```

Confira se o **KVM** reconhece corretamente o sistema operacional: 

```bash
osinfo-query os | grep ubuntu
```

---

### 6. Criando uma Máquina Virtual  

Agora podemos criar uma VM chamada **"ubuntu20"** com as seguintes configurações: 

- **2 vCPUs** 
- **4GB de RAM** 
- **16GB de armazenamento (QCOW2)** 
- **Conexão de rede via VirtIO** 
- **Modo de CPU host-passthrough** 

Execute o seguinte comando para criar a VM: 

```bash
virt-install --virt-type kvm \
--name ubuntu20 \
--description "Ubuntu Focal Fossa" \
--cdrom ~/kvm/isos/ubuntu-20.04.6-desktop-amd64.iso \
--os-variant ubuntu20.04 \
--disk size=16,format=qcow2,bus=virtio,cache=none \
--memory 4096 \
--vcpus 2 \
--cpu host-passthrough \
--network=default,model=virtio
```

---

### Conclusão  

Agora seu ambiente KVM está pronto para criar e gerenciar máquinas virtuais! 

 * **KVM instalado e configurado**
 * **Pools de armazenamento organizados** 
 * **Permissões ajustadas para evitar problemas** 
 * **VM do Ubuntu 20.04 instalada com sucesso**

Com essa estrutura, você pode adicionar mais ISOs e criar novas VMs conforme necessário!



Aqui está a seção formatada para o seu tutorial, tornando a instalação e configuração do **Terraform** mais organizada e fácil de seguir:  

---

## Instalando e Configurando o Terraform

O **Terraform** é uma ferramenta de infraestrutura como código (IaC) que permite criar, modificar e gerenciar infraestruturas de forma declarativa. Aqui vamos instalar o Terraform e usá-lo para criar uma VM com **KVM + libvirt**. 

---

### 1. Instalando o Terraform  

#### Baixando e Instalando a Última Versão  

Primeiro, obtemos a versão mais recente do Terraform:

```bash
TER_VER=$(curl -s https://api.github.com/repos/hashicorp/terraform/releases/latest | grep tag_name | cut -d: -f2 | tr -d \"\,\v | awk '{$1=$1};1')
```

Agora, baixamos e instalamos o binário: 

```bash
wget https://releases.hashicorp.com/terraform/${TER_VER}/terraform_${TER_VER}_linux_amd64.zip
unzip terraform_${TER_VER}_linux_amd64.zip
sudo mv terraform /usr/local/bin/
rm terraform_${TER_VER}_linux_amd64.zip
```

Verifique se a instalação foi bem-sucedida: 

```bash
which terraform
terraform --version
```

---

### 2. Preparando a Imagem Base  

#### Baixando uma Imagem do Debian  

```bash
wget https://cdimage.debian.org/images/cloud/bookworm/latest/debian-12-generic-amd64.qcow2 -P ~/kvm/templates/
```

Verifique o tamanho da imagem antes de redimensioná-la:

```bash
qemu-img info ~/kvm/templates/debian-12-generic-amd64.qcow2 | grep -E 'virtual size'
```

Redimensione a imagem para adicionar **5GB** extras: 

```bash
qemu-img resize ~/kvm/templates/debian-12-generic-amd64.qcow2 +5G
```

Confirme que a imagem foi redimensionada corretamente:

```bash
qemu-img info ~/kvm/templates/debian-12-generic-amd64.qcow2 | grep -E 'virtual size'
```

---

### 3. Criando o Ambiente de Trabalho  

Crie o diretório onde o Terraform irá gerenciar as configurações: 

```bash
mkdir -p ~/Workspace/terraform/providers/libvirt/vms
cd ~/Workspace/terraform/providers/libvirt/vms
```

Gere uma chave SSH para acessar a VM:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/tfvms
```

---

### 4. Criando o Arquivo de Configuração **Terraform**  

Abra o arquivo `main.tf`: 

```bash
vim main.tf
```

Adicione o seguinte código: 

```hcl
terraform {
  required_providers {
    libvirt = {
      source = "dmacvicar/libvirt"
    }
  }
}

provider "libvirt" {
  uri = "qemu:///system"
}

resource "libvirt_volume" "os_image" {
  name   = "file-server.qcow2"
  pool   = "default"
  source = "/home/gean/kvm/templates/debian-12-generic-amd64.qcow2"
  format = "qcow2"
}

data "template_file" "user_data" {
  template = file("${path.module}/cloud_init.yml")
}

resource "libvirt_cloudinit_disk" "cloudinit_resized" {
  name      = "cloudinit_resized.iso"
  user_data = data.template_file.user_data.rendered
  pool      = "default"
}

resource "libvirt_domain" "vm-tf-01" {
  name   = "vm-tf-01"
  memory = "2048" 
  vcpu   = 2      

  cpu {
    mode = "host-passthrough"
  }

  cloudinit = libvirt_cloudinit_disk.cloudinit_resized.id

  network_interface {
    network_name   = "default"
    wait_for_lease = true
  }

  console {
    type        = "pty"
    target_port = "0"
    target_type = "serial"
  }

  disk {
    volume_id = libvirt_volume.os_image.id
  }

  graphics {
    type        = "spice"
    listen_type = "none"
  }
}

output "ip" {
  value = libvirt_domain.vm-tf-01.network_interface[0].addresses[0]
}
```

---

### 5. Configurando o **Cloud-Init**  

O Cloud-Init permite personalizar a VM no primeiro boot. 

#### Gerando a Senha Root  

```bash
mkpasswd --method=SHA-512
```

#### Criando o Arquivo **Cloud-Init**  

Abra o arquivo `cloud_init.yml`: 

```bash
vim cloud_init.yml
```

Adicione o seguinte conteúdo:

```yaml
#cloud-config

users:
  - name: gean
    gecos: "Gean Martins"
    sudo: "ALL=(ALL) NOPASSWD:ALL"
    groups: users, sudo
    shell: /bin/bash
    lock_passwd: false  
    ssh-authorized-keys:
      - ${file("~/.ssh/tfvms.pub")} 

ssh_pwauth: true  

chpasswd:
  list: |
     root: $6$/Xm3JGYtNuB51CX4$uV.8ZfByeVu.sZI.QYUqQZCg0eoDdCHlYnq/0qaH7vfU9QKaHXgxOuIIYXILibGpDP5HgmnvtU2PI3u.3luwH0
  expire: false
    
runcmd:
  - hostnamectl set-hostname vm-tf-01
```

---

### 6. Executando o Terraform  

Agora podemos iniciar a infraestrutura: 

```bash
terraform init          # Inicializa o Terraform
terraform fmt           # Formata os arquivos Terraform
terraform validate      # Valida a configuração
terraform plan          # Mostra o que será criado
terraform apply         # Aplica as mudanças
```

Se tudo estiver correto, o Terraform criará a VM e exibirá o IP de acesso:

```plaintext
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

ip = "192.168.122.146"
```

---

### 7. Verificando a Máquina Virtual  

Após a criação, podemos verificar se a VM foi iniciada corretamente:

```bash
virsh list
```

Para listar os discos da VM:

```bash
virsh domblklist vm-tf-01
```

---

### Testando e Gerenciando a Máquina Virtual  

Agora que o Terraform criou a VM, vamos testar o acesso e aprender a destruí-la quando necessário. 

---

#### 1. Testando o Acesso via SSH  

Após a criação, conecte-se à VM usando **SSH**: 

```bash
ssh 192.168.122.146
```

Se for a primeira conexão, você verá um aviso sobre a autenticidade do host: 

```plaintext
The authenticity of host '192.168.122.146 (192.168.122.146)' can't be established.
ED25519 key fingerprint is SHA256:W59Oqz85Cza0kXmXFNYsAC/PGETePiOExe7M2xylHkA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.122.146' (ED25519) to the list of known hosts.
```

Após aceitar, você será conectado à VM: 

```plaintext
Linux vm-tf-01 6.1.0-30-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.124-1 (2025-01-12) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
```

Para sair da VM, use: 

```bash
exit
```

Isso encerrará a conexão SSH. 

---

#### 2. Destruindo a Máquina Virtual  

Se precisar remover a VM criada pelo Terraform, basta executar:

```bash
terraform destroy
```

Isso removerá todos os recursos associados à VM:

```plaintext
Destroy complete! Resources: 3 destroyed.
```

---

## Conclusão  

Parabéns! Agora você tem um **Ubuntu 24.04 totalmente configurado e otimizado** para produtividade, desenvolvimento e gerenciamento de infraestrutura. 

Você aprendeu a: 
- Personalizar a interface e o terminal para melhorar sua experiência. 
- Instalar ferramentas essenciais para o dia a dia. 
- Criar e gerenciar máquinas virtuais com **KVM + libvirt**. 
- Usar **Terraform** para provisionamento automatizado. 

Com essa configuração, você pode trabalhar de forma mais eficiente, segura e organizada. **A jornada da otimização nunca termina**, então continue explorando e ajustando seu sistema conforme suas necessidades! 

---
