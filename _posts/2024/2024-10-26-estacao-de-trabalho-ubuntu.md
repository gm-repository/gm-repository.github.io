---
title: "Tutorial de Configuração da Estação de Trabalho Ubuntu"
description: "Guia completo para configurar uma estação de trabalho no Ubuntu com ferramentas essenciais, Tmux e Vim personalizados para desenvolvimento e administração de sistemas."
date: 2024-10-26 18:02:01 -0300
version: "1.0"
tags:
  - ubuntu
  - tmux
  - vim
  - configuração de sistema
  - desenvolvimento
  - nerdfonts
  - tutorial
  - produtividade
license: "CC BY-SA 4.0"
language: "pt-BR"
---


### Parte 1: Instalando Ferramentas Essenciais no Ubuntu

1. **Atualizar o sistema**  
   Antes de instalar qualquer nova ferramenta, é recomendável atualizar o sistema para garantir que tudo esteja em sincronia com os repositórios mais recentes.
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Instalar Ferramentas de Desenvolvimento e Utilitários**  
   Agora, vamos instalar uma série de ferramentas úteis para o desenvolvimento e administração do sistema. Execute o seguinte comando:
   ```bash
   sudo apt install vim fzf ipcalc wget nmap \
        tcpdump git traceroute plocate \
        whois dnsutils grc tmux exa -y
   ```

   - **Explicação das Ferramentas**:
     - `vim`: Editor de texto poderoso.
     - `fzf`: Ferramenta de busca fuzzy para agilizar pesquisas em arquivos e diretórios.
     - `ipcalc`: Ferramenta para cálculo de endereços IP.
     - `wget`: Utilitário para baixar arquivos da internet.
     - `nmap`: Scanner de rede.
     - `tcpdump`: Analisador de pacotes para redes.
     - `git`: Controle de versão para desenvolvimento colaborativo.
     - `traceroute`: Utilitário para rastrear o caminho de pacotes de rede.
     - `plocate`: Destinada a localizar arquivos pelo nome de forma rápida e eficiente.
     - `whois`: Ferramenta para consultas de registros DNS.
     - `dnsutils`: Ferramentas para consultas DNS.
     - `grc`: Colore a saída de comandos para facilitar a visualização.
     - `tmux`: Multiplexador de terminais, essencial para sessões persistentes.
     - `exa` : Alternativa ao comando `ls`

3. **Configurar Aliases e o Bash Prompt**  
   Para personalizar a linha de comando, abra o arquivo `.bashrc` e adicione algumas configurações:
   ```bash
   vim ~/.bashrc
   ```

   Em seguida, adicione as seguintes linhas para melhorar a experiência de uso:
   ```bash
   [ -f /usr/share/doc/fzf/examples/key-bindings.bash ] && source /usr/share/doc/fzf/examples/key-bindings.bash

   export HISTSIZE=5000
   export HISTCONTROL=ignoredups:erasedups
   export HISTIGNORE="ls:cd:exit"
   export HISTTIMEFORMAT="%d/%m/%Y %T "

   #export PROMPT_COMMAND="history -a; history -c; history -r; $PROMPT_COMMAND"

   alias tail='grc tail'
   alias ping='grc ping'
   alias ps='grc ps'
   alias traceroute='grc traceroute'
   alias dig='grc dig'
   alias ss='grc ss'
   alias ping='grc ping -c3'
   alias ipinfo='curl http://ipinfo.io/ip'
   alias ls="exa --long --group-directories-first --icons"
   export EDITOR=vim
   export VISUAL=vim
   export PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u\[\033[01;34m\]@\[\033[01;33m\]\h\[\033[01;34m\][\[\033[00m\]\[\033[01;37m\]\w\[\033[01;34m\]]\[\033[01;31m\]\$\[\033[00m\] '
   ```

   - **Explicação das Configurações**:
     - `PS1`: Customiza o prompt do terminal.
     - `HISTSIZE` e `HISTCONTROL`: Aumentam o histórico de comandos e evitam duplicidades.
     - `PROMPT_COMMAND`: Salva e recarrega o histórico em cada comando.
     - **Aliases**: Simplificam comandos como `ping`, `ps`, `traceroute`, entre outros.

