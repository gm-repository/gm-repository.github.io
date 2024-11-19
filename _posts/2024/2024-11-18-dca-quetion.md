---
title: "Docker DCA"
date: 2024-11-18 17:00:00 -0300
categories: [dca]
tags: [dca]
---

### Questão nº 1  
Ação: Esta ação irá atualizar o Docker Engine CE para o Docker Engine EE?  
Solução: Execute `docker engine activate`.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Executar o comando `docker engine activate` atualizará o Docker Engine CE para o Docker Engine EE. Essa funcionalidade permite alternar da edição Community Edition (CE) para a Enterprise Edition (EE) sem reinstalar o Docker ou perder dados. Para usar este recurso, é necessário possuir um arquivo de licença válido e uma assinatura do Docker EE.  

O Docker EE é uma versão premium do Docker CE que oferece recursos adicionais, como varredura de segurança, gerenciamento de imagens e plugins certificados.

---

### Questão nº 2  
Um dos vários containers em um pod foi marcado como não saudável após falhar repetidamente em seu **livenessProbe**. Esta é a ação realizada pelo orquestrador para corrigir o container não saudável?  
Solução: O Kubernetes automaticamente aciona um script definido pelo usuário para tentar corrigir o container não saudável.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A questão trata do Kubernetes, que é um orquestrador para gerenciar múltiplos containers em um pod, onde os pods compartilham rede e armazenamento. Um **livenessProbe** verifica se um container está vivo e pronto para atender requisições.  

Se um container falha no **livenessProbe**, o Kubernetes, por padrão, tenta reiniciá-lo automaticamente. No entanto, é possível definir uma ação personalizada para executar quando um container falha no **livenessProbe**, como um script para corrigir o problema.  

A solução apresentada na questão sugere que o Kubernetes sempre executa um script definido pelo usuário, o que está incorreto. Por padrão, o Kubernetes reinicia o container sem executar scripts personalizados, a menos que isso seja explicitamente configurado.

---

### Questão nº 3 
Este comando exibe todos os pods no cluster que estão etiquetados como 'env: development'?  
Solução: `kubectl get pods --all-namespaces -label env=development`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `kubectl get pods --all-namespaces -label env=development` é inválido porque contém um erro de sintaxe.  

A sintaxe correta para listar pods com um rótulo específico é:  
- `kubectl get pods --all-namespaces --selector label=value`  
ou  
- `kubectl get pods --all-namespaces -l label=value`.  

O erro no comando ocorre porque a flag para especificar o seletor de rótulo é `--selector` ou `-l`, não `-label`.  

Por exemplo, em vez de `-label env=development`, o correto seria:  
- `--selector env=development`  
ou  
- `-l env=development`.  

O comando correto para listar todos os pods no cluster que possuem o rótulo `env: development` é:  
```bash
kubectl get pods --all-namespaces --selector env=development
```  
Este comando exibirá o nome, status, número de reinicializações e tempo de execução dos pods com o rótulo `env: development` em todos os namespaces.

---

### Questão nº 4 
Este conjunto de comandos pode identificar as portas publicadas para um container?  
Solução: `docker container inspect`, `docker port`  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O conjunto de comandos `docker container inspect` e `docker port` pode identificar as portas publicadas para um container.  

- O comando `docker container inspect` fornece informações detalhadas sobre o container, incluindo configurações de rede e vinculações de portas. Por exemplo:  
  ```bash
  docker container inspect -f '{{.NetworkSettings.Ports}}' container_name
  ```  
  Isso exibirá os mapeamentos de portas para o container especificado.  

- O comando `docker port` lista os mapeamentos de portas ou um mapeamento específico para o container. Por exemplo:  
  ```bash
  docker port container_name
  ```  
  Este comando mostrará quais portas do host estão mapeadas para quais portas do container e o protocolo usado.  

Portanto, ambos os comandos podem ser usados para identificar as portas publicadas de um container.

---

### Questão nº 5
O YAML do Kubernetes mostrado abaixo descreve um serviço do tipo **ClusterIP**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: dca
spec:
  type: clusterIP
  selector:
      app: nginx
  ports:
  - port: 8080
    targetPort: 80
  - port: 4443
    targetPort: 443
```

Esta é uma afirmação correta sobre como este serviço roteia as requisições?  
**Solução:** O tráfego enviado ao IP deste serviço na porta 80 será roteado para a porta 8080 em um pod aleatório com o rótulo `app: nginx`.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A afirmação está incorreta porque, no YAML fornecido, **não há nenhuma porta 80 configurada no serviço**.  

- Em um serviço do tipo **ClusterIP**, o tráfego enviado para o **ClusterIP** e uma porta configurada será roteado para os pods selecionados pelo rótulo definido no **selector** (`app: nginx`).  
- A porta para a qual o tráfego é direcionado no pod é definida pelo campo **targetPort** na configuração do serviço.  

No YAML fornecido:  
1. O tráfego para a porta **8080** do serviço será roteado para a porta **80** nos pods.  
2. O tráfego para a porta **4443** do serviço será roteado para a porta **443** nos pods.  

Entretanto, **não há qualquer porta 80 configurada no serviço**, tornando a afirmação inválida.  

### Correção da Afirmação:  
Se o objetivo fosse rotear o tráfego enviado à porta 80 do serviço para a porta 8080 no pod, o YAML deveria incluir algo como:  
```yaml
  - port: 80
    targetPort: 8080
```  

Dessa forma, a configuração estaria correta para cumprir o comportamento descrito na solução da questão.

---

### Questão nº 6
Esta ação atualizará o Docker Engine CE para o Docker Engine EE?  
**Solução:** Baixar manualmente o pacote `docker-ee`.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Baixar manualmente o pacote `docker-ee` **não** atualizará o Docker Engine CE para o Docker Engine EE.  

- O Docker Engine CE e o Docker Engine EE são dois produtos diferentes, com métodos de instalação e funcionalidades distintas.  
  - **Docker Engine CE:** Plataforma de containerização gratuita e de código aberto.  
  - **Docker Engine EE:** Plataforma de nível empresarial baseada em assinatura, que inclui recursos adicionais como varredura de segurança, plugins certificados e suporte técnico.  

- Para atualizar do Docker Engine CE para o Docker Engine EE, é necessário:  
  1. **Desinstalar** o Docker Engine CE.  
  2. **Instalar** o Docker Engine EE seguindo a documentação oficial.  

Portanto, simplesmente baixar o pacote `docker-ee` não realiza a atualização de uma versão para a outra.

---

### Questão nº 7  
Este comando cria um serviço Swarm que escuta apenas na porta 53 usando o protocolo UDP?  
**Solução:** `docker service create -name dns-cache -p 53:53 -service udp dns-cache`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando fornecido é **inválido** devido a erros de sintaxe. A sintaxe correta para criar um serviço Swarm com o Docker é:  
```bash
docker service create [OPÇÕES] IMAGEM [COMANDO] [ARGUMENTOS...]
```  

### Problemas no comando fornecido:
1. **Opção `-name`**: Deve haver um espaço entre a flag e o valor. O correto seria `--name dns-cache`.  
2. **Opção `-service udp`**: A flag usada para definir o modo do serviço é `--mode`, e não `-service`. Porém, neste caso, não é necessário definir o modo como `udp`, já que ele é especificado na porta.  
3. **Flag de publicação `-p`**: A flag correta para mapeamento de portas no Swarm é `--publish` ou `-p`, mas precisa incluir o protocolo UDP explicitamente com `/udp`.  

### Comando correto:  
Para criar um serviço Swarm que escuta apenas na porta 53 usando o protocolo UDP:  
```bash
docker service create --name dns-cache --publish 53:53/udp dns-cache
```  

Esse comando cria um serviço chamado `dns-cache` que:  
- Utiliza a imagem `dns-cache`.  
- Expõe a porta 53 no host e no container, utilizando o protocolo UDP.  

Portanto, o comando original fornecido não realiza a ação descrita.

---

### Questão nº 8  
O YAML do Kubernetes mostrado abaixo descreve uma **NetworkPolicy**:  

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: dca
  namespace: default
spec:
  podSelector:
    matchLabels:
      tier: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          tier: api
```

Esta **NetworkPolicy** BLOQUEARÁ este tráfego?  
**Solução:** Uma solicitação originada de um pod com o rótulo `tier: backend` para um pod com o rótulo `tier: frontend`.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A **NetworkPolicy** fornecida **não bloqueia** o tráfego descrito porque as **NetworkPolicies** no Kubernetes são usadas para controlar explicitamente o tráfego permitido.  

### Comportamento da NetworkPolicy:
1. O `podSelector` especifica que esta política se aplica apenas aos pods com o rótulo `tier: backend`.  
2. A regra de `ingress` permite tráfego de entrada para os pods com o rótulo `tier: backend` **somente** de pods com o rótulo `tier: api`.  

### Tráfego descrito:  
- Uma solicitação de um pod com o rótulo `tier: backend` para um pod com o rótulo `tier: frontend` **não é afetada por esta NetworkPolicy**, porque:  
  - A **NetworkPolicy** não especifica regras para controlar o tráfego **de saída** de `tier: backend`.  
  - O tráfego de saída do pod com `tier: backend` continuará permitido, a menos que uma regra explícita o bloqueie.  

Portanto, o tráfego **não será bloqueado** porque a política não controla tráfego de saída, nem define regras para o tráfego destinado a pods com `tier: frontend`.

---

### Questão nº 9  
O arquivo Docker Compose abaixo é implantado como um stack:  

```yaml
version: '3.1'
services:
  app:
    image: app:1.0
    healthcheck:
      test: "curl --fail http://localhost/health || exit 1"
      interval: 10s
      timeout: 5s
      retries: 3
```

Esta afirmação está correta sobre a definição do **healthcheck**?  
**Solução:** Os **health checks** verificam a saúde do `app` a cada dez segundos. Três falhas consecutivas no **health check** colocam o container no status "unhealthy".  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
A afirmação está correta.  

No arquivo Docker Compose fornecido:  
- O parâmetro `interval: 10s` define que o **health check** será executado a cada 10 segundos.  
- O parâmetro `retries: 3` especifica que, após **três falhas consecutivas**, o container será marcado como "unhealthy".  
- O comando `curl --fail http://localhost/health || exit 1` verifica a saúde do container tentando acessar a URL `http://localhost/health`.  
  - Se o comando `curl` retornar uma falha (código de saída diferente de 0), será considerado uma falha no **health check**.  

### Importância do **health check**:
- **Health checks** são usados para monitorar a saúde de um container em execução.  
- Caso o container seja marcado como "unhealthy", ele pode ser reiniciado automaticamente dependendo das configurações de orquestradores como Docker Swarm ou Kubernetes.  

Portanto, a configuração está de acordo com a descrição na solução, e a afirmação está correta.

---

### Questão nº 10  
Você criou um novo serviço chamado `http` e descobriu que ele não está sendo registrado como saudável. Este comando permitirá visualizar a lista de tarefas históricas para este serviço?  
**Solução:** `docker ps http`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker ps http` **não** é o comando correto para visualizar a lista de tarefas históricas de um serviço no Docker.  

### Detalhes:
- O comando `docker ps` é usado para listar **containers ativos**, e não exibe informações sobre tarefas ou serviços.  
- Para visualizar a lista de tarefas, incluindo o histórico, de um serviço no Docker Swarm, você deve usar o comando:  
  ```bash
  docker service ps http
  ```  
  Este comando exibe as tarefas associadas ao serviço `http`, incluindo tarefas em execução e aquelas que já foram concluídas ou falharam.  

Portanto, para solucionar o problema e investigar por que o serviço `http` não está sendo registrado como saudável, você deve utilizar o comando correto (`docker service ps http`). O comando `docker ps http` não é válido para essa finalidade.

---

### Questão nº 11  
Esta é uma maneira de configurar o Docker Engine para usar um registro sem um certificado TLS confiável?  
**Solução:** Passar a flag `--insecure-registry` para o daemon em tempo de execução.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, essa é uma maneira válida de configurar o Docker Engine para usar um registro sem um certificado TLS confiável.  

### Detalhes:
- A flag `--insecure-registry` permite que o Docker Engine se conecte a um registro que não possui um certificado TLS confiável.  
- Essa configuração deve ser usada com cautela, pois registros inseguros podem expor dados sensíveis e criar vulnerabilidades de segurança.  

### Exemplo de uso:
Adicione a flag ao iniciar o daemon:  
```bash
dockerd --insecure-registry <ENDEREÇO_DO_REGISTRO>
```  

Ou configure no arquivo de configuração do Docker (`/etc/docker/daemon.json`):  
```json
{
  "insecure-registries": ["<ENDEREÇO_DO_REGISTRO>"]
}
```  
Após alterar o arquivo de configuração, reinicie o daemon do Docker para aplicar as alterações.  

Essa abordagem é válida, mas recomenda-se evitar registros inseguros em ambientes de produção, sempre que possível.

---

### Questão nº 12  
Esta configuração alcançará tolerância a falhas para os gerentes em um Swarm?  
**Solução:** Apenas dois gerentes, um ativo e um passivo.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A configuração de apenas dois gerentes (um ativo e um passivo) **não** alcança tolerância a falhas em um Swarm.  

### Por quê?  
- Em um Swarm, a tolerância a falhas para os gerentes depende da formação de um **quórum**.  
- O **quórum** é o número mínimo de gerentes que deve estar disponível para manter o estado consistente do Swarm e tomar decisões.  
- O quórum é calculado como:  
  ```  
  (N / 2) + 1  
  ```  
  Onde **N** é o número total de gerentes no Swarm.

### Problemas com apenas 2 gerentes:  
1. Para 2 gerentes, o quórum necessário também é **2**.  
2. Se **um dos gerentes falhar**, o Swarm perde o quórum e não pode processar solicitações ou agendar tarefas.  
3. Com 2 gerentes, não há tolerância a falhas, pois qualquer falha resulta em indisponibilidade.

### Configuração recomendada:  
- Para alcançar tolerância a falhas, deve-se usar um número **ímpar** de gerentes, preferencialmente pelo menos **3**.  
- Isso permite que o Swarm tolere a perda de até `(N - 1) / 2` gerentes.  
  - Exemplo:  
    - Com 3 gerentes, o quórum é **2**, e o Swarm pode tolerar a falha de 1 gerente.  
    - Com 5 gerentes, o quórum é **3**, e o Swarm pode tolerar a falha de 2 gerentes.  

### Resumo:
Para tolerância a falhas, o Swarm deve ter pelo menos 3 gerentes (e um número ímpar é recomendado). Portanto, a configuração com apenas dois gerentes **não** alcança esse objetivo.

---

### Questão nº 13
Esta funcionalidade do kernel Linux limita o acesso de um container Docker aos recursos do host, como CPU ou memória?  
**Solução:** **capabilities**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
As **capabilities** são um recurso do kernel Linux que permite que processos executem certas operações privilegiadas sem ter acesso total aos privilégios de superusuário (root).  

### Uso no Docker:
- O Docker utiliza as **capabilities** para limitar o acesso dos containers aos recursos do host, como CPU e memória.  
- Por padrão, o Docker adota uma abordagem de **whitelist**, removendo todas as **capabilities** que não são necessárias para o funcionamento básico do container.  

### Benefícios:
1. **Redução de risco:**  
   - Restringir as **capabilities** diminui o risco de comprometimento do sistema host ou de outros containers.  
2. **Controle granular:**  
   - Você pode ajustar as **capabilities** usando as opções `--cap-add` ou `--cap-drop` no comando `docker run` para adicionar ou remover permissões específicas, dependendo das necessidades do container.  

### Exemplo de uso:
```bash
docker run --cap-drop NET_RAW --cap-add SYS_TIME ubuntu
```
- Remove a capacidade `NET_RAW` (manipular pacotes brutos de rede).  
- Adiciona a capacidade `SYS_TIME` (ajustar o relógio do sistema).  

Portanto, as **capabilities** são uma funcionalidade importante para controlar o acesso dos containers Docker aos recursos do host e melhorar a segurança.

---

### Questão nº 14
Um dos vários containers em um pod foi marcado como não saudável após falhar repetidamente em seu **livenessProbe**. Esta é a ação tomada pelo orquestrador para corrigir o container não saudável?  
**Solução:** O container não saudável é reiniciado.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Um **livenessProbe** é uma forma de verificar a saúde interna de um aplicativo para o plano de controle do Kubernetes.  

### Comportamento do **livenessProbe**:
- Quando o **livenessProbe** falha, o Kubernetes entende que o container está em um estado não saudável e tenta restaurar o serviço para um estado operacional.
- A ação padrão do Kubernetes é **reiniciar o container** que falhou no **livenessProbe**.  

### Importância:
- Essa funcionalidade é essencial para manter a alta disponibilidade dos aplicativos, pois permite que containers problemáticos sejam reiniciados automaticamente sem intervenção manual.  

Portanto, a ação tomada pelo orquestrador para corrigir o container não saudável é **reiniciá-lo**, tornando a afirmação correta.

---

### Questão nº 15  
Isso é uma vantagem de **multi-stage builds**?  
**Solução:** Cria e marca simultaneamente várias imagens.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Os **multi-stage builds** no Docker são projetados para simplificar o processo de construção de imagens ao permitir que você use várias etapas em um único Dockerfile.  

### Objetivo principal:
- Eles ajudam a criar imagens menores e mais otimizadas, eliminando arquivos ou dependências desnecessárias que são usadas apenas durante a construção.  
- Com isso, apenas o conteúdo essencial é copiado para a imagem final.  

### O que **multi-stage builds** não fazem:
- **Multi-stage builds** não têm como objetivo criar e marcar múltiplas imagens simultaneamente.  
- O processo de criação e marcação de várias imagens geralmente requer comandos separados ou scripts adicionais para gerenciar múltiplas imagens.

### Vantagens reais dos **multi-stage builds**:
1. **Redução do tamanho da imagem final**: Inclui apenas os artefatos necessários na última etapa.  
2. **Simplificação de builds complexos**: Usa múltiplas etapas para compilar, testar e construir o aplicativo em um único Dockerfile.  
3. **Reutilização de etapas intermediárias**: Permite reutilizar as etapas anteriores no mesmo Dockerfile.

Portanto, a criação e marcação simultânea de várias imagens **não** é uma vantagem dos **multi-stage builds**.

---

### Questão nº 16  
Um **PersistentVolumeClaim (PVC)** foi criado com a especificação `storageClass: ""` e requisitos de tamanho que não podem ser atendidos por nenhum **PersistentVolume (PV)** existente.  
Esta é uma ação que o Kubernetes toma nessa situação?  
**Solução:** O PVC permanece não vinculado até que um **PersistentVolume** que atenda a todos os requisitos do PVC esteja disponível.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Um **PersistentVolumeClaim (PVC)** é uma solicitação de armazenamento feita por um usuário, similar a como os Pods consomem recursos de nós.  

### Detalhes do Comportamento:
1. **Sem StorageClass** (`storageClass: ""`):  
   - Isso indica que o PVC não exige uma **StorageClass** específica e pode ser vinculado a qualquer PV que atenda aos requisitos de tamanho e modo de acesso.  

2. **Sem PV compatível disponível:**  
   - Se nenhum **PersistentVolume** existente atende aos requisitos (tamanho, modo de acesso ou outras restrições), o PVC permanece no estado **"unbound"** (não vinculado).  

3. **Como o PVC pode ser resolvido?**  
   - Um administrador pode **provisionar manualmente** um PV que atenda aos requisitos do PVC.  
   - Alternativamente, se uma configuração de **dynamic provisioning** estiver habilitada, o cluster pode criar um PV dinamicamente.  

### Resumo:
Quando um PV compatível não está disponível, o PVC permanecerá não vinculado até que um volume adequado seja provisionado manual ou dinamicamente. Portanto, a ação descrita é correta.

---

### Questão nº 17  
Um usuário tenta definir a hora do sistema de dentro de um container Docker, mas não tem sucesso. Isso poderia estar bloqueando essa operação?  
**Solução:** **Linux capabilities**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, **Linux capabilities** podem estar bloqueando essa operação.  

### Detalhes:
1. **Linux capabilities** são usadas para restringir os privilégios de processos ou containers no Docker.  
2. Por padrão, os containers Docker possuem um conjunto reduzido de **capabilities** para melhorar a segurança.  
3. Alterar a hora do sistema requer a capacidade `SYS_TIME`, que **não está incluída** no conjunto padrão de capacidades atribuídas aos containers.  

### Como permitir que um container altere a hora do sistema:
Para que o container consiga definir a hora do sistema, você deve adicionar a capacidade `SYS_TIME` ao iniciá-lo.  

#### Exemplo:
```bash
docker run --cap-add SYS_TIME alpine date -s 12:00
```
- Aqui, o comando adiciona a capacidade `SYS_TIME` ao container e permite que ele altere a hora do sistema para `12:00`.  

### Resumo:
A falta da capacidade `SYS_TIME` é o motivo pelo qual a tentativa de alterar a hora do sistema dentro do container falha. Adicionar essa capacidade desbloqueia a operação. Portanto, **Linux capabilities** estão, de fato, bloqueando a operação.

---

### Questão nº 18  
Este recurso do kernel Linux limita o acesso de um container Docker aos recursos do host, como CPU ou memória?  
**Solução:** **cgroups**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, os **cgroups** (control groups) são um recurso do kernel Linux usado para gerenciar e limitar o uso de recursos do sistema, como CPU, memória, E/S de disco e largura de banda de rede, entre processos.  

### Uso no Docker:
- O Docker utiliza **cgroups** para isolar os containers e restringir os recursos que eles podem consumir no host.  
- Cada container é executado dentro de um grupo de controle separado, garantindo que o consumo de recursos por um container não interfira nos outros.  

### Exemplos de limitações usando **cgroups**:
1. **Limitar uso de CPU**:
   ```bash
   docker run --cpus="2" ubuntu
   ```
   - Limita o container a usar até 2 CPUs.  

2. **Limitar uso de memória**:
   ```bash
   docker run --memory="512m" ubuntu
   ```
   - Restringe o uso de memória do container a 512 MB.  

### Benefícios dos **cgroups**:
- **Prevenção de esgotamento de recursos**: Garante que nenhum container consuma todos os recursos do host.  
- **Isolamento**: Cada container tem sua própria cota de recursos, garantindo operações mais previsíveis.  

Portanto, os **cgroups** são fundamentais para limitar e gerenciar os recursos disponíveis para containers Docker. A afirmação está correta.

---

### Questão nº 19  
Este comando exibe todos os pods no cluster que estão etiquetados como `env: development`?  
**Solução:** `kubectl get pods -I env=development`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `kubectl get pods -I env=development` **não** exibirá os pods com o rótulo `env: development` porque a flag `-I` não é uma opção válida no **kubectl**.  

### Comando correto:  
Para filtrar pods pelo rótulo `env: development`, deve-se usar a flag `--selector` ou sua abreviação `-l`.  
Os comandos corretos seriam:  
```bash
kubectl get pods --selector env=development
```
ou  
```bash
kubectl get pods -l env=development
```

### Detalhes:
- A opção `--selector` ou `-l` permite especificar rótulos para filtrar recursos do Kubernetes.  
- O comando correto exibirá apenas os pods que possuem o rótulo `env: development`.

Portanto, o comando fornecido na solução é inválido, e a resposta é **B**.

### Questão nº 20  
Você adiciona um novo usuário à organização "engineering" no DTR.  
Essa ação concede a eles acesso de leitura/escrita ao repositório "engineering/api"?  
**Solução:** Adicioná-los a um time na organização "engineering" que possui acesso de leitura/escrita ao repositório "engineering/api".  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
No **Docker Trusted Registry (DTR)**, adicionar um usuário a uma organização **não concede automaticamente acesso aos repositórios** dessa organização.  

### Detalhes:  
- O DTR organiza permissões usando **organizações** e **times**.  
- Mesmo que o usuário esteja na organização "engineering", ele só terá acesso aos repositórios se for explicitamente atribuído a um time com as permissões adequadas para o repositório em questão.  

### Ação necessária:
Para conceder acesso ao repositório "engineering/api", você deve:  
1. Criar ou usar um **time existente** dentro da organização "engineering" que possua permissões de leitura/escrita para o repositório "engineering/api".  
2. Adicionar o usuário a esse time específico.  

Sem a associação ao time com as permissões corretas, o usuário não terá acesso ao repositório, mesmo estando na organização.  

Portanto, apenas adicionar o usuário à organização **não é suficiente** para garantir acesso ao repositório, e a resposta é **B**.

---

### Questão nº 21 
Você adiciona um novo usuário à organização "engineering" no DTR.  
Essa ação concede a eles acesso de leitura/escrita ao repositório "engineering/api"?  
**Solução:** Espelhar o repositório "engineering/api" para um dos repositórios privados do próprio usuário.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Espelhar o repositório "engineering/api" para um repositório privado do usuário **não concede acesso de leitura/escrita ao repositório original**.  

### Detalhes sobre o espelhamento (mirroring):
- O **espelhamento** é uma funcionalidade do Docker Trusted Registry (DTR) que permite sincronizar automaticamente imagens de um repositório para outro.  
- Ele pode ser usado dentro do mesmo DTR ou entre diferentes DTRs.  
- **Permissões:** O espelhamento **não altera as permissões ou os papéis** dos usuários nos repositórios de origem ou destino.  

### Como conceder acesso:
Para conceder acesso de leitura/escrita ao repositório original "engineering/api", é necessário:  
1. Adicionar o usuário a um time com a função apropriada para o repositório.  
2. Configurar as permissões de visibilidade e acesso diretamente no repositório, se necessário.  

Espelhar o repositório apenas cria uma cópia sincronizada, mas não modifica quem pode acessar ou modificar o repositório original.  

Portanto, a resposta correta é **B**.

---

### Questão nº 22
Este comando exibe uma lista de volumes para um container específico?  
**Solução:** `docker container logs nginx –volumes`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker container logs` é usado para exibir os logs de um container, que normalmente consistem na saída padrão (**stdout**) e erro padrão (**stderr**) do processo principal em execução no container.  

### Problemas com o comando fornecido:
1. **Flag inválida:**  
   - A flag `–volumes` **não é válida** para o comando `docker container logs`. Usá-la resultará em uma mensagem de erro.  

2. **Comando correto para listar volumes:**  
   Para exibir os volumes de um container, você pode usar o comando `docker inspect` com um filtro para os volumes montados:  
   ```bash
   docker inspect -f '{{ .Mounts }}' nginx
   ```  
   Este comando exibirá informações como:  
   - Fonte (source)  
   - Destino (destination)  
   - Tipo (type)  
   - Modo de montagem (mode)  
   - Propagação (propagation)  

### Resumo:
O comando `docker container logs nginx –volumes` **não** exibe uma lista de volumes para o container `nginx`. Para isso, o comando correto seria `docker inspect` com o uso de um filtro apropriado. Portanto, a resposta é **B**.


