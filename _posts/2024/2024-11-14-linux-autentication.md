---
title: "Autenticação em Linux: Guia Completo de Configuração e Automação"
layout: post
date: 2023-11-14 12:13:14 -0300
categories: [Linux, Autenticação, Segurança]
---

### Introdução

A autenticação é uma das principais linhas de defesa para proteger sistemas e redes em ambientes corporativos. Em sistemas Linux, a autenticação pode ser configurada de maneira flexível e abrangente, utilizando desde métodos locais até soluções de autenticação centralizadas, como LDAP, Kerberos e FreeIPA. Em ambientes onde a segurança é crítica, configurar a autenticação adequadamente é fundamental para garantir que apenas usuários autorizados possam acessar recursos sensíveis.

Este tutorial apresenta um guia completo para configurar e gerenciar a autenticação em servidores Linux. Desde a configuração de autenticação local e políticas de senha com o PAM (Pluggable Authentication Modules), até integrações avançadas com serviços de autenticação centralizada, você aprenderá a implementar autenticação robusta e escalável. Além disso, abordaremos como automatizar essas configurações com Ansible, permitindo que múltiplos servidores sejam gerenciados de maneira eficiente e consistente. A automação de tarefas como criação de contas, configuração de políticas de senha, integração com LDAP e Kerberos, implementação de autenticação multifatorial (MFA) e auditoria torna a administração de autenticação mais segura e menos propensa a erros manuais.

Ao final deste tutorial, você estará preparado para implementar uma solução de autenticação sólida e bem estruturada em ambientes de produção, protegendo melhor os recursos e usuários de sua organização contra acessos não autorizados e simplificando a manutenção da segurança de sistemas Linux.

---

## Tabela de Conteúdo

