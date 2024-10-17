---
title: "Orquestrando Ambientes com Docker Swarm"
description: "Aprenda a configurar e gerenciar clusters de nós Docker usando o Docker Swarm. Inclui passos para criar clusters, adicionar nós e gerenciar tarefas de forma eficiente."
author: "Seu Nome"
date: "2024-10-17"
categories: ["Docker", "Orquestração de Containers", "DevOps"]
tags: ["Docker Swarm", "Cluster", "Orquestração", "Containers", "DevOps"]
layout: "post"
---

## Sumário
- [O que é Docker Swarm](#o-que-é-docker-swarm)
- [Vantagens do Docker Swarm](#vantagens-do-docker-swarm)
- [Estrutura de um Cluster Docker Swarm](#estrutura-de-um-cluster-docker-swarm)
- [Criando um Cluster com Docker Swarm](#criando-um-cluster-com-docker-swarm)
- [Gerenciamento de Nós no Cluster Swarm](#gerenciamento-de-nós-no-cluster-swarm)
- [Promoção e Despromoção de Nós no Cluster](#promoção-e-despromoção-de-nós-no-cluster)
- [Descrição dos Comandos para Gerenciamento de Nós](#descrição-dos-comandos-para-gerenciamento-de-nós)

## Orquestrando Ambientes com Docker Swarm

### O que é Docker Swarm?
O Docker Swarm é uma ferramenta integrada ao Docker Engine que permite criar e gerenciar clusters de nós Docker. Com ele, você pode organizar e orquestrar serviços de forma eficiente, tornando o processo de escalar e gerenciar seus aplicativos muito mais fácil.

O Swarm utiliza **tokens** de identificação para adicionar novas máquinas ao cluster. Sempre que um novo nó se junta ao cluster, ele fornece um token que o Docker Engine usa para integrá-lo. Isso permite uma gestão centralizada de todo o ambiente.

### Implantação no Docker Swarm
Uma das abordagens padrão para implantação de serviços no Docker Swarm é o **controle de versão**. Isso significa que, para implantar uma nova versão de um serviço, você cria uma nova imagem, a publica no cluster e o Docker verifica se há uma atualização disponível. Se houver, o serviço será atualizado automaticamente para a versão mais recente.

Outra estratégia comum no Docker Swarm é o **rolling update**. Neste processo, os nós do cluster são atualizados um de cada vez com a nova versão do serviço. Essa abordagem minimiza o tempo de inatividade, já que o serviço continua rodando enquanto os nós são atualizados.

### Vantagens do Docker Swarm
O Docker Swarm oferece diversas vantagens, tornando-se uma excelente opção para orquestração de containers:

- **Integração com Docker Engine**: Como é integrado ao Docker, você pode usar a mesma interface e comandos com os quais já está familiarizado para gerenciar o cluster.
- **Simples de Configurar**: Com ferramentas nativas como o **Swarm Manager**, configurar e gerenciar o ambiente de cluster é simples e direto.
- **Balanceamento de Carga**: O Swarm oferece suporte a múltiplos algoritmos de balanceamento de carga, possibilitando a escolha do método que melhor atenda às suas necessidades.
- **Compatibilidade Ampla**: É capaz de gerenciar vários tipos de aplicativos, como sistemas web, bancos de dados e até serviços de mensagens.
- **Alta Disponibilidade**: Ao usar clusters, o Swarm permite manter seus serviços em execução mesmo durante manutenções ou falhas, garantindo alta disponibilidade.

---

### Estrutura de um Cluster Docker Swarm

No Docker Swarm, a estrutura do cluster é composta por dois tipos principais de nós:

- **Manager Node**: Este nó gerencia o cluster. Ele é responsável por orquestrar os serviços, despachar tarefas para os nós workers e manter o estado desejado do cluster. O manager node também é o ponto central de comunicação com o Docker CLI para gerenciar o cluster.
  
- **Worker Node**: Este nó é responsável por executar as tarefas atribuídas pelos managers. Por padrão, um nó manager também pode atuar como worker, mas é possível configurá-lo para se concentrar apenas no gerenciamento.

#### Exemplo de Infraestrutura

Vamos supor que temos a seguinte configuração de infraestrutura:

| VM               | Função                                    | IP             | Sistema Operacional |
| ---------------- | ----------------------------------------- | -------------- | ------------------- |
| **dca-manager**   | Nó de gerenciamento do cluster            | 10.4.5.10      | Ubuntu              |
| **dca-wk-01**     | Nó de trabalho do cluster                 | 10.4.5.11      | Ubuntu              |
| **dca-wk-02**     | Nó de trabalho do cluster                 | 10.4.5.12      | Ubuntu              |
| **dca-registry**  | Registro de imagens Docker                | 10.4.5.13      | Oracle Linux        |

### Criando um Cluster com Docker Swarm

#### Passo 1: Inicializar o Cluster
No nó manager, inicie o cluster com o seguinte comando:
```bash
docker swarm init --advertise-addr 10.4.5.10
```
Isso inicializa o Docker Swarm, promovendo o nó atual a **manager**. O argumento `--advertise-addr` especifica o IP que os outros nós usarão para se conectar ao nó manager.

Você verá uma mensagem semelhante a esta:
```bash
Swarm initialized: current node (x3pkngofl66650g9rz2qc5qhl) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0a7hbhhn5tyqhq3o5hgjknh7ahh55x8dnq73rjjohhcjxk39gl-48bo2tablmqfi04f8y0i59osh 10.4.5.10:2377
```
Guarde esse comando, pois ele será necessário para adicionar novos nós workers ao cluster.

#### Passo 2: Adicionar Workers ao Cluster
Nos nós workers, execute o comando mostrado no final da inicialização do Swarm. Por exemplo:
```bash
docker swarm join --token SWMTKN-1-0a7hbhhn5tyqhq3o5hgjknh7ahh55x8dnq73rjjohhcjxk39gl-48bo2tablmqfi04f8y0i59osh 10.4.5.10:2377
```
Este comando conecta o nó ao cluster como um worker.

Você pode verificar os nós conectados ao cluster executando o seguinte comando no nó manager:
```bash
docker node ls
```
Isso exibirá uma lista de nós e seus status, incluindo quais são **managers** e quais são **workers**.

#### Verificando o Status do Swarm
Para garantir que o Docker Swarm está ativo, execute o comando:
```bash
docker info | grep -i swarm
```
Se o resultado mostrar `Swarm: active`, significa que o cluster está funcionando corretamente.

---

### Gerenciamento de Nós no Cluster Swarm

Uma vez que o cluster Docker Swarm esteja configurado, você pode gerenciar os nós usando vários comandos do Docker CLI. Abaixo estão alguns comandos essenciais para gerenciar os nós no cluster:

#### Listar Nós no Cluster
Para listar todos os nós conectados ao cluster, incluindo informações sobre seu status, função e versão do Docker Engine, execute o comando:
```bash
docker node ls
```
O resultado mostrará os nós com as seguintes colunas:

- **ID**: Identificador único do nó.
- **HOSTNAME**: Nome do host do nó.
- **STATUS**: Indica se o nó está ativo (Ready).
- **AVAILABILITY**: Indica se o nó está disponível para receber tarefas (Active).
- **MANAGER STATUS**: Se o nó for um manager, indicará se é o líder (Leader) ou um manager acessível (Reachable).

#### Filtrar Nós por Função
Você pode filtrar os nós exibidos com base na função deles, seja **manager** ou **worker**:

- Para listar apenas os nós **managers**, use:
  ```bash
  docker node ls --filter role=manager
  ```

- Para listar apenas os nós **workers**, use:
  ```bash
  docker node ls --filter role=worker
  ```

#### Definir Labels para Identificar Nós
Os **labels** são uma forma de classificar e identificar nós no cluster Swarm, facilitando o gerenciamento e a organização. Para adicionar um label, use o comando `docker node update`:

- Para definir o label `app=backend` no nó `dca-manager`, execute:
  ```bash
  docker node update --label-add app=backend dca-manager
  ```

- Para adicionar o label `app=frontend` nos nós `dca-wk-01` e `dca-wk-02`, use:
  ```bash
  docker node update --label-add app=frontend dca-wk-01
  docker node update --label-add app=frontend dca-wk-02
  ```

Se precisar remover um label, utilize o comando:
```bash
docker node update --label-rm app dca-manager
```

#### Inspecionar um Nó
Para obter informações detalhadas sobre um nó específico, incluindo labels, status e configuração, utilize o comando:
```bash
docker node inspect <NOME_DO_NO>
```

- Se quiser filtrar as informações, por exemplo, para ver apenas os labels de um nó, pode usar:
  ```bash
  docker node inspect --pretty <NOME_DO_NO> | grep -i labels -A 2
  ```

#### Remover um Nó do Cluster
Para remover um nó do cluster, utilize o comando `docker node rm`. Se o nó estiver ativo, pode ser necessário usar a flag `--force`:
```bash
docker node rm dca-wk-01 --force
```

Alternativamente, se quiser que o nó abandone voluntariamente o cluster, execute no próprio nó o comando:
```bash
docker swarm leave
```
Após isso, o nó será removido do cluster e deixará de executar tarefas.

---

### Promoção e Despromoção de Nós no Cluster

Em um cluster Docker Swarm, você pode promover um nó **worker** para **manager** ou despromover um **manager** para **worker**, conforme necessário. Isso permite maior flexibilidade na administração do cluster e no gerenciamento das tarefas.

#### Promoção de um Worker a Manager
Para promover um nó worker a manager, utilize o comando `docker node promote`. Esse comando eleva o nó ao nível de manager, permitindo que ele participe da orquestração e gerenciamento do cluster.

Por exemplo, para promover o nó `dca-wk-01` a manager, use o seguinte comando no nó manager:
```bash
docker node promote dca-wk-01
```
Após a execução, o nó `dca-wk-01` será promovido, e você poderá verificar o novo status com:
```bash
docker node ls
```
O status do nó `dca-wk-01` na coluna **MANAGER STATUS** mudará para **Reachable**, indicando que ele agora é um manager acessível.

#### Despromover um Manager para Worker
Se precisar rebaixar um nó manager para worker, use o comando `docker node demote`. Por exemplo, para despromover o nó `dca-manager` de volta para worker, execute:
```bash
docker node demote dca-manager
```
Isso removerá o status de gerenciamento do nó, e ele voltará a atuar apenas como um nó worker. O status pode ser verificado novamente com:
```bash
docker node ls
```

#### Alterando o Nó Líder (Leader)
No Docker Swarm, o nó **líder** é responsável por tomar decisões de orquestração. Caso o líder atual se torne indisponível, o Swarm automaticamente elege um novo líder entre os nós managers restantes.

Se desejar, você pode promover outro nó a manager, permitindo que ele participe das eleições futuras para a liderança.

#### Reverter uma Promoção ou Despromoção
Se for necessário reverter a promoção ou despromoção de um nó, basta repetir os comandos:

- Para **promover novamente** o `dca-manager` a manager:
  ```bash
  docker node promote dca-manager
  ```

- Para **despromover** o `dca-wk-01` de volta para worker:
  ```bash
  docker node demote dca-wk-01
  ```

Após qualquer alteração de status dos nós, lembre-se de verificar a nova configuração do cluster com:
```bash
docker node ls
```

### Descrição dos Comandos para Gerenciamento de Nós

Aqui está uma breve descrição dos comandos usados para gerenciamento de nós no Docker Swarm:

- `docker node promote`: Promove um nó de worker a manager.
- `docker node demote`: Rebaixa um nó de manager a worker.
- `docker node update --label-add`: Adiciona um label a um nó.
- `docker node update --label-rm`: Remove um label de um nó.
- `docker node inspect`: Exibe informações detalhadas sobre um nó.
- `docker node rm`: Remove um nó do cluster.
- `docker swarm leave`: Remove um nó do cluster de forma voluntária.
- `docker node ls`: Lista todos os nós no cluster, exibindo suas funções e status.

---