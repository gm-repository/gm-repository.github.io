---
title: "Automatizando a Instalação do Docker com Ansible"
description: "Este tutorial detalha o processo de instalação e configuração do Docker e Docker Compose em máquinas remotas utilizando Ansible. Inclui configuração de chaves SSH, gerenciamento de hosts, criação de playbooks e implantação de aplicações via Docker Compose."
date: 2024-10-11 13:00:00 -0300
version: "1.0"
categories: [Docker, Ansible, DevOps, Containers]
keywords:
  - Ansible
  - Docker
  - Docker Compose
  - Automação
  - SSH
  - Playbook
---

### Sumário:

1. [Parte 1: Instalação e Configuração do Ansible no Controlador Ubuntu](#parte-1-instalação-e-configuração-do-ansible-no-controlador-ubuntu)
2. [Parte 2: Automatizando a Instalação do Docker com Ansible](#parte-2-configuração-de-conexão-ssh-e-inventário-do-ansible)
3. [Parte 3: Automatizando o Docker Compose e Implantando Aplicações](#parte-3-automatizando-a-instalação-do-docker-com-ansible)


Esses elementos estruturam o tutorial, facilitando a navegação e compreensão do conteúdo, além de fornecer um resumo inicial claro dos passos envolvidos.
### Parte 1: Instalação e Configuração do Ansible no Controlador Ubuntu

#### 1. **Atualizando o sistema e instalando Ansible**
Antes de instalar o Ansible, é importante garantir que seu sistema está atualizado. Execute os seguintes comandos no terminal do controlador (máquina onde o Ansible será instalado):

```bash
sudo apt update && sudo apt upgrade -y
```

Depois de atualizar, instale os pacotes essenciais:

```bash
sudo apt install software-properties-common -y
```

Agora, adicione o repositório oficial do Ansible para obter a versão mais recente:

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

Por fim, instale o Ansible e o `sshpass`, que será útil para automatizar a conexão SSH com hosts remotos:

```bash
sudo apt install ansible sshpass -y
```

Verifique se a instalação foi bem-sucedida verificando a versão do Ansible:

```bash
ansible --version
```

### Parte 2: Configuração de Conexão SSH e Inventário do Ansible

#### 2. **Gerando e configurando chaves SSH**
A comunicação entre o Ansible e os hosts gerenciados será feita via SSH. Para evitar o uso de senhas manuais, geramos uma chave SSH no controlador:

```bash
ssh-keygen -t ed25519 -f ansible
```

A chave será salva no arquivo `ansible`. Agora, copie a chave pública para o host remoto. Substitua `gean` pelo nome de usuário remoto e `192.168.122.115` pelo endereço IP da máquina alvo:

```bash
ssh-copy-id -i ansible.pub gean@192.168.122.115
```

Isso permitirá que o Ansible se conecte ao host remoto sem solicitar uma senha.

#### 3. **Configurando o inventário do Ansible**
Agora, crie um diretório para os arquivos de configuração do Ansible, se ainda não existir:

```bash
mkdir -p ansible/docker
```

Crie o arquivo `hosts` dentro do diretório `ansible/docker`. Este arquivo de inventário define os hosts que o Ansible gerenciará. Insira o endereço IP do host e o nome de usuário remoto, conforme o exemplo abaixo:

```ini
[docker]
192.168.122.115 ansible_user=gean
```

#### 4. **Configurando o Ansible**
Agora, vamos ajustar o arquivo de configuração do Ansible (`ansible.cfg`) para desativar a verificação de chaves SSH e usar a chave privada gerada anteriormente. Crie ou edite o arquivo `ansible.cfg` no diretório `ansible/docker`:

```ini
[defaults]
inventory = hosts
host_key_checking = False
private_key_file = ansible
```

Com essas configurações, o Ansible utilizará o arquivo `hosts` como inventário e a chave privada para autenticar no host remoto.

#### 5. **Testando a comunicação**
Antes de executar qualquer tarefa no host remoto, é recomendável testar a conectividade. Use o módulo `ping` do Ansible para verificar se o controlador pode se comunicar com o host:

```bash
ansible docker -m ping
```

Se o resultado for algo como `pong`, significa que a comunicação está funcionando corretamente.


### Parte 3: Automatizando a Instalação do Docker com Ansible

Agora que o controlador Ansible está configurado e se comunica com o host remoto, vamos criar um **Playbook Ansible** para automatizar a instalação do Docker e Docker Compose.

#### 1. **Definindo variáveis de grupo**
Antes de iniciar o playbook, podemos definir algumas variáveis para facilitar a reutilização em diferentes hosts. Crie um diretório para armazenar variáveis de grupo:

```bash
mkdir group_vars
```

Dentro deste diretório, crie um arquivo chamado `debian.yml`, que armazenará variáveis relacionadas aos sistemas Debian, como os usuários que serão adicionados ao grupo Docker:

```yaml
# group_vars/debian.yml
docker_users:
  - gean
```

Essa variável `docker_users` será utilizada mais tarde para adicionar usuários ao grupo Docker, permitindo que executem comandos sem `sudo`.

#### 2. **Criando o Playbook**
Agora vamos criar o arquivo `playbook.yml`, onde definiremos as tarefas que o Ansible executará para instalar o Docker e Docker Compose.

Crie ou edite o arquivo `playbook.yml` no diretório `ansible/docker`:

```bash
vim playbook.yml
```

Agora, adicione o seguinte conteúdo YAML ao playbook. Isso inclui as tarefas para atualizar os pacotes, instalar dependências, configurar o repositório do Docker e instalar o Docker e Docker Compose:

```yaml
---
- hosts: docker
  become: true  # Garante que as tarefas sejam executadas com privilégios de superusuário
  tasks:

    - name: Atualizar o cache de pacotes APT
      apt:
        update_cache: yes
        cache_valid_time: 3600  # Cache válido por 1 hora

    - name: Instalar dependências do Docker
      apt:
        name: 
          - apt-transport-https
          - ca-certificates
          - curl
          - gnupg
          - lsb-release
        state: present

    - name: Baixar e adicionar chave GPG oficial do Docker
      ansible.builtin.apt_key:
        url: https://download.docker.com/linux/debian/gpg
        state: present
        keyring: /usr/share/keyrings/docker-archive-keyring.gpg

    - name: Adicionar repositório Docker
      ansible.builtin.apt_repository:
        repo: "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian {{ ansible_facts['lsb']['codename'] }} stable"
        state: present

    - name: Atualizar o cache de pacotes após adicionar o repositório Docker
      apt:
        update_cache: yes

    - name: Instalar Docker e Docker Compose
      apt:
        name:
          - docker-ce
          - docker-ce-cli
          - containerd.io
        state: present

    - name: Adicionar usuários ao grupo Docker
      user:
        name: "{{ item }}"
        groups: docker
        append: yes
      loop: "{{ docker_users }}"

    - name: Iniciar e habilitar o serviço Docker
      systemd:
        name: docker
        enabled: yes
        state: started
```

#### 3. **Explicando o Playbook**
Aqui estão os detalhes das principais tarefas no playbook:
- **Atualização do cache APT**: Garante que o cache do sistema de pacotes esteja atualizado antes de instalar pacotes.
- **Instalação de dependências**: Pacotes como `curl`, `gnupg` e `lsb-release` são necessários para adicionar repositórios e chaves GPG.
- **Chave GPG e repositório Docker**: A chave GPG é necessária para garantir a autenticidade dos pacotes, enquanto o repositório Docker é adicionado para instalar as versões mais recentes.
- **Instalação do Docker**: Os pacotes `docker-ce`, `docker-ce-cli` e `containerd.io` são instalados.
- **Adicionar usuários ao grupo Docker**: Isso permite que os usuários no grupo `docker` executem comandos Docker sem `sudo`.
- **Habilitar e iniciar o serviço Docker**: Garante que o Docker inicie automaticamente após a instalação.

#### 4. **Executando o Playbook**
Agora que o playbook está pronto, você pode executá-lo usando o comando:

```bash
ansible-playbook playbook.yml
```

Isso irá instalar o Docker e configurar o host conforme o playbook, automatizando todo o processo de instalação.

#### 5. **Verificando a Instalação**
Após a execução do playbook, você pode verificar se o Docker foi instalado corretamente com o seguinte comando Ansible, que executa `docker --version` no host remoto:

```bash
ansible docker -m shell -a "docker --version"
```

Se a versão do Docker for exibida, a instalação foi bem-sucedida.

---

### Parte 4: Automatizando o Docker Compose e Implantando Aplicações

Agora que o Docker está instalado e funcionando, podemos avançar para a criação de um **playbook para implantar aplicações** usando Docker Compose.

#### 1. **Criando diretórios para o Docker Compose**
Crie um novo playbook, chamado `playbook-compose.yml`, para preparar o servidor e implantar uma aplicação com Docker Compose (neste caso, DokuWiki). O playbook cria diretórios, ajusta permissões e executa o `docker-compose up`.

```bash
vim playbook-compose.yml
```

Adicione o seguinte conteúdo:

```yaml
---
- hosts: docker
  become: true
  tasks:

    - name: Criar diretório para o Docker Compose
      file:
        path: /srv/dokuwiki
        state: directory
        mode: '0755'

    - name: Copiar o arquivo docker-compose.yml para o servidor
      copy:
        src: ./docker-compose.yml  # Caminho do arquivo local no controlador
        dest: /srv/dokuwiki/docker-compose.yml  # Caminho de destino no servidor
        mode: '0644'

    - name: Criar diretório de volumes para o DokuWiki
      file:
        path: /srv/dokuwiki/html
        state: directory
        mode: '0755'

    - name: Ajustar permissões no diretório html
      file:
        path: /srv/dokuwiki/html
        owner: "1000"
        group: "1000"
        recurse: yes
        mode: '0755'

    - name: Garantir que o Docker Compose esteja instalado
      apt:
        name: docker-compose-plugin
        state: present

    - name: Executar docker-compose up
      shell: docker compose -f /srv/dokuwiki/docker-compose.yml up -d
      args:
        chdir: /srv/dokuwiki
```

#### 2. **Criando o arquivo docker-compose.yml**
Agora, crie o arquivo `docker-compose.yml` no controlador, que será copiado para o servidor remoto no playbook:

```yaml
services:
  dokuwiki:
    image: dokuwiki/dokuwiki:stable
    user: "1000:1000"
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      PHP_TIMEZONE: America/Sao_Paulo
    volumes:
      - ./html:/storage
```

Esse arquivo define o serviço DokuWiki, mapeando portas e configurando volumes e variáveis de ambiente.

#### 3. **Executando o playbook para o Docker Compose**
Execute o playbook `playbook-compose.yml` para configurar e iniciar o DokuWiki:

```bash
ansible-playbook playbook-compose.yml
```

Por fim, verifique se o contêiner está em execução:

```bash
ansible docker -m shell -a "docker container ls"
```

Se o contêiner estiver listado, sua aplicação está rodando corretamente.

---

