---
layout: post
title: "Guia Completo de Docker: Do Básico ao Avançado"
date: 2024-11-06 15:16:17 -0300
categories: [DevOps, Docker, Tutoriais]
tags: [Docker, Containers, DevOps, Tutoriais]
description: "Aprenda Docker do básico ao avançado com um guia detalhado, cobrindo conceitos, configuração, orquestração e melhores práticas."
---

## Introdução

O Docker transformou a maneira como desenvolvedores e equipes de TI criam, testam e implantam aplicações. Ao permitir que aplicações sejam empacotadas com todas as suas dependências em containers, Docker simplifica o desenvolvimento e garante que as aplicações rodem de maneira consistente em diferentes ambientes, seja no desenvolvimento local, no teste ou na produção.

Neste guia completo, você aprenderá desde os conceitos fundamentais de containerização até funcionalidades avançadas do Docker. Vamos abordar desde a instalação e configuração inicial até técnicas de orquestração com Docker Compose, uso de redes, volumes para persistência de dados, otimização e segurança de containers. Com um passo a passo detalhado, este tutorial oferece tudo o que você precisa para dominar o uso do Docker em projetos de qualquer escala, incluindo integração com CI/CD para automação e deploy contínuo.

Prepare-se para explorar o poder do Docker e elevar suas habilidades em DevOps, criando ambientes isolados, seguros e escaláveis para suas aplicações!

---

## Sumário

