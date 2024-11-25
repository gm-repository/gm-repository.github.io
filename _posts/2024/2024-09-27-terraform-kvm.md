---
title: "Instalação e Configuração: KVM e Terraform"
author: "Gean Martins"
date: 2024-09-27 10:00:00 -0300
categories: [Virtualização, Infraestrutura como Código, Terraform, KVM, Ubuntu]
keywords: ["KVM", "Terraform", "virtualização", "infraestrutura como código", "Ubuntu"]
abstract: "Este documento oferece um guia detalhado para a instalação e configuração do KVM (Kernel-based Virtual Machine) e do Terraform, focando em técnicas para gerenciar máquinas virtuais e infraestruturas de forma eficiente e segura."
---

# Sumário

- [Introdução](#introdução)
- [Verificação de Pré-requisitos](#verificação-de-pré-requisitos)
- [Instalação do KVM](#instalação-e-configuração-do-kvm)
- [Instalação do Terraform](#instalação-do-terraform)
- [Baixando os templates](#baixando-os-templates)
- [Configuração e Uso do Terraform para Provisão de VMs](#criando-uma-máquina-virtual)
- [Testes e Validação](#ambiente-de-teste)
- [Anexos e Links de Referência](#referência)

## Introdução
Este guia passo a passo é projetado para instruir profissionais de tecnologia sobre como instalar e configurar o KVM (Kernel-based Virtual Machine) e o Terraform, duas ferramentas poderosas para a criação e gerenciamento de máquinas virtuais e infraestruturas como código. Vamos começar com a verificação de pré-requisitos para garantir que seu sistema esteja pronto para o KVM e o Terraform, seguido por instruções detalhadas para a instalação e configuração de cada ferramenta. Este tutorial é ideal para usuários do sistema operacional Ubuntu e abrange desde a configuração inicial até a verificação e utilização efetiva das ferramentas instaladas.

## 1. Verificação de Pré-requisitos
Antes de começar a instalação, é importante verificar se o sistema está pronto para o KVM e o Terraform. Adicione esta seção no início do tutorial:

### Verificação de Pré-requisitos

- **Virtualização Habilitada**: Verifique se a virtualização está habilitada no BIOS. Você pode verificar isso com o seguinte comando:
  ```bash
  $ egrep -c '(vmx|svm)' /proc/cpuinfo
  ```
  Se o resultado for maior que 0, a virtualização está habilitada.

- **Sistema Operacional**: Certifique-se de que você está executando uma versão suportada do sistema operacional. Este tutorial assume o uso do Ubuntu.
- **Permissões Adequadas**: Certifique-se de que você possui permissões de superusuário (sudo) para executar os comandos de instalação.

## Instalação e configuração do KVM
```bash
# Instala o gerenciador de máquinas virtuais e outras ferramentas necessárias para o gerenciamento de VMs.
$ sudo apt install virt-manager libosinfo-bin mkisofs
```

### Adicionar usuário ao grupo KVM
```bash
# Adiciona o usuário atual ao grupo 'kvm', permitindo gerenciar máquinas virtuais sem privilégios de superusuário.
sudo usermod -aG kvm $USER
```

### Criar diretórios de armazenamento
```bash
# Cria diretórios para armazenar imagens, templates e ISOs de VMs.
$ mkdir -p /home/gean/kvm/images
$ mkdir -p /home/gean/kvm/templates
$ mkdir -p /home/gean/kvm/isos
```

### Definir pools de armazenamento
```bash
# Define pools de armazenamento para organizar melhor os recursos de VM.
$ virsh pool-define-as --name default --type dir --target /home/gean/kvm/images
$ virsh pool-define-as --name templates --type dir --target /home/gean/kvm/templates
$ virsh pool-define-as --name isos --type dir --target /home/gean/kvm/isos
```

### Configurar autostart para pools
```bash
# Configura os pools de armazenamento para iniciar automaticamente com o sistema.
$ virsh pool-autostart default
$ virsh pool-autostart templates
$ virsh pool-autostart isos
```

### Iniciar pools de armazenamento
```bash
# Inicia os pools de armazenamento definidos anteriormente.
$ virsh pool-start default
$ virsh pool-start templates
$ virsh pool-start isos
```

### Listar e verificar informações dos pools
```bash
# Lista todos os pools disponíveis e exibe informações do pool 'default'.
$ virsh pool-list
$ virsh pool-info default
```

### Configurar e aplicar perfil AppArmor para segurança
```bash
$ cat /etc/apparmor.d/libvirt/TEMPLATE.qemu 
#
# This profile is for the domain whose UUID matches this file.
#

#include <tunables/global>

profile LIBVIRT_TEMPLATE flags=(attach_disconnected) {
  #include <abstractions/libvirt-qemu>
  /home/gean/kvm/images/** rwk,
  /home/gean/kvm/templates/** rwk,
}
```

```bash
# Visualiza e aplica um perfil AppArmor para segurança adicional das VMs.
$ cat /etc/apparmor.d/libvirt/TEMPLATE.qemu 
sudo apparmor_parser -r /etc/apparmor.d/libvirt/TEMPLATE.qemu
sudo systemctl restart libvirtd
sudo journalctl -xe | grep apparmor
```

### Alterando o usuário do qemu
```bash
# Desabilita drivers de segurança específicos para o KVM. Usar apenas em ambientes de teste.
$ sudo cp -p /etc/libvirt/qemu.conf{,.dist}
$ sudo sed -i '/#user = "root"/a user = "gean"' /etc/libvirt/qemu.conf
$ sudo sed -i '#group = "root"/a group = "gean"' /etc/libvirt/qemu.conf
$ sudo systemctl restart libvirtd
```

### Opção que desabilita a segurança
```bash
# Desabilita drivers de segurança específicos para o KVM. Usar apenas em ambientes de teste.
$ sudo cp -p /etc/libvirt/qemu.conf{,.dist}
$ sudo sed -i '/#security_driver = "selinux"/a security_driver = "none"' /etc/libvirt/qemu.conf
$ sudo systemctl restart libvirtd
```

**Ou**:
```bash
sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="apparmor=0"
```

## Instalação do Terraform

### Atualizar pacotes e instalar ferramentas necessárias
```bash
# Atualiza a lista de pacotes disponíveis e instala ferramentas necessárias para baixar e extrair o Terraform.
sudo apt update
sudo apt install wget curl unzip
```

### Obter a versão mais recente do Terraform
```bash
# Utiliza o comando curl para consultar a API do GitHub e obter a tag da versão mais recente do Terraform.
TER_VER=`curl -s https://api.github.com/repos/hashicorp/terraform/releases/latest | grep tag_name | cut -d: -f2 | tr -d \"\,\v | awk '{$1=$1};1'`
```

### Baixar o Terraform
```bash
# Baixa o arquivo zip da versão mais recente do Terraform usando a variável de versão obtida anteriormente.
wget https://releases.hashicorp.com/terraform/${TER_VER}/terraform_${TER_VER}_linux_amd64.zip
```

### Descompactar e instalar o Terraform
```bash
# Extrai o arquivo baixado e move o executável do Terraform para o diretório /usr/local/bin para acesso global.
$ unzip terraform_${TER_VER}_linux_amd64.zip
$ sudo mv terraform /usr/local/bin/
```

### Verificar a instalação
```bash
# Verifica se o Terraform foi instalado corretamente e está acessível globalmente.
$ which terraform
```

### Checar a versão do Terraform
```bash
# Exibe a versão do Terraform instalada, útil para confirmar se a instalação foi bem-sucedida e a versão correta foi instalada.
$ terraform --version
```

### Habilitar autocompletar do Terraform
```bash
# Habilita o recurso de autocompletar para comandos do Terraform no shell, melhorando a eficiência ao usar o Terraform.
$ terraform -install-autocomplete
```

## Baixando os templates

### Download de imagens de sistema operacional
```bash
# Baixa a imagem do Ubuntu Server 22.04 (LTS) para utilização como template de máquinas virtuais. O arquivo é salvo diretamente no diretório de templates.
$ wget https://cloud-images.ubuntu.com/releases/22.04/release/ubuntu-22.04-server-cloudimg-amd64.img -P /home/gean/kvm/templates/

# Baixa a imagem mais recente do Debian 12 "Bookworm" em formato QCOW2, adequada para uso com sistemas de virtualização como o KVM.
$ wget https://cdimage.debian.org/images/cloud/bookworm/latest/debian-12-generic-amd64.qcow2 -P /home/gean/kvm/templates/

# Baixa a imagem do Oracle Linux 9 Update 3, uma distribuição otimizada para ambientes de nuvem e virtualizados, em formato QCOW2.
$ wget https://yum.oracle.com/templates/OracleLinux/OL9/u3/x86_64/OL9U3_x86_64-kvm-b220.qcow2 -P /home/gean/kvm/templates/
```

#### Expandindo os templates
```bash
qemu-img info kvm/templates/ubuntu-22.04-server-cloudimg-amd64.img
qemu-img info kvm/templates/debian-12-generic-amd64.qcow2
qemu-img resize kvm/templates/ubuntu-22.04-server-cloudimg-amd64.img +8G
qemu-img resize kvm/templates/debian-12-generic-amd64.qcow2 +8G
``` 

## Criando uma máquina virtual

### Preparação do diretório de trabalho
```bash
# Cria um diretório para armazenar os arquivos de configuração do Terraform para provisão de VMs.
$ mkdir -p ~/terraform/providers/libvirt/vms
# Navega para o diretório recém-criado.
$ cd ~/terraform/providers/libvirt/vms
```

### Geração de chave SSH para autenticação
```bash
# Gera uma chave SSH que será usada para acessar as VMs sem senha.
$ ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/tfvms
```

### Configuração inicial do Terraform
```hcl
# Define os provedores necessários para o Terraform, especificando o provedor libvirt para gestão de recursos de virtualização.
terraform {
  required_providers {
    libvirt = {
      source = "dmacvicar/libvirt"
    }
  }
}

# Configura o provedor libvirt com a URI do sistema QEMU, permitindo a comunicação com o serviço de virtualização.
provider "libvirt" {
  uri = "qemu:///system"
}
```

### Definição das variáveis para a VM
```hcl
# Define as variáveis utilizadas para configurar a VM, incluindo nome, CPU, memória, entre outros.
variable "vm_template" {
  type = object({
    name            = string
    cpu             = number
    memory          = number
    disksize        = number
    storage_pool    = string
    os_image_name   = string
    os_volume_name  = string
    network_name    = string
    cloud_init_file = string
    os_image_url    = string
  })

  description = "Template configuration for the VM"
}
```

### Criação de volumes de armazenamento
```hcl
# Cria um volume para a imagem do sistema operacional e outro para o volume de armazenamento da VM.
resource "libvirt_volume" "os_image" {
  name   = var.vm_template.os_image_name
  pool   = var.vm_template.storage_pool
  source = var.vm_template.os_image_url
  format = "qcow2"
}

resource "libvirt_volume" "os_volume" {
  name           = var.vm_template.os_volume_name
  base_volume_id = libvirt_volume.os_image.id
  pool           = var.vm_template.storage_pool
  size           = var.vm_template.disksize * 1024 * 1024 * 1024  # Convert GB to bytes
}
```

### Configuração de Cloud-Init
```hcl
# Utiliza um arquivo de template para configurar as definições iniciais do sistema operacional via Cloud-Init.
data "template_file" "user_data" {
  template = file("${path.module}/${var.vm_template.cloud_init_file}")
}

# Cria um disco Cloud-Init que será anexado à VM para configurar o sistema operacional na inicialização.
resource "libvirt_cloudinit_disk" "cloudinit" {
  name      = "${var.vm_template.name}-cloudinit.iso"
  user_data = data.template_file.user_data.rendered
  pool      = var.vm_template.storage_pool
}
```

### Configuração e provisionamento da VM
```hcl
# Define a configuração da máquina virtual, incluindo memória, CPUs, e dispositivos como rede e gráficos.
resource "libvirt_domain" "domain" {
  name     = var.vm_template.name
  memory   = var.vm_template.memory
  vcpu     = var.vm_template.cpu

  cpu {
    mode = "host-passthrough"
  }

  cloudinit = libvirt_cloudinit_disk.cloudinit.id

  network_interface {
    network_name   = var.vm_template.network_name
    wait_for_lease = true
  }

  console {
    type = "pty"
    target_port = "0"
    target_type = "serial"
  }

  console {
    type = "pty"
    target_type = "virtio"
    target_port = "1"
  }

  disk {
    volume_id = libvirt_volume.os_volume.id
  }

  graphics {
    type        = "spice"
    listen_type = "address"
    autoport    = true
  }
}
```

### Saída para verificar o IP da VM
```hcl
# Exibe o endereço IP atribuído à interface de rede da VM.
output "ip" {
  value = libvirt_domain.domain.network_interface[0].addresses[0]
}
```

### Configuração do cloud-init
```yml
#cloud-config

# Usuários e grupos
users:
  - name: gean
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: users, wheel
    shell: /bin/bash
    ssh-authorized-keys:
      - ${file("~/.ssh/tfvms.pub")}

# Desativa a senha para todos os usuários, exceto root
disable_root: false
ssh_pwauth: false

# Configuração da senha do root permitida apenas no console
chpasswd:
  list: |
    root:root
  expire: False

# Garantir que o acesso por senha esteja desativado para todos, exceto para o console
system_info:
  default_user:
    lock_passwd: true

# Configurações de segurança para SSH
sshd_config:
  PermitRootLogin: prohibit-password
  PasswordAuthentication: no
  ChallengeResponseAuthentication: no
  UsePAM: yes
  PermitEmptyPasswords: no

# Pacotes a serem instalados
packages:
  - qemu-guest-agent
  - bash-completion

# Configurações de atualização de pacotes
package_update: true
package_upgrade: true
```
### Configuração do terraform.tfvars
```hcl
vm_template = {
  name            = "tf-vm-01"
  cpu             = 2
  memory          = 2048
  disksize        = 64
  storage_pool    = "default"
  os_image_name   = "tf_vm_01_image.qcow2"
  os_volume_name  = "tf_vm_01_volume.qcow2"
  network_name    = "default"
  cloud_init_file = "cloud-init.yml"
  os_image_url    = "/home/gean/kvm/templates/OL9U3_x86_64-kvm-b220.qcow2"
}
```

### Execução dos comandos Terraform
```bash
# Inicializa o Terraform, formata os arquivos de configuração, valida, planeja e aplica a configuração para criar a VM.
$ terraform init
$ terraform fmt
$ terraform validate
$ terraform plan
$ terraform apply
```

## Ambiente de Teste
Antes de aplicar qualquer mudança em um ambiente de produção, é altamente recomendado testar as configurações em um ambiente separado. Isso minimiza os riscos associados com a introdução de novas configurações e permite ajustes em um ambiente controlado.

## Referência
- [Libvirt Provider](https://registry.terraform.io/providers/dmacvicar/libvirt/latest/docs)
- [Cloud config examples](https://cloudinit.readthedocs.io/en/latest/reference/examples.html)
- [How To Provision Virtual Machines on KVM with Terraform](https://computingforgeeks.com/how-to-provision-vms-on-kvm-with-terraform/)
