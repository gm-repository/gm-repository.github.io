---
layout: post
title: "Pipeline CI/CD no GitLab para Blog Jekyll com Docker"
date: 2024-11-16 17:18:19 -0300
categories: [ci-cd, devops, jekyll]
tags: [gitlab, docker, nginx, pipeline, jekyll]
excerpt: "Saiba como implementar uma pipeline CI/CD completa no GitLab para um blog Jekyll utilizando Docker e Nginx com proxy reverso."
---

### Introdução

O **Jekyll** é uma ferramenta poderosa e popular para a criação de sites estáticos. Usado amplamente por desenvolvedores, ele transforma arquivos Markdown em páginas HTML otimizadas, facilitando o gerenciamento de blogs e sites simples sem a necessidade de um backend complexo. Por ser baseado em Ruby e ter uma integração direta com o GitHub Pages, o Jekyll é uma escolha ideal para projetos que priorizam desempenho e simplicidade.

Neste tutorial, utilizaremos o tema **Chirpy-Starter**, uma solução elegante e minimalista para blogs baseados em Jekyll. Esse tema oferece:
- Design responsivo e moderno.
- Configuração simples e suporte integrado a SEO.
- Recursos adicionais, como tabelas de conteúdo automáticas, suporte a comentários e integração com redes sociais.

Combinaremos o Jekyll e o Chirpy-Starter com uma pipeline **CI/CD no GitLab**, Docker, e Nginx para garantir uma experiência de desenvolvimento eficiente e um processo de publicação automatizado.

---

### **1. Criando o Projeto no GitLab**

#### **Passo 1: Criar um repositório no GitLab**