1. [Introdução ao Docker e Conceitos de Containerização](#1-introdução-ao-docker-e-conceitos-de-containerização)
2. [Instalação e Configuração do Docker](#2-instalação-e-configuração-do-docker)
3. [Trabalhando com Imagens no Docker](#3-trabalhando-com-imagens-no-docker)
4. [Criando e Gerenciando Containers](#4-criando-e-gerenciando-containers)
6. [Introdução ao Dockerfile: Construindo Imagens Personalizadas](#5-introdução-ao-dockerfile-construindo-imagens-personalizadas)
7. [Gerenciamento de Volumes no Docker](#6-gerenciamento-de-volumes-no-docker)
8. [Rede e Comunicação entre Containers](#7-rede-e-comunicação-entre-containers)
9. [Docker Compose: Orquestrando Múltiplos Containers](#8-docker-compose-orquestrando-múltiplos-containers)
10. [Trabalhando com Registries e Docker Hub](#9-trabalhando-com-registries-e-docker-hub)
11. [Otimização e Melhores Práticas com Containers](#10-otimização-e-melhores-práticas-com-containers)
12. [Monitoramento e Log de Containers](#11-monitoramento-e-log-de-containers)
13. [Salvando e Carregando Imagens e Containers](#12-salvando-e-carregando-imagens-e-containers)
14. [Troubleshooting e Solução de Problemas Comuns no Docker](#13-troubleshooting-e-solução-de-problemas-comuns-no-docker)
15. [Casos de Uso Avançados e Integração com CI/CD](#14-casos-de-uso-avançados-e-integração-com-cicd)

---

## **1. Introdução ao Docker e Conceitos de Containerização**

### **Objetivo**
Apresentar o Docker, suas principais funcionalidades e a importância da containerização. Compreender os conceitos de imagens, containers, registries e orquestração é essencial para o uso adequado do Docker.

### **O que é o Docker?**
Docker é uma plataforma de código aberto que facilita a criação, o gerenciamento e a execução de containers. Containers são ambientes isolados que contêm aplicações e todas as suas dependências, o que facilita a portabilidade e a consistência entre diferentes ambientes (desenvolvimento, teste e produção).

#### **Containers vs. Máquinas Virtuais (VMs)**

| Característica        | Containers                           | Máquinas Virtuais (VMs)                             |
|-----------------------|--------------------------------------|-----------------------------------------------------|
| **Isolamento**        | Processos e namespaces               | Sistemas operacionais inteiros                      |
| **Uso de recursos**   | Leve (compartilha o kernel)         | Pesado (cada VM tem seu próprio SO)                 |
| **Início/Desligamento** | Rápido (segundos)                 | Lento (minutos)                                     |
| **Portabilidade**     | Alta                                | Limitada, depende do hypervisor                     |
| **Armazenamento**     | Volumes e redes do Docker           | Discos virtuais                                     |

Containers compartilham o kernel do sistema operacional do host, o que os torna mais leves e rápidos em comparação com VMs, que exigem um sistema operacional completo para cada instância.

### **Principais Conceitos do Docker**

1. **Imagens**: São modelos somente-leitura usados para criar containers. Cada imagem contém todos os elementos necessários para a aplicação (sistema de arquivos, bibliotecas, dependências).
   
2. **Containers**: São instâncias de uma imagem em execução. Um container é a unidade básica em que as aplicações e seus ambientes operacionais executam de forma isolada.

3. **Registries**: São repositórios para armazenar e compartilhar imagens Docker. O Docker Hub é o registry padrão, mas também é possível configurar registries privados para compartilhar imagens de maneira controlada.

4. **Orquestração**: Refere-se à prática de gerenciar múltiplos containers que compõem uma aplicação. Ferramentas como Docker Compose e Kubernetes ajudam na orquestração de containers para aplicações mais complexas e escaláveis.

### **Vantagens da Containerização com Docker**

1. **Consistência entre ambientes**: Containers oferecem consistência, pois a aplicação e suas dependências estão dentro de um único ambiente que pode ser replicado em qualquer máquina.
   
2. **Eficiência de recursos**: Por compartilharem o kernel do sistema operacional, containers utilizam menos recursos que VMs.
   
3. **Escalabilidade**: Containers podem ser escalados rapidamente, ideal para sistemas distribuídos e de microserviços.
   
4. **Desenvolvimento acelerado**: Docker permite a criação de ambientes prontos para desenvolvimento rapidamente, eliminando problemas de configuração de ambiente.

### **Fluxo de Trabalho Básico com Docker**

1. **Buscar e criar imagens**: A partir do Docker Hub ou outras fontes, utilizando `docker pull`.
2. **Criar containers**: Executando `docker run` para iniciar um container baseado em uma imagem.
3. **Gerenciar containers**: Parando, reiniciando e removendo containers conforme necessário.
4. **Persistência e redes**: Usando volumes para persistência de dados e redes para comunicação entre containers.

### **Exemplo Prático de Uso do Docker**

Para ilustrar o fluxo básico de trabalho com Docker, vejamos um exemplo simples:

1. **Buscar uma Imagem**:
   ```bash
   docker pull nginx
   ```
   Este comando baixa a imagem do servidor web NGINX do Docker Hub.

2. **Criar e Executar um Container**:
   ```bash
   docker run -d -p 8080:80 nginx
   ```
   Aqui, o Docker cria um container baseado na imagem NGINX, em modo *detached* (`-d`), e mapeia a porta 80 do container para a porta 8080 no host.

3. **Listar Containers em Execução**:
   ```bash
   docker ps
   ```
   Este comando exibe todos os containers que estão em execução. Veremos o container NGINX com suas configurações de porta e status.

4. **Parar e Remover o Container**:
   ```bash
   docker stop <container_id>
   docker rm <container_id>
   ```
   Usando o ID do container, podemos pará-lo e removê-lo. Isso libera recursos no sistema.

---

## **2. Instalação e Configuração do Docker**

### **Objetivo**
Guiar o usuário no processo de instalação e configuração inicial do Docker. Ao final desta seção, você será capaz de verificar a instalação do Docker e ajustar permissões para gerenciar containers.

### **Pré-Requisitos**
- Acesso a um sistema com Debian 11 ou Oracle Linux 8.
- Acesso root ou permissões de superusuário (sudo) para instalação.

### **Passo a Passo para Instalar o Docker no Debian 11**

1. **Atualize o Sistema**:
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

2. **Instale Dependências Necessárias**:
   Instale pacotes que permitirão o uso do repositório HTTPS.
   ```bash
   sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
   ```

3. **Adicione a Chave GPG do Docker**:
   ```bash
   curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **Adicione o Repositório Docker**:
   ```bash
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Instale o Docker**:
   Atualize a lista de pacotes e instale o Docker.
   ```bash
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io -y
   ```

6. **Verifique a Instalação**:
   Após a instalação, confirme se o Docker está funcionando:
   ```bash
   docker --version
   docker info
   ```
   O comando `docker --version` exibirá a versão instalada, enquanto `docker info` mostrará detalhes do ambiente Docker.

---

### **Instalação do Docker no Oracle Linux 8**

1. **Atualize o Sistema**:
   ```bash
   sudo dnf update -y
   ```

2. **Adicione o Repositório Docker**:
   Adicione o repositório oficial do Docker para Oracle Linux.
   ```bash
   sudo dnf config-manager --add-repo=https://download.docker.com/linux/oracle/docker-ce.repo
   ```

3. **Instale o Docker**:
   ```bash
   sudo dnf install docker-ce docker-ce-cli containerd.io -y
   ```

4. **Inicie e Ative o Serviço Docker**:
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

5. **Verifique a Instalação**:
   Assim como no Debian, use:
   ```bash
   docker --version
   docker info
   ```

---

### **Configuração Inicial e Permissões para Usuário**

Após a instalação, configure permissões para que um usuário comum possa executar comandos Docker sem precisar do `sudo` toda vez.

1. **Crie o Grupo Docker (caso não exista)**:
   ```bash
   sudo groupadd docker
   ```

2. **Adicione seu Usuário ao Grupo Docker**:
   ```bash
   sudo usermod -aG docker $USER
   ```
   > Observação: Substitua `$USER` pelo nome do usuário, se estiver configurando para outro usuário.

3. **Aplique as Alterações de Grupo**:
   Você precisará fazer logoff e logon novamente para que as permissões sejam aplicadas. Alternativamente, pode executar:
   ```bash
   newgrp docker
   ```

4. **Teste a Permissão**:
   Execute um comando Docker sem `sudo` para verificar:
   ```bash
   docker run hello-world
   ```
   Esse comando baixa e executa a imagem `hello-world`, exibindo uma mensagem de sucesso se o Docker estiver corretamente configurado.

---

### **Resolução de Problemas Comuns na Instalação**

1. **Erro: Docker Daemon não está rodando**:
   - Verifique o status do Docker:
     ```bash
     sudo systemctl status docker
     ```
   - Inicie o Docker se necessário:
     ```bash
     sudo systemctl start docker
     ```

2. **Erro de Permissão**:
   Se houver erro de permissão ao executar comandos Docker sem `sudo`, verifique se o usuário foi adicionado ao grupo `docker` corretamente e se o login foi reiniciado.

---

## **3. Trabalhando com Imagens no Docker**

### **Objetivo**
Ensinar como localizar, baixar e gerenciar imagens Docker para construir containers. Este é um passo fundamental, pois as imagens formam a base dos containers.

### **Principais Comandos para Gerenciamento de Imagens**

| Comando                  | Descrição                                                    |
|--------------------------|--------------------------------------------------------------|
| `docker pull <imagem>`   | Baixa uma imagem do Docker Hub ou de um registry especificado. |
| `docker images`          | Lista todas as imagens baixadas e disponíveis localmente.    |
| `docker rmi <imagem>`    | Remove uma imagem especificada do sistema.                   |
| `docker build`           | Constrói uma imagem a partir de um Dockerfile.               |
| `docker tag`             | Marca uma imagem para facilitar a identificação e uso.       |

### **1. Baixando Imagens com `docker pull`**

O comando `docker pull` permite buscar e baixar imagens do Docker Hub ou de um registry especificado. Vamos utilizar o **Docker Hub** para buscar uma imagem popular, como o `nginx`.

```bash
docker pull nginx
```

Este comando busca a última versão da imagem `nginx`. Você também pode especificar uma versão específica da imagem (conhecida como "tag"):

```bash
docker pull nginx:1.21
```

> **Nota**: Cada versão ou tag representa uma versão específica da aplicação ou do sistema, facilitando o uso de versões estáveis ou personalizadas.

### **2. Listando Imagens Disponíveis com `docker images`**

Para ver as imagens baixadas e disponíveis no seu ambiente local, use o comando `docker images`:

```bash
docker images
```

Este comando exibe uma lista com as colunas principais, incluindo:

- **REPOSITORY**: O nome da imagem.
- **TAG**: A tag ou versão da imagem.
- **IMAGE ID**: Identificador único da imagem.
- **CREATED**: Tempo decorrido desde a criação.
- **SIZE**: Tamanho da imagem.

**Exemplo**:
```
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    4cdc5dd7eaad   2 weeks ago     133MB
```

### **3. Diferença entre Imagens Oficiais e Personalizadas**

#### **Imagens Oficiais**
Imagens oficiais são mantidas pela comunidade ou por equipes responsáveis diretamente no Docker Hub (ex.: `nginx`, `mysql`). Elas são confiáveis e verificadas pelo Docker, geralmente identificadas com o ícone de "verificado".

#### **Imagens Personalizadas**
Imagens personalizadas são versões de imagens feitas sob medida para atender a necessidades específicas. Podem ser criadas a partir de um **Dockerfile** ou modificações locais em uma imagem padrão.

### **4. Excluindo Imagens com `docker rmi`**

Para liberar espaço no sistema, é possível remover imagens que não são mais necessárias. O comando `docker rmi <imagem>` permite remover uma imagem especificada.

```bash
docker rmi nginx
```

Caso a imagem esteja em uso por algum container, será necessário interromper o container antes de excluí-la.

> **Dica**: Use `docker rmi $(docker images -q -f "dangling=true")` para remover imagens "dangling" (ou seja, imagens intermediárias sem tag), economizando espaço.

### **5. Criando Imagens Personalizadas com Dockerfile**

Um Dockerfile permite criar uma imagem personalizada definindo todas as etapas para montar o ambiente e instalar dependências.

#### **Exemplo Básico de Dockerfile**

1. **Crie um arquivo chamado `Dockerfile`**:
   ```dockerfile
   # Usando uma imagem base
   FROM ubuntu:latest

   # Instalando pacotes necessários
   RUN apt-get update && apt-get install -y python3

   # Definindo o diretório de trabalho
   WORKDIR /app

   # Copiando arquivos para o container
   COPY . /app

   # Comando para iniciar o container
   CMD ["python3", "app.py"]
   ```

2. **Construa a Imagem**:
   ```bash
   docker build -t minha-imagem-python .
   ```

   Neste exemplo, estamos usando a imagem base `ubuntu`, instalando o Python e copiando arquivos locais para o diretório `/app` dentro do container.

3. **Listando a Nova Imagem**:
   Após a construção, verifique se a imagem está disponível:

   ```bash
   docker images
   ```

### **6. Tagging e Versionamento de Imagens com `docker tag`**

O comando `docker tag` permite atribuir tags específicas para imagens, facilitando o versionamento e o compartilhamento em registries.

```bash
docker tag minha-imagem-python minha-conta/minha-imagem-python:v1
```

Essa tag facilita o gerenciamento de versões ao identificar cada versão ou modificação realizada.

---

## **4. Criando e Gerenciando Containers**

### **Objetivo**
Demonstrar como criar, executar e gerenciar containers no Docker. Containers são a base para executar aplicações isoladamente, e dominar o gerenciamento de containers é essencial para o uso do Docker.

### **Principais Comandos para Containers**

| Comando                            | Descrição                                                              |
|------------------------------------|------------------------------------------------------------------------|
| `docker run`                       | Cria e inicia um container com base em uma imagem.                     |
| `docker ps`                        | Lista containers em execução.                                          |
| `docker ps -a`                     | Lista todos os containers (executando e parados).                      |
| `docker stop <container_id>`       | Para a execução de um container.                                       |
| `docker start <container_id>`      | Inicia um container previamente parado.                                |
| `docker restart <container_id>`    | Reinicia um container.                                                 |
| `docker rm <container_id>`         | Remove um container parado.                                            |
| `docker exec -it <container_id>`   | Acessa um container em execução no modo interativo.                    |

### **1. Criando e Executando Containers com `docker run`**

O comando `docker run` é o mais básico e importante para iniciar um container a partir de uma imagem. Ele aceita várias opções que permitem configurar o container de forma personalizada.

#### **Exemplo 1: Executar um Container NGINX em Modo Background**

```bash
docker run -d -p 8080:80 nginx
```

- **-d**: Inicia o container em modo *detached* (background).
- **-p 8080:80**: Mapeia a porta 80 do container para a porta 8080 no host, permitindo acesso à aplicação.

Após executar este comando, o servidor NGINX estará disponível no endereço `http://localhost:8080`.

#### **Exemplo 2: Executar um Container em Modo Interativo**

Para executar um container em modo interativo, permitindo acesso ao terminal do container, use a opção `-it`:

```bash
docker run -it ubuntu /bin/bash
```

- **-it**: Executa em modo interativo com acesso ao terminal do container.
- **ubuntu**: A imagem a ser usada.
- **/bin/bash**: Comando inicial a ser executado (abre um shell Bash).

Este comando abre um shell no container Ubuntu, permitindo executar comandos diretamente dentro do ambiente do container.

#### **Opções Úteis no Comando `docker run`**

| Opção            | Descrição                                                                                 |
|------------------|-------------------------------------------------------------------------------------------|
| `-d`             | Executa o container em segundo plano.                                                     |
| `-p host:container` | Mapeia uma porta do host para uma porta do container, permitindo acesso externo.        |
| `-v host_path:container_path` | Monta um volume, ligando diretórios do host com o container.                 |
| `-e VAR=value`   | Define variáveis de ambiente dentro do container.                                         |
| `--name <nome>`  | Atribui um nome ao container, facilitando o gerenciamento.                                |

---

### **2. Listando Containers com `docker ps`**

O comando `docker ps` mostra todos os containers em execução. Para listar todos os containers, inclusive os parados, use `-a`:

```bash
docker ps            # Lista apenas containers em execução
docker ps -a         # Lista todos os containers (executando e parados)
```

**Exemplo de Saída**:
```
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS                    NAMES
7d9eafacde07   nginx     "nginx -g …"  5 minutes ago   Up 5 minutes   0.0.0.0:8080->80/tcp     amazing_morse
```

### **3. Parando, Reiniciando e Removendo Containers**

#### **Parar um Container com `docker stop`**
Para interromper um container em execução, utilize o comando `docker stop` seguido do ID ou nome do container.

```bash
docker stop amazing_morse
```

#### **Reiniciar um Container com `docker restart`**
Para reiniciar um container (útil para aplicar alterações de configuração):

```bash
docker restart amazing_morse
```

#### **Remover um Container com `docker rm`**
Se o container foi interrompido e você deseja removê-lo, use o comando `docker rm`:

```bash
docker rm amazing_morse
```

> **Dica**: Para remover múltiplos containers de uma vez, use `docker rm $(docker ps -aq)`.

---

### **4. Acessando um Container em Execução com `docker exec`**

O comando `docker exec` é utilizado para executar comandos dentro de um container em execução. Ele é especialmente útil para acessar o terminal ou rodar scripts diretamente no ambiente isolado do container.

#### **Exemplo: Acessar o Terminal do Container NGINX**

```bash
docker exec -it amazing_morse /bin/bash
```

Este comando abre um shell interativo no container `amazing_morse`. Você pode então executar comandos diretamente dentro do container.

---

## **5. Introdução ao Dockerfile: Construindo Imagens Personalizadas**

### **Objetivo**
Explicar o uso do Dockerfile para criar imagens personalizadas, permitindo que você defina os pacotes, dependências e configurações iniciais de um container. Ao final, você será capaz de construir uma imagem Docker sob medida.

### **O que é um Dockerfile?**

Um Dockerfile é um arquivo de texto que contém uma série de instruções para construir uma imagem Docker. Cada instrução no Dockerfile define uma etapa para criar o ambiente de um container, como instalar pacotes, copiar arquivos ou configurar variáveis de ambiente.

### **Principais Instruções do Dockerfile**

| Instrução      | Descrição                                                                              |
|----------------|----------------------------------------------------------------------------------------|
| `FROM`         | Define a imagem base a partir da qual o container será criado.                         |
| `RUN`          | Executa comandos no container durante o processo de criação da imagem.                 |
| `COPY`         | Copia arquivos ou diretórios do host para o sistema de arquivos do container.          |
| `WORKDIR`      | Define o diretório de trabalho dentro do container, onde os comandos serão executados. |
| `CMD`          | Especifica o comando que será executado quando o container for iniciado.               |
| `EXPOSE`       | Expõe uma porta do container para comunicação com o mundo externo.                     |
| `ENV`          | Define variáveis de ambiente que estarão disponíveis dentro do container.              |

### **Criando um Dockerfile Básico**

Vamos criar um Dockerfile simples para uma aplicação Python.

1. **Crie um Diretório de Trabalho**:
   ```bash
   mkdir meu_app_docker
   cd meu_app_docker
   ```

2. **Crie o Arquivo Dockerfile**:
   No diretório `meu_app_docker`, crie um arquivo chamado `Dockerfile` com o seguinte conteúdo:

   ```dockerfile
   # Use uma imagem base do Python
   FROM python:3.9

   # Defina o diretório de trabalho
   WORKDIR /app

   # Copie o arquivo de requisitos
   COPY requirements.txt /app

   # Instale as dependências
   RUN pip install -r requirements.txt

   # Copie o código da aplicação
   COPY . /app

   # Exponha a porta em que a aplicação vai rodar
   EXPOSE 5000

   # Comando para iniciar a aplicação
   CMD ["python", "app.py"]
   ```

3. **Adicione os Arquivos Necessários**:
   No mesmo diretório, crie dois arquivos:

   - `requirements.txt`: Contém as dependências do Python, como `Flask` (exemplo):
     ```
     flask
     ```
   - `app.py`: Um simples servidor Flask:
     ```python
     from flask import Flask
     app = Flask(__name__)

     @app.route('/')
     def home():
         return "Hello, Docker!"

     if __name__ == "__main__":
         app.run(host="0.0.0.0", port=5000)
     ```

### **Construindo a Imagem com `docker build`**

Agora que temos nosso Dockerfile e arquivos da aplicação, vamos construir a imagem.

1. **Construa a Imagem**:
   No terminal, execute o comando abaixo para criar uma imagem a partir do Dockerfile:

   ```bash
   docker build -t minha-imagem-python .
   ```

   - **-t**: Atribui um nome/tag à imagem (`minha-imagem-python`).
   - **.**: Indica o diretório atual como contexto para a construção da imagem.

2. **Verifique a Imagem**:
   Após a construção, verifique se a imagem foi criada com sucesso:

   ```bash
   docker images
   ```

   Você verá a imagem `minha-imagem-python` listada entre as imagens disponíveis.

### **Executando o Container a Partir da Imagem Criada**

Agora vamos executar um container com base na nova imagem.

```bash
docker run -d -p 5000:5000 minha-imagem-python
```

- **-d**: Inicia o container em modo *detached* (background).
- **-p 5000:5000**: Mapeia a porta 5000 do container para a porta 5000 do host.

Acesse `http://localhost:5000` no navegador para ver o servidor Flask em execução, que deverá exibir a mensagem "Hello, Docker!".

---

### **Boas Práticas para Dockerfiles**

1. **Use Imagens Base Específicas**: Prefira imagens com versão específica (`python:3.9` em vez de `python:latest`) para garantir consistência.
   
2. **Reduza o Número de Instruções `RUN`**: Sempre que possível, combine instruções `RUN` para reduzir o número de camadas na imagem. Exemplo:
   ```dockerfile
   RUN apt-get update && \
       apt-get install -y pacote1 pacote2 && \
       apt-get clean
   ```

3. **Use `.dockerignore`**: Crie um arquivo `.dockerignore` para evitar copiar arquivos desnecessários para a imagem, como arquivos temporários ou de configuração do sistema.

4. **Minimize o Tamanho da Imagem**: Remova pacotes desnecessários, use imagens mínimas (como `alpine` quando possível) e limpe caches ao final das instruções `RUN`.

---

## **6. Gerenciamento de Volumes no Docker**

### **Objetivo**
Ensinar como usar volumes para persistir dados em containers. Volumes são o método recomendado para armazenar dados que precisam sobreviver ao ciclo de vida dos containers.

### **Por Que Usar Volumes?**

Containers são efêmeros por natureza; ao serem removidos, todos os dados internos também são apagados. Volumes permitem que dados sejam mantidos de forma independente do ciclo de vida do container, sendo ideais para aplicações que geram ou manipulam dados, como bancos de dados e sistemas de logs.

### **Tipos de Armazenamento no Docker**

1. **Volumes**: São armazenados em diretórios gerenciados pelo Docker no host e são independentes do sistema de arquivos do container.
2. **Bind Mounts**: Mapeiam um diretório específico do host para o container. São mais dependentes da estrutura do sistema de arquivos do host.
3. **tmpfs Mounts**: Armazenam dados na memória do sistema, sendo úteis para dados temporários.

> **Nota**: Volumes são preferidos em produção, pois são mais seguros, flexíveis e permitem que o Docker gerencie o armazenamento.

### **Principais Comandos para Gerenciamento de Volumes**

| Comando                          | Descrição                                                        |
|----------------------------------|------------------------------------------------------------------|
| `docker volume create <nome>`    | Cria um novo volume com o nome especificado.                     |
| `docker volume ls`               | Lista todos os volumes disponíveis.                              |
| `docker volume inspect <nome>`   | Exibe informações detalhadas sobre um volume.                    |
| `docker volume rm <nome>`        | Remove um volume não utilizado.                                  |

### **1. Criando e Listando Volumes**

#### **Criar um Volume**

Para criar um volume no Docker, use o comando `docker volume create`:

```bash
docker volume create meu_volume
```

Esse comando cria um volume chamado `meu_volume`, que pode ser usado em qualquer container.

#### **Listar Volumes**

Para verificar os volumes existentes, use:

```bash
docker volume ls
```

**Exemplo de Saída**:
```
DRIVER              VOLUME NAME
local               meu_volume
```

### **2. Montando Volumes em Containers**

Para montar um volume em um container, utilize a opção `-v` (ou `--mount`) no comando `docker run`. Vamos fazer isso com um exemplo:

#### **Exemplo: Usando Volumes para Armazenamento Persistente**

```bash
docker run -d -p 8080:80 -v meu_volume:/usr/share/nginx/html nginx
```

Neste exemplo:

- **-v meu_volume:/usr/share/nginx/html**: Monta o volume `meu_volume` no diretório `/usr/share/nginx/html` dentro do container. Tudo que for salvo neste diretório será mantido mesmo se o container for removido.

### **3. Bind Mounts: Ligando um Diretório do Host a um Container**

Além de volumes, você pode usar bind mounts para ligar diretórios específicos do sistema host ao container. Essa abordagem é útil em ambiente de desenvolvimento, quando é necessário acessar o código local diretamente no container.

```bash
docker run -d -p 8080:80 -v $(pwd)/site:/usr/share/nginx/html nginx
```

Neste exemplo:

- `$(pwd)/site` refere-se ao diretório `site` no host (no local atual).
- `/usr/share/nginx/html` é o diretório dentro do container.

> **Nota**: Como o bind mount depende da estrutura do host, ele pode ser menos seguro para uso em produção do que volumes.

### **4. Inspecionando Volumes**

Para ver detalhes sobre um volume específico, incluindo o caminho onde ele é armazenado no host, use:

```bash
docker volume inspect meu_volume
```

**Exemplo de Saída**:
```json
[
    {
        "CreatedAt": "2023-11-06T12:34:56Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/meu_volume/_data",
        "Name": "meu_volume",
        "Options": {},
        "Scope": "local"
    }
]
```

O campo `Mountpoint` mostra onde os dados do volume estão armazenados no host.

### **5. Excluindo Volumes**

Volumes ocupam espaço no sistema, então, é útil remover volumes não utilizados para liberar espaço.

#### **Remover um Volume Específico**

```bash
docker volume rm meu_volume
```

#### **Remover Volumes "Dangling" (Não Utilizados)**

Para excluir volumes que não estão em uso por nenhum container:

```bash
docker volume prune
```

Isso remove todos os volumes não utilizados, ajudando a manter o sistema limpo.

---

## **7. Rede e Comunicação entre Containers**

### **Objetivo**
Demonstrar como criar e gerenciar redes para containers Docker, possibilitando que eles se comuniquem de forma isolada e segura. As redes permitem interconectar containers de uma mesma aplicação e garantir que não interfiram em outras redes do sistema.

### **Conceitos de Redes no Docker**

O Docker oferece vários tipos de redes, cada uma com características específicas:

1. **Bridge (Ponte)**: Rede padrão usada para containers no modo standalone. Ideal para containers que precisam se comunicar apenas dentro do mesmo host.
   
2. **Host**: Permite que o container compartilhe a mesma interface de rede do host. É útil quando você deseja máxima performance de rede ou precisa de integração direta com o host.
   
3. **None**: Não configura nenhuma rede para o container, deixando-o isolado sem interface de rede. Útil para segurança em processos que não requerem comunicação externa.

4. **Overlay**: Usado em configurações de orquestração (como Docker Swarm ou Kubernetes) para conectar containers em diferentes hosts. Não será abordado neste tutorial básico, pois requer configuração de cluster.

### **Principais Comandos para Gerenciamento de Redes**

| Comando                             | Descrição                                                                  |
|-------------------------------------|----------------------------------------------------------------------------|
| `docker network create <nome>`      | Cria uma nova rede Docker.                                                 |
| `docker network ls`                 | Lista todas as redes disponíveis.                                          |
| `docker network inspect <nome>`     | Exibe detalhes de uma rede específica.                                     |
| `docker network connect <nome>`     | Conecta um container a uma rede existente.                                 |
| `docker network disconnect <nome>`  | Desconecta um container de uma rede.                                       |
| `docker network rm <nome>`          | Remove uma rede que não esteja sendo usada por containers ativos.          |

### **1. Listando Redes Existentes**

Por padrão, o Docker cria algumas redes básicas. Use `docker network ls` para visualizá-las:

```bash
docker network ls
```

**Exemplo de Saída**:
```
NETWORK ID     NAME      DRIVER    SCOPE
b12345678901   bridge    bridge    local
c23456789012   host      host      local
d34567890123   none      null      local
```

- **bridge**: Rede padrão de ponte para containers isolados.
- **host**: Rede que compartilha a interface de rede do host.
- **none**: Rede sem interface, isolando completamente o container.

### **2. Criando uma Rede Personalizada**

Para facilitar a comunicação entre containers, crie uma rede personalizada. Isso é útil para separar grupos de containers e organizar aplicações em microserviços.

```bash
docker network create minha_rede
```

Após a criação, a rede `minha_rede` estará disponível para conectar containers.

### **3. Conectando Containers a uma Rede**

Ao iniciar um container, você pode conectá-lo diretamente a uma rede específica usando o parâmetro `--network`. Vamos ver um exemplo de como conectar dois containers (um servidor web e um banco de dados) para que eles possam se comunicar.

#### **Exemplo: Criando um Container MySQL e um Container NGINX**

1. **Inicie o Container do MySQL na Rede Personalizada**:
   ```bash
   docker run -d --name mysql_container --network minha_rede -e MYSQL_ROOT_PASSWORD=senha mysql:5.7
   ```

   Neste exemplo:
   - **--network minha_rede**: Conecta o container à rede `minha_rede`.
   - **MYSQL_ROOT_PASSWORD**: Define a senha do usuário `root` no MySQL.

2. **Inicie o Container NGINX na Mesma Rede**:
   ```bash
   docker run -d --name nginx_container --network minha_rede -p 8080:80 nginx
   ```

   Neste caso, os containers `nginx_container` e `mysql_container` estão na mesma rede (`minha_rede`) e, portanto, podem se comunicar usando seus nomes de container como endereço de rede.

### **4. Comunicação entre Containers na Mesma Rede**

Quando containers estão na mesma rede, eles podem se comunicar usando os nomes dos containers como hostname. No exemplo acima:

- O container NGINX pode acessar o MySQL através de `mysql_container:3306` (3306 é a porta padrão do MySQL).
- O nome `mysql_container` será resolvido automaticamente na rede Docker.

Para testar a conexão entre containers, você pode acessar o container NGINX e tentar um comando de conexão:

```bash
docker exec -it nginx_container ping mysql_container
```

Este comando verifica se o `nginx_container` consegue se comunicar com o `mysql_container` pela rede `minha_rede`.

### **5. Inspecionando uma Rede**

Para obter detalhes sobre uma rede, incluindo quais containers estão conectados, use:

```bash
docker network inspect minha_rede
```

Isso exibe informações como ID da rede, drivers, containers conectados e configurações de IP.

### **6. Desconectando e Removendo Redes**

- **Desconectar um Container de uma Rede**:
   ```bash
   docker network disconnect minha_rede nginx_container
   ```

- **Remover uma Rede**:
   ```bash
   docker network rm minha_rede
   ```

   > **Nota**: Só é possível remover uma rede que não esteja sendo utilizada por nenhum container ativo.

---

## **8. Docker Compose: Orquestrando Múltiplos Containers**

### **Objetivo**
Apresentar o Docker Compose para gerenciar e orquestrar aplicações multi-containers. Com o Docker Compose, você poderá configurar a aplicação, definir redes e volumes, e gerenciar múltiplos containers de forma simplificada.

### **Instalando o Docker Compose**

1. **Verificar se o Docker Compose já está instalado**:
   ```bash
   docker-compose --version
   ```
   Caso o Docker Compose esteja instalado, este comando exibirá a versão atual.

2. **Instalação no Linux**:
   Se o Docker Compose não estiver instalado, baixe-o com o comando:

   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep -oP '"tag_name": "\K(.*)(?=")')/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

3. **Conceder Permissão de Execução**:
   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

4. **Verificar a Instalação**:
   ```bash
   docker-compose --version
   ```

---

### **Estrutura do Arquivo `docker-compose.yml`**

O Docker Compose usa um arquivo `docker-compose.yml` para definir serviços, redes e volumes necessários para a aplicação. Vamos examinar a estrutura básica desse arquivo e como ele é usado para criar e conectar containers.

### **Exemplo de Arquivo `docker-compose.yml`**

Suponha que queremos configurar uma aplicação com dois serviços: um servidor web NGINX e um banco de dados MySQL. O arquivo `docker-compose.yml` terá a seguinte estrutura:

```yaml
version: '3.8'

services:
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: senha
      MYSQL_DATABASE: exemplo_db
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - app_network

  web:
    image: nginx
    ports:
      - "8080:80"
    depends_on:
      - db
    networks:
      - app_network

volumes:
  db_data:

networks:
  app_network:
```

### **Explicação do Arquivo**

1. **version**: Define a versão do Compose YAML. A versão `3.8` é comum para funcionalidades avançadas.

2. **services**:
   - **db**: Define o serviço do banco de dados MySQL.
     - **image**: Especifica a imagem Docker (neste caso, `mysql:5.7`).
     - **environment**: Variáveis de ambiente para configuração do MySQL (senha e nome do banco).
     - **volumes**: Monta o volume `db_data` para persistência de dados no diretório do MySQL (`/var/lib/mysql`).
     - **networks**: Conecta o serviço `db` à rede `app_network`.

   - **web**: Define o serviço NGINX para servir conteúdo web.
     - **ports**: Mapeia a porta 8080 no host para a porta 80 no container.
     - **depends_on**: Garante que o serviço `db` seja iniciado antes de `web`.
     - **networks**: Conecta o serviço `web` à rede `app_network`.

3. **volumes**:
   - **db_data**: Define um volume Docker para persistência de dados do banco.

4. **networks**:
   - **app_network**: Define uma rede para permitir comunicação entre os serviços `web` e `db`.

---

### **Comandos Básicos do Docker Compose**

| Comando                          | Descrição                                                        |
|----------------------------------|------------------------------------------------------------------|
| `docker-compose up`              | Cria e inicia todos os serviços definidos no `docker-compose.yml`. |
| `docker-compose down`            | Interrompe e remove os containers, redes e volumes criados pelo Compose. |
| `docker-compose ps`              | Lista os containers que estão sendo gerenciados pelo Compose.    |
| `docker-compose logs`            | Exibe logs dos serviços em execução.                             |
| `docker-compose exec <service>`  | Executa comandos em um container gerenciado pelo Compose.        |

### **1. Iniciando a Aplicação com `docker-compose up`**

Execute o comando abaixo no diretório onde está o arquivo `docker-compose.yml`:

```bash
docker-compose up -d
```

- **-d**: Executa em modo *detached* (background).

Este comando inicia os serviços `db` e `web` e cria a rede e volume definidos no arquivo Compose. Após a execução, você pode acessar o servidor NGINX em `http://localhost:8080`.

### **2. Visualizando os Containers em Execução**

Para listar os containers em execução pelo Compose, use:

```bash
docker-compose ps
```

**Exemplo de Saída**:
```
   Name                  Command               State           Ports         
---------------------------------------------------------------------------
myapp_db_1    docker-entrypoint.sh mysqld      Up      3306/tcp            
myapp_web_1   nginx -g 'daemon off;'           Up      0.0.0.0:8080->80/tcp 
```

### **3. Exibindo Logs dos Containers**

Para ver os logs dos containers em execução (útil para depuração), use:

```bash
docker-compose logs -f
```

### **4. Executando Comandos em Containers com `docker-compose exec`**

Para acessar o terminal de um container, por exemplo, o container do MySQL, use:

```bash
docker-compose exec db /bin/bash
```

Esse comando abre um terminal dentro do container `db`, permitindo que você execute comandos diretamente no ambiente do banco de dados.

### **5. Parando e Removendo Containers com `docker-compose down`**

Para encerrar a aplicação e remover os containers, redes e volumes criados pelo Docker Compose:

```bash
docker-compose down
```

> **Nota**: Para manter os volumes, use `docker-compose down --volumes` apenas quando precisar excluir os dados.

---

## **9. Trabalhando com Registries e Docker Hub**

### **Objetivo**
Aprender a usar o Docker Hub e registries privados para armazenar e compartilhar imagens Docker, com práticas de autenticação e segurança.

### **O que é um Registry?**

Um **registry** é um serviço para armazenar e distribuir imagens Docker. Existem registries públicos, como o **Docker Hub**, e registries privados, que são úteis para ambientes corporativos onde o controle de acesso é necessário.

### **Docker Hub**

O Docker Hub é o registry público padrão, que permite armazenar, compartilhar e acessar imagens de forma aberta ou privada. Com o Docker Hub, você pode:

- Armazenar suas imagens em repositórios públicos ou privados.
- Controlar versões de imagens.
- Compartilhar imagens com outras pessoas ou equipes.

### **Principais Comandos para Gerenciamento de Imagens em Registries**

| Comando                        | Descrição                                                           |
|--------------------------------|---------------------------------------------------------------------|
| `docker login`                 | Faz login no Docker Hub ou em outro registry especificado.          |
| `docker logout`                | Faz logout do Docker Hub ou de um registry.                         |
| `docker pull <imagem>`         | Baixa uma imagem de um registry (público ou privado).               |
| `docker push <imagem>`         | Envia uma imagem para um registry (Docker Hub ou privado).          |
| `docker tag <imagem> <novo_nome>` | Marca uma imagem para organização e envio ao registry.        |

### **1. Configurando o Docker Hub**

#### **Passo 1: Criar uma Conta no Docker Hub**

Se você ainda não possui uma conta, acesse [Docker Hub](https://hub.docker.com/) e crie uma conta. É gratuita e fornece acesso a repositórios públicos e uma opção limitada para repositórios privados.

#### **Passo 2: Fazer Login no Docker Hub**

No terminal, faça login no Docker Hub:

```bash
docker login
```

Você será solicitado a inserir seu **username** e **password**. Após o login, o Docker pode acessar e enviar imagens diretamente para sua conta do Docker Hub.

### **2. Publicando Imagens no Docker Hub**

Para compartilhar uma imagem, você precisa enviá-la ao Docker Hub. Vamos usar um exemplo prático para fazer isso.

#### **Exemplo: Enviando uma Imagem para o Docker Hub**

1. **Nomear a Imagem com seu Nome de Usuário**
   Antes de enviar, renomeie a imagem no formato `<username>/<nome_imagem>:<tag>`. Use `docker tag` para isso:

   ```bash
   docker tag minha-imagem:latest seu_usuario_dockerhub/minha-imagem:latest
   ```

2. **Enviar a Imagem com `docker push`**
   Agora você pode enviar a imagem para o Docker Hub:

   ```bash
   docker push seu_usuario_dockerhub/minha-imagem:latest
   ```

   Após o upload, a imagem estará disponível no Docker Hub em `https://hub.docker.com/r/seu_usuario_dockerhub/minha-imagem`.

### **3. Baixando Imagens de Registries Públicos e Privados**

Para baixar uma imagem, use o comando `docker pull` com o nome da imagem e a tag, caso necessário.

```bash
docker pull nginx:latest
```

Se a imagem for de um registry privado, você precisará fazer login nesse registry primeiro com `docker login <url_do_registry>`.

### **4. Criando e Configurando um Registry Privado**

Se sua equipe ou empresa preferir não usar o Docker Hub, é possível configurar um registry privado localmente ou em um servidor próprio. O Docker fornece uma imagem oficial para isso.

#### **Passo 1: Iniciar um Registry Privado com Docker**

Execute o seguinte comando para iniciar um registry local:

```bash
docker run -d -p 5000:5000 --name meu_registry registry:2
```

- **registry:2** é a imagem oficial do Docker para configurar registries privados.
- A porta 5000 é o padrão, mas pode ser alterada se necessário.

#### **Passo 2: Enviar Imagens para o Registry Privado**

1. **Marcar a Imagem para o Registry Local**
   Renomeie a imagem no formato `<host>:<porta>/<nome_imagem>`:

   ```bash
   docker tag minha-imagem localhost:5000/minha-imagem
   ```

2. **Enviar a Imagem para o Registry Privado**
   Use o comando `docker push` para enviar a imagem:

   ```bash
   docker push localhost:5000/minha-imagem
   ```

#### **Passo 3: Baixar Imagens do Registry Privado**

Para baixar a imagem de volta do registry privado, use `docker pull`:

```bash
docker pull localhost:5000/minha-imagem
```

> **Nota**: Em ambientes de produção, o registry privado deve ser configurado com HTTPS para garantir a segurança dos dados transmitidos. Também é recomendável usar autenticação para controlar o acesso.

### **5. Práticas de Segurança para Imagens e Registries**

1. **Usar HTTPS**: Sempre configure o registry privado com HTTPS para proteger as imagens e os dados de configuração.
   
2. **Autenticação e Controle de Acesso**: Em registries privados, configure autenticação para limitar o acesso a usuários autorizados.

3. **Manter Imagens Atualizadas**: Periodicamente, verifique e atualize imagens para aplicar correções de segurança.

4. **Escanear Imagens**: O Docker Hub e outros registries privados suportam escaneamento de imagens para vulnerabilidades de segurança. Utilize essa funcionalidade para garantir que suas imagens estejam seguras.

---

## **10. Otimização e Melhores Práticas com Containers**

### **Objetivo**
Explorar práticas recomendadas para otimização e segurança ao trabalhar com Docker. Isso inclui maneiras de reduzir o tamanho das imagens, controlar o uso de recursos, e dicas para tornar o ambiente de containers mais seguro.

### **1. Otimização do Tamanho das Imagens Docker**

Imagens menores são mais rápidas de transferir, menos custosas para armazenar e mais eficientes em termos de uso de recursos. Algumas práticas recomendadas incluem:

- **Escolher Imagens Base Leves**: Sempre que possível, use imagens mínimas como `alpine`, que têm um tamanho reduzido (por exemplo, `python:alpine` em vez de `python:latest`).
  
- **Combinar Instruções `RUN`**: Para reduzir a quantidade de camadas e o tamanho final da imagem, combine múltiplos comandos `RUN` em uma única instrução. Por exemplo:
  
  ```dockerfile
  RUN apt-get update && \
      apt-get install -y pacote1 pacote2 && \
      rm -rf /var/lib/apt/lists/*
  ```

- **Remover Arquivos Desnecessários**: Limpe caches e remova pacotes desnecessários após a instalação, como no exemplo acima. Isso garante que não restem dados que ocupem espaço sem necessidade.

- **Utilizar Multistage Builds**: Divida a construção de imagens em estágios quando precisar compilar código ou instalar dependências grandes. A imagem final incluirá apenas os arquivos e pacotes essenciais para a execução.

  **Exemplo de Multistage Build**:
  
  ```dockerfile
  FROM golang:1.18 as builder
  WORKDIR /app
  COPY . .
  RUN go build -o myapp

  FROM alpine
  COPY --from=builder /app/myapp /myapp
  CMD ["/myapp"]
  ```

  Neste exemplo, o código é compilado em uma imagem `golang`, mas a imagem final usa `alpine`, resultando em um tamanho muito menor.

### **2. Limitação de Recursos (CPU e Memória)**

Docker permite controlar o uso de recursos de CPU e memória, evitando que um container consuma recursos excessivos do sistema, o que é especialmente útil em ambientes multi-tenant.

- **Limitar CPU**: Para definir um limite de CPU, use `--cpus`. Por exemplo, para limitar um container a usar no máximo 1.5 CPU:
  
  ```bash
  docker run --cpus="1.5" minha-imagem
  ```

- **Limitar Memória**: Para limitar a memória disponível para um container, use `--memory`. Por exemplo, para limitar a 512 MB:
  
  ```bash
  docker run --memory="512m" minha-imagem
  ```

Essas restrições ajudam a evitar que um container monopolize recursos, garantindo uma performance equilibrada entre múltiplos containers.

### **3. Dicas de Segurança para Containers Docker**

A segurança é um aspecto fundamental no uso de containers, especialmente quando eles estão em produção. Aqui estão algumas práticas para aumentar a segurança dos containers:

- **Use Imagens Oficiais e Verificadas**: Sempre que possível, utilize imagens oficiais ou de fontes confiáveis, que passam por validações e atualizações de segurança constantes.
  
- **Escanear Imagens para Vulnerabilidades**: Muitas ferramentas, como o Docker Hub e o Snyk, oferecem escaneamento de vulnerabilidades. Use esses escaneamentos regularmente para identificar e corrigir problemas de segurança em suas imagens.

- **Evitar Rodar Containers com Privilégios Elevados**: Evite usar o `--privileged` e, sempre que possível, configure o usuário com menos permissões dentro do container usando a instrução `USER` no Dockerfile:

  ```dockerfile
  RUN adduser -D appuser
  USER appuser
  ```

- **Configurar Variáveis de Ambiente Sensíveis com Cuidado**: Evite armazenar informações sensíveis diretamente no Dockerfile. Prefira armazenar variáveis sensíveis em arquivos seguros ou definir variáveis de ambiente no momento da execução:

  ```bash
  docker run -e DB_PASSWORD=$(cat /secrets/db_password) minha-imagem
  ```

- **Isolar Containers com Redes Customizadas**: Evite usar a rede `host`, a menos que seja absolutamente necessário, e crie redes Docker customizadas para isolar os containers.

### **4. Limpeza de Imagens e Containers Não Utilizados**

Com o tempo, o sistema acumula containers, imagens, volumes e redes que não são mais necessários. Docker oferece comandos para limpar automaticamente esses recursos, liberando espaço e mantendo o ambiente organizado:

- **Remover Containers Parados**:
  
  ```bash
  docker container prune
  ```

- **Remover Imagens Sem Tag (Dangling)**:
  
  ```bash
  docker image prune
  ```

- **Remover Volumes Não Utilizados**:
  
  ```bash
  docker volume prune
  ```

- **Remover Todos os Recursos Não Utilizados**:
  
  ```bash
  docker system prune
  ```

Esses comandos ajudam a evitar o desperdício de recursos e manter o ambiente Docker limpo.

### **5. Monitoramento e Logging de Containers**

Para garantir que os containers estão funcionando corretamente, é importante monitorar o uso de recursos e capturar logs.

- **Monitorar Uso de Recursos com `docker stats`**:
  
  ```bash
  docker stats
  ```

  Esse comando exibe o uso de CPU, memória, e rede dos containers em tempo real, o que é útil para detectar problemas de performance.

- **Capturar Logs de Containers**:
  
  ```bash
  docker logs <container_id>
  ```

  Esse comando exibe os logs do container especificado, permitindo identificar erros e verificar a saída da aplicação.

> **Dica Avançada**: Ferramentas como Prometheus e Grafana podem ser integradas ao Docker para monitoramento avançado de métricas e logs.

---

## **11. Monitoramento e Log de Containers**

### **Objetivo**
Ensinar como monitorar o uso de recursos dos containers e acessar seus logs. Além disso, abordaremos a integração com ferramentas externas, como Prometheus e Grafana, para monitoramento avançado.

### **1. Monitorando Containers com Docker**

Docker oferece alguns comandos nativos para monitorar o desempenho e a atividade dos containers.

#### **Verificar o Status dos Containers com `docker ps`**

O comando `docker ps` exibe o status dos containers em execução, incluindo o nome, ID, uptime e portas expostas. Para ver todos os containers, inclusive os parados, use `-a`.

```bash
docker ps
```

#### **Monitorar o Uso de Recursos com `docker stats`**

O comando `docker stats` exibe informações em tempo real sobre o uso de CPU, memória, I/O de rede e I/O de disco para cada container em execução.

```bash
docker stats
```

**Exemplo de Saída**:
```
CONTAINER ID   NAME           CPU %   MEM USAGE / LIMIT   MEM %   NET I/O     BLOCK I/O   PIDS
1a2b3c4d5e6f   web_app        2.13%   50.18MiB / 500MiB   10.04%   800kB / 1MB   0B / 0B    8
```

Isso permite identificar containers que consomem muitos recursos e ajuda a detectar gargalos.

### **2. Capturando Logs de Containers com Docker**

Os logs dos containers são fundamentais para entender o comportamento da aplicação e diagnosticar erros.

#### **Visualizar Logs com `docker logs`**

Para visualizar os logs de um container específico, use:

```bash
docker logs <container_id>
```

- **-f**: Segue os logs em tempo real, similar ao `tail -f`.
- **--tail**: Exibe apenas as últimas linhas dos logs.

```bash
docker logs -f --tail 100 <container_id>
```

Este comando é útil para acompanhar a saída da aplicação em tempo real e verificar qualquer erro que ocorra durante a execução.

### **3. Monitoramento Avançado com Ferramentas Externas**

Em ambientes de produção, é recomendável usar ferramentas de monitoramento dedicadas para acompanhar métricas e logs de containers. Vamos ver como integrar o Docker com duas ferramentas populares: **Prometheus** e **Grafana**.

#### **Configuração do Prometheus para Monitoramento de Containers**

1. **Iniciar o Container do Prometheus**

   Crie um arquivo de configuração `prometheus.yml` para o Prometheus e inclua o seguinte conteúdo básico:

   ```yaml
   global:
     scrape_interval: 5s

   scrape_configs:
     - job_name: 'docker'
       static_configs:
         - targets: ['host.docker.internal:9323']
   ```

   Esse arquivo define um job para monitorar o Docker a cada 5 segundos.

2. **Executar o Exporter do Docker para Prometheus**

   O Docker não expõe diretamente suas métricas para o Prometheus. É necessário usar o **Docker Exporter** (como o `prom/node-exporter` ou `prom/dockerd-exporter`):

   ```bash
   docker run -d -p 9323:9323 --name node-exporter prom/node-exporter
   ```

3. **Iniciar o Container do Prometheus**

   Execute o Prometheus apontando para o arquivo de configuração criado:

   ```bash
   docker run -d -p 9090:9090 -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml --name prometheus prom/prometheus
   ```

   Após a configuração, você pode acessar o Prometheus em `http://localhost:9090` para verificar as métricas dos containers.

#### **Integração com Grafana para Visualização de Métricas**

1. **Iniciar o Container do Grafana**

   Grafana é uma plataforma de visualização de dados que se integra facilmente com Prometheus. Para iniciar o Grafana, execute:

   ```bash
   docker run -d -p 3000:3000 --name grafana grafana/grafana
   ```

2. **Configurar o Grafana para Usar o Prometheus como Fonte de Dados**

   - Acesse o Grafana em `http://localhost:3000` (usuário padrão: `admin`, senha: `admin`).
   - Adicione o Prometheus como fonte de dados (`Settings` -> `Data Sources` -> `Add data source`).
   - Use `http://host.docker.internal:9090` como a URL do Prometheus.

3. **Criar Dashboards para Monitorar Containers**

   Após configurar a fonte de dados, você pode importar dashboards prontos para monitorar Docker. O Grafana tem templates prontos para visualizar o uso de CPU, memória, disco e rede dos containers.

### **4. Ferramentas para Logging Centralizado**

Além das métricas, centralizar e organizar logs é fundamental para análise e troubleshooting. Ferramentas populares para gerenciamento de logs incluem **ELK Stack (Elasticsearch, Logstash, e Kibana)** e **Graylog**.

#### **Configuração Básica com ELK Stack**

1. **Instalar e Configurar o Logstash**

   O Logstash coleta e processa logs dos containers. Configure-o para receber logs do Docker e enviá-los para o Elasticsearch.

2. **Armazenamento de Logs no Elasticsearch**

   O Elasticsearch armazena e indexa logs, permitindo consultas rápidas.

3. **Visualização de Logs com Kibana**

   Kibana permite visualizar e explorar logs. Com a integração com o Elasticsearch, você pode criar gráficos e filtros para analisar logs dos containers em tempo real.

> **Dica**: Para facilitar, você pode rodar o ELK Stack com Docker Compose, que permite iniciar todos os serviços com um único comando.

---

## **12. Salvando e Carregando Imagens e Containers**

### **Objetivo**
Demonstrar como exportar e importar imagens e containers para backup, migração e compartilhamento de ambientes. Esses métodos ajudam a criar snapshots e mover aplicativos Docker entre máquinas ou ambientes.

### **1. Salvando e Carregando Imagens**

As imagens Docker podem ser salvas em um arquivo para backup ou transferência, e posteriormente carregadas em qualquer ambiente Docker compatível.

#### **Salvando uma Imagem com `docker save`**

O comando `docker save` permite exportar uma imagem para um arquivo `.tar`. Isso é útil para compartilhar a imagem sem usar um registry ou para backup.

```bash
docker save -o minha_imagem_backup.tar minha-imagem:latest
```

- **-o**: Especifica o arquivo de saída (neste caso, `minha_imagem_backup.tar`).
- **minha-imagem:latest**: Nome e tag da imagem a ser exportada.

Esse comando cria o arquivo `minha_imagem_backup.tar`, que contém todos os dados da imagem. Esse arquivo pode ser movido para outra máquina ou mantido como um backup.

#### **Carregando uma Imagem com `docker load`**

Para restaurar ou carregar uma imagem salva com `docker save`, use o comando `docker load`:

```bash
docker load -i minha_imagem_backup.tar
```

- **-i**: Especifica o arquivo de entrada (neste caso, `minha_imagem_backup.tar`).

Após o comando, a imagem estará disponível localmente e poderá ser listada com `docker images`.

### **2. Exportando e Importando Containers**

Além de imagens, você pode exportar o sistema de arquivos de um container em execução para compartilhá-lo com outra máquina ou usá-lo como base para novos containers.

#### **Exportando um Container com `docker export`**

O comando `docker export` cria um arquivo `.tar` com o sistema de arquivos completo de um container específico, mas sem as camadas de imagem e histórico de construção.

```bash
docker export -o meu_container_backup.tar <container_id>
```

- **-o**: Define o arquivo de saída (`meu_container_backup.tar`).
- **<container_id>**: ID ou nome do container que será exportado.

Esse arquivo contém o sistema de arquivos do container, mas não inclui informações sobre a imagem ou o histórico.

#### **Importando um Container com `docker import`**

Para carregar o sistema de arquivos exportado em uma nova imagem, use o comando `docker import`:

```bash
docker import meu_container_backup.tar minha-nova-imagem
```

Esse comando cria uma nova imagem chamada `minha-nova-imagem` a partir do arquivo `meu_container_backup.tar`. Essa imagem pode ser usada para iniciar novos containers com `docker run`.

### **3. Exemplo Completo: Migrando um Container Entre Máquinas**

Suponha que você queira mover um container específico de uma máquina para outra. Abaixo está o processo passo a passo:

1. **Exportar o Container na Máquina de Origem**:
   ```bash
   docker export -o meu_container_backup.tar <container_id>
   ```

2. **Transferir o Arquivo para a Máquina de Destino**:
   Você pode transferir o arquivo usando `scp`, `rsync`, ou até mesmo por armazenamento externo.

3. **Importar o Container na Máquina de Destino**:
   Na máquina de destino, importe o arquivo como uma nova imagem:
   ```bash
   docker import meu_container_backup.tar nova-imagem
   ```

4. **Iniciar um Novo Container a Partir da Imagem**:
   Agora que a imagem foi carregada, inicie um container usando-a:
   ```bash
   docker run -d --name novo_container nova-imagem
   ```

Este processo cria um container idêntico ao original, permitindo a continuidade do serviço em outra máquina.

### **4. Backups de Volumes Docker**

Além de imagens e containers, os volumes Docker que armazenam dados persistentes também podem ser exportados e importados. 

#### **Backup de um Volume com `tar`**

Suponha que temos um volume chamado `meu_volume`. Podemos fazer um backup de seu conteúdo com `tar`:

```bash
docker run --rm -v meu_volume:/volume -v $(pwd):/backup alpine tar -czf /backup/meu_volume_backup.tar.gz -C /volume .
```

Esse comando cria um arquivo `.tar.gz` do volume `meu_volume`, armazenado no diretório atual.

#### **Restaurar um Volume de Backup**

Para restaurar o volume a partir do backup, use:

```bash
docker run --rm -v meu_volume:/volume -v $(pwd):/backup alpine tar -xzf /backup/meu_volume_backup.tar.gz -C /volume
```

Esse comando extrai o conteúdo do arquivo de backup para o volume, restaurando os dados.

### **5. Salvando e Carregando Todos os Recursos com `docker-compose`**

Se você trabalha com várias imagens e containers, como em um ambiente Docker Compose, o processo de exportação e importação pode ser facilitado com scripts e comandos de backup para todos os recursos.

**Exemplo de Script Básico para Exportação**:

Este script faz backup das imagens e dos volumes de uma configuração Docker Compose:

```bash
# Salva todas as imagens
docker-compose images | awk '{print $2}' | tail -n +2 | while read image; do
    docker save -o "${image}.tar" "${image}"
done

# Salva os volumes
docker volume ls | awk '{print $2}' | tail -n +2 | while read volume; do
    docker run --rm -v ${volume}:/volume -v $(pwd):/backup alpine tar -czf "/backup/${volume}.tar.gz" -C /volume .
done
```

---

## **13. Troubleshooting e Solução de Problemas Comuns no Docker**

### **Objetivo**
Identificar e resolver problemas comuns ao trabalhar com Docker. Aprender a diagnosticar problemas de rede, permissões e execução de containers é essencial para manter o ambiente Docker em bom funcionamento.

### **1. Ferramentas para Diagnóstico e Solução de Problemas**

Docker fornece alguns comandos e opções úteis para inspecionar containers e identificar a causa de problemas.

| Comando                          | Descrição                                                                 |
|----------------------------------|---------------------------------------------------------------------------|
| `docker inspect <container>`      | Exibe detalhes completos do container, incluindo rede e variáveis de ambiente. |
| `docker logs <container>`         | Exibe os logs de execução do container.                                  |
| `docker exec -it <container> /bin/bash` | Permite acessar o terminal do container para investigação.            |
| `docker events`                  | Monitora eventos em tempo real do Docker, útil para ver logs do daemon. |

### **2. Verificando Problemas de Rede**

#### **Problema Comum: Containers não Conseguem se Comunicar**

Quando containers em uma mesma rede não conseguem se comunicar, verifique:

- **Conexão na Mesma Rede**: Confirme que os containers estão conectados à mesma rede. Use o comando:

  ```bash
  docker network inspect <nome_da_rede>
  ```

- **Firewall ou Políticas de Segurança**: Verifique se há regras de firewall bloqueando a comunicação entre containers. Isso pode acontecer em redes bridge customizadas.

- **Testando a Conexão Entre Containers**: Use o comando `ping` entre containers para testar a conectividade.

  ```bash
  docker exec -it <container1> ping <container2>
  ```

Se a comunicação não for possível, recrie a rede e reconecte os containers.

#### **Problema Comum: Conexão Externa ao Container**

Se você não consegue acessar o container externamente (por exemplo, o NGINX rodando em `localhost:8080`), verifique:

- **Portas Mapeadas Corretamente**: Confirme se as portas foram mapeadas corretamente usando `docker ps`. Por exemplo, para mapear a porta 80 do container para a porta 8080 do host:

  ```bash
  docker run -d -p 8080:80 nginx
  ```

- **Firewall do Host**: Certifique-se de que o firewall do sistema não está bloqueando a porta. No Linux, verifique com `sudo ufw status`.

### **3. Problemas de Permissão**

#### **Erro Comum: `Permission Denied` ao Executar Docker Sem `sudo`**

Se você não consegue executar o Docker sem `sudo`, provavelmente o usuário não está no grupo Docker. Para resolver:

1. Adicione o usuário ao grupo Docker:

   ```bash
   sudo usermod -aG docker $USER
   ```

2. Reinicie a sessão para aplicar as alterações:

   ```bash
   newgrp docker
   ```

#### **Erro Comum: `Permission Denied` ao Acessar Arquivos em Containers**

Isso geralmente ocorre ao usar bind mounts com permissões diferentes entre o host e o container. Tente:

- **Definir Permissões Corretas no Host**: Altere as permissões do diretório no host para o usuário que o container usa:

  ```bash
  sudo chown -R 1000:1000 /caminho/para/diretorio
  ```

- **Executar o Container com Permissões Adequadas**: Use a opção `--user` no `docker run` para especificar o usuário.

### **4. Problemas de Execução de Containers**

#### **Erro Comum: `Container Exited with Code 1`**

Esse erro indica que o container parou devido a um erro na aplicação ou na configuração. Para resolver:

- **Verifique os Logs do Container**: O comando `docker logs <container>` fornece informações sobre o que causou a saída.

- **Rever o Comando de Inicialização**: Verifique o comando `CMD` ou `ENTRYPOINT` no Dockerfile ou Compose e certifique-se de que está correto.

#### **Erro Comum: Container Reiniciando Constantemente**

Esse problema pode ocorrer se o container estiver configurado para reiniciar automaticamente, mas o comando principal falha repetidamente. Para investigar:

1. **Verifique os Logs do Container**:
   ```bash
   docker logs <container>
   ```

2. **Desative o Restart Automático Temporariamente**:
   ```bash
   docker update --restart=no <container>
   ```

3. **Inicie Manualmente para Debug**: Execute o container em modo interativo para verificar a execução do comando principal.

   ```bash
   docker run -it <imagem> /bin/bash
   ```

### **5. Problemas ao Construir Imagens**

#### **Erro Comum: `No Space Left on Device`**

Esse erro ocorre quando não há espaço suficiente para o Docker criar novas camadas de imagem. Para resolver:

- **Limpar Containers, Imagens e Volumes Não Utilizados**:

  ```bash
  docker system prune -a
  ```

  Esse comando remove todas as imagens, containers, volumes e redes não utilizados, liberando espaço.

#### **Erro Comum: `Cannot Find Package` ao Instalar Dependências**

Esse erro é comum ao construir imagens com pacotes específicos que requerem atualização. Verifique:

- **Atualização de Pacotes no Dockerfile**:

  ```dockerfile
  RUN apt-get update && apt-get install -y <pacote>
  ```

Certifique-se de incluir `apt-get update` para garantir que a lista de pacotes está atualizada antes de instalar.

### **6. Ferramentas para Solução de Problemas Avançados**

Além dos comandos nativos, algumas ferramentas ajudam a solucionar problemas complexos:

- **Dive**: Analisa camadas de uma imagem Docker, permitindo identificar grandes arquivos e camadas desnecessárias.
  
  ```bash
  dive <imagem>
  ```

- **ctop**: Exibe métricas de uso de recursos em containers, similar ao comando `top` do Linux, mas focado em containers.

- **Sysdig**: Ferramenta de análise e monitoramento que permite inspecionar containers, detectar falhas e verificar logs em detalhes.

---

## **14. Casos de Uso Avançados e Integração com CI/CD**

### **Objetivo**
Apresentar práticas avançadas para o uso de Docker em pipelines de CI/CD. Isso inclui a automação de builds, testes e deploys de containers, integrando Docker com plataformas de CI/CD como GitLab CI, Jenkins e GitHub Actions.

### **1. Docker em Pipelines de CI/CD**

O uso do Docker em CI/CD oferece diversas vantagens, como isolamento de ambiente e consistência entre diferentes etapas do pipeline. Em geral, um pipeline com Docker tem as seguintes etapas:

1. **Build**: Compila a aplicação e cria a imagem Docker.
2. **Test**: Executa testes dentro de containers.
3. **Push**: Envia a imagem para um registry (Docker Hub ou privado).
4. **Deploy**: Implanta a imagem em um ambiente de produção ou staging.

### **2. Configuração Básica de Docker no GitLab CI**

O GitLab CI é uma das ferramentas de CI/CD que possui integração nativa com Docker. Abaixo está um exemplo de configuração de pipeline CI/CD para uma aplicação que usa Docker no GitLab.

#### **Criando o Arquivo `.gitlab-ci.yml`**

Esse arquivo define o pipeline e as etapas do CI/CD no GitLab.

```yaml
stages:
  - build
  - test
  - push

# Variáveis (substitua "meu_usuario" e "minha_imagem" conforme necessário)
variables:
  DOCKER_REGISTRY: "registry.gitlab.com"
  DOCKER_IMAGE: "$DOCKER_REGISTRY/meu_usuario/minha_imagem"

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $DOCKER_IMAGE:latest .
  
test:
  stage: test
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker run --rm $DOCKER_IMAGE:latest pytest

push:
  stage: push
  image: docker:latest
  services:
    - docker:dind
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login -u "$CI_REGISTRY_USER" --password-stdin $DOCKER_REGISTRY
    - docker push $DOCKER_IMAGE:latest
```

#### **Explicação das Etapas**

- **build**: A etapa `build` cria a imagem Docker da aplicação usando o Dockerfile.
- **test**: A etapa `test` executa os testes. No exemplo, estamos rodando o `pytest`, mas você pode ajustar para qualquer ferramenta de testes.
- **push**: Envia a imagem Docker para o Docker registry, tornando-a disponível para deploy.

> **Nota**: As variáveis `$CI_REGISTRY_USER` e `$CI_REGISTRY_PASSWORD` são configuradas no GitLab CI para autenticação automática.

### **3. Configuração de Docker no Jenkins**

O Jenkins é uma ferramenta de CI/CD amplamente utilizada e suporta o uso de Docker para pipelines de construção e deploy.

#### **Configurando um Pipeline Declarativo no Jenkins**

Para configurar um pipeline Docker no Jenkins, você pode usar o arquivo `Jenkinsfile`:

```groovy
pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'meu_usuario/minha_imagem:latest'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("$DOCKER_IMAGE")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image("$DOCKER_IMAGE").inside {
                        sh 'pytest'
                    }
                }
            }
        }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
                    sh "docker push $DOCKER_IMAGE"
                }
            }
        }
    }
}
```

#### **Explicação das Etapas**

- **Build**: Compila a imagem Docker usando o Dockerfile.
- **Test**: Executa os testes dentro do container.
- **Push**: Envia a imagem Docker para o Docker Hub ou um registry privado, autenticando-se com credenciais armazenadas no Jenkins.

### **4. Usando Docker com GitHub Actions**

GitHub Actions é uma alternativa popular para pipelines CI/CD, integrada ao GitHub. Ela permite automatizar o build e o deploy de projetos diretamente a partir do repositório.

#### **Criando um Workflow no GitHub Actions**

Para configurar um workflow, crie um arquivo YAML em `.github/workflows/docker-ci.yml`:

```yaml
name: Docker CI/CD

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker image
        uses: docker/build-push-action@v2
        with:
          push: true
          tags: meu_usuario/minha_imagem:latest
```

#### **Explicação das Etapas**

- **Checkout Code**: Acessa o código do repositório.
- **Set up Docker Buildx**: Prepara o ambiente Docker.
- **Login to Docker Hub**: Autentica no Docker Hub usando variáveis secretas do GitHub (`DOCKER_USERNAME` e `DOCKER_PASSWORD`).
- **Build and Push Docker image**: Compila e envia a imagem para o Docker Hub.

> **Dica**: No GitHub, você pode configurar variáveis secretas nas configurações do repositório.

### **5. Práticas Avançadas para Docker em CI/CD**

- **Usar Multistage Builds**: Em pipelines, as builds multi-estágio ajudam a reduzir o tempo de construção e o tamanho das imagens, especialmente em ambientes de CI/CD onde a velocidade é essencial.
  
- **Testar e Validar a Imagem Docker**: Inclua etapas de validação para garantir que a imagem criada está funcionando corretamente antes do deploy. Isso pode incluir testes de unidade, integração e até varredura de segurança.

- **Automatizar Deploy em Kubernetes**: Em ambientes de produção que usam Kubernetes, configure uma etapa de deploy ao final do pipeline, aplicando o YAML de configuração do Kubernetes para atualizar a aplicação automaticamente.

- **Usar Tags para Controle de Versionamento**: Adote uma convenção de tags para diferenciar versões, como `:latest`, `:staging`, ou versões específicas (`:v1.0.0`). Isso facilita a organização e o rollback em caso de problemas.

---

## Conclusão

Neste tutorial, percorremos uma jornada completa pelo universo Docker, explorando desde os conceitos iniciais até as práticas avançadas para ambientes de produção. Agora, você está capacitado para criar, gerenciar e otimizar containers, redes e volumes, além de integrar Docker em pipelines de CI/CD. Esse conhecimento permitirá que você entregue aplicações com maior consistência, segurança e eficiência.

O Docker é uma ferramenta poderosa que continua a evoluir, oferecendo cada vez mais recursos para equipes de desenvolvimento e operações. A prática constante e a exploração de novos recursos serão fundamentais para aproveitar ao máximo seu potencial. Esperamos que este guia seja uma base sólida para o seu trabalho com Docker e que o inspire a continuar aprimorando suas habilidades em DevOps e automação.

Seja no desenvolvimento local ou em ambientes complexos de produção, o Docker oferece a flexibilidade e a escalabilidade necessárias para os desafios modernos de software. Aproveite este conhecimento e boas práticas para levar seus projetos ao próximo nível!

---
