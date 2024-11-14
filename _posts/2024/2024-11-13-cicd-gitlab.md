---
layout: post
title: "Implementando CI/CD e Pipelines com GitLab: Tutorial Completo"
date: 2024-11-13 23:01:02 -0300
categories: [DevOps, CI/CD, GitLab]
tags: [GitLab, CI/CD, DevOps, Automação, Docker, Kubernetes]
---

### Introdução

Com a crescente necessidade de entregas ágeis e frequentes, as práticas de **CI/CD** (Integração Contínua e Entrega/Implantação Contínua) tornaram-se fundamentais para equipes de desenvolvimento que desejam manter a qualidade e a eficiência na entrega de software. O **GitLab CI/CD** é uma das soluções mais poderosas e completas disponíveis atualmente, oferecendo uma integração fluida com o repositório de código e uma ampla gama de funcionalidades para automatizar testes, builds, deploys e monitoramento.

Este tutorial explora, passo a passo, o uso do GitLab CI/CD, desde a configuração básica até práticas avançadas, como integração com Docker e Kubernetes, deploys automatizados e seguros, e monitoramento com ferramentas como Prometheus e Grafana. Ao final, você terá uma visão completa de como construir e gerenciar pipelines que atendem tanto a projetos simples quanto a arquiteturas de grande escala, com foco em segurança, controle e eficiência.

---

