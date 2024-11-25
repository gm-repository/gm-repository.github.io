---
layout: post
title: "Infraestrutura KVM usando Terraform e Packer"
date: 2024-10-26 18:01:01 -0300
categories: [Infraestrutura, Virtualização, Automação, KVM, Terraform, Packer]
tags: [KVM, Terraform, Packer, Cloud-init, DevOps]
description: "Um guia passo a passo para implementar uma infraestrutura KVM com Packer e Terraform, automatizando a criação e o provisionamento de VMs."
---

### Parte 1: Instalando o Packer no Ubuntu

O **Packer** será usado para criar uma imagem base do sistema. Para instalá-lo no Ubuntu, execute os comandos abaixo:

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install packer
```

Para verificar a instalação, execute:
```bash
packer -v
```

Com o **Packer** instalado, seguimos para a criação dos scripts que definem a imagem do sistema.

### Parte 2: Criando os Scripts Packer

1. **Estrutura do Diretório**: Organize a estrutura de diretórios criando uma pasta para armazenar os arquivos necessários para o **Packer**.

   ```bash
   mkdir -p ~/packer/kvm/ol8
   cd ~/packer/kvm/ol8
   ```

2. **Script de Configuração HCL - `os-install.pkr.hcl`**:
   Esse script define as configurações do **Packer** para criar uma imagem do sistema. Ele utiliza o plugin **QEMU** para KVM e configura o ISO do Oracle Linux 8.

   ```hcl
   packer {
     required_version = ">= 1.10.0"
     required_plugins {
       qemu = {
         version = "= 1.1.0"
         source  = "github.com/hashicorp/qemu"
       }
     }
   }

   source "qemu" "iso" {
     vm_name          = "ol8-amd64.raw"
     iso_url          = "https://yum.oracle.com/ISOS/OracleLinux/OL8/u10/x86_64/OracleLinux-R8-U10-x86_64-boot.iso"
     iso_checksum     = "c7009ca2b28ddbedd6c09fc9141f933dec01a6ea73c503634786809746af1906"
     memory           = 2048
     disk_image       = false
     output_directory = "build/os-base"
     accelerator      = "kvm"
     disk_size        = "16384M"
     disk_interface   = "virtio"
     format           = "raw"
     net_device       = "virtio-net"
     boot_wait        = "3s"
     boot_command = [
       "<up>",
       "<tab><wait>",
       " inst.ks=http://{% raw %}{{.HTTPIP}}{% endraw %}:{% raw %}{{.HTTPPort}}{% endraw %}/ks.cfg",
       "<enter>"
     ]
     http_directory   = "http"
     cpu_model        = "host"
     shutdown_command = "echo 'packer' | sudo -S shutdown -P now"
     ssh_username     = "packer"
     ssh_password     = "packer"
     ssh_timeout      = "60m"
   }

   build {
     name    = "iso"
     sources = ["source.qemu.iso"]
   }
   ```

3. **Script Kickstart para Automação da Instalação - `http/ks.cfg`**:
   Esse arquivo define configurações automáticas para instalação do Oracle Linux. Primeiro, crie a pasta `http` para armazenar o script Kickstart.

   ```bash
   mkdir http
   ```

   Dentro de `http/ks.cfg`, adicione o conteúdo abaixo:

   ```plaintext
   graphical

   %addon com_redhat_kdump --enable --reserve-mb='auto'
   %end

   keyboard --xlayouts='br'
   lang pt_BR.UTF-8

   url --url="http://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64"

   %packages
   @^minimal-environment
   %end

   firstboot --enable

   ignoredisk --only-use=vda
   autopart
   clearpart --none --initlabel

   timezone America/Sao_Paulo --utc

   rootpw --lock
   user --groups=wheel --name=packer --password=$6$Jfl.H/kwDSeu7FWF$S/Mc/qsxM2DRupVkxKQawCgjQ3.i6beyCgY1fQCC0NUadW9pucgYnraGMWaCSg8g6t4GbozowE40X/FtAGert. --iscrypted --gecos="packer"

   reboot

   %post
   echo "packer   ALL=(ALL)   NOPASSWD: ALL" > /etc/sudoers.d/01packer
   /bin/chown root:root /etc/sudoers.d/01packer
   /bin/chmod 0440 /etc/sudoers.d/01packer

   yum install -y cloud-init qemu-guest-agent

   %end
   ```

4. **Build da Imagem**:
   Agora, inicialize o Packer e inicie o processo de criação da imagem com os seguintes comandos:

   ```bash
   packer init .
   PACKER_LOG=1 packer build os-install.pkr.hcl
   ```

5. **Conversão da Imagem**:
   Converta a imagem para o formato **qcow2**, que será utilizado pelo KVM:

   ```bash
   qemu-img convert -O qcow2 -c build/os-base/ol8-amd64.raw /home/gean/kvm/templates/ol8-amd64.qcow2
   ```

### Parte 3: Criando os Scripts Terraform

Nesta etapa, usaremos o **Terraform** para configurar e provisionar uma máquina virtual KVM utilizando a imagem criada pelo **Packer**. Abaixo estão os passos e scripts necessários.

1. **Estrutura do Diretório**: Certifique-se de estar em uma pasta onde deseja armazenar os arquivos Terraform (como `~/terraform/kvm`).

2. **Script Principal HCL - `main.tf`**:
   Este arquivo define os recursos necessários, incluindo o **provider libvirt** e a configuração da máquina virtual. Vamos criar o arquivo `main.tf` com o seguinte conteúdo:

   ```hcl
   terraform {
     required_providers {
       libvirt = {
         source = "dmacvicar/libvirt"
       }
     }
   }

   provider "libvirt" {
     uri = "qemu:///system"
   }

   resource "libvirt_volume" "os_image" {
     name   = "lvm.qcow2"
     pool   = "default"
     source = "/home/gean/kvm/templates/ol8-amd64.qcow2"
     format = "qcow2"
   }

   data "template_file" "user_data" {
     template = file("${path.module}/cloud_init.yml")
   }

   resource "libvirt_cloudinit_disk" "cloudinit_resized" {
     name      = "cloudinit_resized.iso"
     user_data = data.template_file.user_data.rendered
     pool      = "default"
   }

   resource "libvirt_domain" "lvm" {
     name   = "lvm"
     memory = "2048"
     vcpu   = 2

     cpu {
       mode = "host-passthrough"
     }

     cloudinit = libvirt_cloudinit_disk.cloudinit_resized.id

     network_interface {
       network_name   = "default"
       wait_for_lease = true
     }

     console {
       type        = "pty"
       target_port = "0"
       target_type = "serial"
     }

     disk {
       volume_id = libvirt_volume.os_image.id
     }

     graphics {
       type        = "spice"
       listen_type = "none"
     }
   }

   output "ip" {
     value = libvirt_domain.lvm.network_interface[0].addresses[0]
   }
   ```

3. **Arquivo YAML de Configuração do Cloud-init - `cloud_init.yml`**:
   Este arquivo configura o **cloud-init**, permitindo inicializar a VM com um usuário configurado e uma chave SSH. Crie o arquivo `cloud_init.yml` com o seguinte conteúdo:

   ```yml
   #cloud-config

   users:
     - name: gean
       gecos: "Gean Martins"
       sudo: "ALL=(ALL) NOPASSWD:ALL"
       shell: /bin/bash
       lock_passwd: false  
       ssh-authorized-keys:
         - ${file("~/.ssh/tfvms.pub")} 

   ssh_pwauth: true  

   chpasswd:
     list: |
       gean: $6$kp7ay8JwVMNBTlL1$xf/nfw3WWePI3PhzccOTaaNXiDVrhIBck6i4pKJ89897u3/xNbDXc5zf0LInnCN0HkP4A/jVbQVk3qTMt4hq/1 
     expire: false

   runcmd:
     - hostnamectl set-hostname lvm
   ```

### Parte 4: Executando o Terraform para Provisionar a VM

Agora que temos todos os scripts, vamos rodar o Terraform para criar a VM.

1. **Inicializar o Terraform**:
   Inicialize o diretório para que o Terraform baixe os providers necessários.

   ```bash
   terraform init
   ```

2. **Formatação e Validação dos Arquivos**:
   Formate e valide os arquivos `.tf` para assegurar que estão corretos.

   ```bash
   terraform fmt
   terraform validate
   ```

3. **Plano de Execução**:
   Gere o plano de execução para visualizar as alterações que o Terraform aplicará.

   ```bash
   terraform plan
   ```

4. **Aplicar Configurações**:
   Aplique as configurações para provisionar a VM.

   ```bash
   terraform apply
   ```

### Resultado

Ao final do processo, o Terraform retornará o IP da nova máquina virtual provisionada, disponível na saída `output "ip"`.

