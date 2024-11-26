---
layout: post
title: "Pipeline CI/CD no GitLab para Blog Jekyll com Docker"
date: 2024-11-16 22:23:24 -0300
categories: [CI/CD, DevOps, Jekyll]
tags: [GitLab, Docker, Nginx, Pipeline, Jekyll]
excerpt: "Um guia completo para implementar uma pipeline CI/CD no GitLab para um blog Jekyll usando Docker e Nginx com proxy reverso."
---

## **Introdução**

O **Jekyll** é uma ferramenta poderosa para criar sites estáticos a partir de arquivos Markdown, ideal para blogs e documentações. Combinando Jekyll com uma pipeline CI/CD no GitLab, Docker para contêineres e Nginx como proxy reverso, garantimos:

- Automatização do processo de publicação.
- Manutenção de um ambiente leve e portátil.
- Alto desempenho, com suporte a HTTPS.

Neste tutorial, você aprenderá a:
1. Configurar um repositório no GitLab.
2. Construir e publicar imagens Docker.
3. Configurar um pipeline CI/CD para deploy automático.
4. Implementar um proxy reverso com Nginx e SSL.

---

## **Arquitetura do Projeto**

### **Fluxo do Pipeline CI/CD**
1. **Commit:** O desenvolvedor realiza alterações e faz push no GitLab.
2. **Pipeline:** Jobs automatizados constroem a imagem Docker, verificam erros e publicam no Docker Hub.
3. **Deploy:** A imagem é usada para rodar um contêiner na VPS.
4. **Nginx:** Atua como proxy reverso para servir o site com HTTPS.

### **Componentes**
- **GitLab CI/CD:** Automatiza build e deploy.
- **Docker:** Empacota o site estático gerado pelo Jekyll.
- **Nginx:** Gerencia solicitações HTTP/HTTPS.
- **Certbot:** Configura SSL gratuito.

---

## **1. Configurando o Projeto no GitLab**

Nesta etapa, configuraremos o repositório no GitLab e prepararemos o tema **Chirpy-Starter** para começar a construção do blog.

