---
layout: post
title: "Manual de Sobrevivência do Emacs"
date: 2024-11-15 17:19:20 -0300
categories: [Emacs, Produtividade]
tags: [Emacs]
description: "Manual de Sobrevivência do Emacs."
---

### **Manual de Sobrevivência do Emacs**

O **Emacs** é uma poderosa ferramenta para programadores, escritores e administradores de sistemas. No entanto, sua interface e funcionalidades podem ser intimidadoras para iniciantes. Este manual de sobrevivência é um guia prático e rápido para quem deseja começar a usar o Emacs e dominar seus fundamentos.

---

### **1. Primeiros Passos no Emacs**

#### **1.1 Instalando o Emacs**
- **Linux**:
  ```bash
  sudo apt install emacs       # Para Debian/Ubuntu
  sudo dnf install emacs       # Para Fedora/RHEL
  ```
- **macOS**:
  - Via **Homebrew**:
    ```bash
    brew install emacs
    ```
- **Windows**:
  - Baixe em: [GNU Emacs Downloads](https://www.gnu.org/software/emacs/download.html).

#### **1.2 Interface Básica**
- **Janela principal**: Contém os arquivos ou buffers.
- **Mini-buffer**: Na parte inferior, usado para comandos e mensagens.
- **Modo de Buffer**: Indica o tipo de arquivo (e.g., `Python`, `Org`).

---

### **2. Comandos Essenciais**

O Emacs usa combinações de teclas baseadas no **Control (C)** e **Meta (M)**. No Windows/Linux, `Meta` geralmente é a tecla `Alt`. No macOS, pode ser `Option` ou `Command`.

| **Comando**       | **Atalho**               | **Descrição**                              |
|-------------------|--------------------------|--------------------------------------------|
| Abrir arquivo     | `C-x C-f`               | Abre ou cria um arquivo.                   |
| Salvar arquivo    | `C-x C-s`               | Salva o arquivo atual.                     |
| Fechar arquivo    | `C-x k`                 | Fecha o buffer atual.                      |
| Sair do Emacs     | `C-x C-c`               | Fecha o Emacs.                             |
| Cancelar comando  | `C-g`                   | Cancela o comando em andamento.            |
| Desfazer          | `C-/` ou `C-x u`        | Desfaz a última ação.                      |
| Copiar texto      | `M-w`                   | Copia o texto selecionado.                 |
| Cortar texto      | `C-w`                   | Recorta o texto selecionado.               |
| Colar texto       | `C-y`                   | Cola o último texto copiado/recortado.     |
| Buscar texto      | `C-s`                   | Pesquisa incremental no buffer atual.      |
| Substituir texto  | `M-%`                   | Substitui texto no buffer atual.           |

---

### **3. Navegação no Emacs**

#### **3.1 Movendo o Cursor**
| **Comando**       | **Atalho**       | **Descrição**                              |
|-------------------|------------------|--------------------------------------------|
| Próxima linha     | `C-n`            | Move para a próxima linha.                 |
| Linha anterior    | `C-p`            | Move para a linha anterior.                |
| Próxima palavra   | `M-f`            | Avança para a próxima palavra.             |
| Palavra anterior  | `M-b`            | Retrocede para a palavra anterior.         |
| Início da linha   | `C-a`            | Move para o início da linha.               |
| Fim da linha      | `C-e`            | Move para o fim da linha.                  |
| Início do buffer  | `M-<`            | Move para o início do buffer.              |
| Fim do buffer     | `M->`            | Move para o fim do buffer.                 |

#### **3.2 Dividindo a Tela**
| **Comando**           | **Atalho**       | **Descrição**                              |
|-----------------------|------------------|--------------------------------------------|
| Dividir horizontal    | `C-x 2`         | Divide a janela horizontalmente.           |
| Dividir vertical      | `C-x 3`         | Divide a janela verticalmente.             |
| Fechar divisão        | `C-x 1`         | Fecha todas as divisões exceto a atual.    |
| Alternar janelas      | `C-x o`         | Move o cursor para outra janela.           |

---

### **4. Editando e Gerenciando Buffers**

#### **4.1 Buffers**
- O Emacs gerencia todos os arquivos e janelas em **buffers**. Pense neles como abas invisíveis.
- Para listar os buffers abertos:
  ```bash
  C-x C-b
  ```

#### **4.2 Trocar de Buffer**
- Use `C-x b` para alternar entre buffers.

---

### **5. Trabalhando com Arquivos e Diretórios**

#### **5.1 Abrindo Diretórios**
Use o comando `dired` para navegar por diretórios no Emacs:
```bash
C-x d
```

- **Comandos no Dired**:
  | **Comando**       | **Descrição**                          |
  |-------------------|----------------------------------------|
  | `RET`             | Abre o arquivo ou diretório.           |
  | `+`               | Cria um novo diretório.                |
  | `D`               | Exclui o arquivo ou diretório marcado. |
  | `R`               | Renomeia ou move o arquivo.            |
  | `C`               | Copia o arquivo para outro local.      |

---

### **6. Personalizando o Emacs**

#### **6.1 Editando o Arquivo de Configuração**
O Emacs permite personalizações no arquivo `~/.emacs` ou `~/.emacs.d/init.el`. Aqui estão alguns exemplos úteis:

##### **Definir Tema**
```elisp
(load-theme 'wombat t)  ; Tema escuro confortável
```

##### **Mostrar Números de Linhas**
```elisp
(global-display-line-numbers-mode t)
```

##### **Habilitar Auto-Save**
```elisp
(setq auto-save-default t)
```

---

### **7. Solução de Problemas e Ajuda**

#### **7.1 Acessando Ajuda**
| **Comando**       | **Atalho**         | **Descrição**                              |
|-------------------|--------------------|--------------------------------------------|
| Ajuda geral       | `C-h ?`           | Exibe o menu de ajuda.                     |
| Ajuda sobre tecla | `C-h k`           | Explica o que um atalho faz.               |
| Ajuda sobre comando | `C-h f`         | Detalha a função de um comando.            |

#### **7.2 Cancelando Travamentos**
Se o Emacs "congelar", pressione `C-g` para cancelar o comando em execução.

---

### **8. Extensões Úteis**

#### **8.1 Instalando Pacotes**
- Use o gerenciador de pacotes integrado:
  ```elisp
  M-x package-list-packages
  ```
- Pesquise e instale extensões como:
  - **Magit**: Interface para Git.
  - **Org-mode**: Gerenciamento de tarefas e notas.
  - **Company**: Autocompletar código.

---

### **9. Recursos Avançados**

#### **9.1 Macros**
Grave sequências repetitivas de comandos com:
1. Iniciar gravação: `C-x (`
2. Executar ações desejadas.
3. Finalizar gravação: `C-x )`
4. Reproduzir a macro: `C-x e`

#### **9.2 Trabalhando com Terminais**
Execute comandos de terminal dentro do Emacs:
```elisp
M-x shell
```

---

### **10. Atalho de Referência**

| **Tarefa**             | **Atalho**              |
|------------------------|-------------------------|
| Abrir arquivo          | `C-x C-f`              |
| Salvar arquivo         | `C-x C-s`              |
| Fechar buffer          | `C-x k`                |
| Desfazer               | `C-/` ou `C-x u`       |
| Dividir janela         | `C-x 2` ou `C-x 3`     |
| Trocar buffer          | `C-x b`                |
| Cancelar comando       | `C-g`                  |
| Sair do Emacs          | `C-x C-c`              |

---