### Questão nº 23
Durante o desenvolvimento de uma aplicação destinada a ser orquestrada pelo Kubernetes, você deseja montar o diretório `/data` do seu laptop em um container.  
Esta estratégia alcançará com sucesso esse objetivo?  
**Solução:** Criar um **PersistentVolume** com `storageClass: ""` e `hostPath: /data`, e um **PersistentVolumeClaim** solicitando este PV. Em seguida, usar o PVC para popular um volume em um pod.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A estratégia fornecida não funcionará porque o volume **hostPath** tem limitações significativas:  

### Limitações do **hostPath**:
1. **Adequado apenas para testes locais em um único nó:**  
   - Um volume **hostPath** depende de diretórios no sistema de arquivos do nó específico onde o pod está sendo executado.  
   - Ele **não é portátil** entre diferentes nós de um cluster.  

2. **Sem provisionamento dinâmico:**  
   - Volumes **hostPath** não suportam provisionamento dinâmico e precisam ser configurados manualmente.  

### Alternativas:
Para montar um diretório local do seu laptop em um container Kubernetes, considere as seguintes opções:  

1. **Minikube**:  
   - Se estiver usando Minikube, você pode montar diretórios locais diretamente usando a flag `--mount` ao iniciar o cluster:  
     ```bash
     minikube start --mount --mount-string="/data:/data"
     ```

2. **NFS (Network File System):**  
   - Configure um volume NFS que permita montar diretórios locais do seu laptop no cluster.  

3. **hostPath CSI (Container Storage Interface):**  
   - Use um driver **hostPath CSI**, que fornece suporte mais robusto para cenários baseados em `hostPath`.  

4. **Transferência de arquivos:**  
   - Copie os arquivos do seu laptop para o container usando o comando `kubectl cp`:  
     ```bash
     kubectl cp /data <pod-name>:/data
     ```

### Resumo:
Embora o **hostPath** seja útil para testes locais simples, ele não é adequado para montar o diretório `/data` do seu laptop em um container de maneira confiável. A estratégia fornecida não funciona, portanto a resposta é **B**.

---

### Questão nº 24
Você configura um Docker Engine local para reforçar a **content trust** definindo a variável de ambiente `DOCKER_CONTENT_TRUST=1`.  
Se `myorg/myimage:1.0` não for assinado, o Docker bloqueará este comando?  
**Solução:** `docker container run myorg/myimage:1.0`  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Quando a variável de ambiente `DOCKER_CONTENT_TRUST=1` está habilitada, o Docker ativa o recurso **Docker Content Trust (DCT)**, que verifica a integridade e o editor das imagens Docker usando assinaturas digitais.  

### Comportamento do Docker Content Trust:
1. **Imagens não assinadas ou com assinaturas inválidas:**  
   - O Docker **bloqueará** a execução (`docker container run`), o pull (`docker pull`) ou a construção (`docker build`) de imagens não assinadas.  
   - Será exibida uma mensagem de erro indicando que a imagem não possui uma assinatura válida.  

2. **Solução para imagens não assinadas:**  
   - **Desativar DCT temporariamente:**  
     Você pode definir `DOCKER_CONTENT_TRUST=0` ou usar a flag `--disable-content-trust` no comando:  
     ```bash
     docker container run --disable-content-trust myorg/myimage:1.0
     ```
   - **Assinar a imagem:**  
     Utilize uma chave válida para assinar a imagem antes de usá-la com DCT habilitado.  

### Resumo:
Com `DOCKER_CONTENT_TRUST=1` ativado, o Docker **bloqueará** o comando `docker container run` para a imagem `myorg/myimage:1.0` se ela não estiver assinada. Portanto, a resposta é **A**.

---

### **Questão nº 25**  
No contexto de um cluster em modo Swarm, isso descreve um nó?  
**Solução:** Uma instância do Docker Engine participando do Swarm.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
No contexto de um cluster Docker em modo Swarm, uma instância do Docker Engine que participa do Swarm é de fato chamada de **nó**.  

### Detalhes:
1. **Tipos de nós:**  
   - **Nó Gerenciador (Manager):**  
     - Responsável pela orquestração e gerenciamento do Swarm.  
     - Lida com tarefas como distribuição de serviços, manutenção do estado desejado e reconciliação do cluster.  
   - **Nó Trabalhador (Worker):**  
     - Executa as tarefas atribuídas pelo nó gerenciador.  

2. **Participação:**  
   - Um nó pode ingressar ou sair do Swarm a qualquer momento.  
   - O gerenciador reconcilia o estado desejado do cluster com base nos nós disponíveis.  

### Resumo:
Uma instância do Docker Engine participando de um Swarm é corretamente descrita como um **nó**. A definição está correta, portanto a resposta é **A**.

---

### Questão nº 26  
No Docker Trusted Registry (DTR), é assim que um usuário pode evitar que uma imagem, como `nginx:latest`, seja sobrescrita por outro usuário com acesso de push ao repositório?  
**Solução:** Marcar a imagem com o tag `nginx:immutable`.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Marcar a imagem com o tag `nginx:immutable` **não impede** que outro usuário com acesso de push sobrescreva a tag `nginx:latest`. Isso apenas cria um novo tag para a imagem (`nginx:immutable`), mas não protege o tag original de ser modificado.  

### Como impedir sobrescritas no DTR:
1. **Marcar o tag como imutável (immutable):**  
   - No DTR, um usuário pode configurar tags específicas como imutáveis através da interface web (DTR web UI) ou da API.  
   - Quando um tag é configurado como imutável, ninguém pode sobrescrevê-lo, mesmo que tenha acesso de push ao repositório.

2. **Interface Web:**  
   - Acesse o DTR.  
   - Navegue até o repositório desejado.  
   - Marque o tag como imutável na seção de configurações.  

3. **API do DTR:**  
   - Use a API para configurar o tag como imutável programaticamente.  

### Resumo:
Criar um novo tag (`nginx:immutable`) não impede modificações na tag original (`nginx:latest`). Para evitar sobrescritas, o tag deve ser configurado explicitamente como imutável no DTR. A resposta correta é **B**.

---

### Questão nº 27  
Isso descreve o papel dos Control Groups (**cgroups**) quando usados com um container Docker?  
**Solução:** Contabilização e limitação de recursos.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Os **Control Groups (cgroups)** são uma funcionalidade do kernel Linux usada para gerenciar e limitar o uso de recursos do sistema, como CPU, memória, disco e rede, por processos ou containers.  

### Funções dos **cgroups** com Docker:
1. **Contabilização de recursos (accounting):**  
   - Monitora o uso de recursos do container, garantindo visibilidade sobre o consumo de CPU, memória, E/S de disco, etc.  

2. **Limitação de recursos (limiting):**  
   - Impõe limites sobre os recursos que um container pode consumir. Por exemplo:  
     - Limitar o uso de memória:  
       ```bash
       docker run --memory="512m" ubuntu
       ```  
     - Restringir o uso de CPU:  
       ```bash
       docker run --cpus="1.5" ubuntu
       ```  

3. **Prevenção de esgotamento de recursos:**  
   - Garante que nenhum container consuma mais recursos do que o alocado, prevenindo a fome de recursos para outros containers ou para o host.

4. **Compartilhamento de recursos:**  
   - Permite que o Docker compartilhe de forma justa os recursos disponíveis entre os containers em execução.  

### Resumo:
Os **cgroups** são essenciais para a contabilização e limitação de recursos de containers no Docker, garantindo eficiência e isolamento. A descrição está correta, e a resposta é **A**.

---

### Questão nº 28  
O YAML do Kubernetes mostrado abaixo descreve uma **NetworkPolicy**:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: dca
  namespace: default
spec:
  podSelector:
    matchLabels:
      tier: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          tier: api
```

Esta **NetworkPolicy** bloqueará esse tráfego?  
**Solução:** Uma solicitação originada de um pod com o rótulo `tier: backend` para um pod com o rótulo `tier: frontend`.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
A **NetworkPolicy** especificada define regras de entrada (**ingress**) para pods com o rótulo `tier: backend`. Por padrão, as **NetworkPolicies** no Kubernetes seguem a abordagem **deny-all** para qualquer tráfego que não esteja explicitamente permitido.  

### Detalhes da NetworkPolicy:
1. **`podSelector`:**
   - Aplica esta política apenas aos pods com o rótulo `tier: backend`.  
2. **Ingress Rules:**  
   - Permite tráfego **apenas** de pods com o rótulo `tier: api`.  
   - Qualquer tráfego de outros pods, incluindo aqueles com o rótulo `tier: frontend`, será **bloqueado por padrão**.  

### Tráfego descrito:
- Uma solicitação originada de um pod com o rótulo `tier: backend` para um pod com o rótulo `tier: frontend` **não** está permitida pelas regras da NetworkPolicy.  
- Como resultado, esse tráfego será **bloqueado**.  

### Resumo:
A NetworkPolicy impede o tráfego de `tier: backend` para `tier: frontend`, pois nenhuma regra de permissão cobre esse caso. A resposta correta é **A**.

---

### Questão nº 29  
Este comando monta o diretório `/data` do host no container Ubuntu em modo somente leitura?  
**Solução:** `docker run --volume /data:/mydata:ro ubuntu`  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, o comando `docker run --volume /data:/mydata:ro ubuntu` monta o diretório `/data` do host no caminho `/mydata` do container em modo **somente leitura**.  

### Detalhamento do comando:
1. **Opção `--volume` ou `-v`:**  
   - Permite montar um diretório ou arquivo do host no container como um volume.  
   - A sintaxe é:  
     ```  
     -v|--volume=[host-src:]container-dest[:<options>]  
     ```  
     - **`host-src`:** Caminho absoluto no host (no caso, `/data`).  
     - **`container-dest`:** Caminho absoluto dentro do container (no caso, `/mydata`).  
     - **`options`:** Opções de montagem, como:  
       - `ro`: Somente leitura.  
       - `rw`: Leitura e escrita (padrão).  
       - `z` ou `Z`: Para ajustar etiquetas SELinux.  

2. **Modo somente leitura (`ro`):**  
   - O uso da opção `:ro` impede que o container modifique os arquivos no diretório montado.  
   - Nesse exemplo, o container pode acessar e ler o conteúdo de `/data` no host, mas **não pode gravar ou modificar os arquivos**.  

### Aplicações:
Montagens somente leitura são úteis para compartilhar:  
- Arquivos de configuração.  
- Dados sensíveis que não devem ser alterados pelo container.  

Portanto, o comando descrito funciona como esperado, e a resposta correta é **A**.

---

### Questão nº 30  
Uma imagem de aplicação é executada em múltiplos ambientes, com cada ambiente utilizando certificados e portas diferentes.  
Esta é uma maneira de provisionar configuração para containers em tempo de execução?  
**Solução:** Provisionar um objeto de configuração Docker (**Docker config**) para cada ambiente.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, provisionar um objeto de configuração Docker (**Docker config**) para cada ambiente é uma maneira eficaz de fornecer configurações para containers em tempo de execução.  

### Sobre Docker Configs:
1. **Finalidade:**  
   - Os **Docker configs** permitem que você forneça dados de configuração (como certificados, arquivos de configuração, etc.) para containers sem a necessidade de reconstruir a imagem Docker.  

2. **Como funciona:**  
   - Um **Docker config** é montado como um arquivo dentro do sistema de arquivos do container.  
   - O acesso a um config deve ser explicitamente concedido por meio do atributo `configs` no nível de serviço em um arquivo de configuração Docker Compose ou Swarm.  

3. **Vantagens:**  
   - **Separação de responsabilidades:** Mantém a imagem Docker genérica, enquanto os dados específicos do ambiente são gerenciados separadamente.  
   - **Facilidade de atualização:** Alterar configurações não exige a recriação ou redistribuição da imagem.  

4. **Exemplo de uso com Docker Swarm:**  
   - Criar um config para cada ambiente:  
     ```bash
     docker config create prod_cert prod_cert.pem
     docker config create dev_cert dev_cert.pem
     ```  
   - Especificar o config no arquivo de serviço:  
     ```yaml
     services:
       app:
         image: myapp:latest
         configs:
           - source: prod_cert
             target: /etc/certs/cert.pem
     ```  

### Resumo:
Provisionar objetos de configuração Docker é uma abordagem recomendada para gerenciar configurações específicas de ambiente em containers, permitindo flexibilidade em tempo de execução. A resposta correta é **A**.

---

### Questão nº 31  
Essa é uma função do **Universal Control Plane (UCP)**?  
**Solução:** Escanear imagens para detectar vulnerabilidades de segurança.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, escanear imagens para detectar vulnerabilidades de segurança é uma função do **Universal Control Plane (UCP)** quando integrado ao **Docker Trusted Registry (DTR)**.  

### Detalhes:
1. **UCP e DTR:**  
   - O **UCP** é a plataforma de gerenciamento e orquestração de contêineres para o Docker Enterprise.  
   - Ele se integra ao **DTR**, que é uma solução segura e escalável para armazenamento de imagens.  

2. **Escaneamento de imagens no DTR:**  
   - O **DTR** possui um recurso embutido para escanear imagens, verificando cada camada em busca de vulnerabilidades conhecidas.  
   - Os resultados do escaneamento são exibidos diretamente na interface web do **UCP**, permitindo que os usuários identifiquem e corrijam problemas de segurança antes de implantar suas aplicações.  

3. **Políticas de segurança no UCP:**  
   - O **UCP** permite configurar políticas de segurança que restringem a execução de aplicações apenas para imagens que foram escaneadas e estão livres de vulnerabilidades.  

### Resumo:
O escaneamento de imagens para detectar vulnerabilidades de segurança é uma funcionalidade do UCP em conjunto com o DTR. A resposta correta é **A**.

---

### Questão nº 32  
Este é um namespace do kernel Linux que está desativado por padrão e deve ser habilitado em tempo de execução no Docker Engine para ser usado?  
**Solução:** **user**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O namespace **user** é um namespace do kernel Linux desativado por padrão no Docker Engine e precisa ser explicitamente habilitado para ser usado.  

### Sobre o namespace **user**:
1. **Função:**  
   - Permite mapear os **UIDs** (User IDs) e **GIDs** (Group IDs) do sistema host para diferentes UIDs e GIDs dentro dos containers.  
   - Isso melhora a segurança, isolando os espaços de IDs de usuários e grupos entre o host e os containers.

2. **Por que está desativado por padrão?**  
   - Requer configuração adicional e não é necessário para todos os casos de uso do Docker.  

3. **Como habilitar:**  
   - O **user namespace** pode ser habilitado iniciando o Docker Daemon com a flag `--userns-remap`.  
     - Exemplo:  
       ```bash
       dockerd --userns-remap=default
       ```
   - A configuração também pode ser feita em `/etc/docker/daemon.json`:
     ```json
     {
       "userns-remap": "default"
     }
     ```

4. **Requisitos adicionais:**  
   - Arquivos `/etc/subuid` e `/etc/subgid` devem estar configurados para especificar os intervalos de mapeamento de UIDs/GIDs para os containers.  

### Resumo:
O namespace **user** é desativado por padrão no Docker Engine e precisa ser habilitado manualmente para ser usado. A resposta correta é **A**.

---

### Questão nº 33  
Você deseja criar um container que seja acessível a partir da rede do host.  
Essa ação alcança esse objetivo?  
**Solução:** Usar o comando `network attach` para acessar o container na rede bridge.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
A rede **bridge** do Docker permite que containers conectados à mesma rede bridge se comuniquem entre si, enquanto permanecem isolados de containers em outras redes.  

### Detalhes:
1. **Rede Bridge Padrão:**  
   - A rede bridge padrão do Docker conecta containers à rede do host e os torna acessíveis por IP.  
   - Containers na rede bridge podem ser acessados pelo host através do IP do container.  

2. **Comando `network attach`:**  
   - Conectar um container à rede bridge com o comando `network attach` torna o container acessível a partir da rede do host.  

3. **Limitações da Rede Bridge Padrão:**  
   - Os containers podem se comunicar por IP, mas não por nome, a menos que a opção `--link` seja usada (considerada obsoleta).  
   - Para melhor isolamento e suporte a resolução automática de DNS entre containers, recomenda-se usar redes **bridge definidas pelo usuário**.  

### Resumo:
O comando `network attach` pode conectar um container à rede bridge, tornando-o acessível a partir da rede do host. A resposta correta é **A**.

---

### Questão nº 34  
Você criou um novo serviço chamado `http` e descobriu que ele não está sendo registrado como saudável. Este comando permitirá que você visualize a lista de tarefas históricas para este serviço?  
**Solução:** `docker service ps http`  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O comando `docker service ps http` exibe a lista de tarefas relacionadas ao serviço `http`, incluindo o histórico das tarefas.  

### Detalhes do comando:
1. **Tarefas listadas:**  
   - Mostra tarefas em execução, concluídas, rejeitadas, falhas e outros estados históricos.  
   - Inclui informações úteis, como estado atual, mensagem de erro (se houver), portas e outros detalhes relacionados às tarefas do serviço.  

2. **Utilidade para depuração:**  
   - Se o serviço não está registrando como saudável, o histórico de tarefas ajuda a identificar possíveis problemas, como:
     - Falha na criação de containers.  
     - Configurações incorretas de rede ou volumes.  
     - Problemas de inicialização no container.

### Resumo:
O comando `docker service ps http` é a ferramenta correta para visualizar o histórico de tarefas do serviço `http` e diagnosticar por que ele não está saudável. A resposta correta é **A**.

---

### Questão nº 35 
No Docker Trusted Registry (DTR), é assim que um usuário pode evitar que uma imagem, como `nginx:latest`, seja sobrescrita por outro usuário com acesso de push ao repositório?  
**Solução:** Manter uma cópia de backup da imagem em outro repositório.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Manter uma cópia de backup da imagem em outro repositório **não impede** que a imagem original seja sobrescrita por outro usuário com acesso de push.  

### Detalhes:
1. **Por que o backup não resolve?**  
   - Um backup em outro repositório apenas permite restaurar a imagem se ela for sobrescrita, mas não impede que a tag `nginx:latest` seja modificada no repositório original.  
   - Além disso, o backup pode não estar sincronizado ou acessível em todos os momentos.  

2. **Solução correta:**  
   - No DTR, você pode configurar a tag como **imutável** (immutable) usando a interface web ou a API.  
   - Uma tag imutável **impede qualquer tentativa de sobrescrever a imagem**, mesmo de usuários com permissões de push.  

3. **Como configurar a imutabilidade no DTR:**  
   - Acesse o DTR pela interface web.  
   - Navegue até o repositório desejado.  
   - Configure a tag específica como imutável nas configurações do repositório.  

### Resumo:
Manter um backup em outro repositório não evita que a tag `nginx:latest` seja sobrescrita. Para proteger a tag, é necessário configurá-la como imutável no DTR. A resposta correta é **B**.

---

### Questão nº 36 
Você está solucionando problemas em um deployment Kubernetes chamado `api` e deseja ver a tabela de eventos para este objeto. Este comando exibe os eventos?  
**Solução:** `kubectl events deployment api`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `kubectl events deployment api` **não é válido** no Kubernetes.  

### Comandos corretos para exibir eventos:
1. **Filtrar eventos por nome do objeto:**
   ```bash
   kubectl get events --field-selector involvedObject.name=api
   ```
   - Esse comando usa um **field selector** para filtrar os eventos associados ao deployment `api`.  

2. **Exibir eventos detalhados com `kubectl describe`:**
   ```bash
   kubectl describe deployment api
   ```
   - Além dos eventos, este comando exibe informações detalhadas sobre o deployment, incluindo status, réplicas e condições.  

### Resumo:
O comando fornecido não é válido para exibir eventos. Use `kubectl get events` com um **field selector** ou `kubectl describe deployment api`. A resposta correta é **B**.

--- 

### Questão nº 37  
Este é um tipo de namespace do kernel Linux que fornece isolamento para containers?  
**Solução:** **Process ID**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O **namespace de Process ID (PID)** é um dos tipos de namespace do kernel Linux que fornece isolamento para containers. Ele permite que cada container tenha seu próprio espaço de identificação de processos, garantindo que os IDs de processo sejam independentes entre os namespaces.  

### Detalhes sobre o namespace de PID:
1. **Função:**  
   - Isola o espaço de identificação de processos.  
   - Permite que processos em diferentes namespaces tenham o mesmo PID sem interferir uns nos outros.  

2. **Benefícios:**  
   - Cada container pode ter seu próprio processo `init` com PID 1, que atua como o processo ancestral dentro do namespace.  
   - Melhora o isolamento ao separar identificadores como:  
     - PID (Process ID).  
     - PPID (Parent Process ID).  
     - TID (Thread ID).  

3. **Importância em containers:**  
   - Garante que cada container funcione como uma instância independente, sem impacto nos processos do host ou de outros containers.  

### Resumo:
O namespace de Process ID (PID) é essencial para o isolamento de containers, permitindo que cada um tenha seu próprio espaço de processos. A resposta correta é **A**.

### Questão nº 38  
Essa afirmação está correta?  
**Solução:** Um Dockerfile armazena as opções de configuração do Docker daemon.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A afirmação está incorreta. Um **Dockerfile** não armazena as opções de configuração do Docker daemon.  

### Função do Dockerfile:
- O **Dockerfile** é um arquivo de texto que contém comandos para construir imagens Docker.  
- Ele define como uma imagem será montada, incluindo instruções como:  
  - `FROM` (imagem base).  
  - `RUN` (executa comandos durante a construção da imagem).  
  - `CMD` (define o comando padrão a ser executado pelo container).  

### Configuração do Docker Daemon:
- As opções de configuração do Docker daemon são armazenadas em um arquivo JSON, geralmente localizado em:  
  - **Linux:** `/etc/docker/daemon.json`  
  - **Windows:** `C:\ProgramData\docker\config\daemon.json`  
- Este arquivo permite personalizar o comportamento do daemon, como:  
  - Habilitar o modo debug.  
  - Configurar certificados TLS.  
  - Alterar o diretório de dados do Docker.  

### Resumo:
O Dockerfile é usado para construir imagens, enquanto a configuração do Docker daemon é armazenada em `daemon.json`. A resposta correta é **B**.

---

### Questão nº 39  
Você configura um Docker Engine local para reforçar o **content trust** definindo a variável de ambiente `DOCKER_CONTENT_TRUST=1`.  
Se `myorg/myimage:1.0` não for assinada, o Docker bloqueará este comando?  
**Solução:** `docker service create myorg/myimage:1.0`  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Quando o **Docker Content Trust (DCT)** está habilitado (`DOCKER_CONTENT_TRUST=1`), o Docker bloqueia qualquer operação com imagens que não sejam assinadas ou que tenham assinaturas inválidas.  

### Comportamento do Docker Content Trust:
1. **Verificação de assinaturas:**  
   - O DCT verifica a integridade e o editor de tags específicas de imagens usando assinaturas digitais armazenadas em um servidor Notary.  
   - Se uma imagem não for assinada, ou se a assinatura não puder ser verificada, o Docker bloqueará as operações que envolvem essa imagem.  

2. **Impacto no comando `docker service create`:**  
   - O comando `docker service create` tenta usar a imagem `myorg/myimage:1.0`.  
   - Como a imagem está **sem assinatura**, o Docker exibirá uma mensagem de erro e recusará a execução do comando.  

3. **Como permitir imagens não assinadas:**  
   - Temporariamente desabilitar o DCT configurando `DOCKER_CONTENT_TRUST=0`.  
   - Usar a flag `--disable-content-trust` no comando:  
     ```bash
     docker service create --disable-content-trust myorg/myimage:1.0
     ```  

### Resumo:
Com o DCT habilitado, o Docker bloqueará o comando `docker service create` se a imagem `myorg/myimage:1.0` não for assinada. A resposta correta é **A**.

---

### Questão nº 40  
Duas equipes de desenvolvimento na sua organização utilizam Kubernetes e desejam implantar suas aplicações enquanto garantem que recursos específicos do Kubernetes, como secrets, sejam agrupados para cada aplicação.  
Essa é uma maneira de alcançar esse objetivo?  
**Solução:** Criar um namespace para cada aplicação e adicionar todos os recursos a ele.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Criar um namespace para cada aplicação é uma abordagem recomendada no Kubernetes para organizar e isolar recursos dentro de um cluster.  

### Benefícios de usar namespaces:
1. **Agrupamento lógico:**  
   - Recursos como pods, services, secrets, configmaps e volumes podem ser organizados e agrupados por aplicação, facilitando a gestão.  

2. **Isolamento de recursos:**  
   - Cada namespace funciona como um cluster virtual dentro do cluster físico, isolando os recursos de diferentes aplicações ou equipes.  

3. **Gerenciamento de políticas e acesso:**  
   - Permite aplicar políticas de autorização (RBAC) e configurações específicas, como limites de recursos (Resource Quotas), para cada namespace.  

4. **Escopo de nomes:**  
   - Recursos com o mesmo nome podem coexistir em diferentes namespaces. Por exemplo, dois secrets chamados `db-credentials` podem existir em namespaces distintos sem conflitos.  

### Exemplo:
- Para a aplicação `app1`, você pode criar o namespace e adicionar seus recursos:  
   ```bash
   kubectl create namespace app1
   kubectl apply -f deployment.yaml -n app1
   kubectl apply -f service.yaml -n app1
   ```  

### Resumo:
Criar um namespace para cada aplicação é uma maneira eficaz de organizar, isolar e gerenciar recursos no Kubernetes. A resposta correta é **A**.

---

### Questão nº 41  
Este recurso do kernel Linux limita o acesso de um container Docker aos recursos do host, como CPU ou memória?  
**Solução:** **seccomp**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O **seccomp** é um recurso do kernel Linux que permite restringir as ações disponíveis dentro de um container, controlando quais chamadas de sistema (**syscalls**) podem ser feitas.  

### Como o seccomp limita o acesso aos recursos do host:
1. **Filtragem de chamadas de sistema:**  
   - O **seccomp** pode bloquear chamadas de sistema relacionadas ao gerenciamento de recursos do host, como:  
     - `setpriority`: Ajusta a prioridade do processo.  
     - `sched_setaffinity`: Define em quais CPUs o processo pode ser executado.  
     - `mlock`: Trava partes da memória na RAM.  

2. **Perfil padrão do Docker:**  
   - O Docker usa um perfil **seccomp** padrão que bloqueia chamadas de sistema consideradas perigosas, como `mount`, `reboot` e `ptrace`.  
   - Esse perfil melhora a segurança e o isolamento dos containers.  

3. **Perfis personalizados:**  
   - É possível aplicar um perfil **seccomp** personalizado para ajustar as permissões de syscalls de um container:  
     ```bash
     docker run --security-opt seccomp=/path/to/seccomp-profile.json ubuntu
     ```

### Resumo:
Embora o **seccomp** seja mais focado em restringir chamadas de sistema, ele pode limitar indiretamente o acesso a recursos como CPU e memória ao bloquear chamadas que os manipulam. A resposta correta é **A**.

### Questão nº 42  
Este conjunto de comandos pode identificar as portas publicadas para um container?  
**Solução:** `docker port inspect`, `docker container inspect`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker port inspect` **não existe** no Docker, tornando o conjunto fornecido inválido para identificar as portas publicadas de um container.  

