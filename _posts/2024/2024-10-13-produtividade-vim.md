---
layout: post
title: "Maximizando a Produtividade no Vim: Dicas, Plugins e Automação"
date: 2024-10-13 14:30:00 -0300
categories: [Vim, Produtividade, Linux]
---


## Sumário
1. [Configurações Iniciais no Vim](#parte-1-configurações-iniciais-no-vim)
2. [Atalhos de Teclado e Navegação Eficiente](#parte-2-atalhos-de-teclado-e-navegação-eficiente)
3. [Personalização Avançada](#parte-3-personalização-avançada-no-vim)
4. [Uso de Plugins Essenciais](#parte-4-uso-de-plugins-essenciais-no-vim)
5. [Automatização de Tarefas](#parte-5-automatização-de-tarefas-no-vim)
6. [Integração com Ambientes de Desenvolvimento](#parte-6-integração-com-ambientes-de-desenvolvimento)
7. [Gerenciamento de Janelas e Splits](#parte-7-gerenciamento-de-janelas-e-splits-no-vim)
8. [Boas Práticas de Edição](#parte-8-boas-práticas-de-edição)
9. [Personalização de Snippets](#parte-9-personalização-de-snippets-no-vim)


## **Parte 1: Configurações Iniciais no Vim**

A configuração do arquivo **`.vimrc`** é essencial para personalizar e melhorar a usabilidade do Vim. Abaixo estão as principais configurações para tornar o uso do Vim mais eficiente desde o início.

### **1.1 Ativação de Números de Linha**
Ativar números de linha facilita a navegação e edição, especialmente quando você trabalha com scripts longos.

- **Linha Relativa**: Exibe a linha atual e, nas outras, números relativos à linha onde o cursor está. Isso ajuda muito ao usar atalhos para navegação rápida.
  
  Adicione estas linhas ao arquivo **`~/.vimrc`**:
  ```vim
  set number          " Mostra o número da linha atual
  set relativenumber  " Mostra números relativos para facilitar navegação
  ```

### **1.2 Indentação Automática**
Para garantir que o código ou scripts que você edita sejam sempre indentados de forma correta:

- **Indentação de 4 espaços**: Ideal para scripts Bash ou Python.
- **Expansão de Tabs**: Substitui tabulações por espaços, evitando inconsistências.

Adicione ao **`~/.vimrc`**:
```vim
set tabstop=4         " Exibe tabulação como 4 espaços
set shiftwidth=4      " Indenta usando 4 espaços
set expandtab         " Converte tabs em espaços
set autoindent        " Mantém a indentação da linha anterior
set smartindent       " Detecta automaticamente a necessidade de indentação
```

### **1.3 Mapeamentos de Teclas Customizados**
Adicionar mapeamentos customizados pode acelerar seu trabalho, como salvar, sair ou abrir novos arquivos com menos teclas.

- Mapeamento para salvar com `Ctrl + S` (por padrão, Vim não usa `Ctrl + S`):
  ```vim
  nnoremap <C-s> :w<CR>
  inoremap <C-s> <Esc>:w<CR>
  ```

- Mapeamento para fechar o Vim com `Ctrl + Q`:
  ```vim
  nnoremap <C-q> :q<CR>
  ```

### **1.4 Melhorias na Interface**
Torne o Vim mais confortável para longas sessões de edição, ajustando a exibição de comandos e o comportamento da interface:

```vim
set showcmd          " Exibe os comandos digitados
set showmode         " Mostra o modo de operação (inserção, normal, etc.)
set cursorline       " Destaca a linha atual
set wildmenu         " Autocompletar interativo nos comandos
set ignorecase       " Ignora case em buscas
set smartcase        " Se houver letras maiúsculas, respeita case na busca
```

### **1.5 Melhor Desempenho e Salvamento Automático**
Para evitar a perda de dados, podemos configurar o Vim para salvar arquivos automaticamente quando você sai:

```vim
set autoread         " Atualiza o arquivo se for modificado externamente
autocmd FocusGained,BufEnter * checktime  " Verifica se o arquivo foi modificado
```

Agora que as configurações básicas e essenciais estão ajustadas, podemos passar para a próxima parte: **atalhos de teclado e navegação eficiente**.

---

## **Parte 2: Atalhos de Teclado e Navegação Eficiente**

A navegação rápida é uma das maiores vantagens do **Vim**. Vamos explorar alguns atalhos e técnicas que o ajudarão a mover-se rapidamente por arquivos grandes e realizar tarefas com menos esforço.

### **2.1 Modos de Operação no Vim**
O **Vim** possui três modos principais que você deve dominar:

- **Modo Normal**: Para navegar e executar comandos (pressione `Esc` para entrar neste modo).
- **Modo de Inserção**: Para editar o texto (`i` para entrar no modo de inserção).
- **Modo Visual**: Para selecionar texto (`v` para entrar no modo visual).

### **2.2 Navegação Básica e Eficiente**
Aqui estão alguns atalhos essenciais para mover-se rapidamente:

- **Movimentação por palavras**:
  - `w` move para a próxima palavra.
  - `b` volta para o início da palavra anterior.
  - `e` vai para o final da palavra atual.

- **Navegação rápida por linha e arquivo**:
  - `gg` vai para o início do arquivo.
  - `G` vai para o final do arquivo.
  - `:10` vai para a linha 10 (ou qualquer número que você especificar).

- **Saltos de parênteses**: Para pular diretamente entre parênteses, colchetes ou chaves:
  ```vim
  set showmatch
  ```

### **2.3 Edição Rápida de Linhas**
Estes comandos facilitam a edição de linhas no Vim:

- **Deletar uma linha inteira**: `dd`.
- **Copiar uma linha inteira**: `yy`.
- **Colar a linha copiada**: `p` (abaixo) ou `P` (acima).
  
- **Substituir o conteúdo da linha**: `cc` (apaga e entra no modo de inserção).

### **2.4 Substituições e Modificações de Texto**
- **Buscar e substituir em todo o arquivo**:
  - Substituir "foo" por "bar" no arquivo inteiro:
    ```vim
    :%s/foo/bar/g
    ```
  - Para confirmar antes de cada substituição, adicione `c`:
    ```vim
    :%s/foo/bar/gc
    ```

### **2.5 Saltos por Funções e Seções (Ideal para Arquivos Grandes)**
Você pode saltar rapidamente entre funções em arquivos grandes, especialmente scripts ou código-fonte:

- **Para localizar uma função específica**:
  - Use `/{nome_da_função}` para buscar a função.
  
- **Usar marcações para navegação**:
  - Pressione `m{letra}` para marcar um ponto no arquivo e `'` seguido da letra para voltar a essa marcação.

---


## **Parte 3: Personalização Avançada no Vim**

A personalização avançada permite que você adapte o Vim às suas preferências visuais e de produtividade, utilizando temas, configurações de fontes e a barra de status para obter um feedback visual mais rico durante a edição.

### **3.1 Aplicando Temas no Vim**

Temas podem tornar o ambiente de trabalho mais confortável e facilitar a leitura do código. O Vim suporta temas que podem ser baixados e aplicados diretamente no arquivo **`.vimrc`**.

#### **Instalando um Tema Popular (dracula, gruvbox, etc.)**
- **Passo 1**: Primeiro, crie uma pasta para os arquivos de cores:
  ```bash
  mkdir -p ~/.vim/colors
  ```

- **Passo 2**: Faça o download de um tema popular. Por exemplo, para o tema **gruvbox**:
  ```bash
  wget https://raw.githubusercontent.com/morhetz/gruvbox/master/colors/gruvbox.vim -P ~/.vim/colors/
  ```

- **Passo 3**: No arquivo **`~/.vimrc`**, adicione:
  ```vim
  syntax enable
  colorscheme gruvbox
  ```

- Outros temas populares:
  - **dracula**: [Dracula Vim Theme](https://draculatheme.com/vim)
  - **solarized**: Um tema clássico com suporte a fundo claro e escuro.

### **3.2 Ajustando Fontes no Vim**

A fonte utilizada no Vim também afeta sua produtividade. Se você estiver usando o Vim no terminal, a configuração de fontes deve ser feita no próprio terminal (como no GNOME Terminal, você pode escolher uma fonte monoespaçada como **Fira Code** ou **Ubuntu Mono**).

No **GVim** (versão gráfica do Vim), você pode ajustar a fonte diretamente no arquivo **`.gvimrc`** ou **`.vimrc`**:
```vim
set guifont=Fira\ Code:h14
```

### **3.3 Personalizando a Barra de Status com Vim-Airline**

A **barra de status** é uma ferramenta útil para fornecer informações rápidas sobre o arquivo em edição, como o modo atual, a linha/célula onde o cursor está, e o uso de branches Git.

#### **Instalando o Vim-Airline**
O **Vim-Airline** é um plugin popular que aprimora a barra de status com visual moderno e informativo.

- **Passo 1**: Instale o Vim-Airline com um gerenciador de plugins (como o **vim-plug**, que veremos na próxima seção).
  No **`.vimrc`**, adicione o Vim-Airline:
  ```vim
  Plug 'vim-airline/vim-airline'
  Plug 'vim-airline/vim-airline-themes'
  ```

- **Passo 2**: Ative o Vim-Airline no arquivo **`.vimrc`**:
  ```vim
  let g:airline#extensions#tabline#enabled = 1  " Exibe abas ativas
  let g:airline_powerline_fonts = 1  " Usa ícones especiais, se disponíveis
  ```

#### **Configuração Básica do Vim-Airline**
- **Ativar barra de status completa**:
  ```vim
  set laststatus=2
  ```

- **Mudar o tema da barra de status (compatível com o tema geral)**:
  ```vim
  let g:airline_theme='gruvbox'
  ```

A **Vim-Airline** é extremamente flexível e mostra informações úteis como posição do cursor, porcentagem do arquivo navegada, status do Git e muito mais.

---

## **Parte 4: Uso de Plugins Essenciais no Vim**

Os plugins são fundamentais para melhorar a produtividade no Vim, agregando funcionalidades como navegação de arquivos, manipulação de texto e integração com Git. Vamos abordar os principais plugins que você pode usar para tornar o Vim mais eficiente no seu fluxo de trabalho.

### **4.1 Instalando um Gerenciador de Plugins**

Antes de instalar plugins, é recomendável usar um gerenciador de plugins como o **vim-plug** para simplificar o processo de instalação e atualização de plugins.

#### **Passo 1: Instalar o vim-plug**
Execute o comando abaixo para instalar o **vim-plug**:
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### **Passo 2: Configurar o vim-plug no `.vimrc`**
No seu arquivo **`.vimrc`**, adicione as seções de inicialização de plugins:
```vim
call plug#begin('~/.vim/plugged')

" Coloque aqui os plugins que deseja instalar

call plug#end()
```

Agora você está pronto para adicionar os plugins essenciais!

### **4.2 Plugin `NERDTree`: Navegação de Arquivos**
O **NERDTree** permite navegar pelo sistema de arquivos diretamente no Vim, o que é especialmente útil para abrir múltiplos arquivos sem sair do editor.

- **Instalar NERDTree**:
  No **`.vimrc`**, adicione:
  ```vim
  Plug 'preservim/nerdtree'
  ```

- **Atalho para abrir o NERDTree**:
  ```vim
  nnoremap <C-n> :NERDTreeToggle<CR>
  ```

### **4.3 Plugin `fzf.vim`: Busca Rápida de Arquivos**
O **fzf.vim** é um plugin baseado no **fzf**, uma ferramenta para busca de arquivos ultra-rápida.

- **Instalar fzf e fzf.vim**:
  Primeiro, instale o **fzf** no seu sistema:
  ```bash
  sudo apt install fzf
  ```

  Depois, adicione ao **`.vimrc`**:
  ```vim
  Plug 'junegunn/fzf.vim'
  ```

- **Uso**:
  - Para buscar arquivos rapidamente:
    ```vim
    :Files
    ```

### **4.4 Plugin `vim-surround`: Manipulação de Pares de Caracteres**
O **vim-surround** facilita a adição, modificação e remoção de pares de caracteres, como parênteses, colchetes e aspas.

- **Instalar vim-surround**:
  No **`.vimrc`**, adicione:
  ```vim
  Plug 'tpope/vim-surround'
  ```

- **Exemplo de Uso**:
  - Para adicionar parênteses em volta de uma palavra: `ysiw(`.
  - Para remover parênteses de uma palavra: `ds(`.

### **4.5 Plugin `vim-fugitive`: Integração com Git**
O **vim-fugitive** transforma o Vim em uma ferramenta poderosa para interagir com repositórios Git diretamente do editor.

- **Instalar vim-fugitive**:
  No **`.vimrc`**, adicione:
  ```vim
  Plug 'tpope/vim-fugitive'
  ```

- **Comandos úteis**:
  - **:Gstatus**: Exibe o status do Git.
  - **:Gcommit**: Faz um commit.
  - **:Gpush**: Envia as mudanças para o repositório remoto.

---

### **Conclusão da Parte 4**
Os plugins como **NERDTree**, **fzf.vim**, **vim-surround** e **vim-fugitive** trazem funcionalidades que facilitam o fluxo de trabalho, economizando tempo e esforço, especialmente em projetos que envolvem muitos arquivos e uso constante de Git.

---


## **Parte 5: Automatização de Tarefas no Vim**

Automatizar tarefas repetitivas no Vim economiza tempo e reduz erros. Vamos explorar o uso de **macros** e **comandos personalizados** para automatizar fluxos de trabalho e tarefas frequentes.

### **5.1 Uso de Macros no Vim**

Os **macros** permitem gravar uma sequência de ações e reproduzi-las depois, ideal para automatizar tarefas repetitivas, como edição em massa de arquivos ou formatação de texto.

#### **Gravar um Macro**
- No modo normal, pressione **q** seguido de uma letra para gravar o macro. A letra escolhida (ex: `a`) será o nome do macro.
  - Exemplo: Pressione `qa` para iniciar a gravação do macro "a".
  
- Execute as ações que deseja automatizar (por exemplo, deletar e formatar texto).

- Quando terminar, pressione **q** para parar a gravação.

#### **Executar o Macro**
- Para executar o macro gravado:
  - Digite `@a` para rodar o macro "a".
  - Para repetir várias vezes, use `10@a` (executa 10 vezes).

#### **Exemplo Prático**: Ajustar Indentação
Grave um macro para ajustar a indentação de múltiplas linhas:
1. Pressione `qa` para iniciar o macro.
2. Navegue até a linha que deseja ajustar e pressione `>>` para indentar.
3. Pressione `j` para mover para a próxima linha.
4. Pressione `q` para encerrar a gravação.
5. Execute o macro em várias linhas com `@a` ou `10@a` para indentar 10 linhas de uma vez.

### **5.2 Definir Comandos Personalizados no Vim**

Além de macros, você pode criar **comandos personalizados** no Vim para simplificar tarefas que envolvem múltiplos passos.

#### **Exemplo 1: Criar um Comando para Salvar e Fechar**
- Adicione o seguinte ao seu arquivo **`~/.vimrc`** para criar um comando personalizado que salva e fecha o arquivo:
  ```vim
  command! WQ wq
  ```
  Agora, no modo normal, digitar `:WQ` terá o mesmo efeito que `:wq`.

#### **Exemplo 2: Recarregar o Arquivo `vimrc`**
Se você edita o arquivo de configuração do Vim com frequência, pode criar um comando para recarregar as configurações sem fechar o editor:
```vim
command! ReloadVimrc source ~/.vimrc
```
Agora, ao digitar `:ReloadVimrc`, o arquivo `.vimrc` será recarregado.

---

## **Parte 6: Integração com Ambientes de Desenvolvimento**

Uma das grandes vantagens do Vim é sua flexibilidade para se adaptar a ambientes de desenvolvimento, com suporte a autocompletar, sintaxe destacada e LSP (Language Server Protocol) para navegação e análise de código.

### **6.1 Autocompletar com YouCompleteMe ou coc.nvim**

O autocompletar é essencial em ambientes de desenvolvimento, e o Vim pode ser facilmente integrado a poderosos motores de autocompletar, como **YouCompleteMe** ou **coc.nvim**.

#### **6.1.1 YouCompleteMe**
**YouCompleteMe (YCM)** é um plugin poderoso para autocompletar que suporta várias linguagens de programação.

- **Instalação do YouCompleteMe**:
  O **YouCompleteMe** requer algumas dependências para funcionar corretamente. No Ubuntu, execute os seguintes comandos:
  ```bash
  sudo apt install build-essential cmake python3-dev
  ```

  Instale o plugin no Vim adicionando a seguinte linha ao seu **`~/.vimrc`** com o **vim-plug**:
  ```vim
  Plug 'ycm-core/YouCompleteMe'
  ```

  Após instalar o plugin, compile-o:
  ```bash
  cd ~/.vim/plugged/YouCompleteMe
  python3 install.py --all
  ```

- **Autocompletar em C/C++, Python e outras linguagens**:
  O **YouCompleteMe** detecta automaticamente o código que você está editando e fornece sugestões de autocompletar à medida que você digita.

#### **6.1.2 coc.nvim (Alternativa com Suporte LSP)**
O **coc.nvim** é outra alternativa muito popular que traz suporte nativo para **LSP** (Language Server Protocol), integrando-se facilmente com ferramentas de desenvolvimento modernas.

- **Instalação do coc.nvim**:
  Adicione ao seu **`~/.vimrc`**:
  ```vim
  Plug 'neoclide/coc.nvim', {'branch': 'release'}
  ```

  Instale o **coc.nvim** com:
  ```vim
  :PlugInstall
  ```

- **Configurar LSP para diversas linguagens**:
  Após a instalação, você pode instalar **LSP servers** diretamente no Vim com o comando:
  ```vim
  :CocInstall coc-python coc-tsserver coc-json
  ```

- **Autocompletar e Diagnóstico**:
  O **coc.nvim** oferece autocompletar e também destaca erros de sintaxe diretamente no código. Para navegar entre os erros, use:
  ```vim
  :CocNext
  :CocPrev
  ```

### **6.2 Sintaxe Destacada**

O Vim suporta **realce de sintaxe** para diversas linguagens de programação. Para ativar o destaque de sintaxe para arquivos específicos, adicione ao seu **`~/.vimrc`**:
```vim
syntax enable
filetype plugin indent on
```

Se você quiser habilitar realce de sintaxe para tipos de arquivos específicos, como **YAML** ou **Bash**, pode instalar plugins individuais:
```vim
Plug 'stephpy/vim-yaml'
Plug 'bash-support/vim-bash'
```

### **6.3 Usando LSP para Navegação e Análise de Código**

O **LSP** (Language Server Protocol) é uma interface que facilita a integração com linguagens de programação, fornecendo navegação de código, sugestões de autocompletar, e análise de erros diretamente no Vim.

#### **Instalando e Configurando LSP com coc.nvim**
O **coc.nvim** facilita muito a configuração de **LSP servers** para várias linguagens.

- **Passo 1**: Adicione o **coc.nvim** ao seu **`~/.vimrc`** (como visto na seção anterior).
- **Passo 2**: Instale servidores LSP para as linguagens desejadas (exemplo para Python):
  ```vim
  :CocInstall coc-python
  ```

#### **Navegação por Funções e Erros**
Com o LSP configurado, você pode navegar rapidamente pelo código:
- **Ir para a definição de uma função**:
  ```vim
  gd
  ```
- **Mostrar as referências de uma variável ou função**:
  ```vim
  gr
  ```

---

## **Conclusão da Parte 6**
Com a integração de ferramentas como **YouCompleteMe**, **coc.nvim** e **LSP**, o Vim se transforma em um ambiente de desenvolvimento completo. Essa configuração oferece autocompletar avançado, realce de sintaxe e navegação de código, melhorando muito a produtividade no desenvolvimento.


---

## **Parte 7: Gerenciamento de Janelas e Splits no Vim**

O Vim permite trabalhar de forma eficiente com múltiplos arquivos e seções de código ao dividir a tela em janelas (splits) e alternar entre buffers. Isso é especialmente útil para administradores de sistemas que precisam editar vários arquivos de configuração ao mesmo tempo.

### **7.1 Divisão de Janelas (Splits)**

O **Vim** permite que você divida a tela em múltiplas janelas, seja na vertical ou horizontal, facilitando o trabalho com vários arquivos simultaneamente.

#### **7.1.1 Dividir a Janela Horizontalmente**
- Para dividir a janela **horizontalmente**, use:
  ```vim
  :split
  ```
  Ou, no modo normal:
  ```vim
  Ctrl + W, S
  ```

- Para abrir um novo arquivo na janela dividida, use:
  ```vim
  :split arquivo.txt
  ```

#### **7.1.2 Dividir a Janela Verticalmente**
- Para dividir a janela **verticalmente**, use:
  ```vim
  :vsplit
  ```
  Ou no modo normal:
  ```vim
  Ctrl + W, V
  ```

- Para abrir um novo arquivo na divisão vertical:
  ```vim
  :vsplit outro_arquivo.txt
  ```

#### **7.1.3 Navegar Entre Janelas**
Depois de dividir as janelas, você pode navegar entre elas facilmente usando os atalhos:
- **Ctrl + W + seta direcional**: Move para a janela na direção da seta (esquerda, direita, cima, baixo).
- **Ctrl + W + W**: Alterna para a próxima janela.

#### **7.1.4 Redimensionar Janelas**
Você pode redimensionar as janelas de acordo com sua necessidade:
- **Ctrl + W + =**: Ajusta todas as janelas para o mesmo tamanho.
- **Ctrl + W + +**: Aumenta a altura da janela atual.
- **Ctrl + W + -**: Diminui a altura da janela atual.
- **Ctrl + W + >** ou **<**: Aumenta ou diminui a largura da janela.

### **7.2 Gerenciamento de Buffers**
O **buffer** no Vim é uma área de memória que contém o conteúdo de um arquivo. Quando você abre vários arquivos, cada um é carregado em um buffer.

#### **7.2.1 Alternar Entre Buffers**
- Para listar os buffers abertos:
  ```vim
  :buffers
  ```
  Ou:
  ```vim
  :ls
  ```

- Para alternar entre buffers, use:
  ```vim
  :bN  (N é o número do buffer)
  ```

- Alternar para o buffer anterior:
  ```vim
  :b#
  ```

- Fechar o buffer atual:
  ```vim
  :bd
  ```

#### **7.2.2 Navegação Rápida Entre Arquivos**
Você também pode usar atalhos rápidos para navegar entre os arquivos mais recentes:
- **Ctrl + 6**: Alterna entre o arquivo atual e o último arquivo acessado.

### **7.3 Abrir Múltiplos Arquivos**
Se você precisa abrir vários arquivos de uma vez, pode usar o comando `vim` diretamente no terminal para iniciar o Vim com todos os arquivos:
```bash
vim arquivo1.txt arquivo2.txt
```
No Vim, os arquivos serão carregados em buffers diferentes e você pode alternar entre eles com os comandos de buffer.

---

## **Parte 8: Boas Práticas de Edição**

Existem várias boas práticas de edição no Vim que podem tornar seu trabalho mais eficiente, especialmente quando se trata de realizar substituições em massa, repetição de comandos e gerenciamento de histórico.

### **8.1 Busca e Substituição Avançada**

O comando de **substituição** no Vim é poderoso e pode ser usado para modificar texto em todo o arquivo ou em uma seleção específica.

#### **8.1.1 Substituição Simples**
Para substituir uma palavra em todo o arquivo:
```vim
:%s/palavra_antiga/palavra_nova/g
```

#### **8.1.2 Substituição com Confirmação**
Se você quiser confirmar cada substituição antes de realizar a mudança, use a flag `c`:
```vim
:%s/palavra_antiga/palavra_nova/gc
```
- O Vim solicitará a confirmação (`y` para sim, `n` para não).

#### **8.1.3 Substituição em Linhas Específicas**
Para realizar uma substituição apenas em linhas específicas (ex: linhas 5 a 10):
```vim
:5,10s/palavra_antiga/palavra_nova/g
```

### **8.2 Comandos de Repetição**
Um dos maiores pontos fortes do Vim é a habilidade de repetir comandos rapidamente, o que economiza tempo.

- **Repetir a Última Ação**: Depois de executar qualquer comando de edição (como `dd` para deletar uma linha), você pode repetir o comando simplesmente pressionando:
  ```vim
  .
  ```
  
  Isso repete a última ação na nova posição do cursor.

- **Repetir Macros**: Como mencionado anteriormente, os macros também podem ser repetidos várias vezes com `@a` (ou a tecla correspondente).

### **8.3 Histórico de Desfazer**
O Vim mantém um histórico detalhado de alterações feitas no arquivo, permitindo que você navegue por diferentes estados de edição.

#### **8.3.1 Desfazer (Undo)**
Para desfazer a última alteração:
```vim
u
```

#### **8.3.2 Refazer (Redo)**
Se você desfizer algo acidentalmente, pode refazer com:
```vim
Ctrl + R
```

#### **8.3.3 Navegar Entre Estados Anteriores**
Você pode usar o comando `:earlier` para voltar no tempo, em segundos ou ações de edição:
- Voltar 10 segundos:
  ```vim
  :earlier 10s
  ```

- Voltar 5 ações de edição:
  ```vim
  :earlier 5
  ```

Para avançar no tempo (caso tenha voltado demais), use `:later` da mesma forma.

### **8.4 Movimentação Avançada**
Aqui estão alguns comandos avançados de movimentação que podem acelerar o trabalho em arquivos grandes:

- **Saltos entre parágrafos**: Use `{` e `}` para mover-se rapidamente entre blocos de texto (parágrafos).
- **Mover-se entre seções de código**: Se você usar marcações de comentário para definir seções (`//`, `#`, etc.), use a busca com `/{padrão}` para localizar essas seções rapidamente.

---

## **Conclusão da Parte 8**

Com a divisão de janelas, buffers e boas práticas de edição, o Vim se torna um editor extremamente poderoso e flexível, capaz de lidar com múltiplos arquivos e facilitar a navegação em grandes scripts ou código-fonte. A substituição avançada e a repetição de comandos são essenciais para automatizar a edição em massa.

---

## **Parte 9: Personalização de Snippets no Vim**

Snippets são pequenas sequências de código ou texto que você pode inserir rapidamente em seu arquivo. Isso economiza tempo, especialmente quando você lida com padrões repetitivos, como blocos de código, funções ou configurações de sistema. Vamos ver como configurá-los e utilizá-los no Vim.

### **9.1 Usando o Plugin `UltiSnips` para Gerenciamento de Snippets**

O **UltiSnips** é um dos plugins mais populares para gerenciamento de snippets no Vim. Ele permite definir, organizar e expandir snippets de forma simples.

#### **Instalação do UltiSnips**
- Primeiro, adicione o plugin **UltiSnips** ao seu **`.vimrc`**:
  ```vim
  Plug 'SirVer/ultisnips'
  Plug 'honza/vim-snippets'  " Coleção de snippets prontos para várias linguagens
  ```

- Após adicionar essas linhas, instale os plugins executando o comando:
  ```vim
  :PlugInstall
  ```

#### **Configuração do UltiSnips no `.vimrc`**
Configure o **UltiSnips** para que funcione corretamente:
```vim
let g:UltiSnipsExpandTrigger="<tab>"       " Usa a tecla Tab para expandir os snippets
let g:UltiSnipsJumpForwardTrigger="<c-j>"  " Usa Ctrl + J para pular para a próxima posição
let g:UltiSnipsJumpBackwardTrigger="<c-k>" " Usa Ctrl + K para voltar à posição anterior
```

### **9.2 Criando Snippets Personalizados**

Você pode criar seus próprios snippets personalizados para uso frequente. Esses snippets podem ser específicos para certas linguagens de programação ou arquivos de configuração.

#### **Criando um Snippet para Bash**
Aqui está um exemplo de como criar um snippet para scripts Bash que define a estrutura básica de um `if`-`else`:

- Primeiro, crie o arquivo de snippets para Bash:
  ```bash
  mkdir -p ~/.vim/UltiSnips
  vim ~/.vim/UltiSnips/sh.snippets
  ```

- Adicione o seguinte snippet ao arquivo **sh.snippets**:
  ```snippets
  snippet if
  if [[ ${1} ]]; then
      echo "${1}"
  else
      echo "Nenhum argumento passado"
  fi
  endsnippet
  ```

Agora, ao digitar `if` em um arquivo **.sh** e pressionar `Tab`, o bloco será expandido automaticamente, e você poderá navegar pelos campos preenchíveis usando `Ctrl + J` e `Ctrl + K`.

#### **Criando Snippets para Blocos de Configuração**
Para administradores de sistemas, um exemplo de snippet útil pode ser para configurar permissões em arquivos ou diretórios com `chmod` e `chown`:

- Crie um snippet para permissões em arquivos de configuração:
  ```snippets
  snippet chmod
  chmod ${1:755} ${2:/caminho/para/arquivo}
  endsnippet

  snippet chown
  chown ${1:usuario}:${2:grupo} ${3:/caminho/para/arquivo}
  endsnippet
  ```

Esse snippet insere automaticamente os comandos `chmod` e `chown` com placeholders (variáveis) para as permissões e caminhos, o que facilita a edição de permissões no sistema.

### **9.3 Uso de Snippets com o Plugin `vim-snippets`**

O **vim-snippets** é uma coleção de snippets pré-definidos para diversas linguagens de programação e configurações. Esses snippets estão prontos para uso e podem ser expandidos com o **UltiSnips**.

#### **Instalando e Usando Snippets Prontos**
Após instalar o **vim-snippets**, ele já vem com uma ampla gama de snippets para Bash, Python, HTML, YAML, e muitas outras linguagens:

- Para usar um snippet em um arquivo Bash, por exemplo, basta começar a digitar o nome do snippet (como `for`) e pressionar **Tab** para expandi-lo.

- **Exemplo**: No Bash, ao digitar `for` e pressionar **Tab**, um loop `for` será automaticamente inserido:
  ```bash
  for i in {1..10}; do
      echo $i
  done
  ```

#### **Customizando Snippets Padrão**
Se você quiser modificar um snippet pré-definido, pode editar diretamente o arquivo correspondente em **`~/.vim/plugged/vim-snippets/snippets/`** ou criar um novo arquivo na sua pasta de snippets pessoais para sobrescrever os padrões.

### **9.4 Definindo Snippets para Outras Linguagens**

Aqui estão alguns exemplos de snippets úteis para diferentes linguagens e casos de uso:

#### **Snippet para Configurações YAML**
Se você trabalha frequentemente com arquivos de configuração YAML (por exemplo, em Kubernetes ou Ansible), pode criar snippets para facilitar a criação de blocos de configuração.

- No arquivo **yaml.snippets**:
  ```snippets
  snippet deploy
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ${1:app-name}
    labels:
      app: ${1:app-name}
  spec:
    replicas: ${2:1}
    selector:
      matchLabels:
        app: ${1:app-name}
    template:
      metadata:
        labels:
          app: ${1:app-name}
      spec:
        containers:
        - name: ${1:app-name}
          image: ${3:container-image}
          ports:
          - containerPort: ${4:80}
  endsnippet
  ```

#### **Snippet para Funções Python**
Se você desenvolve em Python, pode criar um snippet para a criação rápida de funções:
- No arquivo **python.snippets**:
  ```snippets
  snippet def
  def ${1:function_name}(${2:args}):
      """${3:docstring}"""
      ${0:pass}
  endsnippet
  ```

Ao digitar `def` e pressionar **Tab**, o snippet cria uma função com parâmetros e docstring prontos para serem preenchidos.

---

## **Conclusão da Parte 9**

Com o **UltiSnips** e **vim-snippets**, o Vim se torna uma ferramenta muito poderosa para automatizar a criação de trechos de código reutilizáveis. A criação de snippets personalizados para tarefas repetitivas pode acelerar significativamente seu fluxo de trabalho, especialmente em ambientes de administração de sistemas.

### **Resumo das Configurações e Ferramentas Utilizadas**:
- **UltiSnips**: Para gerenciamento de snippets customizados.
- **vim-snippets**: Coleção de snippets prontos para várias linguagens.
- **Atalhos de navegação**: `Ctrl + J` e `Ctrl + K` para pular entre variáveis preenchíveis nos snippets.

---

### **Conclusão Geral do Procedimento**

Após seguir este tutorial, seu **Vim** estará configurado para maximizar sua produtividade como administrador de sistemas. Com as **configurações iniciais**, atalhos eficientes, personalização avançada, uso de **plugins essenciais**, automatização de tarefas e integração com ambientes de desenvolvimento, você pode transformar o **Vim** em uma ferramenta completa para suas necessidades diárias.

Aqui está um resumo rápido do que cobrimos:
1. **Configurações básicas no `.vimrc`** para melhorar a usabilidade.
2. **Atalhos e navegação eficiente** para mover-se rapidamente entre arquivos e dentro do código.
3. **Personalização com temas, fontes e status bar (Vim-Airline)**.
4. **Plugins essenciais** como **NERDTree**, **fzf.vim**, **vim-surround**, e **vim-fugitive**.
5. **Automatização de tarefas** com macros e comandos personalizados.
6. **Integração com ambientes de desenvolvimento** via **YouCompleteMe**, **coc.nvim** e **LSP**.
7. **Gerenciamento de janelas e buffers** para trabalhar com múltiplos arquivos.
8. **Boas práticas de edição**, como busca e substituição avançada e repetição de comandos.
9. **Personalização de snippets** para automatizar trechos de código repetidos.

Com essas dicas e ajustes, você terá o Vim completamente otimizado para suas atividades no dia a dia.

