---
title: "Criação de Imagem KVM do Oracle Linux 9 Usando o Packer"
date: 2024-10-24 22:01:01
version: "1.0"
description: "Este tutorial detalha os passos para criar uma imagem KVM do Oracle Linux 9 utilizando o Packer e o QEMU no Ubuntu."
categories:
  - Virtualização
  - Automação
  - Oracle Linux
  - Packer
tags:
  - KVM
  - Packer
  - Oracle Linux
  - QCOW2
  - QEMU
license: "MIT"
---

### Instalação do Packer no Ubuntu
Para instalar o Packer no Ubuntu, execute os comandos abaixo:
```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install packer
```
Verifique se o Packer foi instalado corretamente com o seguinte comando:
```bash
packer -v
```

### Criando os Scripts
Organize os arquivos de configuração criando um diretório específico:
```bash
mkdir -p ~/packer/ol9
cd ~/packer/ol9
```

#### Script HCL - os-install.pkr.hcl
Crie o arquivo `os-install.pkr.hcl` com o conteúdo abaixo, que define a imagem que será gerada usando Packer e QEMU:
```hcl
packer {
  required_version = ">= 1.5.0"  # Garantia de que a versão do Packer seja compatível
  required_plugins {
    qemu = {
      version = "= 1.1.0"
      source  = "github.com/hashicorp/qemu"
    }
  }
}

source "qemu" "iso" {
  vm_name          = "ol9-amd64.raw"
  iso_url          = "https://yum.oracle.com/ISOS/OracleLinux/OL9/u4/x86_64/OracleLinux-R9-U4-x86_64-boot.iso"
  iso_checksum     = "975de11be8761efa4aa2c87d7d3bedcb62c9dc956909b68f62c99062d11599e9"
  memory           = 2048
  disk_image       = false
  output_directory = "build/os-base"
  accelerator      = "kvm"
  disk_size        = "16384M"
  disk_interface   = "virtio"
  format           = "raw"
  net_device       = "virtio-net"
  boot_wait        = "3s"
  boot_command     = [
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
**Nota:** Confirme a compatibilidade das versões mencionadas no script.

#### Script Kickstart - http/ks.cfg
Agora, crie o diretório `http` e adicione o arquivo Kickstart `ks.cfg`, que automatiza a instalação do sistema operacional:
```bash
mkdir http
```

No arquivo `http/ks.cfg`, insira o seguinte conteúdo:
```bash
graphical

%addon com_redhat_kdump --enable --reserve-mb='auto'
%end

keyboard --xlayouts='br'
lang pt_BR.UTF-8

url --url="http://yum.oracle.com/repo/OracleLinux/OL9/baseos/latest/x86_64"

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
%end
```

### Criando o Template
Inicialize o Packer e inicie a criação da imagem com o comando:
```bash
packer init .
PACKER_LOG=1 packer build os-install.pkr.hcl
```

### Convertendo a Imagem para QCOW2
Após a conclusão do processo de build, converta a imagem para o formato QCOW2, que é amplamente utilizado com KVM:
```bash
qemu-img convert -O qcow2 -c build/os-base/ol9-amd64.raw ol9-amd64.qcow2
qemu-img info ol9-amd64.qcow2
```