### Comandos corretos para identificar portas publicadas:
1. **`docker port`:**  
   - Exibe os mapeamentos de portas do container.  
   - Exemplos:
     ```bash
     docker port <container_name>
     ```
     Saída (por exemplo, para um container chamado `web`):  
     ```
     80/tcp -> 0.0.0.0:8080
     ```

     Para uma porta específica:
     ```bash
     docker port <container_name> 80
     ```
     Saída:  
     ```
     0.0.0.0:8080
     ```

2. **`docker container inspect`:**  
   - Mostra informações detalhadas sobre o container, incluindo os mapeamentos de portas.
   - Para exibir apenas as portas publicadas em formato JSON:
     ```bash
     docker container inspect --format='{{ .NetworkSettings.Ports }}' <container_name>
     ```
     Saída:  
     ```
     map[80/tcp:[map[HostIp:0.0.0.0 HostPort:8080]]]
     ```

### Resumo:
O comando `docker port inspect` é inválido. Para identificar portas publicadas, use `docker port` ou `docker container inspect` com um filtro apropriado. A resposta correta é **B**.

---

### Questão nº 43  
Durante o desenvolvimento de uma aplicação destinada a ser orquestrada pelo Kubernetes, você deseja montar o diretório `/data` do seu laptop em um container.  
Essa estratégia alcança esse objetivo?  
**Solução:** Configurar `containers.Mounts.hostBinding: /data` na especificação do container.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A estratégia fornecida é inválida porque `containers.Mounts.hostBinding: /data` **não é uma sintaxe válida** na especificação de containers no Kubernetes.  

### Forma correta de montar um diretório do host no Kubernetes:
1. **Usar um volume do tipo `hostPath`:**  
   - O volume **hostPath** permite montar um diretório do host no sistema de arquivos do container.  

2. **Exemplo de YAML correto:**  
   Para montar o diretório `/data` do host no caminho `/var/data` dentro do container:  
   ```yaml
   spec:
     containers:
     - name: my-container
       image: my-image
       volumeMounts:
       - name: data-volume
         mountPath: /var/data
     volumes:
     - name: data-volume
       hostPath:
         path: /data
   ```

3. **Limitações do `hostPath`:**  
   - Adequado para testes locais ou clusters de nó único.  
   - Não é ideal para ambientes de produção, pois os volumes **hostPath** não são portáveis entre diferentes nós do cluster.  

4. **Alternativas:**  
   - Para cenários mais robustos, considere usar sistemas de arquivos distribuídos, como **NFS**, ou volumes gerenciados por **StorageClasses**.  

### Resumo:
A sintaxe proposta (`containers.Mounts.hostBinding`) é inválida. O método correto para montar o diretório `/data` do host em um container no Kubernetes é usar um volume `hostPath`. A resposta correta é **B**.

---

### Questão nº 44  
Esta afirmação está correta?  
**Solução:** Um Dockerfile armazena dados persistentes entre implantações de um container.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A afirmação está incorreta. Um **Dockerfile** não armazena dados persistentes entre implantações de um container.  

### Função do Dockerfile:
- Um **Dockerfile** é um documento de texto contendo instruções para construir uma imagem Docker.  
- Ele define a configuração do container, como camadas, dependências e comandos padrão.  

### Por que o Dockerfile não armazena dados persistentes?
1. **Docker images:**  
   - As imagens criadas a partir do Dockerfile são **somente leitura**.  
2. **Containers:**  
   - Containers são instâncias efêmeras das imagens e suas alterações no sistema de arquivos são perdidas quando o container é removido.  

### Como armazenar dados persistentes:
1. **Volumes:**  
   - Gerenciados pelo Docker e armazenados em uma localização independente do ciclo de vida do container.  
   - Exemplo:  
     ```bash
     docker run -v my_volume:/data my_image
     ```

2. **Bind mounts:**  
   - Conectam arquivos ou diretórios do sistema host ao sistema de arquivos do container.  
   - Exemplo:  
     ```bash
     docker run -v /host/data:/container/data my_image
     ```

### Resumo:
Para persistência de dados entre implantações, é necessário usar **volumes** ou **bind mounts**, e não um Dockerfile. A resposta correta é **B**.

---

### Questão nº 45  
Duas equipes de desenvolvimento na sua organização utilizam Kubernetes e desejam implantar suas aplicações enquanto garantem que recursos específicos do Kubernetes, como secrets, sejam agrupados para cada aplicação.  
Essa é uma maneira de alcançar esse objetivo?  
**Solução:** Adicionar todos os recursos ao namespace **default**.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Adicionar todos os recursos ao namespace **default** **não** é uma maneira adequada de alcançar o agrupamento e isolamento dos recursos para cada aplicação.  

### Por que o namespace **default** não é adequado:
1. **Falta de isolamento:**  
   - Recursos de ambas as aplicações seriam misturados no namespace **default**, dificultando a organização e o gerenciamento.  
2. **Políticas de segurança:**  
   - No namespace **default**, não há uma separação clara que permita aplicar políticas específicas de autorização e acesso para cada aplicação.  

### Solução correta:
1. **Usar namespaces separados:**  
   - Crie um namespace exclusivo para cada aplicação, o que garante que recursos como secrets, configmaps e serviços sejam isolados.  
   - Isso permite que as equipes gerenciem seus próprios recursos sem interferência de outras equipes ou aplicações.  

2. **Benefícios dos namespaces:**  
   - **Organização:** Agrupam recursos logicamente.  
   - **Isolamento:** Permitem que recursos sejam isolados entre aplicações ou equipes.  
   - **Autorização e políticas:** Facilitam a aplicação de políticas de acesso específicas por namespace.  

3. **Exemplo de configuração:**  
   - Para a aplicação `app1`:  
     ```bash
     kubectl create namespace app1
     kubectl apply -f deployment.yaml -n app1
     kubectl apply -f secret.yaml -n app1
     ```  

### Resumo:
Adicionar recursos ao namespace **default** não garante isolamento nem organização para as aplicações. A solução correta é usar namespaces separados para cada aplicação. A resposta correta é **B**.

---

### Questão nº 46  
A política de segurança de uma empresa especifica que containers de desenvolvimento e produção devem ser executados em nós separados em um cluster Swarm.  
Isso pode ser usado para agendar containers e atender aos requisitos da política de segurança?  
**Solução:** **Node affinities**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
**Node affinities** são uma funcionalidade que fornece controle granular sobre onde os containers são agendados em um cluster Swarm.  

### Como as **Node affinities** atendem à política:
1. **Separação lógica de nós:**  
   - É possível rotular os nós do cluster com rótulos específicos, como `env=production` e `env=development`.  
   - Esses rótulos ajudam a garantir que os containers sejam programados apenas em nós apropriados.  

2. **Configuração no Docker Swarm:**  
   - Use a opção `--constraint` ao criar um serviço para especificar as preferências de agendamento com base nos rótulos dos nós.  
   - Exemplo:
     - Para containers de produção:  
       ```bash
       docker service create --name prod-service --constraint 'node.labels.env == production' my-image
       ```
     - Para containers de desenvolvimento:  
       ```bash
       docker service create --name dev-service --constraint 'node.labels.env == development' my-image
       ```

3. **Benefícios das Node affinities:**  
   - Garantem que os containers sejam agendados em nós apropriados, atendendo aos requisitos de políticas de segurança e organização.  
   - Oferecem flexibilidade para gerenciar onde os serviços são executados em ambientes compartilhados.  

### Resumo:
As **Node affinities** podem ser usadas para garantir que containers de desenvolvimento e produção sejam executados em nós separados, conforme exigido pela política de segurança. A resposta correta é **A**.

---

### Questão nº 47  
Você criou um novo serviço chamado `http` e descobriu que ele não está sendo registrado como saudável. Este comando permitirá que você veja a lista de tarefas históricas para este serviço?  
**Solução:** `docker inspect http`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker inspect http` **não** permitirá que você veja a lista de tarefas históricas para o serviço.  

### Detalhes sobre o comando `docker inspect`:
- O comando `docker inspect` retorna informações de baixo nível sobre objetos Docker, como containers, imagens, redes ou volumes.  
- **Não funciona em serviços**, que são objetos de nível mais alto, definidos para especificar o estado desejado de um conjunto de tarefas.  

### Comando correto:
Para visualizar o histórico das tarefas de um serviço, você deve usar o comando `docker service ps`, que exibe os estados atuais e anteriores de cada tarefa, juntamente com o nó, erros e portas.  

Exemplo:  
```bash
docker service ps http
```  

### Resumo:
O comando `docker inspect` não fornece informações sobre as tarefas de um serviço. Para ver o histórico das tarefas de um serviço, o comando correto é `docker service ps`. A resposta correta é **B**.

---

### Questão nº 48  
Uma imagem de aplicação é executada em múltiplos ambientes, com cada ambiente utilizando certificados e portas diferentes.  
Essa é uma maneira de fornecer configuração para containers em tempo de execução?  
**Solução:** Criar um Dockerfile para cada ambiente, especificando portas e variáveis de ambiente para os certificados.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Embora criar um Dockerfile para cada ambiente seja uma solução possível, **não é a mais eficiente nem escalável** para fornecer configuração para containers em tempo de execução.  

### Alternativas mais eficientes:
1. **Variáveis de ambiente:**  
   - Permite que você defina diferentes configurações de ambiente para cada execução do container sem precisar criar múltiplos Dockerfiles.  
   - Exemplo:
     ```bash
     docker run -e ENV=production -e CERT_PATH=/path/to/cert myapp
     ```

2. **Docker Secrets (para dados sensíveis):**  
   - Uma maneira segura de gerenciar informações sensíveis como certificados, chaves e senhas, sem a necessidade de incluí-los no Dockerfile ou no código.  

3. **Argumentos de linha de comando:**  
   - Passar parâmetros diretamente para o container no momento da execução, permitindo flexibilidade na configuração.  

4. **Arquivos de configuração montados como volumes:**  
   - Uma abordagem muito usada para injetar arquivos de configuração diretamente nos containers, facilitando a gestão de ambientes sem alterar o Dockerfile.

### Por que criar Dockerfiles separados não é recomendado:
- Manter vários Dockerfiles para diferentes ambientes pode resultar em **inconsistências** entre as versões da imagem e aumentar a complexidade do gerenciamento.  
- A prática recomendada é usar uma **única imagem Docker** e injetar as configurações específicas para cada ambiente no momento da execução, promovendo **imutabilidade** e **consistência**.

### Resumo:
Criar um Dockerfile separado para cada ambiente não é a abordagem mais eficiente ou escalável. O uso de variáveis de ambiente, Docker Secrets e volumes é uma prática mais adequada. A resposta correta é **B**.

---

### Questão nº 49  
A política de segurança de uma empresa especifica que containers de desenvolvimento e produção devem ser executados em nós separados em um cluster Swarm.  
Isso pode ser usado para agendar containers e atender aos requisitos da política de segurança?  
**Solução:** **Label constraints**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
**Label constraints** podem ser usadas para agendar containers de acordo com os requisitos da política de segurança.  

### Como as **Label constraints** funcionam:
1. **Rótulos (Labels):**  
   - Labels são pares de chave-valor que podem ser atribuídos a nós no Swarm para identificar seu propósito ou uso.  
   - Por exemplo, você pode rotular nós como `development` ou `production` para indicar seu uso específico.  

2. **Uso de `--constraint`:**  
   - A opção `--constraint` permite que você defina em quais nós um serviço pode ser executado, com base nos labels atribuídos aos nós.
   - Para garantir que um serviço de desenvolvimento seja executado somente em nós de desenvolvimento, você pode usar o seguinte comando:
     ```bash
     docker service create --constraint 'node.labels.environment == development' ...
     ```
   - Para garantir que um serviço de produção seja executado somente em nós de produção, você pode usar:
     ```bash
     docker service create --constraint 'node.labels.environment == production' ...
     ```

### Vantagens:
- **Isolamento de ambientes:** Garante que containers de desenvolvimento e produção sejam executados em nós separados, atendendo aos requisitos da política de segurança da empresa.  
- **Flexibilidade:** O uso de labels e constraints oferece uma maneira flexível e escalável de organizar os containers em um Swarm.

### Resumo:
As **label constraints** são uma solução eficaz para garantir que containers de desenvolvimento e produção sejam agendados em nós separados no cluster Swarm, conforme exigido pela política de segurança. A resposta correta é **A**.

### Questão nº 50  
Este comando cria um serviço Swarm que escuta apenas na porta 53 usando o protocolo UDP?  
**Solução:** `docker service create --name dns-cache -p 53:53 --constraint networking.protocol.udp=true dns-cache`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando fornecido contém vários erros de sintaxe e opções inválidas, o que impede a criação de um serviço Swarm que escute apenas na porta 53 usando o protocolo UDP.  

### Erros no comando:
1. **`-name`**:  
   - A opção correta para definir o nome do serviço é `--name`, não `-name`.  

2. **`-constraint`**:  
   - A opção `-constraint` não é válida para o comando `docker service create`. Essa opção é usada em contextos diferentes, como em `docker node` para definir restrições sobre onde os serviços podem ser executados.  

3. **Sintaxe incorreta para `--publish`**:  
   - A opção `-p` deve ser substituída por `--publish`, e o protocolo deve ser especificado corretamente, como `protocol=udp`.

### Comando correto:
Para criar um serviço Swarm que escute apenas na porta 53 usando o protocolo UDP, você deve usar a seguinte sintaxe:

```bash
docker service create --name dns-cache \
--publish published=53,target=53,protocol=udp,mode=host \
--mode global \
--network host \
dns-cache
```

### Detalhes sobre o comando:
- **`--publish published=53,target=53,protocol=udp,mode=host`**: Mapeia a porta 53 do container para a porta 53 do host, especificando o protocolo UDP e utilizando o modo `host` para bypassar o roteamento do Swarm.
- **`--mode global`**: Define que o serviço será executado em todos os nós do Swarm.
- **`--network host`**: Usa a rede do host para o serviço.

### Resumo:
O comando fornecido está incorreto, pois contém erros de sintaxe e opções inválidas. O comando correto para criar o serviço desejado deve ser usado conforme descrito acima. A resposta correta é **B**.

---

### Questão nº 51  
O YAML do Kubernetes mostrado abaixo descreve uma **NetworkPolicy**:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: dca
  namespace: default
spec:
  podSelector:
    matchLabels:
      tier: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          tier: api
```

Esta **NetworkPolicy** bloqueará esse tráfego?  
**Solução:** Uma solicitação originada de um pod com o rótulo `tier: api` para um pod com o rótulo `tier: backend`.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A **NetworkPolicy** fornecida permite explicitamente o tráfego **de pods com o rótulo `tier: api` para pods com o rótulo `tier: backend`**. Portanto, a solicitação **não será bloqueada** pela política.  

### Detalhes da NetworkPolicy:
1. **`podSelector`:**  
   - A política se aplica aos pods com o rótulo `tier: backend` no namespace `default`.  
2. **Regra de **Ingress**:**  
   - Permite tráfego de **pods com o rótulo `tier: api`** para os pods com o rótulo `tier: backend`.

### Resumo:
Como a **NetworkPolicy** explicitamente permite tráfego de pods com o rótulo `tier: api`, o tráfego de um pod com esse rótulo para um pod com o rótulo `tier: backend` **não será bloqueado**. A resposta correta é **B**.

---

### Questão nº 52  
Durante o desenvolvimento de uma aplicação destinada a ser orquestrada pelo Kubernetes, você deseja montar o diretório `/data` do seu laptop em um container.  
Essa estratégia alcança esse objetivo?  
**Solução:** Adicionar um volume ao pod que define `hostPath.path: /data`, e então montar esse volume nos containers do pod conforme necessário.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A solução proposta não funcionará como esperado, porque um **volume do tipo hostPath** monta um arquivo ou diretório do sistema de arquivos do **nó host** (onde o pod está sendo executado), não do laptop ou máquina local de onde os comandos `kubectl` são executados.  

### Detalhes:
1. **O que é o `hostPath`?**  
   - Um **volume do tipo `hostPath`** permite que você monte arquivos ou diretórios do sistema de arquivos do nó onde o pod está sendo agendado, **não do seu laptop**.  
   - O diretório `/data` no seu laptop **não será acessível** ao pod, a menos que esteja presente no nó **onde o pod será executado**.

2. **Alternativa melhor:**  
   - Uma solução mais adequada seria usar um **PersistentVolume** (PV), que pode ser acessado por qualquer nó do cluster, como um volume NFS, **AWS EBS** ou **Azure Disk**, que são opções que permitem persistência de dados e podem ser montadas em qualquer nó no cluster.

### Resumo:
Para que o Kubernetes acesse um diretório do seu laptop, você precisaria garantir que esse diretório esteja disponível no **nó** onde o pod está sendo executado, ou utilizar um **PersistentVolume**. Portanto, a resposta correta é **B**.

---

### Questão nº 53  
Este recurso do kernel Linux limita o acesso de um container Docker aos recursos do host, como CPU ou memória?  
**Solução:** **cgroups**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, o Docker utiliza os **cgroups**, um recurso do kernel Linux, para limitar os recursos que um container pode usar, incluindo CPU e memória.  

### Como os **cgroups** funcionam no Docker:
1. **Limitação de CPU:**  
   - O Docker pode limitar o uso de CPU de um container para o equivalente a um único núcleo de CPU no sistema host.  
   - Exemplo:  
     ```bash
     docker run --cpus="1" ubuntu
     ```

2. **Limitação de memória:**  
   - O Docker também pode limitar o uso de memória de um container para evitar que ele consuma todos os recursos do sistema.  
   - Exemplo:  
     ```bash
     docker run --memory="512m" ubuntu
     ```

3. **Requisitos para cgroups:**  
   - Para usar essas funcionalidades, o **host Docker** deve ter o controle de memória e swap habilitado no cgroup.  

### Resumo:
Os **cgroups** são uma ferramenta do kernel Linux que permite ao Docker limitar recursos como CPU e memória para containers, garantindo melhor controle e isolamento dos recursos do host. A resposta correta é **A**.

---

### Questão nº 54  
Este comando monta o diretório `/data1` do host no container Ubuntu em modo somente leitura?  
**Solução:** `docker run -v /data:/mydata -mode readonly ubuntu`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando fornecido contém vários erros que impedem o funcionamento correto.  

### Erros no comando:
1. **Diretório do host**:  
   - O diretório do host especificado na questão é `/data1`, mas o comando utiliza `/data`, o que não corresponde à solicitação.
   
2. **Flag `-mode` incorreta**:  
   - A flag correta é `--mode`, não `-mode`. Além disso, deve ser colocada antes do nome da imagem no comando.
   
3. **Valor do modo**:  
   - O valor correto para o modo de somente leitura é **`ro`** (não `readonly`).

### Comando correto:
O comando correto para montar o diretório `/data1` do host em modo somente leitura no container seria:

```bash
docker run -v /data1:/mydata --mode ro ubuntu
```

Este comando montará o diretório `/data1` do host no caminho `/mydata` dentro do container **em modo somente leitura**.  

### Resumo:
O comando fornecido está incorreto devido a erros de sintaxe e parâmetros inválidos. O comando correto deve ser usado conforme descrito acima. A resposta correta é **B**.

---

### Questão nº 55  
O YAML do Kubernetes mostrado abaixo descreve um serviço **ClusterIP**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: dca
spec:
  type: clusterIP
  selector:
      app: nginx
  ports:
  - port: 8080
    targetPort: 80
  - port: 4443
    targetPort: 443
```

Esta é uma afirmação correta sobre como esse serviço roteia as requisições?  
**Solução:** O tráfego enviado para o IP deste serviço na porta 8080 será roteado para a porta 80 em um pod aleatório com o rótulo `app: nginx`.

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
A afirmação está correta. No YAML do Kubernetes fornecido, o tráfego enviado para o IP deste serviço na porta **8080** será roteado para a porta **80** em um pod aleatório com o rótulo `app: nginx`.  

### Detalhes:
1. **Tipo de Serviço `ClusterIP`:**  
   - O **ClusterIP** é o tipo de serviço padrão do Kubernetes, usado para comunicação interna dentro do cluster. Ele fornece um IP interno acessível somente dentro do cluster.
   
2. **Seletores:**  
   - O **selector** `app: nginx` garante que o tráfego seja roteado para pods com esse rótulo.

3. **Portas e Roteamento:**
   - A definição de **`port: 8080` e `targetPort: 80`** indica que o tráfego chegando à porta 8080 do IP do serviço será encaminhado para a porta 80 de um pod que tenha o rótulo `app: nginx`.  
   - O Kubernetes usa **balanceamento de carga interno** para escolher aleatoriamente um dos pods que correspondem ao seletor `app: nginx`.

### Resumo:
A descrição do roteamento do tráfego está correta e atende à funcionalidade esperada de um serviço **ClusterIP**. A resposta correta é **A**.

---


### Questão nº 56  
Você deseja criar um container que seja acessível a partir da rede do host.  
Essa ação alcança esse objetivo?  
**Solução:** Usar **EXPOSE** ou **-publish** para acessar o container na rede bridge.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Usar **EXPOSE** ou **-publish** para acessar o container na rede **bridge** **não** alcançará o objetivo de tornar o container acessível a partir da rede do host.  

### Detalhes:
1. **`EXPOSE`:**  
   - O comando `EXPOSE` apenas documenta as portas nas quais o container está ouvindo, mas **não abre essas portas para o host**.  
   - Ele é útil como uma forma de documentação, mas não tem efeito na acessibilidade do container a partir da rede do host.

2. **`-publish` ou `-p`:**  
   - O comando `-publish` ou `-p` mapeia uma porta do host para uma porta do container, mas isso não muda o **modo de rede** do container.  
   - Isso permite que o container seja acessado a partir da rede externa, mas ele **não compartilha a pilha de rede com o host**, o que pode causar problemas de acessibilidade.

3. **Modo de rede `host`:**  
   - Para criar um container acessível diretamente a partir da rede do host, é necessário usar a opção `--network host`.  
   - Exemplo:
     ```bash
     docker run --network host my-container
     ```
   - Isso faz com que o container compartilhe a pilha de rede do host e tenha o **mesmo IP do host**, tornando-o acessível a partir da rede do host.

### Resumo:
Usar **EXPOSE** ou **-publish** não tornará o container acessível diretamente a partir da rede do host. Para isso, é necessário usar o modo de rede `host`. A resposta correta é **B**.

---

### Questão nº 57  
Este comando garantirá que o tráfego overlay entre as tarefas do serviço seja criptografado?  
**Solução:** `docker service create --network --secure`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker service create --network --secure` **não garantirá** que o tráfego overlay entre as tarefas do serviço seja criptografado. Isso ocorre porque a opção `--secure` **não é válida** para o comando `docker service create`.  

### Como garantir a criptografia do tráfego overlay:
1. **Criação de uma rede overlay criptografada:**
   - Para garantir que o tráfego entre as tarefas do serviço seja criptografado, é necessário usar a opção `--opt encrypted` ao criar uma rede overlay com o comando `docker network create`.  
   - Exemplo de comando para criar uma rede overlay criptografada chamada `my-net`:
     ```bash
     docker network create --driver overlay --opt encrypted my-net
     ```

2. **Associando a rede ao serviço:**
   - Depois de criar a rede overlay criptografada, você pode associá-la ao serviço usando o comando `docker service create`.  
   - Exemplo de comando para criar um serviço `my-service` usando a rede `my-net`:
     ```bash
     docker service create --name my-service --network my-net nginx
     ```

### Resumo:
A opção `--secure` não é válida para garantir a criptografia do tráfego overlay. Para isso, deve-se usar a opção `--opt encrypted` ao criar a rede overlay. A resposta correta é **B**.

---

### Questão nº 58  
O seguinte arquivo Docker Compose é implantado como uma stack:

```yaml
version: '3.1'
services:
  app:
    image: app:1.0
    healthcheck:
      test: "curl --fail http://localhost/health || exit 1"
      interval: 10s
      timeout: 5s
      retries: 3
```

Esta afirmação está correta sobre a definição do health check?  
**Solução:** Os health checks testam a saúde do app a cada cinco segundos. Se o teste falhar, o container será reiniciado três vezes antes de ser reprogramado.

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A afirmação está incorreta. Com base no arquivo Docker Compose fornecido, o health check não está configurado para testar a saúde do container a cada cinco segundos, mas sim a cada **dez segundos** (definido pela opção `interval: 10s`).

### Detalhes sobre o health check:
1. **Intervalo entre os testes (`interval`):**
   - O health check é executado **a cada 10 segundos** (não 5 segundos). O valor `interval: 10s` especifica esse intervalo.

2. **Reinicializações (`retries`):**
   - Se o health check falhar, o Docker tentará **3 vezes** (definido por `retries: 3`) antes de considerar o container **não saudável** e possivelmente reiniciá-lo.

3. **Outras configurações:**
   - **`test`**: Comando para verificar a saúde do container, usando o `curl` para acessar a URL `http://localhost/health`.
   - **`timeout`**: Tempo máximo de espera para cada tentativa de health check, que é de **5 segundos** (definido por `timeout: 5s`).

### Resumo:
O health check está configurado para testar a saúde do app a cada **10 segundos** e não 5, como afirmado. A resposta correta é **B**.

---

### Questão nº 59  
Este é um namespace do kernel Linux que está desativado por padrão e precisa ser habilitado em tempo de execução do Docker para ser utilizado?  
**Solução:** **pid**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O **pid namespace** não está desativado por padrão e não precisa ser habilitado em tempo de execução do Docker. O **pid namespace** é um dos seis namespaces que são habilitados por padrão quando um container é iniciado no Docker.  

### O que é o **pid namespace**:
1. **Isolamento de IDs de processo (PID):**  
   - O **pid namespace** isola o espaço de IDs de processo, permitindo que processos em diferentes namespaces possam ter o mesmo PID.
   - Isso é útil para garantir que os containers possam ter seus próprios processos `init` com PID 1, isolando-os do sistema do host e de outros containers.

2. **Visibilidade de processos:**  
   - Os processos dentro de um container podem ter a mesma identificação de PID que processos fora dele, mas a visibilidade entre os containers e o host é limitada.  
   
3. **Desativando o pid namespace:**  
   - Para desabilitar o pid namespace, você pode usar a opção `--pid` ao rodar um container, especificando o modo do pid, como `host` ou `container`.  
   - Exemplo:
     ```bash
     docker run --pid=host mycontainer
     ```

### Resumo:
O **pid namespace** está **habilitado por padrão** no Docker, portanto, a resposta correta é **B**.

