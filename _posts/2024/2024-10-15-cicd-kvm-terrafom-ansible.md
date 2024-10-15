---
title: "CI/CD: Infraestrutura KVM com Terraform e Ansible"
date: 2024-10-15 15:00:00 -0300
description: "Tutorial passo a passo para criar uma infraestrutura CI/CD automatizada com KVM, Terraform e Ansible."
tags:
  - KVM
  - Terraform
  - Ansible
  - CI/CD
  - Infraestrutura
categories:
  - DevOps
  - Automação
  - Infraestrutura como Código
version: "1.0"
---

### Introdução

Neste tutorial, vamos abordar a implementação de uma infraestrutura automatizada de **CI/CD (Continuous Integration/Continuous Deployment)** usando **KVM**, **Terraform**, e **Ansible**. O objetivo é criar um ambiente completo de virtualização e deploy de aplicações em máquinas virtuais (VMs), utilizando tecnologias modernas e altamente escaláveis.

### Tecnologias Utilizadas:

- **KVM (Kernel-based Virtual Machine)**: Tecnologia de virtualização nativa do Linux, que permite a criação e gerenciamento de máquinas virtuais com desempenho quase nativo.
  
- **Terraform**: Ferramenta de infraestrutura como código (IaC) que permite a criação, atualização e versionamento da infraestrutura em qualquer provedor de nuvem ou servidor local. Neste caso, usamos o **provedor Libvirt** para gerenciar VMs em KVM.
  
- **Ansible**: Ferramenta de automação de configuração que facilita a instalação de softwares, configuração de servidores e o gerenciamento de tarefas repetitivas através de playbooks simples em YAML.

### Estrutura do Tutorial

1. **Configuração de Infraestrutura com Terraform**:
   - Utilizamos o Terraform para definir e provisionar uma máquina virtual no KVM, configurando o sistema operacional e discos, e automatizando a inicialização com **Cloud-init**.
   
2. **Automação de Configuração com Ansible**:
   - Após a criação da VM, utilizamos o Ansible para configurar e instalar aplicações dentro da máquina. Isso inclui a instalação do **Docker**, o uso do **Docker Compose** para implantar uma aplicação (neste caso, o **DokuWiki**), e o gerenciamento de tarefas adicionais via playbooks.

3. **CI/CD e Deploy Automatizado**:
   - A combinação dessas ferramentas permite que todo o processo, desde a criação da infraestrutura até o deploy de uma aplicação, seja totalmente automatizado. Isso é crucial em ambientes de **CI/CD**, onde mudanças rápidas e frequentes precisam ser aplicadas de forma segura e confiável.

### Objetivos

Ao final deste tutorial, você terá uma infraestrutura pronta para:
- Criar máquinas virtuais automaticamente usando **Terraform** e **KVM**.
- Configurar aplicações e serviços dentro dessas VMs com **Ansible**.
- Automatizar o deploy e gestão de aplicações em contêineres Docker usando **Docker Compose**.

Esse tutorial é ideal para administradores de sistemas, engenheiros de DevOps, ou qualquer pessoa que deseje aprender como automatizar a criação e gestão de infraestrutura e aplicações com ferramentas modernas.

---
### Infraestrutura/Arquitetura 
| Serviço/Componente      | Host (IP/Hostname)         | Função                                            |
| ----------------------- | -------------------------- | ------------------------------------------------ |
| Ansible e Terraform      | 192.168.0.100              | Gerenciamento da infraestrutura e automação       |
| KVM (Hypervisor)         | 192.168.0.250              | Host de virtualização, onde a VM será provisionada|
| Máquina Virtual (VM)     | 192.168.122.44             | VM provisionada e gerenciada pelo KVM             |

