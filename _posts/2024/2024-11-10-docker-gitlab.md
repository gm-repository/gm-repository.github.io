---
layout: post
title: "Tutorial Completo: Usando Docker em Pipelines CI/CD no GitLab"
date: 2024-11-10 20:21:22 -0300
categories: [Docker, GitLab, CI-CD, DevOps]
tags: [docker, gitlab, ci-cd, automação, devops]
excerpt: "Aprenda a integrar Docker com GitLab CI/CD para automatizar processos de build, testes e deploys, garantindo eficiência e segurança nos fluxos de trabalho."
---

### Introdução Geral

O Docker, quando integrado ao GitLab CI/CD, torna-se uma poderosa ferramenta de automação para desenvolvimento e operações (DevOps), permitindo construir, testar e implantar aplicações de maneira rápida e eficiente. Neste tutorial, exploramos o processo completo de configuração e uso do Docker no GitLab CI/CD. Desde a configuração inicial do pipeline até práticas avançadas de deploy automatizado, cada passo é detalhado para que desenvolvedores e equipes de DevOps possam maximizar os benefícios dessa combinação poderosa. 

Veremos como configurar o GitLab CI/CD para automação com Docker, desde a estrutura do arquivo `.gitlab-ci.yml`, passando por testes automatizados, deploy seguro e boas práticas de monitoramento, além de exemplos práticos de deploy canário e blue-green. Este tutorial é voltado tanto para iniciantes em CI/CD quanto para profissionais buscando otimizar seus pipelines de automação com segurança e eficiência.

---

### Sumário

