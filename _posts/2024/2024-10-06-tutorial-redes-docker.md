---
title: Gerenciando Redes no Docker
description: Um tutorial passo a passo para gerenciar redes Docker, incluindo a criação e remoção de redes, mapeamento de portas, gerenciamento de links entre contêineres, e configuração de DNS.
date: 2024-10-06 10:30:00 -0300
version: 1.0
categories: [Docker, Redes, Containers, DevOps]
tags: [Docker, Redes, Containers, Bridge, Host, DNS]
---

====== Sumário ======
1. [Introdução](#introdução)
2. [Gerenciar Portas](#gerenciar-portas)
3. [Gerenciar Links](#gerenciar-links)
4. [Gerenciar DNS](#gerenciar-dns)
5. [Rede Bridge](#rede-bridge)
6. [Rede Host](#rede-host)
7. [Comandos de Gerenciamento de Redes no Docker](#comandos-de-gerenciamento-de-redes-no-docker)
8. [Remover Redes](#remover-rede)
9. [Conclusão](#conclusão)

### Introdução

O **Docker** simplificou o processo de criação, implantação e gerenciamento de contêineres, oferecendo aos desenvolvedores e administradores de sistemas um ambiente portátil e consistente para executar aplicações. No entanto, a verdadeira força do Docker não está apenas na execução de contêineres isolados, mas também na sua capacidade de configurar redes robustas e flexíveis que permitem a comunicação entre contêineres, serviços e o mundo externo.

Este tutorial detalha os aspectos essenciais do gerenciamento de redes no Docker, incluindo como criar e personalizar redes, mapear portas, gerenciar links entre contêineres e configurar servidores DNS. Através de exemplos práticos, você aprenderá a usar as redes padrão do Docker, como **bridge** e **host**, e também a criar redes personalizadas com controle sobre sub-redes e endereçamento IP.

### Gerenciando Redes no Docker

#### Gerenciar portas

**Lista de redes disponíveis no Docker:**
```bash
docker network ls
```
- **`docker network ls`**: Este comando lista todas as redes disponíveis no Docker. No exemplo, temos três redes padrões:
  - **bridge**: Rede padrão do Docker, onde os contêineres podem se comunicar entre si, mas com regras de firewall configuradas para o tráfego externo.
  - **host**: Nesta rede, os contêineres compartilham a interface de rede do host diretamente.
  - **none**: Usada para desabilitar completamente a rede de um contêiner.

**Mapeando a porta 80 do contêiner no host local:**
```bash
docker container run -d --name web --network bridge -p 80:80 nginx
```
- **`docker container run -d --name web --network bridge -p 80:80 nginx`**: Este comando cria e executa um contêiner em segundo plano (`-d`) chamado **web**, utilizando a rede `bridge` e mapeando a porta **80** do host para a porta **80** do contêiner.
  - **`-p 80:80`**: Mapeia a porta 80 do host para a porta 80 dentro do contêiner, permitindo acesso externo ao serviço rodando no contêiner (no caso, o **nginx**).

**Verificando o mapeamento de portas do contêiner:**
```bash
docker container port web
```
- **`docker container port web`**: Exibe o mapeamento de portas do contêiner chamado **web**. No exemplo, mostra que a porta **80** está mapeada para **0.0.0.0:80** e **[::]:80**, indicando que a porta está exposta tanto em IPv4 quanto em IPv6.

**Lista de contêineres em execução:**
```bash
docker ps
```
- **`docker ps`**: Lista todos os contêineres que estão atualmente em execução, mostrando o ID do contêiner, a imagem usada, o tempo de execução, e as portas mapeadas.

**Executando uma requisição HTTP ao Nginx:**
```bash
curl 127.0.0.1
```
- **`curl 127.0.0.1`**: Faz uma requisição HTTP ao endereço **127.0.0.1** (localhost) para testar o serviço Nginx rodando no contêiner, que foi mapeado para a porta 80 no host.

**Removendo o contêiner:**
```bash
docker container rm -f web
```
- **`docker container rm -f web`**: Força a remoção do contêiner **web**, encerrando-o e liberando os recursos associados.


#### Mapeando uma porta de forma aleatória

**Executando um contêiner com porta mapeada aleatoriamente:**
```bash
docker container run -d --name web -P nginx
```
- **`docker container run -d --name web -P nginx`**: Este comando cria um contêiner chamado **web** utilizando a imagem **nginx** e mapeia todas as portas expostas do contêiner para portas aleatórias no host. O `-P` (maiúsculo) mapeia as portas internas para portas livres no host, enquanto o `-p` (minúsculo) mapeia portas específicas.

**Verificando o mapeamento de portas aleatórias:**
```bash
docker container port web
```
- **`docker container port web`**: Mostra que a porta **80** do contêiner foi mapeada para uma porta aleatória no host, neste caso, **32768**.

**Testando o mapeamento de porta aleatória:**
```bash
curl 127.0.0.1:32768
```
- **`curl 127.0.0.1:32768`**: Faz uma requisição HTTP à porta aleatória (**32768**) no host, que foi mapeada para a porta **80** do contêiner Nginx. O comando deve retornar a página padrão do **Nginx**.

**Removendo o contêiner:**
```bash
docker container rm -f web
```
- **`docker container rm -f web`**: Força a remoção do contêiner **web**, encerrando o serviço Nginx e liberando a porta aleatória no host.

---

#### Gerenciar links

**Executando um contêiner chamado "server":**
```bash
docker container run -di --name=server -h server busybox
```
- **`docker container run -di --name=server -h server busybox`**: Este comando cria e executa um contêiner em segundo plano com o nome **server**, usando a imagem **busybox**. A flag `-h` define o hostname do contêiner como **server**.

**Executando um contêiner "client" com link para o contêiner "server":**
```bash
docker container run --detach --interactive --name=client --link server:server --hostname client busybox
```
- **`docker container run --detach --interactive --name=client --link server:server --hostname client busybox`**: Este comando cria o contêiner **client** e faz um link para o contêiner **server**. O parâmetro **`--link server:server`** cria uma entrada no arquivo `/etc/hosts` do contêiner **client**, permitindo que ele se conecte ao contêiner **server** usando o hostname **server**.
  - **`--link`**: Permite que um contêiner se comunique com outro através de seu hostname, útil para facilitar a conexão entre contêineres.

**Verificando o arquivo `/etc/hosts` no contêiner "client":**
```bash
docker container exec --interactive --tty client cat /etc/hosts
```
- **`docker container exec --interactive --tty client cat /etc/hosts`**: Verifica o arquivo **/etc/hosts** no contêiner **client**, onde o Docker automaticamente insere o hostname e IP do contêiner **server**, permitindo a comunicação entre eles.

**Teste de conectividade entre "client" e "server":**
```bash
docker container exec --interactive --tty client ping -c3 server
```
- **`docker container exec --interactive --tty client ping -c3 server`**: O comando **ping** testa a conectividade entre o contêiner **client** e o contêiner **server** usando o hostname **server**. Como o link foi criado corretamente, o ping retorna com sucesso.

**Verificando o arquivo `/etc/hosts` no contêiner "server":**
```bash
docker container exec -it server cat /etc/hosts
```
- **`docker container exec -it server cat /etc/hosts`**: No contêiner **server**, o arquivo **/etc/hosts** não tem a entrada para o contêiner **client**, pois o link é unidirecional (somente **client** tem acesso ao **server**).

**Removendo os contêineres:**
```bash
docker container rm -f server client
```
- **`docker container rm -f server client`**: Remove os contêineres **server** e **client**, encerrando o teste de comunicação entre eles.

### Gerenciar DNS

**Executando um contêiner com um servidor DNS público especificado:**
```bash
docker container run --interactive --detach --name=server --hostname server --dns=1.1.1.1 busybox
```
- **`docker container run --interactive --detach --name=server --hostname server --dns=1.1.1.1 busybox`**: Este comando cria um contêiner chamado **server** e define o hostname como **server**. A flag `--dns=1.1.1.1` especifica que o contêiner deve usar o servidor DNS **1.1.1.1** (da Cloudflare) para resolver nomes de domínio.
  - **`--dns`**: Permite especificar um servidor DNS personalizado para o contêiner, substituindo o servidor DNS padrão do host Docker.

**Verificando o arquivo `/etc/resolv.conf` dentro do contêiner:**
```bash
docker container exec -it server cat /etc/resolv.conf
```
- **`docker container exec -it server cat /etc/resolv.conf`**: Exibe o conteúdo do arquivo **/etc/resolv.conf** dentro do contêiner **server**, onde o Docker configurou o servidor DNS **1.1.1.1**. Este arquivo controla como o contêiner resolve nomes de domínio.

**Teste de resolução de DNS com o comando `nslookup`:**
```bash
docker container exec -it server nslookup -querytype=A geanmartins.com.br
```
- **`docker container exec -it server nslookup -querytype=A geanmartins.com.br`**: O comando **nslookup** consulta o servidor DNS especificado no contêiner (1.1.1.1) para obter o endereço IP do domínio **geanmartins.com.br**. O tipo de consulta especificado (`-querytype=A`) retorna o registro A (IPv4) do domínio.

**Removendo o contêiner:**
```bash
docker rm -f server
```
- **`docker rm -f server`**: Remove o contêiner **server**, encerrando seu processo e liberando os recursos associados.

---

### Configuração persistente de DNS no Docker

**Editando o arquivo `daemon.json` para definir o DNS padrão do Docker:**
```bash
cat /etc/docker/daemon.json 
{
  "log-driver": "syslog",
  "dns": ["1.1.1.1"]
}
```
- **`/etc/docker/daemon.json`**: Este arquivo de configuração controla as opções padrão do Docker daemon. Neste exemplo, a chave `"dns": ["1.1.1.1"]` define o DNS padrão para **1.1.1.1** para todos os contêineres que não especificarem manualmente um DNS.
  - **`log-driver`**: Configura o driver de logs para syslog, enviando logs para o serviço de registro de sistema do Linux.
  - **`dns`**: Define o servidor DNS padrão que será aplicado a todos os contêineres.

**Reiniciando o Docker para aplicar as alterações:**
```bash
sudo systemctl restart docker
```
- **`sudo systemctl restart docker`**: Reinicia o serviço Docker, aplicando as alterações feitas no arquivo `daemon.json`. A partir de agora, qualquer contêiner criado sem especificar um DNS usará **1.1.1.1** como servidor DNS.

**Executando um novo contêiner sem especificar um servidor DNS:**
```bash
docker exec -ti server nslookup -querytype=A geanmartins.com.br
```
- Mesmo sem especificar o DNS ao criar o contêiner, o comando **nslookup** usa o DNS **1.1.1.1** configurado no `daemon.json` para resolver o domínio.

---

### Rede Bridge

**Executando um contêiner utilizando a rede bridge e mapeando a porta 80:**
```bash
docker container run -d --name web --network bridge -p 80:80 nginx
```
- **`docker container run -d --name web --network bridge -p 80:80 nginx`**: Este comando cria um contêiner chamado **web** que usa a rede **bridge** (a rede padrão no Docker). O parâmetro **`-p 80:80`** mapeia a porta **80** do contêiner para a porta **80** do host, permitindo que o Nginx dentro do contêiner seja acessado via **localhost:80**.
  - **Rede Bridge**: A rede **bridge** isola os contêineres entre si, mas permite que eles se comuniquem através de NAT (Network Address Translation) configurado pelo Docker. Essa é a rede padrão para contêineres.

**Verificando se a porta 80 está sendo escutada no host:**
```bash
sudo ss -nltp | grep 80
```
- **`ss -nltp | grep 80`**: O comando **ss** mostra as portas que estão sendo escutadas no sistema. Aqui, ele verifica se a porta **80** está aberta e sendo usada pelo contêiner **nginx** através do proxy do Docker (**docker-proxy**).

**Verificando a regra de firewall (iptables) gerada pelo Docker:**
```bash
sudo iptables -nL --line-number | grep 80 -B2
```
- **`iptables -nL --line-number | grep 80`**: Este comando mostra as regras de firewall (iptables) geradas automaticamente pelo Docker. No exemplo, a regra permite que o tráfego para a porta **80** no host seja redirecionado para o contêiner **nginx**.

**Removendo o contêiner:**
```bash
docker rm -f web
```
- **`docker rm -f web`**: Força a remoção do contêiner **web**, liberando a porta **80** no host e removendo as regras de redirecionamento configuradas pelo Docker.

---

### Rede Host

**Executando um contêiner utilizando a rede host:**
```bash
docker container run -d --name server --net=host nginx
```
- **`docker container run -d --name server --net=host nginx`**: Este comando cria um contêiner chamado **server** que usa a rede **host**. Na rede **host**, o contêiner compartilha diretamente a interface de rede do host, sem isolamento de rede. Isso significa que o Nginx dentro do contêiner estará diretamente acessível pelas portas do host, sem a necessidade de mapeamento de portas.
  - **Rede Host**: Permite que o contêiner use a pilha de rede do host diretamente, o que pode ser útil em casos onde o desempenho de rede é crítico ou quando o contêiner precisa acessar diretamente interfaces de rede do host.

**Verificando se a porta 80 está sendo escutada no host:**
```bash
sudo ss -nltp | grep 80
```
- **`ss -nltp | grep 80`**: Como o contêiner está usando a rede **host**, o processo **nginx** está diretamente escutando na porta **80** do host, sem precisar de mapeamento de portas via **docker-proxy**.

**Verificando a ausência de mapeamento de portas no contêiner:**
```bash
docker container port server
```
- **`docker container port server`**: Como o contêiner está na rede **host**, este comando não retorna nenhuma porta mapeada, uma vez que o contêiner está diretamente utilizando a porta do host sem NAT ou redirecionamento.

**Removendo o contêiner:**
```bash
docker container rm -f server
```
- **`docker container rm -f server`**: Remove o contêiner **server**, encerrando o processo **nginx** que estava rodando diretamente na interface de rede do host.

---

### Comandos de gerenciamento de redes no Docker

#### Utilizar Rede Padrão

**Opções do comando `docker network`:**
```bash
docker network --help
```
- **`docker network --help`**: Exibe a lista de comandos disponíveis para gerenciamento de redes no Docker. Comandos como `connect`, `create`, `disconnect`, `inspect`, `ls`, `prune` e `rm` são usados para criar, conectar, inspecionar e remover redes.

**Listando redes existentes:**
```bash
docker network ls
```
- **`docker network ls`**: Lista todas as redes no Docker. Cada rede tem um **ID**, **nome**, **driver** (por exemplo, **bridge**, **host**) e o escopo, que geralmente é local.

**Inspecionando uma rede em detalhes:**
```bash
docker network inspect bridge
```
- **`docker network inspect bridge`**: Exibe informações detalhadas sobre a rede **bridge**, como seu ID, a sub-rede configurada, o gateway, e os contêineres conectados a essa rede. Esse comando é útil para depurar problemas de rede e visualizar como o Docker gerencia o isolamento entre contêineres.

---

#### Criar Redes Personalizadas

**Criando uma nova rede personalizada:**
```bash
docker network create --driver bridge --subnet 172.32.0.0/16 dca
```
- **`docker network create --driver bridge --subnet 172.32.0.0/16 dca`**: Este comando cria uma nova rede chamada **dca** com o driver **bridge** e atribui a sub-rede **172.32.0.0/16**. O Docker permite a criação de redes personalizadas para separar ambientes de contêineres ou para fornecer controle mais granular sobre as redes.
  - **`--driver bridge`**: Especifica que a rede deve usar o driver **bridge**.
  - **`--subnet 172.32.0.0/16`**: Define o range de IPs da sub-rede que os contêineres dessa rede vão utilizar.

**Listando as redes após a criação:**
```bash
docker network ls
```
- **`docker network ls`**: Mostra a nova rede **dca** criada, além das redes padrão do Docker (**bridge**, **host**, **none**).

**Inspecionando a rede personalizada:**
```bash
docker network inspect dca
```
- **`docker network inspect dca`**: Exibe informações detalhadas sobre a rede **dca**, incluindo a sub-rede configurada, o gateway, e os contêineres conectados a ela. Essa visão detalhada ajuda a entender como a rede está configurada e se está funcionando conforme o esperado.

---

#### Utilizando IP Fixo

**Criando um contêiner com IP fixo em uma rede personalizada:**
```bash
docker container run -di --name=c1 -h server --network dca --ip 172.32.0.100 --add-host=client:172.32.0.113 busybox
```
- **`docker container run -di --name=c1 -h server --network dca --ip 172.32.0.100 --add-host=client:172.32.0.113 busybox`**: Cria o contêiner **c1** com um IP fixo **172.32.0.100** na rede personalizada **dca**. O parâmetro **`--add-host=client:172.32.0.113`** adiciona uma entrada no arquivo `/etc/hosts` do contêiner, associando o hostname **client** ao IP **172.32.0.113**, facilitando a comunicação.
  - **`--ip`**: Define um IP fixo para o contêiner dentro da rede especificada.

**Teste de conectividade entre contêineres:**
```bash
docker container exec -it c1 ping -c2 client
```
- **`docker container exec -it c1 ping -c2 client`**: O contêiner **c1** realiza um teste de conectividade (ping) para o hostname **client** (resolvendo o IP **172.32.0.113**), demonstrando que a rede personalizada está funcionando corretamente com a atribuição de IPs fixos.

---

#### Conectar e Desconectar Contêineres

**Desconectando um contêiner de uma rede:**
```bash
docker network disconnect dca c2
```
- **`docker network disconnect dca c2`**: Desconecta o contêiner **c2** da rede **dca**, removendo sua associação com essa rede. Isso pode ser útil se o contêiner não precisar mais se comunicar com outros contêineres naquela rede.

**Verificando a perda de conectividade:**
```bash
docker container exec -ti c1 ping -c2 client
```
- Após a desconexão, o comando **ping** no contêiner **c1** falha ao tentar se conectar ao contêiner **client**, confirmando que **c2** foi corretamente desconectado da rede.

**Reconectando o contêiner à rede com um IP fixo:**
```bash
docker network connect --ip 172.32.0.113 dca c2
```
- **`docker network connect --ip 172.32.0.113 dca c2`**: Reconecta o contêiner **c2** à rede **dca** com o mesmo IP **172.32.0.113**, restaurando a conectividade.

---

### Remover Rede

**Verificando a conectividade antes da remoção:**
```bash
docker container exec -ti c1 ping -c2 client
```
- **`docker container exec -ti c1 ping -c2 client`**: Antes de remover a rede, este comando verifica a conectividade entre os contêineres **c1** e **c2** na rede **dca**. O ping deve retornar pacotes recebidos corretamente, confirmando que ambos os contêineres ainda estão conectados à rede.

**Removendo os contêineres conectados à rede:**
```bash
docker container rm -f c1 c2
```
- **`docker container rm -f c1 c2`**: Força a remoção dos contêineres **c1** e **c2**, liberando os IPs e desconectando-os da rede. O Docker não permite remover uma rede se ainda houver contêineres conectados a ela, por isso os contêineres devem ser removidos primeiro.

**Removendo a rede personalizada:**
```bash
docker network rm dca
```
- **`docker network rm dca`**: Remove a rede personalizada **dca** do Docker. Após este comando, a rede **dca** não estará mais disponível para novos contêineres.

**Listando redes restantes:**
```bash
docker network ls
```
- **`docker network ls`**: Lista as redes restantes no Docker, que agora devem ser apenas as redes padrão (**bridge**, **host**, e **none**) após a remoção da rede **dca**.

---

#### Removendo redes que não estão em uso

**Removendo todas as redes não utilizadas:**
```bash
docker network prune
```
- **`docker network prune`**: Remove todas as redes personalizadas que não estão em uso por nenhum contêiner. Esta é uma maneira rápida de limpar redes antigas ou redes não utilizadas, liberando recursos.
  - **Alerta**: Este comando remove **todas** as redes não usadas, portanto deve ser utilizado com cautela em ambientes onde algumas redes são necessárias, mas não possuem contêineres ativos no momento.

### Conclusão

O gerenciamento de redes no Docker é um aspecto crucial para quem deseja operar contêineres em ambientes produtivos, simulando redes internas complexas ou expostas para o público. Neste tutorial, abordamos desde a criação de redes básicas com drivers padrão, como **bridge** e **host**, até a configuração de redes personalizadas com IPs fixos e sub-redes específicas.

Além disso, você aprendeu a testar a conectividade entre contêineres, utilizar mapeamento de portas, e configurar DNS personalizados para contêineres. Essas práticas são fundamentais para garantir que suas aplicações sejam escaláveis, seguras e funcionais em diferentes ambientes de desenvolvimento e produção.

Com o conhecimento adquirido, você agora tem as ferramentas necessárias para controlar como os contêineres se comunicam, como eles interagem com o host e com o mundo externo, e como manter sua infraestrutura Docker organizada e eficiente.
