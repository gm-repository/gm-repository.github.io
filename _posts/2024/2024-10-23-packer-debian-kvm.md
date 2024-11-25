---
title: "Criação de Imagem KVM Debian Usando o Packer"
description: "Passo a passo para criar uma imagem Debian 12 usando o Packer e KVM, com instalação automatizada via Preseed e otimização da imagem QCOW2."
date: 2024-10-23 21:18:00 -0300
tags:
  - KVM
  - Packer
  - Debian
  - Preseed
  - Automação
  - Infraestrutura como Código
categories:
  - DevOps
  - Packer
  - Virtualização
  - Infraestrutura
  - Automação
---


## Criação de Imagem KVM Debian Usando o Packer

### 1. Instalação do Packer no Ubuntu

O Packer é uma ferramenta de automação que permite a criação de imagens de máquina de maneira repetível. Para instalá-lo no Ubuntu, siga os passos abaixo:

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install packer
```

### 2. Criando os Scripts

Crie um diretório para armazenar os arquivos do Packer e scripts de configuração:

```bash
mkdir -p ~/packer/debian-12
cd ~/packer/debian-12
```

#### Script HCL - `os-install.pkr.hcl`

Esse script define o processo de criação da imagem usando o **plugin QEMU** do Packer e a instalação do Debian usando um arquivo Preseed para automatizar.

```hcl
packer {
  required_version = ">= 1.5.0"
  required_plugins {
    qemu = {
      version = ">= 1.0.0"
      source = "github.com/hashicorp/qemu"
    }
  }
}

source "qemu" "debian" {
  iso_url              = "https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.7.0-amd64-netinst.iso"
  iso_checksum         = "8fde79cfc6b20a696200fc5c15219cf6d721e8feb367e9e0e33a79d1cb68fa83"
  output_directory     = "build/debian-12"
  disk_size            = "8192M"
  memory               = "2048"
  format               = "qcow2"
  headless             = false
  boot_wait            = "10s"
  accelerator          = "kvm"
  disk_interface       = "virtio"
  net_device           = "virtio-net"

  boot_command         = [
    "<esc><wait>",
    "auto preseed/url=http://{% raw %}{{.HTTPIP}}{% endraw %}:{% raw %}{{.HTTPPort}}{% endraw %}/preseed.cfg ",
    "debian-installer=en_US auto locale=en_US kbd-chooser/method=us ",
    "hostname=debian-tpl ",
    "fb=false ",
    "debconf/frontend=noninteractive ",
    "keyboard-configuration/xkb-keymap=us ",
    "<enter>"
  ]

  shutdown_command     = "echo 'packer' | sudo -S shutdown -P now"
  http_directory       = "http"
  ssh_username         = "packer"
  ssh_password         = "packer"
  ssh_timeout          = "60m"
}

build {
  name    = "debian-12-tpl"
  sources = ["source.qemu.debian"]
}
```

Esse script:
- **Baixa a ISO do Debian 12.7.0**, inicializa a instalação via QEMU/KVM.
- **Configura o boot e o preseed** para automatizar a instalação.
- **Define o formato QCOW2** e outras configurações para a VM.
- **Automatiza o shutdown** após a instalação.

#### Script Preseed - `http/preseed.cfg`

Crie um diretório para armazenar o arquivo Preseed, que automatiza a instalação do Debian:

```bash
mkdir http
```

Agora, crie o arquivo `http/preseed.cfg`:

```bash
## http/preseed.cfg

# Configurações de localização e idioma
d-i localechooser/shortlist select BR
d-i localechooser/languagelist select pt_BR
d-i debian-installer/country string BR
d-i debconf/language string pt_BR
d-i debian-installer/locale select pt_BR.UTF-8

# Configuração do teclado
d-i keyboard-configuration/xkb-keymap select br
d-i keyboard-configuration/layoutcode string br
d-i keyboard-configuration/toggle select No toggling

# Rede e Hostname
d-i netcfg/choose_interface select auto
d-i netcfg/get_hostname string debian-tpl
d-i netcfg/get_domain string local

# Configuração do espelho de rede
d-i apt-setup/use_mirror boolean true
d-i mirror/country string BR
d-i mirror/http/hostname string http.deb.debian.org
d-i mirror/http/directory string /debian
d-i mirror/http/proxy string
d-i apt-setup/services-select security

# Configuração de Timezone e Relógio
d-i clock-setup/utc boolean true
d-i time/zone string America/Sao_Paulo

# Particionamento Automático - LVM
d-i partman-auto/disk string /dev/vda
d-i partman-auto/method string lvm
d-i partman-lvm/device_remove_lvm boolean true
d-i partman-md/device_remove_md boolean true
d-i partman-lvm/confirm boolean true
d-i partman-lvm/confirm_nooverwrite boolean true

# Forçar o particionamento e gravação no disco sem confirmação
d-i partman/choose_partition select finish
d-i partman/confirm boolean true
d-i partman/confirm_nooverwrite boolean true

# Senha do Root
d-i passwd/root-password password packer
d-i passwd/root-password-again password packer

# Criar o usuário `packer` com permissões de sudo
d-i passwd/user-fullname string Packer User
d-i passwd/username string packer
d-i passwd/user-password password packer
d-i passwd/user-password-again password packer
d-i passwd/user-default-groups string sudo

# Adicionar o usuário `packer` ao grupo sudo
d-i preseed/late_command string in-target adduser packer sudo

# Seleção de pacotes a serem instalados
tasksel tasksel/first multiselect standard, ssh-server
d-i pkgsel/upgrade select full-upgrade
d-i pkgsel/include string sudo cloud-init

# Instalação do Grub
d-i grub-installer/bootdev string /dev/vda

# Reinicialização após instalação
d-i finish-install/reboot_in_progress note
```

Esse arquivo Preseed configura:
- **Localização e idioma**: Definido para português brasileiro (pt_BR).
- **Particionamento automático com LVM**.
- **Criação do usuário `packer` com permissões de sudo**.
- **Instalação do servidor SSH e pacotes adicionais** como `sudo` e `cloud-init`.

### 3. Criando o Template

Agora que os scripts estão prontos, inicialize o Packer e construa a imagem:

```bash
packer init .
PACKER_LOG=1 packer build os-install.pkr.hcl
```

O Packer irá baixar a ISO do Debian, iniciar a instalação automática com base no Preseed e, em seguida, criar a imagem no formato QCOW2.

### 4. Recompactar a Imagem QCOW2

Depois que a imagem for criada, você pode recompactá-la para otimizar o tamanho usando o `qemu-img`:

```bash
qemu-img convert -O qcow2 -c build/debian-12/packer-debian debian-12-tpl.qcow2
qemu-img info debian-12-tpl.qcow2
```

Isso irá:
- **Recompactar a imagem QCOW2**, reduzindo seu tamanho para economizar espaço.
- **Verificar as informações da imagem** com `qemu-img info`.

---

### Conclusão

Com este tutorial, você pode criar uma imagem Debian otimizada no formato **QCOW2** para uso no **KVM** utilizando o **Packer** e o **Preseed**. Esta abordagem automatiza todo o processo de instalação, desde a configuração da VM até a instalação de pacotes essenciais, e otimiza a imagem final para uso como template.