1. [Introdução ao CI/CD no GitLab com Docker](#1-introdução-ao-cicd-no-gitlab-com-docker)
2. [Configuração Inicial do GitLab CI/CD](#2-configuração-inicial-do-gitlab-cicd)
3. [Estrutura do Arquivo `.gitlab-ci.yml`](#3-estrutura-do-arquivo-gitlab-ciyml)
4. [Configurando o GitLab Runner com Suporte a Docker](#4-configurando-o-gitlab-runner-com-suporte-a-docker)
5. [Usando Imagens Docker no Pipeline](#5-usando-imagens-docker-no-pipeline)
6. [Construindo e Publicando Imagens Docker](#6-construindo-e-publicando-imagens-docker)
7. [Implementando Testes Automatizados com Docker](#7-implementando-testes-automatizados-com-docker)
8. [Uso de Docker Compose em Pipelines CI/CD](#8-uso-de-docker-compose-em-pipelines-cicd)
9. [Deploy Automatizado de Imagens Docker](#9-deploy-automatizado-de-imagens-docker)
10. [Gerenciamento de Artefatos e Cache](#10-gerenciamento-de-artefatos-e-cache)
11. [Variáveis de Ambiente e Configuração Secreta](#11-variáveis-de-ambiente-e-configuração-secreta)
12. [Pipelines Condicionais e Branches de Deploy](#12-pipelines-condicionais-e-branches-de-deploy)
13. [Integração com Monitoramento e Notificações](#13-integração-com-monitoramento-e-notificações)
14. [Troubleshooting e Resolução de Problemas em CI/CD](#14-troubleshooting-e-resolução-de-problemas-em-cicd)
15. [Casos de Uso Avançados e Práticas Recomendadas](#15-casos-de-uso-avançados-e-práticas-recomendadas)

---

# **1. Introdução ao CI/CD no GitLab com Docker**

## Objetivo
Apresentar os conceitos de CI/CD, os benefícios da automação com GitLab CI/CD e como o Docker pode ser usado para otimizar pipelines de integração e entrega contínua.

## Conteúdo
- **O que é CI/CD e os Benefícios da Automação**
- **Vantagens do Uso do Docker em Pipelines de CI/CD**
- **Estrutura de um Pipeline GitLab e Cenários de Uso para Docker e CI/CD**

---

### **O que é CI/CD e os Benefícios da Automação**

CI/CD (Continuous Integration/Continuous Delivery ou Deployment) é um conjunto de práticas que automatizam o processo de integração de código e implantação de aplicações. Ele ajuda a entregar software com rapidez e confiabilidade ao integrar continuamente as mudanças de código, executando testes e disponibilizando as novas versões para o ambiente de produção ou de staging.

**Benefícios da CI/CD**:
- **Redução de Erros Manuais**: Com a automação de tarefas repetitivas, o CI/CD reduz o risco de erros humanos.
- **Feedback Rápido**: Com testes automatizados, a equipe recebe rapidamente o feedback sobre o impacto de mudanças de código.
- **Deploy Contínuo e Menos Interrupções**: A automatização do deploy reduz o tempo de inatividade e facilita o lançamento de novas versões.

### **Vantagens do Uso do Docker em Pipelines de CI/CD**

A integração com Docker potencializa os pipelines de CI/CD ao fornecer ambientes de execução consistentes e isolados. Isso é especialmente útil para testes automatizados e builds, pois elimina diferenças de ambiente que podem causar falhas imprevisíveis.

**Principais Vantagens do Docker**:
- **Consistência do Ambiente**: Garantia de que o código será executado no mesmo ambiente em qualquer etapa do pipeline.
- **Isolamento**: Containers isolam processos, permitindo testes paralelos e seguros, sem interferência de outras execuções.
- **Escalabilidade**: Docker é altamente escalável e permite fácil replicação de serviços, ideal para CI/CD em larga escala.

### **Estrutura de um Pipeline GitLab e Cenários de Uso para Docker e CI/CD**

No GitLab CI/CD, um pipeline é definido por meio de um arquivo chamado `.gitlab-ci.yml`, onde são descritas as etapas do fluxo de CI/CD em diferentes estágios, como build, test e deploy. Cada etapa é composta por *jobs* (tarefas), e o GitLab orquestra a execução desses jobs com base nas configurações definidas.

#### **Exemplo de Pipeline com Docker no GitLab**

Um pipeline de CI/CD pode conter os seguintes estágios:
1. **Build**: Compilar o código e construir uma imagem Docker com o código mais recente.
2. **Test**: Executar testes automatizados dentro de containers Docker, garantindo isolamento.
3. **Deploy**: Publicar as alterações em um ambiente de staging ou produção usando containers Docker.

**Exemplo Básico de `.gitlab-ci.yml`**:

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .

test:
  stage: test
  image: docker:latest
  script:
    - docker run --rm myapp:latest pytest

deploy:
  stage: deploy
  image: docker:latest
  script:
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
    - docker tag myapp:latest myrepo/myapp:latest
    - docker push myrepo/myapp:latest
```

Neste exemplo:
- **Fase de Build**: O job `build` utiliza o Docker para compilar o código e criar uma imagem.
- **Fase de Test**: O job `test` executa testes automatizados em um container baseado na imagem gerada.
- **Fase de Deploy**: A última etapa faz o deploy, publicando a imagem no repositório de imagens Docker.

### Cenários de Uso para Docker e CI/CD

Com Docker e GitLab CI/CD, é possível:
- **Automatizar a criação de imagens Docker** para que cada commit resulte em uma nova versão da imagem de produção.
- **Executar testes de integração e unitários isolados**, garantindo que o código seja validado em um ambiente estável.
- **Implementar deploys contínuos**, transferindo imagens Docker atualizadas automaticamente para um ambiente de produção ou de staging.

---

### Conclusão

A integração de Docker com GitLab CI/CD oferece um processo automatizado, ágil e seguro para construir, testar e implantar aplicações. A estrutura de pipeline do GitLab permite uma fácil orquestração das etapas e acelera o ciclo de desenvolvimento. No próximo tópico, **Configuração Inicial do GitLab CI/CD**, vamos explorar a criação e configuração de um pipeline básico no GitLab, incluindo variáveis de ambiente e definições iniciais do arquivo `.gitlab-ci.yml`.

---

# **2. Configuração Inicial do GitLab CI/CD**

## Objetivo
Ensinar a configurar um pipeline básico no GitLab CI/CD, criando um projeto e configurando o arquivo `.gitlab-ci.yml`, além de definir variáveis de ambiente para gerenciar dados sensíveis, como tokens e senhas.

## Conteúdo
- **Criação de um Projeto no GitLab**
- **Configuração do Arquivo `.gitlab-ci.yml`**
- **Configuração de Variáveis de Ambiente no GitLab para Dados Sensíveis**
- **Exemplo de Pipeline Básico com Fases de Build, Test e Deploy**

---

### **Criação de um Projeto no GitLab**

1. **Criar um Projeto**:
   - Acesse sua conta GitLab e vá para **Projects > Create New Project**.
   - Selecione a opção de criação de um projeto vazio e insira um nome (por exemplo, `myapp-ci-cd`).
   - Configure a visibilidade do projeto (público ou privado) de acordo com suas necessidades.

2. **Clonar o Projeto Localmente**:
   Após criar o projeto, clone-o no seu ambiente local para começar a adicionar o código e o arquivo `.gitlab-ci.yml`.

   ```bash
   git clone https://gitlab.com/username/myapp-ci-cd.git
   cd myapp-ci-cd
   ```

### **Configuração do Arquivo `.gitlab-ci.yml`**

O arquivo `.gitlab-ci.yml` é o coração do pipeline GitLab CI/CD. Ele define estágios e tarefas que o GitLab executará automaticamente a cada commit ou push no repositório.

1. **Criar o Arquivo `.gitlab-ci.yml`**:
   No diretório do projeto clonado, crie um novo arquivo chamado `.gitlab-ci.yml`.

   ```bash
   touch .gitlab-ci.yml
   ```

2. **Configurar as Fases do Pipeline**:
   Um pipeline no GitLab pode ser dividido em várias fases, como build, test e deploy. Essas fases serão definidas no arquivo `.gitlab-ci.yml` usando a diretiva `stages`.

   ```yaml
   stages:
     - build
     - test
     - deploy
   ```

3. **Adicionar Jobs para Cada Fase**:
   Cada fase terá um ou mais jobs que especificam as ações a serem executadas. Abaixo está uma configuração básica para compilar, testar e fazer o deploy de uma aplicação Docker.

   ```yaml
   build:
     stage: build
     image: docker:latest
     services:
       - docker:dind
     script:
       - docker build -t myapp:latest .

   test:
     stage: test
     image: docker:latest
     script:
       - docker run --rm myapp:latest pytest

   deploy:
     stage: deploy
     image: docker:latest
     script:
       - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
       - docker tag myapp:latest myrepo/myapp:latest
       - docker push myrepo/myapp:latest
   ```

   - **Job Build**: Compila a imagem Docker da aplicação.
   - **Job Test**: Executa testes com o Pytest (ou outro framework de testes) dentro do container.
   - **Job Deploy**: Publica a imagem Docker em um repositório remoto.

### **Configuração de Variáveis de Ambiente no GitLab para Dados Sensíveis**

Variáveis de ambiente são úteis para armazenar dados sensíveis, como credenciais de login e chaves de API, sem incluí-los diretamente no arquivo `.gitlab-ci.yml`.

1. **Acessar as Configurações do Projeto**:
   No GitLab, vá para **Settings > CI / CD > Variables**.

2. **Adicionar Variáveis de Ambiente**:
   Clique em **Add Variable** para adicionar uma nova variável:
   - **Key**: Nome da variável (por exemplo, `DOCKER_USER` e `DOCKER_PASS`).
   - **Value**: Valor da variável (como o nome de usuário e senha do Docker Hub).
   - **Mask Variable**: Ative essa opção para ocultar o valor da variável nos logs do pipeline.
   - **Protected**: Marque se a variável deve ser protegida e usada apenas em branches protegidos.

Essas variáveis estarão disponíveis para os jobs do pipeline, tornando o processo mais seguro e flexível.

### **Exemplo de Pipeline Básico com Fases de Build, Test e Deploy**

Aqui está uma versão consolidada do arquivo `.gitlab-ci.yml` para o pipeline básico:

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .

test:
  stage: test
  image: docker:latest
  script:
    - docker run --rm myapp:latest pytest

deploy:
  stage: deploy
  image: docker:latest
  script:
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
    - docker tag myapp:latest myrepo/myapp:latest
    - docker push myrepo/myapp:latest
```

### Explicação do Pipeline:

- **Fase de Build**: Compila o código em uma imagem Docker.
- **Fase de Test**: Executa testes automatizados dentro de um container.
- **Fase de Deploy**: Faz o login no Docker Hub e publica a imagem no repositório.

---

### Conclusão

Esse pipeline básico oferece uma introdução ao uso do GitLab CI/CD com Docker, automatizando o processo de build, teste e deploy. Com o uso de variáveis de ambiente, as credenciais e dados sensíveis ficam protegidos, tornando o ambiente seguro e eficiente. No próximo tópico, **Estrutura do Arquivo `.gitlab-ci.yml`**, vamos aprofundar a configuração desse arquivo, abordando diretivas e opções avançadas para personalizar os pipelines.

---

# **3. Estrutura do Arquivo `.gitlab-ci.yml`**

## Objetivo
Explicar a estrutura e a sintaxe do arquivo `.gitlab-ci.yml`, abordando as principais diretivas e como configurá-las para criar pipelines eficientes e personalizados no GitLab CI/CD.

## Conteúdo
- **Introdução às Principais Diretivas do `.gitlab-ci.yml`**
- **Criação de um Pipeline com Diferentes Fases**
- **Uso de Artefatos e Cache para Otimização de Builds**

---

### **Introdução às Principais Diretivas do `.gitlab-ci.yml`**

O `.gitlab-ci.yml` é o arquivo de configuração do GitLab CI/CD, onde você define as etapas e os comandos do pipeline. As principais diretivas incluem:

1. **`stages`**: Define as fases do pipeline (por exemplo, build, test, deploy) e a ordem de execução.
2. **`jobs`**: Cada fase é composta por jobs, que são as tarefas executadas pelo GitLab.
3. **`script`**: Lista de comandos executados em cada job.
4. **`image`**: Especifica a imagem Docker base para o job.
5. **`services`**: Define serviços adicionais, como `docker:dind` para executar Docker no pipeline.
6. **`only` e `except`**: Controla a execução dos jobs em branches, tags ou condições específicas.
7. **`artifacts`**: Especifica os arquivos que serão salvos e compartilhados entre jobs.
8. **`cache`**: Define arquivos e diretórios que devem ser armazenados em cache, acelerando builds futuros.

### **Criação de um Pipeline com Diferentes Fases**

Abaixo, configuramos um pipeline básico com fases de build, test e deploy, usando diferentes diretivas para personalizar cada job.

```yaml
stages:
  - build
  - test
  - deploy

# Job de Build
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .
  only:
    - main  # Executa apenas em commits no branch 'main'

# Job de Test
test:
  stage: test
  image: docker:latest
  script:
    - docker run --rm myapp:latest pytest
  except:
    - tags  # Não executa em commits com tags

# Job de Deploy
deploy:
  stage: deploy
  image: docker:latest
  script:
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
    - docker tag myapp:latest myrepo/myapp:latest
    - docker push myrepo/myapp:latest
  only:
    - tags  # Executa apenas em commits com tags
```

#### **Explicação das Diretivas Usadas**:

- **`stages`**: Define a ordem das fases: primeiro o build, depois o test, e por fim o deploy.
- **`only` e `except`**: Controle de condições:
  - `only: main`: O job `build` é executado apenas no branch `main`.
  - `except: tags`: O job `test` não é executado quando um commit é tagueado.
  - `only: tags`: O job `deploy` só executa em commits que contêm uma tag, ideal para deploys de versões específicas.

### **Uso de Artefatos e Cache para Otimização de Builds**

O GitLab CI/CD permite compartilhar arquivos e diretórios entre jobs usando **artifacts** e **cache**. Artefatos são arquivos gerados por um job que podem ser utilizados em outro, enquanto o cache armazena dados que não mudam frequentemente, acelerando o pipeline.

#### **Uso de Artefatos**

O `artifacts` armazena arquivos de um job para serem usados em outros, como relatórios de teste ou builds intermediários.

```yaml
test:
  stage: test
  image: docker:latest
  script:
    - docker run --rm myapp:latest pytest > test_report.txt
  artifacts:
    paths:
      - test_report.txt
    expire_in: 1 week  # Define o tempo de retenção do artefato
```

Neste exemplo:
- **`artifacts.paths`**: Salva o arquivo `test_report.txt` como artefato para outros jobs.
- **`expire_in`**: Define que o artefato expirará em uma semana, economizando espaço de armazenamento.

#### **Uso de Cache**

O `cache` armazena dependências que podem ser reutilizadas em jobs futuros, reduzindo o tempo de build.

```yaml
build:
  stage: build
  image: node:14
  script:
    - npm install
    - npm run build
  cache:
    paths:
      - node_modules/
```

Neste exemplo:
- **`cache.paths`**: Armazena o diretório `node_modules/` em cache para que futuros jobs de build não precisem instalar as dependências novamente.

---

### Conclusão

Com essas diretivas, você pode configurar pipelines mais otimizados e adaptados ao seu fluxo de trabalho, controlando quando e onde os jobs são executados, além de compartilhar arquivos entre eles. No próximo tópico, **Configurando o GitLab Runner com Suporte a Docker**, veremos como configurar um GitLab Runner para executar jobs com Docker, incluindo práticas de segurança e configuração.

---

# **4. Configurando o GitLab Runner com Suporte a Docker**

## Objetivo
Demonstrar a instalação e configuração de um GitLab Runner com Docker executor, permitindo que os pipelines GitLab CI/CD executem jobs em containers. Este tópico também abordará dicas de segurança para configurar permissões e acessos.

## Conteúdo
- **O Que é o GitLab Runner e Sua Função no CI/CD**
- **Instalação e Registro do GitLab Runner**
- **Configuração do Docker Executor**
- **Dicas de Segurança para GitLab Runner com Docker Executor**

---

### **O Que é o GitLab Runner e Sua Função no CI/CD**

O **GitLab Runner** é uma aplicação que executa os jobs dos pipelines definidos no arquivo `.gitlab-ci.yml`. Ele atua como um “agente” do GitLab CI/CD, conectando-se ao GitLab e executando os comandos especificados em cada job do pipeline. Os Runners podem ser configurados para diferentes ambientes e tipos de executor, sendo o **Docker executor** uma das opções mais populares devido ao isolamento, escalabilidade e flexibilidade que oferece.

### **Instalação e Registro do GitLab Runner**

#### **1. Instalação do GitLab Runner**

Para instalar o GitLab Runner em uma máquina com Linux, siga os passos abaixo. O GitLab Runner também está disponível para Windows e macOS, mas o processo pode variar ligeiramente.

```bash
# Adiciona o repositório oficial do GitLab Runner
curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

# Define permissões de execução
chmod +x /usr/local/bin/gitlab-runner

# Registra o GitLab Runner como um serviço
useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
install -o gitlab-runner -g gitlab-runner -d /home/gitlab-runner
```

Para iniciar o serviço:
```bash
systemctl enable gitlab-runner
systemctl start gitlab-runner
```

#### **2. Registro do GitLab Runner**

Para conectar o Runner ao seu projeto GitLab, registre-o com o token de autenticação disponível nas configurações do GitLab:

1. No GitLab, vá para **Settings > CI / CD > Runners > Set up a specific Runner manually**.
2. Copie o token de registro e execute o seguinte comando no terminal da máquina onde o GitLab Runner foi instalado:

```bash
gitlab-runner register
```

3. Durante o processo de registro, você será solicitado a fornecer algumas informações:

   - **URL do GitLab**: A URL base do seu GitLab (ex.: `https://gitlab.com`).
   - **Token**: Cole o token de registro do GitLab.
   - **Descrição**: Descrição do Runner (ex.: `Docker Runner`).
   - **Tags**: Tags para identificar o Runner, como `docker`.
   - **Executor**: Escolha `docker` como o executor.

### **Configuração do Docker Executor**

Com o Docker executor, o GitLab Runner executa cada job dentro de um container Docker. Isso permite que cada job tenha um ambiente isolado, proporcionando segurança e consistência.

1. **Especificar a Imagem Base do Docker**:
   Durante o registro, você pode definir uma imagem Docker padrão. No entanto, é possível especificar a imagem em cada job no arquivo `.gitlab-ci.yml` com a diretiva `image`.

2. **Configurar o Docker-in-Docker (DinD)**:
   Se os jobs exigirem acesso ao Docker, como quando um pipeline executa `docker build` e `docker push`, habilite o **Docker-in-Docker (DinD)** para que os containers possam executar o Docker.

   No arquivo `.gitlab-ci.yml`, adicione o serviço `docker:dind` ao job:

   ```yaml
   build:
     stage: build
     image: docker:latest
     services:
       - docker:dind
     script:
       - docker build -t myapp:latest .
       - docker push myrepo/myapp:latest
   ```

3. **Configurar Recursos e Limites**:
   No arquivo de configuração do Runner (`config.toml`), você pode definir limites de CPU, memória e tempo de execução para cada job, garantindo que o Runner não consuma todos os recursos do sistema.

   ```toml
   [[runners]]
     name = "docker-runner"
     url = "https://gitlab.com/"
     token = "your-runner-token"
     executor = "docker"
     [runners.custom_build_dir]
     [runners.docker]
       tls_verify = false
       image = "docker:latest"
       privileged = true
       disable_entrypoint_overwrite = false
       oom_kill_disable = false
       shm_size = 0
   ```

### **Dicas de Segurança para GitLab Runner com Docker Executor**

1. **Habilite o Modo Privileged com Cuidado**:
   A configuração `privileged=true` permite o uso do Docker-in-Docker, mas expõe o host a maiores riscos. Use-a apenas em ambientes controlados e seguros.

2. **Isolamento de Runners para Repositórios Públicos e Privados**:
   Para repositórios públicos, configure Runners dedicados e restrinja o acesso para evitar o uso não autorizado.

3. **Limitar o Uso de Recursos**:
   Defina limites de uso de CPU e memória para evitar que um job consuma recursos excessivos e afete outros processos.

4. **Atualizações de Segurança**:
   Mantenha o Docker, o GitLab Runner e as imagens Docker atualizados com as últimas versões para proteger contra vulnerabilidades.

---

### Conclusão

Com o Docker executor, o GitLab Runner se torna uma poderosa ferramenta para executar jobs em containers, proporcionando ambientes de execução consistentes e isolados. Configurar o Docker-in-Docker e aplicar práticas de segurança são passos importantes para otimizar o uso do Runner em pipelines CI/CD. No próximo tópico, **Usando Imagens Docker no Pipeline**, veremos como especificar e utilizar imagens Docker personalizadas e preexistentes em cada job do GitLab CI/CD.

---

# **5. Usando Imagens Docker no Pipeline**

## Objetivo
Ensinar a usar imagens Docker personalizadas e preexistentes em jobs do GitLab CI/CD, permitindo configurar ambientes específicos e otimizados para diferentes fases do pipeline.

## Conteúdo
- **Uso da Diretiva `image` para Especificar Imagem Base**
- **Exemplo de Pipeline com Diferentes Imagens para Cada Estágio**
- **Como Escolher Imagens Leves e Otimizadas para CI/CD**

---

### **Uso da Diretiva `image` para Especificar Imagem Base**

O GitLab CI/CD permite que cada job seja executado em um ambiente Docker configurado, especificado pela diretiva `image` no arquivo `.gitlab-ci.yml`. A imagem define o ambiente em que o script do job será executado, incluindo sistema operacional, ferramentas e bibliotecas.

#### **Exemplo de Uso da Diretiva `image`**

```yaml
build:
  stage: build
  image: node:14
  script:
    - npm install
    - npm run build
```

Neste exemplo:
- **`image: node:14`**: Configura o ambiente com a imagem `node:14`, que contém o Node.js na versão 14, ideal para builds de aplicações JavaScript ou Node.js.

A diretiva `image` permite especificar imagens para cada job, otimizando o ambiente para diferentes linguagens ou dependências. Imagens com ferramentas pré-instaladas economizam tempo no pipeline, pois evitam a instalação de pacotes desnecessários.

### **Exemplo de Pipeline com Diferentes Imagens para Cada Estágio**

Utilizar imagens diferentes para cada estágio do pipeline é uma prática recomendada para CI/CD, pois permite otimizar o ambiente conforme a fase do processo, reduzindo o tempo e o consumo de recursos.

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  image: maven:3.6-jdk-11
  script:
    - mvn clean install

test:
  stage: test
  image: openjdk:11
  script:
    - java -jar target/myapp.jar --test

deploy:
  stage: deploy
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
    - docker tag myapp:latest myrepo/myapp:latest
    - docker push myrepo/myapp:latest
```

Neste pipeline:
- **Fase de Build**: Usa a imagem `maven:3.6-jdk-11`, que inclui o Maven e o JDK 11, ideal para construir projetos Java.
- **Fase de Test**: Usa a imagem `openjdk:11`, com Java pré-instalado, reduzindo o tamanho do ambiente para testes e eliminando dependências de build.
- **Fase de Deploy**: Utiliza a imagem `docker:latest` com `docker:dind` como serviço, permitindo construir e enviar a imagem Docker do projeto para o repositório.

### **Como Escolher Imagens Leves e Otimizadas para CI/CD**

Imagens otimizadas para CI/CD reduzem o tempo de build e o uso de recursos. Seguem algumas práticas recomendadas para selecionar e criar imagens ideais para pipelines:

1. **Escolha Imagens Officiais e Estáveis**:
   - Use imagens Docker oficiais, como `node`, `python`, `alpine`, `maven`, que são mantidas pela comunidade e atualizadas com segurança.
   - Verifique as tags `stable` ou de versão específica (ex.: `node:14`) para evitar que mudanças não planejadas afetem o pipeline.

2. **Use Imagens Base Leves, como `alpine`**:
   - Imagens baseadas em `alpine` são mais leves e rápidas de baixar, como `python:3.9-alpine` ou `node:14-alpine`.
   - No entanto, algumas bibliotecas podem precisar de ajustes, pois `alpine` usa `musl` em vez de `glibc`.

3. **Crie Imagens Customizadas para o Projeto**:
   - Para projetos complexos que requerem muitas dependências, crie uma imagem Docker customizada com todas as dependências.
   - Salve a imagem em um repositório como o GitLab Container Registry para acesso rápido pelo pipeline.

#### **Exemplo de Dockerfile para Imagem Customizada**

```dockerfile
# Dockerfile personalizado para builds de CI/CD
FROM node:14-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .
CMD ["npm", "run", "start"]
```

Salve essa imagem customizada no GitLab Container Registry ou Docker Hub, e depois use-a no pipeline:

```yaml
build:
  stage: build
  image: registry.gitlab.com/myuser/myapp:ci
  script:
    - npm run build
```

---

### Conclusão

O uso de diferentes imagens Docker nos jobs do pipeline oferece flexibilidade e permite criar ambientes otimizados para cada fase. Imagens customizadas ou leves, como `alpine`, podem acelerar significativamente o pipeline, enquanto imagens pré-configuradas economizam tempo e reduzem erros. No próximo tópico, **Construindo e Publicando Imagens Docker**, vamos configurar o pipeline para criar e enviar automaticamente imagens Docker para um registro, automatizando o processo de build e deploy.

---

# **6. Construindo e Publicando Imagens Docker**

## Objetivo
Demonstrar como configurar o GitLab CI/CD para construir e publicar automaticamente imagens Docker personalizadas, permitindo a automação do deploy de novas versões de aplicativos em um repositório de imagens, como o GitLab Container Registry ou Docker Hub.

## Conteúdo
- **Criando um Dockerfile para Imagem Customizada**
- **Comandos `docker build` e `docker push` no Pipeline**
- **Configuração do GitLab Container Registry para Armazenamento de Imagens Docker**

---

### **Criando um Dockerfile para Imagem Customizada**

O primeiro passo para construir uma imagem Docker no pipeline é criar um **Dockerfile** que defina a estrutura e as dependências do aplicativo. O Dockerfile especifica qual imagem base usar, as dependências, as variáveis de ambiente e os comandos para inicializar o aplicativo.

#### **Exemplo de Dockerfile para um Aplicativo Node.js**

```dockerfile
# Define uma imagem base
FROM node:14-alpine

# Define o diretório de trabalho no container
WORKDIR /app

# Copia o arquivo de dependências para o container
COPY package*.json ./

# Instala as dependências do projeto
RUN npm install

# Copia o código fonte para o container
COPY . .

# Define o comando de inicialização
CMD ["npm", "start"]
```

Esse Dockerfile cria uma imagem Docker com o Node.js e as dependências do aplicativo, executando-o no diretório `/app`.

### **Comandos `docker build` e `docker push` no Pipeline**

Para construir e publicar uma imagem Docker, configure o GitLab CI/CD para executar os comandos `docker build` e `docker push`. Esses comandos serão definidos no arquivo `.gitlab-ci.yml`.

#### **Exemplo de Pipeline para Build e Push de Imagem Docker**

O exemplo a seguir mostra um pipeline básico para construir e enviar uma imagem Docker para um registro:

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
```

#### **Explicação do Pipeline**:

- **`IMAGE_TAG`**: Define uma variável que gera a tag da imagem com base no commit e no repositório GitLab.
- **Login no Registro**: O comando `docker login` autentica o Runner no repositório de imagens. As credenciais são armazenadas nas variáveis de ambiente (`CI_REGISTRY_USER` e `CI_REGISTRY_PASSWORD`) configuradas nas configurações do GitLab.
- **`docker build`**: Constrói a imagem Docker com base no Dockerfile no diretório do projeto.
- **`docker push`**: Envia a imagem Docker para o registro, tornando-a acessível para deploys e outras equipes.

### **Configuração do GitLab Container Registry para Armazenamento de Imagens Docker**

O GitLab Container Registry é um repositório Docker integrado ao GitLab que permite armazenar imagens Docker diretamente no projeto, facilitando o acesso e o controle de versões.

#### **Configurando o Registro do Container**

1. **Ativar o GitLab Container Registry**:
   No projeto GitLab, vá para **Settings > Packages & Registries > Container Registry** e ative o Container Registry se ele ainda não estiver habilitado.

2. **Configurar Variáveis de Ambiente**:
   Para o GitLab CI/CD acessar o registro, defina variáveis de ambiente para o nome de usuário, senha e URL do registro:
   
   - **CI_REGISTRY**: A URL do registro, que geralmente é `registry.gitlab.com`.
   - **CI_REGISTRY_USER** e **CI_REGISTRY_PASSWORD**: Informações de login do GitLab. No caso de usuários pessoais, use seu nome de usuário e uma senha de token pessoal gerada em **Settings > Access Tokens**.

3. **Acessar a Imagem no GitLab Registry**:
   Após o `docker push`, a imagem será armazenada no GitLab Container Registry e poderá ser visualizada na interface do GitLab, acessando **Packages & Registries > Container Registry**.

#### **Configuração de Tagging e Versionamento de Imagens**

Usar tags como o commit SHA ou o nome do branch permite versionar cada imagem com base na alteração de código. A variável `$CI_COMMIT_REF_SLUG` é especialmente útil para referenciar o branch ou tag do commit, criando uma imagem exclusiva para cada versão.

**Exemplo com Tag Dinâmica**:

```yaml
variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

script:
  - docker build -t $IMAGE_TAG .
  - docker push $IMAGE_TAG
```

### Publicando Imagens no Docker Hub (Opcional)

Para publicar a imagem no Docker Hub, configure variáveis de ambiente para o Docker Hub (ex.: `DOCKER_USER` e `DOCKER_PASS`) e substitua o login e a URL do repositório no script:

```yaml
deploy:
  stage: deploy
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
    - docker build -t myrepo/myapp:$CI_COMMIT_SHORT_SHA .
    - docker push myrepo/myapp:$CI_COMMIT_SHORT_SHA
```

---

### Conclusão

Com esses passos, o pipeline GitLab CI/CD pode construir e publicar automaticamente imagens Docker, acelerando o processo de deploy e assegurando consistência nas versões. A utilização de registros de imagem, como o GitLab Container Registry ou Docker Hub, permite que as imagens estejam acessíveis para deploys em diferentes ambientes. No próximo tópico, **Implementando Testes Automatizados com Docker**, vamos explorar como configurar pipelines de testes automatizados usando containers Docker para validar a qualidade do código antes do deploy.

---

# **7. Implementando Testes Automatizados com Docker**

## Objetivo
Demonstrar como configurar pipelines de testes automatizados com Docker no GitLab CI/CD, permitindo a execução de testes unitários e de integração em containers isolados para garantir a qualidade do código.

## Conteúdo
- **Execução de Testes Unitários e de Integração em Containers Docker**
- **Exemplo de Pipeline com Fases de Build e Test**
- **Uso de Containers de Banco de Dados para Testes de Integração**

---

### **Execução de Testes Unitários e de Integração em Containers Docker**

Docker permite executar testes em um ambiente isolado, garantindo que as dependências e o sistema subjacente sejam consistentes em todos os ambientes de desenvolvimento e produção. Para cada job de teste, podemos usar containers para rodar o aplicativo e suas dependências (ex.: bancos de dados ou outros serviços) sem instalar nada diretamente no Runner.

#### **Benefícios do Docker para Testes Automatizados**:
- **Ambiente Consistente**: Elimina problemas relacionados ao ambiente, pois todos os testes são executados em containers com as mesmas configurações.
- **Isolamento**: Cada job pode rodar em um container separado, evitando conflitos entre diferentes testes.
- **Configuração Rápida**: Containers são iniciados rapidamente, o que acelera o ciclo de teste.

### **Exemplo de Pipeline com Fases de Build e Test**

Aqui está um exemplo de pipeline onde a aplicação é construída e testada em diferentes jobs. A fase de build cria a imagem Docker, e o job de teste executa os testes de integração dentro de um container baseado na imagem criada.

```yaml
stages:
  - build
  - test

# Fase de Build: Constrói a Imagem Docker da Aplicação
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .

# Fase de Test: Executa Testes Automatizados em um Container
test:
  stage: test
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker run --rm myapp:latest pytest
```

Neste exemplo:
- **Job Build**: Cria a imagem `myapp:latest`, que será usada no job de teste.
- **Job Test**: Executa o Pytest (ou outra ferramenta de testes) dentro de um container, isolando o ambiente de testes.

### **Uso de Containers de Banco de Dados para Testes de Integração**

Para testes de integração, você pode precisar de um banco de dados para validar como o código interage com um sistema externo. Docker facilita a execução de containers com bancos de dados como MySQL, PostgreSQL e MongoDB, configurando-os automaticamente para os testes.

#### **Exemplo de Testes de Integração com Banco de Dados MySQL**

No exemplo abaixo, um container MySQL é iniciado como serviço no GitLab CI/CD, permitindo que o job de teste se conecte a ele. A aplicação e o banco de dados podem se comunicar pela rede Docker interna.

```yaml
stages:
  - build
  - test

# Fase de Build
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .

# Fase de Teste com Banco de Dados
test:
  stage: test
  image: docker:latest
  services:
    - docker:dind
    - name: mysql:5.7  # Serviço de banco de dados
      alias: db  # Alias que a aplicação usará para se conectar
  variables:
    MYSQL_DATABASE: testdb
    MYSQL_ROOT_PASSWORD: root_password
  script:
    - docker run --rm --network="$CI_NETWORK_NAME" -e DB_HOST=db -e DB_NAME=testdb -e DB_PASS=root_password myapp:latest pytest
```

#### **Explicação do Pipeline**:

- **Serviço MySQL**: O serviço MySQL é adicionado ao job `test` com a imagem `mysql:5.7`. A aplicação se comunica com o banco usando o alias `db`.
- **Variáveis de Ambiente do Banco de Dados**: As variáveis `MYSQL_DATABASE` e `MYSQL_ROOT_PASSWORD` são definidas para configurar o banco.
- **Testes de Integração**: O comando `pytest` executa os testes, passando variáveis de ambiente para conectar-se ao banco MySQL.

Essa configuração permite que os testes de integração sejam executados em um ambiente isolado com um banco de dados totalmente configurado.

---

### Conclusão

Ao integrar testes automatizados no pipeline GitLab CI/CD com Docker, é possível garantir a qualidade do código em ambientes consistentes e isolados. A adição de containers de banco de dados e outras dependências no pipeline oferece flexibilidade para testes de integração, validando a funcionalidade da aplicação em um ambiente próximo ao de produção. No próximo tópico, **Uso de Docker Compose em Pipelines CI/CD**, exploraremos como configurar e executar ambientes multi-container em pipelines utilizando Docker Compose.

---

# **8. Uso de Docker Compose em Pipelines CI/CD**

## Objetivo
Aprender a usar o Docker Compose em pipelines GitLab CI/CD para orquestrar ambientes multi-container, permitindo a configuração e execução de ambientes de teste complexos que envolvem múltiplos serviços.

## Conteúdo
- **Configuração de `docker-compose.yml` para Serviços Multi-Container**
- **Execução de `docker-compose` no GitLab Runner**
- **Exemplo de Cenário Multi-Container com `docker-compose` para Build e Teste**

---

### **Configuração de `docker-compose.yml` para Serviços Multi-Container**

O Docker Compose permite definir e executar ambientes que envolvem múltiplos containers. Em um ambiente de CI/CD, o Docker Compose pode ser usado para configurar testes de integração e builds complexos, onde diferentes serviços dependem um do outro (como uma aplicação que depende de um banco de dados e de um serviço de cache).

#### **Exemplo de Arquivo `docker-compose.yml`**

Aqui está um exemplo básico de um arquivo `docker-compose.yml` para uma aplicação Node.js que interage com um banco de dados MySQL e um serviço de cache Redis.

```yaml
version: '3.8'

services:
  app:
    image: node:14
    build:
      context: .
      dockerfile: Dockerfile
    environment:
      - DB_HOST=db
      - REDIS_HOST=cache
    networks:
      - app-network

  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root_password
      - MYSQL_DATABASE=testdb
    networks:
      - app-network

  cache:
    image: redis:alpine
    networks:
      - app-network

networks:
  app-network:
```

Neste exemplo:
- **`app`**: Serviço que usa a imagem Node.js e depende do MySQL e do Redis.
- **`db`**: Serviço de banco de dados MySQL.
- **`cache`**: Serviço de cache Redis.
- **`app-network`**: Rede Docker personalizada para comunicação entre os serviços.

### **Execução de `docker-compose` no GitLab Runner**

Para rodar o Docker Compose no GitLab CI/CD, configure o GitLab Runner com Docker e o **Docker-in-Docker (DinD)**, permitindo que o pipeline gerencie containers.

1. **Adicione `docker:dind` como Serviço**: Isso permite que o Docker Compose funcione corretamente no pipeline.
2. **Especifique a Imagem `docker-compose` no Job**: Use uma imagem com Docker Compose ou instale o Docker Compose durante o pipeline.

#### **Exemplo de Configuração de Pipeline com Docker Compose**

```yaml
stages:
  - build
  - test

variables:
  DOCKER_DRIVER: overlay2
  COMPOSE_PROJECT_NAME: myapp

# Fase de Build
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker-compose -f docker-compose.yml build

# Fase de Teste com Docker Compose
test:
  stage: test
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker-compose -f docker-compose.yml up -d
    - docker-compose exec app npm test
    - docker-compose down
```

Neste exemplo:
- **Fase de Build**: Executa o `docker-compose build`, criando todas as imagens especificadas no `docker-compose.yml`.
- **Fase de Teste**: Sobe os serviços em modo `detached` (`up -d`), executa os testes dentro do container `app`, e finaliza com `docker-compose down` para limpar os containers.

### **Exemplo de Cenário Multi-Container com `docker-compose` para Build e Teste**

Para cenários mais complexos, onde múltiplos serviços interagem, o Docker Compose facilita o teste da aplicação em um ambiente integrado.

#### **Pipeline de Exemplo para Aplicação Multi-Container**

Este pipeline executa um teste de integração completo em um ambiente com vários serviços.

```yaml
stages:
  - setup
  - integration-test

variables:
  DOCKER_DRIVER: overlay2

setup:
  stage: setup
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker-compose -f docker-compose.yml build

integration-test:
  stage: integration-test
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker-compose -f docker-compose.yml up -d
    - docker-compose exec app npm run integration-test
    - docker-compose down
```

#### **Descrição do Pipeline**:
- **Setup**: Constrói as imagens Docker de todos os serviços antes dos testes.
- **Integration Test**: Sobe todos os serviços e executa os testes de integração. Após os testes, derruba o ambiente com `docker-compose down` para liberar os recursos.

Esse fluxo é útil para projetos que exigem múltiplos serviços para testes de integração, simulando um ambiente próximo ao de produção.

---

### Conclusão

O uso do Docker Compose no GitLab CI/CD permite orquestrar ambientes de teste complexos e multi-container, como aplicações que dependem de bancos de dados e caches. Isso facilita a execução de testes de integração e builds de maneira eficiente e consistente. No próximo tópico, **Deploy Automatizado de Imagens Docker**, exploraremos como configurar um job de deploy que publica automaticamente as imagens Docker em ambientes de produção ou staging.

---

# **9. Deploy Automatizado de Imagens Docker**

## Objetivo
Configurar a etapa de deploy no GitLab CI/CD para realizar a implantação automática de imagens Docker em ambientes de produção ou staging, utilizando ferramentas e práticas que garantem segurança e reversibilidade.

## Conteúdo
- **Configuração de Job para Deploy com `docker pull` e `docker run`**
- **Automação de Deploy para Clusters de Containers com Docker Swarm ou Kubernetes**
- **Boas Práticas para Deploy Seguro e Reversível**

---

### **Configuração de Job para Deploy com `docker pull` e `docker run`**

O GitLab CI/CD permite configurar jobs para deploy automatizado em servidores de produção ou staging. Nesta etapa, você pode realizar o `pull` da imagem Docker que foi publicada no registro (como o GitLab Container Registry) e executá-la com o `docker run` em servidores configurados para esse fim.

#### **Exemplo de Configuração Básica de Job para Deploy**

No exemplo a seguir, o job de deploy faz o login no registro, baixa a imagem mais recente e a executa em um servidor de destino.

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

deploy:
  stage: deploy
  image: docker:latest
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker pull $IMAGE_TAG
    - docker stop myapp || true
    - docker rm myapp || true
    - docker run -d --name myapp -p 80:80 $IMAGE_TAG
  only:
    - main
```

#### **Explicação do Pipeline**:

- **Job Build**: Constrói e publica a imagem Docker.
- **Job Deploy**:
  - **docker login**: Autentica no registro do GitLab para permitir o download da imagem.
  - **docker pull**: Baixa a imagem Docker da última versão para o servidor.
  - **docker stop e docker rm**: Interrompe e remove o container anterior (se existir).
  - **docker run**: Executa a nova versão da imagem em um container, publicando-a na porta 80.

### **Automação de Deploy para Clusters de Containers com Docker Swarm ou Kubernetes**

Para projetos de larga escala, Docker Swarm e Kubernetes oferecem uma infraestrutura para gerenciar e automatizar o deploy de containers em clusters distribuídos. Esses orquestradores fornecem balanceamento de carga, tolerância a falhas e escalabilidade.

#### **Deploy Automatizado com Docker Swarm**

No Docker Swarm, você pode criar um serviço usando o `docker service update` para implementar uma nova versão da imagem no cluster.

```yaml
deploy:
  stage: deploy
  image: docker:latest
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker pull $IMAGE_TAG
    - docker service update --image $IMAGE_TAG myapp_service
  only:
    - main
```

Esse script atualiza o serviço `myapp_service` com a nova imagem no Docker Swarm, aplicando o *rolling update* para minimizar o downtime.

#### **Deploy Automatizado com Kubernetes**

Com o Kubernetes, o deploy é realizado através de `kubectl`, que controla o cluster e aplica novas configurações de maneira automática.

1. **Instale o `kubectl` no GitLab Runner**: Certifique-se de que o `kubectl` esteja instalado e configurado no Runner.
2. **Defina as Credenciais do Cluster Kubernetes**: No GitLab CI/CD, configure as variáveis de ambiente para autenticação com o cluster Kubernetes (ex.: `KUBECONFIG`).

**Exemplo de Job para Deploy com Kubernetes**:

```yaml
deploy:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - echo "$KUBE_CONFIG" | base64 -d > ~/.kube/config
    - kubectl set image deployment/myapp myapp=$IMAGE_TAG
  only:
    - main
```

Neste exemplo:
- **`kubectl set image`**: Atualiza o container `myapp` no deployment Kubernetes com a nova imagem.
- **Variável `KUBE_CONFIG`**: Contém o arquivo de configuração do cluster Kubernetes, armazenado como uma variável protegida no GitLab.

### **Boas Práticas para Deploy Seguro e Reversível**

1. **Configurar Deploys para Ambientes Separados**: Execute deploys iniciais em um ambiente de staging para validação antes de promover a versão para produção.
2. **Implementar Rolling Updates e Rollbacks**: Ferramentas como Docker Swarm e Kubernetes suportam rolling updates, que permitem a atualização gradual das instâncias. Configure o rollback automático para restaurar a versão anterior em caso de falhas.
3. **Usar Tags de Versionamento**: Tags e versões de imagem facilitam a reversão a versões anteriores sem necessidade de reconstruir a imagem.

---

### Conclusão

Automatizar o deploy de imagens Docker no GitLab CI/CD agiliza o processo de entrega e melhora a consistência das versões. Ao utilizar Docker Swarm ou Kubernetes para clusters de containers, é possível escalar a aplicação e implementar políticas de atualização seguras, garantindo alta disponibilidade. No próximo tópico, **Gerenciamento de Artefatos e Cache**, exploraremos como otimizar o pipeline usando artefatos e cache para reduzir o tempo de build e reaproveitar dados entre jobs.

---

# **10. Gerenciamento de Artefatos e Cache**

## Objetivo
Explicar o uso de artefatos e cache no GitLab CI/CD para otimizar os pipelines, reduzindo o tempo de build e compartilhando dados entre jobs.

## Conteúdo
- **Diretivas `artifacts` e `cache` no `.gitlab-ci.yml`**
- **Como Compartilhar Arquivos e Diretórios entre Jobs**
- **Exemplo de Pipeline com Cache para Acelerar Builds de Dependências**

---

### **Diretivas `artifacts` e `cache` no `.gitlab-ci.yml`**

O GitLab CI/CD oferece dois recursos importantes para gerenciar dados entre jobs:

1. **`artifacts`**: Salva arquivos e diretórios produzidos por um job e os disponibiliza para jobs subsequentes ou para download.
2. **`cache`**: Armazena dados reutilizáveis entre execuções de pipeline, como dependências instaladas, para reduzir o tempo de build em execuções futuras.

Esses recursos são configurados no arquivo `.gitlab-ci.yml` e ajudam a otimizar o fluxo de trabalho, especialmente em projetos que demandam etapas repetitivas e consumo de dados pesados.

### **Como Compartilhar Arquivos e Diretórios entre Jobs com Artefatos**

A diretiva `artifacts` permite que você compartilhe arquivos gerados por um job para serem usados em outro. Por exemplo, você pode compilar o código em um job e armazenar o artefato compilado para ser testado ou implantado em um job subsequente.

#### **Exemplo de Uso de Artefatos**

```yaml
stages:
  - build
  - test

build:
  stage: build
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/  # Diretório de saída do build
    expire_in: 1 week

test:
  stage: test
  script:
    - npm test
  dependencies:
    - build
```

Neste exemplo:
- **`artifacts.paths`**: Salva o diretório `dist/`, contendo o código compilado, como um artefato.
- **`expire_in`**: Define o tempo de retenção do artefato (1 semana), após o qual ele será automaticamente removido para economizar espaço.
- **`dependencies`**: O job `test` depende do job `build` e tem acesso aos arquivos do artefato.

### **Uso de Cache para Reutilizar Dados entre Jobs**

A diretiva `cache` permite armazenar arquivos e diretórios de dependências entre execuções do pipeline, acelerando processos como a instalação de bibliotecas e pacotes. Isso é especialmente útil em projetos que usam gerenciadores de pacotes como npm, pip, ou Maven.

#### **Exemplo de Uso de Cache para Dependências**

No exemplo a seguir, o cache é usado para armazenar o diretório `node_modules/`, evitando que o npm reinstale todas as dependências em cada execução do pipeline.

```yaml
stages:
  - build
  - test

build:
  stage: build
  script:
    - npm install
    - npm run build
  cache:
    key: "$CI_COMMIT_REF_SLUG"
    paths:
      - node_modules/
```

Neste exemplo:
- **`cache.key`**: Define uma chave de cache baseada no nome do branch ou tag (`$CI_COMMIT_REF_SLUG`), o que permite que diferentes branches tenham caches separados.
- **`cache.paths`**: Armazena o diretório `node_modules/` em cache, permitindo que futuros jobs ou pipelines reutilizem as dependências instaladas, economizando tempo de instalação.

### **Exemplo Completo de Pipeline com Artefatos e Cache**

Aqui está um exemplo de pipeline que utiliza tanto artefatos quanto cache para otimizar o tempo de execução e compartilhar dados entre jobs:

```yaml
stages:
  - build
  - test
  - deploy

variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG

build:
  stage: build
  image: node:14
  cache:
    key: "$CI_COMMIT_REF_SLUG"
    paths:
      - node_modules/
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week

test:
  stage: test
  image: node:14
  dependencies:
    - build
  script:
    - npm test

deploy:
  stage: deploy
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  only:
    - main
```

#### **Descrição do Pipeline**:
- **Fase de Build**:
  - **Cache**: Armazena `node_modules/` para reutilizar as dependências instaladas em execuções futuras.
  - **Artefatos**: Salva o diretório `dist/` para o próximo job.
- **Fase de Teste**:
  - **Dependencies**: Acessa o artefato `dist/` da fase de build para realizar os testes.
- **Fase de Deploy**:
  - Constrói e publica a imagem Docker usando o artefato `dist/` como base para o código fonte.

### Conclusão

O uso de artefatos e cache no GitLab CI/CD é uma estratégia eficaz para reduzir o tempo de execução do pipeline e melhorar o compartilhamento de dados entre jobs. Artefatos são ideais para compartilhar arquivos específicos entre jobs, enquanto o cache é ideal para armazenar dependências e acelerar builds futuros. No próximo tópico, **Variáveis de Ambiente e Configuração Secreta**, vamos explorar como usar variáveis de ambiente para armazenar dados sensíveis e segredos no pipeline, garantindo segurança e flexibilidade.

---

# **11. Variáveis de Ambiente e Configuração Secreta**

## Objetivo
Ensinar a configurar variáveis de ambiente e segredos no GitLab CI/CD, permitindo o controle seguro de dados sensíveis, como tokens de API e credenciais de login, durante a execução do pipeline.

## Conteúdo
- **Definição e Uso de Variáveis no GitLab CI/CD para Docker Login, Tokens e Credenciais**
- **Armazenamento Seguro de Segredos no GitLab CI/CD (GitLab Secrets e Vaults)**
- **Exemplo de Configuração de Variáveis para Autenticação em Registries Privados**

---

### **Definição e Uso de Variáveis no GitLab CI/CD para Docker Login, Tokens e Credenciais**

No GitLab CI/CD, variáveis de ambiente são configuradas para armazenar dados sensíveis, como tokens de autenticação e credenciais de acesso. Essas variáveis são definidas nas configurações do projeto e ficam disponíveis para os jobs do pipeline.

#### **Como Configurar Variáveis de Ambiente no GitLab**

1. **Acessar as Configurações do Projeto**:
   - No GitLab, vá para o seu projeto e acesse **Settings > CI / CD > Variables**.

2. **Adicionar Variáveis de Ambiente**:
   - Clique em **Add Variable** e insira o nome e valor da variável.
   - **Key**: Nome da variável (ex.: `DOCKER_USER`, `DOCKER_PASS`).
   - **Value**: Valor da variável (ex.: nome de usuário e senha do Docker Hub).
   - **Mask Variable**: Habilite para ocultar o valor da variável nos logs do pipeline.
   - **Protected**: Restrinja o uso para branches protegidos, útil para deploys em produção.

Essas variáveis podem ser usadas nos scripts dos jobs como `$VARIAVEL`. Elas são automaticamente injetadas no ambiente do job, o que permite acessá-las sem que os valores apareçam no código.

#### **Exemplo de Uso de Variáveis no `.gitlab-ci.yml`**

O exemplo a seguir mostra um job de deploy que utiliza variáveis para autenticação em um registry privado:

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

deploy:
  stage: deploy
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASS" $CI_REGISTRY
    - docker pull $IMAGE_TAG
    - docker run -d -p 80:80 $IMAGE_TAG
  only:
    - main
```

Neste exemplo:
- **`DOCKER_USER` e `DOCKER_PASS`**: São variáveis configuradas para autenticar no registro Docker.
- **`CI_REGISTRY_IMAGE` e `CI_COMMIT_REF_SLUG`**: Variáveis fornecidas automaticamente pelo GitLab que definem o nome da imagem e o branch de origem do commit.

### **Armazenamento Seguro de Segredos no GitLab CI/CD (GitLab Secrets e Vaults)**

O GitLab oferece várias opções para gerenciar segredos de forma segura:

1. **GitLab Secrets**:
   - As variáveis adicionadas na seção **CI/CD Variables** são protegidas por padrão e mascaradas nos logs.
   - Variáveis configuradas como "Protected" só podem ser usadas em jobs executados em branches protegidos.

2. **Integrando com HashiCorp Vault**:
   - O GitLab pode se integrar com o HashiCorp Vault, uma solução de armazenamento seguro de segredos para CI/CD.
   - O Vault armazena tokens, senhas e chaves de API de forma segura, permitindo o acesso direto às credenciais sem armazená-las no GitLab.

#### **Exemplo de Integração com HashiCorp Vault**

Primeiro, configure o Vault no GitLab em **Settings > CI/CD > Secret Management**. Após configurar, use a variável de ambiente para acessar os segredos durante o pipeline.

```yaml
stages:
  - build
  - deploy

variables:
  VAULT_ADDR: https://vault.example.com

build:
  stage: build
  image: docker:latest
  script:
    - echo "Building application..."

deploy:
  stage: deploy
  image: docker:latest
  script:
    - export DB_PASSWORD=$(vault kv get -field=password secret/data/myapp/db)
    - echo "Deploying with secret from Vault"
```

Neste exemplo:
- **`VAULT_ADDR`**: Define o endereço do Vault.
- **`vault kv get`**: Recupera o segredo `DB_PASSWORD` do Vault.

### **Exemplo de Configuração de Variáveis para Autenticação em Registries Privados**

A configuração de variáveis de autenticação permite o uso de registries privados para armazenar imagens Docker. Adicione as credenciais do Docker Hub ou GitLab Container Registry e acesse-as com segurança no pipeline.

```yaml
deploy:
  stage: deploy
  image: docker:latest
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker pull $IMAGE_TAG
    - docker run -d -p 80:80 $IMAGE_TAG
```

Neste exemplo, as variáveis `CI_REGISTRY_USER` e `CI_REGISTRY_PASSWORD` são usadas para autenticação no registro privado, acessíveis apenas dentro do job.

---

### Conclusão

As variáveis de ambiente e a gestão de segredos no GitLab CI/CD fornecem uma camada adicional de segurança e flexibilidade, permitindo o armazenamento de credenciais e dados sensíveis sem expor informações nos scripts. O uso do Vault para gerenciamento avançado de segredos é uma opção poderosa para projetos maiores. No próximo tópico, **Pipelines Condicionais e Branches de Deploy**, veremos como configurar pipelines condicionais para execução seletiva com base em branches e outras condições, otimizando a execução de jobs.

---

# **12. Pipelines Condicionais e Branches de Deploy**

## Objetivo
Demonstrar como configurar pipelines condicionais no GitLab CI/CD para que jobs sejam executados apenas em branches específicos, tags, ou em determinadas condições, permitindo maior controle e eficiência no fluxo de deploy.

## Conteúdo
- **Uso das Diretivas `only` e `except` para Controle de Execução com Base em Branches e Tags**
- **Exemplo de Pipeline para Deploy Automático apenas em Branches de Produção**
- **Configuração de Pipelines Diferentes para Branches de Desenvolvimento e Branches Principais**

---

### **Uso das Diretivas `only` e `except` para Controle de Execução com Base em Branches e Tags**

O GitLab CI/CD oferece as diretivas `only` e `except` para definir as condições de execução de cada job, permitindo que o pipeline seja adaptado a diferentes cenários. Esses filtros controlam quando um job deve ser executado, baseando-se no branch, nas tags do commit ou em outras variáveis.

#### **Sintaxe das Diretivas `only` e `except`**

- **`only`**: Define as condições em que o job será executado. Pode ser configurado para branches específicos, tags, ou pipelines de merge requests.
- **`except`**: Define as condições em que o job **não** será executado, funcionando como uma exclusão.

#### **Exemplo Básico com `only` e `except`**

No exemplo abaixo, o job `deploy` será executado apenas em commits no branch `main`, enquanto o job `test` é executado em todos os branches, exceto em tags.

```yaml
stages:
  - test
  - deploy

test:
  stage: test
  script:
    - npm test
  except:
    - tags  # Não executa testes em commits com tags

deploy:
  stage: deploy
  script:
    - echo "Deploying to production..."
  only:
    - main  # Executa deploy apenas em commits no branch principal
```

Neste exemplo:
- O job `test` exclui tags para evitar execuções redundantes.
- O job `deploy` é restrito ao branch `main`, ideal para evitar deploys não planejados em outros branches.

### **Exemplo de Pipeline para Deploy Automático apenas em Branches de Produção**

A prática de realizar deploy automático apenas em branches de produção reduz riscos, garantindo que apenas versões estáveis e validadas sejam publicadas. A configuração a seguir mostra como definir o job de deploy apenas para branches específicos.

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - echo "Building the application..."

test:
  stage: test
  script:
    - echo "Running tests..."

deploy:
  stage: deploy
  script:
    - echo "Deploying to production..."
  only:
    - main
    - tags  # Executa deploy apenas em commits com tags ou no branch principal
```

Neste exemplo:
- **Deploy Restrito ao Branch `main` e Tags**: Garante que o deploy seja feito apenas em `main` e em commits tagueados, como lançamentos de versões (`v1.0`, `v2.0`).

### **Configuração de Pipelines Diferentes para Branches de Desenvolvimento e Branches Principais**

Para projetos com diferentes fluxos de trabalho, você pode configurar pipelines específicos para branches de desenvolvimento e produção. Essa abordagem permite que novos recursos sejam testados em um ambiente separado antes de serem enviados ao branch principal.

#### **Exemplo: Pipeline Condicional para Desenvolvimento e Produção**

Aqui está um pipeline onde o job de deploy é configurado para ser executado apenas no branch `main`, enquanto o job de staging é executado apenas no branch `develop`.

```yaml
stages:
  - build
  - test
  - staging
  - production

build:
  stage: build
  script:
    - echo "Building the application..."

test:
  stage: test
  script:
    - echo "Running tests..."

staging:
  stage: staging
  script:
    - echo "Deploying to staging..."
  only:
    - develop  # Executa deploy em staging apenas no branch de desenvolvimento

production:
  stage: production
  script:
    - echo "Deploying to production..."
  only:
    - main  # Executa deploy em produção apenas no branch principal
```

#### **Descrição do Pipeline**:
- **Staging**: Executa o deploy de staging para o branch `develop`, permitindo que novas alterações sejam testadas antes do deploy final.
- **Production**: Executa o deploy de produção apenas no branch `main`, garantindo que somente código aprovado e revisado chegue ao ambiente de produção.

Essa configuração oferece flexibilidade ao processo de desenvolvimento, ajudando a manter a integridade do código em produção enquanto permite testes contínuos no ambiente de staging.

---

### Conclusão

Pipelines condicionais no GitLab CI/CD permitem um controle detalhado sobre a execução de jobs, facilitando o deploy automático com segurança e reduzindo riscos. Com diretivas como `only` e `except`, é possível definir diferentes pipelines para ambientes de desenvolvimento e produção, garantindo um fluxo de trabalho consistente e seguro. No próximo tópico, **Integração com Monitoramento e Notificações**, veremos como configurar alertas e monitoramento para acompanhar o status dos pipelines e facilitar a detecção de problemas.

---

# **13. Integração com Monitoramento e Notificações**

## Objetivo
Configurar monitoramento e alertas no GitLab CI/CD para acompanhar o status dos pipelines e deploys, facilitando a detecção rápida de falhas e o acompanhamento do ciclo de desenvolvimento e deploy.

## Conteúdo
- **Configuração de Notificações para Slack, Email e Outras Plataformas**
- **Uso do GitLab para Monitoramento de Falhas, Logs e Status dos Jobs**
- **Exemplos de Integração com Ferramentas de Monitoramento Externas**

---

### **Configuração de Notificações para Slack, Email e Outras Plataformas**

Notificações são essenciais para manter a equipe informada sobre o status dos pipelines e eventuais problemas. O GitLab oferece integrações nativas para enviar alertas para Slack, email, Microsoft Teams e outras ferramentas de comunicação.

#### **Configuração de Notificações por Slack**

1. **Configurar o Slack**:
   - No seu projeto GitLab, acesse **Settings > Integrations > Slack Notifications**.
   - Adicione o **Webhook URL** do Slack, que pode ser gerado diretamente no Slack, em **Apps > Manage apps > Custom Integrations > Incoming Webhooks**.

2. **Configurar Opções de Notificação**:
   - Escolha os eventos que devem gerar notificações, como falhas no pipeline, sucesso no deploy ou novos commits.
   - Configure os canais do Slack que devem receber essas notificações.

Exemplo de configuração no GitLab:
```yaml
notify:
  stage: notify
  script:
    - curl -X POST -H 'Content-type: application/json' --data '{"text":"O pipeline foi concluído!"}' $SLACK_WEBHOOK_URL
```

#### **Configuração de Notificações por Email**

1. **Configurar Emails no GitLab**:
   - No projeto GitLab, vá para **Settings > CI / CD > General pipelines settings**.
   - Ative a opção **Email notifications** e selecione os destinatários.

2. **Configuração Personalizada no `.gitlab-ci.yml`**:
   Para enviar notificações específicas, configure um job de notificação com um script de envio de email personalizado.

### **Uso do GitLab para Monitoramento de Falhas, Logs e Status dos Jobs**

O GitLab CI/CD fornece ferramentas para monitorar o status dos jobs, logs e falhas diretamente na interface do projeto. É possível revisar o histórico de execuções para entender o comportamento do pipeline ao longo do tempo e investigar a causa de falhas.

- **Status dos Pipelines**: Acompanhe o status atual de todos os pipelines em **CI / CD > Pipelines**. Cada pipeline exibe o tempo de execução, a data de início e os jobs em cada estágio.
- **Logs Detalhados**: Acesse os logs completos de cada job para verificar o que foi executado e identificar a causa de falhas ou problemas.
- **Configuração de Retentividade de Logs**: O GitLab permite definir o tempo de retenção de logs para liberar espaço de armazenamento, o que pode ser configurado em **Settings > CI / CD > Pipelines > Pipeline artifacts**.

### **Exemplos de Integração com Ferramentas de Monitoramento Externas**

Para monitorar e analisar o desempenho do pipeline, o GitLab pode ser integrado a ferramentas de monitoramento externas, como Prometheus, Grafana e ELK Stack. Essas ferramentas permitem alertas mais avançados e visualizações detalhadas do histórico de execuções.

#### **Integração com Prometheus para Monitoramento em Tempo Real**

1. **Configurar Prometheus no GitLab**:
   - No projeto GitLab, vá para **Settings > Integrations > Prometheus**.
   - Adicione o endereço do Prometheus e configure o token de autenticação.

2. **Configuração de Métricas**:
   O GitLab fornece métricas como uso de CPU, memória e tempo de execução de jobs, que podem ser visualizadas no Prometheus e conectadas a Grafana para dashboards customizados.

#### **Exemplo de Alerta no Grafana Baseado em Métricas do Pipeline**

Após configurar o Prometheus, crie um dashboard no Grafana que exibe o tempo médio de execução dos pipelines e define alertas caso o tempo ultrapasse um limite.

```yaml
- alert: LongPipelineDuration
  expr: job_duration_seconds > 600  # Alerta se o job durar mais de 10 minutos
  for: 5m
  labels:
    severity: "critical"
  annotations:
    summary: "Duração excessiva do pipeline"
    description: "O job {% raw %}{{ $labels.job_name }}{% endraw %} está em execução há mais de 10 minutos."
```

Esse alerta envia uma notificação quando o tempo de execução de um job ultrapassa o limite, permitindo que a equipe identifique gargalos e otimize o pipeline.

---

### Conclusão

Integrar o GitLab CI/CD com notificações e monitoramento permite que a equipe acompanhe o status dos pipelines em tempo real e reaja rapidamente a problemas. A utilização de ferramentas como Prometheus e Grafana complementa o monitoramento, proporcionando visibilidade detalhada e alertas personalizados. No próximo e último tópico, **Troubleshooting e Resolução de Problemas em CI/CD**, abordaremos técnicas para diagnosticar e resolver problemas comuns no pipeline, garantindo que o GitLab CI/CD funcione de forma robusta e eficiente.

---

# **14. Troubleshooting e Resolução de Problemas em CI/CD**

## Objetivo
Identificar e solucionar problemas comuns ao usar Docker com GitLab CI/CD, incluindo falhas de build, deploy e execução de containers, para garantir que o pipeline funcione de forma estável e confiável.

## Conteúdo
- **Diagnóstico com Logs e Variáveis de Debug no GitLab**
- **Solução de Problemas de Build, Deploy e Execução de Containers**
- **Dicas para Otimizar e Tornar o Pipeline Mais Robusto**

---

### **Diagnóstico com Logs e Variáveis de Debug no GitLab**

Os logs do GitLab CI/CD são essenciais para entender o comportamento dos jobs e identificar problemas. Cada job gera um log detalhado que inclui as etapas executadas, o ambiente de execução e os erros encontrados.

#### **Acessando e Analisando Logs no GitLab**

1. **Acessar Logs dos Jobs**:
   - No GitLab, vá para **CI/CD > Pipelines** e selecione o pipeline que contém o job com problemas.
   - Clique no job específico para ver os logs detalhados.

2. **Filtrar e Buscar Erros**:
   - Use o recurso de busca do navegador (Ctrl+F) para localizar palavras-chave, como `error`, `failed`, ou o nome do comando que falhou.
   - Procure por mensagens de erro específicas que indiquem a causa do problema, como falta de permissões ou variáveis de ambiente ausentes.

3. **Ativar Variáveis de Debug**:
   - Para obter informações detalhadas durante a execução do pipeline, ative variáveis de debug. Adicione uma variável de ambiente no GitLab chamada `CI_DEBUG_TRACE` com o valor `true`, que exibirá cada comando e suas saídas nos logs.

```yaml
variables:
  CI_DEBUG_TRACE: "true"
```

Essa configuração aumenta a visibilidade dos comandos executados, ajudando a identificar a origem de falhas específicas.

### **Solução de Problemas de Build, Deploy e Execução de Containers**

#### **Problemas Comuns e Soluções**

1. **Erro ao Construir Imagem Docker (`docker build` falha)**:
   - **Causa Comum**: Problemas de sintaxe no Dockerfile, permissões de arquivo ou falta de dependências.
   - **Solução**: Revise o Dockerfile e execute `docker build` localmente para verificar o comportamento. Verifique se todos os arquivos necessários estão no contexto de build.

2. **Falha ao Fazer Login no Docker Registry (`docker login` falha)**:
   - **Causa Comum**: Credenciais inválidas ou variáveis de ambiente ausentes.
   - **Solução**: Verifique as variáveis `DOCKER_USER` e `DOCKER_PASS` no GitLab. Assegure-se de que as variáveis de autenticação estão configuradas corretamente e ativadas para o branch atual.

3. **Erro de Permissão ao Executar Jobs com Docker-in-Docker (`docker:dind`)**:
   - **Causa Comum**: Falta de privilégios no Docker executor para usar Docker-in-Docker.
   - **Solução**: Verifique se o Docker executor está configurado com `privileged: true` no arquivo `config.toml` do GitLab Runner.

4. **Timeout Durante o Deploy**:
   - **Causa Comum**: Conexões lentas ou bloqueios de firewall que interrompem o processo de deploy.
   - **Solução**: Aumente o limite de timeout do job no GitLab CI/CD. Em **Settings > CI/CD > General pipelines settings**, configure um timeout mais alto.

5. **Erros em Testes Automatizados em Containers**:
   - **Causa Comum**: Dependências ausentes, variáveis de ambiente incorretas ou falhas na comunicação entre containers.
   - **Solução**: Certifique-se de que todas as dependências estão corretamente configuradas e que os serviços, como bancos de dados, estão acessíveis via rede interna do Docker (`--network`).

### **Dicas para Otimizar e Tornar o Pipeline Mais Robusto**

1. **Cache e Artefatos**:
   - Utilize `cache` para armazenar dependências, reduzindo o tempo de build.
   - Armazene arquivos compartilhados com `artifacts` para otimizar o uso de recursos entre jobs.

2. **Configuração de Retentividade para Jobs de Debug**:
   - Adicione variáveis de debug apenas temporariamente para não aumentar o tamanho dos logs desnecessariamente.
   - Quando resolver o problema, remova `CI_DEBUG_TRACE` para manter os logs concisos.

3. **Uso de Ambientes de Staging para Validação**:
   - Utilize um ambiente de staging para testes de deploy antes de enviar para produção. Isso reduz o impacto de falhas em deploys críticos.

4. **Dividir Jobs e Modularizar Pipelines**:
   - Separe jobs com tarefas complexas em múltiplos estágios para identificar facilmente onde ocorrem os problemas.
   - Modularize o pipeline usando includes (`include`), o que facilita a manutenção e a atualização dos scripts.

---

### Conclusão

Com técnicas eficazes de troubleshooting e otimização, é possível manter um pipeline GitLab CI/CD robusto, garantindo que os processos de build, teste e deploy sejam executados de forma consistente. A análise dos logs, o uso de variáveis de debug e a divisão dos jobs em tarefas modulares são essenciais para uma execução eficiente e segura. No próximo e último tópico, **Casos de Uso Avançados e Práticas Recomendadas**, veremos práticas e exemplos de configuração de pipelines avançados para cenários específicos, garantindo a aplicação das melhores práticas em CI/CD com Docker no GitLab.

---

# **15. Casos de Uso Avançados e Práticas Recomendadas**

## Objetivo
Apresentar práticas avançadas e casos de uso para configuração de pipelines GitLab CI/CD, abordando estratégias como deploy canário, atualização contínua e práticas recomendadas de segurança e otimização.

## Conteúdo
- **Configuração de Deploy Canário e Blue-Green com Docker e GitLab CI/CD**
- **Exemplo de Pipeline Completo com Build, Test, Deploy e Monitoramento**
- **Melhores Práticas para Segurança, Otimização e Manutenção do Pipeline**

---

### **Configuração de Deploy Canário e Blue-Green com Docker e GitLab CI/CD**

Deploy canário e blue-green são técnicas avançadas de deploy que minimizam riscos ao lançar novas versões de um serviço, oferecendo uma estratégia segura e controlada para atualizações em produção.

#### **Deploy Canário**

No deploy canário, uma nova versão do serviço é implementada em um pequeno grupo de servidores ou para um subconjunto de usuários antes de ser totalmente implementada.

```yaml
stages:
  - build
  - deploy
  - canary-deploy
  - full-deploy

build:
  stage: build
  script:
    - docker build -t $CI_REGISTRY_IMAGE:latest .
    - docker push $CI_REGISTRY_IMAGE:latest

canary-deploy:
  stage: deploy
  script:
    - docker service update --image $CI_REGISTRY_IMAGE:latest --replicas 2 myapp_service
  only:
    - main

full-deploy:
  stage: deploy
  script:
    - docker service update --image $CI_REGISTRY_IMAGE:latest --replicas 10 myapp_service
  when: manual
```

Neste exemplo:
- **canary-deploy**: Atualiza o serviço com a nova imagem e define apenas 2 réplicas.
- **full-deploy**: Executa o deploy completo em produção com 10 réplicas, que pode ser disparado manualmente após a validação do canário.

#### **Deploy Blue-Green**

No deploy blue-green, duas versões do serviço (por exemplo, `blue` e `green`) coexistem. A nova versão (`green`) é implementada em paralelo à versão atual (`blue`) e, após a validação, o tráfego é redirecionado para a versão `green`.

```yaml
stages:
  - build
  - deploy-blue
  - switch-traffic

build:
  stage: build
  script:
    - docker build -t $CI_REGISTRY_IMAGE:green .
    - docker push $CI_REGISTRY_IMAGE:green

deploy-blue:
  stage: deploy
  script:
    - docker service create --name myapp_green --replicas 5 $CI_REGISTRY_IMAGE:green

switch-traffic:
  stage: deploy
  script:
    - docker service update --name myapp --image $CI_REGISTRY_IMAGE:green --replicas 10
  when: manual
```

Neste exemplo:
- **deploy-blue**: Implementa a nova versão (`green`) em paralelo com a versão atual (`blue`).
- **switch-traffic**: Redireciona o tráfego para a nova versão (`green`) após a validação manual.

### **Exemplo de Pipeline Completo com Build, Test, Deploy e Monitoramento**

Aqui está um exemplo de pipeline GitLab CI/CD completo que inclui as etapas de build, teste, deploy e monitoramento.

```yaml
stages:
  - build
  - test
  - deploy
  - monitor

build:
  stage: build
  image: docker:latest
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

test:
  stage: test
  script:
    - docker pull $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
    - docker run --rm $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA npm test

deploy:
  stage: deploy
  script:
    - docker pull $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
    - docker run -d -p 80:80 $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
  only:
    - main

monitor:
  stage: monitor
  script:
    - curl -X POST -H 'Content-type: application/json' --data '{"text":"Pipeline concluído com sucesso!"}' $SLACK_WEBHOOK_URL
```

#### Descrição do Pipeline:
- **Build**: Constrói e publica a imagem Docker.
- **Test**: Executa testes em um container baseado na imagem recém-criada.
- **Deploy**: Realiza o deploy da nova versão em produção.
- **Monitor**: Envia uma notificação para o Slack informando sobre o status do pipeline.

### **Melhores Práticas para Segurança, Otimização e Manutenção do Pipeline**

1. **Automatize Atualizações e Rollbacks**:
   - Utilize tags de versionamento e crie backups regulares para facilitar rollbacks em caso de falhas.

2. **Use Variáveis de Ambiente para Dados Sensíveis**:
   - Armazene credenciais e tokens de API como variáveis de ambiente e use ferramentas como o HashiCorp Vault para gerenciamento seguro.

3. **Limite o Escopo de Deploys Automatizados**:
   - Configure deploys automáticos apenas para branches principais e adote processos de revisão para garantir que o código de produção esteja sempre estável.

4. **Monitore o Desempenho e Crie Alertas**:
   - Configure alertas com Prometheus ou Grafana para monitorar o desempenho do pipeline e da aplicação em produção.

5. **Otimização e Reutilização com Cache e Artefatos**:
   - Configure o cache para acelerar o build de dependências e use artefatos para compartilhar dados entre jobs.

6. **Configure o Timeout e Parâmetros de Limite para Jobs**:
   - Defina limites de tempo para cada job para evitar que jobs falhos permaneçam em execução indefinidamente, consumindo recursos do Runner.

---

### Conclusão

A aplicação de práticas avançadas no GitLab CI/CD, como deploy canário, blue-green e integração com ferramentas de monitoramento, oferece segurança e flexibilidade, permitindo que o pipeline seja otimizado para atender às demandas de produção. Com essas práticas, os pipelines se tornam mais robustos e preparados para lidar com requisitos de deploy contínuo, garantindo alta disponibilidade e controle de qualidade. 

Esse foi o último tópico do tutorial. Agora você tem uma visão completa sobre como implementar, otimizar e manter pipelines de CI/CD no GitLab com Docker, usando práticas recomendadas e estratégias avançadas. Se precisar de mais ajuda ou ajustes, estou à disposição!

---

### Conclusão Geral

Ao integrar o Docker com o GitLab CI/CD, é possível criar pipelines de automação robustos e flexíveis, com ambientes de execução consistentes, testes isolados e deploys automatizados. Neste tutorial, abordamos desde configurações iniciais até práticas avançadas, capacitando os usuários a automatizar processos de maneira segura e eficiente. Com técnicas como deploys canários e blue-green, além da integração com monitoramento e notificações, esse fluxo de trabalho oferece maior controle e confiança nas atualizações em produção.

---