### Tabela de Conteúdos
1. [Introdução ao CI/CD e GitLab Pipelines](#1-introdução-ao-cicd-e-gitlab-pipelines)
2. [Configuração Inicial do GitLab CI/CD](#2-configuração-inicial-do-gitlab-cicd)
3. [Estrutura do Arquivo `.gitlab-ci.yml`](#3-estrutura-do-arquivo-gitlab-ciyml)
4. [Configuração e Registro do GitLab Runner](#4-configuração-e-registro-do-gitlab-runner)
5. [Criando Jobs e Stages no Pipeline](#5-criando-jobs-e-stages-no-gitlab-pipeline)
6. [Uso de Variáveis de Ambiente](#6-uso-de-variáveis-de-ambiente-no-gitlab-cicd)
7. [Artefatos e Cache no Pipeline](#7-artefatos-e-cache-no-gitlab-pipeline)
8. [Testes Automatizados com GitLab CI/CD](#8-testes-automatizados-com-gitlab-cicd)
9. [Deploy Automatizado em Ambientes de Staging e Produção](#9-deploy-automatizado-em-ambientes-de-staging-e-produção)
10. [Rollback e Deploy Canário no GitLab CI/CD](#10-rollback-e-deploy-canário-no-gitlab-cicd)
11. [Pipeline Dinâmico com Includes e Templates](#11-pipeline-dinâmico-com-includes-e-templates)
12. [Jobs Manuais e Aprovações em Pipelines](#12-jobs-manuais-e-aprovações-em-pipelines)
13. [Monitoramento e Logs de Execução no Pipeline](#13-monitoramento-e-logs-de-execução-no-pipeline)
14. [Segurança e Boas Práticas no GitLab CI/CD](#14-segurança-e-boas-práticas-no-gitlab-cicd)
15. [Casos de Uso Avançados e Integração com Ferramentas Externas](#15-casos-de-uso-avançados-e-integração-com-ferramentas-externas)

---

### **1. Introdução ao CI/CD e GitLab Pipelines**

#### **Objetivo**
Introduzir os conceitos de CI/CD (Continuous Integration/Continuous Delivery) e sua implementação usando GitLab Pipelines. Essa seção explica a importância da automação no desenvolvimento e como os pipelines do GitLab CI/CD podem ajudar a otimizar o ciclo de vida de desenvolvimento de software.

---

#### **Conteúdo**

---

##### **1.1 O Que é CI/CD?**

**CI/CD** é um conjunto de práticas de desenvolvimento que visa a automação e monitoramento constantes em todo o ciclo de vida do aplicativo, desde a integração e os testes até a entrega e implantação. Ele é dividido em duas partes principais:

1. **Continuous Integration (CI)**: A integração contínua é uma prática em que os desenvolvedores frequentemente integram o código em um repositório compartilhado. Cada integração é verificada por uma automação de build e testes para detectar problemas o mais cedo possível.
   
2. **Continuous Delivery/Deployment (CD)**: 
   - **Continuous Delivery**: Automatiza a entrega de builds testadas para ambientes de staging e produção, garantindo que estejam prontas para implantação. A entrega em produção geralmente requer aprovação manual.
   - **Continuous Deployment**: Automatiza completamente a implantação em produção sem intervenção humana, desde que o build passe por todos os testes.

**Benefícios do CI/CD**:
- Detecção precoce de bugs e problemas de integração.
- Agilidade para lançar atualizações com rapidez e segurança.
- Redução de erros humanos nos processos de deploy.
- Feedback rápido para desenvolvedores sobre o impacto de suas mudanças.

---

##### **1.2 O Que São GitLab Pipelines?**

**GitLab Pipelines** são fluxos de trabalho automatizados que integram, testam e implantam o código. Um pipeline GitLab é composto por uma sequência de **stages** (etapas) e **jobs** (tarefas) definidos em um arquivo de configuração chamado `.gitlab-ci.yml`. Cada job é executado por um **Runner**, que processa as instruções do pipeline e executa o código.

**Componentes Básicos de um Pipeline GitLab**:
- **Stages**: Etapas do pipeline que definem a ordem de execução (ex.: `build`, `test`, `deploy`).
- **Jobs**: Tarefas específicas executadas dentro de cada stage (ex.: um job para rodar testes ou compilar o código).
- **GitLab Runner**: Um executor que realiza as tarefas definidas nos jobs, podendo ser configurado para diferentes ambientes, como Docker, Shell, ou SSH.

---

##### **1.3 Como o GitLab CI/CD Se Integra ao Ciclo de Vida de Desenvolvimento**

O GitLab CI/CD permite que equipes de desenvolvimento integrem e entreguem código continuamente de forma integrada ao ciclo de vida de desenvolvimento de software. Um pipeline CI/CD típico pode incluir etapas como:

1. **Build**: Compila o código para garantir que não haja erros de sintaxe ou dependências ausentes.
2. **Test**: Executa testes automatizados, como testes unitários e de integração, para garantir a qualidade do código.
3. **Deploy**: Realiza a implantação do código em um ambiente de staging ou produção.

O GitLab CI/CD se adapta bem tanto a equipes pequenas quanto a projetos empresariais maiores, graças à flexibilidade e escalabilidade dos Runners e à estrutura modular do `.gitlab-ci.yml`. Além disso, integra-se com ferramentas externas, como Docker e Kubernetes, facilitando o deploy de aplicações modernas.

---

##### **Resumo da Seção**

| Termo                | Definição                                                                 |
|----------------------|--------------------------------------------------------------------------|
| CI (Continuous Integration)  | Integração contínua para verificar automaticamente cada mudança no código. |
| CD (Continuous Delivery/Deployment) | Entrega e implantação contínua de versões testadas para staging e produção. |
| GitLab Pipelines     | Fluxos de trabalho automatizados de CI/CD configurados no GitLab.         |
| GitLab Runner        | Executor que processa e executa os jobs do pipeline.                     |
| Stages               | Etapas de um pipeline, como `build`, `test` e `deploy`.                  |
| Jobs                 | Tarefas específicas que compõem cada stage.                              |

---

### **Conclusão da Seção**

Com o conceito de CI/CD e a estrutura de GitLab Pipelines, você tem uma visão inicial da importância da automação no desenvolvimento de software. A partir das próximas seções, veremos como configurar o GitLab CI/CD para criar pipelines personalizados e eficientes, ajudando a integrar, testar e implantar seu código de forma contínua e segura.

---

### **2. Configuração Inicial do GitLab CI/CD**

#### **Objetivo**
Demonstrar os primeiros passos para configurar um pipeline de CI/CD no GitLab, incluindo a criação do arquivo `.gitlab-ci.yml` e a configuração do repositório. Ao final desta seção, você estará apto a criar e executar pipelines básicos no GitLab.

---

#### **Conteúdo**

---

##### **2.1 O Arquivo `.gitlab-ci.yml`**

O arquivo `.gitlab-ci.yml` é o coração do CI/CD no GitLab. Nele, você define todas as instruções para a execução do pipeline, como etapas (stages), jobs e scripts. Esse arquivo deve ser adicionado à raiz do repositório, e o GitLab detectará automaticamente as instruções ao processar o pipeline.

###### **Exemplo Básico de `.gitlab-ci.yml`**
```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."
    - # Aqui entrariam comandos específicos para compilar seu código.

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - # Comando para executar testes, como pytest ou npm test.

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - # Comandos para fazer deploy da aplicação.
```

Neste exemplo:
- **`stages`** define as três etapas principais do pipeline: `build`, `test` e `deploy`.
- Cada **job** (como `build_job`, `test_job`, e `deploy_job`) especifica qual etapa pertence e os comandos a serem executados.

> **Dica**: Um pipeline básico sempre começa com um arquivo `.gitlab-ci.yml` simples. À medida que o projeto evolui, você pode adicionar mais jobs e configurá-los de forma específica.

---

##### **2.2 Configuração do Repositório e Acesso ao CI/CD**

Para ativar o CI/CD em seu repositório GitLab, basta adicionar o arquivo `.gitlab-ci.yml`. No entanto, algumas configurações adicionais podem ser necessárias:

1. **Permissões**: Assegure-se de que sua conta ou grupo tem permissões adequadas para criar e editar pipelines no repositório.
2. **Acessar o CI/CD no GitLab**:
   - Acesse o menu **CI/CD** no painel lateral do GitLab.
   - Nessa seção, você pode visualizar e gerenciar pipelines, jobs e configurações avançadas do CI/CD.

---

##### **2.3 Primeira Execução do Pipeline**

Assim que o `.gitlab-ci.yml` for adicionado ao repositório e comitado, o GitLab automaticamente inicia um pipeline para cada nova modificação, caso a configuração esteja correta.

1. **Visualizar o Status do Pipeline**:
   - No painel do GitLab, vá para **CI/CD > Pipelines** para ver a execução dos pipelines.
   - Cada job terá um status como **Running** (Em Execução), **Passed** (Aprovado), ou **Failed** (Falhou).

2. **Detalhes do Job**:
   - Clique em cada job para visualizar detalhes e logs da execução.
   - Logs detalhados são essenciais para detectar e corrigir erros que possam ocorrer durante o build, teste ou deploy.

> **Exemplo de Fluxo**: Após comitar o arquivo `.gitlab-ci.yml`, o pipeline `build -> test -> deploy` será executado sequencialmente. Se o job `build` falhar, os jobs subsequentes (`test` e `deploy`) não serão executados, seguindo o princípio de "fail-fast".

---

##### **2.4 Como o GitLab Runner Processa o Pipeline**

O GitLab Runner é um executor que processa as tarefas (jobs) definidas no `.gitlab-ci.yml`. O Runner pode estar configurado para diferentes ambientes (como Docker, Shell, ou SSH), o que define onde e como os jobs serão executados.

- **GitLab Shared Runners**: GitLab oferece Runners compartilhados, disponíveis para todos os projetos. Estes Runners são ideais para testes rápidos e projetos de menor escala.
- **Configuração de Runners Personalizados**: Para projetos complexos, você pode configurar seu próprio Runner, ajustado às necessidades específicas do ambiente do projeto.

> **Nota**: Em seções futuras, abordaremos em detalhes a configuração e o uso de GitLab Runners personalizados.

---

##### **Resumo da Seção em Tabela**

| Elemento                  | Descrição                                                                                 |
|---------------------------|-------------------------------------------------------------------------------------------|
| `.gitlab-ci.yml`          | Arquivo de configuração do pipeline, armazenado na raiz do repositório.                  |
| Stages                     | Etapas do pipeline (ex.: `build`, `test`, `deploy`).                                     |
| Jobs                       | Tarefas específicas que são executadas em cada stage.                                    |
| CI/CD Menu (GitLab)       | Interface onde é possível visualizar e gerenciar pipelines e jobs.                       |
| GitLab Runner             | Executor que processa os jobs, podendo ser compartilhado ou personalizado.               |

---

### **Conclusão da Seção**

Com a configuração inicial do GitLab CI/CD, você agora tem o conhecimento necessário para criar e executar pipelines básicos em seu projeto. Este é o primeiro passo para integrar, testar e implantar o código automaticamente. Nas próximas seções, aprofundaremos na estrutura do arquivo `.gitlab-ci.yml` e nas opções de configuração para personalizar seus pipelines e torná-los mais eficientes.

---

### **3. Estrutura do Arquivo `.gitlab-ci.yml`**


#### **Objetivo**
Explicar a estrutura e a sintaxe do arquivo `.gitlab-ci.yml`, que é o centro de configuração dos pipelines no GitLab. Ao final desta seção, você estará familiarizado com as principais diretivas e a definição de stages e jobs, aprendendo a organizar o fluxo de trabalho em um pipeline.

---

#### **Conteúdo**

---

##### **3.1 Estrutura Básica do `.gitlab-ci.yml`**

O `.gitlab-ci.yml` define um pipeline como uma sequência de jobs, agrupados em stages que determinam a ordem de execução. O arquivo é escrito em YAML, uma linguagem de marcação estruturada e legível.

###### **Diretivas Principais do `.gitlab-ci.yml`**
1. **`stages`**: Define as etapas do pipeline e a ordem em que os jobs são executados.
2. **`job`**: Cada job representa uma tarefa específica dentro de um stage e contém os comandos a serem executados.
3. **`script`**: Instruções e comandos executados dentro do job.

###### **Exemplo Básico de Estrutura**
```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."
    - # Comandos para compilar o código

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - # Comando para rodar os testes

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - # Comandos de deploy
```

Neste exemplo:
- O pipeline executa três stages (`build`, `test`, e `deploy`) em sequência.
- Cada job (`build_job`, `test_job`, `deploy_job`) é atribuído a um stage e executa um conjunto específico de comandos.

---

##### **3.2 Definindo Etapas (Stages) e a Ordem de Execução**

As **stages** definem a sequência de execução dos jobs. Cada stage representa um momento importante no ciclo de vida do pipeline (por exemplo, `build`, `test`, `deploy`). Jobs dentro do mesmo stage são executados em paralelo, enquanto os stages são executados em sequência.

```yaml
stages:
  - setup
  - build
  - test
  - deploy
```

**Exemplo de Ordem de Execução**:
1. **Setup**: Preparação do ambiente ou instalação de dependências.
2. **Build**: Compilação do código ou geração de artefatos.
3. **Test**: Execução de testes.
4. **Deploy**: Implantação em staging ou produção.

> **Dica**: Definir stages de forma sequencial permite que você pare o pipeline se um estágio crítico, como `build` ou `test`, falhar, evitando um deploy potencialmente problemático.

---

##### **3.3 Criando Jobs e Especificando Script de Execução**

Os **jobs** são as unidades de trabalho que realizam tarefas dentro dos stages. Cada job deve ter pelo menos um script, que define os comandos a serem executados.

###### **Parâmetros Comuns de Job**:
- **`script`**: Comandos que o job executará.
- **`stage`**: Stage em que o job será executado.
- **`only` e `except`**: Define condições para executar o job em branches específicas, tags ou merge requests.

###### **Exemplo de Definição de Job**
```yaml
build_job:
  stage: build
  script:
    - echo "Compiling source code..."
    - make

test_job:
  stage: test
  script:
    - echo "Running unit tests..."
    - ./run_tests.sh

deploy_job:
  stage: deploy
  script:
    - echo "Deploying application..."
    - ./deploy.sh
  only:
    - main
```

Neste exemplo:
- `build_job` compila o código.
- `test_job` roda testes.
- `deploy_job` realiza o deploy, mas apenas na branch `main`, controlado pelo parâmetro `only`.

---

##### **3.4 Exemplo Completo de `.gitlab-ci.yml` com Múltiplos Stages e Jobs**

Abaixo, um exemplo que demonstra um pipeline completo com preparação, build, testes e deploy.

```yaml
stages:
  - setup
  - build
  - test
  - deploy

setup_job:
  stage: setup
  script:
    - echo "Setting up environment..."
    - npm install

build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm run build

unit_tests:
  stage: test
  script:
    - echo "Running unit tests..."
    - npm test

e2e_tests:
  stage: test
  script:
    - echo "Running end-to-end tests..."
    - npm run e2e

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
    - ./deploy_to_staging.sh
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy_to_prod.sh
  only:
    - main
  when: manual
```

Neste exemplo:
- **setup_job** instala dependências e prepara o ambiente.
- **build_job** compila o projeto.
- **unit_tests** e **e2e_tests** executam testes unitários e end-to-end respectivamente.
- **deploy_staging** faz deploy em um ambiente de staging automaticamente para a branch `develop`.
- **deploy_production** é um job manual de deploy em produção, executado apenas na branch `main` quando acionado manualmente.

---

##### **Resumo da Seção em Tabela**

| Parâmetro             | Descrição                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------|
| `stages`              | Define as etapas do pipeline, determinando a ordem dos jobs.                                        |
| `job`                 | Representa uma tarefa específica dentro de uma stage.                                               |
| `script`              | Comandos a serem executados dentro do job.                                                          |
| `only` / `except`     | Define condições para execução do job (ex.: em branches específicas).                               |
| `when: manual`        | Define que o job será executado manualmente, útil para deploys em produção.                         |

---

### **Conclusão da Seção**

Com essa introdução à estrutura e sintaxe do arquivo `.gitlab-ci.yml`, você agora consegue definir pipelines organizados, estruturando stages e jobs de acordo com o fluxo de trabalho desejado. Esse arquivo oferece flexibilidade para controlar quando e como cada etapa do pipeline será executada, facilitando a automação no CI/CD.

---

### **4. Configuração e Registro do GitLab Runner**


#### **Objetivo**
Ensinar como configurar e registrar um GitLab Runner para execução de jobs no pipeline. Nesta seção, abordaremos os diferentes tipos de Runners disponíveis, o processo de instalação e registro, e como escolher o melhor executor para seu ambiente de desenvolvimento.

---

#### **Conteúdo**

---

##### **4.1 O Que é o GitLab Runner?**

O **GitLab Runner** é um agente que executa os jobs definidos no arquivo `.gitlab-ci.yml`. Cada job é processado pelo Runner, que interpreta e executa os comandos especificados em seu próprio ambiente.

###### **Tipos de GitLab Runners**

Existem três tipos principais de Runners no GitLab:

1. **Shared Runners**: Disponíveis para todos os projetos do GitLab (especialmente em instâncias GitLab.com). Eles são ideais para projetos menores ou pipelines de teste, pois são gerenciados pelo próprio GitLab.
   
2. **Group Runners**: Configurados para serem compartilhados entre vários projetos de um mesmo grupo.

3. **Specific Runners**: Instalados e configurados para um projeto específico, ideal para grandes projetos ou pipelines que exigem configurações de ambiente personalizadas.

---

##### **4.2 Tipos de Executor do GitLab Runner**

O GitLab Runner oferece vários executores (executors) que definem o ambiente onde o job será executado:

- **Shell**: Executa os comandos diretamente no shell do sistema onde o Runner está instalado.
- **Docker**: Cria containers Docker para cada job, garantindo ambientes isolados e controlados.
- **SSH**: Conecta-se via SSH a máquinas remotas e executa os jobs.
- **VirtualBox** e **Kubernetes**: Para execução de jobs em máquinas virtuais ou clusters de contêineres, respectivamente.

> **Dica**: O executor Docker é amplamente utilizado em pipelines de CI/CD modernos, pois permite isolamento e reutilização de imagens de contêiner, tornando o processo mais seguro e escalável.

---

##### **4.3 Instalando o GitLab Runner**

A instalação do GitLab Runner varia de acordo com o sistema operacional. Aqui estão os passos para os sistemas mais comuns:

###### **Instalação no Linux**

1. Adicione o repositório oficial do GitLab Runner:
   ```bash
   curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
   chmod +x /usr/local/bin/gitlab-runner
   ```

2. Registre o Runner (detalhado na próxima etapa).

###### **Instalação no Windows**

1. Baixe o GitLab Runner para Windows em: [GitLab Runner para Windows](https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe).
2. Renomeie o arquivo para `gitlab-runner.exe` e mova-o para `C:\GitLab-Runner`.
3. Abra o terminal no diretório `C:\GitLab-Runner` e registre o Runner conforme a próxima etapa.

###### **Instalação no MacOS**

1. Instale o GitLab Runner via Homebrew:
   ```bash
   brew install gitlab-runner
   ```

2. Em seguida, registre o Runner.

---

##### **4.4 Registrando o GitLab Runner**

Após instalar o GitLab Runner, é necessário registrá-lo para associá-lo ao projeto GitLab que executará os jobs. 

1. No GitLab, vá até **Settings > CI/CD > Runners** do projeto.
2. Copie o **token** de registro.
3. No terminal, execute o comando para registrar o Runner:
   ```bash
   gitlab-runner register
   ```

4. Siga as instruções fornecendo:
   - **URL do GitLab**: Geralmente `https://gitlab.com` ou o endereço do GitLab autogerenciado.
   - **Token de Registro**: Cole o token que você copiou anteriormente.
   - **Descrição do Runner**: Nome para identificação.
   - **Tags**: Tags para selecionar jobs específicos para este Runner.
   - **Executor**: Selecione o tipo de executor, como `docker` ou `shell`.

###### **Exemplo de Registro com Executor Docker**
Ao escolher Docker como executor, o sistema pedirá a imagem Docker padrão a ser utilizada:

```plaintext
Enter an executor: docker
Default Docker image (ex.: python:3.9, node:14): alpine:latest
```

Essa imagem é utilizada como base para os jobs, mas também pode ser modificada no próprio arquivo `.gitlab-ci.yml` de acordo com as necessidades do projeto.

---

##### **4.5 Verificando e Gerenciando Runners**

1. Após o registro, o Runner aparecerá na lista de Runners do projeto, em **Settings > CI/CD > Runners**.
2. Você pode ativar ou desativar o Runner, visualizar o status e aplicar restrições para que ele execute apenas jobs específicos, de acordo com as tags configuradas.

---

##### **4.6 Exemplo Prático: Configuração de Job com Docker Executor**

Uma vez configurado um Runner com executor Docker, o `.gitlab-ci.yml` pode utilizar imagens Docker para jobs específicos. 

###### **Exemplo de Configuração do Job**
```yaml
stages:
  - test

test_job:
  stage: test
  image: python:3.9
  script:
    - echo "Testing in a Docker container..."
    - python --version
```

Neste exemplo:
- O Runner utiliza a imagem `python:3.9` para criar um ambiente de contêiner Docker isolado para o job `test_job`.
- O comando `python --version` é executado para verificar a versão do Python instalada.

> **Dica**: Runners com executor Docker são especialmente úteis para garantir que o ambiente de execução seja consistente entre diferentes pipelines e etapas de teste.

---

##### **Resumo da Seção em Tabela**

| Elemento                       | Descrição                                                                                 |
|--------------------------------|-------------------------------------------------------------------------------------------|
| Shared Runners                 | Runners compartilhados disponíveis para múltiplos projetos no GitLab.                     |
| Specific Runners               | Runners configurados para um único projeto ou grupo.                                      |
| Executors (Docker, Shell, etc.)| Ambiente onde os jobs são executados; Docker é o mais usado por oferecer isolamento.      |
| Registro do Runner             | Processo de associação de um Runner a um projeto usando um token do GitLab.               |
| Imagem Docker no `.gitlab-ci.yml` | Define a imagem de contêiner usada para o job no ambiente Docker do Runner.            |

---

### **Conclusão da Seção**

A configuração e o registro do GitLab Runner são etapas essenciais para executar pipelines de CI/CD. Com o uso adequado dos diferentes tipos de executores, especialmente o Docker, você pode otimizar o ambiente de execução e garantir consistência nos testes e builds. Na próxima seção, exploraremos a criação de jobs e stages, estruturando pipelines para atender às necessidades do seu projeto.

---

### **5. Criando Jobs e Stages no GitLab Pipeline**


#### **Objetivo**
Explicar como criar e organizar jobs e stages para automatizar tarefas dentro de um pipeline no GitLab. Nesta seção, abordaremos a criação de jobs independentes, o controle de execução paralela e sequencial, e exemplos práticos de organização do pipeline.

---

#### **Conteúdo**

---

##### **5.1 O Que São Jobs e Stages no GitLab CI/CD?**

- **Jobs**: Representam as tarefas individuais do pipeline (por exemplo, compilação do código, execução de testes, deploy).
- **Stages**: São as etapas que agrupam os jobs, determinando a ordem de execução (ex.: `build`, `test`, `deploy`).

Os jobs de um mesmo stage são executados em paralelo, enquanto os stages são executados sequencialmente, de acordo com a ordem definida no arquivo `.gitlab-ci.yml`.

---

##### **5.2 Definindo Stages no `.gitlab-ci.yml`**

No GitLab CI/CD, a ordem de execução das etapas (stages) é controlada por uma lista na diretiva `stages` no arquivo `.gitlab-ci.yml`. 

```yaml
stages:
  - build
  - test
  - deploy
```

Neste exemplo, o pipeline possui três stages: `build`, `test`, e `deploy`. Os jobs dentro de cada stage são executados paralelamente, mas a sequência de stages é respeitada.

> **Dica**: É uma boa prática estruturar os stages em etapas lógicas que refletem o fluxo de desenvolvimento do projeto, como configuração (`setup`), build, teste e deploy.

---

##### **5.3 Criando Jobs para Cada Stage**

Cada job precisa ser configurado com um `stage`, `script` (comandos a serem executados) e, opcionalmente, outros parâmetros que definem o ambiente ou condições de execução.

###### **Exemplo Básico de Job**
```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."
    - # Comandos para compilar o código

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - # Comandos de teste

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - # Comandos para fazer deploy
  only:
    - main
```

Neste exemplo:
- `build_job` pertence ao stage `build` e compila o código.
- `test_job` roda testes no stage `test`.
- `deploy_job` executa o deploy e é configurado para rodar apenas na branch `main` através do parâmetro `only`.

---

##### **5.4 Controle de Execução Paralela e Sequencial**

1. **Execução Paralela de Jobs**: Todos os jobs em um mesmo stage são executados em paralelo. Isso é útil para acelerar o pipeline, permitindo que múltiplas tarefas sejam processadas simultaneamente, como testes unitários e de integração.

2. **Execução Sequencial entre Stages**: Os stages são executados em sequência, o que permite que o pipeline falhe rapidamente se um job crítico em uma etapa inicial não for bem-sucedido.

> **Exemplo**: Se `build_job` falhar, o GitLab interromperá o pipeline e não executará os stages `test` e `deploy`, preservando o tempo e os recursos de processamento.

---

##### **5.5 Exemplo Prático: Pipeline com Controle de Execução**

Abaixo, um exemplo completo de pipeline no `.gitlab-ci.yml`, estruturado para compilar o código, executar testes e, em seguida, realizar o deploy.

```yaml
stages:
  - setup
  - build
  - test
  - deploy

setup_job:
  stage: setup
  script:
    - echo "Setting up environment..."
    - npm install

build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm run build

unit_tests:
  stage: test
  script:
    - echo "Running unit tests..."
    - npm test

e2e_tests:
  stage: test
  script:
    - echo "Running end-to-end tests..."
    - npm run e2e

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
    - ./deploy_to_staging.sh
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy_to_prod.sh
  only:
    - main
  when: manual
```

Neste pipeline:
- O stage `setup` é responsável pela configuração inicial, como instalar dependências.
- O stage `build` compila o código.
- O stage `test` executa testes unitários e end-to-end em paralelo.
- O stage `deploy` possui dois jobs: `deploy_staging` (automatizado para a branch `develop`) e `deploy_production` (manual para a branch `main`).

> **Observação**: O parâmetro `when: manual` permite que o deploy para produção seja iniciado apenas quando autorizado manualmente, o que é uma prática recomendada para ambientes sensíveis.

---

##### **5.6 Configurações Avançadas de Jobs**

Além dos parâmetros básicos, o GitLab oferece configurações avançadas para personalizar ainda mais os jobs.

- **`allow_failure: true`**: Permite que o job falhe sem interromper o pipeline, útil para testes opcionais.
- **`retry`**: Define o número de tentativas automáticas para um job falho.
- **`tags`**: Permite selecionar Runners específicos para jobs, útil em projetos com múltiplos Runners.
- **`artifacts`**: Define arquivos que devem ser mantidos após a execução do job, como relatórios de teste ou binários.

###### **Exemplo com Configurações Avançadas**
```yaml
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  allow_failure: true
  retry: 2
  tags:
    - docker
  artifacts:
    paths:
      - report.xml
```

Neste exemplo:
- `allow_failure: true` permite que o job `test_job` falhe sem parar o pipeline.
- `retry: 2` tenta rodar o job até duas vezes em caso de falha.
- `artifacts` mantém o arquivo `report.xml` para referência em jobs futuros ou auditoria.

---

##### **Resumo da Seção em Tabela**

| Parâmetro             | Descrição                                                                                   |
|-----------------------|---------------------------------------------------------------------------------------------|
| `stages`              | Define as etapas do pipeline em ordem de execução.                                          |
| `stage`               | Atribui cada job a uma etapa específica do pipeline.                                        |
| `only` / `except`     | Condiciona a execução do job a branches, tags ou merge requests.                            |
| `allow_failure`       | Permite falhas no job sem interromper o pipeline.                                           |
| `retry`               | Define o número de tentativas automáticas para jobs com falha.                              |
| `tags`                | Seleciona Runners específicos para executar o job.                                          |
| `artifacts`           | Define arquivos ou diretórios para serem preservados após o job.                            |

---

### **Conclusão da Seção**

Com o conhecimento sobre criação de jobs e stages no GitLab, você agora pode configurar pipelines que automatizam múltiplas tarefas e respeitam a ordem de execução. Essas práticas permitem estruturar um pipeline eficiente e modular, além de otimizar o tempo de execução com o controle de paralelismo.

---

### **6. Uso de Variáveis de Ambiente no GitLab CI/CD**


#### **Objetivo**
Demonstrar como configurar e utilizar variáveis de ambiente no GitLab CI/CD, tornando o pipeline mais seguro e flexível. Variáveis de ambiente permitem armazenar informações sensíveis, como senhas, chaves de API e configurações específicas para diferentes ambientes.

---

#### **Conteúdo**

---

##### **6.1 O Que São Variáveis de Ambiente?**

As **variáveis de ambiente** no GitLab CI/CD são valores que podem ser usados no pipeline sem serem explicitamente codificados no arquivo `.gitlab-ci.yml`. Elas são ideais para dados sensíveis, que podem ser definidos uma vez e referenciados em múltiplos jobs.

###### **Tipos de Variáveis de Ambiente no GitLab**

1. **Variáveis Predefinidas**: Variáveis automaticamente definidas pelo GitLab, como `CI_COMMIT_REF_NAME` (nome da branch do commit) ou `CI_PROJECT_NAME` (nome do projeto).
2. **Variáveis de Usuário**: Variáveis definidas pelo usuário no GitLab ou diretamente no `.gitlab-ci.yml`.
3. **Variáveis de Secretos/Protegidas**: Variáveis confidenciais, como chaves de API e senhas, que podem ser configuradas com permissões de acesso restritas.

---

##### **6.2 Configurando Variáveis de Ambiente no GitLab**

Para configurar variáveis de ambiente no GitLab:

1. Vá até o seu projeto no GitLab.
2. Acesse **Settings > CI/CD > Variables**.
3. Clique em **Add Variable** e preencha:
   - **Key**: Nome da variável.
   - **Value**: Valor da variável.
   - **Protected**: Define se a variável só estará disponível para jobs em branches protegidas.
   - **Masked**: Esconde o valor da variável nos logs (ideal para dados sensíveis).

###### **Exemplo de Variáveis Configuradas**:
- **API_KEY**: Chave de API para um serviço externo.
- **DATABASE_URL**: URL de conexão com o banco de dados.
- **DEPLOY_ENV**: Ambiente de deploy (ex.: `staging` ou `production`).

> **Dica**: Use variáveis protegidas (`Protected`) para garantir que dados sensíveis só estejam disponíveis em ambientes de produção.

---

##### **6.3 Usando Variáveis de Ambiente no `.gitlab-ci.yml`**

No arquivo `.gitlab-ci.yml`, você pode acessar as variáveis usando a sintaxe `$VARIAVEL`, que o GitLab substitui automaticamente pelo valor configurado.

###### **Exemplo de Uso de Variáveis no Pipeline**
```yaml
stages:
  - build
  - deploy

build_job:
  stage: build
  script:
    - echo "Building project with API_KEY: $API_KEY"

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to $DEPLOY_ENV environment..."
    - ./deploy_script.sh
```

Neste exemplo:
- `$API_KEY` é usada no job `build_job` para acessar a chave de API.
- `$DEPLOY_ENV` define o ambiente de deploy, tornando o processo mais flexível.

---

##### **6.4 Usando Variáveis para Configurações Sensíveis e Seguras**

Variáveis de ambiente são ideais para armazenar dados sensíveis e de configuração. Exemplo: uma chave de API pode ser armazenada como variável e referenciada em diferentes jobs sem expor seu valor no código.

###### **Exemplo de Configuração Segura**
1. No GitLab, adicione uma variável chamada `SECRET_KEY` com o valor da chave.
2. No `.gitlab-ci.yml`, utilize a variável sem expor seu valor:
   ```yaml
   test_job:
     stage: test
     script:
       - echo "Testing with secret key..."
       - ./test_script.sh $SECRET_KEY
   ```

---

##### **6.5 Variáveis de Ambiente no Escopo do `.gitlab-ci.yml`**

Além das variáveis configuradas no GitLab, você pode definir variáveis diretamente no `.gitlab-ci.yml`, mas essas serão visíveis no repositório e não devem conter dados sensíveis.

###### **Exemplo de Definição no `.gitlab-ci.yml`**
```yaml
variables:
  NODE_ENV: "production"
  DATABASE_USER: "root"

stages:
  - test

test_job:
  stage: test
  script:
    - echo "Running in $NODE_ENV mode"
    - echo "Connecting as $DATABASE_USER"
```

> **Nota**: As variáveis configuradas no `.gitlab-ci.yml` têm prioridade sobre variáveis predefinidas do GitLab, mas não sobre variáveis definidas no painel de configurações do projeto.

---

##### **6.6 Exemplo Completo: Pipeline com Variáveis de Ambiente**

Abaixo, um exemplo de pipeline utilizando variáveis de ambiente para controle de configurações e segurança:

```yaml
variables:
  DEPLOY_ENV: "staging"

stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project in $DEPLOY_ENV environment..."

test_job:
  stage: test
  script:
    - echo "Running tests with API_KEY: $API_KEY"
    - ./run_tests.sh

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to $DEPLOY_ENV"
    - ./deploy_to_$DEPLOY_ENV.sh
  only:
    - main
```

Neste pipeline:
- O `build_job` usa a variável `DEPLOY_ENV` para configurar o ambiente.
- O `test_job` utiliza a variável `API_KEY` para acessar uma chave de API.
- O `deploy_job` executa um script específico de acordo com o ambiente configurado.

---

##### **Resumo da Seção em Tabela**

| Parâmetro               | Descrição                                                                                       |
|-------------------------|-------------------------------------------------------------------------------------------------|
| Variáveis Predefinidas  | Variáveis automáticas fornecidas pelo GitLab (ex.: `CI_COMMIT_REF_NAME`).                       |
| Variáveis de Usuário    | Variáveis definidas no painel GitLab, acessíveis em qualquer job do pipeline.                   |
| Variáveis Protegidas    | Variáveis que só estão disponíveis para jobs em branches protegidas, ideais para dados sensíveis.|
| `$VARIAVEL`             | Sintaxe para acessar variáveis no `.gitlab-ci.yml`.                                             |

---

### **Conclusão da Seção**

Com o uso de variáveis de ambiente, você pode configurar e proteger dados sensíveis no pipeline, mantendo as operações flexíveis e seguras. Variáveis de ambiente tornam o pipeline mais adaptável a diferentes ambientes e simplificam o gerenciamento de configurações. Nas próximas seções, exploraremos a utilização de artefatos e cache para otimizar o pipeline e o armazenamento de dados entre jobs.

---

### **7. Artefatos e Cache no GitLab Pipeline**


#### **Objetivo**
Explicar o uso de artefatos e cache no GitLab para otimizar o pipeline e melhorar a reutilização de dados entre jobs e stages. Esta seção mostra como configurar e compartilhar arquivos e diretórios entre etapas, agilizando o processo de build e deploy.

---

#### **Conteúdo**

---

##### **7.1 O Que São Artefatos e Cache?**

- **Artefatos**: São arquivos gerados durante a execução de um job e armazenados temporariamente pelo GitLab para que possam ser usados em jobs subsequentes ou para auditoria. Exemplos incluem relatórios de teste, logs de build e arquivos de configuração.
  
- **Cache**: Um diretório ou conjunto de arquivos que o GitLab armazena para reduzir o tempo de execução de jobs, especialmente para dependências que não mudam com frequência, como bibliotecas de terceiros ou pacotes de desenvolvimento.

---

##### **7.2 Configurando Artefatos no `.gitlab-ci.yml`**

Para definir artefatos no pipeline, adicione a seção `artifacts` dentro do job que gera os arquivos. A seção `artifacts` permite especificar quais arquivos ou diretórios devem ser armazenados e por quanto tempo.

###### **Exemplo de Uso de Artefatos**
```yaml
stages:
  - build
  - test

build_job:
  stage: build
  script:
    - echo "Compiling project..."
    - mkdir build && echo "Build files" > build/output.txt
  artifacts:
    paths:
      - build/
    expire_in: 1 week

test_job:
  stage: test
  script:
    - echo "Testing the build files..."
    - cat build/output.txt
```

Neste exemplo:
- O `build_job` gera arquivos na pasta `build/` e os define como artefatos.
- O `test_job`, que vem depois, acessa esses artefatos para realizar testes.
- **`expire_in: 1 week`** define que os artefatos expiram em uma semana, liberando espaço de armazenamento automaticamente.

> **Dica**: Configure um tempo de expiração (`expire_in`) para evitar o acúmulo de dados desnecessários e otimizar o uso do armazenamento no GitLab.

---

##### **7.3 Compartilhamento de Dados Entre Jobs Usando Artefatos**

Ao definir artefatos, você permite que os arquivos sejam compartilhados entre jobs em diferentes stages. Isso é útil em casos onde o output de um job deve ser usado por outro, como na sequência `build -> test -> deploy`.

###### **Exemplo de Pipeline Completo com Artefatos**
```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - mkdir build
    - echo "Compiling project..." > build/output.txt
  artifacts:
    paths:
      - build/

test_job:
  stage: test
  script:
    - echo "Running tests on build output..."
    - cat build/output.txt

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production using build output..."
    - cat build/output.txt
  only:
    - main
```

Neste exemplo:
- O job `build_job` gera arquivos de build que são armazenados como artefatos.
- O job `test_job` acessa esses arquivos para executar testes.
- O job `deploy_job` usa o mesmo output para deploy em produção.

> **Observação**: O artefato `build/` é mantido entre stages, garantindo que o mesmo build seja usado para testes e deploy, melhorando a consistência do pipeline.

---

##### **7.4 Configurando Cache no `.gitlab-ci.yml`**

O cache armazena arquivos ou diretórios específicos para reduzir o tempo de execução dos jobs, especialmente útil para pacotes e dependências. O cache é reutilizado entre execuções do pipeline, diferentemente dos artefatos, que são criados e utilizados apenas no pipeline atual.

###### **Exemplo de Uso de Cache**
```yaml
stages:
  - setup
  - test

setup_job:
  stage: setup
  script:
    - echo "Setting up environment..."
    - npm install
  cache:
    paths:
      - node_modules/

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  cache:
    paths:
      - node_modules/
```

Neste exemplo:
- O cache `node_modules/` permite que o GitLab armazene as dependências do Node.js entre execuções, evitando a reinstalação completa de pacotes.
- Em pipelines futuros, o GitLab usa o cache armazenado em vez de instalar tudo novamente, acelerando o processo de setup e testes.

> **Dica**: Use o cache para diretórios de dependências ou pacotes de build, garantindo que jobs subsequentes acessem versões já baixadas, reduzindo a utilização de largura de banda e o tempo de execução.

---

##### **7.5 Diferença Entre Artefatos e Cache**

| Característica          | Artefatos                                              | Cache                                    |
|-------------------------|--------------------------------------------------------|------------------------------------------|
| **Finalidade**          | Compartilhar dados entre jobs em um pipeline           | Reutilizar dados entre execuções de pipeline |
| **Escopo**              | Disponível apenas para o pipeline atual                | Disponível para execuções futuras       |
| **Tempo de Expiração**  | Definido com `expire_in`, valores padrão variam         | Pode ser definido, mas é geralmente mantido enquanto necessário |
| **Exemplo de Uso**      | Resultados de builds e logs de testes                  | Dependências como `node_modules/` ou bibliotecas baixadas |

---

##### **7.6 Configurações Avançadas de Artefatos e Cache**

1. **Escopo de Artefatos**: Use o parâmetro `when` para definir se o artefato deve ser armazenado apenas quando o job passar (`on_success`), falhar (`on_failure`), ou em ambos (`always`).
   ```yaml
   artifacts:
     paths:
       - build/
     when: on_success
   ```

2. **Chaves de Cache**: Defina uma chave para o cache para torná-lo específico a um branch ou commit.
   ```yaml
   cache:
     key: "$CI_COMMIT_REF_NAME"
     paths:
       - node_modules/
   ```

3. **Cache Compartilhado**: Se você precisar de um cache compartilhado entre diferentes branches ou pipelines, use uma chave fixa para garantir que o cache seja acessível entre jobs.

---

### **Exemplo Completo de Pipeline com Artefatos e Cache**

```yaml
stages:
  - setup
  - build
  - test
  - deploy

setup_job:
  stage: setup
  script:
    - echo "Installing dependencies..."
    - npm install
  cache:
    key: "$CI_COMMIT_REF_NAME"
    paths:
      - node_modules/

build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  artifacts:
    paths:
      - reports/

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
```

Neste pipeline:
- O **setup_job** configura as dependências e armazena o cache do diretório `node_modules/` para reutilização em futuros pipelines.
- O **build_job** armazena o diretório `dist/` como artefato, contendo os arquivos de build.
- O **test_job** gera relatórios armazenados como artefatos no diretório `reports/`.
- O **deploy_job** executa o deploy usando os arquivos de build gerados.

---

##### **Resumo da Seção em Tabela**

| Parâmetro             | Descrição                                                                                   |
|-----------------------|---------------------------------------------------------------------------------------------|
| `artifacts`           | Define arquivos ou diretórios gerados por um job para uso em outros jobs.                  |
| `cache`               | Define arquivos ou diretórios a serem reutilizados entre execuções de pipeline.            |
| `expire_in`           | Define o tempo de expiração dos artefatos para liberar espaço de armazenamento.            |
| `when`                | Define quando os artefatos devem ser mantidos (ex.: `on_success`, `on_failure`).           |
| `key`                 | Define uma chave para o cache, útil para controlar a reutilização em diferentes branches.  |

---

### **Conclusão da Seção**

Com o uso de artefatos e cache, você pode otimizar o tempo de execução e garantir que os dados necessários estejam disponíveis em cada job do pipeline. Essa prática é essencial para projetos que requerem builds e testes rápidos, melhorando a eficiência e reduzindo o consumo de recursos.

---

### **8. Testes Automatizados com GitLab CI/CD**

#### **Objetivo**
Explicar como configurar testes automatizados em pipelines GitLab CI/CD para garantir a qualidade do código em diferentes etapas do desenvolvimento. Nesta seção, abordaremos a configuração de jobs para testes unitários e de integração, além do uso de containers para ambientes de teste e a geração de relatórios.

---

#### **Conteúdo**

---

##### **8.1 A Importância dos Testes Automatizados**

Os testes automatizados são fundamentais no CI/CD, pois permitem que o código seja validado automaticamente em cada mudança, reduzindo o risco de bugs. Os testes comuns em pipelines incluem:
- **Testes Unitários**: Verificam o funcionamento de componentes individuais do código.
- **Testes de Integração**: Garantem que componentes diferentes funcionem bem juntos.
- **Testes End-to-End (E2E)**: Validam o sistema como um todo, simulando o uso real.

---

##### **8.2 Configurando Jobs para Testes no `.gitlab-ci.yml`**

Para configurar um job de teste, adicione um job específico para cada tipo de teste no arquivo `.gitlab-ci.yml`, especificando os comandos de execução para cada conjunto de testes.

###### **Exemplo Básico: Testes Unitários e de Integração**

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building project..."
    - make

unit_test_job:
  stage: test
  script:
    - echo "Running unit tests..."
    - make test-unit

integration_test_job:
  stage: test
  script:
    - echo "Running integration tests..."
    - make test-integration
```

Neste exemplo:
- O pipeline contém dois jobs de teste: `unit_test_job` e `integration_test_job`, ambos no stage `test`.
- Os testes são executados separadamente, facilitando a depuração e permitindo que um teste falhe sem interromper a execução de outros.

---

##### **8.3 Usando Containers para Testes com Dependências**

Para projetos com múltiplas dependências (como bancos de dados ou serviços externos), o uso de containers ajuda a criar um ambiente de teste consistente e isolado. O GitLab permite configurar serviços de contêiner no job de teste.

###### **Exemplo: Teste com Banco de Dados em Container**

```yaml
stages:
  - test

test_job:
  stage: test
  image: python:3.9
  services:
    - postgres:latest
  variables:
    POSTGRES_DB: test_db
    POSTGRES_USER: user
    POSTGRES_PASSWORD: pass
    DATABASE_URL: "postgres://user:pass@postgres:5432/test_db"
  script:
    - echo "Running tests with PostgreSQL database..."
    - pytest
```

Neste exemplo:
- O job `test_job` usa a imagem `python:3.9` e o serviço `postgres:latest` para simular um ambiente de teste com banco de dados.
- As variáveis `DATABASE_URL` e `POSTGRES_*` configuram a conexão para que os testes acessem o banco de dados PostgreSQL.

---

##### **8.4 Geração e Publicação de Relatórios de Teste**

Relatórios de teste permitem visualizar o resultado de cada teste executado no pipeline. Esses relatórios podem ser configurados como artefatos e, no GitLab, podem ser processados para visualização direta na interface de CI/CD.

###### **Exemplo: Geração de Relatório JUnit**

```yaml
stages:
  - test

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - pytest --junitxml=report.xml
  artifacts:
    paths:
      - report.xml
    reports:
      junit: report.xml
```

Neste exemplo:
- O `pytest` é configurado para gerar um relatório em formato JUnit, armazenado no arquivo `report.xml`.
- O relatório é especificado na seção `artifacts` com `reports: junit`, permitindo que o GitLab exiba os resultados dos testes na interface.

> **Dica**: O GitLab reconhece arquivos JUnit automaticamente, facilitando a análise de falhas de teste diretamente na interface de CI/CD.

---

##### **8.5 Exemplo Completo de Pipeline com Testes Automatizados**

Abaixo está um exemplo de pipeline GitLab CI/CD completo, incluindo build, testes unitários, testes de integração e deploy.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."
    - npm install
    - npm run build

unit_test_job:
  stage: test
  script:
    - echo "Running unit tests..."
    - npm test -- --coverage
  artifacts:
    paths:
      - coverage/

integration_test_job:
  stage: test
  services:
    - mysql:5.7
  variables:
    MYSQL_DATABASE: test_db
    MYSQL_USER: user
    MYSQL_PASSWORD: pass
    MYSQL_ROOT_PASSWORD: rootpass
  script:
    - echo "Running integration tests with MySQL..."
    - npm run test-integration

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
```

Neste pipeline:
- `build_job` instala dependências e compila o projeto.
- `unit_test_job` executa testes unitários e armazena o diretório de cobertura de código (`coverage/`) como artefato.
- `integration_test_job` usa o serviço `mysql:5.7` para testes de integração.
- `deploy_job` executa o deploy em produção somente na branch `main`.

> **Observação**: O uso de containers para banco de dados (`mysql:5.7`) no `integration_test_job` isola o ambiente de teste e evita interferências com o ambiente local.

---

##### **8.6 Estrutura Modular para Testes em Diferentes Ambientes**

Para projetos complexos, configure jobs separados para ambientes como staging e produção, usando variáveis para diferenciar as configurações.

###### **Exemplo: Configuração Modular de Ambientes de Teste**

```yaml
stages:
  - build
  - test
  - deploy

test_unit:
  stage: test
  script:
    - echo "Running unit tests..."
    - npm test

test_staging:
  stage: test
  script:
    - echo "Testing in staging environment..."
    - ./staging_tests.sh
  only:
    - develop

test_production:
  stage: test
  script:
    - echo "Testing in production environment..."
    - ./production_tests.sh
  only:
    - main
```

Neste exemplo:
- `test_unit` executa testes unitários em todas as branches.
- `test_staging` e `test_production` são configurados para rodar testes específicos para staging e produção, dependendo da branch (`develop` ou `main`).

---

##### **Resumo da Seção em Tabela**

| Componente            | Descrição                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------|
| Testes Unitários      | Verificam funcionalidades individuais de componentes.                                               |
| Testes de Integração  | Validam a interação entre diferentes módulos ou serviços.                                           |
| Contêiner de Serviço  | Executa serviços auxiliares, como bancos de dados, durante o teste.                                 |
| Relatório JUnit       | Formato de relatório suportado pelo GitLab para exibir resultados de teste.                         |
| Variáveis de Ambiente | Configurações específicas para ambientes de teste, como credenciais e URLs de conexão.              |

---

### **Conclusão da Seção**

Com a configuração de testes automatizados no pipeline GitLab CI/CD, você pode garantir que seu código seja validado em cada alteração, melhorando a qualidade e a confiabilidade do projeto. Esses testes são essenciais para detectar problemas antes do deploy, reduzindo o risco de bugs em produção.

---

### **9. Deploy Automatizado em Ambientes de Staging e Produção**

#### **Objetivo**
Demonstrar como configurar o deploy automatizado em ambientes de staging e produção no GitLab CI/CD. Essa seção aborda o controle de deploys em diferentes branches, o uso de variáveis para configuração de ambiente e a criação de pipelines que gerenciam deploys em diversos ambientes.

---

#### **Conteúdo**

---

##### **9.1 O Que é Deploy Automatizado?**

O **deploy automatizado** permite que o código seja enviado para um ambiente de staging ou produção de forma automática, seguindo critérios e validações configurados no pipeline. No GitLab CI/CD, os deploys podem ser:
- **Automatizados**: Executados automaticamente após a aprovação dos testes.
- **Manuais**: Executados apenas quando autorizados, garantindo controle sobre o momento de deploy.

**Benefícios**:
- Redução de erros humanos durante o deploy.
- Menor tempo de entrega, pois o processo é automático.
- Consistência e controle de versões nos ambientes.

---

##### **9.2 Configurando Jobs de Deploy para Staging e Produção**

Os jobs de deploy são configurados como qualquer outro job no `.gitlab-ci.yml`, mas são executados em stages específicos (como `deploy`) e geralmente utilizam variáveis para acessar o ambiente correto.

###### **Exemplo Básico: Deploy para Staging e Produção**

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
    - ./deploy_to_staging.sh
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy_to_production.sh
  only:
    - main
  when: manual
```

Neste exemplo:
- `deploy_staging` é executado automaticamente na branch `develop` para testar a aplicação em staging.
- `deploy_production` só é executado na branch `main`, e de forma manual (`when: manual`), dando maior controle sobre o deploy em produção.

> **Dica**: Para ambientes de produção, é recomendável usar deploy manual para evitar atualizações indesejadas sem aprovação.

---

##### **9.3 Uso de `only` e `except` para Controle de Branches**

Os parâmetros **`only`** e **`except`** são usados para definir em quais branches ou tags um job será executado, permitindo que deploys sejam realizados apenas em branches específicas, como `main` para produção e `develop` para staging.

```yaml
deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
  only:
    - main
  when: manual
```

Neste caso, `deploy_staging` roda apenas em `develop`, enquanto `deploy_production` é restrito à `main`.

---

##### **9.4 Usando Variáveis de Ambiente para Deploy**

Variáveis de ambiente facilitam a configuração de credenciais e URLs específicas para cada ambiente, tornando o processo de deploy mais seguro e flexível.

1. Defina variáveis no GitLab (em **Settings > CI/CD > Variables**).
   - **STAGING_URL**: URL do ambiente de staging.
   - **PRODUCTION_URL**: URL do ambiente de produção.
   - **API_TOKEN**: Token de autenticação para o servidor.

2. Configure o pipeline para usar essas variáveis.

###### **Exemplo de Job Usando Variáveis de Ambiente**

```yaml
deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to $STAGING_URL"
    - curl -X POST -H "Authorization: Bearer $API_TOKEN" $STAGING_URL

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to $PRODUCTION_URL"
    - curl -X POST -H "Authorization: Bearer $API_TOKEN" $PRODUCTION_URL
  only:
    - main
  when: manual
```

Neste exemplo:
- O `API_TOKEN` protege a autenticação nos servidores.
- As variáveis `STAGING_URL` e `PRODUCTION_URL` permitem que o mesmo job execute o deploy nos ambientes certos, sem necessidade de alterar o código.

> **Dica**: Use variáveis protegidas no GitLab para dados sensíveis, garantindo que só estejam acessíveis em branches de produção.

---

##### **9.5 Exemplo Completo de Pipeline com Deploy em Staging e Produção**

O exemplo abaixo demonstra um pipeline completo, com deploys automáticos e manuais, usando variáveis de ambiente e controles de branch.

```yaml
stages:
  - build
  - test
  - deploy

variables:
  STAGING_URL: "https://staging.example.com/deploy"
  PRODUCTION_URL: "https://production.example.com/deploy"

build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging at $STAGING_URL"
    - curl -X POST -H "Authorization: Bearer $API_TOKEN" $STAGING_URL
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production at $PRODUCTION_URL"
    - curl -X POST -H "Authorization: Bearer $API_TOKEN" $PRODUCTION_URL
  only:
    - main
  when: manual
```

Neste pipeline:
- `deploy_staging` é acionado automaticamente quando há mudanças na branch `develop`, usando a URL de staging.
- `deploy_production` depende de autorização manual e roda na branch `main`, utilizando a URL de produção.

> **Observação**: Este pipeline permite controle completo sobre deploys, garantindo que apenas código aprovado seja enviado para produção, enquanto atualizações em staging são automáticas para testes contínuos.

---

##### **Resumo da Seção em Tabela**

| Parâmetro             | Descrição                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------|
| `only` / `except`     | Restringe a execução de jobs a branches ou tags específicas, ideal para controle de deploy.         |
| `when: manual`        | Define que o job será executado manualmente, útil para deploys em produção.                         |
| Variáveis de Ambiente | Configurações seguras para URLs e credenciais de deploy.                                            |
| `curl`                | Utilizado para realizar deploys através de comandos HTTP, acionando APIs de servidores de deploy.   |

---

### **Conclusão da Seção**

Com a configuração de deploys automatizados no GitLab CI/CD, você agora pode controlar a entrega de código para staging e produção de forma segura e eficiente. Os controles de branches, as variáveis de ambiente e a opção de deploy manual para produção garantem maior segurança e flexibilidade.

---

### **10. Rollback e Deploy Canário no GitLab CI/CD**

#### **Objetivo**
Ensinar a configurar estratégias de rollback e deploy canário no GitLab CI/CD. Rollbacks permitem reverter um deploy em caso de falha, enquanto o deploy canário possibilita lançar novas funcionalidades gradualmente, reduzindo o risco de problemas em produção.

---

#### **Conteúdo**

---

##### **10.1 O Que é Rollback?**

**Rollback** é o processo de reverter uma aplicação para uma versão anterior caso uma atualização em produção apresente problemas. Configurar uma estratégia de rollback no GitLab permite que o deploy seja revertido automaticamente ou manualmente em caso de falhas.

---

##### **10.2 Configurando um Job de Rollback no `.gitlab-ci.yml`**

Para implementar um rollback, você pode criar um job específico no pipeline que execute o processo de reversão para uma versão estável anterior.

###### **Exemplo de Job de Rollback**

```yaml
stages:
  - build
  - test
  - deploy
  - rollback

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy_to_production.sh
  only:
    - main
  when: manual

rollback_production:
  stage: rollback
  script:
    - echo "Rolling back to previous version..."
    - ./rollback.sh
  only:
    - main
  when: manual
```

Neste exemplo:
- `deploy_production` realiza o deploy manual em produção.
- `rollback_production` executa o script `rollback.sh` para reverter a atualização em produção, garantindo um retorno a uma versão anterior estável.

> **Dica**: Configure o rollback para ser acionado manualmente (`when: manual`), permitindo que a equipe analise o problema antes de reverter o deploy.

---

##### **10.3 O Que é Deploy Canário?**

**Deploy Canário** é uma estratégia de deploy onde uma nova versão é lançada apenas para uma pequena parte dos usuários. Essa abordagem permite que a nova funcionalidade seja testada em produção com um impacto reduzido, facilitando a detecção de problemas antes de liberar a atualização para todos os usuários.

---

##### **10.4 Configurando Deploy Canário no GitLab**

Para um deploy canário, você geralmente divide o processo de deploy em duas fases:
1. **Deploy Canário**: Libera a nova versão para uma pequena porção de servidores ou usuários.
2. **Deploy Completo**: Após verificar a estabilidade, libera a versão para todos os usuários.

###### **Exemplo de Pipeline com Deploy Canário**

```yaml
stages:
  - build
  - test
  - deploy

deploy_canary:
  stage: deploy
  script:
    - echo "Deploying canary release..."
    - ./deploy_to_canary.sh
  only:
    - main
  when: manual

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to all production servers..."
    - ./deploy_to_production.sh
  only:
    - main
  when: manual
  needs:
    - deploy_canary
```

Neste exemplo:
- `deploy_canary` realiza o deploy canário em produção para uma pequena porção de servidores. Esse deploy é acionado manualmente para monitoramento.
- `deploy_production` só é executado após o sucesso do `deploy_canary`, garantindo que o deploy completo só ocorra após a verificação da estabilidade.

> **Nota**: Usar `needs` permite que o deploy completo dependa do sucesso do deploy canário.

---

##### **10.5 Exemplo Completo de Pipeline com Rollback e Deploy Canário**

Abaixo está um pipeline completo que incorpora as estratégias de rollback e deploy canário.

```yaml
stages:
  - build
  - test
  - deploy
  - rollback

build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test

deploy_canary:
  stage: deploy
  script:
    - echo "Deploying canary release..."
    - ./deploy_to_canary.sh
  only:
    - main
  when: manual

deploy_production:
  stage: deploy
  script:
    - echo "Deploying full production release..."
    - ./deploy_to_production.sh
  only:
    - main
  when: manual
  needs:
    - deploy_canary

rollback_production:
  stage: rollback
  script:
    - echo "Rolling back to previous stable version..."
    - ./rollback.sh
  only:
    - main
  when: manual
```

Neste pipeline:
- `deploy_canary` realiza o deploy para um ambiente canário, sendo ativado manualmente para monitoramento.
- `deploy_production` depende do sucesso do deploy canário (`needs: - deploy_canary`) e também é manual.
- `rollback_production` permite reverter a aplicação para uma versão estável anterior em caso de problemas.

> **Observação**: Tanto o deploy canário quanto o deploy completo e o rollback são configurados para execução manual, permitindo maior controle no ambiente de produção.

---

##### **10.6 Monitoramento Durante o Deploy Canário**

Para maximizar o sucesso de um deploy canário, é importante monitorar métricas e logs em tempo real. Algumas práticas de monitoramento incluem:
- **Verificação de Logs**: Acompanhar logs de erro no ambiente canário.
- **Métricas de Performance**: Monitorar o uso de CPU, memória e tempo de resposta para detectar problemas de desempenho.
- **Feedback do Usuário**: Coletar feedback de uma pequena porção de usuários que usam a nova versão.

---

##### **Resumo da Seção em Tabela**

| Termo                | Descrição                                                                                           |
|----------------------|-----------------------------------------------------------------------------------------------------|
| Rollback             | Processo de reverter uma aplicação para uma versão anterior, geralmente em caso de falha.           |
| Deploy Canário       | Estratégia de deploy que libera a nova versão para um subconjunto de usuários para monitoramento.    |
| `when: manual`       | Configuração que define jobs de deploy ou rollback acionados manualmente para maior controle.       |
| `needs`              | Parâmetro que permite definir dependências entre jobs, ideal para sequenciar deploys canários.      |
| Monitoramento        | Processo de coleta de métricas e logs para verificar o impacto do deploy e prevenir problemas.      |

---

### **Conclusão da Seção**

Com as estratégias de rollback e deploy canário, você pode controlar e monitorar a introdução de novas versões em produção, minimizando o impacto de eventuais problemas. O rollback manual oferece uma medida de segurança, enquanto o deploy canário permite a verificação gradual e controlada de novas funcionalidades antes da liberação completa.

---

### **11. Pipeline Dinâmico com Includes e Templates**


#### **Objetivo**
Demonstrar como usar `includes` e templates para criar pipelines dinâmicos e modulares no GitLab CI/CD. Essas técnicas permitem organizar o arquivo `.gitlab-ci.yml` em configurações reutilizáveis, facilitando a manutenção de pipelines complexos.

---

#### **Conteúdo**

---

##### **11.1 O Que São Includes e Templates no GitLab CI/CD?**

- **Includes**: A diretiva `include` permite incluir configurações externas ou arquivos YAML adicionais, modularizando o pipeline. Isso é útil para compartilhar configurações entre projetos ou dividir um pipeline extenso em partes menores e gerenciáveis.
- **Templates**: Arquivos YAML reutilizáveis que definem configurações padrão para jobs, stages ou variáveis e podem ser incluídos em diferentes pipelines. 

**Vantagens**:
- Facilita a reutilização e a padronização de configurações.
- Simplifica a manutenção de pipelines extensos e complexos.
- Permite que múltiplos projetos compartilhem a mesma estrutura de pipeline.

---

##### **11.2 Usando `include` no `.gitlab-ci.yml`**

A diretiva `include` permite incorporar arquivos YAML externos ao `.gitlab-ci.yml`. É possível usar includes para importar arquivos armazenados em repositórios internos do GitLab, URLs externas ou templates de CI/CD.

###### **Exemplo de Estrutura Básica de Include**
```yaml
include:
  - local: 'configs/build.yml'     # Arquivo local no repositório
  - project: 'group/shared-ci'     # Arquivo em outro repositório GitLab
    file: '/ci-templates/test.yml'
  - remote: 'https://example.com/deploy.yml'  # URL externa
```

> **Dica**: Estruturar arquivos YAML por função (por exemplo, `build.yml`, `test.yml`, `deploy.yml`) ajuda a manter o pipeline organizado.

---

##### **11.3 Exemplos de Arquivos YAML para Includes**

###### **Arquivo de Configuração `build.yml`**

```yaml
build_job:
  stage: build
  script:
    - echo "Building project..."
    - npm install
    - npm run build
  artifacts:
    paths:
      - build/
    expire_in: 1 week
```

Este arquivo define um job de build que pode ser incluído em diferentes pipelines, centralizando a configuração de build em um local específico.

###### **Arquivo de Configuração `test.yml`**

```yaml
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  artifacts:
    paths:
      - coverage/
    reports:
      junit: report.xml
```

Este arquivo define um job de teste com geração de relatórios e cobertura de código.

###### **Incorporando os Arquivos `build.yml` e `test.yml` no Pipeline Principal**

No arquivo `.gitlab-ci.yml` principal:

```yaml
include:
  - local: 'configs/build.yml'
  - local: 'configs/test.yml'

stages:
  - build
  - test
  - deploy

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
```

Neste exemplo:
- Os jobs de build e teste são incluídos de arquivos externos, permitindo modularizar o pipeline.
- `deploy_job` é configurado diretamente no arquivo principal para deploy em produção.

---

##### **11.4 Usando Templates de CI/CD no GitLab**

O GitLab fornece templates prontos para várias linguagens e frameworks. Esses templates oferecem configurações iniciais que podem ser personalizadas conforme necessário.

Para incluir um template oficial do GitLab, use a diretiva `include` apontando para o template desejado.

###### **Exemplo de Uso de Template Oficial do GitLab**

```yaml
include:
  - template: 'Ruby/Rails.gitlab-ci.yml'   # Template oficial para Rails

stages:
  - build
  - test
  - deploy
```

Neste exemplo:
- O template `Rails.gitlab-ci.yml` fornece uma configuração padrão para projetos Ruby on Rails, incluindo jobs de build, testes e deploy.
- Este template pode ser combinado com jobs personalizados no mesmo `.gitlab-ci.yml`.

> **Dica**: Templates são ideais para configurações comuns que podem ser padronizadas em múltiplos projetos, economizando tempo e evitando a duplicação de código.

---

##### **11.5 Exemplos de Templates Personalizados**

Além dos templates oficiais, você pode criar seus próprios templates para reutilização entre projetos.

###### **Exemplo de Template Customizado: `ci-template.yml`**

```yaml
stages:
  - setup
  - build
  - test
  - deploy

variables:
  NODE_ENV: "production"

setup_job:
  stage: setup
  script:
    - echo "Setting up environment..."
    - npm install
```

Inclua este template em diferentes projetos para aplicar a mesma configuração de setup.

###### **Pipeline Principal com o Template Customizado**

```yaml
include:
  - project: 'my-group/my-templates'
    file: '/ci-template.yml'

deploy_job:
  stage: deploy
  script:
    - echo "Deploying application..."
    - ./deploy.sh
  only:
    - main
```

Neste caso:
- O pipeline principal inclui o template de setup e configurações iniciais.
- O job de deploy é configurado diretamente no pipeline principal, mas com todas as variáveis e stages já definidas pelo template.

---

##### **11.6 Exemplo Completo de Pipeline Modular com Includes e Templates**

O exemplo abaixo demonstra um pipeline completo, incorporando includes e templates para criar um fluxo modular e reutilizável.

###### **Arquivo Principal `.gitlab-ci.yml`**

```yaml
include:
  - local: 'configs/setup.yml'
  - local: 'configs/build.yml'
  - project: 'shared-ci/templates'
    file: '/test.yml'

stages:
  - setup
  - build
  - test
  - deploy

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to production environment..."
    - ./deploy_production.sh
  only:
    - main
```

###### **Arquivo `configs/setup.yml`**

```yaml
setup_job:
  stage: setup
  script:
    - echo "Setting up environment..."
    - npm install
  artifacts:
    paths:
      - node_modules/
```

###### **Arquivo `configs/build.yml`**

```yaml
build_job:
  stage: build
  script:
    - echo "Building the project..."
    - npm run build
  artifacts:
    paths:
      - dist/
```

###### **Arquivo `templates/test.yml`**

```yaml
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  artifacts:
    paths:
      - reports/
    reports:
      junit: report.xml
```

Neste pipeline:
- Cada etapa é modularizada em diferentes arquivos (`setup.yml`, `build.yml`, `test.yml`), com o arquivo principal incluindo essas configurações.
- Isso permite manter a organização do pipeline, facilitando ajustes em cada etapa sem modificar o arquivo principal.

---

##### **Resumo da Seção em Tabela**

| Termo                | Descrição                                                                                           |
|----------------------|-----------------------------------------------------------------------------------------------------|
| `include`            | Diretiva que incorpora arquivos YAML externos ou templates no pipeline principal.                   |
| Templates Oficiais   | Configurações pré-prontas do GitLab para linguagens e frameworks comuns.                             |
| Templates Customizados | Arquivos YAML personalizados, reutilizáveis em múltiplos projetos para padronização.              |
| Arquivos Modulares   | Arquivos YAML que dividem o pipeline em partes menores e mais fáceis de gerenciar.                  |

---

### **Conclusão da Seção**

O uso de includes e templates no GitLab CI/CD permite organizar e modularizar pipelines, facilitando a reutilização de configurações e a padronização em múltiplos projetos. Com essas técnicas, você consegue manter um pipeline limpo e eficiente, adaptável para necessidades específicas de cada etapa.

---

### **12. Jobs Manuais e Aprovações em Pipelines**


#### **Objetivo**
Explicar como configurar jobs manuais e aprovações no GitLab CI/CD para controle sobre etapas críticas do pipeline, como deploys e rollbacks. Essa prática permite que equipes revisem e aprovem etapas antes de serem executadas, garantindo maior segurança e qualidade nas operações de produção.

---

#### **Conteúdo**

---

##### **12.1 O Que São Jobs Manuais?**

Jobs manuais são jobs configurados para serem executados somente quando autorizados, geralmente para etapas sensíveis como deploy em produção. Eles adicionam uma camada de controle, permitindo que a execução ocorra apenas quando a equipe estiver pronta.

**Exemplo Básico de Job Manual**:
```yaml
stages:
  - build
  - deploy

build_job:
  stage: build
  script:
    - echo "Building project..."

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
  when: manual
```

Neste exemplo:
- `deploy_production` é executado manualmente, permitindo que a equipe aprove o deploy para a branch `main` antes de colocá-lo em produção.

> **Dica**: A configuração `when: manual` é ideal para jobs em ambientes críticos onde o deploy automático pode apresentar riscos.

---

##### **12.2 Configurando Aprovações para Jobs**

Para adicionar uma camada extra de segurança, o GitLab permite configurar aprovadores para jobs manuais. Isso significa que, além de ser manual, o job só será executado após a aprovação dos usuários designados.

**Configurando Aprovações em GitLab**:
1. Acesse **Settings > CI/CD > Pipelines** no projeto GitLab.
2. Na seção **Pipelines must be approved by**, configure os aprovadores para o pipeline.

> **Nota**: As aprovações de jobs manuais são particularmente úteis em equipes grandes ou em fluxos de trabalho que exigem conformidade com políticas de segurança.

---

##### **12.3 Exemplo Completo de Job Manual com Aprovação**

O exemplo abaixo configura um pipeline onde o deploy em produção exige autorização manual e a aprovação de um membro da equipe.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building project..."

test_job:
  stage: test
  script:
    - echo "Running tests..."

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
    - ./deploy_staging.sh
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy_production.sh
  only:
    - main
  when: manual
  allow_failure: false
```

Neste pipeline:
- `deploy_staging` roda automaticamente para a branch `develop`, facilitando os testes em um ambiente de staging.
- `deploy_production` é configurado como manual e é restrito à branch `main`, garantindo que o deploy em produção ocorra apenas quando autorizado pela equipe.
- O parâmetro `allow_failure: false` impede que o job falhe silenciosamente, garantindo visibilidade total do status do deploy.

---

##### **12.4 Implementação de Aprovações Multinível**

O GitLab permite configurar aprovações multinível para jobs críticos, exigindo que múltiplas pessoas revisem e aprovem antes da execução. Esta configuração é útil para ambientes altamente regulamentados onde é necessário conformidade e auditoria de cada etapa.

**Exemplo de Aprovações Multinível no GitLab**:
1. Acesse **Settings > CI/CD > Pipelines**.
2. Adicione múltiplos aprovadores nas configurações de aprovação do pipeline.
3. Cada aprovador precisa aprovar o job antes de ele ser executado.

> **Observação**: Para projetos com aprovações multinível, o job manual só é executado após todos os aprovadores configurados autorizarem a execução.

---

##### **12.5 Uso de `when: delayed` para Jobs Programados**

Além de `when: manual`, o GitLab permite a configuração de `when: delayed` para jobs que precisam ser agendados para execução após um período. Isso é útil para agendar deploys em horários de menor uso ou após o horário comercial.

###### **Exemplo de Job com Delay de 1 Hora**

```yaml
deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
  when: delayed
  start_in: 1 hour
```

Neste exemplo:
- `deploy_production` é agendado para ser executado uma hora após a criação do pipeline.
- `start_in` permite definir o tempo de atraso antes da execução.

> **Dica**: `when: delayed` é útil para deploys programados, mas mantenha a equipe informada sobre o agendamento para garantir que o ambiente esteja preparado.

---

##### **12.6 Exemplo Completo: Pipeline com Jobs Manuais, Aprovações e Delay**

O exemplo abaixo incorpora deploys manuais e atrasados, com jobs que exigem aprovação para diferentes ambientes.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging environment..."
    - ./deploy_staging.sh
  only:
    - develop

deploy_canary:
  stage: deploy
  script:
    - echo "Deploying canary release..."
    - ./deploy_canary.sh
  only:
    - main
  when: manual

deploy_production:
  stage: deploy
  script:
    - echo "Deploying full production release..."
    - ./deploy_production.sh
  only:
    - main
  when: delayed
  start_in: 2 hours
```

Neste pipeline:
- **Deploy Staging**: Roda automaticamente na branch `develop` para testes no ambiente de staging.
- **Deploy Canário**: Configurado como job manual para testes em produção em um subconjunto de servidores.
- **Deploy Completo**: Configurado como `when: delayed`, agendado para execução duas horas após a criação do pipeline.

> **Observação**: Esse pipeline modular permite controle total sobre cada fase do deploy, com a opção de atrasar o deploy completo e monitorar o deploy canário antes de liberar a versão para todos os usuários.

---

##### **Resumo da Seção em Tabela**

| Parâmetro            | Descrição                                                                                           |
|----------------------|-----------------------------------------------------------------------------------------------------|
| `when: manual`       | Define jobs que só serão executados manualmente. Ideal para etapas críticas.                         |
| `when: delayed`      | Permite agendar a execução do job para um tempo futuro.                                             |
| Aprovações           | Configura revisores para jobs manuais, exigindo aprovação para executar o job.                      |
| `start_in`           | Define o tempo de atraso antes da execução do job, utilizado com `when: delayed`.                   |

---

### **Conclusão da Seção**

Com a configuração de jobs manuais e aprovações, você agora tem o controle necessário para executar etapas críticas do pipeline com segurança. Essas configurações são essenciais para ambientes sensíveis onde o deploy direto pode representar um risco e permitem que a equipe tome decisões informadas antes da execução.

---

### **13. Monitoramento e Logs de Execução no Pipeline**

#### **Objetivo**
Explicar como monitorar a execução de jobs e analisar logs no GitLab CI/CD para diagnosticar e solucionar problemas de pipeline. Essa seção mostra como acessar logs detalhados, configurar alertas e integrar ferramentas de monitoramento para rastrear o status e o desempenho dos pipelines.

---

#### **Conteúdo**

---

##### **13.1 Acessando Logs de Execução no GitLab CI/CD**

Os logs de execução são gerados para cada job do pipeline, permitindo rastrear e depurar a execução de scripts e comandos. Esses logs estão disponíveis na interface do GitLab e podem ser acessados conforme o seguinte procedimento:

1. No GitLab, acesse **CI/CD > Pipelines** e clique em um pipeline específico.
2. Clique no job que deseja inspecionar. A interface abrirá os logs detalhados do job selecionado.

Os logs mostram a saída completa dos comandos executados, incluindo mensagens de erro e status de cada etapa. Logs de execução são essenciais para:
- Identificar erros e falhas no código.
- Acompanhar o progresso de cada etapa.
- Diagnosticar problemas de performance.

---

##### **13.2 Configurando Logs Detalhados e Verbosos**

Em alguns casos, é útil habilitar logs mais detalhados para capturar informações adicionais durante a execução. Isso pode ser feito incluindo sinalizadores de debug nos comandos do script.

###### **Exemplo: Log Verboso com Flag de Debug**

```yaml
stages:
  - build

build_job:
  stage: build
  script:
    - echo "Starting build process..."
    - npm install --verbose  # Flag de log detalhado
    - npm run build --debug  # Debug no processo de build
```

Neste exemplo:
- As flags `--verbose` e `--debug` nos comandos `npm` geram logs mais detalhados, ajudando a capturar informações adicionais para solucionar problemas.

> **Dica**: Use logs verbosos somente em jobs específicos, pois logs muito detalhados podem ser difíceis de analisar em jobs complexos.

---

##### **13.3 Configurando Alertas para Falhas no Pipeline**

O GitLab permite configurar notificações para alertar a equipe sobre falhas nos pipelines. Para configurar notificações:
1. Vá até **Settings > Integrations** no projeto.
2. Configure uma integração de notificação (ex.: Slack, email) para receber alertas sobre falhas ou status dos jobs.

Esses alertas garantem que a equipe seja notificada rapidamente sobre qualquer falha no pipeline, permitindo uma resposta rápida para resolução do problema.

---

##### **13.4 Monitoramento de Pipeline com Ferramentas Externas**

O GitLab CI/CD pode ser integrado a ferramentas de monitoramento externas, como **Prometheus** e **Grafana**, para obter uma visualização mais detalhada do desempenho dos pipelines.

###### **Integração com Prometheus**
1. No GitLab, vá para **Settings > Metrics and Monitoring** e ative a integração com Prometheus.
2. Configure o Prometheus para monitorar métricas como:
   - Tempo de execução dos jobs.
   - Número de falhas no pipeline.
   - Recursos consumidos durante a execução.

###### **Exemplo de Métricas Disponíveis**
- **Pipeline Duration**: Duração média do pipeline para identificar gargalos.
- **Job Success Rate**: Taxa de sucesso de cada job para avaliar a estabilidade.

> **Observação**: A integração com Prometheus e Grafana é especialmente útil em projetos complexos, onde o tempo de execução e o consumo de recursos são métricas críticas.

---

##### **13.5 Monitoramento de Erros com Relatórios de Teste**

Relatórios de teste, como relatórios JUnit, podem ser incluídos no pipeline e visualizados na interface do GitLab. Esses relatórios fornecem informações detalhadas sobre falhas nos testes e são ideais para monitorar a qualidade do código.

###### **Exemplo de Job com Relatório JUnit**

```yaml
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test -- --reporter junit --reporter-options output=report.xml
  artifacts:
    paths:
      - report.xml
    reports:
      junit: report.xml
```

Neste exemplo:
- O `test_job` executa os testes e gera um relatório no formato JUnit.
- O GitLab processa e exibe o relatório, facilitando a identificação de testes com falha diretamente na interface.

---

##### **13.6 Exemplo Completo de Pipeline com Monitoramento e Logs de Execução**

Abaixo, um pipeline com configurações de log detalhado, geração de relatórios e alertas integrados para monitoramento completo.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Starting build process..."
    - npm install --verbose
    - npm run build --debug
  artifacts:
    paths:
      - dist/

test_job:
  stage: test
  script:
    - echo "Running unit tests..."
    - npm test -- --reporter junit --reporter-options output=report.xml
  artifacts:
    paths:
      - report.xml
    reports:
      junit: report.xml

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
  when: manual
```

Neste pipeline:
- `build_job` registra logs detalhados durante a instalação e build.
- `test_job` gera um relatório JUnit, que é exibido diretamente na interface do GitLab.
- `deploy_production` é um deploy manual, permitindo monitoramento adicional antes da liberação em produção.

> **Dica**: Configure alertas para o status do pipeline e use logs detalhados em stages críticos para melhorar o monitoramento e a resolução de problemas.

---

##### **Resumo da Seção em Tabela**

| Elemento                | Descrição                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------|
| Logs de Execução        | Registros completos dos comandos executados em cada job, acessíveis na interface do GitLab.         |
| Logs Verbosos           | Logs detalhados ativados com flags de debug nos comandos, para diagnósticos mais profundos.         |
| Alertas de Falhas       | Notificações automáticas para a equipe sobre falhas no pipeline, via email, Slack ou outras integrações. |
| Integração com Prometheus| Coleta métricas detalhadas de execução e uso de recursos do pipeline.                               |
| Relatórios JUnit        | Relatórios de teste em formato JUnit, exibidos no GitLab CI/CD para análise de falhas.               |

---

### **Conclusão da Seção**

Com as ferramentas de monitoramento e logs de execução do GitLab CI/CD, você agora pode acompanhar o desempenho e diagnosticar problemas nos pipelines de maneira mais eficiente. Essas práticas são essenciais para manter a estabilidade e a qualidade do pipeline, especialmente em projetos de grande escala.

---

### **14. Segurança e Boas Práticas no GitLab CI/CD**

#### **Objetivo**
Apresentar práticas de segurança e configurações recomendadas para proteger dados sensíveis e controlar o acesso no GitLab CI/CD. A segurança é fundamental para evitar a exposição de variáveis, senhas e garantir que somente usuários autorizados possam executar ou modificar o pipeline.

---

#### **Conteúdo**

---

##### **14.1 Usando Variáveis Secretas e Protegidas**

As **variáveis secretas** (ou protegidas) permitem armazenar dados confidenciais, como tokens de API, senhas e chaves de acesso, fora do código fonte. No GitLab, essas variáveis podem ser configuradas para estarem acessíveis apenas em branches protegidas, como `main` e `develop`, evitando que dados sensíveis sejam expostos acidentalmente.

**Passo a Passo para Configurar Variáveis Secretas**:
1. Vá até **Settings > CI/CD > Variables** no projeto GitLab.
2. Adicione as variáveis necessárias, como `API_KEY`, `DATABASE_PASSWORD`.
3. Marque as variáveis como **Protected** e **Masked**:
   - **Protected**: Limita o acesso a branches protegidas.
   - **Masked**: Oculta o valor da variável nos logs de execução.

> **Exemplo**: Uma variável `PRODUCTION_API_KEY` protegida e mascarada só estará acessível em pipelines rodando em branches protegidas e será oculta dos logs, evitando exposição inadvertida.

---

##### **14.2 Restringindo Acesso ao Pipeline e Branches Protegidas**

No GitLab, é possível proteger branches e restringir quem pode realizar ações, como merge e deploy, em branches sensíveis, como `main`. Isso evita alterações não autorizadas no código e no pipeline.

**Configurações de Branch Protegida**:
1. Acesse **Settings > Repository > Protected Branches**.
2. Selecione a branch (ex.: `main`) e defina as permissões para:
   - **Quem pode fazer push**: Permitir apenas para mantenedores.
   - **Quem pode fazer merge**: Restrição para membros com permissões específicas.
3. Branches protegidas automaticamente restringem variáveis protegidas, melhorando a segurança.

---

##### **14.3 Implementação de Controle de Acesso Granular com Permissões**

O GitLab CI/CD oferece um controle de acesso baseado em papéis, permitindo que você ajuste permissões para diferentes níveis de acesso ao pipeline.

| Papel             | Permissões Principais                                           |
|-------------------|------------------------------------------------------------------|
| **Guest**         | Acesso de visualização apenas.                                  |
| **Reporter**      | Visualizar logs e histórico de jobs.                            |
| **Developer**     | Executar jobs manuais, criar branches, editar código.           |
| **Maintainer**    | Editar configurações do pipeline, adicionar variáveis protegidas.|
| **Owner**         | Acesso total, incluindo configurações de segurança.             |

> **Dica**: Limite o papel de **Maintainer** ou **Owner** a um número reduzido de usuários, evitando alterações acidentais nas configurações do pipeline.

---

##### **14.4 Boas Práticas para Proteção de Dados no GitLab CI/CD**

1. **Evite Armazenar Dados Sensíveis no Código**: Sempre use variáveis de ambiente para dados confidenciais.
2. **Auditoria Regular de Permissões**: Revise as permissões dos membros do projeto periodicamente para garantir que apenas usuários autorizados tenham acesso a configurações críticas.
3. **Controle de Logs**: Monitore logs regularmente para detectar tentativas de acesso não autorizadas ou falhas de segurança.

> **Exemplo**: Configure alertas de segurança para notificar a equipe quando houver alterações nas configurações de segurança, como adição de novos membros com alto nível de permissão.

---

##### **14.5 Gerenciamento de Tokens de Acesso**

Tokens de acesso, como tokens de deploy e tokens de API, são fundamentais para que o GitLab CI/CD interaja com outros serviços. Tokens devem ser rotacionados periodicamente e configurados com permissões mínimas necessárias.

**Passo a Passo para Criar Tokens Seguros**:
1. Acesse **Settings > Access Tokens**.
2. Defina permissões mínimas (ex.: leitura, escrita) para limitar o escopo do token.
3. Use variáveis protegidas para armazenar tokens de API no GitLab e referenciá-los no `.gitlab-ci.yml` em jobs específicos.

```yaml
deploy_job:
  stage: deploy
  script:
    - echo "Deploying with secure API key..."
    - curl -X POST -H "Authorization: Bearer $PRODUCTION_API_KEY" https://api.example.com/deploy
```

Neste exemplo, o token é acessado por meio de uma variável protegida, e não diretamente no código.

---

##### **14.6 Exemplo Completo de Pipeline Seguro**

O exemplo a seguir demonstra um pipeline com segurança reforçada, utilizando variáveis protegidas, controle de acesso e tokens de API para proteger dados sensíveis.

```yaml
stages:
  - build
  - test
  - deploy

variables:
  DATABASE_PASSWORD: $DATABASE_PASSWORD  # Variável protegida
  PRODUCTION_API_KEY: $PRODUCTION_API_KEY  # Token de API protegido

build_job:
  stage: build
  script:
    - echo "Building the project securely..."
    - npm install
    - npm run build

test_job:
  stage: test
  script:
    - echo "Running secure tests..."
    - npm test

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - curl -X POST -H "Authorization: Bearer $PRODUCTION_API_KEY" https://api.example.com/deploy
  only:
    - main
  when: manual
```

Neste pipeline:
- **Variáveis protegidas** são usadas para dados sensíveis, acessíveis apenas nas branches protegidas.
- **Acesso manual** para o `deploy_production`, evitando deploys não autorizados.
- **Tokens seguros** são aplicados diretamente via variável, protegendo o token de API contra vazamentos.

---

##### **Resumo da Seção em Tabela**

| Prática de Segurança   | Descrição                                                                                         |
|------------------------|---------------------------------------------------------------------------------------------------|
| Variáveis Protegidas   | Armazenam dados sensíveis e limitam o acesso a branches protegidas.                               |
| Branches Protegidas    | Restringem permissões de merge e push, protegendo o pipeline e a integridade do código.           |
| Controle de Acesso     | Limita as ações dos membros com base em papéis específicos, como Guest, Reporter, Developer, etc. |
| Tokens de API Seguros  | Armazenam tokens com permissões mínimas e rotacionados regularmente para proteger dados.          |
| Auditoria Regular      | Revisão periódica de permissões e logs de execução para manter a segurança do pipeline.           |

---

### **Conclusão da Seção**

Com a aplicação das práticas de segurança no GitLab CI/CD, você agora possui um pipeline mais protegido, com controle rígido de acesso e configurações de dados sensíveis. Essas medidas garantem que dados críticos permaneçam seguros e acessíveis apenas por usuários autorizados.

---

### **15. Casos de Uso Avançados e Integração com Ferramentas Externas**

#### **Objetivo**
Explorar casos de uso avançados no GitLab CI/CD, incluindo a integração com ferramentas externas para aumentar a capacidade do pipeline. Veremos como usar Docker para builds de imagem, implementar deploy em Kubernetes e monitorar com Prometheus e Grafana.

---

#### **Conteúdo**

---

##### **15.1 Integração com Docker para Builds e Deploys**

O Docker é amplamente utilizado para criar ambientes isolados e consistentes. Com o GitLab CI/CD, você pode configurar jobs que constroem e publicam imagens Docker diretamente no pipeline.

###### **Exemplo de Job para Build e Push de Imagem Docker**

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_NAME: "myapp"

build_docker_image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$IMAGE_NAME .
    - docker push $CI_REGISTRY_IMAGE:$IMAGE_NAME
  only:
    - main
```

Neste exemplo:
- `docker:dind` é utilizado para habilitar Docker in Docker, permitindo criar imagens Docker no job.
- O job `build_docker_image` realiza login no registro, constrói a imagem e faz o push para o registro do GitLab.

> **Dica**: Configure variáveis `CI_REGISTRY_USER` e `CI_REGISTRY_PASSWORD` no GitLab para armazenar credenciais de login com segurança.

---

##### **15.2 Deploy Automatizado em Kubernetes**

Para realizar o deploy em clusters Kubernetes, o GitLab CI/CD pode ser configurado para aplicar configurações e gerenciar o ciclo de vida de aplicações. Essa integração exige que o GitLab Runner tenha acesso ao cluster Kubernetes, geralmente configurado com um arquivo `kubeconfig`.

###### **Exemplo de Job para Deploy em Kubernetes**

```yaml
stages:
  - deploy

deploy_to_kubernetes:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config set-cluster my-cluster --server=$KUBE_SERVER
    - kubectl config set-credentials gitlab --token=$KUBE_TOKEN
    - kubectl config set-context gitlab --cluster=my-cluster --user=gitlab
    - kubectl config use-context gitlab
    - kubectl apply -f k8s/deployment.yaml
  only:
    - main
```

Neste exemplo:
- `kubectl` é usado para interagir com o Kubernetes.
- As variáveis `KUBE_SERVER` e `KUBE_TOKEN` são definidas no GitLab CI/CD para autenticação no cluster.
- `deployment.yaml` é aplicado para atualizar a aplicação no Kubernetes.

> **Nota**: A integração com Kubernetes oferece flexibilidade para implantar, escalar e gerenciar aplicações em ambientes de produção.

---

##### **15.3 Integração com Prometheus para Monitoramento**

O GitLab CI/CD suporta integração com Prometheus, permitindo monitorar a saúde e o desempenho dos pipelines. Métricas como tempo de execução dos jobs e sucesso ou falha do pipeline podem ser capturadas e visualizadas em dashboards.

**Passo a Passo para Configurar a Integração**:
1. Acesse **Settings > Operations > Metrics and Monitoring** no GitLab.
2. Ative a integração com Prometheus e configure o endpoint de coleta.
3. No Prometheus, configure o GitLab como um alvo para captura de métricas do pipeline.

**Exemplo de Métricas Comuns**:
- **Pipeline Duration**: Tempo médio de execução dos pipelines.
- **Job Success Rate**: Percentual de jobs executados com sucesso.
- **Job Failures**: Número de falhas por job para identificar áreas de instabilidade.

> **Dica**: Use o Prometheus em conjunto com Grafana para visualizar as métricas de maneira mais amigável e compreensível.

---

##### **15.4 Monitoramento Avançado com Grafana**

Grafana permite criar dashboards personalizados para visualizar métricas de Prometheus e outras fontes. Integrar Grafana com GitLab CI/CD proporciona uma visão mais abrangente do status dos pipelines e do consumo de recursos.

###### **Configuração Básica do Grafana com Prometheus**

1. Instale o Grafana e adicione o Prometheus como uma fonte de dados.
2. No painel do Grafana, crie dashboards com gráficos baseados nas métricas de pipeline e desempenho.
3. Configure alertas para notificações em caso de métricas críticas (ex.: aumento de tempo de execução ou falhas repetidas).

> **Nota**: Dashboards Grafana podem ser compartilhados entre equipes, facilitando o monitoramento colaborativo.

---

##### **15.5 Exemplo Completo de Pipeline com Docker, Kubernetes e Monitoramento**

O exemplo a seguir demonstra um pipeline GitLab CI/CD avançado que constrói uma imagem Docker, a implanta em Kubernetes e configura a integração com Prometheus para monitoramento.

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_NAME: "myapp"

build_docker_image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$IMAGE_NAME .
    - docker push $CI_REGISTRY_IMAGE:$IMAGE_NAME
  only:
    - main

deploy_to_kubernetes:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config set-cluster my-cluster --server=$KUBE_SERVER
    - kubectl config set-credentials gitlab --token=$KUBE_TOKEN
    - kubectl config set-context gitlab --cluster=my-cluster --user=gitlab
    - kubectl config use-context gitlab
    - kubectl apply -f k8s/deployment.yaml
  only:
    - main
  environment:
    name: production
    url: https://production.example.com
```

Neste exemplo:
- **Docker** é usado para construir e armazenar imagens Docker no registro do GitLab.
- **Kubernetes** gerencia o deploy em um cluster, utilizando o `kubectl` para aplicar a configuração.
- **Prometheus** monitora a duração e o status dos jobs, integrando-se com o GitLab CI/CD.

> **Dica**: Esse pipeline é ideal para ambientes complexos que exigem automação em Kubernetes, com monitoramento contínuo via Prometheus.

---

##### **Resumo da Seção em Tabela**

| Ferramenta Integrada | Descrição                                                                                          |
|----------------------|----------------------------------------------------------------------------------------------------|
| Docker               | Constrói e armazena imagens Docker, permitindo deploys consistentes e baseados em contêineres.     |
| Kubernetes           | Gerencia o deploy e o escalonamento de aplicações em clusters Kubernetes.                          |
| Prometheus           | Coleta métricas de execução e desempenho do pipeline para monitoramento e análise contínua.        |
| Grafana              | Visualiza métricas e cria dashboards customizados para acompanhar a saúde e o desempenho do pipeline.|

---

### **Conclusão da Seção**

A integração com ferramentas como Docker, Kubernetes, Prometheus e Grafana permite que o GitLab CI/CD se adapte a ambientes avançados de DevOps. Essas integrações aumentam a eficiência e o monitoramento dos pipelines, tornando o processo de deploy e a gestão de ambientes mais ágil e seguro.

---

### Conclusão

A implementação de **pipelines CI/CD com o GitLab** pode transformar a maneira como sua equipe entrega software, reduzindo o tempo de deploy e garantindo uma alta qualidade nas entregas. Este tutorial mostrou como configurar e personalizar pipelines com segurança, desde as etapas iniciais de build e teste até o deploy em produção, com opções para rollback e deploy canário, que permitem gerenciar riscos de atualização.

Ao incorporar ferramentas como Docker e Kubernetes para automação de ambientes e Prometheus e Grafana para monitoramento, você pode criar um pipeline robusto e preparado para desafios complexos de DevOps. Com as práticas e integrações apresentadas, sua equipe estará pronta para implementar CI/CD de forma eficaz, automatizando tarefas repetitivas e minimizando o risco de erros humanos. O GitLab CI/CD é mais do que uma ferramenta de automação; é um aliado na transformação digital e na busca por uma entrega de software ágil, segura e de alta qualidade.

---