### **1.1 Criar o Repositório no GitLab**
1. Faça login no [GitLab](https://gitlab.com).
2. Navegue para **Menu > Projects > New Project**.
3. Clique em **Create Blank Project**.
4. Preencha os campos:
   - **Project name:** `jekyll-blog`.
   - **Visibility Level:** Escolha `Private` (somente você) ou `Public` (visível a todos).
5. Clique em **Create Project**.

---

### **1.2 Configurar o Tema Chirpy-Starter**

O Chirpy-Starter oferece um design responsivo e funcionalidades integradas como SEO e tabelas de conteúdo automáticas.

1. Clone o repositório oficial:
   ```bash
   mkdir jekyll-blog
   cd jekyll-blog/
   git clone https://github.com/cotes2020/chirpy-starter.git chirpy-temp
   cp -r chirpy-temp/* .
   rm -rf chirpy-temp
   ```

2. Configure o repositório para o seu GitLab:
   ```bash
   git init --initial-branch=main
   git remote add origin https://gitlab.com/seu-usuario/jekyll-blog.git
   git add .
   git commit -m "Initial commit"
   git push -u origin main
   ```

---

### **1.3 Verificar os Arquivos no Repositório**

1. Acesse o repositório no GitLab.
2. Confirme se todos os arquivos foram carregados corretamente.

---

### **1.4 Criar Token de Acesso no Docker Hub**

#### **Acessar o Docker Hub**

1. Acesse sua conta no [Docker Hub](https://hub.docker.com).
2. Faça login com seu nome de usuário e senha.

#### **Navegar para as Configurações de Token**

1. Clique no seu avatar ou nome de usuário no canto superior direito.
2. Vá para **Account Settings > Security > New Access Token**.

#### **Criar o Token**

1. Na página de tokens, clique em **Create Access Token**.
2. Insira um nome descritivo para o token, como `gitlab-ci-token`.
3. Selecione **Read/Write** nas permissões.
4. Clique em **Generate**.

#### **Salvar o Token**

1. Copie o token gerado.  
2. Guarde o token em um local seguro, pois você não poderá visualizá-lo novamente.

---

### **1.5 Criar Variáveis de Acesso do Docker Hub no Projeto do GitLab**

#### **Acessar Configurações de CI/CD no GitLab**

1. Acesse o repositório no GitLab.  
2. No menu lateral, clique em **Settings > CI/CD**.

#### **Adicionar Variáveis do Docker Hub**

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

#### **Salvar e Testar**

1. Após adicionar as variáveis, clique em **Save Variables**.  
2. Verifique se as variáveis foram salvas corretamente.

---

### **2. Criando o Dockerfile e Preparando o Ambiente de Build**

O Dockerfile define como a imagem do contêiner será construída, encapsulando o Jekyll e suas dependências em um ambiente isolado. Usaremos um **multi-stage build** para criar uma imagem otimizada.

---

### **2.1 Criar o Dockerfile**

1. No diretório raiz do projeto, crie um arquivo chamado `Dockerfile`:
   ```bash
   touch Dockerfile
   ```

2. Edite o `Dockerfile` com o seguinte conteúdo:

```dockerfile
# Etapa 1: Construir os arquivos estáticos com o Jekyll
FROM ruby:3.3-alpine AS builder

# Defina o diretório de trabalho
WORKDIR /app

# Copie o Gemfile e o Gemfile.lock para o container
COPY Gemfile ./

# Instale as dependências
RUN bundle install

# Copie o restante dos arquivos da aplicação
COPY . .

# Gere os arquivos estáticos
RUN bundle exec jekyll build --destination /app/_site

# Etapa 2: Servir os arquivos estáticos com Nginx
FROM nginx:1.27-alpine

# Copie os arquivos estáticos da etapa de build para o diretório do Nginx
COPY --from=builder /app/_site /usr/share/nginx/html

# Exponha a porta padrão do Nginx
EXPOSE 80

# Use o comando padrão do Nginx
CMD ["nginx", "-g", "daemon off;"]
```

---

### **2.2 Explicando o Dockerfile**

1. **Fase de Construção (`builder`):**
   - Usa a imagem leve `ruby:3.3-alpine` para instalar o Jekyll.
   - Instala todas as dependências necessárias e compila o site para a pasta `_site`.

2. **Fase de Produção:**
   - Usa `nginx:1.27-alpine` para servir o site estático gerado.
   - Apenas os arquivos necessários são copiados, reduzindo o tamanho final da imagem.

---

### **2.3 Testar o Dockerfile Localmente**

1. Construa a imagem Docker:
   ```bash
   docker build -t jekyll-blog .
   ```

2. Execute o contêiner para testar:
   ```bash
   docker run -d --name jekyll-container -p 8080:80 jekyll-blog
   ```

3. Acesse o site em: [http://localhost:8080](http://localhost:8080)

4. Verifique os logs do contêiner (se necessário):
   ```bash
   docker logs jekyll-container
   ```

---

### **2.4 Otimizações e Boas Práticas**

1. **Cache de Dependências:** Use cache para acelerar builds futuros:
   ```dockerfile
   RUN bundle config set path 'vendor/bundle' && bundle install
   ```

2. **Verificação de Vulnerabilidades:** Use o Trivy para escanear a imagem:
   ```bash
   trivy image jekyll-blog
   ```

3. **Reduza o Tamanho da Imagem:** Certifique-se de que apenas os arquivos essenciais são copiados para a fase de produção.

---

### **3. Configurando o Pipeline CI/CD no GitLab**

Agora, vamos configurar o pipeline CI/CD no GitLab para automatizar a construção, teste e deploy do blog Jekyll. O pipeline será definido no arquivo `.gitlab-ci.yml`.

---

### **3.1 Criar o Arquivo `.gitlab-ci.yml`**

1. No diretório raiz do projeto, crie o arquivo `.gitlab-ci.yml`:
   ```bash
   touch .gitlab-ci.yml
   ```

2. Adicione o seguinte conteúdo básico ao arquivo:

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
    - echo "=== Obtendo o digest da imagem ==="
      # Extraindo o digest da imagem enviada para uso no deploy
    - IMAGE_DIGEST=$(docker inspect --format='{{index .RepoDigests 0}}' $IMAGE_NAME:$CI_COMMIT_SHA)
    - echo $IMAGE_DIGEST > image_digest.txt
  artifacts:
    paths:
      - image_digest.txt  # Salva o digest da imagem para o job de deploy
  only:
    - main

# Job para deploy na VPS
deploy:
  stage: deploy
  image: docker:latest
  tags:
    - chirpy-starter
  before_script:
    - echo "=== Logando no Docker Host ==="
    - docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"
  script:
    - |
      IMAGE_DIGEST=$(cat image_digest.txt)
      if [ -z "$IMAGE_DIGEST" ]; then
        echo "Erro: IMAGE_DIGEST está vazio ou não definido."
        exit 1
      fi
      echo "Imagem a ser usada: ${IMAGE_DIGEST}"
    - echo "=== Removendo imagens locais antigas ==="
    - docker rmi $IMAGE_NAME:latest || echo "Imagem local 'latest' não encontrada"
    - docker rmi $IMAGE_NAME:$CI_COMMIT_SHA || echo "Imagem local específica não encontrada"
    - echo "=== Baixando a imagem mais recente ==="
    - docker pull $IMAGE_DIGEST
    - echo "=== Parando e removendo contêiner antigo ==="
    - docker stop chirpy-starter || echo "Nenhum contêiner em execução para parar"
    - docker rm chirpy-starter || echo "Nenhum contêiner antigo para remover"
    - echo "=== Iniciando novo contêiner ==="
    - docker run -d --name chirpy-starter -p 8092:80 $IMAGE_DIGEST
    - echo "=== Novo contêiner iniciado com sucesso ==="
  dependencies:
    - build
  only:
    - main
```

---

### **3.2 Explicação do Pipeline**

1. **Stages:**
   - **Build:** Constrói a imagem Docker e a publica no Docker Hub.
   - **Test:** Valida o site gerado, verificando links e integridade.
   - **Deploy:** Faz deploy da imagem Docker em uma VPS.

2. **Jobs:**
   - **Build:** Usa `docker:dind` para criar e enviar imagens Docker.
   - **Test Links:** Verifica links quebrados usando `HTMLProofer`.
   - **Deploy:** Substitui o contêiner em execução na VPS pelo novo.

3. **Triggers:**
   - O pipeline é executado automaticamente em push para a branch `main`.

---

### **3.3 Agendar Builds Automáticos**

1. Acesse **CI/CD > Schedules** no GitLab.
2. Configure a execução automática da pipeline semanalmente ou em intervalos definidos.
3. Escolha a branch (`main`) e salve o agendamento.

---

### **3.4 Testar o Pipeline**

1. Faça um commit para a branch `main`:
   ```bash
   git add .gitlab-ci.yml
   git commit -m "Adiciona pipeline CI/CD ao projeto"
   git push
   ```

2. Verifique a execução do pipeline:
   - Acesse **CI/CD > Pipelines** no repositório.
   - Veja o progresso e logs de cada job.

---

### **4. Configurando o GitLab Runner na VPS**

O GitLab Runner será configurado em uma VPS para executar o job de deploy. Essa configuração permite que o pipeline faça o deploy diretamente no ambiente de produção.

---

### **4.1 Preparar a VPS**

1. **Instalar Docker na VPS**:
   - Faça login na VPS via SSH.
   - Instale o Docker:
     ```bash
     curl -fsSL https://get.docker.com -o get-docker.sh
     sudo sh get-docker.sh
     ```
   - Adicione seu usuário ao grupo Docker para evitar usar `sudo` em todos os comandos:
     ```bash
     sudo usermod -aG docker $USER
     ```

2. **Reinicie a sessão para aplicar as permissões**:
   ```bash
   exit
   ssh usuario@seu-ip
   ```

3. **Verifique a instalação do Docker**:
   ```bash
   docker --version
   ```

---

### **4.2 Configurar o GitLab Runner**

1. **Executar o GitLab Runner como contêiner Docker**:
   - Baixe e inicie o GitLab Runner:
     ```bash
     docker run -d --name gitlab-runner \
         --restart always \
         -v /srv/gitlab-runner/config:/etc/gitlab-runner \
         -v /var/run/docker.sock:/var/run/docker.sock \
         gitlab/gitlab-runner:latest
     ```

2. **Verificar se o contêiner está rodando**:
   ```bash
   docker ps
   ```

   Você verá uma saída com o contêiner `gitlab-runner` listado.

---

### **4.3 Registrar o GitLab Runner**

1. **Obter o Token de Registro no GitLab**:
   - Acesse o repositório no GitLab.
   - Navegue até **Settings > CI/CD > Runners**.
   - Clique em **Expand** na seção **Available Runners** e copie o token de registro.

2. **Registrar o Runner**:
   - No terminal da VPS, execute:
     ```bash
     docker exec -it gitlab-runner gitlab-runner register
     ```
   - Siga as instruções interativas:
     - **URL do GitLab**: `https://gitlab.com`
     - **Token do Runner**: Cole o token copiado.
     - **Descrição**: `runner-jekyll`
     - **Tags**: `runner-jekyll` (use as mesmas tags no `.gitlab-ci.yml`).
     - **Executor**: Escolha `docker`.

3. **Configurar o Executor Docker**:
   - Durante o registro, configure o executor para usar a imagem Docker padrão:
     ```text
     Please enter the Docker image (eg. ruby:2.6):
     [docker:latest]
     ```

---

### **4.4 Verificar a Configuração**

1. Após o registro, verifique o arquivo `config.toml` do GitLab Runner:
   ```bash
   sudo cat /srv/gitlab-runner/config/config.toml
   ```

2. Certifique-se de que as tags, URL e executor estão configurados corretamente:
   ```toml
   [[runners]]
     name = "runner-jekyll"
     url = "https://gitlab.com"
     token = "seu-token"
     executor = "docker"
     [runners.docker]
       image = "docker:latest"
       privileged = true
       volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock"]
   ```

3. Reinicie o Runner para aplicar as alterações:
   ```bash
   docker restart gitlab-runner
   ```

---

### **4.5 Testar o GitLab Runner**

1. Faça um commit na branch `main` para disparar o pipeline:
   ```bash
   git commit --allow-empty -m "Testando o GitLab Runner"
   git push
   ```

2. No GitLab, acesse **CI/CD > Pipelines** e verifique se o job de deploy é executado com o Runner configurado.

---

### **5. Configurando o Proxy Reverso com Nginx**

Agora, vamos configurar o Nginx na VPS para servir o site gerado pelo contêiner do Jekyll. O Nginx atuará como proxy reverso, redirecionando as solicitações para o contêiner e fornecendo suporte a HTTPS usando o Certbot.

---

### **5.1 Instalar e Configurar o Nginx**

1. **Instalar o Nginx**:
   - Conecte-se à VPS via SSH.
   - Instale o Nginx:
     ```bash
     sudo apt update
     sudo apt install nginx
     ```

2. **Verificar se o Nginx está ativo**:
   ```bash
   sudo systemctl status nginx
   ```
   O status deve indicar **active (running)**.

3. **Criar a Configuração do Site**:
   - Crie um arquivo de configuração para o proxy:
     ```bash
     sudo vim /etc/nginx/sites-available/jekyll
     ```

   - Adicione o seguinte conteúdo ao arquivo:
     ```nginx
     server {
         server_name geanmartins.com.br www.geanmartins.com.br;

         location / {
             proxy_pass http://localhost:8092;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }
     }
     ```

4. **Ativar o Site**:
   - Crie um link simbólico para habilitar a configuração:
     ```bash
     sudo ln -s /etc/nginx/sites-available/jekyll /etc/nginx/sites-enabled/
     ```

   - Teste a configuração do Nginx:
     ```bash
     sudo nginx -t
     ```

   - Reinicie o Nginx para aplicar as alterações:
     ```bash
     sudo systemctl restart nginx
     ```

---

### **5.2 Configurar HTTPS com Certbot**

1. **Instalar o Certbot**:
   - Instale o Certbot e o plugin para Nginx:
     ```bash
     sudo apt install certbot python3-certbot-nginx
     ```

2. **Obter o Certificado SSL**:
   - Execute o Certbot para configurar o HTTPS:
     ```bash
     sudo certbot --nginx -d geanmartins.com.br -d www.geanmartins.com.br
     ```

3. **Verificar a Instalação do SSL**:
   - Certifique-se de que o Certbot atualizou o arquivo de configuração do Nginx.
   - Acesse o site em [https://geanmartins.com.br](https://geanmartins.com.br).

4. **Configurar Renovação Automática**:
   - Verifique a renovação automática:
     ```bash
     sudo certbot renew --dry-run
     ```
   - Adicione um cron job para executar a renovação regularmente:
     ```bash
     crontab -e
     ```
     Adicione a linha:
     ```bash
     0 3 * * * certbot renew --quiet
     ```

---

### **5.3 Testar a Configuração**

1. **Verificar o Acesso ao Site**:
   - Acesse o site usando HTTP e HTTPS para confirmar que o redirecionamento está funcionando.

2. **Validar Logs do Nginx**:
   - Inspecione os logs para identificar possíveis problemas:
     ```bash
     sudo tail -f /var/log/nginx/access.log /var/log/nginx/error.log
     ```

---

### **6. Resumo e Validação Final**

Agora que o blog Jekyll foi configurado e publicado, vamos validar todo o fluxo para garantir que o sistema funcione conforme esperado.

---

### **6.1 Validação de Componentes**

#### **Pipeline CI/CD**
- **Objetivo:** Automatizar build, teste e deploy.
- **Verificação:**
  1. Faça um commit na branch `main`.
  2. No GitLab, acesse **CI/CD > Pipelines** e confirme que os jobs:
     - **Build**: Constrói e publica a imagem Docker.
     - **Test**: Valida links e o site gerado.
     - **Deploy**: Substitui o contêiner na VPS.
  3. Todos os jobs devem ser marcados como **Passed**.

#### **Contêiner Docker**
- **Objetivo:** Rodar o site gerado pelo Jekyll.
- **Verificação:**
  1. Acesse a VPS e liste os contêineres:
     ```bash
     docker ps
     ```
  2. Verifique se o contêiner `jekyll-blog` está ativo e escutando na porta `8092`.

#### **Nginx e Proxy Reverso**
- **Objetivo:** Redirecionar o tráfego para o contêiner.
- **Verificação:**
  1. Abra o navegador e acesse:
     - [http://geanmartins.com.br](http://geanmartins.com.br)
     - [https://geanmartins.com.br](https://geanmartins.com.br)
  2. Confirme que o HTTPS está funcionando.

#### **Certificado SSL**
- **Objetivo:** Garantir a segurança do tráfego.
- **Verificação:**
  1. Use uma ferramenta online como [SSL Labs](https://www.ssllabs.com/ssltest/) para verificar o certificado.
  2. Certifique-se de que o Certbot está configurado para renovação automática.

---

### **6.2 Dicas de Manutenção**

1. **Atualizações do Pipeline:**
   - Monitore a integridade das imagens Docker e dependências.
   - Use `Trivy` ou `Snyk` para verificar vulnerabilidades periodicamente.

2. **Logs e Monitoramento:**
   - Inspecione os logs regularmente:
     ```bash
     sudo tail -f /var/log/nginx/access.log
     docker logs jekyll-blog
     ```

3. **Renovação de Certificados:**
   - Garanta que o cron job do Certbot está ativo e funcionando.

---