Aqui você encontra instruções de como instalar o KVM e o Terraform: [Instalação e Configuração: KVM e Terraform](https://geanmartins.com.br/posts/terraform-kvm/)

### main.tf 
```hcl
# Definição de variáveis. Aqui, estamos criando um mapa de configurações que 
# armazena informações sobre o template da VM, como nome da imagem, pool de armazenamento, etc.
variable "vm_template" {
  type = map(any)
}

# Configuração dos provedores necessários. Neste caso, estamos utilizando o provedor Libvirt
# para gerenciar máquinas virtuais no KVM (QEMU) via Terraform.
terraform {
  required_providers {
    libvirt = {
      source = "dmacvicar/libvirt"
    }
  }
}

# Configura o provedor Libvirt para se conectar ao sistema QEMU via SSH.
# O 'uri' define o destino da conexão para gerenciar a infraestrutura da VM.
provider "libvirt" {
  uri = "qemu+ssh://gean@192.168.0.250/system"  # IP do servidor remoto onde a VM será gerenciada
}

# Recurso para criar o volume da imagem do sistema operacional da VM.
# O volume é baseado no URL da imagem do SO fornecido nas variáveis.
resource "libvirt_volume" "os_image" {
  name   = var.vm_template["os_image_name"]    # Nome da imagem
  pool   = var.vm_template["storage_pool"]     # Pool de armazenamento onde a imagem será armazenada
  source = var.vm_template["os_image_url"]     # URL de origem da imagem do SO
  format = "qcow2"                             # Formato da imagem de disco
}

# Recurso para criar um volume de dados adicional para a VM.
# Este volume será usado para armazenar dados específicos da VM, como discos adicionais.
resource "libvirt_volume" "os_datas" {
  name           = var.vm_template["os_volume_name"]  # Nome do volume de dados
  base_volume_id = libvirt_volume.os_image.id         # Baseado na imagem do SO (anteriormente definida)
  pool           = var.vm_template["storage_pool"]    # Pool de armazenamento
  size           = var.vm_template["disksize"] * 1024 * 1024 * 1024  # Tamanho do volume (convertido para bytes)
}

# Definição de um arquivo cloud-init, que será usado para automatizar a inicialização da VM.
# O cloud-init contém configurações iniciais, como usuários, pacotes, etc.
data "template_file" "user_data" {
  template = file("${path.module}/${var.vm_template["cloud_init_file"]}")  # Carrega o arquivo cloud-init
}

# Criação de um disco cloud-init que será anexado à VM para automatizar as configurações na inicialização.
resource "libvirt_cloudinit_disk" "cloudinit" {
  name      = "cloudinit.iso"                              # Nome do disco cloud-init
  user_data = data.template_file.user_data.rendered        # Conteúdo do arquivo cloud-init processado
  pool      = var.vm_template["storage_pool"]              # Pool de armazenamento onde será armazenado
}

# Criação do recurso principal da VM.
resource "libvirt_domain" "domain" {
  name   = var.vm_template["name"]  # Nome da VM
  memory = var.vm_template["memory"]  # Memória RAM atribuída à VM (em MB)
  vcpu   = var.vm_template["cpu"]     # Número de vCPUs atribuídos à VM

  # Configuração do modo de CPU para "host-passthrough" para melhor performance.
  cpu {
    mode = "host-passthrough"
  }

  # Vincula o disco cloud-init criado anteriormente à VM.
  cloudinit = libvirt_cloudinit_disk.cloudinit.id

  # Configuração da interface de rede. A VM vai se conectar à rede especificada nas variáveis.
  network_interface {
    network_name   = var.vm_template["network_name"]  # Nome da rede para a VM
    wait_for_lease = true  # Garante que a VM aguarde até obter um lease de IP
  }

  # Configuração do console da VM para uso de terminal de emulação.
  console {
    type        = "pty"       # Tipo de console
    target_type = "virtio"    # Tipo de dispositivo de destino
    target_port = "1"         # Porta de destino
  }

  # Anexa o volume de dados à VM.
  disk {
    volume_id = libvirt_volume.os_datas.id  # Referência ao volume de dados criado anteriormente
  }

  # Configurações gráficas da VM. Usa o protocolo Spice para acesso remoto.
  graphics {
    type        = "spice"    # Tipo de gráfico (Spice para acesso remoto)
    listen_type = "address"  # Endereço de escuta para conexões gráficas
    autoport    = true       # Gera automaticamente uma porta para a conexão
  }
}

# Output para exibir o endereço IP da VM após a criação.
# Mostra o primeiro IP atribuído à VM pela interface de rede.
output "ip" {
  value = libvirt_domain.domain.network_interface[0].addresses[0]
}
```

### cloud-init.yml
```yaml
# Arquivo de configuração Cloud-init
# Este arquivo automatiza várias configurações iniciais da VM, como hostname, usuários, SSH e pacotes.
# Ele é lido no primeiro boot da VM para provisionar o ambiente.

# Define o hostname (nome da máquina na rede)
hostname: wiki

# Permitir autenticação SSH com senha (não recomendado para produção, onde o ideal é usar chaves SSH)
ssh_pwauth: yes  

# Configuração de usuários do sistema
users:
  - name: gean  # Nome do usuário a ser criado
    sudo: ALL=(ALL) NOPASSWD:ALL  # Permite que o usuário use sudo sem fornecer senha
    groups: users, sudo  # Adiciona o usuário aos grupos "users" e "sudo"
    shell: /bin/bash  # Define o shell padrão como bash
    lock_passwd: false  # Impede o bloqueio da senha (permite login com senha)
    
    # Hash da senha do usuário (cuidado ao armazenar senhas em texto plano).
    # A senha aqui está criptografada, mas usar uma ferramenta segura como HashiCorp Vault
    # para gerenciar senhas seria mais seguro.
    passwd: $6$JQM81.aQoND9qFhZ$ij4VbSFByU3OOWOx6qDb2faADBGGvyDsRd8iY6amNO5APfcTIrJT7ogcwMVesxxcoXaPlKPggNGxBMDSRlhXv1
   
    # Nota de Segurança:
    # Em ambientes de produção, evite expor senhas ou dados sensíveis diretamente nos arquivos de configuração.
    # Uma prática recomendada é usar ferramentas como HashiCorp Vault para armazenar e acessar credenciais
    # de forma segura, evitando riscos de vazamento de informações.
    
# Configura a senha do usuário root
chpasswd:
  list: |
    root:$6$JQM81.aQoND9qFhZ$ij4VbSFByU3OOWOx6qDb2faADBGGvyDsRd8iY6amNO5APfcTIrJT7ogcwMVesxxcoXaPlKPggNGxBMDSRlhXv1
  expire: False  # Define que a senha não expira

# Lista de pacotes a serem instalados durante o primeiro boot da VM.
# Pacotes essenciais como o agente qemu e bash-completion para conveniência.
packages:
  - qemu-guest-agent  # Agente do QEMU, útil para fornecer informações de VM para o host
  - bash-completion  # Facilita a auto-completação de comandos no shell

# Ativar atualização de pacotes logo após a inicialização
package_update: true

# Ativar a atualização do sistema operacional durante o provisionamento inicial
package_upgrade: true
```

### terraform.tfvars
```hcl
vm_template = {
  name            = "wiki"
  cpu             = 2
  memory          = 2048
  disksize        = 16
  storage_pool    = "default"
  os_image_name   = "wiki_image.qcow2"
  os_volume_name  = "wiki_volume.qcow2"
  network_name    = "default"
  cloud_init_file = "cloud-init.yml"
  os_image_url    = "/home/gean/kvm/templates/debian-12-generic-amd64.qcow2"
}
```

### Redirecionamento de porta
OBS: Por se tratar de um servidor remoto, é necessário fazer fazer o redirecionamento de porta para a máquina virtual
```bash
sudo iptables -I FORWARD -o virbr0 -p tcp -d 192.168.122.44 --dport 22 -j ACCEPT
sudo iptables -t nat -I PREROUTING -p tcp --dport 22022 -j DNAT --to 192.168.122.44:22
```

### Ansible
```bash
mkdir -p ansible/docker
cd ansible/docker
ssh-keygen -t ed25519 -f ansible
ssh-copy-id -p22022 -i ansible.pub 192.168.0.250
```

#### hosts
```bash
# Definição do inventário de hosts gerenciados pelo Ansible
[docker]
192.168.0.250  # Endereço IP do host Docker a ser configurado

# Definição de variáveis para o grupo de hosts 'docker'
[docker:vars]
ansible_python_interpreter=/usr/bin/python3  # Define o interpretador Python a ser utilizado no servidor
``

#### ansible.cfg
```bash
# Configuração do Ansible
[defaults]
inventory = hosts  # Inventário padrão para o Ansible, aponta para o arquivo 'hosts'
remote_user = gean  # Usuário remoto que executará as tarefas
remote_port = 22022  # Porta SSH para conexão (já que foi feito redirecionamento de porta)
host_key_checking = False  # Desabilita a verificação de chave do host (útil em testes, mas não recomendado para produção)
private_key_file = ansible  # Arquivo de chave privada para autenticação SSH

# Nota de Segurança:
# Em produção, ative o host_key_checking para evitar ataques de man-in-the-middle.
# Certifique-se de que as chaves públicas dos hosts estejam devidamente configuradas no arquivo de known_hosts.
```

#### validando a configuração
```bash
ansible docker -m ping
```

#### playbook.yml
```yaml
---
- name: Playbook para instalação do Docker no Debian
  hosts: docker  # Define que as tarefas serão executadas no grupo 'docker'
  become: true  # Garante que as tarefas sejam executadas com privilégios de superusuário (root)

  tasks:
    - name: Atualizar a lista de pacotes do APT
      apt:
        update_cache: yes  # Atualiza a lista de pacotes apt para garantir que a última versão esteja disponível

    - name: Instalar dependências necessárias
      apt:
        name:
          - apt-transport-https  # Pacote para habilitar APT via HTTPS
          - ca-certificates  # Certificados raiz para HTTPS
          - curl  # Ferramenta para fazer requisições de rede
          - gnupg  # Ferramenta de criptografia para adicionar chaves GPG
          - lsb-release  # Informação da versão do sistema
        state: present  # Garante que os pacotes estejam presentes

    - name: Adicionar chave GPG oficial do Docker
      apt_key:
        url: https://download.docker.com/linux/debian/gpg  # Chave pública do Docker para Debian
        state: present  # Adiciona a chave GPG ao sistema

    - name: Adicionar repositório do Docker APT
      apt_repository:
        repo: "deb [arch=amd64] https://download.docker.com/linux/debian {{ ansible_distribution_release }} stable"  # Repositório Docker para Debian
        state: present  # Garante que o repositório esteja presente

    - name: Atualizar a lista de pacotes após adicionar repositório do Docker
      apt:
        update_cache: yes  # Atualiza a lista de pacotes novamente após adicionar o novo repositório

    - name: Instalar o Docker
      apt:
        name: docker-ce  # Pacote Docker CE (Community Edition)
        state: present  # Garante que o Docker esteja instalado

    - name: Adicionar usuários ao grupo Docker
      user:
        name: "{{ item }}"  # Variável para definir o nome do usuário
        groups: docker  # Adiciona o usuário ao grupo Docker
        append: yes  # Adiciona o grupo Docker, sem remover os grupos existentes
      loop:
        - gean  # Adiciona o usuário "gean" ao grupo Docker

    - name: Iniciar e habilitar o Docker
      systemd:
        name: docker
        enabled: true  # Garante que o Docker inicie automaticamente na inicialização do sistema
        state: started  # Garante que o Docker esteja rodando
```

#### executando a playbook
```bash
ansible-playbook playbook.yml
```

#### validando a instalação do docker
```bash
# Validação de instalação do Docker
ansible docker -m shell -a "docker --version"
```

#### Automatizando o Docker Compose e Implantando Aplicações

##### playbook-compose.yml
```yaml
---
- name: Automatização do Docker Compose e Deploy do DokuWiki
  hosts: docker  # Define que as tarefas serão executadas no host "docker"
  become: true  # Assegura que todas as tarefas sejam executadas com privilégios de superusuário (root)

  tasks:
    - name: Criar diretório para o Docker Compose
      file:
        path: /srv/dokuwiki  # Diretório onde o Docker Compose e a aplicação serão configurados
        state: directory  # Garante que o diretório exista
        mode: '0755'  # Permissões para o diretório (leitura e execução para todos, gravação para o dono)

    - name: Copiar o arquivo docker-compose.yml para o servidor
      copy:
        src: ./docker-compose.yml  # Caminho do arquivo docker-compose.yml local (controlador Ansible)
        dest: /srv/dokuwiki/docker-compose.yml  # Caminho no servidor onde o arquivo será copiado
        mode: '0644'  # Permissões do arquivo (leitura para todos, gravação apenas para o dono)

    - name: Criar diretório de volumes para o DokuWiki
      file:
        path: /srv/dokuwiki/html  # Diretório onde os dados do DokuWiki serão armazenados (volumes)
        state: directory
        mode: '0755'

    - name: Ajustar permissões no diretório html
      file:
        path: /srv/dokuwiki/html
        owner: "1000"  # UID do usuário que vai gerenciar o diretório
        group: "1000"  # GID correspondente
        recurse: yes  # Aplica as permissões recursivamente a todos os arquivos dentro do diretório
        mode: '0755'

    - name: Garantir que o Docker Compose esteja instalado
      apt:
        name: docker-compose-plugin  # Pacote do Docker Compose para ser instalado
        state: present  # Garante que o pacote esteja instalado

    - name: Executar docker-compose up
      shell: docker compose -f /srv/dokuwiki/docker-compose.yml up -d  # Executa o Docker Compose
      args:
        chdir: /srv/dokuwiki  # Define o diretório onde o comando será executado
```

##### docker-compose.yml - deploy dokuwiki
```yaml
version: '3.8'

services:
  dokuwiki:
    image: dokuwiki/dokuwiki:stable  # Usa a imagem estável do DokuWiki do Docker Hub
    user: "1000:1000"  # Define o UID e GID para garantir que o usuário correto acesse os volumes
    restart: unless-stopped  # Garante que o contêiner seja reiniciado automaticamente em caso de falha
    ports:
      - "8080:8080"  # Mapeia a porta 8080 do contêiner para a porta 8080 do host
    environment:
      PHP_TIMEZONE: America/Sao_Paulo  # Define a timezone no contêiner para evitar problemas de data/hora
    volumes:
      - ./html:/storage  # Mapeia o diretório local ./html para o diretório de armazenamento do contêiner
      
# Nota de Boas Práticas:
# A política de reinício 'restart: unless-stopped' garante que o contêiner do DokuWiki seja reiniciado automaticamente
# em caso de falha. Isso ajuda a manter a aplicação disponível, aumentando a resiliência do sistema.
```

#### Executando o playbook para o Docker Compose
```bash
ansible-playbook playbook-compose.yml
```

#### verificando se o contêiner está em execução
```bash
# Valida se o contêiner DokuWiki está em execução
ansible docker -m shell -a "docker container ls"
```

### Redirecionamento de porta para a aplicação
```bash
# Redireciona a porta 8080 da máquina host para a máquina virtual que está rodando o DokuWiki
sudo iptables -I FORWARD -o virbr0 -p tcp -d 192.168.122.44 --dport 8080 -j ACCEPT
sudo iptables -t nat -I PREROUTING -p tcp --dport 8080 -j DNAT --to 192.168.122.44:8080
```

### Instale o Dokuwiki
```bash
http://192.168.122.44:8080/install.php
``