---
title: "Como usar chave SSH no GitLab"
description: "Um guia passo a passo para configurar e usar uma chave SSH para autenticação no GitLab."
date: "2024-10-16 09:01:01 -0300"
categories: 
  - "GitLab"
  - "SSH"
  - "Controle de Versão"
tags: 
  - "SSH"
  - "GitLab"
  - "Autenticação"
  - "Chaves SSH"
---

## Tutorial: Como usar chave SSH no GitLab

Este tutorial irá guiá-lo na configuração de uma chave SSH para autenticar no GitLab, facilitando a interação com repositórios sem a necessidade de digitar sua senha todas as vezes.

### Passo 1: Criar uma chave SSH

Primeiro, crie uma chave SSH na sua máquina para associá-la à sua conta no GitLab. Use o seguinte comando:

```bash
mkdir ~/gitlab
ssh-keygen -t ed25519 -C "seu-email@example.com" -f ~/gitlab/gitlab
```

- **Explicação**:
  - `-t ed25519`: Especifica o tipo de chave SSH (Ed25519, que é moderna e segura).
  - `-C "seu-email@example.com"`: Um comentário, geralmente seu e-mail, para identificar a chave.
  - `-f ~/gitlab/gitlab`: Define o local onde a chave SSH será salva (no caso, dentro da pasta `~/gitlab`).

### Passo 2: Adicionar a chave pública ao GitLab

Agora, você precisa adicionar a chave pública gerada à sua conta do GitLab. Para isso, exiba o conteúdo da chave pública com o comando:

```bash
cat ~/gitlab/gitlab.pub
```

Copie o conteúdo exibido.

- Acesse o GitLab e adicione sua chave SSH no seguinte link:  
  [Configurações de Chaves SSH no GitLab](https://gitlab.com/-/user_settings/ssh_keys)
  
- Cole a chave no campo apropriado e salve as configurações.

### Passo 3: Configurar o SSH para apontar para sua chave privada

Para que o GitLab utilize a chave correta, configure o SSH editando o arquivo `~/.ssh/config` com o seguinte conteúdo:

```bash
vim ~/.ssh/config
```

Adicione as linhas abaixo ao arquivo:

```bash
Host gitlab.com
    Hostname gitlab.com
    IdentityFile ~/gitlab/gitlab
    IdentitiesOnly yes
```

- **Explicação**:
  - `IdentityFile ~/gitlab/gitlab`: Informa ao SSH para usar sua chave privada localizada em `~/gitlab/gitlab`.
  - `IdentitiesOnly yes`: Garante que apenas a chave especificada seja usada.

### Passo 4: Testar a conexão SSH com o GitLab

Para confirmar que tudo está configurado corretamente, teste a conexão SSH com o GitLab:

```bash
ssh -T git@gitlab.com
```

Se tudo estiver funcionando, você verá uma mensagem de sucesso indicando que a autenticação SSH foi bem-sucedida.

---