1. Acesse sua conta no [GitLab](https://gitlab.com).
2. Vá para **Menu > Projects > New Project**.
3. Clique em **Create Blank Project**.
4. Preencha os campos necessários:
   - **Project name**: Defina um nome para o projeto, como `jekyll-blog`.
   - **Visibility Level**: Escolha `Private` ou `Public`, dependendo do seu objetivo.
5. Clique em **Create Project**.

---

Após criar o projeto, você terá acesso à URL do repositório, que será utilizada nas etapas seguintes.

---

### **2. Fazer Clone do Tema Chirpy-Starter**

#### **Passo 2.1: Clonar o Tema do Repositório Oficial**

1. Abra o terminal e execute o comando para clonar o repositório do tema **Chirpy-Starter**:
   ```bash
   git clone https://github.com/cotes2020/chirpy-starter.git
   ```

2. Acesse o diretório clonado:
   ```bash
   cd chirpy-starter
   ```

---

#### **Passo 2.2: Remover Arquivos Desnecessários**

Remova arquivos e pastas que não serão usados no projeto:

```bash
rm -rf .devcontainer .git .github .vscode .editorconfig .gitignore .gitmodules .nojekyll
```

**Pronto!** Agora o diretório está preparado para ser enviado ao seu repositório no GitLab.

---

### **3. Fazer Push do Tema para o Projeto do GitLab**

#### **Passo 3.1: Inicializar um Repositório Git Local**

1. No diretório do tema, inicialize um novo repositório:
   ```bash
   git init --initial-branch=main
   ```

2. Adicione o repositório remoto do GitLab:
   ```bash
   git remote add origin <URL do repositório GitLab>
   ```
   Substitua `<URL do repositório GitLab>` pela URL do seu repositório, como:
   ```
   https://gitlab.com/seu-usuario/jekyll-blog.git
   ```

3. Configure seu nome e email para este repositório:
   ```bash
   git config --local user.name "Seu Nome no GitLab"
   git config --local user.email "SeuEmail@exemplo.com"
   ```

---

#### **Passo 3.2: Enviar os Arquivos para o Repositório do GitLab**

1. Adicione todos os arquivos ao repositório local:
   ```bash
   git add .
   ```

2. Faça o primeiro commit:
   ```bash
   git commit -m "Initial commit"
   ```

3. Envie os arquivos para o repositório no GitLab:
   ```bash
   git push --set-upstream origin main
   ```

---

#### **Passo 3.3: Verificar os Arquivos no GitLab**

1. Acesse o projeto no GitLab.
2. Verifique se todos os arquivos do tema foram enviados corretamente.

---

### **4. Criar Token de Acesso no Docker Hub**

#### **Passo 4.1: Acessar o Docker Hub**

1. Acesse sua conta no [Docker Hub](https://hub.docker.com).
2. Faça login com seu nome de usuário e senha.

---

#### **Passo 4.2: Navegar para as Configurações de Token**

1. Clique no seu avatar ou nome de usuário no canto superior direito.
2. Vá para **Account Settings > Security > New Access Token**.

---

#### **Passo 4.3: Criar o Token**

1. Na página de tokens, clique em **Create Access Token**.
2. Insira um nome descritivo para o token, como `gitlab-ci-token`.
3. Selecione **Read/Write** nas permissões.
4. Clique em **Generate**.

---

#### **Passo 4.4: Salvar o Token**

1. Copie o token gerado.  
2. Guarde o token em um local seguro, pois você não poderá visualizá-lo novamente.

---

### **5. Criar Variáveis de Acesso do Docker Hub no Projeto do GitLab**

#### **Passo 5.1: Acessar Configurações de CI/CD no GitLab**

1. Acesse o repositório no GitLab.  
2. No menu lateral, clique em **Settings > CI/CD**.

---

#### **Passo 5.2: Adicionar Variáveis do Docker Hub**

1. Role a página até a seção **Variables** e clique em **Expand**.  
2. Clique em **Add Variable** para criar cada variável necessária:
   - **`DOCKER_USERNAME`**:  
     - Valor: seu nome de usuário no Docker Hub.  
     - Tipo: `Variable`.  
     - Protegida: Marque se só será usada em branches protegidas.  
   - **`DOCKER_PASSWORD`**:  
     - Valor: o token gerado no Docker Hub.  
     - Tipo: `Variable`.  
     - Protegida: Marque se só será usada em branches protegidas.

---

#### **Passo 5.3: Salvar e Testar**

1. Após adicionar as variáveis, clique em **Save Variables**.  
2. Verifique se as variáveis foram salvas corretamente.

---

Com essas variáveis configuradas, o pipeline poderá se autenticar no Docker Hub para construir e enviar imagens Docker.

---

### **6. Executar o GitLab Runner no Docker na VPS**

#### **Passo 6.1: Preparar o Ambiente na VPS**

Certifique-se de que sua VPS atende aos requisitos:
1. **Docker instalado**:  
   Caso não esteja instalado, siga este comando básico:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   ```

2. **Permissão de acesso ao Docker**:  
   Adicione seu usuário ao grupo do Docker:
   ```bash
   sudo usermod -aG docker $USER
   ```

---

#### **Passo 6.2: Executar o Contêiner do GitLab Runner**

1. Baixe e execute o GitLab Runner como um contêiner Docker:
   ```bash
   docker run -d --name gitlab-runner \
       --restart always \
       -v /srv/gitlab-runner/config:/etc/gitlab-runner \
       -v /var/run/docker.sock:/var/run/docker.sock \
       gitlab/gitlab-runner:latest
   ```

2. Verifique se o contêiner está em execução:
   ```bash
   docker ps
   ```

Você deve ver o contêiner `gitlab-runner` listado.

---

#### **Passo 6.3: Verificar Configurações do GitLab Runner**

1. Certifique-se de que o diretório de configuração foi criado:
   ```bash
   ls /srv/gitlab-runner/config
   ```
   Deve conter o arquivo `config.toml`, que será atualizado no próximo passo.

---

### **7. Registrar o GitLab Runner**

#### **Passo 7.1: Obter o Token de Registro no GitLab**

1. No repositório GitLab, vá para **Settings > CI/CD > Runners**.  
2. Na seção **Available Runners**, clique em **Expand**.
3. Copie o token de registro exibido para o seu projeto.

---

#### **Passo 7.2: Registrar o Runner**

1. No terminal da VPS, execute o seguinte comando:
   ```bash
   docker exec -it gitlab-runner gitlab-runner register
   ```

2. Siga as instruções interativas:
   - **URL do GitLab**:  
     Insira `https://gitlab.com`.
   - **Token do Runner**:  
     Cole o token copiado no passo anterior.
   - **Descrição**:  
     Insira um nome descritivo, como `runner-chirpy-starter`.
   - **Tags**:  
     Insira tags que identificam o Runner, como `docker` ou `chirpy-starter`.
   - **Executor**:  
     Escolha `docker`.

---

#### **Passo 7.3: Configurar o Executor Docker**

1. Durante o registro, será solicitado para configurar o Docker como executor:
   - **Imagem padrão**:  
     Insira `docker:latest`.

2. Após o registro, o arquivo `/srv/gitlab-runner/config/config.toml` será atualizado.

---

#### **Passo 7.4: Validar o Registro**

1. Verifique o conteúdo do arquivo de configuração:
   ```bash
   sudo cat /srv/gitlab-runner/config/config.toml
   ```

2. Confirme que ele contém as configurações do Runner, incluindo o nome, URL, token e executor.

> Nota: O Runner precisa de privilégios. Certifique-se de que o arquivo está com essa definição.

```bash
sudo cat /srv/gitlab-runner/config/config.toml
[...]
  [runners.docker]
  [...]
    volumes = [
      "/cache",
      "/var/run/docker.sock:/var/run/docker.sock"
    ]
  [...]
```

---

O Runner está configurado e pronto para ser usado com o pipeline.

---

### **8. Criar o Dockerfile**

#### **Passo 8.1: Criar o Arquivo Dockerfile**

1. No diretório do seu projeto local, crie um arquivo chamado `Dockerfile`:
   ```bash
   touch Dockerfile
   ```

2. Abra o arquivo em um editor de texto e copie o seguinte conteúdo:

```dockerfile
# Fase 1: Construção
FROM ruby:3.2-alpine AS builder

# Variáveis de ambiente
ENV BUNDLER_VERSION=2.4.0
ENV JEKYLL_ENV=production

# Instalar dependências necessárias
RUN apk update && apk add --no-cache \
    build-base \
    gcc \
    libc-dev \
    linux-headers \
    nodejs \
    git \
    libxml2-dev \
    libxslt-dev \
    tzdata \
    bash

# Configurar o diretório de trabalho
WORKDIR /usr/src/app

# Copiar Gemfile do chirpy-starter
COPY Gemfile ./

# Instalar Bundler e dependências do Jekyll
RUN gem install bundler -v $BUNDLER_VERSION && bundle install --jobs 4 --retry 3

# Copiar o restante dos arquivos do projeto
COPY . .

# Construir o site com o Chirpy
RUN bundle exec jekyll build --destination /usr/src/app/_site

# Fase 2: Produção
FROM nginx:alpine

# Configurar o diretório de trabalho do nginx
WORKDIR /usr/share/nginx/html

# Remover arquivos padrão do nginx
RUN rm -rf ./*

# Copiar o site gerado na fase anterior
COPY --from=builder /usr/src/app/_site ./

# Expor a porta padrão do nginx
EXPOSE 80

# Comando para iniciar o servidor
CMD ["nginx", "-g", "daemon off;"]
```

---

#### **Passo 8.2: Validar o Dockerfile**

Certifique-se de que o Dockerfile está configurado corretamente:
- **Fase de Construção**:  
  Configura o ambiente, instala o Jekyll e constrói o site.
- **Fase de Produção**:  
  Copia os arquivos construídos para um contêiner do Nginx para servir o site.

---

O Dockerfile está pronto para ser usado no pipeline.

---

### **9. Configurar o Pipeline para Usar o Runner**

#### **Passo 9.1: Criar o Arquivo `.gitlab-ci.yml`**

1. No diretório do projeto local, crie o arquivo de configuração do pipeline:
   ```bash
   touch .gitlab-ci.yml
   ```

2. Abra o arquivo em um editor de texto e copie o seguinte conteúdo:

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_NAME: $DOCKER_USERNAME/chirpy-starter  # Nome da imagem no Docker Hub

# Job para construir e enviar a imagem para o Docker Hub
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - echo "=== Logando no Docker Hub ==="
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
    - echo "=== Construindo a imagem Docker ==="
    - docker build -t $IMAGE_NAME:$CI_COMMIT_SHA .
    - docker tag $IMAGE_NAME:$CI_COMMIT_SHA $IMAGE_NAME:latest
    - echo "=== Enviando a imagem para o Docker Hub ==="
    - docker push $IMAGE_NAME:$CI_COMMIT_SHA
    - docker push $IMAGE_NAME:latest
  only:
    - main

# Job para deploy na VPS
deploy:
  stage: deploy
  image: docker/compose:latest
  tags:
    - runner-chirpy-starter  # Tag para direcionar ao runner na VPS
  before_script:
    - echo "=== Logando no Docker Host ==="
    - docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"
  script:
    - echo "=== Parando contêiner existente ==="
    - docker stop chirpy-starter || echo "Nenhum contêiner em execução para parar"
    - echo "=== Removendo contêiner antigo ==="
    - docker rm chirpy-starter || echo "Nenhum contêiner antigo para remover"
    - echo "=== Iniciando novo contêiner ==="
    - docker run -d --name chirpy-starter -p 8092:80 $IMAGE_NAME:latest
    - echo "=== Novo contêiner iniciado com sucesso ==="
  only:
    - main
```

---

#### **Passo 9.2: Configurar o Pipeline**

1. **Staging**:  
   - Define dois estágios: `build` (para construir e enviar a imagem) e `deploy` (para implementar o site na VPS).
   
2. **Build Job**:  
   - Constrói a imagem Docker e a envia para o Docker Hub.
   
3. **Deploy Job**:  
   - Baixa a imagem Docker do Docker Hub e inicia o contêiner na VPS.

4. **Tags do Runner**:  
   - Certifique-se de que as tags correspondem às do Runner configurado no GitLab.

---

#### **Passo 9.3: Salvar o Arquivo**

Certifique-se de salvar o arquivo como `.gitlab-ci.yml` no diretório raiz do projeto.

---

### **10. Fazer Push do Dockerfile e do `.gitlab-ci.yml`**

#### **Passo 10.1: Preparar os Arquivos para Envio**

1. No diretório do projeto local, adicione o `Dockerfile` e o `.gitlab-ci.yml` ao repositório:
   ```bash
   git add Dockerfile .gitlab-ci.yml
   ```

2. Faça o commit das alterações:
   ```bash
   git commit -m "Adiciona Dockerfile e configuração de pipeline no .gitlab-ci.yml"
   ```

---

#### **Passo 10.2: Fazer o Push para o Repositório do GitLab**

1. Envie os arquivos para o repositório remoto:
   ```bash
   git push
   ```

2. Verifique se os arquivos foram carregados corretamente no GitLab, acessando o repositório pelo navegador.

---

Com o push concluído, o pipeline será automaticamente iniciado pelo GitLab.

---

### **11. Acompanhar a Execução da Pipeline no GitLab**

#### **Passo 11.1: Acessar a Seção de Pipelines**

1. No repositório do projeto no GitLab, clique em **CI/CD > Pipelines** no menu lateral.

---

#### **Passo 11.2: Visualizar a Execução da Pipeline**

1. Na página de **Pipelines**, você verá a lista das pipelines executadas.
2. Para acompanhar os detalhes, clique na última pipeline criada (provavelmente a que foi acionada pelo push do `.gitlab-ci.yml`).

---

#### **Passo 11.3: Monitorar os Jobs**

1. Na página da pipeline, observe os dois estágios principais:
   - **build**: Responsável por construir a imagem Docker e enviá-la ao Docker Hub.
   - **deploy**: Responsável por baixar a imagem e rodar o contêiner na VPS.
2. Clique em cada job para visualizar os logs em tempo real.

---

#### **Passo 11.4: Corrigir Erros, se Necessário**

1. Caso algum job falhe:
   - Analise os logs para identificar o erro.
   - Verifique configurações, como variáveis de ambiente ou permissões no Docker Hub.
   - Faça as correções no projeto e envie um novo commit para reiniciar a pipeline.

---

#### **Passo 11.5: Confirmar a Execução**

1. Após a pipeline ser concluída com sucesso, você verá o status **Passed** ao lado da execução.
2. O site estará rodando no contêiner na sua VPS.

---

### **12. Criar um Proxy Reverso com o Nginx para o Jekyll**

#### **Passo 12.1: Instalar o Nginx na VPS**

1. Acesse sua VPS via SSH.  
2. Instale o Nginx com o seguinte comando:
   ```bash
   sudo apt update
   sudo apt install nginx
   ```

3. Verifique se o Nginx está em execução:
   ```bash
   sudo systemctl status nginx
   ```
   O status deve indicar **active (running)**.

---

#### **Passo 12.2: Configurar o Proxy Reverso**

1. Crie um arquivo de configuração para o site:
   ```bash
   sudo vim /etc/nginx/sites-available/jekyll
   ```

2. Adicione a seguinte configuração ao arquivo:

```nginx
server {
    server_name geanmartins.com.br;

    location / {
        proxy_pass http://localhost:8092;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

3. Ative o site criando um link simbólico:
   ```bash
   sudo ln -s /etc/nginx/sites-available/jekyll /etc/nginx/sites-enabled/
   ```

4. Verifique a configuração do Nginx:
   ```bash
   sudo nginx -t
   ```

5. Reinicie o Nginx para aplicar as alterações:
   ```bash
   sudo systemctl restart nginx
   ```

---

#### **Passo 12.3: Configurar o Certificado SSL**

1. Instale o Certbot para gerenciar os certificados SSL:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

2. Gere e aplique o certificado SSL com o Certbot:
   ```bash
   sudo certbot --nginx -d geanmartins.com.br -d www.geanmartins.com.br
   ```

3. Certifique-se de que o certificado foi configurado corretamente. O Certbot ajustará automaticamente a configuração do Nginx para redirecionar o tráfego HTTP para HTTPS.

4. Configure a renovação automática dos certificados:
   ```bash
   sudo certbot renew --dry-run
   ```

---

Com o proxy reverso configurado, o site estará acessível em **https://geanmartins.com.br**. O Nginx gerencia as solicitações externas e as redireciona para o contêiner do Jekyll.

---