4. **Carregar as Alterações no .bashrc**  
   Para aplicar as configurações do `.bashrc` sem precisar sair do terminal, use:
   ```bash
   source ~/.bashrc
   ```
Esta primeira parte configura as ferramentas essenciais e personaliza o ambiente de terminal para facilitar o desenvolvimento e a administração. 

### Parte 2: Instalação e Configuração de NerdFont

As NerdFonts são fontes especializadas que incluem ícones úteis para desenvolvedores, especialmente em terminais com temas e plugins avançados.

1. **Baixar a NerdFont**  
   Acesse o site [NerdFonts](https://www.nerdfonts.com) para visualizar as fontes disponíveis. Neste tutorial, vamos instalar a **SourceCodePro Nerd Font**. Execute o seguinte comando para baixá-la:
   ```bash
   wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.2.1/SourceCodePro.zip
   ```

2. **Descompactar o Arquivo da Fonte**  
   Após o download, descompacte o arquivo:
   ```bash
   unzip SourceCodePro.zip
   ```

3. **Mover a Fonte para a Pasta de Fontes Locais**  
   Para instalar a fonte localmente, mova os arquivos `.ttf` para a pasta de fontes local do seu usuário:
   ```bash
   mkdir -p ~/.local/share/fonts
   mv *.ttf ~/.local/share/fonts/
   ```

4. **Atualizar o Cache de Fontes**  
   Atualize o cache de fontes para que o sistema reconheça a nova instalação:
   ```bash
   fc-cache -fv
   ```

5. **Configurar o Terminal para Usar a Fonte NerdFont**  
   Agora, abra seu terminal e acesse as configurações de fontes:
   - No menu do terminal, vá para **Preferences** (Preferências).
   - Selecione o perfil de terminal ativo (pode ser **Unnamed** ou outro).
   - Habilite a opção de **Custom Font** (Fonte personalizada).
   - Escolha **SourceCodePro Nerd Font Mono**.

Com a fonte configurada, o terminal agora pode exibir ícones e símbolos que muitos plugins e ferramentas utilizam para melhorar a visualização e a organização da informação.


### Parte 3: Instalação e Configuração do Tmux e Seus Plugins

O Tmux é uma ferramenta poderosa para gerenciar múltiplas sessões de terminal em uma única janela. Vamos configurá-lo com plugins para melhorar a funcionalidade.

1. **Instalar o Plugin Manager do Tmux (TPM)**  
   O TPM (Tmux Plugin Manager) facilita a instalação e o gerenciamento de plugins no Tmux. Para instalá-lo, execute:
   ```bash
   #git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
   git clone -b v2.1.0 https://github.com/catppuccin/tmux.git ~/.tmux/plugins/catppuccin/tmux
   ```

2. **Configurar o Arquivo `.tmux.conf`**  
   Vamos editar o arquivo de configuração do Tmux para adicionar o TPM e outras opções. Abra o arquivo `.tmux.conf`:
   ```bash
   vim ~/.tmux.conf
   ```

   No arquivo, adicione as seguintes linhas para configurar o Tmux com plugins essenciais e personalizações:
   ```bash
   # Lista de plugins
   set -g @plugin 'tmux-plugins/tpm'
   set -g @plugin 'tmux-plugins/tmux-sensible'

   # Configurações para o plugin Catppuccin
   set -g @catppuccin_flavor "mocha"            # Escolhe o tema 'mocha'
   set -g @catppuccin_window_status_style "rounded"  # Deixa o estilo das janelas arredondado

   # Carregar o plugin Catppuccin
   #run ~/.config/tmux/plugins/catppuccin/tmux/catppuccin.tmux
   run ~/.tmux/plugins/catppuccin/tmux/catppuccin.tmux

   # Estender o status do tmux com módulos
   set -g status-right-length 100
   set -g status-left-length 100
   set -g status-right "#{E:@catppuccin_status_application} #{E:@catppuccin_status_cpu} #{E:@catppuccin_status_session} #{E:@catppuccin_status_uptime} #{E:@catppuccin_status_battery}"
   ```

3. **Recarregar o Tmux**  
   Após editar o arquivo de configuração, recarregue o Tmux para aplicar as novas configurações:
   ```bash
   tmux source ~/.tmux.conf
   ```

4. **Iniciar o Tmux e Instalar os Plugins**  
   Abra o Tmux com o comando:
   ```bash
   tmux
   ```
   Em seguida, pressione `CTRL + B` seguido de `I` (maiúsculo) para instalar os plugins configurados com o TPM.

### Exemplos Alternativos para `status-right`

1. **Informações Básicas do Sistema**  
   Exibe a hora, data e o nome do host:
   ```bash
   set -g status-right "#{hostname} | %Y-%m-%d %H:%M:%S"
   ```

2. **Uso de CPU e Memória**  
   Exibe o uso atual de CPU e memória:
   ```bash
   set -g status-right "CPU: #{cpu_percentage} | MEM: #{mem_usage} | %H:%M:%S"
   ```

3. **Rede e Bateria**  
   Inclui status da rede e carga da bateria (útil para laptops):
   ```bash
   set -g status-right "IP: #{ip_address} | BATT: #{battery_percentage} | %H:%M"
   ```

4. **Informação de Sessão Tmux**  
   Exibe o número da janela atual e o nome da sessão, útil para usuários que trabalham com várias janelas e sessões:
   ```bash
   set -g status-right "Window: #I:#W | Session: #S | %H:%M:%S"
   ```

5. **Ícones NerdFonts com Informações de Sistema**  
   Combinando ícones de NerdFonts para criar uma barra visualmente mais informativa:
   ```bash
   set -g status-right " #{cpu_percentage} |  #{mem_usage} |  #{battery_percentage} |  %d/%m %H:%M"
   ```

6. **Status do Git (Para Repositórios Git)**  
   Exibe o status do Git na pasta atual:
   ```bash
   set -g status-right "#(git rev-parse --abbrev-ref HEAD 2>/dev/null) | %H:%M"
   ```

### Variáveis Padrão do Tmux para `status-right`

Além dos exemplos, aqui estão algumas variáveis padrão do Tmux que você pode usar em `status-right`:

- `#H`: Nome do host.
- `#h`: Nome do host curto.
- `#S`: Nome da sessão.
- `#I`: Número do painel.
- `#W`: Nome da janela.
- `#(command)`: Permite executar um comando e exibir sua saída.
- `%Y`, `%m`, `%d`, `%H`, `%M`, `%S`: Formatação de data e hora (Ano, mês, dia, horas, minutos e segundos).

### Outros exemplo para personalizar o tmux
```bash
set -g prefix ^A                        # Change Prefix to CTRL A

set -g default-terminal "tmux-256color"

set -g base-index 1                     # Start Counting on 1
set -g renumber-windows on              # Renumber windows if some is closed
setw -g mode-keys vi                    # Use vi keys in copy mode
set -g detach-on-destroy off            # Move to the last active session if current destroyed

set-option -g mouse on                  # Enable Mouse support Click/Resize
#set-option -g status-position top       # Move tmux status position to top

# Split windows in current folder/session
bind-key '"' split-window -c "#{pane_current_path}"
bind-key % split-window -h -c "#{pane_current_path}"
bind-key a set-window-option synchronize-panes\; display-message "synchronize-panes is now #{?pane_synchronized,on,off}"

# bind vi keys to move panels
bind-key h select-pane -L
bind-key j select-pane -D
bind-key k select-pane -U
bind-key l select-pane -R

# bind keys to resize panel
bind-key C-h resize-pane -L 5
bind-key C-k resize-pane -U 5
bind-key C-j resize-pane -D 5
bind-key C-l resize-pane -R 5
```

### Parte 4: Configuração do Vim com Plugins e Temas

Para desenvolvedores e administradores, o Vim se torna ainda mais poderoso com plugins e configurações personalizadas. Vamos configurá-lo com o **vim-plug** para gerenciar plugins, além de instalar temas e funcionalidades úteis para infraestrutura como código.

1. **Instalar o Gerenciador de Plugins do Vim (vim-plug)**  
   O **vim-plug** simplifica a instalação e o gerenciamento de plugins no Vim. Execute o comando abaixo para instalar o vim-plug:
   ```bash
   curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
   ```

2. **Configurar o Arquivo `.vimrc`**  
   Em seguida, abra o arquivo de configuração do Vim para personalizar sua aparência e funcionalidades:
   ```bash
   vim ~/.vimrc
   ```

   Adicione o seguinte conteúdo para configurar o Vim com plugins específicos e ajustes de interface:
   ```vim
   " Inicialização básica
   set nocompatible               " Desabilita a compatibilidade com o vi
   filetype off                   " Desativa a detecção de tipo de arquivo

   " Configurar plugins com vim-plug
   call plug#begin('~/.vim/plugged')

   " Plugins para infraestrutura como código
   Plug 'hashivim/vim-terraform'          " Suporte a Terraform
   Plug 'pearofducks/ansible-vim'         " Suporte a Ansible

   " Plugin para navegação de arquivos
   Plug 'preservim/nerdtree'              " Navegação de arquivos em árvore
   Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
   Plug 'junegunn/fzf.vim'

   " Tema visual confortável
   Plug 'joshdick/onedark.vim'            " Tema One Dark

   call plug#end()

   " Configuração de temas e interface
   syntax enable                           " Habilita o destaque de sintaxe
   set background=dark                     " Define o tema como escuro
   colorscheme onedark                     " Aplica o tema One Dark

   " Configurações gerais de edição
   set number                              " Mostra números de linha
   set smartindent                         " Indentação inteligente
   set tabstop=2 shiftwidth=2 expandtab    " Configura tabs e espaços

   " Atalhos úteis
   map <C-n> :NERDTreeToggle<CR>           " Ctrl+n para abrir/fechar o NERDTree
   map <C-f> :Files<CR>                    " Ctrl+f para buscar arquivos com fzf

   " Configurações para Terraform e Ansible
   let g:terraform_fmt_on_save = 1         " Formata arquivos Terraform ao salvar
   let g:ansible_unindent_after_newline = 1 " Melhora a indentação para Ansible

   " Encerramento automático de tags e strings
   au FileType html,xml,javascript autocmd BufWritePre <buffer> %s/\s\+$//e

   " Retorna ao último local do cursor ao reabrir o arquivo
   if has("autocmd")
     au BufReadPost * if line("'\"") > 0 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
   endif

   " Encerra sessão de edição automaticamente ao fechar o Vim
   au VimLeave * call system('echo "Sessão encerrada às " . strftime("%c") >> ~/.vim/sessao.log')
   ```

3. **Instalar os Plugins no Vim**  
   Abra o Vim e execute o comando `:PlugInstall` para instalar todos os plugins configurados:
   ```vim
   :PlugInstall
   ```

4. **Configuração de Funções Úteis para Infraestrutura como Código**  
   Algumas funções adicionais foram configuradas para melhorar o suporte a arquivos Terraform e Ansible, com formatação automática e ajustes de indentação.


### Seção Final: Dicas de Solução de Problemas Comuns


#### Problemas Comuns e Soluções

1. **Erro ao Carregar Plugins no Vim**
   - **Problema**: Após executar `:PlugInstall`, alguns plugins não foram instalados corretamente ou aparecem mensagens de erro.
   - **Solução**:
     - Certifique-se de que a conexão com a internet está estável, pois o Vim precisa baixar os plugins de repositórios online.
     - Verifique se o vim-plug foi instalado corretamente no caminho `~/.vim/autoload/plug.vim`. Se necessário, reinstale com o comando:
       ```bash
       curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
           https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
       ```
     - Execute novamente o comando `:PlugInstall` para tentar reinstalar os plugins ausentes.

2. **Tmux não Carrega Plugins ao Iniciar**
   - **Problema**: Mesmo após a configuração do `.tmux.conf`, os plugins do Tmux não carregam ou não são instalados ao pressionar `CTRL + B + I`.
   - **Solução**:
     - Verifique se o diretório `~/.tmux/plugins/tpm` existe. Se não, reinstale o Tmux Plugin Manager (TPM) com o comando:
       ```bash
       git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
       ```
     - Certifique-se de que o Tmux está lendo o arquivo `.tmux.conf`. Reinicie o Tmux e recarregue as configurações:
       ```bash
       tmux source ~/.tmux.conf
       ```
     - Tente pressionar `CTRL + B + I` novamente para instalar os plugins.

3. **Fonte NerdFont Não Aparece no Terminal**
   - **Problema**: Mesmo após a instalação da NerdFont, o terminal não exibe os ícones ou os caracteres especiais corretamente.
   - **Solução**:
     - Verifique se a fonte foi movida para o diretório correto (`~/.local/share/fonts`) e se o cache foi atualizado com o comando:
       ```bash
       fc-cache -fv
       ```
     - Abra as **Preferências** do seu terminal e certifique-se de que a **SourceCodePro Nerd Font Mono** (ou a fonte NerdFont desejada) está selecionada como fonte personalizada.
     - Se o problema persistir, tente reiniciar o terminal ou a sessão do sistema.

4. **Erros na Configuração do `.vimrc` ou `.tmux.conf`**
   - **Problema**: Ao abrir o Vim ou o Tmux, aparecem mensagens de erro indicando problemas de sintaxe nos arquivos `.vimrc` ou `.tmux.conf`.
   - **Solução**:
     - Verifique a sintaxe dos arquivos `.vimrc` e `.tmux.conf`. Erros de digitação em comandos ou falta de aspas e parênteses são causas comuns de falhas.
     - Comente as linhas adicionadas recentemente para identificar qual configuração está gerando o erro. No Vim, você pode usar `"` no início da linha, e no Tmux, `#`:
       ```vim
       " Comentário em .vimrc
       ```
       ```bash
       # Comentário em .tmux.conf
       ```

5. **Desempenho Lento no Vim com Plugins**
   - **Problema**: O Vim fica lento ao abrir ou editar arquivos, especialmente quando muitos plugins estão ativos.
   - **Solução**:
     - Verifique o uso de memória e CPU no sistema para garantir que há recursos disponíveis.
     - Desabilite plugins temporariamente para identificar qual deles pode estar afetando o desempenho. Para desabilitar, comente a linha correspondente no bloco `plug#begin` e `plug#end` e execute `:PlugClean`:
       ```vim
       " Plug 'junegunn/fzf.vim'
       ```

### Dica Extra: Melhorando o Fluxo de Trabalho com Tmux e Vim Integrados

Uma configuração avançada e opcional é integrar o Tmux e o Vim para uma experiência mais fluida:

- Adicione a seguinte linha ao seu `.vimrc` para que o Vim abra automaticamente em uma janela Tmux dividida, facilitando o gerenciamento de múltiplas sessões:
  ```vim
  let g:tmux_navigator_no_mappings = 1
  ```

- **Instalar o Plugin Tmux Navigator**:
   - O **Tmux Navigator** permite navegar entre os painéis do Tmux e as janelas do Vim com atalhos, aumentando a produtividade:
     ```bash
     git clone https://github.com/christoomey/vim-tmux-navigator ~/.vim/plugged/vim-tmux-navigator
     ```

   - Após a instalação, adicione ao seu `.tmux.conf` as configurações de navegação para Tmux:
     ```bash
     set -g @plugin 'christoomey/vim-tmux-navigator'
     ```
