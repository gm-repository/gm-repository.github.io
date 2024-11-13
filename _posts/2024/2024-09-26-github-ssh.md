---
title: "Configurando e Usando Chave SSH no GitHub (Linux)"
date: 2024-09-30 17:00:00 -0300
categories: [Git, Github]
tags: [git, github, ssh]
status: "final"
abstract: "Este guia detalha o processo de configuração e uso de chaves SSH no GitHub, especificamente para usuários do sistema operacional Linux. Ele aborda desde a instalação do SSH até a configuração e teste da chave SSH, proporcionando uma abordagem segura para gerenciar repositórios no GitHub."
---

# Sumário
1. [Introdução ao SSH](#introdução-ao-ssh)
2. [Geração de Chaves SSH](#geração-de-chaves-ssh)
3. [Usando o ssh-agent](#usando-o-ssh-agent)
4. [Copiando a Chave SSH para o GitHub](#copiando-a-chave-ssh-para-o-github)
5. [Configuração no GitHub](#configuração-no-github)
6. [Configurando Git para Usar SSH](#configurando-git-para-usar-ssh)
7. [Testando a Conexão SSH](#testando-a-conexão-ssh)
8. [Clonando Repositórios com SSH](#clonando-repositórios-com-ssh)
9. [Resolução de Problemas](#resolução-de-problemas)

## Introdução ao SSH
SSH (Secure Shell) é um protocolo utilizado para acessar servidores remotos de maneira segura. O comando `ssh` permite executar comandos em máquinas remotas e mover arquivos de/para essas máquinas.

### Verifique se SSH está instalado
Verifique a instalação do SSH com:

```bash
ssh -V
```
Se necessário, instale o SSH com:

```bash
sudo apt update
sudo apt install openssh-client
```

## Geração de Chaves SSH
As chaves SSH são usadas para autenticar em sistemas que usam SSH. A chave privada é mantida em segredo no seu computador, e a chave pública pode ser compartilhada.

### Gerar uma nova chave SSH
Use o comando abaixo para gerar uma nova chave usando a criptografia Ed25519:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/github
```
Troque `"your_email@example.com"` pelo seu email.

## Usando o ssh-agent
O `ssh-agent` é um programa que mantém suas chaves privadas carregadas e prontas para autenticação.

### Iniciar o agente SSH
Para iniciar o `ssh-agent` em segundo plano:

```bash
eval "$(ssh-agent -s)"
```

### Adicionar sua chave SSH ao ssh-agent
Adicione sua chave SSH privada ao `ssh-agent`:

```bash
ssh-add ~/.ssh/github
```

## Copiando a Chave SSH para o GitHub
A chave pública deve ser adicionada ao GitHub para permitir a autenticação sem a necessidade de senha.

### Copiar a chave SSH para sua área de transferência
Corrija o comando para copiar a chave pública:

```bash
cat ~/.ssh/github.pub | xclip -selection clipboard
```

### Instalar xclip
Se necessário, instale o `xclip`:

```bash
sudo apt install xclip
```

## Configuração no GitHub
Adicionar a chave SSH à sua conta no GitHub permite clonar, puxar e empurrar repositórios sem usar senha.

### Adicionar a chave SSH à sua conta GitHub
- Acesse as configurações do GitHub.
- Adicione sua chave SSH em **SSH and GPG keys**.

## Configurando Git para Usar SSH
Configure o Git para usar SSH, facilitando o gerenciamento de várias chaves para diferentes repositórios.

### Configurar o Git para usar um comando SSH específico
```bash
git config --global core.sshCommand "ssh -i ~/.ssh/github -F /dev/null"
```

## Testando a Conexão SSH
Verifique se a configuração está correta, testando a conexão com o GitHub.

```bash
ssh -T git@github.com
```

## Clonando Repositórios com SSH
Use SSH para clonar repositórios de forma segura e prática.

```bash
git clone git@github.com:username/repository.git
```

## Resolução de Problemas
Explore soluções comuns para problemas de configuração SSH no GitHub, incluindo erros de conexão e problemas com chaves.

### Recursos Adicionais e Resolução de Problemas
Para detalhes adicionais e resolução de problemas, acesse a [documentação oficial do GitHub sobre SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh).