---

### Questão nº 60 
Você deseja criar um container que seja acessível a partir da rede do host. Essa ação alcança esse objetivo?  
**Solução:** Usar **EXPOSE** ou **--publish** para acessar os containers na rede bridge.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Embora **EXPOSE** e **--publish** sejam usados para gerenciar a visibilidade de portas de containers, eles **não tornam um container acessível diretamente pela rede do host**, se o container estiver usando a rede **bridge**.

### Diferença entre **EXPOSE** e **--publish**:
1. **`EXPOSE`:**
   - O comando `EXPOSE` é usado apenas como **documentação** para indicar que um container escuta em uma determinada porta. **Não abre a porta** para o host ou para a rede externa.  
   - Exemplo:  
     ```Dockerfile
     EXPOSE 8080
     ```

2. **`--publish` ou `-p`:**
   - O comando **`--publish`** é usado para mapear portas do host para portas do container, permitindo que o host acesse as portas do container.  
   - Exemplo:
     ```bash
     docker run -p 8080:80 my-container
     ```
   - **No entanto**, mesmo com o mapeamento de portas, o container **não compartilha a pilha de rede** com o host e ainda usa a rede **bridge**. Para o container ser diretamente acessível pela rede do host, você deve usar o modo de rede **host**.

### Para criar um container acessível diretamente pela rede do host:
- Use a opção **`--network host`** para fazer o container compartilhar a pilha de rede com o host.
  ```bash
  docker run --network host my-container
  ```

### Resumo:
Embora **EXPOSE** e **--publish** sejam usados para gerenciar a acessibilidade de portas, **eles não tornam o container acessível diretamente pela rede do host** se o container estiver usando a rede bridge. Para isso, é necessário usar **`--network host`**. A resposta correta é **B**.

---

### Questão nº 61  
Essa sequência de etapas exclui completamente uma imagem do disco no Docker Trusted Registry?  
**Solução:** Excluir a imagem e remover as permissões para o repositório no Docker Trusted Registry.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A sequência de passos descrita **não excluirá completamente** a imagem do disco no Docker Trusted Registry. Excluir a imagem e remover as permissões apenas remove a imagem da interface do usuário do registro e impede o acesso não autorizado a ela. No entanto, os dados da imagem **ainda permanecem no backend de armazenamento** do registro até que a coleta de lixo (garbage collection) seja realizada.  

### Como a coleta de lixo funciona:
1. **Coleta de lixo (Garbage Collection):**
   - A coleta de lixo é um processo que **remove blobs não utilizados** (camadas) do armazenamento do registro. Isso é necessário para garantir que os dados das imagens excluídas sejam realmente removidos do armazenamento.
   
2. **Execução manual:**
   - Para rodar a coleta de lixo manualmente, o registro deve ser interrompido e o seguinte comando deve ser executado:
     ```bash
     bin/registry garbage-collect /etc/docker/registry/config.yml
     ```

3. **Execução automática:**
   - Alternativamente, o registro pode ser configurado para rodar a coleta de lixo automaticamente em intervalos regulares.

### Resumo:
A sequência de deletação mencionada não exclui os dados da imagem do disco até que a coleta de lixo seja executada. A resposta correta é **B**.

---

### Questão nº 62  
Sua organização possui uma solução de log centralizado, como o Splunk.  
Esse comando configura um container Docker para exportar logs para a solução de logging?  
**Solução:** `docker logs <container-id>`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker logs <container-id>` **não configura** um container Docker para exportar logs para uma solução de logging, como o Splunk. Esse comando apenas exibe os logs de um container específico na saída padrão ou em um arquivo, mas não os envia para sistemas externos.

### Como exportar logs de containers para o Splunk:
1. **Docker Logger Drivers:**
   - O Docker possui **drivers de log** que permitem que os logs dos containers sejam enviados para soluções externas, como o Splunk.  
   - O **Splunk logging driver** envia os logs do container para o **HTTP Event Collector** no Splunk Enterprise ou no Splunk Cloud.

2. **Usando o driver de log do Splunk:**
   - Para configurar o Docker para exportar logs para o Splunk, você deve usar as flags `--log-driver` e `--log-opt` ao executar o container.  
   - Exemplo de comando para configurar o driver de log para o Splunk:
     ```bash
     docker run --log-driver=splunk --log-opt splunk-token=VALUE --log-opt splunk-url=VALUE ...
     ```

### Resumo:
O comando `docker logs <container-id>` exibe logs localmente, mas não exporta para soluções como o Splunk. Para isso, é necessário configurar o **Splunk logging driver**. A resposta correta é **B**.

---

### Questão nº 63  
Qual flag do `docker run` remove as limitações de cgroup?  
**A.** `docker run --privileged`  
**B.** `docker run --cpu-period`  
**C.** `docker run --isolation`  
**D.** `docker run --cap-drop`  

**Resposta: A**  

**Explicação:**  
A flag `--privileged` do `docker run` **remove todas as limitações de cgroup** para um container, além de outras restrições de segurança impostas pelo daemon do Docker. Ela concede ao container acesso total aos dispositivos, recursos e capacidades do host, como se o container estivesse sendo executado diretamente no host.

### Detalhes:
1. **`--privileged`**:
   - O uso dessa flag permite que o container tenha permissões elevadas e acesso completo ao sistema, o que pode ser necessário para casos específicos, como rodar **Docker-in-Docker** ou realizar **debugging de sistema**.
   - **Riscos de segurança**: Usar a flag `--privileged` pode representar um risco, pois expõe o host a potenciais ataques ou danos pelo container. Por isso, deve ser usada com cautela, apenas quando absolutamente necessário e com imagens e containers confiáveis.

2. **Outras opções:**
   - **`--cpu-period`**: Define o período do **Completely Fair Scheduler** (CFS) para o container, limitando a alocação de CPU, mas **não remove limitações de cgroup**.
   - **`--isolation`**: Define a tecnologia de isolamento para containers, mas é **usado apenas em containers Windows**, e **não afeta as limitações de cgroup**.
   - **`--cap-drop`**: Remove **capacidades Linux** de um container, o que ajuda a reduzir a superfície de ataque, mas também **não afeta as limitações de cgroup**.

### Resumo:
A flag `--privileged` remove as limitações de cgroup e concede privilégios elevados ao container. Portanto, a resposta correta é **A**.

---

### Questão nº 64  
Este comando exibe todos os pods no cluster que são rotulados como `env: development`?  
**Solução:** `kubectl gel pods --all-namespaces -l 'env in (development)'`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando fornecido `kubectl gel pods --all-namespaces -l 'env in (development)'` contém dois erros de digitação que impedem seu funcionamento correto:

1. **Erro no verbo:**  
   - O verbo correto é `get`, e não `gel`. O comando correto é `kubectl get pods`.

2. **Erro na flag do seletor de rótulos:**  
   - A flag correta para selecionar rótulos é `-l`, não `-I`. Portanto, o comando deve usar `-l` para especificar o seletor de rótulos.

### Comando correto:
O comando correto para listar todos os pods em todos os namespaces com o rótulo `env: development` seria:

```bash
kubectl get pods --all-namespaces -l 'env=development'
```

### Resumo:
A versão fornecida do comando contém erros de digitação. O comando correto deve usar `get` no lugar de `gel` e `-l` no lugar de `-I`. A resposta correta é **B**.

---

### Questão nº 65  
Esta é uma vantagem das **multi-stage builds**?  
**Solução:** Melhor cache ao construir imagens Docker.

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Melhorar o cache ao construir imagens Docker é, de fato, uma vantagem das **multi-stage builds**.  

### Como funciona o cache em **multi-stage builds**:
1. **Vários estágios de construção (FROM):**
   - Em uma **multi-stage build**, você pode usar múltiplas instruções `FROM` em um único Dockerfile, criando diferentes estágios para diferentes tarefas de construção. Cada estágio tem seu próprio **cache de camada**.
   
2. **Aproveitamento do cache:**
   - Se um estágio, como a instalação de dependências, não mudar, o Docker pode reutilizar o cache da camada desse estágio. Isso significa que você só precisa reconstruir os estágios subsequentes se as partes relevantes do Dockerfile mudarem.
   
3. **Exemplo prático:**
   - Se você tiver um estágio que instala dependências e outro que compila o código, o Docker pode **reutilizar o cache** do estágio de instalação de dependências se as dependências não mudarem, economizando tempo e largura de banda durante a construção e envio da imagem.

### Resumo:
O uso de **multi-stage builds** realmente melhora a eficiência do cache, pois permite que diferentes estágios usem seu próprio cache, acelerando o processo de construção. A resposta correta é **A**.

---

### Questão nº 66  
Um dos vários containers em um pod é marcado como não saudável após falhar várias vezes em seu **livenessProbe**. Esta é a ação tomada pelo orquestrador para corrigir o container não saudável?  
**Solução:** O controlador que gerencia o pod é **autoscalado** para excluir o pod não saudável e aliviar a carga.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A afirmação está incorreta. Quando um container falha repetidamente no **livenessProbe**, o orquestrador **não autoscalará de volta** nem excluirá o pod para aliviar a carga. Em vez disso, o **orquestrador recriará o pod**, mantendo o número desejado de réplicas e garantindo que o serviço continue funcionando corretamente.  

### O que acontece com a falha do **livenessProbe**:
1. **Função do livenessProbe:**  
   - O **livenessProbe** verifica se o container está saudável e funcionando corretamente. Se falhar, o Kubernetes reiniciará o container automaticamente para tentar restaurá-lo ao estado saudável.
   
2. **Ação do orquestrador:**  
   - O **orquestrador**, como o Kubernetes, cria um novo pod para substituir o pod não saudável, seja no mesmo nó ou em outro nó do cluster, dependendo da configuração e da disponibilidade de recursos.  
   - O objetivo não é excluir o pod e reduzir a disponibilidade, mas sim garantir que a quantidade de réplicas desejada seja mantida.

3. **Autoscaling não é a solução para falhas no livenessProbe:**  
   - O **autoscaling** normalmente se refere ao ajuste do número de réplicas do pod com base na carga ou utilização de recursos. No entanto, falhas no **livenessProbe** não são tratadas dessa maneira, já que o problema é resolvido pela recriação do pod.

### Resumo:
A ação correta tomada pelo orquestrador é recriar o pod, não excluir e aliviar a carga através de **autoscaling**. A resposta correta é **B**.

---

### Questão nº 67  
Você está solucionando problemas em um deployment Kubernetes chamado `api` e deseja ver a tabela de eventos para esse objeto.  
Este comando exibe a tabela de eventos?  
**Solução:** `kubectl get deployment api`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `kubectl get deployment api` **não exibe a tabela de eventos** para o objeto `deployment`. Ele mostra apenas o **status** do deployment, como o número de réplicas, pods disponíveis e pods atualizados. 

### Para exibir os eventos de um deployment:
1. **Comando correto:**
   - Use o comando `kubectl describe deployment api`, que exibe detalhes do deployment, incluindo os **eventos** relacionados à criação, escalonamento e atualização dos pods do deployment.
   
   Exemplo:
   ```bash
   kubectl describe deployment api
   ```

2. **Comando alternativo com filtro de eventos:**
   - Você também pode usar o comando `kubectl get events` com um filtro para exibir os eventos específicos do deployment:
   
   Exemplo:
   ```bash
   kubectl get events --field-selector involvedObject.name=api
   ```

### Resumo:
O comando `kubectl get deployment api` exibe informações sobre o status do deployment, mas não os eventos. Para ver os eventos, use `kubectl describe deployment api` ou `kubectl get events` com o filtro adequado. A resposta correta é **B**.

---

### Questão nº 68  
Você deseja criar um container que seja acessível a partir da rede do host.  
Essa ação alcança esse objetivo?  
**Solução:** Usar `network connect` para acessar o container na rede bridge.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Usar `network connect` para acessar o container na rede **bridge** **não** alcança o objetivo de torná-lo acessível a partir da rede do host. O comando `network connect` conecta um container a uma rede existente, mas **não expõe as portas do container ao host**.

### Detalhes sobre a rede **bridge**:
1. **Rede bridge:**  
   - A **rede bridge** é a rede padrão que o Docker cria para os containers, oferecendo isolamento entre o container e o host.  
   - Containers na rede bridge não são acessíveis diretamente pela rede do host.

2. **Solução para acessibilidade do container:**  
   - Para criar um container acessível diretamente pela rede do host, é necessário usar o **driver de rede host**. Isso **desativa o isolamento de rede** e faz com que o container compartilhe a pilha de rede do host.
   - Exemplo:
     ```bash
     docker run --network host my-container
     ```
   
   - Alternativamente, você pode usar o mapeamento de portas para expor as portas específicas do container para o host:
     ```bash
     docker run -p 8080:80 my-container
     ```

### Resumo:
O comando `network connect` não expõe as portas do container ao host. Para fazer isso, use o driver de rede **host** ou o mapeamento de portas. A resposta correta é **B**.

---

### Questão nº 69  
Este é um método de autenticação de usuário suportado para o **Universal Control Plane** (UCP)?  
**Solução:** **x.500**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O **x.500** não é um método de autenticação de usuário suportado para o **Universal Control Plane** (UCP). O **x.500** é um padrão para serviços de diretório, que pode ser utilizado por LDAP ou Active Directory, mas **não é um método de autenticação de usuário** por si só.

### Métodos de autenticação suportados pelo UCP:
1. **Autenticação interna (built-in):**  
   - O UCP usa seu próprio banco de dados para armazenar e verificar credenciais de usuários.

2. **Autenticação externa (external):**  
   - O UCP pode usar serviços externos como **LDAP** ou **Active Directory** para gerenciar contas de usuário e senhas.

### Resumo:
O **x.500** é um padrão de serviço de diretório e não um método de autenticação de usuário diretamente suportado pelo UCP. Portanto, a resposta correta é **B**.

---

### Questão nº 70  
Este comando lista todos os nós em um cluster Swarm a partir da linha de comando?  
**Solução:** `docker ls -a`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker ls -a` **não lista todos os nós em um cluster Swarm**. Ele é utilizado para listar todos os **containers**, tanto em execução quanto parados, no nó atual. 

### Comando correto para listar os nós em um cluster Swarm:
Para listar todos os nós em um cluster Swarm, você deve usar o comando:

```bash
docker node ls
```

Esse comando deve ser executado **em um nó manager** do Swarm, e ele exibirá o **ID do nó, hostname, status, disponibilidade e o status de manager** para cada nó no cluster.

### Resumo:
O comando `docker ls -a` lista containers, não nós de um cluster Swarm. O comando correto para listar os nós em um Swarm é `docker node ls`. A resposta correta é **B**.

---

### Questão nº 71  
Esta é uma forma de configurar o Docker Engine para usar um registro sem um certificado TLS confiável?  
**Solução:** Listar registros inseguros no arquivo de configuração `daemon.json` sob a chave `insecure-registries`.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Sim, é possível configurar o Docker para usar um registro sem um certificado TLS confiável listando os registros inseguros no arquivo de configuração `daemon.json` sob a chave `insecure-registries`.

### Como funciona:
1. **Arquivo `daemon.json`:**
   - O arquivo `daemon.json` permite a configuração do Docker, incluindo a definição de registros inseguros.
   
2. **Chave `insecure-registries`:**
   - Quando você lista registros inseguros sob a chave `insecure-registries`, o Docker interage com esses registros mesmo sem um certificado TLS confiável.

3. **Exemplo de configuração:**
   - No arquivo `daemon.json`, você pode adicionar algo como:
     ```json
     {
       "insecure-registries" : ["my-registry.local:5000"]
     }
     ```
   - Isso permite que o Docker se conecte ao registro `my-registry.local:5000` sem exigir um certificado TLS válido.

4. **Cuidado com a segurança:**
   - Essa configuração **bypassa a segurança** fornecida pelo TLS, tornando a comunicação menos segura. Portanto, deve ser usada com cautela, especialmente em ambientes de produção.

### Resumo:
Configurar o Docker para usar registros inseguros é feito através da chave `insecure-registries` no arquivo `daemon.json`. A resposta correta é **A**.

---

### Questão nº 72  
Você criou um novo serviço chamado `http` e descobriu que ele não está sendo registrado como saudável. Este comando permitirá que você veja a lista de tarefas históricas para este serviço?  
**Solução:** `docker service inspect http`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando `docker service inspect http` exibe informações detalhadas sobre o serviço `http`, como **ID, nome, modo, réplicas, especificação do container, redes, portas**, etc. No entanto, ele **não mostra a lista de tarefas históricas** para o serviço.

### Para visualizar as tarefas históricas de um serviço:
1. **Comando correto:**
   - O comando **`docker service ps http`** deve ser usado para exibir a lista de tarefas associadas ao serviço. Ele mostra informações como **ID, nome, imagem, nó, estado desejado, estado atual e erros** de cada tarefa.
   
   Exemplo:
   ```bash
   docker service ps http
   ```

### Resumo:
O comando `docker service inspect http` exibe informações sobre o serviço, mas não as tarefas. Para visualizar as tarefas, o comando correto é `docker service ps http`. A resposta correta é **B**.

---

### Questão nº 73 
Você está puxando imagens de uma instalação do **Docker Trusted Registry** configurada para usar certificados autoassinados e aparece o seguinte erro:  
`x509: certificate signed by unknown authority.`  
Você já baixou o certificado da autoridade certificadora do Docker Trusted Registry de `https://dtr.example.com/ca`. Como você confia nesse certificado? (Selecione duas opções.)

**A.** Passar `-trust-certificate ca.crt` para o cliente Docker.  
**B.** Colocar o certificado em `/etc/docker/dtr/dtr.example.com.crt` e reiniciar o daemon Docker em todos os nós do cluster.  
**C.** Colocar o certificado em `/etc/docker/certs.d/dtr.example.com/ca.crt` em todos os nós do cluster.  
**D.** Passar `--insecure-registry` para o cliente Docker.  
**E.** Colocar o certificado no caminho de certificados do sistema operacional, confiar no certificado globalmente e reiniciar o daemon Docker em todos os nós do cluster.

**Resposta: C E**  

**Explicação:**  
Para confiar em um certificado autoassinado do Docker Trusted Registry (DTR), você precisa colocar o certificado no local adequado em todos os nós do cluster e reiniciar o daemon Docker.

### Localizações corretas para o certificado:
1. **`/etc/docker/certs.d/dtr.example.com/ca.crt`**:  
   - Este é o local **preferido para sistemas Linux** e versões do Docker 1.13 ou superiores. O Docker verifica automaticamente essa pasta para encontrar certificados para cada domínio de registro.
   
2. **Caminho de certificados do sistema operacional**:  
   - Esse é o local **alternativo** para outros sistemas operacionais e versões do Docker. O certificado precisa ser copiado para o **repositório de certificados do sistema operacional**, e o certificado precisa ser confiado globalmente, o que pode exigir passos adicionais dependendo do sistema operacional.

### Outras opções incorretas:
- **`-trust-certificate ca.crt`**: Não é uma opção válida para o cliente Docker. Não há esse flag no cliente Docker.
- **`/etc/docker/dtr/dtr.example.com.crt`**: Não é um local válido. O local correto é `/etc/docker/certs.d`, não `/etc/docker/dtr`.
- **`--insecure-registry`**: Usar essa flag **não é recomendado**, pois desativa a verificação TLS para o registro, tornando a comunicação insegura e vulnerável a ataques.

### Resumo:
As duas opções corretas são **C** e **E**, pois essas instruções garantem que o certificado seja confiado corretamente e a comunicação com o Docker Trusted Registry seja segura. A resposta correta é **C E**.

---

### Questão nº 74  
Sete gerentes estão em um cluster Swarm.  
Esta é a forma correta de distribuí-los em três datacenters ou zonas de disponibilidade?  
**Solução:** 3-2-2  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Distribuir sete gerentes como 3-2-2 em três datacenters ou zonas de disponibilidade **não é uma boa maneira** de garantir alta disponibilidade e tolerância a falhas no cluster Swarm.  

### Problemas com a distribuição 3-2-2:
1. **Quórum e disponibilidade:**
   - Em um cluster Swarm, é necessário que a **maioria dos gerentes** (mais da metade) esteja disponível para garantir que o estado do Swarm seja mantido e evitar um cenário de **split-brain**.  
   - Com uma distribuição 3-2-2, se um dos datacenters ou zonas de disponibilidade, com 3 gerentes, falhar, **os 4 gerentes restantes não terão quorum**, o que fará o Swarm parar de funcionar.

2. **Melhor distribuição:**
   - Uma melhor maneira de distribuir sete gerentes seria usar uma configuração como **3-3-1** ou **3-2-1-1**, que permite que o Swarm continue funcionando mesmo com a perda de um ou dois datacenters ou zonas de disponibilidade.

### Resumo:
Distribuir os gerentes como 3-2-2 não garante a continuidade do Swarm em caso de falhas, pois pode resultar na perda do quorum. A distribuição recomendada é **3-3-1** ou **3-2-1-1**. A resposta correta é **B**.

---

### Questão nº 75  
No Docker Trusted Registry (DTR), esta é uma maneira de um usuário evitar que uma imagem, como `nginx:latest`, seja sobrescrita por outro usuário com acesso de push ao repositório?  
**Solução:** Usar a interface web do DTR para tornar todas as tags no repositório imutáveis.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Usar a interface web do DTR para tornar **todas as tags** no repositório imutáveis **não é uma boa maneira** de impedir que uma imagem, como `nginx:latest`, seja sobrescrita por outro usuário com acesso de push. Tornar todas as tags imutáveis impediria **qualquer atualização** nas imagens, o que pode não ser desejável, pois pode impedir a atualização de imagens, como a aplicação de **patches de segurança**.

### Melhor abordagem:
1. **Política de promoção (promotion policy):**  
   Uma maneira mais eficaz de evitar que imagens sejam sobrescritas é usar a interface web do DTR para criar uma **política de promoção**. Essa política pode **restrigir** quem pode fazer o push para uma tag ou repositório específico. Isso permite que você controle quem tem permissão para atualizar a imagem sem impedir a atualização legítima.

2. **Webhooks:**  
   Além disso, é possível usar a **API do DTR** para criar **webhooks** que disparam uma ação personalizada quando uma imagem é empurrada para o repositório. Isso também pode ser útil para monitorar e controlar os pushes no repositório.

### Resumo:
Tornar todas as tags imutáveis pode impedir atualizações importantes e não é a solução ideal para evitar sobrescritas. A melhor abordagem seria usar uma **política de promoção** ou **webhooks** para controlar quem pode fazer o push de imagens. A resposta correta é **B**.

---

### Questão nº 76  
Um nó Kubernetes é alocado com um bloco CIDR /26 (64 IPs únicos) para seu espaço de endereçamento.  
Se cada pod neste nó tiver exatamente dois containers, quantos pods este espaço de endereçamento pode suportar neste nó?  

**A. -995**  
**B. 64**  
**C. 32 em cada namespace do Kubernetes**  
**D. 64 para cada serviço roteando para pods neste nó**  
**E. 32**  

**Resposta: E**  

**Explicação:**  
Um bloco CIDR /26 fornece 64 endereços IP únicos para o nó. Para calcular o número de pods que podem ser suportados, precisamos considerar que cada **pod** tem **dois containers** e, portanto, **precisa de dois endereços IP** (um para cada container).

### Cálculo:
- O nó possui **64 endereços IP disponíveis**.
- Como cada pod precisa de **dois endereços IP** (um por container), o número de pods que o nó pode suportar é dado por:

\[
\frac{64}{2} = 32
\]

Portanto, o nó pode suportar até **32 pods**.

### Resumo:
Cada pod, com dois containers, utiliza dois endereços IP, o que limita o número de pods a **32** para um espaço de endereçamento /26. A resposta correta é **E**.

---

### Questão nº 77  
Você deseja criar um container que seja acessível a partir da rede do host.  
Essa ação alcança esse objetivo?  
**Solução:** Usar **network attach** para acessar os containers na rede bridge.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Usar o comando **network attach** para conectar um container à rede **bridge** não fará com que o container seja acessível diretamente pela rede do host.

### O que o comando **network attach** faz:
- O comando **`docker network attach`** permite conectar um container a uma rede adicional. Contudo, ele não muda o comportamento de acessibilidade de rede do container. Se o container já estiver na rede **bridge**, ele não estará automaticamente acessível pela rede do host.
  
### Como tornar o container acessível pela rede do host:
Para criar um container acessível diretamente pela rede do host, você precisa usar o **modo de rede host**. Isso faz com que o container compartilhe a pilha de rede com o host, permitindo o acesso direto.

Exemplo de comando:

```bash
docker run --network host my-container
```

Esse comando fará com que o container use a rede do host diretamente, tornando-o acessível.

### Resumo:
O comando **`network attach`** não expõe o container à rede do host. Para isso, é necessário usar o modo de rede **host**. A resposta correta é **B**.

---

### Questão nº 78  
Essas condições são suficientes para o Kubernetes provisionar dinamicamente um **persistentVolume**, assumindo que não há limitações quanto à quantidade e tipo de armazenamento externo disponível?  
**Solução:** Um **storageClass** padrão é especificado, e subsequentemente um **persistentVolumeClaim** é criado.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
As condições descritas são **suficientes** para o Kubernetes provisionar dinamicamente um **persistentVolume**.

### Como funciona o **provisionamento dinâmico** de volumes no Kubernetes:
1. **StorageClass padrão:**
   - O **StorageClass** define qual provisionador será usado e quais parâmetros serão passados a ele quando o provisionamento dinâmico for invocado.  
   - A presença de um **storageClass padrão** garante que, se um **persistentVolumeClaim** (PVC) não especificar um `storageClass`, ele usará automaticamente o padrão, o que habilita o provisionamento dinâmico.

2. **PersistentVolumeClaim (PVC):**
   - O **PVC** solicita um volume persistente de um tamanho específico, modo de acesso e uma `storageClass`. Se um **persistentVolume** (PV) que atenda a esses requisitos já existir ou puder ser provisionado, o PVC será vinculado ao PV.  

### Resumo:
Se um **storageClass padrão** estiver especificado e um **PVC** for criado, o Kubernetes será capaz de provisionar dinamicamente volumes de armazenamento conforme necessário, sem a necessidade de intervenção manual. A resposta correta é **A**.

---

### Questão nº 79  
No contexto de um cluster no modo Swarm, isso descreve um nó?  
**Solução:** Uma **máquina física** participando do Swarm.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
No modo **Swarm**, um **nó** é qualquer máquina física ou virtual que esteja executando o Docker Engine em modo Swarm. O nó pode ser **gerente** (manager) ou **trabalhador** (worker), dependendo de seu papel no cluster.  

Portanto, uma **máquina física** participando do Swarm, independentemente de ser um manager ou worker, é considerada um nó.

### Resumo:
A definição fornecida está correta, pois um nó pode ser uma máquina física ou virtual que participa do cluster Swarm. A resposta correta é **A**.