- [Introdução à Autenticação em Sistemas Linux](#1-introdução-à-autenticação-em-sistemas-linux)
- [Autenticação Local em Linux](#2-autenticação-local-em-linux)
- [Configuração de Políticas de Senha e Bloqueio de Conta](#3-configuração-de-políticas-de-senha-e-bloqueio-de-conta)
- [Introdução ao PAM (Pluggable Authentication Modules)](#4-introdução-ao-pam-pluggable-authentication-modules)
- [Configuração e Estrutura dos Arquivos PAM](#5-configuração-e-estrutura-dos-arquivos-pam)
- [Configuração de Autenticação Multifatorial com PAM](#6-configuração-de-autenticação-multifatorial-com-pam)
- [Introdução ao SSSD e Suas Funcionalidades](#7-introdução-ao-sssd-e-suas-funcionalidades)
- [Instalação e Configuração Básica do SSSD](#8-instalação-e-configuração-básica-do-sssd)
- [Integração do SSSD com LDAP para Autenticação Centralizada](#9-integração-do-sssd-com-ldap-para-autenticação-centralizada)
- [Integração do SSSD com Kerberos para Autenticação Segura](#10-integração-do-sssd-com-kerberos-para-autenticação-segura)
- [Configuração de Autenticação de Clientes com FreeIPA e SSSD](#11-configuração-de-autenticação-de-clientes-com-freeipa-e-sssd)
- [Integração do FreeIPA com Kerberos e Configuração de Tickets](#12-integração-do-freeipa-com-kerberos-e-configuração-de-tickets)
- [Integração com Active Directory Usando SSSD](#13-integração-com-active-directory-usando-sssd)
- [Políticas de Bloqueio de Conta e Auditoria com PAM](#14-políticas-de-bloqueio-de-conta-e-auditoria-com-pam)
- [Monitoramento e Troubleshooting de Autenticação](#15-monitoramento-e-troubleshooting-de-autenticação)
- [Práticas Recomendadas para Segurança de Autenticação](#16-práticas-recomendadas-para-segurança-de-autenticação)
- [Automação de Configuração de Autenticação com Ansible](#17-automação-de-configuração-de-autenticação-com-ansible)

---

## 1. Introdução à Autenticação em Sistemas Linux

### Objetivo
Explicar o conceito de autenticação e sua importância no contexto de segurança de sistemas Linux, apresentando uma visão geral dos métodos de autenticação disponíveis.

### Conteúdo

#### 1.1 O que é Autenticação?

A autenticação é o processo de validação da identidade de um usuário que tenta acessar um sistema ou recurso. Em sistemas Linux, a autenticação controla quem pode acessar o sistema, quais ações podem ser realizadas e permite monitorar atividades, essencial para manter a integridade e segurança dos dados.

#### 1.2 Importância da Autenticação Centralizada e Multifatorial em Ambientes Corporativos

Em ambientes corporativos, a autenticação centralizada permite a administração de usuários em múltiplos servidores de maneira central e consistente, melhorando a segurança e facilitando o gerenciamento de permissões. Já a autenticação multifatorial (MFA) adiciona uma camada extra de segurança, exigindo múltiplas verificações de identidade para autenticar um usuário.

**Principais Benefícios**:
- **Segurança**: Reduz o risco de acesso não autorizado.
- **Conformidade**: Facilita o cumprimento de requisitos regulatórios (como GDPR e HIPAA).
- **Eficiência**: Centraliza o gerenciamento de contas de usuários em ambientes com muitos sistemas e servidores.

#### 1.3 Diferentes Métodos de Autenticação

Existem diversos métodos de autenticação em sistemas Linux, cada um adequado para diferentes tipos de ambientes e necessidades. Abaixo estão os principais métodos abordados neste tutorial:

1. **Autenticação Local**:
   - Utiliza arquivos locais para armazenar informações de login, como `/etc/passwd` e `/etc/shadow`.
   - Adequada para sistemas isolados ou com poucos usuários.

2. **PAM (Pluggable Authentication Modules)**:
   - Sistema modular de autenticação que permite integrar diversos métodos de autenticação.
   - Facilita a adição de módulos como autenticação de dois fatores e bloqueio de contas.

3. **SSSD (System Security Services Daemon)**:
   - Ferramenta que possibilita a integração de autenticação com serviços de diretório (LDAP, Active Directory) e Kerberos.
   - Ideal para ambientes corporativos que necessitam de cache de credenciais e autenticação offline.

4. **LDAP (Lightweight Directory Access Protocol)**:
   - Protocolo para autenticação centralizada, utilizado em muitos servidores de diretórios como OpenLDAP.
   - Popular para gerenciamento centralizado de contas de usuários e grupos.

5. **Kerberos**:
   - Sistema de autenticação de rede que utiliza tickets para autenticar usuários de forma segura.
   - Comumente utilizado em integração com SSSD e ambientes que exigem autenticação robusta, como FreeIPA e Active Directory.

#### 1.4 Visão Geral do Tutorial

Este tutorial irá guiá-lo na configuração de autenticação em Linux, abordando desde a autenticação local até configurações avançadas de integração com serviços de diretório e autenticação multifatorial. Com exemplos práticos e configurações detalhadas, você aprenderá a implementar autenticação segura em um ambiente de produção.

**Próximas Etapas**:
- Configuração de **Autenticação Local**.
- Implementação e configuração de **PAM** para autenticação modular.
- Uso do **SSSD** para autenticação centralizada com **LDAP** e **Kerberos**.
- Integração com **Active Directory** e **FreeIPA** para ambientes corporativos.

---

## 2. Autenticação Local em Linux

### Objetivo
Explicar o funcionamento da autenticação local em sistemas Linux, detalhando os arquivos essenciais e comandos usados para gerenciar usuários e definir permissões de segurança.

### Conteúdo

#### 2.1 Arquivos Essenciais para Autenticação Local

A autenticação local em Linux é gerida por três arquivos principais localizados em `/etc`:

1. **/etc/passwd**:
   - Contém informações básicas dos usuários, como nome, UID (User ID), GID (Group ID), e diretório home.
   - A senha não é armazenada diretamente nesse arquivo por questões de segurança; em vez disso, ele aponta para `/etc/shadow`.

2. **/etc/shadow**:
   - Armazena as senhas dos usuários de forma criptografada.
   - Cada linha representa um usuário e inclui informações de senha, data de expiração e requisitos de complexidade.
   - As permissões são restritas apenas ao usuário root, protegendo contra leitura não autorizada.

3. **/etc/group**:
   - Define grupos no sistema e lista os usuários membros de cada grupo.
   - Permite associar permissões específicas a grupos de usuários, facilitando a administração de permissões.

#### 2.2 Controle de Permissões e Segurança de Senhas

O sistema Linux usa diferentes níveis de permissões para garantir a segurança dos dados e controlar o acesso aos arquivos:

1. **Permissões de Arquivo**:
   - Os arquivos `/etc/passwd`, `/etc/shadow`, e `/etc/group` possuem permissões específicas que garantem acesso limitado e seguro:
     ```plaintext
     -rw-r--r-- 1 root root /etc/passwd
     -rw------- 1 root root /etc/shadow
     -rw-r--r-- 1 root root /etc/group
     ```
   - O arquivo `/etc/shadow`, por exemplo, é acessível apenas pelo root, protegendo as senhas criptografadas contra leitura.

2. **Armazenamento Seguro de Senhas**:
   - As senhas são armazenadas usando técnicas de hashing (comumente SHA-512), garantindo que apenas o hash criptografado seja visível, dificultando a obtenção da senha original.

#### 2.3 Comandos para Gerenciamento de Usuários Locais

Para administrar usuários no Linux, os seguintes comandos são utilizados:

1. **useradd**:
   - Cria novos usuários e configura o ambiente inicial.
   - **Sintaxe**:
     ```bash
     sudo useradd -m -s /bin/bash <nome_do_usuário>
     ```
   - **Opções**:
     - `-m`: Cria o diretório home do usuário.
     - `-s`: Define o shell padrão do usuário.

2. **passwd**:
   - Define ou altera a senha de um usuário.
   - **Sintaxe**:
     ```bash
     sudo passwd <nome_do_usuário>
     ```

3. **usermod**:
   - Modifica atributos de um usuário existente, como grupo ou shell.
   - **Sintaxe**:
     ```bash
     sudo usermod -aG <nome_do_grupo> <nome_do_usuário>
     ```
   - **Exemplo**:
     ```bash
     sudo usermod -aG sudo alice
     ```
   - Esse comando adiciona o usuário `alice` ao grupo `sudo`, concedendo a ele permissões de administrador.

4. **userdel**:
   - Remove um usuário do sistema e, opcionalmente, o diretório home associado.
   - **Sintaxe**:
     ```bash
     sudo userdel -r <nome_do_usuário>
     ```
   - A opção `-r` remove o diretório home e outros arquivos associados ao usuário.

#### 2.4 Exemplo Prático de Gerenciamento de Usuários Locais

Aqui está um exemplo completo de como criar, modificar e remover um usuário local em um sistema Linux.

1. **Criar um novo usuário**:
   ```bash
   sudo useradd -m -s /bin/bash carlos
   ```

2. **Definir uma senha para o usuário**:
   ```bash
   sudo passwd carlos
   ```

3. **Adicionar o usuário a um grupo específico**:
   ```bash
   sudo usermod -aG sudo carlos
   ```

4. **Remover o usuário e o diretório home**:
   ```bash
   sudo userdel -r carlos
   ```

#### Tabela de Referência: Comandos de Gerenciamento de Usuários

| Comando            | Função                                   | Exemplo                                   |
|--------------------|------------------------------------------|-------------------------------------------|
| `useradd`          | Cria um novo usuário                     | `sudo useradd -m -s /bin/bash alice`      |
| `passwd`           | Define ou altera a senha do usuário      | `sudo passwd alice`                       |
| `usermod`          | Modifica atributos de um usuário         | `sudo usermod -aG sudo alice`             |
| `userdel`          | Remove o usuário e o diretório home      | `sudo userdel -r alice`                   |

---

## 3. Configuração de Políticas de Senha e Bloqueio de Conta

### Objetivo
Demonstrar como configurar políticas de senha e definir bloqueios de conta em Linux para reforçar a segurança de autenticação local, impondo requisitos de complexidade e políticas de expiração.

### Conteúdo

#### 3.1 Configuração de Políticas de Senha com `/etc/login.defs`

O arquivo `/etc/login.defs` permite definir políticas gerais de senha e parâmetros de segurança para usuários locais. Nele, é possível configurar complexidade de senha, expiração e validade.

1. **Principais Parâmetros do `/etc/login.defs`**:
   - **PASS_MAX_DAYS**: Número máximo de dias que uma senha é válida antes de exigir alteração.
   - **PASS_MIN_DAYS**: Número mínimo de dias antes que um usuário possa alterar sua senha.
   - **PASS_WARN_AGE**: Número de dias antes da expiração da senha em que o sistema começa a alertar o usuário.
   
   - **Exemplo de Configuração**:
     ```plaintext
     PASS_MAX_DAYS   90
     PASS_MIN_DAYS   1
     PASS_WARN_AGE   7
     ```

   Esses parâmetros configuram uma validade máxima de 90 dias para senhas, um intervalo mínimo de 1 dia entre alterações, e notificações de expiração 7 dias antes da data limite.

#### 3.2 Configuração de Políticas de Complexidade de Senha com PAM

O **Pluggable Authentication Modules (PAM)** permite configurar regras adicionais para complexidade e segurança de senhas. Para isso, utilize o módulo `pam_pwquality` para definir requisitos mínimos de complexidade.

1. **Instalação do `pam_pwquality`** (caso não esteja instalado):
   ```bash
   sudo apt install libpam-pwquality  # Em distribuições baseadas em Debian/Ubuntu
   ```

2. **Configuração do Módulo de Complexidade**:
   - Edite o arquivo `/etc/pam.d/common-password` para configurar o `pam_pwquality`.
   - Adicione ou edite a linha para incluir parâmetros como número mínimo de caracteres e variedade de caracteres:
     ```plaintext
     password requisite pam_pwquality.so retry=3 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
     ```

   - **Parâmetros**:
     - `minlen=8`: Define o tamanho mínimo da senha como 8 caracteres.
     - `ucredit=-1`: Exige pelo menos uma letra maiúscula.
     - `lcredit=-1`: Exige pelo menos uma letra minúscula.
     - `dcredit=-1`: Exige pelo menos um dígito.
     - `ocredit=-1`: Exige pelo menos um caractere especial.

3. **Exemplo Completo de Configuração**:
   Com essa configuração, o sistema exigirá senhas de pelo menos 8 caracteres, incluindo letras maiúsculas, minúsculas, números e caracteres especiais, garantindo uma senha complexa e segura.

#### 3.3 Configuração de Expiração de Senha e Bloqueio de Conta com `chage`

O comando `chage` permite definir políticas de expiração e bloqueio de conta diretamente para cada usuário, proporcionando um controle mais granular.

1. **Definir Expiração de Senha com `chage`**:
   - Para configurar a expiração de senha de um usuário específico:
     ```bash
     sudo chage -M 90 -m 1 -W 7 <nome_do_usuário>
     ```

   - **Parâmetros**:
     - `-M 90`: Define a validade da senha em 90 dias.
     - `-m 1`: Define o intervalo mínimo entre alterações de senha como 1 dia.
     - `-W 7`: Define o aviso de expiração 7 dias antes.

2. **Visualizar Configurações de Expiração**:
   - Para verificar as configurações de expiração de um usuário:
     ```bash
     sudo chage -l <nome_do_usuário>
     ```

#### 3.4 Exemplo Prático: Configuração de Políticas de Senha e Bloqueio de Conta

Aqui está um exemplo prático de como configurar políticas de senha para reforçar a segurança em um sistema Linux.

1. **Configuração no `/etc/login.defs`**:
   - Abra o arquivo `/etc/login.defs` e adicione:
     ```plaintext
     PASS_MAX_DAYS   90
     PASS_MIN_DAYS   1
     PASS_WARN_AGE   7
     ```

2. **Configuração de Complexidade de Senha com PAM**:
   - Edite o arquivo `/etc/pam.d/common-password` e inclua:
     ```plaintext
     password requisite pam_pwquality.so retry=3 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
     ```

3. **Configuração Individual para Expiração com `chage`**:
   - Aplique configurações de expiração para o usuário `alice`:
     ```bash
     sudo chage -M 90 -m 1 -W 7 alice
     ```

#### Tabela de Referência: Comandos e Parâmetros de Políticas de Senha

| Comando/Arquivo                   | Função                                                       | Exemplo                                                         |
|-----------------------------------|--------------------------------------------------------------|-----------------------------------------------------------------|
| **`/etc/login.defs`**             | Configura políticas globais de expiração de senha            | `PASS_MAX_DAYS 90`                                             |
| **`pam_pwquality`**               | Configura complexidade mínima para senhas                    | `minlen=8 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1`         |
| **`chage -M -m -W`**              | Configura expiração e aviso para senha de um usuário         | `chage -M 90 -m 1 -W 7 alice`                                  |
| **`chage -l`**                    | Verifica configuração de expiração de senha de um usuário    | `chage -l alice`                                               |

---

## 4. Introdução ao PAM (Pluggable Authentication Modules)

### Objetivo
Apresentar o **PAM (Pluggable Authentication Modules)** como um sistema modular de autenticação em Linux, explicando sua estrutura e papel na integração de diferentes métodos de autenticação de forma flexível e segura.

### Conteúdo

#### 4.1 O que é o PAM?

O **PAM** é uma estrutura modular que permite o uso de diversos métodos de autenticação em sistemas Linux de forma configurável. Ele gerencia como os usuários autenticam e acessam o sistema, além de permitir a implementação de políticas de segurança personalizadas.

Com o PAM, você pode adicionar módulos que definem regras específicas de autenticação, como requisitos de senha, bloqueios de conta e autenticação multifatorial, entre outros. Cada módulo atua como um bloco de construção, possibilitando que diferentes métodos de autenticação sejam aplicados de acordo com as necessidades.

#### 4.2 Arquitetura do PAM

A arquitetura do PAM é organizada em quatro tipos principais de módulos, cada um responsável por uma etapa do processo de autenticação:

1. **auth**:
   - Define como a identidade do usuário é verificada (por exemplo, senha ou chave SSH).
   - Exemplo: Módulo `pam_unix.so` para autenticação local com senha.

2. **account**:
   - Gerencia políticas de conta, como bloqueios e expirações.
   - Exemplo: Módulo `pam_tally2.so` para bloqueio após várias tentativas falhas.

3. **password**:
   - Define regras de criação e atualização de senha.
   - Exemplo: Módulo `pam_pwquality.so` para impor complexidade mínima da senha.

4. **session**:
   - Gerencia ações durante a sessão, como definição de variáveis de ambiente ou montagem de diretórios.
   - Exemplo: Módulo `pam_limits.so` para limitar o uso de recursos do sistema durante uma sessão.

#### 4.3 Arquivos de Configuração do PAM (`/etc/pam.d/`)

Os arquivos de configuração do PAM estão localizados em `/etc/pam.d/`, onde cada serviço (como `login`, `sshd`, `su`) possui um arquivo dedicado que define as regras de autenticação específicas para aquele serviço.

1. **Exemplo do Arquivo `/etc/pam.d/sshd`**:
   - Este arquivo controla as políticas de autenticação para o serviço SSH.
   - Exemplo de configuração básica:
     ```plaintext
     auth       required     pam_sepermit.so
     auth       include      password-auth
     account    required     pam_nologin.so
     account    include      password-auth
     session    required     pam_loginuid.so
     session    include      password-auth
     ```
   - Cada linha especifica um módulo PAM e as diretivas para o serviço `sshd`.

2. **Sintaxe dos Arquivos PAM**:
   - A estrutura de uma linha no arquivo PAM segue a sintaxe:
     ```plaintext
     <tipo> <controle> <módulo> <opções>
     ```
     - **Tipo**: `auth`, `account`, `password`, ou `session`.
     - **Controle**: Define o comportamento caso o módulo falhe (`required`, `requisite`, `sufficient`, ou `optional`).
     - **Módulo**: Nome do módulo PAM a ser usado.
     - **Opções**: Parâmetros específicos para o módulo.

#### 4.4 Tipos de Controles do PAM

Os controles determinam o comportamento do PAM ao verificar cada módulo e como ele deve proceder caso uma regra seja satisfeita ou falhe:

1. **required**:
   - O módulo deve ser satisfeito para que o acesso seja permitido, mas a falha não interrompe imediatamente o processo de autenticação.

2. **requisite**:
   - Se o módulo falhar, o processo de autenticação é interrompido imediatamente, sem verificar módulos subsequentes.

3. **sufficient**:
   - Se o módulo for satisfeito, os módulos subsequentes são ignorados, e o usuário é autenticado.

4. **optional**:
   - O módulo é verificado, mas o resultado não afeta o processo geral de autenticação (usado em módulos de configuração de sessão, por exemplo).

#### 4.5 Exemplo Prático: Configurando o PAM para Autenticação Local

Aqui está um exemplo prático de configuração básica do PAM para autenticação local segura, que utiliza o módulo `pam_unix.so` para verificação de senha e o `pam_tally2.so` para bloquear contas após tentativas de login falhas.

1. **Configuração do `/etc/pam.d/common-auth`**:
   - Edite o arquivo `/etc/pam.d/common-auth` para adicionar as seguintes regras:
     ```plaintext
     auth required pam_tally2.so deny=5 unlock_time=600
     auth required pam_unix.so
     ```
   - **Explicação**:
     - `pam_tally2.so deny=5 unlock_time=600`: Após 5 tentativas falhas, o usuário será bloqueado por 10 minutos.
     - `pam_unix.so`: Usa autenticação local de senha.

2. **Configuração do `/etc/pam.d/common-password`**:
   - Configure as regras de complexidade e criação de senha no arquivo `/etc/pam.d/common-password`:
     ```plaintext
     password requisite pam_pwquality.so retry=3 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
     ```
   - **Explicação**:
     - `pam_pwquality.so`: Impõe complexidade na criação de senhas, exigindo ao menos 8 caracteres, incluindo letras maiúsculas, minúsculas, números e símbolos.

#### Tabela de Referência: Módulos e Controles PAM

| Módulo              | Função                                           | Exemplo de Uso                                                 |
|---------------------|--------------------------------------------------|-----------------------------------------------------------------|
| **pam_unix.so**     | Autenticação local usando senha                  | `auth required pam_unix.so`                                     |
| **pam_tally2.so**   | Controle de tentativas de login                  | `auth required pam_tally2.so deny=5 unlock_time=600`            |
| **pam_pwquality.so**| Impõe complexidade mínima de senha               | `password requisite pam_pwquality.so minlen=8 ucredit=-1 ...`   |
| **pam_limits.so**   | Limita recursos do sistema durante a sessão      | `session required pam_limits.so`                                |

---

## 5. Configuração e Estrutura dos Arquivos PAM

### Objetivo
Explicar a estrutura e configuração dos arquivos PAM em `/etc/pam.d/`, abordando como ajustar os módulos de autenticação para maior segurança e controle, com exemplos práticos.

### Conteúdo

#### 5.1 Estrutura dos Arquivos PAM

Os arquivos de configuração do PAM estão organizados no diretório `/etc/pam.d/`. Cada serviço que requer autenticação, como `login`, `sshd` e `su`, possui um arquivo correspondente que define as regras de autenticação para aquele serviço específico. Os arquivos comuns, como `common-auth` e `common-password`, armazenam configurações que são aplicadas a múltiplos serviços, garantindo consistência e facilitando a manutenção.

1. **Arquivo `common-auth`**:
   - Define as regras de autenticação (por exemplo, senha e bloqueio de conta) aplicáveis a todos os serviços que incluem `common-auth`.

2. **Arquivo `common-account`**:
   - Define políticas de conta, como permissões e restrições de login, em todos os serviços que incluem `common-account`.

3. **Arquivo `common-password`**:
   - Configura políticas de senha (exigências de complexidade, regras de alteração de senha).

4. **Arquivo `common-session`**:
   - Especifica ações realizadas no início e fim de cada sessão (como o carregamento de variáveis de ambiente e limites de recursos).

#### 5.2 Diretrizes do PAM

Os arquivos PAM contêm quatro diretrizes principais que controlam como cada módulo é processado, como vimos na introdução. Essas diretrizes (`auth`, `account`, `password`, e `session`) têm um papel essencial no funcionamento do PAM:

1. **auth**:
   - Verifica a identidade do usuário, normalmente pedindo uma senha.
   
2. **account**:
   - Verifica políticas de conta, como permissões e condições de acesso.

3. **password**:
   - Controla as regras de criação e atualização de senhas.

4. **session**:
   - Define ações que ocorrem durante a sessão, como limites de uso de recursos.

#### 5.3 Exemplos Práticos de Configuração de Arquivos PAM

Para ilustrar o uso do PAM na prática, vamos configurar o arquivo `/etc/pam.d/common-auth` para implementar uma autenticação local com segurança adicional e controle de tentativas de login.

##### Exemplo 1: Configuração de Autenticação com Bloqueio de Conta

Aqui configuramos o arquivo `/etc/pam.d/common-auth` para bloquear contas após múltiplas tentativas de login falhas, usando `pam_tally2.so`.

1. **Edite o Arquivo `/etc/pam.d/common-auth`**:
   - Adicione as seguintes linhas:
     ```plaintext
     auth    required    pam_tally2.so deny=5 unlock_time=600
     auth    required    pam_unix.so
     ```
   - **Explicação**:
     - `pam_tally2.so deny=5 unlock_time=600`: Após 5 tentativas falhas, o usuário é bloqueado por 10 minutos.
     - `pam_unix.so`: Realiza a autenticação local utilizando a senha do usuário.

##### Exemplo 2: Configuração de Módulo PAM para Complexidade de Senha

Neste exemplo, vamos configurar o arquivo `/etc/pam.d/common-password` para reforçar as políticas de senha usando o módulo `pam_pwquality.so`, que exige uma senha forte para novas contas ou alterações de senha.

1. **Edite o Arquivo `/etc/pam.d/common-password`**:
   - Adicione a seguinte linha:
     ```plaintext
     password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
     ```
   - **Explicação**:
     - `pam_pwquality.so`: Exige uma senha forte, com no mínimo 10 caracteres e pelo menos uma letra maiúscula, uma minúscula, um dígito e um caractere especial.
     - `retry=3`: Permite que o usuário tente até 3 vezes antes de ser bloqueado temporariamente.

##### Exemplo 3: Configuração de Sessão com Limite de Recursos

Usando o módulo `pam_limits.so`, podemos definir limites de recursos para cada sessão, o que ajuda a controlar o uso de memória, CPU e processos por usuário.

1. **Configuração no Arquivo `/etc/security/limits.conf`**:
   - Este arquivo permite configurar limites de recursos por usuário ou grupo. Adicione uma linha para limitar o número de processos:
     ```plaintext
     <username>     hard    nproc       100
     ```
   - **Explicação**:
     - `<username>`: Substitua pelo nome do usuário ou `*` para aplicar a todos.
     - `hard`: Define o limite como rígido, não permitindo ao usuário ultrapassá-lo.
     - `nproc`: Limita o número de processos simultâneos que o usuário pode iniciar.

2. **Incluir o Módulo no Arquivo `/etc/pam.d/common-session`**:
   - Adicione a seguinte linha ao arquivo `/etc/pam.d/common-session` para aplicar os limites:
     ```plaintext
     session required pam_limits.so
     ```

#### 5.4 Tabela de Referência: Módulos e Configurações do PAM

| Arquivo PAM                  | Função                                                      | Exemplo de Configuração                                             |
|------------------------------|-------------------------------------------------------------|---------------------------------------------------------------------|
| **`common-auth`**            | Configuração de autenticação                                | `auth required pam_tally2.so deny=5 unlock_time=600`                |
| **`common-password`**        | Políticas de complexidade de senha                          | `password requisite pam_pwquality.so minlen=10 ucredit=-1 ...`      |
| **`common-session`**         | Limites de uso de recursos e configurações de sessão        | `session required pam_limits.so`                                    |
| **`/etc/security/limits.conf`** | Definição de limites de recurso para usuários e grupos     | `<username> hard nproc 100`                                         |

---

## 6. Configuração de Autenticação Multifatorial com PAM

### Objetivo
Demonstrar como configurar autenticação multifatorial (MFA) em sistemas Linux utilizando o **PAM** para melhorar a segurança, com foco em módulos como o Google Authenticator e `pam_u2f` (autenticação por chave de segurança).

### Conteúdo

#### 6.1 O que é Autenticação Multifatorial (MFA)?

A autenticação multifatorial (MFA) é uma prática de segurança que exige mais de uma forma de verificação antes de conceder acesso ao sistema. Em ambientes Linux, uma abordagem comum para MFA é exigir a senha do usuário e um segundo fator, como um código gerado por um aplicativo autenticador ou uma chave de segurança física.

#### 6.2 Configuração de Autenticação Multifatorial com Google Authenticator

O Google Authenticator gera códigos de uso único (OTP) que podem ser usados como segundo fator de autenticação. Abaixo estão os passos para configurar o PAM para exigir MFA com o Google Authenticator.

1. **Instalação do Google Authenticator**:
   - Instale o pacote do Google Authenticator no sistema.
   - **Comando**:
     ```bash
     sudo apt install libpam-google-authenticator  # Para distribuições Debian/Ubuntu
     ```

2. **Configuração do Google Authenticator para Cada Usuário**:
   - Cada usuário precisa configurar seu próprio autenticador. Para isso, execute o comando:
     ```bash
     google-authenticator
     ```
   - O comando exibe um QR code que pode ser escaneado com o aplicativo Google Authenticator. Também fornece opções de configuração, como:
     - **Exigir que o código seja usado uma única vez**.
     - **Permitir um número limitado de códigos de emergência**.
   - A configuração gerada é armazenada no diretório home do usuário, em `~/.google_authenticator`.

3. **Configuração do PAM para MFA**:
   - Para aplicar a MFA ao login ou SSH, edite o arquivo `/etc/pam.d/sshd` (para SSH) ou `/etc/pam.d/common-auth` (para logins locais) e adicione a seguinte linha:
     ```plaintext
     auth required pam_google_authenticator.so
     ```
   - **Nota**: A linha `auth required pam_google_authenticator.so` deve ser incluída antes do módulo de autenticação de senha (`pam_unix.so`), garantindo que o sistema exija o segundo fator antes da senha.

4. **Configuração do SSH para MFA**:
   - Se o MFA será usado para autenticação SSH, verifique se o arquivo de configuração `/etc/ssh/sshd_config` está configurado para suportar MFA:
     ```plaintext
     ChallengeResponseAuthentication yes
     ```
   - **Reinicie o SSH** para aplicar as mudanças:
     ```bash
     sudo systemctl restart sshd
     ```

#### 6.3 Configuração de Autenticação Multifatorial com Chaves de Segurança U2F

A autenticação com U2F (Universal 2nd Factor), como chaves de segurança YubiKey, oferece uma alternativa física para MFA.

1. **Instalação do `pam_u2f`**:
   - Instale o módulo PAM para U2F.
   - **Comando**:
     ```bash
     sudo apt install libpam-u2f
     ```

2. **Configuração da Chave U2F para Cada Usuário**:
   - Conecte a chave U2F e configure-a para o usuário. Primeiro, crie o diretório `.config/Yubico/` no home do usuário e gere o arquivo de mapeamento da chave:
     ```bash
     mkdir -p ~/.config/Yubico
     pamu2fcfg > ~/.config/Yubico/u2f_keys
     ```
   - O comando `pamu2fcfg` registra a chave U2F e cria o arquivo de configuração `u2f_keys` no diretório do usuário, necessário para que a chave seja reconhecida pelo PAM.

3. **Configuração do PAM para U2F**:
   - Para exigir autenticação U2F ao login ou SSH, edite o arquivo `/etc/pam.d/sshd` ou `/etc/pam.d/common-auth` e adicione a linha:
     ```plaintext
     auth required pam_u2f.so
     ```

4. **Configuração do SSH para Suporte U2F**:
   - Para habilitar U2F em SSH, edite o arquivo `/etc/ssh/sshd_config`:
     ```plaintext
     ChallengeResponseAuthentication yes
     ```
   - Reinicie o serviço SSH:
     ```bash
     sudo systemctl restart sshd
     ```

#### 6.4 Exemplo Completo: Configuração de Autenticação Multifatorial com Google Authenticator e U2F

Aqui está um exemplo de como configurar o sistema para exigir uma senha (fator 1), um código do Google Authenticator (fator 2) e, opcionalmente, uma chave U2F.

1. **Configuração do PAM para MFA**:
   - Edite o arquivo `/etc/pam.d/common-auth` para aplicar MFA ao login local. A configuração ficará assim:
     ```plaintext
     auth required pam_google_authenticator.so
     auth required pam_u2f.so
     auth required pam_unix.so
     ```

   - **Explicação**:
     - `pam_google_authenticator.so`: Exige que o usuário insira um código OTP do Google Authenticator.
     - `pam_u2f.so`: Solicita a chave U2F como um segundo fator.
     - `pam_unix.so`: Solicita a senha do usuário para autenticação local.

2. **Configuração do SSH para MFA**:
   - Configure o SSH para aceitar Challenge-Response, editando `/etc/ssh/sshd_config`:
     ```plaintext
     ChallengeResponseAuthentication yes
     ```
   - Reinicie o SSH:
     ```bash
     sudo systemctl restart sshd
     ```

#### 6.5 Tabela de Referência: Módulos PAM para MFA

| Módulo PAM                      | Função                                                      | Exemplo de Configuração                                     |
|---------------------------------|-------------------------------------------------------------|-------------------------------------------------------------|
| **pam_google_authenticator.so** | Autenticação com Google Authenticator                       | `auth required pam_google_authenticator.so`                 |
| **pam_u2f.so**                  | Autenticação com chave de segurança U2F (ex.: YubiKey)      | `auth required pam_u2f.so`                                  |
| **ChallengeResponseAuthentication** (SSH) | Habilita MFA para sessões SSH                         | `ChallengeResponseAuthentication yes`                       |

---

## 7. Introdução ao SSSD e Suas Funcionalidades

### Objetivo
Apresentar o **SSSD (System Security Services Daemon)** como uma solução para autenticação centralizada em sistemas Linux, explicando como ele facilita a integração com serviços de diretório (como LDAP e Active Directory) e suporte para autenticação offline.

### Conteúdo

#### 7.1 O que é o SSSD?

O **SSSD (System Security Services Daemon)** é um serviço de gerenciamento de autenticação que permite a integração de sistemas Linux com diferentes serviços de diretório, incluindo LDAP, Kerberos, e Active Directory. Ele simplifica o processo de autenticação centralizada, fornecendo uma solução unificada para gerenciar contas de usuários e controlar o acesso.

O SSSD possui funcionalidades que vão além da autenticação, como:
- **Cache de Credenciais**: Armazena informações localmente, permitindo autenticação offline.
- **Autenticação Centralizada**: Integra sistemas Linux a um diretório central, facilitando o gerenciamento de usuários.
- **Políticas de Conta**: Gerencia políticas de expiração de senha, bloqueio de contas e regras de acesso.

#### 7.2 Principais Benefícios do SSSD

1. **Cache de Credenciais e Autenticação Offline**:
   - O SSSD permite que os usuários façam login mesmo quando o servidor de autenticação (como o LDAP) está temporariamente indisponível, armazenando em cache as credenciais localmente.

2. **Integração com Serviços de Diretório**:
   - O SSSD permite a integração com serviços de diretório populares como LDAP e Active Directory, facilitando o gerenciamento centralizado de contas em ambientes corporativos.

3. **Suporte a Múltiplos Provedores**:
   - Suporta vários provedores de identidade simultaneamente, permitindo autenticação em ambientes híbridos (ex.: LDAP e Kerberos).

#### 7.3 Arquivo de Configuração do SSSD (`/etc/sssd/sssd.conf`)

O arquivo principal de configuração do SSSD é o `/etc/sssd/sssd.conf`. Este arquivo define parâmetros para o SSSD e contém seções específicas para provedores de identidade (como LDAP e Kerberos).

1. **Estrutura Básica do Arquivo `sssd.conf`**:
   - Abaixo está um exemplo básico do arquivo `sssd.conf` para uma configuração com um provedor LDAP:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = exemplo.local

     [domain/exemplo.local]
     id_provider = ldap
     auth_provider = ldap
     ldap_uri = ldap://ldap.exemplo.local
     ldap_search_base = dc=exemplo,dc=local
     ```
   - **Explicação**:
     - `[sssd]`: Configuração global do serviço SSSD.
     - `services`: Define os serviços habilitados (`nss` para integração de identidade e `pam` para autenticação).
     - `domains`: Lista os domínios (provedores de identidade) que o SSSD gerencia.
     - `[domain/exemplo.local]`: Define um domínio de autenticação, neste caso, um servidor LDAP com base em `exemplo.local`.
     - `id_provider` e `auth_provider`: Configura o provedor de identidade e autenticação como `ldap`.
     - `ldap_uri`: URI do servidor LDAP.
     - `ldap_search_base`: Base de pesquisa do diretório LDAP.

2. **Permissões do `sssd.conf`**:
   - O arquivo `sssd.conf` deve ter permissões restritas para proteger credenciais sensíveis.
   - **Comando**:
     ```bash
     sudo chmod 600 /etc/sssd/sssd.conf
     ```

#### 7.4 Exemplo de Caso de Uso do SSSD em um Ambiente Corporativo

Imagine um ambiente corporativo onde a autenticação dos usuários é centralizada em um servidor LDAP. Com o SSSD, todos os sistemas Linux podem se conectar ao servidor LDAP para autenticação, proporcionando os seguintes benefícios:

- **Consistência de Acesso**: Todas as máquinas Linux podem autenticar usuários com as mesmas credenciais.
- **Facilidade de Gerenciamento**: O administrador de TI gerencia apenas uma base de dados de usuários no servidor LDAP.
- **Autenticação Offline**: Mesmo se o servidor LDAP ficar temporariamente indisponível, os usuários conseguem fazer login graças ao cache de credenciais do SSSD.

#### 7.5 Configuração Básica para Habilitar o SSSD

1. **Instalação do SSSD**:
   - Em distribuições baseadas em Debian/Ubuntu, use:
     ```bash
     sudo apt install sssd
     ```

2. **Configuração do `sssd.conf` para LDAP**:
   - Crie o arquivo de configuração `/etc/sssd/sssd.conf` com os seguintes parâmetros:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = exemplo.local

     [domain/exemplo.local]
     id_provider = ldap
     auth_provider = ldap
     ldap_uri = ldap://ldap.exemplo.local
     ldap_search_base = dc=exemplo,dc=local
     ```
   - **Permissão**:
     ```bash
     sudo chmod 600 /etc/sssd/sssd.conf
     ```

3. **Configuração do NSS e PAM para SSSD**:
   - Edite o arquivo `/etc/nsswitch.conf` para incluir o SSSD nos serviços de identidade:
     ```plaintext
     passwd:     compat sss
     group:      compat sss
     shadow:     compat sss
     ```
   - Isso garante que o sistema Linux use o SSSD para buscar informações de contas de usuários e grupos.

4. **Iniciar e Ativar o Serviço SSSD**:
   - Ative e inicie o SSSD:
     ```bash
     sudo systemctl enable sssd
     sudo systemctl start sssd
     ```

#### Tabela de Referência: Principais Parâmetros do SSSD

| Parâmetro               | Descrição                                                        | Exemplo                                            |
|-------------------------|------------------------------------------------------------------|----------------------------------------------------|
| `services`              | Define serviços habilitados pelo SSSD (`nss`, `pam`)            | `services = nss, pam`                              |
| `domains`               | Lista domínios gerenciados pelo SSSD                            | `domains = exemplo.local`                          |
| `id_provider`           | Define o provedor de identidade (ex.: `ldap`)                   | `id_provider = ldap`                               |
| `auth_provider`         | Define o provedor de autenticação (ex.: `ldap`)                 | `auth_provider = ldap`                             |
| `ldap_uri`              | Endereço do servidor LDAP                                       | `ldap_uri = ldap://ldap.exemplo.local`             |
| `ldap_search_base`      | Base de pesquisa no diretório LDAP                              | `ldap_search_base = dc=exemplo,dc=local`           |

---

## 8. Instalação e Configuração Básica do SSSD

### Objetivo
Demonstrar como instalar e configurar o **SSSD** para uma autenticação centralizada básica, abordando a configuração do arquivo principal `sssd.conf` e a integração com o NSS (Name Service Switch) e PAM.

### Conteúdo

#### 8.1 Instalação do SSSD

O SSSD pode ser instalado nas principais distribuições Linux por meio do gerenciador de pacotes.

1. **Instalação em Debian/Ubuntu**:
   - Use o seguinte comando:
     ```bash
     sudo apt install sssd
     ```

2. **Instalação em Red Hat/CentOS**:
   - Use o comando:
     ```bash
     sudo yum install sssd
     ```

#### 8.2 Estrutura do Arquivo de Configuração `sssd.conf`

O arquivo de configuração principal do SSSD é o `/etc/sssd/sssd.conf`. Abaixo está a estrutura básica do arquivo, com parâmetros comuns para integração com um servidor LDAP.

1. **Criação do Arquivo `sssd.conf`**:
   - Crie o arquivo `/etc/sssd/sssd.conf` se ele ainda não existir e adicione as seguintes configurações:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = exemplo.local

     [domain/exemplo.local]
     id_provider = ldap
     auth_provider = ldap
     ldap_uri = ldap://ldap.exemplo.local
     ldap_search_base = dc=exemplo,dc=local
     ldap_id_use_start_tls = true
     ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt
     ```
   - **Explicação dos Parâmetros**:
     - **[sssd]**: Configuração global do SSSD.
       - `config_file_version = 2`: Versão do arquivo de configuração.
       - `services = nss, pam`: Ativa os serviços NSS (Name Service Switch) e PAM.
       - `domains = exemplo.local`: Define o domínio LDAP como `exemplo.local`.
     - **[domain/exemplo.local]**: Configura o domínio LDAP.
       - `id_provider = ldap`: Define o provedor de identidade como `ldap`.
       - `auth_provider = ldap`: Define o provedor de autenticação como `ldap`.
       - `ldap_uri`: Especifica o endereço do servidor LDAP.
       - `ldap_search_base`: Define o ponto de início para buscas no diretório.
       - `ldap_id_use_start_tls`: Habilita o uso de TLS para proteger a comunicação.
       - `ldap_tls_cacert`: Especifica o caminho para o certificado da CA.

2. **Permissão do Arquivo `sssd.conf`**:
   - Por segurança, o arquivo `sssd.conf` deve ter permissões restritas:
     ```bash
     sudo chmod 600 /etc/sssd/sssd.conf
     ```

#### 8.3 Integração do SSSD com NSS e PAM

Para que o SSSD funcione corretamente, ele precisa ser integrado ao NSS e PAM para gerenciar a autenticação de usuários e a consulta de dados de identidade.

1. **Configuração do NSS**:
   - Edite o arquivo `/etc/nsswitch.conf` para que o sistema consulte o SSSD ao buscar informações de usuários e grupos.
   - Adicione `sss` às linhas `passwd`, `group` e `shadow`:
     ```plaintext
     passwd:     compat sss
     group:      compat sss
     shadow:     compat sss
     ```

2. **Configuração do PAM**:
   - O SSSD precisa ser incluído nas configurações PAM para que ele participe do processo de autenticação. Em distribuições baseadas em Debian/Ubuntu, as configurações já estão prontas após a instalação.
   - Verifique os arquivos `/etc/pam.d/common-auth`, `/etc/pam.d/common-account` e `/etc/pam.d/common-session`, que devem conter referências ao `sss`:
     ```plaintext
     auth    [success=1 default=ignore] pam_sss.so
     account [success=1 new_authtok_reqd=done default=ignore] pam_sss.so
     password sufficient pam_sss.so
     session optional pam_sss.so
     ```

#### 8.4 Iniciar e Ativar o SSSD

Depois de configurar o arquivo `sssd.conf` e integrar o SSSD ao NSS e PAM, inicie e habilite o serviço para que ele seja carregado automaticamente no boot.

1. **Iniciar o Serviço SSSD**:
   ```bash
   sudo systemctl start sssd
   ```

2. **Habilitar o SSSD no Boot**:
   ```bash
   sudo systemctl enable sssd
   ```

3. **Verificar o Status do SSSD**:
   - Confirme que o serviço está ativo e sem erros:
     ```bash
     sudo systemctl status sssd
     ```

#### 8.5 Testando a Configuração do SSSD

Após a configuração, é importante testar se o SSSD está funcionando corretamente.

1. **Consultar um Usuário LDAP**:
   - Para testar a integração com o LDAP, use o comando `id` para verificar as informações de um usuário LDAP.
   - **Comando**:
     ```bash
     id <nome_do_usuário_ldap>
     ```
   - Se a configuração estiver correta, o comando exibirá as informações do usuário obtidas do servidor LDAP.

2. **Realizar Login com um Usuário LDAP**:
   - Tente fazer login no sistema usando um usuário do LDAP para garantir que a autenticação via SSSD está funcionando conforme esperado.

#### Tabela de Referência: Configurações Básicas do SSSD

| Parâmetro                 | Descrição                                                          | Exemplo                                              |
|---------------------------|--------------------------------------------------------------------|------------------------------------------------------|
| `services`                | Define os serviços habilitados pelo SSSD (`nss`, `pam`)           | `services = nss, pam`                                |
| `domains`                 | Lista domínios gerenciados pelo SSSD                              | `domains = exemplo.local`                            |
| `id_provider`             | Define o provedor de identidade (ex.: `ldap`)                     | `id_provider = ldap`                                 |
| `auth_provider`           | Define o provedor de autenticação (ex.: `ldap`)                   | `auth_provider = ldap`                               |
| `ldap_uri`                | URI do servidor LDAP                                              | `ldap_uri = ldap://ldap.exemplo.local`               |
| `ldap_search_base`        | Define a base de pesquisa do diretório LDAP                       | `ldap_search_base = dc=exemplo,dc=local`             |
| `ldap_id_use_start_tls`   | Habilita StartTLS para a conexão segura com o servidor LDAP       | `ldap_id_use_start_tls = true`                       |
| `ldap_tls_cacert`         | Caminho para o certificado CA para StartTLS                       | `ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt` |

---

## 9. Integração do SSSD com LDAP para Autenticação Centralizada

### Objetivo
Explicar como integrar o **SSSD** com um servidor **LDAP** para autenticação centralizada de usuários, detalhando a configuração do `sssd.conf` e o mapeamento de atributos necessários para busca e autenticação.

### Conteúdo

#### 9.1 Configuração do `sssd.conf` para Integração com LDAP

Para configurar o SSSD de forma a autenticar usuários de um servidor LDAP, ajustes detalhados no arquivo `sssd.conf` são necessários. Abaixo está um exemplo de configuração típica para autenticação LDAP.

1. **Configuração do Arquivo `sssd.conf`**:
   - Edite o arquivo `/etc/sssd/sssd.conf` e adicione os parâmetros para integração com o servidor LDAP:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = exemplo.local

     [domain/exemplo.local]
     id_provider = ldap
     auth_provider = ldap
     ldap_uri = ldap://ldap.exemplo.local
     ldap_search_base = dc=exemplo,dc=local
     ldap_id_use_start_tls = true
     ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt
     ldap_default_bind_dn = cn=admin,dc=exemplo,dc=local
     ldap_default_authtok_type = password
     ldap_default_authtok = <senha_do_admin_ldap>
     ```
   - **Explicação dos Parâmetros**:
     - `ldap_default_bind_dn`: Especifica o Distinguished Name (DN) usado para se conectar ao servidor LDAP.
     - `ldap_default_authtok_type` e `ldap_default_authtok`: Define a autenticação usada para bind LDAP com senha do administrador.

#### 9.2 Configuração de Mapeamento de Atributos LDAP

Os atributos LDAP, como UID e GID, precisam estar mapeados corretamente para que o SSSD possa associar informações do diretório LDAP aos atributos de identidade em Linux.

1. **Atributos LDAP Importantes**:
   - **uid**: Nome de usuário.
   - **gidNumber**: ID de grupo.
   - **homeDirectory**: Diretório home do usuário.
   - **loginShell**: Shell de login padrão.

2. **Adicionando Mapeamento de Atributos no `sssd.conf`**:
   - Certifique-se de que o arquivo `sssd.conf` inclui o mapeamento de atributos necessário:
     ```ini
     ldap_user_uid_number = uidNumber
     ldap_user_gid_number = gidNumber
     ldap_user_home_directory = homeDirectory
     ldap_user_shell = loginShell
     ```

#### 9.3 Cache de Credenciais e Autenticação Offline

O SSSD armazena em cache as credenciais dos usuários, permitindo que eles façam login mesmo que o servidor LDAP esteja temporariamente inacessível.

1. **Configuração do Cache de Credenciais**:
   - No arquivo `sssd.conf`, defina o tempo de retenção do cache com `entry_cache_timeout`. Exemplo:
     ```ini
     entry_cache_timeout = 600
     ```
   - Esse parâmetro define que as informações armazenadas em cache serão atualizadas a cada 10 minutos (600 segundos).

#### 9.4 Exemplo Prático de Configuração Completa do `sssd.conf` para LDAP

Aqui está um exemplo completo do arquivo `sssd.conf` configurado para autenticação centralizada com LDAP:

```ini
[sssd]
config_file_version = 2
services = nss, pam
domains = exemplo.local

[domain/exemplo.local]
id_provider = ldap
auth_provider = ldap
ldap_uri = ldap://ldap.exemplo.local
ldap_search_base = dc=exemplo,dc=local
ldap_id_use_start_tls = true
ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt
ldap_default_bind_dn = cn=admin,dc=exemplo,dc=local
ldap_default_authtok_type = password
ldap_default_authtok = <senha_do_admin_ldap>

# Mapeamento de atributos
ldap_user_uid_number = uidNumber
ldap_user_gid_number = gidNumber
ldap_user_home_directory = homeDirectory
ldap_user_shell = loginShell

# Configuração de cache
entry_cache_timeout = 600
```

1. **Configuração do Serviço SSSD**:
   - Após editar o arquivo `sssd.conf`, aplique as permissões corretas e reinicie o serviço:
     ```bash
     sudo chmod 600 /etc/sssd/sssd.conf
     sudo systemctl restart sssd
     ```

#### 9.5 Verificando a Conexão com o LDAP

1. **Teste de Consulta LDAP com `ldapsearch`**:
   - Antes de confirmar a configuração do SSSD, teste a conexão com o LDAP usando o comando `ldapsearch`:
     ```bash
     ldapsearch -x -H ldap://ldap.exemplo.local -b "dc=exemplo,dc=local"
     ```
   - Esse comando lista as entradas LDAP na base de pesquisa `dc=exemplo,dc=local`.

2. **Teste de Consulta via `id` com SSSD**:
   - Com o SSSD configurado, use o comando `id` para verificar as informações de um usuário LDAP:
     ```bash
     id <nome_do_usuário_ldap>
     ```
   - Se a configuração estiver correta, o comando deve retornar as informações do usuário, como UID e GID.

#### 9.6 Tabela de Referência: Configurações Específicas do SSSD para LDAP

| Parâmetro                     | Descrição                                                        | Exemplo                                           |
|-------------------------------|------------------------------------------------------------------|---------------------------------------------------|
| `ldap_default_bind_dn`        | DN usado para se conectar ao LDAP                                | `ldap_default_bind_dn = cn=admin,dc=exemplo,dc=local` |
| `ldap_default_authtok`        | Senha do DN para autenticação LDAP                               | `ldap_default_authtok = <senha_do_admin_ldap>`    |
| `ldap_user_uid_number`        | Mapeia o atributo LDAP para UID                                  | `ldap_user_uid_number = uidNumber`                |
| `ldap_user_gid_number`        | Mapeia o atributo LDAP para GID                                  | `ldap_user_gid_number = gidNumber`                |
| `ldap_user_home_directory`    | Define o atributo para o diretório home do usuário               | `ldap_user_home_directory = homeDirectory`        |
| `entry_cache_timeout`         | Define o tempo de retenção de cache de identidade em segundos    | `entry_cache_timeout = 600`                       |

---

## 10. Integração do SSSD com Kerberos para Autenticação Segura

### Objetivo
Demonstrar como configurar o **SSSD** para autenticar usuários com um servidor **Kerberos**, usando um sistema de tickets para maior segurança e autenticidade, configurando o sistema Linux para emitir e gerenciar tickets Kerberos de forma integrada.

### Conteúdo

#### 10.1 Introdução ao Kerberos

O **Kerberos** é um protocolo de autenticação que utiliza um sistema de tickets para validar a identidade de usuários e serviços em rede. Sua arquitetura se baseia no **KDC (Key Distribution Center)**, que gera **TGTs (Ticket-Granting Tickets)** que permitem ao usuário acessar serviços adicionais sem precisar reinserir as credenciais.

Componentes principais:
- **KDC (Key Distribution Center)**: O servidor que emite e valida tickets.
- **TGT (Ticket-Granting Ticket)**: Ticket inicial que permite solicitar acesso a serviços.
- **SPN (Service Principal Name)**: Identificador de cada serviço registrado para autenticação.

#### 10.2 Configuração do Cliente Kerberos no Sistema

Antes de integrar o SSSD com o Kerberos, configure o cliente Kerberos para garantir que o sistema possa se autenticar corretamente com o KDC.

1. **Instalação do Cliente Kerberos**:
   - No Debian/Ubuntu, instale o cliente Kerberos com:
     ```bash
     sudo apt install krb5-user
     ```
   - Em Red Hat/CentOS, instale com:
     ```bash
     sudo yum install krb5-workstation
     ```

2. **Configuração do Arquivo `/etc/krb5.conf`**:
   - Edite o arquivo `/etc/krb5.conf` para configurar o realm e o KDC:
     ```ini
     [libdefaults]
     default_realm = EXEMPLO.LOCAL
     dns_lookup_kdc = true
     dns_lookup_realm = false

     [realms]
     EXEMPLO.LOCAL = {
         kdc = kdc.exemplo.local
         admin_server = kdc.exemplo.local
     }

     [domain_realm]
     .exemplo.local = EXEMPLO.LOCAL
     exemplo.local = EXEMPLO.LOCAL
     ```
   - **Explicação**:
     - `default_realm`: Define o realm Kerberos padrão.
     - `kdc`: Endereço do servidor KDC.
     - `admin_server`: Define o servidor administrativo do Kerberos.

3. **Testar a Configuração do Kerberos**:
   - Verifique se o cliente Kerberos está funcionando com `kinit` e `klist`:
     ```bash
     kinit <nome_do_usuário_kerberos>
     klist
     ```
   - Após executar `kinit` e inserir a senha, `klist` exibirá o ticket TGT obtido.

#### 10.3 Configuração do `sssd.conf` para Autenticação com Kerberos

Após configurar o cliente Kerberos, vamos configurar o SSSD para usar Kerberos como provedor de autenticação.

1. **Exemplo de Configuração Kerberos no `sssd.conf`**:
   - No arquivo `/etc/sssd/sssd.conf`, configure o Kerberos como provedor de autenticação:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = exemplo.local

     [domain/exemplo.local]
     id_provider = ldap
     auth_provider = krb5
     chpass_provider = krb5
     ldap_uri = ldap://ldap.exemplo.local
     ldap_search_base = dc=exemplo,dc=local
     ldap_id_use_start_tls = true
     ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt

     krb5_realm = EXEMPLO.LOCAL
     krb5_server = kdc.exemplo.local
     krb5_kpasswd = kdc.exemplo.local
     ```
   - **Explicação dos Parâmetros**:
     - `auth_provider = krb5`: Define Kerberos como provedor de autenticação.
     - `krb5_realm`: Define o realm do Kerberos.
     - `krb5_server` e `krb5_kpasswd`: Especificam o servidor KDC e o servidor de troca de senha.

2. **Cache de Senha para Autenticação Offline com Kerberos**:
   - Para permitir autenticação offline, adicione:
     ```ini
     krb5_store_password_if_offline = true
     ```
   - Esse parâmetro armazena a senha localmente se o servidor KDC estiver inacessível temporariamente.

3. **Definir Permissão do `sssd.conf`**:
   - Para proteger as credenciais, restrinja as permissões:
     ```bash
     sudo chmod 600 /etc/sssd/sssd.conf
     ```

4. **Reiniciar o Serviço SSSD**:
   - Reinicie o serviço para aplicar as configurações:
     ```bash
     sudo systemctl restart sssd
     ```

#### 10.4 Testando a Autenticação com Kerberos

1. **Verificação de Tickets com `klist`**:
   - Faça login com um usuário Kerberos e verifique os tickets usando:
     ```bash
     klist
     ```
   - O comando deve mostrar o TGT, confirmando que a autenticação Kerberos está em funcionamento.

2. **Login com Usuário Kerberos**:
   - Teste o login no sistema com um usuário do Kerberos para verificar a autenticação pelo SSSD.

#### 10.5 Exemplo Completo do `sssd.conf` para LDAP e Kerberos

Aqui está um exemplo completo do arquivo `sssd.conf` configurado para autenticação integrada com LDAP e Kerberos:

```ini
[sssd]
config_file_version = 2
services = nss, pam
domains = exemplo.local

[domain/exemplo.local]
id_provider = ldap
auth_provider = krb5
chpass_provider = krb5
ldap_uri = ldap://ldap.exemplo.local
ldap_search_base = dc=exemplo,dc=local
ldap_id_use_start_tls = true
ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt

krb5_realm = EXEMPLO.LOCAL
krb5_server = kdc.exemplo.local
krb5_kpasswd = kdc.exemplo.local
krb5_store_password_if_offline = true
```

#### Tabela de Referência: Parâmetros Kerberos no `sssd.conf`

| Parâmetro                         | Descrição                                                             | Exemplo                               |
|-----------------------------------|-----------------------------------------------------------------------|---------------------------------------|
| `auth_provider = krb5`            | Define Kerberos como provedor de autenticação                         | `auth_provider = krb5`                |
| `krb5_realm`                      | Define o realm padrão para Kerberos                                   | `krb5_realm = EXEMPLO.LOCAL`          |
| `krb5_server`                     | Define o servidor KDC                                                 | `krb5_server = kdc.exemplo.local`     |
| `krb5_kpasswd`                    | Define o servidor para mudança de senha Kerberos                      | `krb5_kpasswd = kdc.exemplo.local`    |
| `krb5_store_password_if_offline`  | Armazena a senha em cache se o KDC estiver indisponível               | `krb5_store_password_if_offline = true` |

---

## 11. Configuração de Autenticação de Clientes com FreeIPA e SSSD

### Objetivo
Explicar como configurar um cliente Linux para se integrar ao **FreeIPA** usando o **SSSD** para autenticação centralizada. O FreeIPA combina LDAP e Kerberos, fornecendo uma solução completa para gerenciamento de identidades e autenticação em redes corporativas.

### Conteúdo

#### 11.1 O que é FreeIPA?

O **FreeIPA** é uma solução de gerenciamento de identidades, políticas e auditoria para redes Linux e Unix, baseada em LDAP, Kerberos, e DNS. Ele permite configurar e gerenciar autenticação centralizada, grupos, permissões e políticas de segurança de forma simplificada e integrada, o que é ideal para ambientes corporativos.

Componentes principais do FreeIPA:
- **LDAP** para armazenamento de informações de identidade.
- **Kerberos** para autenticação segura baseada em tickets.
- **DNS** e **Certificados** para a configuração e segurança da rede.

#### 11.2 Instalação do Cliente FreeIPA

Para conectar uma máquina Linux ao FreeIPA, é necessário instalar e configurar o **cliente FreeIPA**. Este cliente simplifica o processo de integração, configurando automaticamente o SSSD e outros serviços para autenticação.

1. **Instalação do Cliente FreeIPA**:
   - Em sistemas Debian/Ubuntu, instale com:
     ```bash
     sudo apt install freeipa-client
     ```
   - Em Red Hat/CentOS:
     ```bash
     sudo yum install ipa-client
     ```

#### 11.3 Configuração do Cliente FreeIPA

Após instalar o cliente, o próximo passo é configurar a integração com o servidor FreeIPA usando o comando `ipa-client-install`.

1. **Executando o `ipa-client-install`**:
   - Execute o comando `ipa-client-install` para iniciar o processo de configuração:
     ```bash
     sudo ipa-client-install --domain=exemplo.local --server=ipa.exemplo.local --realm=EXEMPLO.LOCAL
     ```
   - O comando solicita confirmação para várias configurações e pode solicitar a senha de um administrador do FreeIPA.

2. **Parâmetros Principais do `ipa-client-install`**:
   - `--domain`: Define o domínio do FreeIPA.
   - `--server`: Endereço do servidor FreeIPA.
   - `--realm`: Define o realm Kerberos do FreeIPA.

3. **Configuração Automática**:
   - O `ipa-client-install` configura automaticamente o SSSD, Kerberos e o cliente LDAP, integrando o sistema ao FreeIPA.
   - Ele também cria entradas DNS (se configurado) e baixa certificados de CA para segurança da conexão.

#### 11.4 Testando a Integração com o FreeIPA

1. **Verificação de Autenticação**:
   - Após a instalação, faça login com um usuário FreeIPA no sistema para verificar a integração.
   - Use o comando `id` para ver as informações de um usuário FreeIPA:
     ```bash
     id <nome_do_usuário_ipa>
     ```

2. **Verificação de Tickets Kerberos com `klist`**:
   - Após o login, use o comando `klist` para verificar se o ticket Kerberos foi gerado.
   - **Comando**:
     ```bash
     klist
     ```

#### 11.5 Configuração Avançada do FreeIPA com o SSSD

Embora o `ipa-client-install` configure automaticamente o SSSD, ajustes adicionais podem ser feitos no arquivo `/etc/sssd/sssd.conf` para personalizar a configuração.

1. **Configurações Avançadas no `sssd.conf`**:
   - Parâmetros como cache e mapeamento de atributos podem ser ajustados. Aqui está um exemplo com cache de credenciais:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = exemplo.local

     [domain/exemplo.local]
     id_provider = ipa
     auth_provider = ipa
     cache_credentials = true
     entry_cache_timeout = 600
     ```

2. **Explicação dos Parâmetros**:
   - `id_provider` e `auth_provider`: Definem o FreeIPA como o provedor de identidade e autenticação.
   - `cache_credentials`: Ativa o cache de credenciais para permitir autenticação offline.
   - `entry_cache_timeout`: Define o tempo em segundos para atualizar o cache de identidade.

#### 11.6 Gerenciamento de Políticas e Grupos no FreeIPA

1. **Acesso e Permissões com FreeIPA**:
   - O FreeIPA permite gerenciar grupos, permissões e políticas de acesso diretamente pelo console de administração web.
   - Grupos podem ser criados e associados a políticas de autenticação específicas, facilitando o gerenciamento de permissões em larga escala.

2. **Autenticação de Grupos com SSSD**:
   - No FreeIPA, grupos LDAP podem ser sincronizados com o SSSD, permitindo que usuários herdem permissões e políticas baseadas no grupo ao qual pertencem.

#### 11.7 Tabela de Referência: Comandos FreeIPA

| Comando                         | Descrição                                                   |
|---------------------------------|-------------------------------------------------------------|
| `ipa-client-install`            | Instala e configura um cliente para autenticação com FreeIPA|
| `id <nome_do_usuário>`          | Verifica as informações de um usuário autenticado pelo FreeIPA |
| `klist`                         | Lista os tickets Kerberos ativos                            |
| `ipa user-add`                  | Adiciona um novo usuário no FreeIPA                         |
| `ipa group-add`                 | Cria um novo grupo no FreeIPA                               |

---

## 12. Integração do FreeIPA com Kerberos e Configuração de Tickets

### Objetivo
Explicar como configurar e gerenciar a autenticação baseada em **tickets Kerberos** no **FreeIPA**, permitindo que os usuários se autentiquem em múltiplos serviços sem reinserir suas credenciais, e detalhar o processo de emissão e gerenciamento de tickets.

### Conteúdo

#### 12.1 Como Funciona a Autenticação com Tickets Kerberos no FreeIPA

O FreeIPA utiliza o **Kerberos** como sistema de autenticação padrão, oferecendo um mecanismo de autenticação centralizado baseado em tickets. Isso significa que, ao fazer login, um usuário recebe um **Ticket-Granting Ticket (TGT)** do **KDC (Key Distribution Center)**, que é o servidor central do Kerberos. Com o TGT, o usuário pode solicitar acesso a outros serviços de rede sem precisar fornecer as credenciais novamente.

**Principais componentes**:
- **TGT (Ticket-Granting Ticket)**: Ticket inicial obtido ao autenticar-se, permitindo solicitar outros tickets.
- **Service Ticket**: Tickets individuais que autorizam o usuário a acessar serviços específicos.
- **KDC (Key Distribution Center)**: Emissor central dos tickets, gerenciado pelo FreeIPA.

#### 12.2 Configuração do Kerberos no FreeIPA

O servidor FreeIPA já possui um KDC integrado, mas é importante garantir que o cliente Linux esteja corretamente configurado para se autenticar e usar os tickets emitidos.

1. **Verificação da Configuração do Cliente Kerberos**:
   - Certifique-se de que o arquivo `/etc/krb5.conf` contém as configurações corretas do realm do FreeIPA. Aqui está um exemplo:
     ```ini
     [libdefaults]
     default_realm = EXEMPLO.LOCAL
     dns_lookup_kdc = true
     dns_lookup_realm = false

     [realms]
     EXEMPLO.LOCAL = {
         kdc = ipa.exemplo.local
         admin_server = ipa.exemplo.local
     }

     [domain_realm]
     .exemplo.local = EXEMPLO.LOCAL
     exemplo.local = EXEMPLO.LOCAL
     ```

2. **Configuração de Usuários com Autenticação Kerberos no FreeIPA**:
   - No FreeIPA, ao criar um usuário, ele automaticamente recebe uma principal Kerberos. Isso significa que o usuário está configurado para autenticação via tickets, e sua identidade pode ser usada para acessar serviços na rede.

#### 12.3 Uso de Tickets Kerberos para Autenticação

Uma vez que o cliente Linux esteja configurado com FreeIPA e Kerberos, o processo de autenticação ocorre da seguinte forma:

1. **Obtenção do TGT com `kinit`**:
   - Para verificar se um usuário pode obter um TGT, use o comando `kinit`:
     ```bash
     kinit <nome_do_usuário_ipa>
     ```
   - Após inserir a senha, o usuário receberá um TGT válido. Esse ticket permite ao usuário solicitar tickets de serviços adicionais.

2. **Visualizar Tickets Ativos com `klist`**:
   - O comando `klist` exibe todos os tickets Kerberos ativos, incluindo o TGT e quaisquer Service Tickets.
   - **Comando**:
     ```bash
     klist
     ```

#### 12.4 Renovação e Expiração de Tickets

Os tickets Kerberos possuem um tempo de validade após o qual eles expiram, exigindo renovação ou reemissão. Configurar a renovação automática de tickets ajuda a evitar problemas de autenticação.

1. **Configuração do Tempo de Expiração de Tickets**:
   - O FreeIPA permite definir o tempo de expiração dos tickets diretamente no servidor.
   - No FreeIPA, acesse **Identity > Authentication Settings** e ajuste o **Maximum ticket lifetime** e **Maximum renewable lifetime** de acordo com as políticas da organização.

2. **Renovação de Tickets com `kinit -R`**:
   - Para renovar um TGT ativo antes que ele expire, o usuário pode usar o comando:
     ```bash
     kinit -R
     ```
   - Esse comando tenta renovar o ticket, mantendo o usuário autenticado sem a necessidade de reinserir a senha.

#### 12.5 Gerenciamento de Tickets no Ambiente FreeIPA

1. **Revogação de Tickets e Controle de Sessão**:
   - Em casos de segurança, como perda de acesso ou comprometimento, o FreeIPA permite revogar tickets específicos ou realizar o logout forçado de usuários.

2. **Configuração de Políticas de Expiração e Renovação no FreeIPA**:
   - As políticas de expiração, renovação e reemissão de tickets são configuráveis no FreeIPA, garantindo que as práticas de segurança sejam seguidas.
   - No console web do FreeIPA, vá para **Identity > Policies** para definir regras de validade e expiração de tickets.

#### 12.6 Exemplo Prático: Autenticação com Tickets em um Cliente FreeIPA

Aqui está um exemplo de como obter, visualizar e renovar um TGT em um cliente configurado com o FreeIPA:

1. **Obter o TGT**:
   ```bash
   kinit usuario_ipa
   ```

2. **Visualizar o TGT e Service Tickets Ativos**:
   ```bash
   klist
   ```

3. **Renovar o Ticket para Evitar Expiração**:
   ```bash
   kinit -R
   ```

#### Tabela de Referência: Comandos Kerberos e FreeIPA

| Comando                        | Descrição                                                             |
|--------------------------------|-----------------------------------------------------------------------|
| `kinit <nome_do_usuário>`      | Obtém o TGT para um usuário do FreeIPA                                |
| `klist`                        | Lista todos os tickets ativos                                        |
| `kinit -R`                     | Renova o TGT, mantendo o usuário autenticado                          |
| `ipa user-mod`                 | Modifica atributos do usuário no FreeIPA                              |
| `ipa krbtpolicy-mod`           | Modifica as políticas de expiração e renovação de tickets Kerberos    |

---

## 13. Integração com Active Directory Usando SSSD

### Objetivo
Demonstrar como configurar o **SSSD** para integrar sistemas Linux ao **Active Directory (AD)**, permitindo autenticação centralizada e compartilhamento de identidades entre ambientes Linux e Windows.

### Conteúdo

#### 13.1 Integração Linux com Active Directory

A integração de sistemas Linux com o Active Directory oferece às organizações uma maneira de gerenciar contas e autenticação de forma centralizada em um ambiente de rede misto. O SSSD permite que usuários do AD façam login em máquinas Linux com as mesmas credenciais que usam para acessar recursos Windows, centralizando a gestão de identidade.

**Principais benefícios da integração com o AD**:
- **Autenticação Centralizada**: Permite que usuários utilizem suas credenciais do AD para acessar sistemas Linux.
- **Consistência de Identidade**: Permite a sincronização de permissões e grupos entre Linux e Windows.
- **Facilidade de Gestão**: Centraliza a administração de usuários, reduzindo a duplicação de contas.

#### 13.2 Configuração Inicial do Cliente Linux

Antes de configurar o SSSD para conectar-se ao AD, é necessário preparar o cliente Linux com as ferramentas e configurações necessárias.

1. **Instalação das Ferramentas Necessárias**:
   - Em distribuições baseadas em Debian/Ubuntu, instale os pacotes de cliente AD e o SSSD:
     ```bash
     sudo apt install realmd sssd adcli krb5-user
     ```
   - Em Red Hat/CentOS:
     ```bash
     sudo yum install realmd sssd adcli krb5-workstation
     ```

2. **Configuração do Kerberos para o Active Directory**:
   - No arquivo `/etc/krb5.conf`, configure o realm do Active Directory. Exemplo:
     ```ini
     [libdefaults]
     default_realm = AD.EXEMPLO.LOCAL
     dns_lookup_kdc = true
     dns_lookup_realm = false

     [realms]
     AD.EXEMPLO.LOCAL = {
         kdc = ad.exemplo.local
         admin_server = ad.exemplo.local
     }

     [domain_realm]
     .exemplo.local = AD.EXEMPLO.LOCAL
     exemplo.local = AD.EXEMPLO.LOCAL
     ```

#### 13.3 Associando o Cliente Linux ao Domínio do Active Directory

1. **Verificação do Realmd**:
   - O **realmd** facilita a associação de sistemas Linux ao AD. Antes de associar, verifique se o domínio está acessível:
     ```bash
     realm discover ad.exemplo.local
     ```

2. **Associação ao Domínio com o `realm join`**:
   - Use o comando `realm join` para associar o sistema Linux ao AD:
     ```bash
     sudo realm join ad.exemplo.local -U Administrador
     ```
   - O comando pedirá a senha do usuário `Administrador` do AD para autenticar a associação.

3. **Verificação da Associação**:
   - Para verificar se o sistema está associado ao domínio, use:
     ```bash
     realm list
     ```

#### 13.4 Configuração do `sssd.conf` para Active Directory

Após a associação, o `realmd` configura automaticamente o SSSD, mas ajustes adicionais podem ser necessários para garantir a integração completa.

1. **Ajustes no Arquivo `/etc/sssd/sssd.conf`**:
   - Após a associação, edite o arquivo `/etc/sssd/sssd.conf` para otimizar a configuração para o Active Directory:
     ```ini
     [sssd]
     config_file_version = 2
     services = nss, pam
     domains = ad.exemplo.local

     [domain/ad.exemplo.local]
     id_provider = ad
     access_provider = ad
     ldap_id_mapping = true
     fallback_homedir = /home/%u@%d
     default_shell = /bin/bash
     ```
   - **Explicação dos Parâmetros**:
     - `id_provider` e `access_provider`: Definem o AD como provedor de identidade e acesso.
     - `ldap_id_mapping`: Habilita o mapeamento automático de IDs para garantir que os IDs do AD sejam consistentes.
     - `fallback_homedir`: Define o diretório home padrão dos usuários.
     - `default_shell`: Define o shell padrão para usuários do AD.

2. **Permissão do Arquivo `sssd.conf`**:
   - Defina as permissões corretas para proteger o arquivo:
     ```bash
     sudo chmod 600 /etc/sssd/sssd.conf
     ```

3. **Reiniciar o Serviço SSSD**:
   - Após realizar os ajustes, reinicie o SSSD:
     ```bash
     sudo systemctl restart sssd
     ```

#### 13.5 Testando a Autenticação com Usuários do AD

1. **Verificar Informações de um Usuário AD com `id`**:
   - Use o comando `id` para verificar as informações de um usuário do Active Directory:
     ```bash
     id usuario_ad@ad.exemplo.local
     ```

2. **Login com um Usuário do AD**:
   - Tente fazer login no sistema com um usuário do AD para confirmar que a autenticação está funcionando.

#### 13.6 Controle de Acesso com o Active Directory

O controle de acesso no AD permite definir quais usuários e grupos podem acessar o sistema Linux.

1. **Configuração de Acesso Restrito**:
   - No arquivo `sssd.conf`, configure `access_provider = ad` para usar os grupos de segurança do AD.
   - Para limitar o acesso a grupos específicos, edite o arquivo `/etc/sssd/sssd.conf` e adicione:
     ```ini
     access_provider = simple
     simple_allow_groups = grupo_linux_ad
     ```
   - Isso permite o acesso apenas aos usuários que pertencem ao grupo `grupo_linux_ad` no AD.

#### Tabela de Referência: Comandos e Parâmetros para Integração com o AD

| Comando                          | Descrição                                                             |
|----------------------------------|-----------------------------------------------------------------------|
| `realm discover <domínio>`       | Descobre informações sobre o domínio AD                               |
| `realm join <domínio>`           | Associa o sistema Linux ao domínio AD                                 |
| `realm list`                     | Exibe domínios aos quais o sistema está associado                     |
| `id <usuário_ad@domínio>`        | Verifica informações do usuário do AD                                 |
| `sssd`                           | Serviço de segurança que gerencia autenticação centralizada           |

| Parâmetro no `sssd.conf`         | Descrição                                                             |
|----------------------------------|-----------------------------------------------------------------------|
| `id_provider = ad`               | Define o AD como provedor de identidade                               |
| `access_provider = ad`           | Define o AD como provedor de acesso                                   |
| `ldap_id_mapping = true`         | Ativa o mapeamento automático de IDs do AD                            |
| `fallback_homedir`               | Define o diretório home padrão para usuários do AD                    |
| `default_shell`                  | Define o shell padrão para usuários do AD                             |

---

## 14. Políticas de Bloqueio de Conta e Auditoria com PAM

### Objetivo
Demonstrar como configurar o **PAM** para aplicar políticas de bloqueio de conta e auditoria de tentativas de login, ajudando a proteger o sistema contra ataques de força bruta e garantir o monitoramento de tentativas de autenticação.

### Conteúdo

#### 14.1 Políticas de Bloqueio de Conta no PAM

Bloquear contas após múltiplas tentativas falhas de login é uma medida de segurança essencial para prevenir ataques de força bruta. Usando o módulo **PAM `pam_tally2`** ou **`pam_faillock`** (dependendo da distribuição), é possível definir quantas tentativas de login são permitidas antes de bloquear o acesso de um usuário.

1. **Configuração do `pam_tally2` para Bloqueio de Conta (Debian/Ubuntu)**:
   - Edite o arquivo `/etc/pam.d/common-auth` para adicionar a configuração do `pam_tally2`:
     ```plaintext
     auth required pam_tally2.so deny=5 unlock_time=600 onerr=fail audit
     ```
   - **Explicação dos Parâmetros**:
     - `deny=5`: Bloqueia a conta após 5 tentativas falhas.
     - `unlock_time=600`: Desbloqueia a conta automaticamente após 10 minutos (600 segundos).
     - `onerr=fail`: Bloqueia a conta em caso de erro.
     - `audit`: Ativa o registro em log de todas as tentativas de login.

2. **Configuração do `pam_faillock` para Bloqueio de Conta (Red Hat/CentOS)**:
   - Em distribuições Red Hat/CentOS, o módulo `pam_faillock` é mais comum. Edite o arquivo `/etc/pam.d/system-auth` e adicione:
     ```plaintext
     auth required pam_faillock.so preauth deny=5 unlock_time=600
     auth [success=1 default=bad] pam_unix.so
     auth [default=die] pam_faillock.so authfail deny=5 unlock_time=600
     ```
   - **Explicação dos Parâmetros**:
     - `preauth` e `authfail`: Verificam o status da conta e aplicam o bloqueio após tentativas falhas.
     - `deny=5`: Bloqueia a conta após 5 tentativas falhas.
     - `unlock_time=600`: Desbloqueia a conta após 10 minutos.

3. **Desbloqueio Manual de Contas com `pam_tally2`**:
   - Caso uma conta seja bloqueada, é possível desbloqueá-la manualmente com o comando:
     ```bash
     pam_tally2 --user <nome_do_usuário> --reset
     ```

#### 14.2 Auditoria de Tentativas de Login no PAM

O PAM pode registrar informações detalhadas sobre cada tentativa de autenticação, incluindo o sucesso ou falha do login e o motivo da falha. Essas informações são essenciais para monitorar atividades suspeitas e detectar tentativas de ataque.

1. **Ativando a Auditoria de Login com `pam_tally2`**:
   - No arquivo `/etc/pam.d/common-auth`, certifique-se de que o módulo `pam_tally2` possui a opção `audit` ativada:
     ```plaintext
     auth required pam_tally2.so deny=5 unlock_time=600 onerr=fail audit
     ```

2. **Ativando a Auditoria de Login com `pam_faillock`**:
   - Para `pam_faillock`, a auditoria é ativada automaticamente quando `preauth` e `authfail` são configurados com `audit`. Verifique as configurações em `/etc/pam.d/system-auth` para confirmar.

3. **Monitoramento dos Logs de Autenticação**:
   - Os logs de tentativas de login são geralmente armazenados em `/var/log/auth.log` (Debian/Ubuntu) ou `/var/log/secure` (Red Hat/CentOS). Use o comando `grep` para filtrar informações específicas de login:
     ```bash
     sudo grep "pam_tally2" /var/log/auth.log
     sudo grep "pam_faillock" /var/log/secure
     ```

#### 14.3 Exemplo Completo de Configuração de Bloqueio e Auditoria

Abaixo está um exemplo completo de configuração para aplicar políticas de bloqueio e auditoria usando o PAM em sistemas Debian/Ubuntu.

1. **Arquivo `/etc/pam.d/common-auth`**:
   - Edite o arquivo para incluir a política de bloqueio e auditoria:
     ```plaintext
     auth required pam_tally2.so deny=5 unlock_time=600 onerr=fail audit
     auth required pam_unix.so
     ```
   
2. **Arquivo `/etc/pam.d/common-account`**:
   - Configure para garantir que o `pam_tally2` verifique o status da conta durante o login:
     ```plaintext
     account required pam_tally2.so
     ```

#### 14.4 Tabela de Referência: Parâmetros de Bloqueio e Auditoria com PAM

| Parâmetro                         | Descrição                                                         |
|-----------------------------------|-------------------------------------------------------------------|
| `pam_tally2.so`                   | Módulo para contar e limitar tentativas de login (Debian/Ubuntu) |
| `pam_faillock.so`                 | Módulo para contar e limitar tentativas de login (Red Hat/CentOS)|
| `deny=<n>`                        | Número de tentativas de login antes do bloqueio                   |
| `unlock_time=<tempo>`             | Tempo (em segundos) para desbloqueio automático                   |
| `audit`                           | Ativa a auditoria de tentativas de login                          |
| `onerr=fail`                      | Bloqueia em caso de erro no módulo                                |

---

## 15. Monitoramento e Troubleshooting de Autenticação

### Objetivo
Explicar como monitorar e solucionar problemas comuns de autenticação em sistemas Linux, utilizando ferramentas de diagnóstico para analisar logs de autenticação e identificar erros em configurações de PAM, SSSD, LDAP e Kerberos.

### Conteúdo

#### 15.1 Monitoramento de Autenticação com Logs do Sistema

Os logs de autenticação são essenciais para monitorar atividades de login e diagnóstico de problemas de autenticação. Em sistemas Linux, diferentes arquivos de log fornecem informações detalhadas sobre tentativas de login e erros de configuração.

1. **Localização dos Logs de Autenticação**:
   - Em Debian/Ubuntu:
     - Logs de autenticação: `/var/log/auth.log`
   - Em Red Hat/CentOS:
     - Logs de autenticação: `/var/log/secure`

2. **Análise de Logs com `grep` e `tail`**:
   - Use `grep` para filtrar mensagens relacionadas ao PAM, SSSD e falhas de autenticação:
     ```bash
     sudo grep "pam" /var/log/auth.log
     sudo grep "sssd" /var/log/secure
     ```
   - Use `tail` para monitorar o log em tempo real e acompanhar tentativas de login:
     ```bash
     sudo tail -f /var/log/auth.log
     sudo tail -f /var/log/secure
     ```

3. **Mensagens Comuns nos Logs**:
   - **`authentication failure`**: Indica uma falha na autenticação, normalmente por senha incorreta.
   - **`account locked`**: Informa que a conta está bloqueada após múltiplas tentativas falhas.
   - **`no logins from <source>`**: Indica que logins de uma determinada origem estão restritos.

#### 15.2 Troubleshooting de Autenticação com PAM

Ao configurar o PAM, erros de sintaxe e configurações inadequadas podem bloquear o acesso ao sistema. Aqui estão alguns métodos para solucionar problemas com o PAM.

1. **Verificação da Configuração do PAM**:
   - Verifique se os arquivos de configuração do PAM estão corretos, especialmente nos arquivos `/etc/pam.d/common-auth` e `/etc/pam.d/common-account` em Debian/Ubuntu ou `/etc/pam.d/system-auth` em Red Hat/CentOS.
   
2. **Comandos de Diagnóstico**:
   - **`pamtester`**: Uma ferramenta para testar a autenticação PAM sem a necessidade de login:
     ```bash
     sudo pamtester <serviço> <usuário> authenticate
     ```
   - Exemplo:
     ```bash
     sudo pamtester login usuario authenticate
     ```

3. **Logs do PAM**:
   - Verifique os logs do PAM em `/var/log/auth.log` ou `/var/log/secure` para mensagens de erro específicas.

#### 15.3 Troubleshooting do SSSD

O SSSD é utilizado para autenticação centralizada com LDAP, Kerberos e Active Directory. Quando o SSSD apresenta problemas, ele geralmente gera logs detalhados para análise.

1. **Verificação do Serviço SSSD**:
   - Verifique o status do serviço SSSD:
     ```bash
     sudo systemctl status sssd
     ```

2. **Logs do SSSD**:
   - Os logs do SSSD estão localizados em `/var/log/sssd/`. Os principais arquivos incluem:
     - `/var/log/sssd/sssd.log`: Logs gerais do SSSD.
     - `/var/log/sssd/sssd_<domínio>.log`: Logs específicos do domínio (como LDAP ou AD).
   - Use o `tail` para monitorar esses logs em tempo real:
     ```bash
     sudo tail -f /var/log/sssd/sssd.log
     ```

3. **Mensagens Comuns nos Logs do SSSD**:
   - **`Authentication failed`**: Falha de autenticação, geralmente devido a credenciais inválidas.
   - **`Offline authentication`**: O SSSD está em modo offline, permitindo login com cache.
   - **`Cannot contact any KDC`**: Falha na conexão com o servidor Kerberos.

#### 15.4 Troubleshooting de LDAP e Kerberos

LDAP e Kerberos são frequentemente usados em conjunto para autenticação centralizada. Problemas de conexão ou configuração nesses serviços podem impedir a autenticação de usuários.

1. **Diagnóstico de Conexão com o Servidor LDAP**:
   - Verifique a conexão com o servidor LDAP usando `ldapsearch`:
     ```bash
     ldapsearch -x -H ldap://ldap.exemplo.local -b "dc=exemplo,dc=local"
     ```
   - Isso confirma se o cliente consegue se conectar ao servidor LDAP.

2. **Verificação de Tickets Kerberos**:
   - Use `kinit` para obter um TGT e `klist` para verificar os tickets ativos:
     ```bash
     kinit usuario
     klist
     ```
   - **Erros Comuns**:
     - **`kinit: Cannot contact any KDC`**: Indica problemas de conexão com o servidor Kerberos.
     - **`kinit: KDC has no support for encryption type`**: Falha devido a configurações de encriptação incompatíveis.

#### 15.5 Exemplo Completo de Troubleshooting de Login

Aqui está um fluxo de exemplo para diagnosticar problemas de login:

1. **Verificar o Status do SSSD**:
   ```bash
   sudo systemctl status sssd
   ```

2. **Verificar Logs do Sistema**:
   - Para identificar mensagens de erro relacionadas ao PAM, SSSD, LDAP ou Kerberos:
     ```bash
     sudo tail -f /var/log/auth.log
     sudo tail -f /var/log/secure
     ```

3. **Testar a Autenticação com `pamtester`**:
   ```bash
   sudo pamtester login usuario authenticate
   ```

4. **Diagnóstico de LDAP e Kerberos**:
   - Verifique a conexão com o LDAP:
     ```bash
     ldapsearch -x -H ldap://ldap.exemplo.local -b "dc=exemplo,dc=local"
     ```
   - Verifique os tickets Kerberos:
     ```bash
     kinit usuario
     klist
     ```

#### Tabela de Referência: Ferramentas e Comandos para Troubleshooting

| Ferramenta/Comando                 | Descrição                                                             |
|------------------------------------|-----------------------------------------------------------------------|
| `pamtester`                        | Testa autenticação PAM sem login real                                 |
| `ldapsearch`                       | Diagnostica conexão com o servidor LDAP                               |
| `kinit` e `klist`                  | Gerencia e verifica tickets Kerberos                                  |
| `tail -f /var/log/auth.log`        | Monitora logs de autenticação em tempo real (Debian/Ubuntu)           |
| `tail -f /var/log/secure`          | Monitora logs de autenticação em tempo real (Red Hat/CentOS)          |

---

## 16. Práticas Recomendadas para Segurança de Autenticação

### Objetivo
Fornecer um conjunto de práticas recomendadas para fortalecer a segurança de autenticação em sistemas Linux, com foco em proteção de contas, configurações do PAM, auditoria e autenticação multifatorial (MFA).

### Conteúdo

#### 16.1 Configuração de Políticas de Senhas Seguras

Definir políticas de senha robustas é essencial para evitar acessos não autorizados. Políticas de segurança adequadas incluem requisitos de complexidade, expiração e histórico de senhas.

1. **Definindo Políticas de Senha em `/etc/login.defs`**:
   - Configure a complexidade e expiração de senhas no arquivo `/etc/login.defs`.
     ```plaintext
     PASS_MAX_DAYS   90   # Expira a senha após 90 dias
     PASS_MIN_DAYS   10   # Permite alteração a cada 10 dias
     PASS_MIN_LEN    12   # Define o comprimento mínimo para 12 caracteres
     PASS_WARN_AGE   7    # Alerta o usuário 7 dias antes da expiração
     ```

2. **Forçar Complexidade de Senhas com PAM**:
   - O módulo `pam_pwquality` permite definir regras de complexidade. Edite o arquivo `/etc/pam.d/common-password` (Debian/Ubuntu) ou `/etc/pam.d/system-auth` (Red Hat/CentOS) e adicione:
     ```plaintext
     password requisite pam_pwquality.so retry=3 minlen=12 dcredit=-1 ucredit=-1 ocredit=-1 lcredit=-1
     ```
   - **Explicação dos Parâmetros**:
     - `retry=3`: Permite 3 tentativas para definir uma senha que atenda às regras.
     - `minlen=12`: Define o comprimento mínimo.
     - `dcredit`, `ucredit`, `ocredit`, `lcredit`: Exigem ao menos um dígito, letra maiúscula, caractere especial e letra minúscula, respectivamente.

#### 16.2 Implementação de Autenticação Multifatorial (MFA)

O uso de autenticação multifatorial adiciona uma camada extra de segurança, exigindo um segundo fator (além da senha) para que o acesso seja concedido.

1. **Configuração do Google Authenticator para MFA**:
   - Instale o Google Authenticator:
     ```bash
     sudo apt install libpam-google-authenticator   # Debian/Ubuntu
     sudo yum install google-authenticator          # Red Hat/CentOS
     ```
   - Configure o Google Authenticator para cada usuário:
     ```bash
     google-authenticator
     ```
   - Edite o arquivo PAM de autenticação (como `/etc/pam.d/sshd`) e adicione:
     ```plaintext
     auth required pam_google_authenticator.so
     ```

2. **Configuração de MFA com Chaves de Segurança FIDO/U2F**:
   - O módulo `pam_u2f` permite o uso de chaves de segurança compatíveis com FIDO. Instale o módulo e siga as instruções para configurar chaves de segurança.

#### 16.3 Auditoria e Monitoramento Regular

Monitorar atividades de login e autenticação ajuda a detectar e responder a atividades suspeitas em tempo real.

1. **Ativar e Monitorar Logs de Autenticação**:
   - Verifique regularmente os arquivos de log `/var/log/auth.log` (Debian/Ubuntu) ou `/var/log/secure` (Red Hat/CentOS) para tentativas de login suspeitas.

2. **Configuração de Auditing com `auditd`**:
   - Instale e configure o `auditd` para registrar atividades relacionadas à segurança.
     ```bash
     sudo apt install auditd   # Debian/Ubuntu
     sudo yum install audit    # Red Hat/CentOS
     ```
   - Defina regras no arquivo `/etc/audit/audit.rules` para monitorar atividades específicas, como alterações no PAM:
     ```plaintext
     -w /etc/pam.d/ -p wa -k pam_changes
     ```

#### 16.4 Limitar o Acesso a Contas Sensíveis

Restringir o acesso a contas administrativas e de serviço minimiza o risco de comprometimento.

1. **Restringir Acesso ao `sudo`**:
   - Controle o acesso ao `sudo` definindo grupos de administradores. Edite o arquivo `/etc/sudoers` para garantir que apenas usuários específicos tenham privilégios elevados.
     ```plaintext
     %admin ALL=(ALL) ALL
     ```

2. **Configuração de Login Seguro para o SSH**:
   - Desative o login direto de root e restrinja o acesso via SSH a usuários específicos.
     ```plaintext
     PermitRootLogin no
     AllowUsers usuario1 usuario2
     ```

#### 16.5 Práticas Adicionais para Segurança

1. **Políticas de Expiração e Bloqueio de Conta**:
   - Use o `chage` para definir expiração e bloqueio em contas de usuário, reforçando as políticas de segurança:
     ```bash
     sudo chage -E <data_expiração> <nome_do_usuário>
     ```

2. **Desativar Contas Inativas**:
   - Desabilite contas que não são usadas regularmente para reduzir a superfície de ataque:
     ```bash
     sudo usermod -L <nome_do_usuário>
     ```

#### Tabela de Referência: Práticas de Segurança

| Prática                           | Descrição                                                                                  |
|-----------------------------------|--------------------------------------------------------------------------------------------|
| `PASS_MAX_DAYS`, `PASS_MIN_LEN`   | Define a expiração e comprimento mínimo de senhas                                          |
| `pam_pwquality`                   | Exige complexidade mínima para senhas                                                      |
| `pam_google_authenticator`        | Implementa autenticação multifatorial com Google Authenticator                             |
| `auditd`                          | Monitora atividades de segurança no sistema                                                |
| `AllowUsers` no SSH               | Restringe o login SSH a usuários específicos                                               |
| `chage -E`                        | Define uma data de expiração para contas de usuário                                        |
| `usermod -L`                      | Bloqueia uma conta, impedindo o login                                                      |

---

#### **17. Automação de Configuração de Autenticação com Ansible**

---

### 17.1 Introdução ao Uso do Ansible para Configuração de Autenticação

#### Objetivo
Explicar os benefícios de usar o Ansible para automatizar configurações de autenticação e fornecer uma visão geral dos módulos Ansible mais relevantes para gerenciamento de usuários, políticas de segurança e autenticação centralizada.

#### Conteúdo

#### 17.1.1 Benefícios da Automação com Ansible

O Ansible oferece uma abordagem eficiente e escalável para gerenciar configurações de autenticação em múltiplos servidores, especialmente em ambientes corporativos. Com Ansible, é possível aplicar configurações de segurança de forma consistente, reduzir erros manuais e garantir que todos os servidores sigam as mesmas políticas e diretrizes de autenticação. Principais vantagens incluem:

1. **Consistência**: Configurações de autenticação e segurança são aplicadas uniformemente, eliminando diferenças entre servidores.
2. **Escalabilidade**: Gerencia a autenticação em centenas de servidores com o mesmo esforço que seria necessário para um único servidor.
3. **Automação de Tarefas Repetitivas**: Playbooks automatizam tarefas recorrentes, como criação de usuários, aplicação de políticas PAM e configuração de SSSD.
4. **Auditoria e Compliance**: Facilita a auditoria e compliance ao aplicar configurações padronizadas em todos os servidores.

#### 17.1.2 Módulos Ansible Relevantes para Configuração de Autenticação

O Ansible oferece vários módulos úteis para gerenciar autenticação, criação de contas e configuração de políticas. Abaixo estão os principais módulos que ajudam na configuração de autenticação e segurança:

1. **Módulo `user`**:
   - Gerencia a criação e modificação de usuários, definindo atributos como senha, diretório home e grupo.
   - Exemplo:
     ```yaml
     - name: Cria um novo usuário
       ansible.builtin.user:
         name: "usuario"
         password: "{% raw %}{{ 'senha123' | password_hash('sha512') }}{% endraw %}"
         state: present
     ```

2. **Módulo `pam_limits`**:
   - Define políticas PAM para controle de limites de sessão, como número máximo de processos por usuário e uso de memória.
   - Exemplo:
     ```yaml
     - name: Define limite máximo de processos para o usuário
       community.general.pam_limits:
         domain: "usuario"
         limit_type: "soft"
         item: "nproc"
         value: "1024"
     ```

3. **Módulo `community.general.ldap_attr`**:
   - Gerencia atributos LDAP em servidores de diretório, útil para configurar autenticação centralizada e permissões.
   - Exemplo:
     ```yaml
     - name: Configura um atributo no servidor LDAP
       community.general.ldap_attr:
         dn: "uid=usuario,ou=people,dc=exemplo,dc=local"
         server_uri: "ldap://ldap.exemplo.local"
         bind_dn: "cn=admin,dc=exemplo,dc=local"
         bind_pw: "admin_password"
         attributes:
           mail: "usuario@exemplo.com"
     ```

Esses módulos serão fundamentais para as subseções seguintes, onde desenvolveremos playbooks para automatizar configurações de autenticação local, LDAP, Kerberos, FreeIPA e autenticação multifatorial.

### 17.2 Automação de Configuração de Autenticação Local e Políticas PAM

#### Objetivo
Demonstrar como usar o Ansible para configurar autenticação local em múltiplos servidores, incluindo a criação de contas de usuários, definição de políticas de senha e restrições de autenticação com o PAM. Essa automação simplifica a configuração e aplicação consistente de políticas de segurança em ambientes corporativos.

#### Conteúdo

#### 17.2.1 Playbooks para Configuração de Autenticação Local e Políticas de Senha

1. **Criação de Contas de Usuários com Políticas de Senha Específicas**:
   - O módulo `user` permite criar contas de usuários e aplicar políticas de senha, garantindo que todos os servidores tenham as mesmas contas de usuários com as mesmas restrições.

   - Exemplo de playbook para criar uma conta de usuário com uma senha criptografada:
     ```yaml
     - name: Configuração de autenticação local
       hosts: all
       become: yes
       tasks:
         - name: Cria uma conta de usuário com senha criptografada
           ansible.builtin.user:
             name: "usuario"
             password: "{% raw %}{{ 'senha123' | password_hash('sha512') }}{% endraw %}"
             state: present
             groups: "sudo"
     ```

2. **Aplicação de Políticas de Senha com o PAM**:
   - O módulo `pam_limits` é usado para definir restrições de uso de recursos e o módulo `lineinfile` pode configurar políticas de senha diretamente no PAM.

   - Exemplo de playbook para aplicar políticas de senha com o PAM:
     ```yaml
     - name: Configuração de políticas de senha PAM
       hosts: all
       become: yes
       tasks:
         - name: Define o comprimento mínimo de senha no PAM
           ansible.builtin.lineinfile:
             path: "/etc/security/pwquality.conf"
             regexp: "^minlen"
             line: "minlen = 12"
             create: yes

         - name: Define a quantidade mínima de caracteres especiais na senha
           ansible.builtin.lineinfile:
             path: "/etc/security/pwquality.conf"
             regexp: "^ocredit"
             line: "ocredit = -1"
             create: yes
     ```

#### 17.2.2 Exemplo Prático: Playbook para Configuração de Restrições PAM

Este exemplo de playbook configura a autenticação local e define políticas de segurança com o PAM, aplicando restrições de uso de senha, bloqueio de conta e controle de acesso.

- **Playbook para Configuração PAM**:
   ```yaml
   - name: Configuração de autenticação local e restrições PAM
     hosts: all
     become: yes
     tasks:
       - name: Configura bloqueio de conta após 5 tentativas falhas
         ansible.builtin.lineinfile:
           path: "/etc/pam.d/common-auth"
           regexp: "^auth required pam_tally2.so"
           line: "auth required pam_tally2.so deny=5 unlock_time=600 onerr=fail audit"
           create: yes

       - name: Define limites de uso de recursos para o usuário
         community.general.pam_limits:
           domain: "usuario"
           limit_type: "soft"
           item: "nproc"
           value: "512"
   ```

#### 17.2.3 Aplicação e Verificação

1. **Executar o Playbook**:
   - Para aplicar essa configuração em múltiplos servidores, execute o playbook:
     ```bash
     ansible-playbook -i hosts configuracao_autenticacao_local.yaml
     ```

2. **Verificar Configuração no Servidor**:
   - Após a execução, verifique a configuração do PAM em `/etc/pam.d/common-auth` e em `/etc/security/pwquality.conf` para garantir que as políticas foram aplicadas.

Com essa automação, é possível configurar autenticação local e políticas de senha de forma consistente e rápida em todos os servidores do ambiente. 

### 17.3 Configuração Automatizada de Clientes LDAP com Ansible

#### Objetivo
Automatizar a configuração de clientes LDAP em múltiplos servidores Linux usando Ansible, incluindo a instalação de pacotes necessários, configuração dos arquivos LDAP (`/etc/ldap/ldap.conf` e `/etc/nslcd.conf`), e ajustes do SSSD para autenticação centralizada via LDAP.

#### Conteúdo

#### 17.3.1 Playbook para Instalação e Configuração do Cliente LDAP

1. **Instalação dos Pacotes de Cliente LDAP e SSSD**:
   - Esse playbook instala os pacotes necessários para que o cliente se conecte ao servidor LDAP e configure o SSSD para gerenciamento centralizado de autenticação.

   - Exemplo de playbook:
     ```yaml
     - name: Instalação de pacotes LDAP e SSSD
       hosts: all
       become: yes
       tasks:
         - name: Instala o pacote de cliente LDAP e SSSD
           ansible.builtin.package:
             name: "{% raw %}{{ item }}{% endraw %}"
             state: present
           loop:
             - ldap-utils
             - sssd
             - libnss-ldapd
     ```

2. **Configuração do Arquivo `/etc/ldap/ldap.conf`**:
   - Configura o arquivo `ldap.conf` para definir o servidor LDAP, a base de pesquisa e outras opções de autenticação.

   - Exemplo de configuração:
     ```yaml
     - name: Configuração do arquivo /etc/ldap/ldap.conf
       hosts: all
       become: yes
       tasks:
         - name: Define configurações de servidor LDAP
           ansible.builtin.blockinfile:
             path: "/etc/ldap/ldap.conf"
             block: |
               BASE dc=exemplo,dc=local
               URI ldap://ldap.exemplo.local
               TLS_CACERT /etc/ssl/certs/ca-certificates.crt
     ```

3. **Configuração do Arquivo `/etc/nslcd.conf`**:
   - O arquivo `nslcd.conf` define detalhes para conectar ao servidor LDAP, como o base DN, URI e DN de administração.

   - Exemplo de playbook para configuração do `nslcd.conf`:
     ```yaml
     - name: Configuração do arquivo /etc/nslcd.conf
       hosts: all
       become: yes
       tasks:
         - name: Define o servidor e base de pesquisa no nslcd.conf
           ansible.builtin.blockinfile:
             path: "/etc/nslcd.conf"
             block: |
               uri ldap://ldap.exemplo.local
               base dc=exemplo,dc=local
               binddn cn=admin,dc=exemplo,dc=local
               bindpw senha_do_admin
               ssl on
               tls_reqcert allow
     ```

4. **Configuração do SSSD para LDAP**:
   - Configura o `sssd.conf` para utilizar LDAP como provedor de identidade e autenticação, aplicando o cache de credenciais e o fallback de diretório home para usuários.

   - Exemplo:
     ```yaml
     - name: Configuração do SSSD para autenticação LDAP
       hosts: all
       become: yes
       tasks:
         - name: Define configurações no sssd.conf
           ansible.builtin.blockinfile:
             path: "/etc/sssd/sssd.conf"
             block: |
               [sssd]
               config_file_version = 2
               services = nss, pam
               domains = exemplo.local

               [domain/exemplo.local]
               id_provider = ldap
               auth_provider = ldap
               ldap_uri = ldap://ldap.exemplo.local
               ldap_search_base = dc=exemplo,dc=local
               cache_credentials = true
               fallback_homedir = /home/%u@%d
               ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt
           mode: "0600"
           owner: root
           group: root
     ```

#### 17.3.2 Exemplo Prático: Playbook Completo para Configuração do Cliente LDAP

Este exemplo reúne todos os passos em um único playbook para automatizar a instalação e configuração de clientes LDAP.

```yaml
- name: Configuração completa do cliente LDAP com Ansible
  hosts: all
  become: yes
  tasks:
    - name: Instala pacotes necessários para LDAP e SSSD
      ansible.builtin.package:
        name: "{% raw %}{{ item }}{% endraw %}"
        state: present
      loop:
        - ldap-utils
        - sssd
        - libnss-ldapd

    - name: Configura /etc/ldap/ldap.conf
      ansible.builtin.blockinfile:
        path: "/etc/ldap/ldap.conf"
        block: |
          BASE dc=exemplo,dc=local
          URI ldap://ldap.exemplo.local
          TLS_CACERT /etc/ssl/certs/ca-certificates.crt

    - name: Configura /etc/nslcd.conf
      ansible.builtin.blockinfile:
        path: "/etc/nslcd.conf"
        block: |
          uri ldap://ldap.exemplo.local
          base dc=exemplo,dc=local
          binddn cn=admin,dc=exemplo,dc=local
          bindpw senha_do_admin
          ssl on
          tls_reqcert allow

    - name: Configura /etc/sssd/sssd.conf para autenticação LDAP
      ansible.builtin.blockinfile:
        path: "/etc/sssd/sssd.conf"
        block: |
          [sssd]
          config_file_version = 2
          services = nss, pam
          domains = exemplo.local

          [domain/exemplo.local]
          id_provider = ldap
          auth_provider = ldap
          ldap_uri = ldap://ldap.exemplo.local
          ldap_search_base = dc=exemplo,dc=local
          cache_credentials = true
          fallback_homedir = /home/%u@%d
          ldap_tls_cacert = /etc/ssl/certs/ca-certificates.crt
        mode: "0600"
        owner: root
        group: root
```

#### 17.3.3 Aplicação do Playbook e Verificação

1. **Executar o Playbook**:
   - Para aplicar a configuração em múltiplos servidores, execute:
     ```bash
     ansible-playbook -i hosts configuracao_cliente_ldap.yaml
     ```

2. **Verificação da Configuração LDAP**:
   - Após a execução, verifique a conectividade com o servidor LDAP usando `ldapsearch`:
     ```bash
     ldapsearch -x -H ldap://ldap.exemplo.local -b "dc=exemplo,dc=local"
     ```

Com esse playbook, é possível configurar automaticamente clientes LDAP em todos os servidores, garantindo autenticação centralizada e consistência em ambientes corporativos.

### 17.4 Configuração de Kerberos com Ansible para Autenticação Segura

#### Objetivo
Automatizar a configuração de clientes **Kerberos** em múltiplos servidores Linux usando Ansible. Isso inclui a configuração do arquivo `krb5.conf`, instalação de pacotes necessários e criação de chave para o cliente no servidor KDC, permitindo autenticação segura com tickets Kerberos em ambientes centralizados.

#### Conteúdo

#### 17.4.1 Playbook para Instalação e Configuração do Cliente Kerberos

1. **Instalação dos Pacotes de Cliente Kerberos**:
   - Esse playbook instala os pacotes Kerberos necessários para que o cliente possa obter e gerenciar tickets de autenticação de um KDC.

   - Exemplo de playbook:
     ```yaml
     - name: Instalação de pacotes de cliente Kerberos
       hosts: all
       become: yes
       tasks:
         - name: Instala o pacote de cliente Kerberos
           ansible.builtin.package:
             name: "{% raw %}{{ item }}{% endraw %}"
             state: present
           loop:
             - krb5-user       # Debian/Ubuntu
             - krb5-workstation # Red Hat/CentOS
     ```

2. **Configuração do Arquivo `/etc/krb5.conf`**:
   - O arquivo `krb5.conf` define o realm e os servidores KDC para autenticação Kerberos. Esse playbook configura o arquivo com os detalhes do domínio e do KDC.

   - Exemplo de configuração:
     ```yaml
     - name: Configuração do arquivo /etc/krb5.conf
       hosts: all
       become: yes
       tasks:
         - name: Define configurações do Kerberos no krb5.conf
           ansible.builtin.blockinfile:
             path: "/etc/krb5.conf"
             block: |
               [libdefaults]
               default_realm = EXEMPLO.LOCAL
               dns_lookup_kdc = true
               dns_lookup_realm = false

               [realms]
               EXEMPLO.LOCAL = {
                   kdc = kdc.exemplo.local
                   admin_server = kdc.exemplo.local
               }

               [domain_realm]
               .exemplo.local = EXEMPLO.LOCAL
               exemplo.local = EXEMPLO.LOCAL
     ```

3. **Configuração Automática de Chaves com `kadmin`**:
   - Após configurar o cliente, é necessário criar uma chave para o cliente no KDC. O Ansible pode automatizar essa criação usando o comando `kadmin`.

   - Exemplo de playbook para registrar o cliente no KDC:
     ```yaml
     - name: Registro do cliente no KDC
       hosts: all
       become: yes
       tasks:
         - name: Cria principal para o cliente no KDC
           ansible.builtin.shell: |
             echo "addprinc -randkey host/{% raw %}{{ ansible_hostname }}{% endraw %}@EXEMPLO.LOCAL" | kadmin -p admin@EXEMPLO.LOCAL -w senha_admin
           args:
             executable: /bin/bash
     ```

#### 17.4.2 Exemplo Prático: Playbook Completo para Configuração do Cliente Kerberos

Este exemplo de playbook integra todos os passos para instalação e configuração completa do cliente Kerberos, permitindo autenticação segura com Kerberos em múltiplos servidores.

```yaml
- name: Configuração completa do cliente Kerberos com Ansible
  hosts: all
  become: yes
  tasks:
    - name: Instala pacotes necessários para Kerberos
      ansible.builtin.package:
        name: "{% raw %}{{ item }}{% endraw %}"
        state: present
      loop:
        - krb5-user
        - krb5-config
        - krb5-workstation

    - name: Configura /etc/krb5.conf com detalhes do KDC
      ansible.builtin.blockinfile:
        path: "/etc/krb5.conf"
        block: |
          [libdefaults]
          default_realm = EXEMPLO.LOCAL
          dns_lookup_kdc = true
          dns_lookup_realm = false

          [realms]
          EXEMPLO.LOCAL = {
              kdc = kdc.exemplo.local
              admin_server = kdc.exemplo.local
          }

          [domain_realm]
          .exemplo.local = EXEMPLO.LOCAL
          exemplo.local = EXEMPLO.LOCAL

    - name: Registra o cliente no KDC criando principal
      ansible.builtin.shell: |
        echo "addprinc -randkey host/{% raw %}{{ ansible_hostname }}{% endraw %}@EXEMPLO.LOCAL" | kadmin -p admin@EXEMPLO.LOCAL -w senha_admin
      args:
        executable: /bin/bash
```

#### 17.4.3 Aplicação do Playbook e Verificação

1. **Executar o Playbook**:
   - Para aplicar a configuração de clientes Kerberos em múltiplos servidores, execute o playbook:
     ```bash
     ansible-playbook -i hosts configuracao_cliente_kerberos.yaml
     ```

2. **Verificar Configuração e Obtenção de Tickets**:
   - Após a execução, verifique se o cliente Kerberos foi configurado corretamente obtendo um TGT com `kinit`:
     ```bash
     kinit usuario@EXEMPLO.LOCAL
     klist
     ```
   - Esses comandos devem mostrar o ticket TGT, confirmando a autenticação com o KDC.

Esse playbook permite a configuração e integração de servidores Linux ao Kerberos, fornecendo uma autenticação segura baseada em tickets e simplificando o gerenciamento centralizado de usuários.

### 17.5 Integração Automatizada com FreeIPA Usando Ansible

#### Objetivo
Automatizar a integração de clientes Linux ao **FreeIPA** usando Ansible, incluindo a instalação do cliente FreeIPA, registro do servidor no domínio, e configuração do SSSD para autenticação centralizada. A automação dessa configuração permite que múltiplos servidores sejam adicionados ao domínio FreeIPA de forma rápida e consistente.

#### Conteúdo

#### 17.5.1 Playbook para Instalação e Configuração do Cliente FreeIPA

1. **Instalação do Cliente FreeIPA**:
   - Este playbook instala o pacote de cliente FreeIPA, essencial para que o servidor se comunique com o domínio FreeIPA e realize a autenticação centralizada.

   - Exemplo de playbook para instalação do cliente FreeIPA:
     ```yaml
     - name: Instalação do cliente FreeIPA
       hosts: all
       become: yes
       tasks:
         - name: Instala o pacote de cliente FreeIPA
           ansible.builtin.package:
             name: ipa-client
             state: present
     ```

2. **Configuração e Registro no Domínio FreeIPA com `ipa-client-install`**:
   - O comando `ipa-client-install` automatiza o registro do servidor no domínio FreeIPA. O Ansible permite que esse comando seja executado com as opções e parâmetros necessários para conectar o servidor ao domínio e configurar o SSSD.

   - Exemplo de playbook:
     ```yaml
     - name: Configuração e registro do cliente no domínio FreeIPA
       hosts: all
       become: yes
       tasks:
         - name: Registra o cliente no domínio FreeIPA
           ansible.builtin.command:
             cmd: ipa-client-install --domain=exemplo.local --server=ipa.exemplo.local --realm=EXEMPLO.LOCAL --principal=admin --password=senha_admin --unattended
           args:
             creates: /etc/ipa/default.conf
     ```

3. **Configuração do SSSD para FreeIPA**:
   - Durante a instalação do cliente, o FreeIPA configura automaticamente o SSSD para gerenciar autenticação. No entanto, configurações adicionais podem ser aplicadas ao `sssd.conf` para ajustar detalhes, como cache de credenciais e diretório home dos usuários.

   - Exemplo de configuração extra:
     ```yaml
     - name: Configuração extra do SSSD para FreeIPA
       hosts: all
       become: yes
       tasks:
         - name: Define configurações no sssd.conf para cache de credenciais
           ansible.builtin.lineinfile:
             path: "/etc/sssd/sssd.conf"
             regexp: "^cache_credentials"
             line: "cache_credentials = true"
           notify: Restart SSSD

     handlers:
       - name: Restart SSSD
         ansible.builtin.service:
           name: sssd
           state: restarted
     ```

#### 17.5.2 Exemplo Prático: Playbook Completo para Configuração do Cliente FreeIPA

Este playbook integra todos os passos necessários para configurar e registrar automaticamente servidores no domínio FreeIPA e ajustar as configurações do SSSD para autenticação centralizada.

```yaml
- name: Configuração completa do cliente FreeIPA com Ansible
  hosts: all
  become: yes
  tasks:
    - name: Instala o cliente FreeIPA
      ansible.builtin.package:
        name: ipa-client
        state: present

    - name: Registra o cliente no domínio FreeIPA
      ansible.builtin.command:
        cmd: ipa-client-install --domain=exemplo.local --server=ipa.exemplo.local --realm=EXEMPLO.LOCAL --principal=admin --password=senha_admin --unattended
      args:
        creates: /etc/ipa/default.conf

    - name: Configuração adicional do SSSD para cache de credenciais
      ansible.builtin.lineinfile:
        path: "/etc/sssd/sssd.conf"
        regexp: "^cache_credentials"
        line: "cache_credentials = true"
      notify: Restart SSSD

  handlers:
    - name: Restart SSSD
      ansible.builtin.service:
        name: sssd
        state: restarted
```

#### 17.5.3 Aplicação do Playbook e Verificação

1. **Executar o Playbook**:
   - Para adicionar múltiplos servidores ao domínio FreeIPA, execute o playbook:
     ```bash
     ansible-playbook -i hosts configuracao_cliente_freeipa.yaml
     ```

2. **Verificação de Integração com o FreeIPA**:
   - Verifique se o cliente está registrado no FreeIPA usando o comando `ipa` para listar informações do domínio:
     ```bash
     ipa host-show $(hostname)
     ```

Com esse playbook, é possível automatizar a integração de múltiplos servidores ao domínio FreeIPA, simplificando o processo de autenticação centralizada e garantindo consistência nas políticas.

### 17.6 Configuração de Autenticação Multifatorial com Ansible e PAM

#### Objetivo
Automatizar a configuração de **autenticação multifatorial (MFA)** em servidores Linux usando Ansible, incluindo a instalação e configuração do **Google Authenticator** ou **chaves de segurança U2F** (Universal 2nd Factor) com `pam_u2f`. Essa automação permite adicionar uma camada extra de segurança de forma consistente em múltiplos servidores.

#### Conteúdo

#### 17.6.1 Configuração Automática do Google Authenticator com Ansible

1. **Instalação do Google Authenticator**:
   - O Google Authenticator oferece uma forma prática de MFA, gerando códigos temporários (TOTP) para login. Este playbook instala o pacote do Google Authenticator para configurar MFA em servidores Linux.

   - Exemplo de playbook para instalar o Google Authenticator:
     ```yaml
     - name: Instalação do Google Authenticator
       hosts: all
       become: yes
       tasks:
         - name: Instala o pacote libpam-google-authenticator
           ansible.builtin.package:
             name: libpam-google-authenticator
             state: present
     ```

2. **Configuração do Google Authenticator para Usuários**:
   - O comando `google-authenticator` deve ser executado para cada usuário que utilizará MFA. Isso pode ser automatizado para usuários específicos ou gerenciado por grupo.

   - Exemplo de configuração:
     ```yaml
     - name: Configuração do Google Authenticator para um usuário
       hosts: all
       become: yes
       tasks:
         - name: Configura o Google Authenticator para o usuário
           ansible.builtin.command:
             cmd: google-authenticator --time-based --rate-limit=3 --rate-time=30 --force --no-confirm
           become_user: "usuario_exemplo"
     ```

3. **Configuração do PAM para Exigir MFA**:
   - Para ativar o Google Authenticator no PAM, adicione a linha de configuração ao arquivo PAM (ex: `/etc/pam.d/sshd` para acesso SSH).

   - Exemplo de playbook para configurar o PAM com Google Authenticator:
     ```yaml
     - name: Configuração do PAM para Google Authenticator
       hosts: all
       become: yes
       tasks:
         - name: Adiciona o Google Authenticator ao PAM SSH
           ansible.builtin.lineinfile:
             path: "/etc/pam.d/sshd"
             line: "auth required pam_google_authenticator.so"
             create: yes
     ```

#### 17.6.2 Configuração de Autenticação U2F com `pam_u2f`

Para organizações que utilizam chaves de segurança U2F (como YubiKey), o `pam_u2f` permite autenticação multifatorial com um segundo fator físico.

1. **Instalação do Módulo `pam_u2f`**:
   - Este playbook instala o `pam_u2f`, que gerencia autenticação com dispositivos U2F.

   - Exemplo de playbook:
     ```yaml
     - name: Instalação do pam_u2f
       hosts: all
       become: yes
       tasks:
         - name: Instala o pacote libpam-u2f
           ansible.builtin.package:
             name: libpam-u2f
             state: present
     ```

2. **Configuração do U2F para Usuários**:
   - As chaves U2F precisam ser registradas para cada usuário. Este processo configura o `pam_u2f` para exigir a autenticação via chave.

   - Exemplo de playbook:
     ```yaml
     - name: Configuração de U2F para autenticação PAM
       hosts: all
       become: yes
       tasks:
         - name: Configura o arquivo .config/Yubico/u2f_keys para o usuário
           ansible.builtin.file:
             path: "/home/usuario_exemplo/.config/Yubico/u2f_keys"
             state: touch
             mode: "0600"
           become_user: "usuario_exemplo"

         - name: Adiciona a chave U2F ao PAM SSH
           ansible.builtin.lineinfile:
             path: "/etc/pam.d/sshd"
             line: "auth required pam_u2f.so"
             create: yes
     ```

#### 17.6.3 Exemplo Prático: Playbook Completo para Configuração de Autenticação Multifatorial

Este playbook inclui a instalação e configuração do Google Authenticator e U2F com o PAM, automatizando o processo de configuração de MFA em todos os servidores.

```yaml
- name: Configuração de MFA com Ansible (Google Authenticator e U2F)
  hosts: all
  become: yes
  tasks:
    - name: Instala Google Authenticator e pam_u2f
      ansible.builtin.package:
        name: "{% raw %}{{ item }}{% endraw %}"
        state: present
      loop:
        - libpam-google-authenticator
        - libpam-u2f

    - name: Configura Google Authenticator para o usuário especificado
      ansible.builtin.command:
        cmd: google-authenticator --time-based --rate-limit=3 --rate-time=30 --force --no-confirm
      become_user: "usuario_exemplo"

    - name: Configura chave U2F para o usuário especificado
      ansible.builtin.file:
        path: "/home/usuario_exemplo/.config/Yubico/u2f_keys"
        state: touch
        mode: "0600"
      become_user: "usuario_exemplo"

    - name: Configura PAM para Google Authenticator e U2F no SSH
      ansible.builtin.blockinfile:
        path: "/etc/pam.d/sshd"
        block: |
          auth required pam_google_authenticator.so
          auth required pam_u2f.so
```

#### 17.6.4 Aplicação do Playbook e Verificação

1. **Executar o Playbook**:
   - Para aplicar a configuração de autenticação multifatorial em múltiplos servidores:
     ```bash
     ansible-playbook -i hosts configuracao_mfa.yaml
     ```

2. **Verificar MFA para o Usuário**:
   - Teste o login para o usuário configurado e confirme que a autenticação multifatorial está ativa, solicitando o segundo fator via Google Authenticator ou U2F.

Esse playbook permite configurar o MFA com o Google Authenticator ou U2F de maneira automatizada, aumentando a segurança de autenticação em todos os servidores.

### 17.7 Monitoramento e Auditoria com Ansible

#### Objetivo
Automatizar o monitoramento e auditoria de autenticação em servidores Linux usando Ansible, configurando logs de autenticação, regras de auditoria e ajustes no `auditd` para rastrear atividades de login e tentativas de acesso. Essa automação facilita o gerenciamento de conformidade e segurança em ambientes com múltiplos servidores.

#### Conteúdo

#### 17.7.1 Configuração de Logs de Autenticação

Para garantir o registro detalhado de eventos de autenticação, configuramos os arquivos de log de autenticação padrão e ajustamos a retenção e a rotação de logs.

1. **Configuração de Logs de Autenticação com Ansible**:
   - Este playbook configura o sistema para registrar tentativas de login em `/var/log/auth.log` (Debian/Ubuntu) ou `/var/log/secure` (Red Hat/CentOS).

   - Exemplo de playbook:
     ```yaml
     - name: Configuração de logs de autenticação
       hosts: all
       become: yes
       tasks:
         - name: Configura o rsyslog para registrar logs de autenticação detalhados
           ansible.builtin.lineinfile:
             path: "/etc/rsyslog.conf"
             regexp: "^authpriv.*"
             line: "authpriv.* /var/log/auth.log"
             create: yes
     ```

2. **Configuração de Rotação de Logs**:
   - A rotação de logs ajuda a gerenciar o armazenamento, arquivando automaticamente os logs de autenticação após um período específico.

   - Exemplo de configuração com Ansible:
     ```yaml
     - name: Configuração de rotação de logs de autenticação
       hosts: all
       become: yes
       tasks:
         - name: Define política de rotação para o arquivo auth.log
           ansible.builtin.copy:
             dest: "/etc/logrotate.d/auth"
             content: |
               /var/log/auth.log {
                   weekly
                   rotate 4
                   compress
                   missingok
                   notifempty
               }
     ```

#### 17.7.2 Configuração de Auditoria com `auditd`

O `auditd` permite monitorar atividades específicas, como tentativas de login e alterações nos arquivos de configuração de autenticação. Com Ansible, podemos configurar regras de auditoria e garantir o monitoramento detalhado de ações sensíveis.

1. **Instalação do `auditd` e Configuração Básica**:
   - Este playbook instala o `auditd` e define políticas de auditoria para monitorar atividades relacionadas à autenticação.

   - Exemplo de playbook:
     ```yaml
     - name: Instalação e configuração básica do auditd
       hosts: all
       become: yes
       tasks:
         - name: Instala o pacote auditd
           ansible.builtin.package:
             name: auditd
             state: present

         - name: Inicia e habilita o serviço auditd
           ansible.builtin.service:
             name: auditd
             state: started
             enabled: yes
     ```

2. **Definição de Regras de Auditoria para Autenticação**:
   - Configure o `auditd` para registrar alterações nos arquivos do PAM, tentativas de login e modificações de políticas.

   - Exemplo de configuração de regras de auditoria:
     ```yaml
     - name: Configuração de regras de auditoria para autenticação
       hosts: all
       become: yes
       tasks:
         - name: Adiciona regras de auditoria para monitorar mudanças no PAM
           ansible.builtin.blockinfile:
             path: "/etc/audit/rules.d/audit.rules"
             block: |
               -w /etc/pam.d/ -p wa -k pam_changes
               -w /etc/sssd/sssd.conf -p wa -k sssd_changes
               -w /var/log/auth.log -p r -k auth_log
           notify: Restart auditd

     handlers:
       - name: Restart auditd
         ansible.builtin.service:
           name: auditd
           state: restarted
     ```

#### 17.7.3 Exemplo Prático: Playbook Completo para Configuração de Monitoramento e Auditoria

Este playbook inclui a configuração completa de logs de autenticação, rotatividade de logs e regras de auditoria, permitindo monitorar e auditar atividades de autenticação de forma automatizada.

```yaml
- name: Configuração de monitoramento e auditoria de autenticação
  hosts: all
  become: yes
  tasks:
    - name: Configura rsyslog para registrar logs de autenticação detalhados
      ansible.builtin.lineinfile:
        path: "/etc/rsyslog.conf"
        regexp: "^authpriv.*"
        line: "authpriv.* /var/log/auth.log"
        create: yes

    - name: Define política de rotação para auth.log
      ansible.builtin.copy:
        dest: "/etc/logrotate.d/auth"
        content: |
          /var/log/auth.log {
              weekly
              rotate 4
              compress
              missingok
              notifempty
          }

    - name: Instala e configura o auditd
      ansible.builtin.package:
        name: auditd
        state: present

    - name: Inicia e habilita o auditd
      ansible.builtin.service:
        name: auditd
        state: started
        enabled: yes

    - name: Configura regras de auditoria para monitoramento de autenticação
      ansible.builtin.blockinfile:
        path: "/etc/audit/rules.d/audit.rules"
        block: |
          -w /etc/pam.d/ -p wa -k pam_changes
          -w /etc/sssd/sssd.conf -p wa -k sssd_changes
          -w /var/log/auth.log -p r -k auth_log
      notify: Restart auditd

  handlers:
    - name: Restart auditd
      ansible.builtin.service:
        name: auditd
        state: restarted
```

#### 17.7.4 Aplicação do Playbook e Verificação

1. **Executar o Playbook**:
   - Para aplicar o monitoramento e auditoria em múltiplos servidores, execute:
     ```bash
     ansible-playbook -i hosts configuracao_auditoria.yaml
     ```

2. **Verificação dos Logs e Eventos de Auditoria**:
   - Verifique os logs de autenticação em `/var/log/auth.log` e os eventos de auditoria com o comando:
     ```bash
     ausearch -k pam_changes
     ausearch -k auth_log
     ```

Esse playbook garante que as atividades de autenticação e alterações em arquivos de configuração de autenticação sejam monitoradas em tempo real, fornecendo uma trilha de auditoria robusta e automatizada para o ambiente.

--- 

### Conclusão

Neste tutorial, exploramos a configuração de autenticação em sistemas Linux desde práticas básicas até configurações avançadas e automação com Ansible. Cobrir métodos de autenticação local, centralizada com LDAP, Kerberos, e FreeIPA, além da integração de multifator (MFA) e auditoria, fornece uma base robusta para implementar e manter autenticação segura em ambientes corporativos.

A automação com Ansible se mostrou uma ferramenta poderosa para garantir consistência e eficiência na gestão de autenticação em múltiplos servidores. Com a criação de playbooks, abordamos como configurar e aplicar políticas de senha, bloquear contas após tentativas falhas, implementar MFA com Google Authenticator ou chaves U2F, e monitorar atividades de autenticação por meio de `auditd` e logs centralizados. Ao automatizar essas práticas, você obtém um ambiente de autenticação escalável e seguro, simplificando a manutenção de políticas de conformidade e segurança.

A implementação de métodos de autenticação robustos, em conjunto com auditoria e monitoramento, é essencial para proteger dados e usuários em ambientes corporativos. Com este guia, você possui as ferramentas para garantir um gerenciamento eficiente e seguro da autenticação, aprimorando a proteção de sua infraestrutura e promovendo um ambiente controlado e confiável.

--- 
