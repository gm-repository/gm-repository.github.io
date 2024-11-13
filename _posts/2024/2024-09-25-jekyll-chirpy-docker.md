---
title: 'Criando um Ambiente Jekyll com Docker e Docker Hub'
date: 2024-09-24 17:00:00 -0300
categories: 
  - Docker
  - Jekyll
  - DevOps
tags:
  - Docker
  - Jekyll
  - Docker Hub
  - Containers
summary: 'Este tutorial aborda como configurar um ambiente de desenvolvimento com Jekyll utilizando Docker, automatizar o processo com Docker Compose e publicar a imagem no Docker Hub para compartilhamento e replicação.'
---

### Sumário

- [Visão Geral e Introdução ao Projeto](#etapa-1-visão-geral-e-introdução-ao-projeto)
- [Configuração do Jekyll Localmente com Dockerfile](#etapa-2-configuração-do-jekyll-localmente-com-dockerfile)
- [Construindo e Executando a Imagem Docker](#etapa-3-construindo-e-executando-a-imagem-docker)
- [Acessando o Site e Fazendo Alterações](#etapa-4-acessando-o-site-e-fazendo-alterações)
- [Automação com Docker Compose](#etapa-5-automação-com-docker-compose)
- [Publicação da Imagem no Docker Hub](#etapa-7-publicação-da-imagem-no-docker-hub)
- [Usando a Imagem do Docker Hub com Docker Compose](#etapa-8-usando-a-imagem-do-docker-hub-com-docker-compose)

---
### **Etapa 1: Visão Geral e Introdução ao Projeto**

## **Criando um Ambiente Jekyll com Docker e Docker Hub**

Neste tutorial, vamos configurar um ambiente de desenvolvimento para um site Jekyll usando o Docker. Vamos seguir as seguintes etapas:

1. **Configuração Local com Dockerfile**: Inicialmente, configuraremos o Jekyll localmente com Docker.
2. **Automação com Docker Compose**: Usaremos o Docker Compose para simplificar o gerenciamento do container.
3. **Publicação no Docker Hub**: Após configurar o ambiente local, vamos enviar a imagem para o Docker Hub para facilitar a distribuição.
4. **Uso da Imagem do Docker Hub**: Finalmente, veremos como usar essa imagem diretamente de qualquer máquina via Docker Compose.

> **Pré-requisitos**: Você deve ter o Docker instalado em sua máquina. Se precisar de ajuda, consulte a [documentação oficial do Docker](https://docs.docker.com/get-docker/).

---

### **Etapa 2: Configuração do Jekyll Localmente com Dockerfile**

Nesta etapa, vamos criar um `Dockerfile` para configurar um ambiente Jekyll local. O `Dockerfile` descreve as etapas necessárias para criar a imagem do Docker que rodará o Jekyll.

#### **2.1. Estrutura do Projeto**

1. **Crie o diretório do projeto**:
   - Crie uma pasta para o seu projeto e navegue até ela.

   ```bash
   mkdir jekyll-chirpy
   cd jekyll-chirpy
   ```

2. **Clone o repositório do tema Chirpy**:
   - Vamos usar o tema `chirpy-starter` como base. Clone o repositório dentro do seu diretório.

   ```bash
   git clone https://github.com/cotes2020/chirpy-starter.git .
   ```

3. **Crie o arquivo `Dockerfile`**:
   - Agora crie o arquivo `Dockerfile` que definirá a imagem Docker que rodará o Jekyll.

   ```bash
   touch Dockerfile
   ```

4. **Edite o `Dockerfile` com o seguinte conteúdo**:

   ```dockerfile
   # Usar uma imagem oficial do Ruby como base
   FROM ruby:3.2-alpine

   # Definir variáveis de ambiente para evitar prompts durante instalações
   ENV BUNDLER_VERSION=2.4.0
   ENV JEKYLL_ENV=production

   # Instalar dependências no Alpine
   RUN apk update && apk add --no-cache \
       build-base \
       gcc \
       libc-dev \
       linux-headers \
       nodejs \
       git \
       vim \
       libxml2-dev \
       libxslt-dev \
       tzdata \
       bash

   # Instalar Bundler na versão especificada
   RUN gem install bundler -v $BUNDLER_VERSION

   # Configurar o diretório de trabalho
   WORKDIR /usr/src/app

   # Copiar o Gemfile e o Gemfile.lock para o diretório de trabalho
   COPY Gemfile ./

   # Instalar as gems necessárias
   RUN bundle install --jobs 4 --retry 3

   # Copiar todos os arquivos do projeto para o diretório de trabalho
   COPY . .

   # Construir o site Jekyll em modo de produção
   RUN bundle exec jekyll build

   # Expor a porta 4000 para servir o site
   EXPOSE 4000

   # Comando para iniciar o servidor Jekyll
   CMD ["bundle", "exec", "jekyll", "serve", "--host", "0.0.0.0"]
   ```

#### **Explicação do Dockerfile**:
- **Base Ruby**: Usamos uma imagem base `ruby:3.2-alpine`, que é uma versão leve do Ruby.
- **Ambiente de Produção**: A variável `JEKYLL_ENV=production` define que o Jekyll rodará em modo de produção, o que otimiza a build para um site mais eficiente.
- **Instalação de Dependências**: Instalamos as dependências básicas do Alpine Linux e do Ruby.
- **Comando Final**: O comando final `CMD` roda o servidor Jekyll em `http://0.0.0.0:4000`, permitindo que o site seja acessado pela rede local.

---

### **Etapa 3: Construindo e Executando a Imagem Docker**

Agora que temos o `Dockerfile` configurado, vamos construir a imagem e rodar o container.

1. **Construir a imagem Docker**:
   - No diretório onde está o `Dockerfile`, execute o comando para criar a imagem.

   ```bash
   docker build -t jekyll-chirpy .
   ```

   - **Explicação**: A flag `-t` nomeia a imagem como `jekyll-chirpy`, e o ponto (`.`) indica que o Docker deve usar o contexto atual para construir a imagem.

2. **Executar o container**:
   - Após construir a imagem, execute o container com o seguinte comando:

   ```bash
   docker run -p 4000:4000 --name jekyll -v $(pwd):/app jekyll-chirpy
   ```

   - **Explicação do comando**:
     - **`-p 4000:4000`**: Mapeia a porta 4000 do container para a porta 4000 da sua máquina, permitindo o acesso ao site localmente.
     - **`--name jekyll`**: Nomeia o container como `jekyll`.
     - **`-v $(pwd):/app`**: Sincroniza o diretório atual no host com o diretório `/app` no container, permitindo editar arquivos localmente e refletir as alterações no container em tempo real.

---

### **Etapa 4: Acessando o Site e Fazendo Alterações**

1. **Acesse o site**:
   - Agora, abra o navegador e acesse [http://localhost:4000](http://localhost:4000). O site Jekyll com o tema Chirpy estará rodando localmente.

2. **Editando o conteúdo**:
   - Graças ao volume mapeado com `-v $(pwd):/app`, as alterações feitas no seu diretório local serão automaticamente refletidas no container.
   
   **Exemplo**:
   - Abra o arquivo `_config.yml` no diretório local e modifique o título do site:

     ```yaml
     title: "Meu Blog com Chirpy"
     ```

   - Salve o arquivo. O Jekyll detectará automaticamente as alterações e atualizará o site.

3. **Adicionando novos posts**:
   - Para adicionar um novo post, crie um arquivo na pasta `_posts`:

     ```bash
     mkdir -p _posts
     touch _posts/2024-01-01-meu-primeiro-post.md
     ```

   - Adicione o seguinte conteúdo ao arquivo:

     ```markdown
     ---
     layout: post
     title: "Meu Primeiro Post"
     date: 2024-01-01 12:00:00 -0000
     categories: blog
     ---

     Este é meu primeiro post utilizando o tema Jekyll Chirpy em Docker!
     ```

   - Assim que salvar o arquivo, o site será automaticamente atualizado.

---

### **Conclusão da Primeira Parte**

Com essas etapas, agora você tem o ambiente Jekyll rodando localmente com Docker. Você pode editar o conteúdo e ver as mudanças em tempo real, graças ao mapeamento de volumes. Na próxima etapa, vamos simplificar o gerenciamento do container usando o Docker Compose.

---

### **Etapa 5: Automação com Docker Compose**

Agora que temos um ambiente funcional com Docker, vamos automatizar esse processo utilizando o **Docker Compose**. Isso facilitará o ciclo de desenvolvimento e manterá os comandos mais organizados.

#### **5.1. Criando o arquivo `docker-compose.yml`**

1. **Crie o arquivo `docker-compose.yml` no diretório do projeto**:
   
   ```bash
   touch docker-compose.yml
   ```

2. **Edite o arquivo com o seguinte conteúdo**:

   ```yaml
   version: '3'

   services:
     jekyll:
       build:
         context: .
         dockerfile: Dockerfile
       container_name: jekyll_site
       ports:
         - "4000:4000"
       volumes:
         - .:/app
       environment:
         JEKYLL_ENV: production
       command: bundle exec jekyll serve --host 0.0.0.0
   ```

#### **Explicação do `docker-compose.yml`**:

- **version: '3'**: Define a versão do Docker Compose. A versão 3 é amplamente compatível e recomendada.
  
- **services**: Define os containers que serão gerenciados pelo Docker Compose. No nosso caso, há apenas um serviço chamado `jekyll`.
  
  - **build**: 
    - **context**: Define o diretório atual como o contexto para construção da imagem.
    - **dockerfile**: Especifica que o Docker Compose deve usar o `Dockerfile` para construir a imagem.

  - **container_name**: Nomeia o container como `jekyll_site`, o que facilita o gerenciamento.
  
  - **ports**: Mapeia a porta 4000 do container para a porta 4000 da máquina host, permitindo o acesso ao site em `http://localhost:4000`.
  
  - **volumes**: Sincroniza o diretório atual do host com o diretório `/app` dentro do container, permitindo que você edite os arquivos localmente e veja as mudanças refletidas automaticamente no container.
  
  - **environment**: Define a variável de ambiente `JEKYLL_ENV=production` para rodar o Jekyll em modo de produção.
  
  - **command**: Especifica o comando que será executado no container ao iniciar, rodando o servidor Jekyll em `0.0.0.0`.

---

#### **5.2. Executando o ambiente com Docker Compose**

Agora que criamos o arquivo `docker-compose.yml`, podemos automatizar o processo de construção e execução do container.

1. **Construir e iniciar o container**:
   - Use o comando abaixo para construir a imagem (caso necessário) e iniciar o container.

   ```bash
   docker-compose up --build
   ```

   - **Explicação**: O comando `up` cria e inicia o container, enquanto a flag `--build` força a reconstrução da imagem Docker com base no `Dockerfile`.

2. **Executar em segundo plano**:
   - Se preferir rodar o container em segundo plano (background), use a flag `-d`:

   ```bash
   docker-compose up -d --build
   ```

   - Isso mantém o container rodando sem ocupar o terminal.

3. **Parar e remover o container**:
   - Para parar o container e remover os serviços do Docker Compose, use:

   ```bash
   docker-compose down
   ```

   - Esse comando encerra o container e remove a rede e o volume gerados pelo Compose.

---

### **Etapa 6: Benefícios do Docker Compose**

Agora que o Docker Compose está configurado, você não precisa mais repetir comandos longos. Simplesmente executar `docker-compose up` já gerenciará a construção e a execução do container, facilitando o desenvolvimento diário.

- **Automação**: Docker Compose reduz a repetição de comandos e centraliza a configuração em um único arquivo (`docker-compose.yml`).
- **Facilidade de Uso**: Para iniciar ou parar o ambiente, basta rodar `docker-compose up` ou `docker-compose down`.
- **Escalabilidade**: Se no futuro você adicionar mais serviços (como um banco de dados), o Compose permite orquestrar múltiplos containers com facilidade.

---

### **Conclusão da Segunda Parte**

Agora que o **Docker Compose** está configurado, você tem um ambiente automatizado e mais fácil de gerenciar para rodar seu projeto Jekyll. Nas próximas etapas, vamos explorar a publicação da imagem no **Docker Hub** e como usar essa imagem a partir de qualquer máquina.

---

### **Etapa 7: Publicação da Imagem no Docker Hub**

Publicar sua imagem no Docker Hub permite que ela seja acessada a partir de qualquer máquina, facilitando o compartilhamento e a distribuição. Vamos abordar como fazer isso.

#### **7.1. Criando uma Conta no Docker Hub**

Se você ainda não tem uma conta, vá para [Docker Hub](https://hub.docker.com/signup) e crie uma.

---

#### **7.2. Login no Docker Hub via Terminal**

Antes de enviar a imagem para o Docker Hub, faça login usando o seguinte comando:

```bash
docker login
```

Você será solicitado a inserir seu **nome de usuário** e **senha**. Após fazer o login, você verá uma mensagem de confirmação.

---

#### **7.3. Taguear a Imagem**

Agora vamos "taguear" a imagem que você criou localmente para que ela esteja no formato correto para ser enviada ao Docker Hub. O formato do nome da imagem deve ser:

```
<nome-de-usuario>/<nome-do-repositorio>:<tag>
```

Por exemplo, se seu nome de usuário no Docker Hub for `meuusuario` e você quiser chamar o repositório de `jekyll-chirpy` com a tag `latest`, use o seguinte comando:

```bash
docker tag jekyll-chirpy meuusuario/jekyll-chirpy:latest
```

- **Explicação**:
  - `jekyll-chirpy`: Nome da imagem que criamos localmente.
  - `meuusuario/jekyll-chirpy:latest`: Nome da imagem no Docker Hub e a tag `latest`.

---

#### **7.4. Enviar a Imagem para o Docker Hub**

Agora que a imagem foi tagueada corretamente, envie-a para o Docker Hub com o comando:

```bash
docker push meuusuario/jekyll-chirpy:latest
```

Esse processo pode levar algum tempo, dependendo do tamanho da imagem e da velocidade da sua conexão.

---

#### **7.5. Verificar no Docker Hub**

Após o upload, você pode acessar sua conta no [Docker Hub](https://hub.docker.com), navegar até o seu repositório e verificar se a imagem foi enviada corretamente. Você verá a imagem com a tag `latest` (ou qualquer outra que tenha utilizado).

---

### **Etapa 8: Usando a Imagem do Docker Hub com Docker Compose**

Agora que a imagem está publicada no Docker Hub, podemos configurar o **Docker Compose** para utilizar diretamente essa imagem. Isso permite que você (ou qualquer outra pessoa) use a imagem sem precisar construir tudo localmente.

Clone o repositório do tema Chirpy:

```bash
mkdir jekyll
cd jekyll
git clone https://github.com/cotes2020/chirpy-starter app
```

#### **8.1. Criando um Novo `docker-compose.yml`**

Vamos criar um novo arquivo `docker-compose.yml` para usar a imagem do Docker Hub. No diretório do seu projeto, edite ou crie o arquivo `docker-compose.yml` com o seguinte conteúdo:

```yaml
services:
  jekyll:
    image: meuusuario/jekyll-chirpy:latest  # Substitua com seu nome de usuário e repositório
    container_name: jekyll_site
    ports:
      - "4000:4000"
    volumes:
      - "./app:/usr/src/app"
    environment:
      JEKYLL_ENV: production
    command: bundle exec jekyll serve --host 0.0.0.0
```

#### **Explicação do arquivo**:

- **image**: Em vez de construir a imagem localmente, estamos usando a imagem que foi publicada no Docker Hub. Substitua `meuusuario/jekyll-chirpy:latest` pelo seu nome de usuário e repositório.
  
- **container_name**: Nomeia o container como `jekyll_site`.
  
- **ports**: Mapeia a porta 4000 do container para a porta 4000 da máquina host, permitindo o acesso ao site em `http://localhost:4000`.
  
- **volumes**: Sincroniza o diretório local com o diretório `/usr/src/app` dentro do container, permitindo que alterações locais sejam refletidas no container.
  
- **environment**: Define a variável de ambiente `JEKYLL_ENV=production`, rodando o Jekyll em modo de produção.
  
- **command**: Inicia o servidor Jekyll usando `bundle exec jekyll serve --host 0.0.0.0`, o que permite o acesso externo.

---

#### **8.2. Executando o Docker Compose com a Imagem do Docker Hub**

Agora que temos o `docker-compose.yml` configurado para usar a imagem do Docker Hub, execute o Docker Compose da seguinte maneira:

1. **Executar o Compose**:

   ```bash
   docker-compose up
   ```

   Isso baixará a imagem do Docker Hub (caso ainda não tenha sido baixada) e iniciará o container.

2. **Executar em segundo plano**:

   Se quiser rodar o container em segundo plano (background), use a flag `-d`:

   ```bash
   docker-compose up -d
   ```

3. **Parar o serviço**:

   Para parar o container rodando via Docker Compose, use:

   ```bash
   docker-compose down
   ```

---

### **Conclusão da Terceira Parte**

Agora você já pode usar a imagem Docker que publicou no Docker Hub de qualquer lugar, sem a necessidade de reconstruir o ambiente localmente. Isso facilita o compartilhamento do seu projeto e a replicação em diferentes ambientes.

Com o Docker Compose, você pode simplesmente apontar para a imagem do Docker Hub, automatizando todo o processo de configuração do ambiente. Este fluxo é especialmente útil para distribuição em ambientes de produção ou desenvolvimento remoto.