---

### Questão nº 80 
Esta configuração atingirá a tolerância a falhas para os gerentes em um Swarm?  
**Solução:** Um **nó gerente** para dois **nós trabalhadores**.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Uma configuração com **um único nó gerente** para **dois nós trabalhadores** **não atingirá a tolerância a falhas** para os gerentes em um Swarm.

### Por que não:
1. **Requisitos de tolerância a falhas:**
   - Para que um Swarm tenha **tolerância a falhas**, ele precisa de **mais de um nó gerente**. Se houver apenas **um único gerente**, se esse nó falhar, todo o cluster ficará indisponível.
   
2. **Número ímpar de nós gerentes:**
   - O Swarm requer um **número ímpar de nós gerentes** para garantir que um **quórum** seja mantido e que o cluster continue funcionando, mesmo que um nó falhe.
   - Uma configuração típica seria **3 nós gerentes**, que oferece a capacidade de **tolerar a perda de 1 gerente** e ainda manter o quórum. Isso é fundamental para garantir que o cluster continue funcionando sem interrupções.

### Resumo:
A configuração mencionada de ter apenas **um nó gerente** não é suficiente para garantir a tolerância a falhas. O Swarm precisa de **mais de um nó gerente** e, idealmente, um **número ímpar** de nós gerentes para garantir a tolerância a falhas. A resposta correta é **B**.

---

### Questão nº 81  
Quais drivers de rede permitem habilitar conectividade de rede multi-host entre containers?  

**A.** macvlan, ipvlan e overlay  
**B.** bridge, user-defined, host  
**C.** host, macvlan, overlay, user-defined  
**D.** bridge, macvlan, ipvlan, overlay  

**Resposta: D**  

**Explicação:**  
Os drivers de rede que permitem habilitar conectividade multi-host entre containers são **bridge, macvlan, ipvlan** e **overlay**.

### Como cada driver suporta conectividade multi-host:

1. **bridge:**  
   - O driver **bridge** conecta containers no mesmo host usando uma **ponte Linux**. Embora possa ser configurado para permitir a conectividade entre containers em hosts diferentes, essa configuração é obsoleta e não é recomendada devido às limitações. O driver preferido para redes multi-host é o **overlay**.

2. **macvlan:**  
   - O driver **macvlan** atribui um **endereço MAC** a cada container, permitindo que eles se comportem como dispositivos físicos na rede. Isso permite que containers se comuniquem com dispositivos na mesma rede, independentemente do host em que estão executando. O driver **macvlan** pode ser configurado para isolar tráfego entre diferentes redes usando trunking 802.1q.

3. **ipvlan:**  
   - O driver **ipvlan** atribui um **endereço IP** a cada container, permitindo que eles se comportem como dispositivos lógicos na rede. Ele suporta **modos de rede** como L2 (nivel 2), L3 (nivel 3) e L3s, oferecendo controle sobre o roteamento e a isolação de tráfego entre diferentes redes. O **ipvlan** também permite a comunicação entre containers em diferentes hosts.

4. **overlay:**  
   - O driver **overlay** cria uma rede que conecta múltiplos **daemons Docker** usando túneis **VXLAN**, permitindo que containers em diferentes hosts se comuniquem, mesmo que estejam em redes diferentes. O **overlay** é o driver padrão e recomendado para redes multi-host, especialmente em ambientes Swarm, e também oferece suporte a criptografia, balanceamento de carga e descoberta de serviços.

### Resumo:
Os drivers que permitem a conectividade de rede entre containers em hosts diferentes são **bridge**, **macvlan**, **ipvlan** e **overlay**, sendo o **overlay** o mais recomendado e utilizado para esse tipo de conectividade. A resposta correta é **D**.

---

### Questão nº 82  
Esta é uma vantagem das **multi-stage builds**?  
**Solução:** **Otima imagens copiando artefatos seletivamente das fases anteriores.**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
As **multi-stage builds** são uma funcionalidade do Docker que permite usar várias instruções **FROM** em um **Dockerfile**, criando diferentes estágios de construção. Cada estágio pode usar uma **imagem base** diferente e executar comandos distintos. A vantagem de usar **multi-stage builds** é a capacidade de copiar **somente os artefatos necessários** de um estágio para o próximo, descartando tudo o que não é necessário no estágio final.

### Benefícios:
1. **Otimização do tamanho da imagem:**  
   - Ao copiar **somente artefatos necessários**, como arquivos compilados ou binários, você pode reduzir significativamente o tamanho da imagem final. Por exemplo, você pode compilar o código em um estágio e copiar apenas os arquivos executáveis para o estágio final, que pode usar uma imagem base mínima, como o `scratch`.

2. **Redução da superfície de ataque:**  
   - Ao excluir dependências, ferramentas de desenvolvimento ou código-fonte desnecessários, a imagem final torna-se **menor e mais segura**, com menos pacotes vulneráveis incluídos.

3. **Eficiência:**  
   - Isso ajuda a criar imagens mais limpas e eficientes, sem sobrecarregar o container final com ferramentas que são necessárias apenas durante a construção, como compiladores ou dependências de construção.

### Resumo:
A **cópia seletiva de artefatos** é uma das principais vantagens das **multi-stage builds**, pois permite otimizar o tamanho da imagem e melhorar a segurança. A resposta correta é **A**.

---

### Questão nº 83  
Estas condições são suficientes para que o Kubernetes provisione dinamicamente um **persistentVolume**, assumindo que não há limitações quanto à quantidade e tipo de armazenamento externo disponível?  
**Solução:** Um **persistentVolumeClaim** é criado que especifica um **storageClass** predefinido.  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
As condições descritas são **suficientes** para o Kubernetes realizar o **provisionamento dinâmico** de um **persistentVolume**. 

### Como o **provisionamento dinâmico** funciona:
1. **StorageClass**:  
   - O **StorageClass** define o provisionador (provisioner) e os parâmetros necessários para criar um volume **sob demanda**. Isso especifica qual provisionador (por exemplo, NFS, iSCSI, etc.) e parâmetros serão utilizados quando o volume for solicitado.
   
2. **PersistentVolumeClaim (PVC)**:  
   - O **PVC** que especifica um **StorageClass** aciona o processo de provisionamento dinâmico. O Kubernetes então cria e vincula automaticamente um **persistentVolume** que corresponda ao pedido especificado no PVC, como o tamanho do volume e os modos de acesso.

3. **Provisão automática de volumes**:  
   - Isso elimina a necessidade de intervenção manual dos administradores do cluster para provisionar volumes de armazenamento, o que facilita a automação e a escalabilidade de armazenamento no Kubernetes.

### Resumo:
A especificação de um **StorageClass** em um **PVC** é suficiente para que o Kubernetes realize o **provisionamento dinâmico** de volumes persistentes. A resposta correta é **A**.

---

### Questão nº 84  
Esta é uma forma de configurar o Docker Engine para usar um registro sem um certificado TLS confiável?  
**Solução:** Definir e exportar a variável de ambiente **IGNORE_TLS** na linha de comando.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Definir e exportar a variável de ambiente **IGNORE_TLS** na linha de comando **não é uma maneira válida** de configurar o Docker Engine para usar um registro sem um certificado TLS confiável. Essa variável de ambiente **não é reconhecida pelo Docker** e não afeta o processo de verificação TLS.

### Como configurar o Docker para usar um registro sem TLS confiável:
1. **Adicionar o certificado ao repositório de confiança do Docker:**  
   Você pode adicionar o certificado autoassinado ou não confiável ao **repositório de certificados do Docker** ou ao **repositório de certificados do sistema**. Isso permite que o Docker confie no registro.
   
2. **Configurar o Docker para permitir registros inseguros:**  
   Outra forma de permitir que o Docker use um registro sem um certificado confiável é configurar o Docker daemon para permitir **registros inseguros**. Isso é feito adicionando o registro à lista de **insecure-registries** no arquivo de configuração do Docker (`daemon.json`), como mostrado abaixo:
   
   ```json
   {
     "insecure-registries" : ["my-registry.local:5000"]
   }
   ```

### Resumo:
A variável de ambiente **IGNORE_TLS** não é uma forma válida de configurar o Docker para usar um registro sem certificado TLS confiável. A maneira correta é usar os **repositórios de certificados** ou configurar o Docker para permitir **registros inseguros**. A resposta correta é **B**.

---

### Questão nº 85  
Você está solucionando problemas de um deployment Kubernetes chamado **api** e deseja ver a tabela de eventos para esse objeto.  
Este comando exibe a tabela de eventos?  
**Solução:** `kubectl logs deployment api`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando **`kubectl logs deployment api`** não exibe a tabela de eventos para o objeto **deployment**. Esse comando exibe **os logs dos pods** que pertencem ao deployment, não a tabela de eventos.

### Para visualizar a tabela de eventos:
1. **Comando correto:**  
   Para visualizar os eventos associados ao deployment, você deve usar o comando **`kubectl describe deployment api`**, que fornece detalhes do deployment, incluindo os eventos registrados.

   Exemplo:
   ```bash
   kubectl describe deployment api
   ```

2. **Comando alternativo com eventos filtrados:**  
   Você também pode usar o comando **`kubectl get events`** com um filtro para exibir eventos específicos do deployment:
   ```bash
   kubectl get events --field-selector involvedObject.name=api
   ```

### Resumo:
O comando **`kubectl logs deployment api`** exibe logs dos pods, não os eventos do deployment. Para isso, o comando correto é **`kubectl describe deployment api`**. A resposta correta é **B**.

---

### Questão nº 86  
Este é um tipo de namespace do kernel Linux que fornece isolamento de containers?  
**Solução:** **Storage**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
**Storage** **não é um tipo de namespace do kernel Linux** que fornece isolamento para containers. O **Linux namespaces** é uma característica do kernel Linux que permite dividir recursos do sistema, de forma que diferentes conjuntos de processos veem diferentes conjuntos de recursos. Isso permite o isolamento entre containers ou processos.

### Tipos de namespaces:
Desde a versão 5.6 do kernel, existem **8 tipos de namespaces** no Linux:

1. **Mount**: Isola o sistema de arquivos (montagens).
2. **UTS**: Isola os nomes de host e domínio.
3. **IPC**: Isola a comunicação entre processos (IPC).
4. **PID**: Isola os IDs de processos.
5. **Network**: Isola as interfaces de rede e configurações.
6. **User**: Isola os IDs de usuário e grupo.
7. **Cgroup**: Isola os grupos de controle (cgroups) para gerenciamento de recursos.
8. **Time**: Isola as configurações de tempo.

**Storage** não é um namespace do kernel Linux, portanto, **não fornece isolamento de containers**.

### Resumo:
O namespace **Storage** não faz parte dos namespaces do Linux para isolamento de containers. A resposta correta é **B**.

---

### Questão nº 87  
Você criou um novo serviço chamado **http** e descobriu que ele não está sendo registrado como saudável. Este comando permitirá que você veja a lista de tarefas históricas para este serviço?  
**Solução:** `docker inspect http`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando **`docker inspect http`** exibe informações de baixo nível sobre objetos Docker, como containers, imagens, redes, etc. No entanto, ele **não exibe a lista de tarefas** para um serviço. Ele fornece informações detalhadas sobre o serviço, mas não sobre o histórico das tarefas associadas a ele.

### Comando correto para visualizar as tarefas de um serviço:
Para visualizar as tarefas associadas a um serviço, você deve usar o comando **`docker service ps`**. Esse comando exibe a lista de tarefas que estão sendo executadas no serviço, incluindo o **ID, nome, imagem, nó, estado desejado, estado atual e erros** de cada tarefa.

Exemplo de comando:
```bash
docker service ps http
```

### Resumo:
O comando **`docker inspect http`** não exibe informações sobre as tarefas. O comando correto para ver as tarefas históricas é **`docker service ps http`**. A resposta correta é **B**.

---

### Questão nº 88  
Isso descreve o papel dos **Control Groups (cgroups)** quando usados com um container Docker?  
**Solução:** **Autorização de usuário para a API Docker.**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O papel dos **Control Groups (cgroups)** em um container Docker não está relacionado à **autorização de usuário** para a API Docker. Os **cgroups** são um recurso do kernel Linux que permitem **limitar o acesso dos processos e containers a recursos do sistema** como **CPU**, **memória RAM**, **IOPS** e **rede**. Eles permitem que o Docker compartilhe os recursos do hardware disponível entre os containers e **aplique limites e restrições**, garantindo que um container não consuma recursos excessivos.

### Distinção:
1. **Cgroups:**
   - Os **cgroups** são usados para **controlar e limitar o uso de recursos** por processos ou containers. Por exemplo, você pode definir o quanto de CPU ou memória um container pode usar, garantindo a **isolação** e **controle** de recursos entre diferentes containers.

2. **Autorização de usuário na API Docker:**
   - A **autorização de usuário** para a API Docker é um conceito separado, que trata de **conceder permissões** a usuários ou grupos para realizar ações específicas no daemon Docker, como **criar, executar ou parar containers**. Isso é tratado com **controle de acesso** e **políticas de segurança**, não com cgroups.

### Resumo:
Os **cgroups** controlam os recursos do sistema para os containers, e não têm relação com a autorização de usuários para a API Docker. A resposta correta é **B**.


---

### Questão nº 89  
Esta é uma forma de autenticação de usuário suportada para o **Universal Control Plane (UCP)**?  
**Solução:** **Docker ID**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O **Docker Universal Control Plane (UCP)** possui seu próprio mecanismo de autenticação integrado e também se integra com serviços LDAP. Além disso, o UCP utiliza o **controle de acesso baseado em funções (RBAC)** para definir quem pode acessar e fazer alterações no cluster e nas aplicações. 

No entanto, **não há menção de que o Docker ID** seja um método de autenticação suportado para o **UCP** nas fontes oficiais. A autenticação no UCP é gerenciada por meio de métodos mais tradicionais como **LDAP** e os próprios **métodos de autenticação integrados do UCP**.

### Resumo:
O Docker ID não é uma forma de autenticação suportada para o UCP. A resposta correta é **B**.

---

### Questão nº 90  
Este é o propósito do **Docker Content Trust**?  
**Solução:** Indicar que uma imagem no Docker Hub é uma imagem oficial.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O propósito do **Docker Content Trust** não é indicar se uma imagem no Docker Hub é uma **imagem oficial**. O **Docker Content Trust (DCT)** é uma funcionalidade que permite que os usuários verifiquem a **integridade e a origem** das imagens de contêiner que eles fazem o pull ou deploy de um registro, usando **assinaturas digitais** armazenadas em um servidor Notary. 

### O que o **Docker Content Trust** faz:
- Ele **garante que as imagens são autênticas** e não foram alteradas, permitindo que você verifique a integridade e a autenticidade das imagens.
- As imagens **oficiais** no Docker Hub são um conjunto curado de repositórios considerados os melhores pontos de partida para a maioria dos usuários. **Essas imagens não são necessariamente assinadas** pelo Docker Content Trust, embora algumas possam ser.

### Como identificar uma imagem oficial:
- Para identificar uma imagem oficial no Docker Hub, você deve procurar o **distintivo azul "official image"** na página da imagem.

### Resumo:
O **Docker Content Trust** não tem como objetivo indicar se uma imagem é oficial, mas sim garantir a autenticidade e integridade das imagens. A resposta correta é **B**.

---

### Questão nº 91  
Esta ação irá atualizar o Docker Engine CE para o Docker Engine EE?  
**Solução:** Desabilitar o serviço Docker via `chkconfig` ou `systemctl`.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Desabilitar o serviço Docker usando **`chkconfig`** ou **`systemctl`** **não atualiza o Docker Engine CE para o Docker Engine EE**. Isso apenas **interrompe o daemon do Docker**, mas não altera a versão ou a edição do Docker Engine.

### Para atualizar o Docker Engine CE para o Docker Engine EE, o procedimento correto seria:
1. **Baixar a licença do Docker Enterprise** do Docker Store.
2. **Instalar o pacote `docker-ee`** a partir do repositório Docker.
3. **Reiniciar o serviço Docker** e verificar a versão e edição do Docker com o comando `docker version`.

### Resumo:
Desabilitar o serviço Docker não altera a versão ou edição do Docker. A resposta correta é **B**.

---

### Questão nº 92  
Este é um método de autenticação de usuário suportado para o **Universal Control Plane**?  
**Solução:** **PAM**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
**PAM** (Pluggable Authentication Modules) **não é um método de autenticação suportado** para o **Universal Control Plane (UCP)**. Segundo a documentação oficial do Docker, os métodos de autenticação suportados pelo UCP são:

- **LDAP** (Lightweight Directory Access Protocol)
- **Active Directory**
- **SAML 2.0** (Security Assertion Markup Language)
- **Usuários locais** (usuários internos do UCP)

### Resumo:
O **PAM** não é uma forma de autenticação suportada para o **Universal Control Plane**. A resposta correta é **B**.

---

### Questão nº 93  
Este comando lista todos os nós em um cluster Swarm a partir da linha de comando?  
**Solução:** `docker node ls`  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O comando **`docker node ls`** **lista todos os nós em um cluster Swarm** a partir da linha de comando. Esse comando deve ser executado em um **nó manager** do Swarm e retorna informações sobre cada nó no cluster, como o **ID do nó**, **hostname**, **status**, **disponibilidade**, e **status de manager**.

### Comando correto:
- Para listar os nós de um cluster Swarm, use **`docker node ls`**. Não é necessário nenhum outro parâmetro.

### Resumo:
O comando **`docker node ls`** é o correto para listar todos os nós em um Swarm a partir da linha de comando. A resposta correta é **A**.

---

### Questão nº 94  
Você configura um Docker Engine local para forçar o **Docker Content Trust (DCT)**, definindo a variável de ambiente **DOCKER_CONTENT_TRUST=1**.  
Se a imagem **myorg/myimage:1.0** não for assinada, o Docker bloqueará este comando?  
**Solução:** `docker image import <tarball> myorg/myimage:1.0`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O **Docker Content Trust (DCT)** é uma funcionalidade que permite verificar a **integridade e o autor** de imagens de contêiner que são retiradas ou implantadas de um servidor de registro, com assinaturas validadas por um **Notary server**. Quando o DCT está ativado (configurado com a variável **DOCKER_CONTENT_TRUST=1**), o Docker apenas irá puxar, rodar ou construir imagens que tenham **assinaturas válidas** para uma tag específica.

### No entanto, o DCT **não aplica ao comando `docker image import`**:
- O comando **`docker image import`** permite importar uma imagem ou tarball com um repositório e tag de um arquivo ou entrada padrão (STDIN), mas ele **não interage com um servidor de registro** ou **Notary server**.
- Portanto, mesmo que o DCT esteja habilitado e a imagem **myorg/myimage:1.0** não tenha assinatura, o comando **`docker image import`** **não será bloqueado**.

### Resumo:
O comando **`docker image import`** **não interage com o DCT**, portanto, mesmo que a imagem não tenha assinatura, o Docker não bloqueará o comando. A resposta correta é **B**.

---

### Questão nº 95  
Esta é uma função do **UCP (Universal Control Plane)**?  
**Solução:** **Controle de acesso baseado em funções (RBAC) para imagens.**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O **controle de acesso baseado em funções para imagens** (**Image RBAC**) **não é uma função do Universal Control Plane (UCP)**. O UCP possui um mecanismo de autenticação embutido e também se integra a serviços **LDAP**. Ele oferece **controle de acesso baseado em funções (RBAC)** para controlar quem pode acessar e fazer alterações no cluster e nas aplicações.

No entanto, o **controle de acesso baseado em funções para imagens** é uma funcionalidade do **Docker Trusted Registry (DTR)**, que se integra ao UCP. O DTR permite gerenciar o acesso às imagens do Docker, controlando quem pode empurrar ou puxar imagens específicas de um repositório, por meio de **RBAC** no contexto do armazenamento de imagens.

### Resumo:
Embora o UCP ofereça **RBAC** para o gerenciamento de acesso a clusters e aplicações, o controle de acesso para imagens é uma função do **Docker Trusted Registry (DTR)**, não do UCP. A resposta correta é **B**.

---

### **Questão nº 96**  
Você deseja criar um container que seja acessível a partir da rede do host.  
Essa ação realiza isso?  
**Solução:** Usar `--link` para acessar o container na rede bridge.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Usar a opção **`--link`** para acessar o container na **rede bridge** **não realiza o objetivo** de tornar o container acessível pela rede do host. O parâmetro **`--link`** conecta containers que estão na mesma rede, mas não expõe as portas do container para a rede do host.

### Como criar um container acessível pela rede do host:
1. **Usar `--network host`:**  
   Para tornar o container acessível pela rede do host, você pode usar a opção **`--network host`**. Isso **faz o container compartilhar a pilha de rede do host** e utilizar o mesmo **endereço IP**.
   
2. **Usar `--publish` ou `-p`:**  
   Se você deseja mapear portas específicas do container para as portas do host, pode usar as opções **`--publish`** ou **`-p`**, o que permitirá o acesso ao container pelas portas mapeadas.

### Resumo:
O uso de **`--link`** não torna o container acessível pela rede do host. Para isso, você deve usar **`--network host`** ou mapear as portas com **`--publish`**. A resposta correta é **B**.

---

### Questão nº 97  
Isso descreve o papel dos **Control Groups (cgroups)** quando usados com um container Docker?  
**Solução:** **Controle de acesso baseado em funções para recursos em cluster.**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O papel dos **Control Groups (cgroups)** quando usados com um container Docker **não está relacionado ao controle de acesso baseado em funções** para recursos em cluster. O **cgroup** é um recurso do **kernel Linux** que permite **limitar, gerenciar e isolar** o uso de recursos (como **CPU**, **memória do sistema**, **largura de banda de rede**, etc.) entre os containers ou processos no sistema.

### O que os **cgroups** fazem:
- **Cgroups** permitem que o Docker **compartilhe recursos de hardware** disponíveis entre containers e aplique **limites e restrições** opcionalmente.
- **Cgroups** são usados para garantir que cada container tenha um uso de recursos controlado, evitando que um container monopolize recursos como CPU ou memória.

### Resumo:
Os **cgroups** são usados para gerenciar e limitar o uso de recursos por containers, não para controlar o acesso baseado em funções a recursos em cluster. A resposta correta é **B**.

---

### Questão nº 98  
Uma **varredura de segurança do DTR** detectará isso?  
**Solução:** **Chaves privadas copiadas para a imagem.**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
A **varredura de segurança do DTR** (Docker Trusted Registry) detectará **chaves privadas** copiadas para uma imagem. A varredura de segurança do DTR utiliza a ferramenta de código aberto **SecretScanner**, que procura por segredos não protegidos em imagens de contêiner ou sistemas de arquivos.

### Como funciona a varredura de segurança:
- **SecretScanner** verifica o conteúdo das imagens em busca de **aproximadamente 140 tipos de segredos**, incluindo **chaves privadas** e outros dados sensíveis, como tokens e senhas.
- Quando a ferramenta encontra uma chave privada ou outro segredo, ela gera um alerta, sinalizando que esse dado confidencial deve ser removido da imagem.

### Resumo:
O DTR utiliza o **SecretScanner** para identificar chaves privadas e outros segredos em imagens de contêiner. Portanto, a resposta correta é **A**.

---

### Questão nº 99  
Durante o desenvolvimento de uma aplicação destinada a ser orquestrada pelo Kubernetes, você deseja montar o diretório **/data** no seu laptop em um container.  
Esta estratégia irá alcançar o objetivo?  
**Solução:** Criar um **PersistentVolumeClaim** solicitando **storageClass:""** (que por padrão é para armazenamento local) e **hostPath: /data**, e usar isso para preencher um volume em um pod.  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
Esta estratégia **não funcionará** para montar o diretório **/data** do seu laptop no container no Kubernetes. A principal razão é que a **hostPath volume** é um tipo de volume usado para montar um arquivo ou diretório do sistema de arquivos do **nó host** no pod, mas ele **não é portátil** entre nós, e depende de o diretório **/data** existir e ser idêntico no nó onde o pod for agendado.

### Problemas com a estratégia proposta:
1. **Dependência de nó específico**:  
   A **hostPath** pressupõe que o diretório **/data** no nó Kubernetes seja o mesmo que o **/data** no seu laptop. No entanto, os nós podem estar em máquinas diferentes com layouts de sistemas de arquivos distintos.
   
2. **Falta de portabilidade**:  
   O volume **hostPath** não é portátil entre nós. Se o pod for movido para outro nó, ele não terá acesso ao mesmo diretório **/data**.

3. **StorageClass e hostPath**:  
   O **storageClass** não é aplicável para volumes **hostPath**, já que eles não são provisionados dinamicamente.

### Solução alternativa:
Para montar o diretório **/data** do seu laptop em um container, você pode usar volumes que suportam acesso remoto, como **NFS**, **Ceph**, ou **GlusterFS**. Além disso, o seu laptop precisa ser acessível pela rede do cluster e ter as permissões adequadas para compartilhar o diretório **/data**. Outra opção seria usar ferramentas como **Skaffold** ou **Telepresence** para sincronizar seus arquivos locais com o cluster Kubernetes.

### Resumo:
A solução proposta não funcionará para montar o diretório **/data** do laptop no container. A resposta correta é **B**.

---

### Questão nº 100  
Você está executando apenas cargas de trabalho do Kubernetes em um nó de trabalho que requer manutenção, como instalação de patches ou atualização do sistema operacional.  
Qual comando deve ser executado no nó para terminar suavemente todos os pods no nó, enquanto marca o nó como **não agendável**?  

**A.** `docker swarm leave`  
**B.** `docker node update -availability drain <nome do nó>`  
**C.** `kubectl drain <nome do nó>`  

**Resposta: C**  

**Explicação:**  
O comando **`kubectl drain <nome do nó>`** é o comando correto a ser executado para terminar suavemente todos os pods no nó e marcá-lo como **não agendável**. Esse comando **evita a programação de novos pods** no nó e **termina os pods existentes de forma controlada**, respeitando as **PodDisruptionBudgets** configuradas, se houver, e permitindo que os containers dos pods sejam encerrados de forma graciosa.

### Outras opções explicadas:
- **`docker swarm leave`**: Este comando faz o nó deixar o cluster Swarm, mas **não afeta as cargas de trabalho do Kubernetes** no nó.
- **`docker node update -availability drain <nome do nó>`**: Este comando altera a **disponibilidade do nó para "drain"**, o que significa que **não serão atribuídas novas tarefas ao nó**, mas **não irá terminar os pods existentes**.
- **`kubectl cordon <nome do nó>`**: Este comando **marca o nó como não agendável**, mas **não termina os pods** que já estão no nó.

### Resumo:
O comando correto para realizar a tarefa de forma controlada e adequada é **`kubectl drain <nome do nó>`**. A resposta correta é **C**.

---

### Questão nº 101  
Esta é uma vantagem das **multi-stage builds**?  
**Solução:** **Melhor separação lógica das instruções do Dockerfile para aumentar a legibilidade.**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
As **multi-stage builds** permitem que você use várias instruções **FROM** no seu **Dockerfile**, iniciando cada uma em um estágio diferente. Essa abordagem facilita uma **melhor separação lógica** das instruções do Dockerfile, o que melhora a **legibilidade** do arquivo, além de trazer outras vantagens, como **menor tamanho da imagem**, **tempo de construção mais rápido** e **redução de riscos de segurança**.

### Benefícios das **multi-stage builds**:
1. **Separação lógica**:  
   Ao dividir seu Dockerfile em diferentes estágios, você pode organizar suas instruções de acordo com o objetivo de cada fase (como construir, testar ou implantar sua aplicação), tornando o arquivo mais fácil de entender e manter.

2. **Tamanho reduzido da imagem**:  
   Ao copiar apenas os artefatos necessários de um estágio para outro, você pode **eliminar dependências e ferramentas desnecessárias**, o que resulta em imagens mais leves.

3. **Segurança aprimorada**:  
   Como você pode deixar para trás dependências desnecessárias nos estágios finais, isso reduz a **superfície de ataque** da sua imagem final.

4. **Melhor manutenção**:  
   Dockerfiles com **multi-stage builds** são mais fáceis de manter e modificar, pois você tem a liberdade de reorganizar a estrutura sem afetar o objetivo de cada estágio.

### Resumo:
A **multi-stage build** é uma ótima maneira de melhorar a legibilidade do seu Dockerfile e outros aspectos da construção da imagem. A resposta correta é **A**.

---

### Questão nº 102  
Este é um método de autenticação de usuário suportado para o **Universal Control Plane (UCP)**?  
**Solução:** **LDAP**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
O **LDAP (Lightweight Directory Access Protocol)** **é um método de autenticação suportado** pelo **Universal Control Plane (UCP)**. O UCP possui seu próprio mecanismo de autenticação integrado e **se integra com LDAP e Active Directory**. Ele também oferece **Controle de Acesso Baseado em Funções (RBAC)** e **Docker Content Trust**.

No UCP, é possível configurar o **LDAP como método de autenticação**, conectando-se ao servidor LDAP fornecendo informações como:
- URL do LDAP
- **Base DN** (Distinguished Name)
- **Bind DN** e senha
- Configurações de busca de usuários e grupos

### Resumo:
O **LDAP** é, de fato, um método de autenticação suportado para o **UCP**. A resposta correta é **A**.

---

### Questão nº 103  
Este comando exibirá uma lista de volumes para um container específico?  
**Solução:** `docker volume logs nginx --containers`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando **`docker volume logs nginx --containers`** **não é válido** para exibir a lista de volumes de um container. O comando **`docker volume`** é usado para **gerenciar volumes**, enquanto o comando **`docker logs`** exibe os logs de um container. 

O comando proposto mistura duas sintaxes incorretas. Para listar os volumes associados a um container específico, você pode usar o **`docker inspect`** com a opção de filtro.

### Comando correto:
Para exibir os volumes montados por um container específico (como o **nginx**), use:
```bash
docker inspect -f '{{ .Mounts }}' nginx
```
Esse comando retorna detalhes sobre os volumes montados no container **nginx**.

### Resumo:
A solução sugerida está incorreta. O comando **`docker inspect`** deve ser usado para obter a lista de volumes de um container. A resposta correta é **B**.

---

### **Questão nº 104**  
A política de segurança de uma empresa especifica que os containers de desenvolvimento e produção devem ser executados em nós separados em um cluster Swarm.  
Pode-se usar isso para agendar containers e atender aos requisitos da política de segurança?  
**Solução:** **Node taints**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Os **node taints** são uma maneira de marcar nós em um cluster Swarm para repelir ou atrair containers com base nas suas **tolerâncias**. Ao aplicar taints nos nós designados para desenvolvimento ou produção, a empresa pode garantir que apenas containers com as tolerâncias correspondentes possam ser agendados nesses nós, atendendo assim aos requisitos de segurança.

### Como funcionam os **node taints**:
1. Os **node taints** são expressos como **chave=valor:efeito**, onde o efeito pode ser **NoSchedule**, **PreferNoSchedule** ou **NoExecute**.
2. Por exemplo, se você deseja taint um nó para ser usado apenas para desenvolvimento, pode rodar o seguinte comando:
   ```bash
   kubectl taint nodes node1 env=dev:NoSchedule
   ```
   Isso significa que **nenhum container será agendado** no **node1** a menos que tenha uma **tolerância** para o taint **env=dev:NoSchedule**.
3. Para adicionar uma **tolerância** a um container, você pode especificá-la no **PodSpec**. Exemplo:
   ```yaml
   tolerations:
   - key: "env"
     operator: "Equal"
     value: "dev"
     effect: "NoSchedule"
   ```
   Essa tolerância permite que o container seja agendado no nó com o taint correspondente.

### Resumo:
Os **node taints** são uma maneira eficaz de garantir que containers de desenvolvimento e produção sejam agendados em nós separados, conforme os requisitos da política de segurança. A resposta correta é **A**.

---

### Questão nº 105  
Este é um método de autenticação de usuário suportado para o **Universal Control Plane (UCP)**?  
**Solução:** **SAML**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
**SAML (Security Assertion Markup Language)** **é um método de autenticação suportado** pelo **Universal Control Plane (UCP)**. O UCP tem um mecanismo de autenticação integrado e também oferece suporte para **LDAP**, **Active Directory** e **SAML**. 

### Como o SAML é configurado no UCP:
- O UCP permite que você configure o **SAML** como um método de autenticação e conecte-o ao seu **Identity Provider (IdP)**.
- Para configurar, você precisa fornecer ao IdP o **Entity ID** e a **ACS URL** do UCP, e fornecer ao UCP o **SAML Sign-on URL** e o **certificado x509** do IdP.

### Resumo:
O **SAML** é um método de autenticação suportado para o **Universal Control Plane (UCP)**. A resposta correta é **A**.

---

### Questão nº 106  
A política de segurança de uma empresa especifica que os containers de desenvolvimento e produção devem ser executados em nós separados em um cluster Swarm.  
Pode-se usar isso para agendar containers e atender aos requisitos da política de segurança?  
**Solução:** **Label constraints**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
**Label constraints** são uma maneira eficaz de garantir que os containers de desenvolvimento e produção sejam agendados em nós separados em um cluster Swarm. Com **label constraints**, você pode especificar em quais nós um serviço pode ser executado, com base nos rótulos (labels) atribuídos a esses nós.

### Como funciona:
1. Você pode rotular os nós destinados ao **desenvolvimento** com **`env=dev`** e os nós destinados à **produção** com **`env=prod`**.
2. Em seguida, ao criar um serviço, você pode usar a flag **`--constraint`** para restringir sua execução apenas nos nós com o rótulo correspondente:
   - Exemplo para criar um serviço de desenvolvimento:
     ```bash
     docker service create --name dev-app --constraint 'node.labels.env == dev' ...
     ```
   - Exemplo para criar um serviço de produção:
     ```bash
     docker service create --name prod-app --constraint 'node.labels.env == prod' ...
     ```

### Resumo:
As **label constraints** permitem que você garanta que containers de desenvolvimento e produção sejam agendados em nós separados, atendendo aos requisitos da política de segurança da empresa. A resposta correta é **A**.

---

### Questão nº 107  
Este comando listará todos os nós em um cluster Swarm a partir da linha de comando?  
**Solução:** `docker inspect nodes`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando **`docker inspect nodes`** **não é válido** para listar todos os nós em um cluster Swarm. O comando **`docker inspect`** requer o nome ou o ID de um objeto (como container, imagem, volume, etc.) como argumento, mas **não pode ser usado diretamente para listar nós** no Swarm.

### Comando correto:
Para listar todos os nós de um cluster Swarm, você deve usar o comando **`docker node ls`**, executado a partir de um **nó manager**. Este comando exibirá informações detalhadas sobre cada nó no cluster, como **ID do nó**, **hostname**, **status**, **disponibilidade**, **status do manager** e **versão do engine**.

### Resumo:
O comando **`docker inspect nodes`** não pode ser usado para listar nós em um cluster Swarm. O comando correto é **`docker node ls`**. A resposta correta é **B**.

---

### Questão nº 108  
Este comando garantirá que o tráfego de sobreposição (overlay) entre as tarefas de serviço seja criptografado?  
**Solução:** `docker service create --network --encrypted`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando **`docker service create --network --encrypted`** **não garante** que o tráfego de sobreposição entre as tarefas de serviço seja criptografado. Isso ocorre porque a opção **`--encrypted`** **não é válida** no comando **`docker service create`**. Além disso, a opção **`--network`** exige que um nome ou ID da rede seja especificado, mas o comando não está configurado corretamente para garantir a criptografia.

### Como criptografar o tráfego overlay:
Para criptografar o tráfego de sobreposição entre as tarefas de serviço, você deve usar a opção **`--opt encrypted`** ao criar a rede sobreposta com o comando **`docker network create`**. Por exemplo:
```bash
docker network create --opt encrypted --driver overlay my-encrypted-network
```
Após criar a rede criptografada, você pode conectar seu serviço à rede usando o comando **`docker service create`**:
```bash
docker service create --network my-encrypted-network my-service
```

### Resumo:
O comando fornecido não está correto para garantir a criptografia do tráfego overlay. A resposta correta é **B**.

---

### Questão nº 109  
Isso descreve o papel dos **Control Groups (cgroups)** quando usados com um container Docker?  
**Solução:** **Isolamento entre os recursos usados pelos containers**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
Os **Control Groups (cgroups)** são uma característica do **kernel Linux** que permite limitar, modificar ou alocar recursos conforme necessário. O Docker usa os **cgroups** para **isolar os recursos** utilizados pelos containers, como **CPU**, **memória**, **I/O de disco**, **rede**, entre outros. Isso garante que cada container tenha seus próprios **limites de recursos** e que **os containers não interfiram uns nos outros ou no sistema host**.

### Benefícios do uso de **cgroups**:
- **Isolamento de recursos**: Cada container pode ter um conjunto exclusivo de **limites e restrições** para recursos como CPU e memória.
- **Segurança e confiabilidade**: O isolamento ajuda a **prevenir interferências entre containers** e **melhora a segurança** do sistema.
- **Desempenho**: Com a aplicação de restrições de recursos, é possível garantir que os containers funcionem dentro de seus limites de desempenho, sem afetar o sistema ou outros containers.

### Resumo:
Os **cgroups** desempenham um papel fundamental no **isolamento de recursos** utilizados pelos containers, como descrito na solução. A resposta correta é **A**.

---

### Questão nº 110  
Este é um **namespace** do **kernel Linux** que está desabilitado por padrão e precisa ser habilitado no momento da execução do Docker engine para ser usado?  
**Solução:** **net**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O **namespace net** (que gerencia o isolamento da rede) **não precisa ser habilitado manualmente** no momento da execução do Docker engine. Ele está **habilitado por padrão** em containers Docker. O **namespace net** é uma das várias características do **kernel Linux** que Docker utiliza para isolar os recursos da rede entre os containers, permitindo que cada container tenha sua própria pilha de rede.

### Resumo:
O **namespace net** não precisa ser habilitado manualmente no Docker, pois já é habilitado por padrão. A resposta correta é **B**.

---

### Questão nº 111 
Este comando exibirá uma lista de volumes para um container específico?  
**Solução:** `docker container inspect nginx`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
O comando **`docker container inspect nginx`** **não exibirá** uma lista de volumes para um container específico. Ele fornece informações detalhadas sobre o container, como sua configuração, configurações de rede, estado e caminho dos logs, mas **não mostra diretamente os volumes montados**.

### Como obter a lista de volumes:
Para exibir a lista de volumes montados em um container específico, é necessário usar o **`--format`** com um template customizado que filtre o campo **`Mounts`**. Por exemplo, o seguinte comando mostrará a origem e o destino dos volumes montados no container **nginx**:

```bash
docker container inspect --format='{{range .Mounts}}{{.Source}} -> {{.Destination}}{{end}}' nginx
```

Este comando filtrará as informações sobre os volumes e exibirá os detalhes de origem e destino.

### Resumo:
O comando **`docker container inspect nginx`** não fornece a lista de volumes montados diretamente. A resposta correta é **B**.

---

### Questão nº 112  
O YAML do Kubernetes mostrado abaixo descreve um serviço **clusterIP**.  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dca
spec:
  type: clusterIP
  selector:
      app: nginx
  ports:
  - port: 8080
    targetPort: 80
  - port: 4443
    targetPort: 443
```
Esta é uma declaração correta sobre como este serviço roteia as requisições?  
**Solução:** **O tráfego enviado ao IP de qualquer pod com o rótulo app: nginx na porta 8080 será encaminhado para a porta 80 desse pod.**  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A declaração está incorreta porque não menciona o **nome do serviço** ou o **endereço clusterIP**. O tráfego enviado ao IP de qualquer pod com o rótulo **app: nginx** na porta 8080 **não será encaminhado diretamente para a porta 80 desse pod**, a menos que o tráfego venha de outro pod dentro do mesmo cluster que conheça o IP do pod. Para acessar o serviço de fora do cluster, o tráfego precisa ser enviado ao **endereço clusterIP** do serviço, que é atribuído pelo Kubernetes, e à porta 8080 do serviço definida no arquivo YAML. O serviço, então, encaminhará o tráfego para um dos pods selecionados na porta 80.

### Declaração correta:
O tráfego enviado ao **endereço clusterIP** do serviço **dca** na porta **8080** será encaminhado para a **porta 80** de um dos pods com o rótulo **app: nginx**.

### Resumo:
A declaração fornecida está incorreta, pois não menciona a utilização do **clusterIP** do serviço. A resposta correta é **B**.

---

### Questão nº 113  
Sua organização possui uma solução de log centralizada, como o **Splunk**.  
Este comando configurará um container Docker para exportar logs do container para a solução de logging?  
**Solução:** `docker system events --filter splunk`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A solução proposta, **`docker system events --filter splunk`**, **não configura** um container Docker para exportar logs para uma solução de logging, como o **Splunk**. Este comando **não é válido** para enviar logs a um destino remoto. O **`--filter`** na verdade só aceita filtros como **container**, **daemon**, **event**, **image**, **label**, **network**, **plugin**, **type**, e **volume**, mas **splunk** não é uma chave válida para filtrar eventos.

### Configuração correta para exportar logs:
Para exportar logs de containers para uma solução como **Splunk**, você deve usar o **`--log-driver`** e **`--log-opt`** quando criar ou rodar o container. Exemplo:
```bash
docker run --log-driver=splunk --log-opt splunk-token=176FCEBF-4CF5-4EDF-91BC-703796522D20 --log-opt splunk-url=https://splunkhost:8088 ...
```
Este comando irá **enviar os logs do container para o Splunk HTTP Event Collector (HEC)** usando o **token de autenticação** fornecido e o **URL do Splunk**.

### Resumo:
A solução proposta não é válida para configurar o envio de logs para o Splunk. A resposta correta é **B**.

---

### Questão nº 114  
O YAML do Kubernetes mostrado abaixo descreve uma **NetworkPolicy**.  
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: dca
  namespace: default
spec:
  podSelector:
    matchLabels:
      tier: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          tier: api
```
Esta **NetworkPolicy** **bloqueará** este tráfego?  
**Solução:** **Uma requisição emitida de um pod que possui apenas o rótulo tier: frontend, para um pod que possui o rótulo tier: backend.**  

**A. Sim**  
**B. Não**  

**Resposta: A**  

**Explicação:**  
A configuração da **NetworkPolicy** fornecida indica que a política se aplica aos pods com o rótulo **tier: backend** no namespace **default**. A regra de **ingress** permite o tráfego de pods com o rótulo **tier: api**, ou seja, ela **não permite** o tráfego de pods com o rótulo **tier: frontend** para os pods **backend**.

### Resumo:
A **NetworkPolicy** bloqueia o tráfego de qualquer pod que tenha o rótulo **tier: frontend** para os pods com o rótulo **tier: backend**, pois não há uma regra permitindo esse tráfego. A resposta correta é **A**.

---

### Questão nº 115  
Você quer fornecer um arquivo de configuração a um container em tempo de execução. Este conjunto de ferramentas e etapas do Kubernetes realiza isso?  
**Solução:** Transformar o arquivo de configuração em um objeto **configMap** e montá-lo diretamente no pod e container apropriado usando a chave **.spec.containers.configMounts**.

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**  
A solução proposta está **incorreta** porque **não existe a chave `.spec.containers.configMounts`** no Kubernetes. Para montar um arquivo de configuração em um container, a abordagem correta seria criar um **configMap** no Kubernetes e, em seguida, montar esse configMap no pod por meio da chave **`volumeMounts`** dentro da especificação do container.

### Passos corretos para montar um arquivo de configuração:
1. **Criar o configMap** a partir de um arquivo de configuração.
   Exemplo:
   ```bash
   kubectl create configmap my-config --from-file=config-file.txt
   ```
2. **Montar o configMap** no pod, utilizando volumes e **volumeMounts**. A especificação correta seria:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: my-pod
   spec:
     containers:
     - name: my-container
       image: my-image
       volumeMounts:
       - name: config-volume
         mountPath: /path/in/container
     volumes:
     - name: config-volume
       configMap:
         name: my-config
   ```

### Resumo:
O método correto envolve **volumes e volumeMounts**, e não a chave **.spec.containers.configMounts**, que **não existe** no Kubernetes. A resposta correta é **B**.

---

### Questão nº 116  
Sete **managers** estão em um cluster Swarm.  
Esta é a maneira correta de distribuí-los entre três **datacenters** ou **zonas de disponibilidade**?  
**Solução:** 4-2-1  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**

A distribuição **4-2-1** **não é ideal** para garantir alta disponibilidade e tolerância a falhas em um cluster Swarm.

Para garantir que o **cluster Swarm** continue funcionando corretamente, a regra para **alta disponibilidade de managers** é garantir que você tenha um número **ímpar** de managers e distribuir os managers de forma que, mesmo com a falha de uma zona de disponibilidade ou datacenter, você ainda mantenha a maioria de managers para formar o **quorum**.

A distribuição **ideal para sete managers** seria **3-3-1** ou **3-2-2**. Isso garantiria que você sempre teria a maioria de managers em qualquer falha de zona de disponibilidade, o que é crucial para a operação correta do Swarm. Com a distribuição **4-2-1**, se o datacenter com 4 managers falhar, o restante dos managers não manteria o quorum e o cluster ficaria inoperante.

### Resumo:
A distribuição **4-2-1** **não é recomendada** para garantir a alta disponibilidade dos managers em um cluster Swarm. A resposta correta é **B**.

---

### Questão nº 117  
Este comando listará todos os nós em um cluster Swarm a partir da linha de comando?  
**Solução:** `docker swarm nodes`  

**A. Sim**  
**B. Não**  

**Resposta: B**  

**Explicação:**

O comando **`docker swarm nodes`** **não é válido** para listar todos os nós de um cluster Swarm. O comando correto para listar os nós de um cluster Swarm é **`docker node ls`**, que deve ser executado em um **nó manager** para exibir os detalhes de todos os nós no Swarm.

O comando **`docker swarm`** é usado para **gerenciar o Swarm em si**, como **iniciar** (`docker swarm init`) ou **adicionar** nós ao Swarm (`docker swarm join`), mas não para listar os nós do cluster.

### Resumo:
O comando correto para listar todos os nós em um cluster Swarm é **`docker node ls`**. A resposta correta é **B**.

---

### Questão nº 118  
Este é o propósito do **Docker Content Trust**?  
**Solução:** Verificar e criptografar o TLS do registro Docker.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O **Docker Content Trust** (DCT) **não** tem como propósito verificar ou criptografar o TLS do registro Docker. O **Docker Content Trust** é uma funcionalidade que permite aos usuários verificar a **integridade** e o **editor** das imagens de contêiner que são puxadas ou implantadas a partir de um servidor de registro, usando assinaturas digitais feitas por um servidor Notary. A principal função do DCT é garantir que as imagens não foram **alteradas** ou **modificadas** de forma maliciosa por qualquer outra pessoa além do **publicador original**.

A verificação e a criptografia do TLS no registro Docker são **mecanismos separados**, que garantem a segurança da comunicação entre o cliente Docker e o servidor de registro, mas não fazem parte da funcionalidade do Docker Content Trust.

### Resumo:
O **Docker Content Trust** não lida com a verificação ou criptografia do TLS do registro Docker. A resposta correta é **B**.

---

### Questão nº 119  
Sua organização possui uma solução de logging centralizada, como o **Splunk**.  
Este comando configurará um container Docker para exportar os logs do container para a solução de logging?  
**Solução:** `docker run --log-driver=splunk` para cada container em tempo de execução.

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

A solução proposta **está correta**. O comando `docker run --log-driver=splunk` é utilizado para configurar o **driver de logs** de um container, e o **driver Splunk** é um plugin que envia os logs do container para o **HTTP Event Collector (HEC)** no **Splunk Enterprise** ou no **Splunk Cloud**.

No entanto, para que o Splunk funcione corretamente, você também precisa especificar opções adicionais com o `--log-opt`, como o **token Splunk**, a **URL**, **source**, **sourcetype**, **index**, entre outros parâmetros necessários para enviar os logs para o Splunk.

Exemplo de comando completo:
```bash
docker run --log-driver=splunk --log-opt splunk-url=https://splunk.example.com:8088 --log-opt splunk-token=your-token --log-opt splunk-source=my-source --log-opt splunk-sourcetype=my-sourcetype my-container
```

### Resumo:
A solução está correta. O comando `docker run --log-driver=splunk` configura o Docker para enviar logs para o Splunk, desde que você também forneça os parâmetros adicionais necessários para a configuração. A resposta correta é **A**.

---

### Questão nº 120 
Sua organização possui uma solução de logging centralizada, como o **Splunk**.  
Este comando configurará um container Docker para exportar os logs do container para a solução de logging?  
**Solução:** Defina as chaves **log-driver** e **log-opt** com os valores para a solução de logging (Splunk) no arquivo **daemon.json**.

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

A solução está **correta**. Para configurar o Docker para exportar logs de containers para uma solução de logging centralizada como o **Splunk**, você pode definir o **log-driver** e as opções **log-opt** no arquivo de configuração do Docker **`daemon.json`**. Essa configuração irá tornar o **driver Splunk** o driver de logs padrão para todos os containers, de modo que os logs serão enviados automaticamente para o **HTTP Event Collector** do **Splunk**.

A configuração no arquivo **`daemon.json`** pode ser semelhante a isto:

```json
{
  "log-driver": "splunk",
  "log-opts": {
    "splunk-url": "https://splunk.example.com:8088",
    "splunk-token": "your-token",
    "splunk-source": "docker-logs",
    "splunk-sourcetype": "docker",
    "splunk-index": "docker-logs"
  }
}
```

Após modificar o arquivo **`daemon.json`**, será necessário reiniciar o serviço Docker para aplicar as configurações.

### Resumo:
Configurar o **daemon.json** com o **log-driver** e **log-opt** para o Splunk irá garantir que todos os containers enviem seus logs para o Splunk automaticamente. A resposta correta é **A**.

---

### Questão nº 121  
Duas equipes de desenvolvimento em sua organização usam Kubernetes e querem implantar suas aplicações enquanto garantem que os recursos específicos do Kubernetes, como segredos, sejam agrupados para cada aplicação.  
Esta é uma maneira de conseguir isso?  
**Solução:** Criar um único pod e adicionar todos os recursos necessários para cada aplicação.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

Criar um único pod e adicionar todos os recursos necessários para cada aplicação **não é uma boa prática** para agrupar recursos específicos do Kubernetes para cada aplicação. 

Os **pods** são a menor unidade de implantação no Kubernetes e são projetados para rodar um único container ou um conjunto de containers intimamente acoplados que compartilham os mesmos recursos de rede e armazenamento. Colocar várias aplicações dentro de um único pod tornaria difícil gerenciar, escalar e atualizar essas aplicações de maneira independente, o que é contra as práticas recomendadas do Kubernetes.

Uma abordagem melhor seria usar **namespaces**. Os **namespaces** são clusters lógicos dentro de um cluster físico que podem isolar recursos, políticas e configurações para diferentes aplicações. Isso permite agrupar recursos de maneira organizada, incluindo **secrets**, que são objetos do Kubernetes usados para armazenar informações sensíveis, como senhas, tokens e chaves. Isso oferece melhor isolamento e controle sobre os recursos de cada aplicação.

### Resumo:
A melhor abordagem seria usar **namespaces** para agrupar recursos de maneira organizada e isolada, e não colocar todas as aplicações em um único pod. A resposta correta é **B**.

---

### Questão nº 122  
Esta é uma maneira de configurar o mecanismo Docker para usar um registro sem um certificado TLS confiável?  
**Solução:** Defina **IGNORE_TLS** no arquivo de configuração **`daemon.json`**.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A solução proposta **não está correta**. Não existe uma opção chamada **IGNORE_TLS** no arquivo de configuração **`daemon.json`**. O **daemon.json** é usado para configurar várias opções do mecanismo Docker, como logs, armazenamento, rede e segurança. Para configurar o Docker para usar um registro sem um certificado TLS confiável, você deve seguir uma abordagem diferente:

1. **Adicionar o certificado à raiz de certificados confiáveis** do sistema, copiando o arquivo do certificado para o diretório **`/etc/docker/certs.d/<registry-hostname>/`** em cada host Docker.
   
2. Ou **configurar o Docker para permitir registros inseguros**. Para isso, você deve adicionar o nome do host ou o endereço IP do registro à chave **`"insecure-registries"`** no arquivo **`daemon.json`**.

Exemplo de configuração no **`daemon.json`** para permitir registros inseguros:
```json
{
  "insecure-registries": ["my-registry.example.com"]
}
```

### Resumo:
A opção **IGNORE_TLS** não existe no Docker e não pode ser usada dessa forma. A maneira correta de configurar um registro sem um certificado TLS confiável é adicionar o registro à lista de **insecure-registries** ou configurar corretamente os certificados. A resposta correta é **B**.

---

### Questão nº 123  
Esta sequência de etapas excluirá completamente uma imagem do disco no Docker Trusted Registry?  
**Solução:** Excluir a imagem e excluir o repositório de imagens do Docker Trusted Registry.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

Excluir uma imagem e seu repositório no Docker Trusted Registry **não excluirá completamente a imagem do disco**. Quando você exclui um repositório ou uma tag, apenas a referência para a imagem é removida, mas a imagem em si ainda permanece armazenada como um **blob** no disco, acessível através de seu **digest**.

Para excluir completamente a imagem do disco, é necessário:

1. Habilitar o recurso de **deletação** na configuração do registro.
2. Usar a **API** para excluir a imagem com base no seu **manifesto**.
3. Após a exclusão, é necessário **executar o coletor de lixo** (garbage collector) para liberar o espaço em disco.

Alternativamente, você poderia excluir manualmente os arquivos da imagem do diretório de armazenamento do registro, mas isso não é recomendado e pode levar a inconsistências.

### Resumo:
A exclusão de uma imagem e de seu repositório no Docker Trusted Registry não a remove completamente do disco. A resposta correta é **B**.

---

### Questão nº 124  
Este comando irá montar o diretório **/data** do host no contêiner Ubuntu em modo somente leitura?  
**Solução:** `docker run -add-volume /data /mydata -read-only ubuntu`

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O comando `docker run -add-volume /data /mydata -read-only ubuntu` **não funcionará** para montar o diretório **/data** do host no contêiner **Ubuntu** em modo somente leitura. Isso ocorre porque o comando contém **erros de sintaxe** e **opções inválidas**. A maneira correta de montar um diretório do host em modo somente leitura no contêiner seria:

```bash
docker run --mount type=bind,source=/data,target=/mydata,readonly ubuntu
```

Aqui está o que cada parte faz:
- `--mount type=bind`: Especifica que estamos montando um diretório do host (e não criando um volume Docker).
- `source=/data`: O diretório do host.
- `target=/mydata`: O ponto de montagem no contêiner.
- `readonly`: Monta o diretório como somente leitura no contêiner.

Portanto, a resposta correta é **B**.

---

### Questão nº 125  
Você adiciona um novo usuário à organização de engenharia no DTR.  
Esta ação concede a ele acesso de leitura/gravação ao repositório **engineering/api**?  
**Solução:** Adicionar o usuário diretamente à lista de usuários com acesso de leitura/gravação na guia de **Permissões** do repositório.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

Adicionar um novo usuário à organização de engenharia no Docker Trusted Registry (DTR) **não concederá automaticamente** acesso de leitura/gravação ao repositório **engineering/api**. Isso acontece porque as permissões do repositório não são herdadas a partir do nível da organização, mas sim configuradas separadamente para cada repositório. Para conceder acesso de leitura/gravação ao repositório específico, o usuário deve ser adicionado diretamente à lista de permissões do repositório na guia **Permissões**.

Portanto, a resposta correta é **B**.

---

### Questão nº 126  
Esta funcionalidade do kernel Linux limita o acesso de um contêiner Docker aos recursos do host, como CPU ou memória?  
**Solução:** namespaces

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

Os **namespaces** são uma funcionalidade do kernel Linux que isolam os contêineres uns dos outros e do sistema host. Eles limitam o acesso de um contêiner aos recursos do host, como CPU ou memória, criando um namespace separado para cada aspecto de um contêiner, como IDs de processo, interfaces de rede, IDs de usuário, entre outros. Dessa forma, um contêiner só pode ver e usar os recursos pertencentes ao seu próprio namespace, não podendo acessar os recursos de outros contêineres ou do host.

Portanto, a resposta correta é **A**.

---

### Questão nº 127  
Esta configuração alcançará tolerância a falhas para os gerentes em um swarm?  
**Solução:** um número ímpar de nós gerentes, totalizando mais de dois.

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

Para garantir a tolerância a falhas em um **Docker Swarm**, é necessário ter um número ímpar de nós gerentes. Isso é importante porque o Swarm usa um mecanismo de consenso baseado em quorum. Com um número ímpar de gerentes, o Swarm pode tolerar a falha de até metade dos nós gerentes, desde que o quorum restante seja alcançado. Com mais de dois nós gerentes, o swarm pode continuar funcionando mesmo se um ou mais nós falharem.

Portanto, a resposta correta é **A**.

---

### Questão nº 128  
As tentativas de um usuário de definir a hora do sistema dentro de um contêiner Docker não são bem-sucedidas.  
**Isso pode estar bloqueando essa operação?**  
**Solução:** SELinux

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

O **SELinux** (Security-Enhanced Linux) pode estar bloqueando a operação de definição da hora do sistema dentro de um contêiner Docker. O SELinux é um mecanismo de segurança que aplica políticas de controle de acesso obrigatório (MAC) em sistemas Linux. Ele pode restringir certas operações no contêiner, como a modificação de configurações de sistema (por exemplo, o tempo do sistema), se não houver permissões adequadas.

No caso de um contêiner tentando alterar a hora do sistema, o SELinux pode impedir essa ação se a política de segurança não permitir esse tipo de operação. Para permitir que o contêiner defina a hora do sistema, seria necessário ajustar as políticas do SELinux ou usar a opção **--cap-add=SYS_TIME** ao executar o contêiner para conceder permissões adequadas.

Portanto, a resposta correta é **A**.


---

### Questão nº 129  
Este comando garantirá que o tráfego overlay entre as tarefas de serviço seja criptografado?  
**Solução:** `docker network create -d overlay -o encrypted=true <network-name>`

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

O comando `docker network create -d overlay -o encrypted=true <network-name>` irá garantir que o tráfego overlay entre as tarefas de serviço seja criptografado. Esse comando cria uma rede overlay com a opção de criptografia ativada. Ao usar essa configuração, o Docker criará túneis IPSEC entre todos os nós onde as tarefas dos serviços são agendadas, garantindo que os dados trocados entre os contêineres em diferentes nós da rede overlay sejam seguros. A criptografia usa o algoritmo AES em modo GCM e os nós gerentes automaticamente fazem a rotação das chaves a cada 12 horas.

Portanto, a resposta correta é **A**.

---

### **Questão nº 130**  
Sua organização possui uma solução de log centralizada, como o Splunk.  
Isso configurará um contêiner Docker para exportar logs de contêiner para a solução de log?  
**Solução:** Defina as chaves `log-driver` e `log-opt` para os valores da solução de log (Splunk) no arquivo `daemon.json`.

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

Para configurar um contêiner Docker para exportar logs de contêiner para uma solução de log, como o Splunk, você precisa definir as chaves `log-driver` e `log-opt` para os valores da solução de log no arquivo `daemon.json`. Isso permitirá que o driver de log do Splunk seja utilizado, que envia logs de contêiner para o HTTP Event Collector no Splunk Enterprise e no Splunk Cloud. Além disso, você também pode usar as flags de linha de comando `--log-driver` e `--log-opt` com o comando `docker run` para usar o driver do Splunk para um contêiner específico.

Portanto, a resposta correta é **A**.

---

### Questão nº 131  
Essa é a finalidade do Docker Content Trust?  
**Solução:** Habilitar TLS mútuo entre o cliente Docker e o servidor.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

Docker Content Trust (DCT) é um recurso que permite aos usuários verificar a integridade e o editor das imagens de contêiner que eles puxam ou implantam de um servidor de registro, assinado em um servidor Notary. No entanto, o Docker Content Trust **não habilita** TLS mútuo entre o cliente Docker e o servidor. O TLS mútuo é um mecanismo de segurança distinto que garante comunicação criptografada e autenticada entre o cliente e o servidor. O DCT é baseado em assinaturas digitais e na Framework de Atualização (TUF) para fornecer confiança sobre coleções arbitrárias de dados.

Portanto, a resposta correta é **B**.

---

### Questão nº 132  
Este comando exibe todos os pods no cluster que estão rotulados como 'env: development'?  
**Solução:** `'kubectl get pods --all-namespaces -I env=development'`

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O comando `kubectl get pods --all-namespaces -I env=development` **não** exibe todos os pods no cluster rotulados como 'env: development'. O motivo é que o flag `-I` não é uma opção válida para o comando `kubectl get pods`. A flag correta a ser utilizada é `--selector` ou `-l`, que permite filtrar pods com base em rótulos.

Por exemplo, para exibir todos os pods que possuem o rótulo `env=development`, o comando correto seria:

```bash
kubectl get pods --selector env=development
```

Ou:

```bash
kubectl get pods -l env=development
```

A flag `--all-namespaces` pode ser usada para listar os pods em todos os namespaces. Portanto, o comando correto seria:

```bash
kubectl get pods --all-namespaces --selector env=development
```

Ou:

```bash
kubectl get pods --all-namespaces -l env=development
```

Portanto, a resposta correta é **B**.

---

### Questão nº 133  
A política de segurança de uma empresa especifica que os containers de desenvolvimento e produção devem ser executados em nós separados em um cluster Swarm. Pode-se usar isso para agendar containers e atender aos requisitos da política de segurança?  
**Solução:** Variáveis de ambiente

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

As variáveis de ambiente não podem ser usadas para agendar containers de acordo com os requisitos de segurança. Elas são usadas para passar dados de configuração para os containers, mas não para controlar onde os containers são executados. 

Para agendar containers para serem executados em nós separados em um cluster Swarm, você deve usar **rótulos de nós** e **restrições de serviço**. Os rótulos de nós são pares chave-valor que você pode atribuir aos nós para organizá-los em grupos. As restrições de serviço são expressões usadas para limitar onde um serviço pode ser executado com base nos rótulos dos nós.

Por exemplo, você pode rotular alguns nós com `env=dev` e outros com `env=prod`, e então usar a restrição `--constraint node.labels.env==dev` ou `--constraint node.labels.env==prod` ao criar um serviço, garantindo que o serviço seja executado apenas nos nós com o rótulo correspondente.

Portanto, a resposta correta é **B**.

---

### Questão nº 134  
Este comando exibirá uma lista de volumes para um container específico?  
**Solução:** `docker container inspect nginx`

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

O comando `docker container inspect nginx` exibirá detalhes sobre o container específico chamado **nginx**, incluindo uma seção chamada **Mounts**. Essa seção mostra a origem, o destino, o modo, o tipo e a propagação de cada volume montado no container. 

Por exemplo, a saída do comando pode mostrar que o container **nginx** possui dois volumes: um é um **bind mount** do diretório `/var/log/nginx` do host para o diretório `/var/log/nginx` no container, e o outro é um volume anônimo criado pelo Docker em `/var/lib/docker/volumes/...` e montado no diretório `/etc/nginx/conf.d` do container.

Portanto, a resposta correta é **A**.

---

### Questão nº 135  
Os tentativas de um usuário de configurar o horário do sistema de dentro de um container Docker foram malsucedidas. Isso pode estar bloqueando essa operação?  
**Solução:** comunicação interprocessos

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A comunicação interprocessos (IPC) não é o motivo pelo qual a operação de definir o horário do sistema dentro de um container Docker pode falhar. Em contêineres, os problemas para definir a hora geralmente estão relacionados a permissões e à falta de acesso a recursos do sistema, como a capacidade de alterar o relógio do sistema. Por padrão, containers Docker não têm permissões para modificar a hora do sistema do host.

Para permitir que um container altere a hora do sistema, você precisaria conceder permissões especiais, como a capacidade `SYS_TIME` usando o flag `--cap-add` no comando `docker run`. O IPC, por outro lado, refere-se à comunicação entre os processos dentro do container e não está relacionado ao ajuste de hora do sistema.

Portanto, a resposta correta é **B**.

---

### Questão nº 136  
Isso é uma função do UCP?  
**Solução:** força o uso de imagens assinadas para o deployment no cluster

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

Sim, isso é uma função do UCP (Universal Control Plane). O UCP integra-se com o Docker Trusted Registry (DTR) e oferece controle de acesso e segurança para imagens em um cluster Docker. Através do Docker Content Trust (DCT), as imagens podem ser assinadas para garantir sua autenticidade e integridade. O UCP pode ser configurado para garantir que apenas imagens assinadas sejam implantadas no cluster, verificando as assinaturas das imagens antes que elas sejam utilizadas, aumentando a segurança do ambiente de execução.

Portanto, a resposta correta é **A**.

---

### Questão nº 137  
Essa sequência de etapas apagará completamente uma imagem do disco no Docker Trusted Registry?  
**Solução:** Deletar a imagem e deletar o repositório de imagens no Docker Trusted Registry.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A sequência de etapas mencionada (deletar a imagem e o repositório) **não apagará completamente a imagem do disco** no Docker Trusted Registry. Quando você exclui uma imagem ou o repositório, o que é removido são apenas as referências a esses objetos, mas os dados da imagem ainda permanecem no disco. Para excluir completamente a imagem do disco, é necessário executar a coleta de lixo (garbage collection) no servidor de registro, o que remove os blobs não referenciados. O comando para isso é `bin/registry garbage-collect /path/to/config.yml`.

Portanto, a resposta correta é **B**.

---

### Questão nº 138  
Duas equipes de desenvolvimento na sua organização usam o Kubernetes e querem implantar suas aplicações enquanto garantem que os recursos específicos do Kubernetes, como segredos, sejam agrupados para cada aplicação.  
**Solução:** Criar uma coleção para cada aplicação.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

Criar uma **coleção** para cada aplicação **não é uma forma de agrupar recursos** no Kubernetes. O termo "coleção" é utilizado pelo **Ansible** para descrever um pacote de conteúdo relacionado, que pode ser usado para automatizar a gestão de recursos, mas não é um conceito nativo do Kubernetes. No Kubernetes, para agrupar recursos como **segredos** e **configurações** para cada aplicação, é necessário usar **namespaces**.

Um **namespace** é uma partição lógica do cluster que permite isolar recursos e aplicar políticas a eles. Para cada aplicação, você pode criar um namespace e armazenar os segredos e outros recursos dentro desse namespace, garantindo que os recursos de cada aplicação sejam isolados e controlados. Para criar um namespace, você pode usar o comando `kubectl create namespace` ou um arquivo YAML. Da mesma forma, para criar segredos dentro de um namespace, você pode usar `kubectl create secret` com a opção `--namespace` ou definir o campo `metadata.namespace` em um arquivo YAML.

Portanto, a resposta correta é **B**.

---

### Questão nº 139 
É este um tipo de namespace do kernel Linux que proporciona isolamento de contêineres?  
**Solução:** Rede

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

**Network** (Rede) é, de fato, um tipo de **namespace do kernel Linux** que proporciona **isolamento de contêineres**. Os **network namespaces** isolam os recursos do sistema associados à rede, como interfaces de rede, endereços IP, tabelas de roteamento, regras de firewall, etc. Cada namespace de rede tem sua própria **pilha de rede virtual**, e os processos em diferentes namespaces de rede podem se comunicar por meio de dispositivos de rede virtuais ou túneis.

O Docker usa **network namespaces** para criar redes isoladas para seus contêineres, permitindo que os usuários personalizem a configuração e a conectividade de rede de cada contêiner de forma independente. Isso é fundamental para garantir que contêineres possam ter redes privadas e isoladas, com endereços IP separados, por exemplo.

Portanto, a resposta correta é **A**.

---

### Questão nº 140  
Este conjunto de comandos pode identificar a(s) porta(s) publicada(s) para um contêiner?  
**Solução:** ‘docker container inspect", docker port’

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O conjunto de comandos **`docker container inspect`** e **`docker port`** **não** pode identificar diretamente as portas publicadas para um contêiner de forma eficiente. 

- O comando **`docker container inspect`** retorna informações detalhadas sobre o contêiner, como seu ID, nome, estado, configurações de rede, montagens, etc. No entanto, ele **não** mostra mapeamentos de portas entre o contêiner e o host.
  
- O comando **`docker port`** pode listar os mapeamentos de portas, mas precisa do nome ou ID do contêiner como argumento. Portanto, para obter a(s) porta(s) publicada(s), você deve usar o comando **`docker port`** com o nome ou ID do contêiner.

Para identificar corretamente as portas publicadas de um contêiner, você precisaria usar **os dois comandos juntos**, como mostrado na solução:

```bash
docker port $(docker container inspect -f '{{.Name}}' CONTAINER)
```

Isso garante que o comando **`docker port`** receba o nome ou ID do contêiner retornado pelo **`docker container inspect`**.

Por isso, a resposta correta é **B**.

---

### Questão nº 141  
Este comando cria um serviço swarm que escuta apenas na porta 53 usando o protocolo UDP?  
**Solução:** ‘docker service create -name dns-cache -p 53:53 -udp dns-cache’

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O comando **`docker service create -name dns-cache -p 53:53 -udp dns-cache`** **não** cria um serviço Swarm que escuta apenas na porta 53 usando o protocolo UDP, pois há erros de sintaxe e opções inválidas.

- O comando correto para criar um serviço Swarm que escuta apenas na porta 53 usando o protocolo UDP seria:

```bash
docker service create --name dns-cache --publish published=53,target=53,protocol=udp dns-cache
```

- O **`-udp`** não é uma opção válida para o comando **`docker service create`**.
- O comando **`--publish`** (ou **`-p`**) deve ser usado para mapear as portas do contêiner para as portas do host. O correto é especificar o protocolo como **`protocol=udp`** ao definir o mapeamento da porta.

Por isso, a resposta correta é **B**.

---

### Questão nº 142  
Este é um método para configurar o Docker engine para usar um registro sem um certificado TLS confiável?  
**Solução:** Definir **INSECURE_REGISTRY** no arquivo de configuração **/etc/docker/default**.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

Embora definir **INSECURE_REGISTRY** no arquivo **/etc/docker/default** possa parecer uma forma de permitir o uso de um registro sem um certificado TLS confiável, a maneira correta de configurar o Docker para usar um registro inseguro é adicionando o nome do registro ao parâmetro **insecure-registries** no arquivo **daemon.json**, não em **/etc/docker/default**.

A configuração correta seria:

1. Editar o arquivo **/etc/docker/daemon.json** (ou criá-lo, se não existir) e adicionar o seguinte:

```json
{
  "insecure-registries": ["meuregistro.local"]
}
```

2. Reiniciar o Docker para aplicar a configuração:

```bash
sudo systemctl restart docker
```

Isso permite que o Docker se conecte ao registro sem verificar o certificado TLS, mas deve ser feito com cautela, pois isso pode expor o sistema a riscos de segurança.

A resposta correta é **B**, pois a opção **INSECURE_REGISTRY** não é válida ou correta no arquivo **/etc/docker/default** para essa configuração.

---

### Questão nº 143 
Este comando lista todos os nós em um cluster Swarm a partir da linha de comando?  
**Solução:** `'docker swarm nodes'`

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O comando **docker swarm nodes** não é válido para listar os nós em um cluster Swarm. O comando correto para listar todos os nós de um Swarm é **docker node ls**.

- **docker node ls**: Este comando lista todos os nós no cluster Swarm, mostrando o ID, hostname, status, disponibilidade, status do gerente e versão do Docker de cada nó.
  
- **docker swarm**: Este comando é utilizado para gerenciar o Swarm em si, como inicializar, adicionar ou remover nós, e realizar operações no próprio Swarm.

Portanto, a resposta correta é **B** (Não), porque o comando correto para listar os nós de um Swarm é **docker node ls**.

---

### Questão nº 144  
Sete gerentes estão em um cluster Swarm.  
É assim que eles devem ser distribuídos em três datacentros ou zonas de disponibilidade?  
**Solução:** 5-1-1  

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A distribuição sugerida de **5-1-1** para os 7 gerentes em 3 datacentros ou zonas de disponibilidade não é a mais ideal para garantir alta disponibilidade e evitar cenários de divisão de quorum. Embora tenha 7 gerentes, o modelo de distribuição 5-1-1 cria um ponto único de falha na zona com 5 gerentes. Se essa zona falhar, os 2 gerentes restantes (em zonas diferentes) não conseguirão formar o quorum necessário para manter o funcionamento do cluster.

Uma distribuição mais equilibrada seria **3-2-2** ou **2-2-2-1**, pois essas opções oferecem maior redundância e resistência a falhas, garantindo que o quorum seja preservado mesmo em caso de falhas em uma zona.

Portanto, a resposta correta é **B** (Não), pois a solução 5-1-1 não oferece uma redundância ideal para a alta disponibilidade do cluster.

---

### Questão nº 145  
Essas condições são suficientes para o Kubernetes provisionar dinamicamente um persistentVolume, assumindo que não há limitações na quantidade e tipo de armazenamento externo disponível?  
**Solução:** Um persistentVolumeClaim é criado especificando um provisionador pré-definido.  

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A criação de um **persistentVolumeClaim** (PVC) com um provisionador pré-definido não é suficiente para que o Kubernetes provisionar dinamicamente um **persistentVolume** (PV). Embora o provisionador seja importante para a criação de volumes sob demanda, há outros fatores e configurações necessárias para permitir o provisionamento dinâmico. Especificamente, você precisa de uma **StorageClass**, que define o tipo de armazenamento e o provisionador a ser utilizado.

A **StorageClass** descreve as diferentes classes de armazenamento disponíveis no cluster e é essencial para que o Kubernetes saiba como provisionar e configurar o armazenamento dinamicamente. Sem a configuração de uma **StorageClass**, o PVC não será capaz de acionar o provisionamento dinâmico.

Portanto, a criação do PVC com um provisionador pré-definido é apenas parte do processo, sendo necessário também configurar corretamente a **StorageClass** para que o provisionamento dinâmico de volumes seja realizado corretamente.

**Resposta correta: B** (Não).

---

### Questão nº 146  
Este comando cria um serviço no Swarm que só escuta na porta 53 usando o protocolo UDP?  
**Solução:** `'docker service create --name dns-cache -p 53:53/udp dns-cache'`

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

O comando `'docker service create --name dns-cache -p 53:53/udp dns-cache'` cria um serviço no Swarm que escuta apenas na porta 53 utilizando o protocolo UDP. Isso ocorre porque a opção `-p` especifica o mapeamento da porta entre o host e o serviço, e o sufixo `/udp` indica que o protocolo UDP deve ser utilizado para a comunicação.

A porta 53 é comumente usada para serviços de DNS, e o protocolo UDP é o protocolo padrão para o DNS devido à sua baixa sobrecarga e natureza de comunicação sem conexão.

Portanto, o comando está correto para criar um serviço de DNS usando UDP.

**Resposta correta: A** (Sim).

---

### Questão nº 147  
A verificação de segurança do DTR detectará isso?  
**Solução:** Práticas de configuração de imagem inadequadas, como portas expostas ou inclusão de compiladores em imagens de produção.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A verificação de segurança do Docker Trusted Registry (DTR) não detecta práticas inadequadas de configuração de imagem, como portas expostas ou a inclusão de compiladores em imagens de produção. O objetivo das verificações de segurança do DTR é encontrar vulnerabilidades conhecidas nas imagens, com base em bancos de dados como MITRE CVE ou NIST NVD.

No entanto, práticas como a inclusão de compiladores em imagens de produção ou a exposição de portas podem ser consideradas más práticas de segurança, mas não são detectadas pela análise de vulnerabilidades do DTR. Para verificar essas questões de configuração, você deve usar outras ferramentas, como o **Dockerfile Linter** ou o **Docker Bench for Security**, que verificam as práticas de segurança e a conformidade das imagens.

Portanto, a resposta correta é **B** (Não).

---

### Questão nº 148  
Este comando montará o diretório '/data' do host para o contêiner Ubuntu em modo somente leitura?  
**Solução:** 'docker run --add-volume /data /mydata -read-only ubuntu'

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O comando fornecido está incorreto devido à sintaxe inválida e ao uso de opções incorretas. A opção `--add-volume` não é válida, e o parâmetro `-read-only` também está errado. Para montar um diretório do host no contêiner em modo somente leitura, você deve usar a opção correta para volumes com a flag `--mount` ou `-v`.

A forma correta de montar o diretório `/data` do host no contêiner `ubuntu` em modo somente leitura seria:

```bash
docker run --mount type=bind,source=/data,target=/mydata,readonly ubuntu
```

Aqui, `--mount` especifica que você está montando um diretório de volume do host (`type=bind`), o diretório do host é `/data`, o destino dentro do contêiner é `/mydata`, e a opção `readonly` garante que o contêiner possa apenas ler os dados do diretório, mas não modificá-los.

Portanto, a resposta correta é **B** (Não).

---

### Questão nº 149  
Sua organização tem uma solução de log centralizada, como o Splunk.  
Este comando configurará um contêiner Docker para exportar os logs do contêiner para a solução de logs?  
**Solução:** 'docker logs <container-id>'

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

O comando `docker logs <container-id>` não configura um contêiner Docker para exportar logs para uma solução de logs externa, como o Splunk. Este comando é utilizado para **exibir** os logs de um contêiner já existente, mas não para enviá-los a um serviço externo.

Para exportar os logs de um contêiner para uma solução de log, como o Splunk, você precisa configurar o contêiner para usar o **driver de log** adequado. O Docker oferece vários drivers de log, incluindo o driver do Splunk, que permite enviar os logs diretamente para o **HTTP Event Collector (HEC)** do Splunk.

A configuração pode ser feita ao executar o contêiner com o parâmetro `--log-driver=splunk`, incluindo as opções necessárias como o token do Splunk e a URL de coleta. Além disso, é possível configurar o driver de log do Splunk como o driver de log padrão editando o arquivo `daemon.json` do Docker.

Portanto, a resposta correta é **B** (Não).

---

### Questão nº 150  
No contexto de um cluster em modo swarm, isso descreve um nó?  
**Solução:** uma instância do Docker CLI conectada ao swarm.

**A. Sim**  
**B. Não**

**Resposta: B**

**Explicação:**

A afirmação não descreve corretamente um nó no contexto de um cluster em modo swarm. 

Um **nó** em um cluster swarm é uma **máquina física ou virtual** que executa o Docker Engine (versão 1.12 ou posterior) em modo swarm. Ele pode ser um **nó gerente** ou **nó trabalhador**, dependendo da função que ele desempenha no cluster. 

Por outro lado, uma **instância do Docker CLI** conectada ao swarm é simplesmente um **cliente** que interage com o cluster usando a **API do Docker**. O Docker CLI pode ser usado para criar e gerenciar o swarm, adicionar nós ao cluster, implantar serviços e monitorar o comportamento do swarm, mas **não é considerado um nó** em si.

Portanto, a resposta correta é **B** (Não).

---

### A questão 151 refere-se ao processo de exclusão de uma imagem no Docker Trusted Registry (DTR) e se a execução de uma coleta de lixo (garbage collection) após a exclusão da imagem a remove completamente do disco.

**Solução:** Deletar a imagem e executar a coleta de lixo no Docker Trusted Registry.

**A. Sim**  
**B. Não**

**Resposta: A**

**Explicação:**

Para remover completamente uma imagem do Docker Trusted Registry, você precisa não apenas deletar a imagem, mas também executar a **garbage collection** (coleta de lixo). Quando você deleta uma imagem no DTR, o repositório e as referências à imagem são removidos, mas as camadas subjacentes (blobs) da imagem ainda podem permanecer no armazenamento até que o processo de garbage collection seja executado. 

A coleta de lixo é responsável por limpar as camadas de imagens não referenciadas (ou seja, aquelas que não estão associadas a nenhuma imagem ou repositório ativo), liberando o espaço no disco.

Portanto, a sequência de **deletar a imagem e rodar a coleta de lixo** **remove completamente a imagem do disco** no Docker Trusted Registry.

---

### A resposta para a questão está correta.

**Pergunta:**
Is this a type of Linux kernel namespace that provides container isolation?
Solution: Authentication

**Resposta: B (Não)**

**Explicação:**

A **autenticação** não é um tipo de namespace do kernel do Linux usado para isolar containers. O conceito de **namespaces** no Linux é uma funcionalidade do kernel que permite isolar recursos do sistema entre diferentes processos ou containers, criando um ambiente de execução isolado. As namespaces que o Docker utiliza para isolar containers incluem:

- **PID (Process ID):** Isola os IDs dos processos entre os containers.
- **USER:** Isola os IDs de usuário e grupo entre containers.
- **UTS (UNIX Timesharing System):** Isola o nome do host e domínio entre containers.
- **IPC (Inter-process communication):** Isola os mecanismos de comunicação entre processos.
- **MNT (Mount):** Isola os sistemas de arquivos entre containers.
- **NET (Network):** Isola as configurações de rede entre containers.

A **autenticação**, embora seja uma parte importante de segurança e controle de acesso, não é uma namespace no Linux e não fornece isolamento entre containers. Ela está mais relacionada ao controle de acesso de usuários e sistemas a recursos, mas não faz parte do isolamento de containers em si.

---

### 153
**Pergunta:**  
Esta sequência de comandos irá listar todos os nós em um cluster swarm a partir da linha de comando?  
**Solução:** 'docker inspect nodes'  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
O comando `docker inspect nodes` não irá listar todos os nós em um cluster swarm a partir da linha de comando. Isso ocorre porque o `docker inspect` requer um ou mais nomes ou IDs dos objetos a serem inspecionados. Para listar todos os nós de um cluster swarm, você precisa usar o comando `docker node ls`, que exibirá informações como o ID do nó, nome do host, status, disponibilidade e função.

--- 

### 154
**Pergunta:**  
A seguinte definição de Docker Compose é implantada como uma stack:
```yaml
version: '3.1'
services:
  app:
    image: app:1.0
    healthcheck:
      test: "curl --fail http://localhost/health || exit 1"
      interval: 10s
      timeout: 5s
      retries: 3
```
Esta afirmação está correta sobre a definição do health check?  
**Solução:** Os health checks testam a saúde do aplicativo a cada dez segundos. Se o teste falhar, o contêiner será reiniciado três vezes antes de ser remarcado.  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
A definição do health check no arquivo Docker Compose testa a saúde do aplicativo a cada 10 segundos, mas o intervalo total entre as verificações será de 15 segundos (10s de `interval` + 5s de `timeout`). Portanto, o intervalo entre os testes é de 15 segundos e não de 10 segundos. Além disso, a definição de retries (`retries: 3`) indica que o Docker tentará reiniciar o contêiner até três vezes caso o teste falhe. Após três falhas consecutivas, o contêiner será marcado como "unhealthy".

---

### 155
**Pergunta:**  
Uma imagem de aplicativo é executada em múltiplos ambientes, com cada ambiente utilizando certificados e portas diferentes. Esta é uma maneira de provisionar configurações para contêineres em tempo de execução?  
**Solução:** Crie um Dockerfile para cada ambiente, especificando portas e segredos do Docker para os certificados.  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Criar um Dockerfile para cada ambiente, especificando portas e segredos do Docker para certificados, não é uma maneira adequada de provisionar configurações para contêineres em tempo de execução. O Dockerfile é utilizado para construir uma imagem, não para executar um contêiner. Uma vez que a imagem é construída, as configurações definidas no Dockerfile não podem ser alteradas em tempo de execução. Para provisionar configurações em tempo de execução, deve-se utilizar variáveis de ambiente, argumentos de linha de comando, ou ConfigMaps no Kubernetes, por exemplo. Esses métodos permitem modificar ou injetar configurações no momento em que o contêiner é iniciado.

---

### 156
**Pergunta:**  
Esta ação fará o upgrade do Docker Engine CE para o Docker Engine EE?  
**Solução:** Desinstalar o pacote 'docker-ce' antes de instalar o pacote 'docker-ee'.  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Desinstalar o pacote 'docker-ce' antes de instalar o pacote 'docker-ee' não fará o upgrade do Docker Engine CE para o Docker Engine EE. Isso apenas removerá a instalação do Docker Engine CE existente e instalará uma nova instalação do Docker Engine EE. Isso significa que todos os contêineres, imagens, volumes, redes e outros recursos do Docker serão perdidos. Para fazer o upgrade do Docker Engine CE para o Docker Engine EE sem perder dados, é necessário usar a ferramenta de migração fornecida pela Docker ou seguir os passos descritos na documentação do Docker.

---

### Pergunta #:157  
Sete gerentes estão em um cluster Swarm.  
É assim que eles devem ser distribuídos entre três datacenters ou zonas de disponibilidade?  
**Solução:** 3-3-1  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Distribuir sete gerentes entre três datacenters ou zonas de disponibilidade como 3-3-1 não é a melhor forma de garantir alta disponibilidade e tolerância a falhas. Isso ocorre porque, se um dos datacenters com três gerentes falhar, os quatro gerentes restantes não terão quorum para eleger um líder e continuar as operações do swarm. O quorum é o número mínimo de gerentes que deve estar disponível para manter o estado do swarm, e é calculado como (N/2) + 1, onde N é o número total de gerentes. Para sete gerentes, o quorum é cinco, então perder três gerentes fará com que o swarm perca o quorum. Uma forma melhor de distribuir sete gerentes entre três datacenters ou zonas de disponibilidade é 2-2-3, o que permitirá que o swarm sobreviva à falha de qualquer um dos datacenters.

---

### Pergunta #:158  
No Docker Trusted Registry, é assim que um usuário pode impedir que uma imagem, como 'nginx:latest', seja sobrescrita por outro usuário com acesso de push ao repositório?  
**Solução:** Remover o acesso de push de todos os outros usuários.  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Embora remover o acesso de push de todos os outros usuários possa impedir que uma imagem seja sobrescrita, essa não é a única maneira e pode não ser a solução mais eficiente ou prática, especialmente em um ambiente colaborativo. O Docker Trusted Registry (DTR) fornece um recurso chamado "Tags Imutáveis", que pode ser usado para impedir que uma imagem, como 'nginx:latest', seja sobrescrita. Uma vez que uma tag é marcada como imutável, o DTR impedirá qualquer usuário de enviar a mesma tag para o repositório, preservando assim a imagem. Isso permite um melhor controle de versão e evita sobrescritas acidentais. Portanto, a solução para impedir que uma imagem seja sobrescrita não é apenas remover o acesso de push de todos os outros usuários, mas usar os recursos fornecidos pelo DTR, como as "Tags Imutáveis".

---

### Pergunta #:159  
Isso é uma vantagem das construções multi-stage?  
**Solução:** builds de imagens mais rápidas, permitindo a execução paralela das builds do Docker.  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Criar e marcar simultaneamente várias imagens não é uma vantagem das construções multi-stage. As construções multi-stage são um recurso que permite usar várias instruções FROM no seu Dockerfile, cada uma iniciando uma nova etapa da construção. Você pode copiar seletivamente artefatos de uma etapa para outra, deixando para trás tudo o que não deseja na imagem final. Isso ajuda a otimizar o tamanho e a segurança das suas imagens, além de simplificar o processo de construção. No entanto, as construções multi-stage não criam ou marcam várias imagens ao mesmo tempo. Cada Dockerfile produz uma imagem final, que é o resultado da última etapa do Dockerfile. Se você quiser criar e marcar várias imagens a partir de um único Dockerfile, precisará usar a opção --target com o comando docker build e especificar o nome da etapa que deseja construir e marcar.

---

### Pergunta #:160  
Você deseja fornecer um arquivo de configuração para um container em tempo de execução. Este conjunto de ferramentas e etapas do Kubernetes realiza isso?  
**Solução:** Transforme o arquivo de configuração em um objeto configMap, use-o para popular um volume associado ao pod, e monte esse arquivo do volume para o container e caminho apropriados.

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Montar o arquivo de configuração diretamente no pod e container apropriados usando a chave `.spec.containers.configMounts` não é uma maneira válida de fornecer um arquivo de configuração para um container em tempo de execução. A chave `.spec.containers.configMounts` não existe na API do Kubernetes. A maneira correta de fornecer um arquivo de configuração para um container em tempo de execução é usar um **ConfigMap**. Um ConfigMap é um objeto do Kubernetes que armazena dados de configuração como pares chave-valor. Você pode criar um ConfigMap a partir de um arquivo e então montar o ConfigMap como um volume no pod e container. O arquivo de configuração estará disponível como um arquivo no caminho de montagem especificado. Alternativamente, você também pode usar variáveis de ambiente para passar dados de configuração para um container a partir de um ConfigMap.

---

### Pergunta #:161 
Este comando montará o diretório '/data' do host no container ubuntu em modo somente leitura?  
**Solução:** 'docker run -v /data:/mydata --mode readonly ubuntu'

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
O comando `docker run -v /data:/mydata --mode readonly ubuntu` não é válido porque contém alguns erros de sintaxe. A sintaxe correta para executar um container com um volume montado como somente leitura é `docker run -v /data:/mydata:ro ubuntu`. O `:ro` após o caminho do diretório de montagem indica que o volume será montado em modo somente leitura. A opção `--mode readonly` não é válida e não faz parte dos parâmetros aceitos pelo Docker.

---

### Pergunta #:162  
Esta ação fará a atualização do Docker Engine CE para Docker Engine EE?  
**Solução:** Excluir o diretório '/var/lib/docker'.  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Excluir o diretório `/var/lib/docker` não atualizará o Docker Engine CE para Docker Engine EE. Isso apenas removerá todos os dados armazenados pelo Docker, como imagens, containers, volumes e redes, o que pode causar perda de dados e interromper os serviços em execução. Para atualizar do Docker Engine CE para Docker Engine EE, você deve seguir as instruções oficiais do Docker, que envolvem desinstalar o pacote CE e instalar o pacote EE.

---

### Pergunta #:163  
Você deseja montar um armazenamento externo em um caminho específico do sistema de arquivos em um container dentro de um pod do Kubernetes.  
Qual é o conjunto correto de objetos a ser usado para isso?  

A. um persistentVolume na especificação do pod, populado com um persistentVolumeClaim que está vinculado a um volume definido por um storageClass  
B. um storageClass na especificação do pod, populado com um volume que está vinculado a um provisionador definido por um persistentVolume  
C. um volume na especificação do pod, populado com um storageClass que está vinculado a um provisionador definido por um persistentVolume  
D. um volume na especificação do pod, populado com um persistentVolumeClaim vinculado a um persistentVolume definido por um storageClass  

**Resposta:** D  

**Explicação:**  
A opção D é a correta porque, no Kubernetes, os volumes persistentes (persistentVolumes) são criados e gerenciados separadamente e vinculados a persistentVolumeClaims (PVCs). Esses PVCs são então usados para fazer referência a um volume no pod. O `storageClass` define o tipo de armazenamento e o provisionador, mas o PVC e o volume são os objetos principais para montar o armazenamento externo no caminho desejado dentro do container no pod.

---

### Pergunta #:164 
Este comando garantirá que o tráfego overlay entre tarefas de serviço seja criptografado?  
Solução: `docker network create -d overlay --secure`  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
A solução apresentada está incorreta porque o parâmetro `--secure` não é válido para o comando `docker network create`. Para garantir que o tráfego overlay entre as tarefas de serviço seja criptografado, você precisa usar a opção `--opt encrypted`. Por exemplo, o comando correto seria:  

`docker network create -d overlay --opt encrypted my-net`

Esse comando criará uma rede overlay chamada `my-net` com a criptografia habilitada. Isso assegura que o tráfego entre as tarefas de serviço que usam essa rede seja criptografado.

---

### Pergunta #:165  
Este comando exibirá uma lista de volumes para um contêiner específico?  
Solução: `docker volume inspect nginx`

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
O comando `docker volume inspect nginx` não exibirá uma lista de volumes para um contêiner específico, porque o comando `docker volume inspect` espera o nome de um volume como argumento, e não o nome de um contêiner. Para exibir os volumes montados por um contêiner específico, você pode usar o comando `docker inspect` com a opção `--format` e um modelo que extraia as informações de volume do JSON de saída do contêiner.

Por exemplo, para exibir a origem e o destino dos volumes montados pelo contêiner `nginx`, você pode usar o seguinte comando:

```bash
docker inspect --format='{{range .Mounts}}{{.Source}}:{{.Destination}}{{end}}' nginx
``` 

Isso mostrará a origem e o destino dos volumes montados no contêiner `nginx`.

---

### Pergunta #:166
O Kubernetes YAML mostrado abaixo descreve uma `networkPolicy`.

```yaml
appVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: dca
  namespace: default
spec:
  podSelector:
    matchLabels:
      tier: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          tier: api
```

Esta política de rede bloqueará este tráfego?  
Solução: uma solicitação emitida de um pod sem o rótulo `tier: api`, para um pod com o rótulo `tier: backend`.

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
A `networkPolicy` mostrada foi configurada para permitir o tráfego apenas de pods com o rótulo `tier: api` para pods com o rótulo `tier: backend`. Qualquer tráfego originado de um pod sem o rótulo `tier: api` será bloqueado, pois a política de rede especifica que o tráfego de entrada (ingress) é permitido apenas de pods que atendem ao critério `tier: api`. Portanto, a solicitação de um pod sem esse rótulo será bloqueada.

---

### Pergunta #:167  
Você está solucionando problemas de um deployment Kubernetes chamado `api` e deseja ver a tabela de eventos para esse objeto.  
Este comando exibirá a tabela de eventos?  
Solução: `kubectl describe deployment api`

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
O comando `kubectl describe deployment api` exibirá a tabela de eventos para o objeto de deployment chamado `api`, junto com outras informações como rótulos, réplicas, estratégia, condições e o template do pod. A tabela de eventos mostra o histórico de ações que afetaram o deployment, como escalonamento, atualização ou criação de pods. Isso pode ajudar a solucionar problemas com o deployment. Para ver apenas a tabela de eventos, você pode usar a flag `--show-events=true` com o comando.

---

### Pergunta #:168  
Você configura um Docker engine local para impor confiança de conteúdo configurando a variável de ambiente `DOCKER_CONTENT_TRUST=1`.  
Se `myorg/myimage:1.0` não estiver assinado, o Docker bloqueia este comando?  
Solução: `docker image inspect myorg/myimage:1.0`

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
O Docker bloqueará o comando `docker image inspect myorg/myimage:1.0` se a tag da imagem não estiver assinada e a variável de ambiente `DOCKER_CONTENT_TRUST` estiver configurada como 1. Isso ocorre porque o Docker Content Trust (DCT) permite a verificação da integridade e do editor das imagens Docker usando assinaturas digitais. Quando o DCT está ativado, o Docker só permite inspecionar, puxar ou executar imagens que tenham uma assinatura válida. Se a tag da imagem não estiver assinada, o Docker rejeitará o comando e exibirá uma mensagem de erro, como "No valid trust data for 1.0". Para inspecionar uma imagem não assinada, você precisaria desabilitar o DCT configurando `DOCKER_CONTENT_TRUST=0` ou usar a flag `--disable-content-trust` no comando.

---

### Pergunta #:169  
Este conjunto de comandos pode identificar as portas publicadas de um contêiner?  
Solução: `docker network inspect`, `docker port`

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
Sim, o comando `docker port` pode ser usado para identificar as portas publicadas de um contêiner em execução. Ele mostra o mapeamento entre as portas do host e as portas expostas do contêiner. O comando `docker network inspect` também pode fornecer informações sobre as configurações de rede do contêiner, incluindo os mapeamentos de porta. No entanto, é importante observar que o comando `docker network inspect` requer o nome ou ID da rede como argumento, e não o ID do contêiner. Portanto, para obter os detalhes da rede de um contêiner específico, você precisaria primeiro identificar a rede à qual o contêiner está conectado. Esses comandos, quando usados de forma apropriada, podem ajudá-lo a identificar as portas publicadas de um contêiner.

---

### Pergunta #:170  
Este é um tipo de namespace do kernel Linux que fornece isolamento de contêiner?  
Solução: Host

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Host não é um tipo de namespace do kernel Linux que fornece isolamento de contêiner. Os namespaces Linux são uma funcionalidade do kernel que particiona recursos do kernel, de forma que um conjunto de processos vê um conjunto de recursos, enquanto outro conjunto vê recursos diferentes. Existem oito tipos de namespaces disponíveis: Mount, Process, User, Network, UTS, IPC, Cgroup e Time. O parâmetro **Host** é usado para rodar um contêiner no namespace de rede do host, o que significa que o contêiner compartilha as mesmas interfaces e configurações de rede do host. Esse parâmetro não oferece isolamento, mas, sim, faz com que o contêiner utilize diretamente o ambiente de rede do host.

---

### Pergunta #:171 
O seguinte arquivo Docker Compose é implantado como um stack:
```yaml
version: '3.1'
services:
  app:
    image: app:1.0
    healthcheck:
      test: "curl --fail http://localhost/health || exit 1"
      interval: 10s
      timeout: 5s
      retries: 3
```
Esta afirmação está correta sobre a definição da verificação de integridade?  
Solução: As verificações de integridade testam a saúde do aplicativo com intervalo de 10 segundos. Três falhas de verificação de integridade fazem o contêiner mudar para o status "não saudável".

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
A afirmação não está completamente correta. A definição da verificação de integridade no arquivo Docker Compose testa a saúde do aplicativo a cada 10 segundos (intervalo de 10s), mas com base nos parâmetros `interval`, `timeout` e `retries`, a falha de saúde ocorreria após três tentativas consecutivas (não duas). Portanto, a frequência de checagem e o número de falhas estão corretos, mas a mudança de status ocorre após três falhas, o que está correto na definição do Docker Compose e não faz menção a "18 segundos".

---

### Pergunta #:172  
Uma imagem de aplicativo é executada em vários ambientes, com cada ambiente utilizando certificados e portas diferentes.  
Esta é uma maneira de fornecer configuração aos contêineres em tempo de execução?  
Solução: Criar imagens que contenham a configuração específica para cada ambiente.

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
Criar imagens que contenham a configuração específica para cada ambiente não é uma boa maneira de fornecer configuração aos contêineres em tempo de execução. Essa abordagem viola o princípio de separar o código do aplicativo da configuração, tornando as imagens menos portáveis e reutilizáveis em diferentes ambientes. Além disso, ela aumenta a sobrecarga de manutenção e o risco de divergência de configuração, pois qualquer mudança na configuração exigiria a reconstrução e o reenvio das imagens. Para fornecer configuração aos contêineres em tempo de execução, é melhor usar mecanismos como variáveis de ambiente, argumentos de linha de comando ou ConfigMaps.

---

### Pergunta #:173  
Você quer fornecer um arquivo de configuração para um contêiner em tempo de execução. Este conjunto de ferramentas e etapas do Kubernetes realiza isso?  
Solução: Montar o arquivo de configuração diretamente no pod e no contêiner apropriados usando a chave `.spec.containers.configMounts`.

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
A solução apresentada não é uma maneira válida de fornecer um arquivo de configuração para um contêiner em tempo de execução usando ferramentas e etapas do Kubernetes. O motivo é que não existe a chave `.spec.containers.configMounts` no PodSpec. A chave correta a ser usada é `.spec.containers.volumeMounts`, que especifica os volumes a serem montados no sistema de arquivos do contêiner. Para usar um ConfigMap como fonte de volume, é necessário criar um objeto ConfigMap que contenha o arquivo de configuração como um par chave-valor e, em seguida, referenciá-lo na seção `.spec.volumes` do PodSpec. O ConfigMap é um objeto da API do Kubernetes que permite armazenar dados de configuração para que outros objetos possam usá-los.

---

### Pergunta #:174  
Esta afirmação está correta?  
Solução: Um Dockerfile fornece instruções para construir uma imagem Docker.

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
Um Dockerfile é um arquivo de texto que contém todos os comandos que um usuário pode executar na linha de comando para criar uma imagem. Um Dockerfile é composto por instruções que especificam a imagem base, os pacotes a serem instalados, os arquivos a serem copiados, as portas a serem expostas e os comandos a serem executados. Um Dockerfile pode ser usado para construir uma imagem Docker com o comando `docker build`.

---

### Pergunta #:175  
Este comando garantirá que o tráfego overlay entre tarefas de serviço seja criptografado?  
Solução: `docker network create -d overlay --secure <network-name>`

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
O comando `docker network create -d overlay --secure <network-name>` não garantirá que o tráfego overlay entre tarefas de serviço seja criptografado. A opção `--secure` não é uma flag válida para o comando `docker network create`. Para habilitar a criptografia em uma rede overlay, você precisa usar a flag `--opt encrypted` em vez disso. Isso criará túneis IPSEC entre os nós onde as tarefas do serviço são agendadas, usando o algoritmo AES no modo GCM. Você pode verificar se uma rede overlay está criptografada verificando se os túneis IPSEC foram criados com ferramentas como o `netstat`.

---

### Pergunta #:176  
Este comando cria um serviço Swarm que ouve apenas na porta 53 usando o protocolo UDP?  
Solução: `docker service create -name dns-cache -p 53:53/udp dns-cache`

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
O comando `docker service create -name dns-cache -p 53:53/udp dns-cache` não está correto e não criará um serviço Swarm que ouve apenas na porta 53 usando o protocolo UDP. A sintaxe está incorreta. O comando correto para criar um serviço Swarm que escuta na porta 53 usando UDP é:

```bash
docker service create --name dns-cache --publish published=53,target=53,protocol=udp dns-cache
```

Aqui, a opção `--publish` especifica o mapeamento de portas com o protocolo adequado (`udp`).

---

### Pergunta #:177  
A política de segurança de uma empresa especifica que os contêineres de desenvolvimento e produção devem ser executados em nós separados em um cluster Swarm.  
Isso pode ser usado para agendar os contêineres e atender aos requisitos da política de segurança?  
Solução: **reservação de recursos**  

A. Sim  
B. Não  

**Resposta:** B  

**Explicação:**  
A **reservação de recursos** é um recurso que permite especificar a quantidade de CPU e memória necessária para um serviço ou contêiner. Isso ajuda o agendador a colocar o serviço ou contêiner em um nó com recursos suficientes disponíveis. No entanto, a **reservação de recursos** não controla em qual nó o serviço ou contêiner será executado, nem impõe separação ou isolamento entre diferentes serviços ou contêineres. Portanto, a **reservação de recursos** não pode ser usada para garantir que os contêineres de desenvolvimento e produção sejam executados em nós separados, como especificado pela política de segurança. Para atender a esse requisito, seria necessário usar **rótulos de nó** e **restrições de serviço**.

---

### Pergunta #:178  
Qual é o objetivo do Docker Content Trust?  
Solução: **Assinar e verificar tags de imagens.**  

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
O **Docker Content Trust (DCT)** tem como objetivo **assinar e verificar tags de imagens** usando assinaturas digitais para os dados enviados e recebidos de repositórios Docker remotos. Isso permite a verificação de integridade e do editor das tags de imagem, garantindo a proveniência e a segurança das imagens de contêiner. Essa verificação pode ser feita tanto no lado do cliente quanto em tempo de execução, para garantir que as imagens não foram modificadas ou comprometidas.

---

### Pergunta #:179  
Você configura um Docker engine local para impor a confiança de conteúdo definindo a variável de ambiente **DOCKER_CONTENT_TRUST=1**. Se **myorg/myimage:1.0** não for assinado, o Docker bloqueia esse comando?  
Solução: **docker image build**, a partir de um Dockerfile que começa com **FROM myorg/myimage:1.0**.  

A. Sim  
B. Não  

**Resposta:** A  

**Explicação:**  
Se o Docker engine estiver configurado para impor a confiança de conteúdo, ou seja, se a variável de ambiente **DOCKER_CONTENT_TRUST=1** estiver definida, o Docker bloqueará o comando. Isso ocorre porque o Docker só permitirá o uso de imagens confiáveis que foram assinadas usando o Docker Content Trust (DCT). O DCT utiliza assinaturas digitais para garantir a integridade e a origem da tag da imagem. Se a imagem **myorg/myimage:1.0** não for assinada, o Docker não permitirá que ela seja usada como base para a construção de uma nova imagem, pois não pode verificar a integridade ou a proveniência da imagem base.

---

### Pergunta #:181  
É este um namespace do kernel Linux que é desativado por padrão e deve ser habilitado na execução do Docker engine para ser usado?  
Solução: **mnt**

A. Sim  
B. Não  

**Resposta:** B

**Explicação:**  
O namespace **mnt** (montagem) não é desativado por padrão e não precisa ser habilitado na execução do Docker engine. Ele é um dos namespaces do kernel Linux usados pelo Docker para isolar containers do sistema host. O namespace **mnt** permite que um container tenha seu próprio conjunto de sistemas de arquivos montados e diretórios raiz, diferentes dos do host. Isso significa que um container pode acessar apenas os arquivos e diretórios montados dentro de seu próprio namespace, e não os que estão montados no host ou em outros containers. O namespace **mnt** é criado automaticamente quando o container é iniciado e é destruído quando o container é parado.

---

### Pergunta #:182  
Sua organização possui uma solução de logging centralizada, como o Splunk.  
Isso configurará um container Docker para exportar logs de containers para a solução de logging?  
Solução: `docker system events --filter splunk`

A. Sim  
B. Não  

**Resposta:** B

**Explicação:**  
O comando `docker system events --filter splunk` não configurará um container Docker para exportar logs de containers para a solução de logging. O comando `docker system events` exibe eventos em tempo real do daemon Docker, como criação, início, parada de containers, etc. A opção `--filter` pode ser usada para filtrar os eventos por vários critérios, como tipo, rótulo, nome, etc. No entanto, não existe um filtro para "splunk", e mesmo que existisse, ele mostraria apenas eventos relacionados ao Splunk, não os logs do container. Para configurar um container Docker para exportar logs para o Splunk, você precisará usar o driver de logs do Splunk, que é um plugin que envia os logs do container para o HTTP Event Collector no Splunk Enterprise e no Splunk Cloud.

---

### Pergunta #:183  
São estas condições suficientes para o Kubernetes provisionar dinamicamente um persistentVolume, assumindo que não há limitações quanto à quantidade e tipo de armazenamento externo disponível?  
Solução: Um provisionador padrão é especificado, e subsequentemente um persistentVolumeClaim é criado.

A. Sim  
B. Não  

**Resposta:** B

**Explicação:**  
As condições não são suficientes para o Kubernetes provisionar dinamicamente um persistentVolume, pois estão faltando um objeto StorageClass. O objeto StorageClass define qual provisionador deve ser usado e quais parâmetros devem ser passados para esse provisionador quando o provisionamento dinâmico for invocado. Um persistentVolumeClaim deve especificar o nome de um StorageClass no campo `storageClassName` para solicitar um persistentVolume provisionado dinamicamente.
