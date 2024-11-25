---
title: "Criação de Imagem Base Minimalista com Kickstart, Cloud-Init, Terraform e Libvirt"
description: "Tutorial completo para a criação de uma imagem base minimalista usando Kickstart, incluindo automação de configuração com cloud-init e integração com Terraform e Libvirt."
date: 2024-10-09 13:00:00 -0300
version: "1.0"
tags:
  - Kickstart
  - Cloud-Init
  - Terraform
  - Libvirt
  - Virtualização
  - Automação
  - KVM
categories: [Virtualização, KVM, Kickstart, Terraform]
license: "MIT"
---

### Sumário:

1. [Introdução](#introdução)
2. [Parte 1: Criação de uma Imagem Base Minimalista com Kickstart](#parte-1-criação-de-uma-imagem-base-minimalista-com-kickstart)  
3. [Parte 2: Otimização e Compressão da Imagem Base](#parte-2-otimização-e-compressão-da-imagem-base)  
4. [Parte 3: Validação e Integração com Terraform e Libvirt](#parte-3-validação-e-integração-com-terraform-e-libvirt)  
5. [Conclusão](#conclusão)

### Introdução:

A criação de imagens base minimalistas é um processo essencial para automatizar o provisionamento de máquinas virtuais (VMs) em ambientes de virtualização, especialmente quando se utiliza **KVM**, **Libvirt**, e ferramentas de infraestrutura como código, como **Terraform**. Este tutorial detalha o processo de construção de uma imagem base otimizada e minimalista utilizando **Kickstart**, integrando-a com o **cloud-init** para configuração dinâmica, e preparando-a para ser reutilizada de forma eficiente.

Você aprenderá a configurar um ambiente de instalação automatizada, otimizar a imagem resultante com ferramentas como **virt-sparsify**, e generalizá-la para que possa ser utilizada como um template replicável. Finalmente, vamos validar todo o processo criando novas VMs com **Terraform** e configurando-as dinamicamente via **cloud-init**, garantindo que a infraestrutura seja escalável, leve e eficiente.

O objetivo final é permitir que você crie e gerencie ambientes virtuais com maior eficiência e controle, otimizando o uso de recursos de armazenamento e facilitando a replicação de VMs em grande escala.


## Parte 1: Criação de uma Imagem Base Minimalista com Kickstart

### Objetivo:
Criar uma imagem base automatizada com **Kickstart** e configurá-la para ser minimalista, incluindo o **cloud-init** para permitir a personalização dinâmica em VMs criadas posteriormente. Vamos iniciar com a criação do arquivo **Kickstart**.

### Requisitos:
- Sistema operacional: **Red Hat**, **CentOS**, ou **Oracle Linux**.
- Infraestrutura com **KVM**, **Libvirt**, e **Terraform** instalados e configurados.

### 1. Criando o Arquivo Kickstart

O **Kickstart** permite a automação completa da instalação, permitindo especificar a configuração do disco, rede, usuários e pacotes a serem instalados.

#### Arquivo Kickstart (kickstart.ks) Exemplo:

```bash
# Instalação em modo texto
text

# Idioma e layout do teclado
lang pt_BR.UTF-8
keyboard --xlayouts='br'

# Fuso horário
timezone America/Sao_Paulo --utc

# Configuração de rede via DHCP
network --bootproto dhcp

# Senha do root
rootpw --plaintext root

# Limpeza do disco e configuração automática de partições
zerombr
clearpart --all --initlabel
autopart

# Seleção de pacotes: ambiente minimalista e cloud-init
%packages
@^minimal-environment
cloud-init
%end

# Script pós-instalação para otimizar a imagem

%post
# Limpar o cache do dnf
dnf clean all

# Limpar arquivos temporários
rm -rf /tmp/* /var/tmp/*

# Limpar logs do sistema
rm -rf /var/log/* /var/log/journal/*

# Limpar cache do usuário root
rm -rf /root/.cache/*

# Zerar espaço não utilizado para melhor compactação (opcional)
dd if=/dev/zero of=/zerofile bs=1M || :
rm -f /zerofile

%end

# Reiniciar após a instalação
reboot
```

#### Explicação:
1. **Ambiente Minimalista**: A instalação utiliza o grupo `@^minimal-environment`, que instala apenas o sistema básico necessário. Adicionamos o **cloud-init** para permitir a configuração dinâmica das VMs.
2. **Remoção de Pacotes**: A remoção de pacotes indesejados como **NetworkManager**, **firewalld**, e **chrony** é feita após a instalação usando um script no `%post`. Isso é uma alternativa à remoção dentro da seção `%packages` para evitar problemas de dependência.
3. **Limpeza Pós-Instalação**: No `%post`, removemos arquivos temporários para garantir que a imagem final esteja limpa.

---

### 2. Iniciar a Instalação com Virt-Install

#### Baixando a ISO do Oracle Linux:
```bash
wget https://yum.oracle.com/ISOS/OracleLinux/OL9/u4/x86_64/OracleLinux-R9-U4-x86_64-dvd.iso -P /home/gean/kvm/isos/
```

Agora que temos o arquivo **Kickstart**, vamos usá-lo para iniciar a instalação da VM. O comando abaixo cria a VM com base no arquivo Kickstart, especificando também a localização da ISO e os parâmetros para inicialização automática.

#### Comando:

```bash
virt-install \
  --name ol9-tpl \
  --ram 2048 \
  --vcpus 2 \
  --disk path=/home/gean/kvm/images/ol9-tpl.qcow2,format=qcow2,size=32 \
  --os-variant ol9.4 \
  --network network=default \
  --graphics none \
  --location /home/gean/kvm/isos/OracleLinux-R9-U4-x86_64-dvd.iso \
  --initrd-inject=/home/gean/kvm/isos/kickstart.ks \
  --extra-args="inst.ks=file:/kickstart.ks console=tty0 console=ttyS0,115200n8"
```

#### Explicação:
- **`virt-install`**: Inicia a instalação da VM.
- **`--disk`**: Especifica o caminho e o formato do disco (QCOW2).
- **`--os-variant`**: Define a variante do sistema operacional, neste caso, **Oracle Linux 9.4**.
- **`--initrd-inject`**: Injeta o arquivo **kickstart.ks** na inicialização.
- **`--extra-args`**: Passa os parâmetros para que o **Kickstart** seja usado durante a instalação, além de configurar o console serial para o acesso.

---

### 3. Removendo Pacotes Desnecessários

Como os pacotes **NetworkManager**, **firewalld** e **chrony** podem ter dependências, a remoção deles é feita no `%post` do **Kickstart**. Isso garante que a instalação termine sem conflitos e que os pacotes sejam removidos com segurança após o sistema estar instalado.

- **Por que remover esses pacotes?**  
  Em ambientes minimalistas, pacotes como **NetworkManager** e **firewalld** podem não ser necessários, especialmente quando você está lidando com redes configuradas via **cloud-init** ou com scripts personalizados para gerenciamento de rede e firewall.

---

## Parte 2: Otimização e Compressão da Imagem Base

### 4. Generalização e Preparação da Imagem para Reuso

Agora que temos uma imagem otimizada, o próximo passo é prepará-la para uso como um template reutilizável. Para isso, precisamos remover dados específicos da VM (como chaves SSH, logs e UUIDs de rede) e garantir que a imagem possa ser personalizada dinamicamente com **cloud-init**.

#### Usando o Virt-Sysprep

O **virt-sysprep** é uma ferramenta que facilita a generalização de uma imagem de VM, removendo informações específicas que não devem ser replicadas em novas instâncias.

#### Comando para Generalização:

```bash
sudo virt-sysprep --domain ol9-tpl --operations user-account,ssh-hostkeys,logfiles,tmp-files,net-hwaddr,bash-history
```

- **Explicação**:
  - **`user-account`**: Remove contas de usuário criadas, exceto o root.
  - **`ssh-hostkeys`**: Remove as chaves SSH geradas pela máquina, garantindo que novas chaves sejam criadas ao inicializar novas VMs.
  - **`logfiles`**: Limpa os arquivos de log para remover registros de instalação.
  - **`tmp-files`**: Remove arquivos temporários para garantir que a imagem esteja limpa.
  - **`net-hwaddr`**: Remove os endereços MAC específicos da VM, permitindo que novas VMs gerem suas próprias configurações de rede.
  - **`bash-history`**: Limpa o histórico do shell para garantir que não haja rastros de comandos anteriores.
  
### 5. Otimização da Imagem com QEMU e Virt-Sparsify

Após a criação da imagem com o **virt-install**, ela pode conter blocos de disco que não estão sendo utilizados, o que aumenta o tamanho da imagem desnecessariamente. Usaremos as ferramentas **`qemu-img`** e **`virt-sparsify`** para reduzir o tamanho da imagem final e garantir que ela esteja otimizada para uso.

#### Passos para Otimização

1. **Converter a Imagem Inicial**:
   Vamos converter a imagem criada pelo **virt-install** para garantir que ela esteja no formato **QCOW2** e pronta para a otimização. Este passo também ajuda a garantir que quaisquer metadados desnecessários sejam removidos.

   ```bash
   qemu-img convert -O qcow2 /home/gean/kvm/images/ol9-tpl.qcow2 template-ol94.qcow2
   ```

   - **Explicação**: O comando `qemu-img convert` transforma a imagem original **QCOW2** em uma nova versão com o nome **template-ol94.qcow2**. O novo arquivo será o ponto de partida para as próximas otimizações.

2. **Otimizar com Virt-Sparsify**:
   O próximo passo é usar o **virt-sparsify** para reduzir o tamanho da imagem, removendo blocos de disco que não estão sendo usados. A opção `--in-place` garante que a operação seja feita diretamente na imagem, sem a criação de uma cópia extra.

   ```bash
   sudo virt-sparsify --in-place template-ol94.qcow2
   ```

   - **Explicação**: O **virt-sparsify** analisa o conteúdo da imagem e remove blocos de dados "vazios", ou seja, blocos que não contêm dados úteis. Isso é muito útil para reduzir o tamanho da imagem resultante.

3. **Converter e Comprimir a Imagem**:
   Agora vamos converter a imagem novamente para garantir que ela seja compactada. A opção `-c` no comando **`qemu-img`** ativa a compressão durante a conversão, o que ajuda a economizar espaço em disco.

   ```bash
   qemu-img convert -O qcow2 -c template-ol94.qcow2 OL94-TPL.qcow2
   ```

   - **Explicação**: O comando aplica compressão à imagem **template-ol94.qcow2**, criando uma versão compactada chamada **OL94-TPL.qcow2**. Isso resulta em uma imagem final que ocupa menos espaço, o que é ideal para ambientes com muitas VMs.

4. **Verificar Informações da Imagem**:
   Após a conversão e compressão, é importante verificar o estado da imagem para garantir que todas as otimizações foram aplicadas corretamente. O comando abaixo mostra detalhes como o tamanho virtual e real da imagem.

   ```bash
   qemu-img info OL94-TPL.qcow2
   ```

   - **Explicação**: O comando `qemu-img info` exibe detalhes sobre a imagem final, como o tamanho real e o virtual, além de confirmar que a compressão foi aplicada corretamente.

5. **Mover a Imagem para o Diretório de Templates**:
   Agora que a imagem está otimizada, mova-a para o diretório onde você armazena templates de VM para uso futuro com **Libvirt** e **Terraform**.

   ```bash
   mv OL94-TPL.qcow2 /home/gean/kvm/templates/
   ```

   - **Explicação**: Este passo organiza a imagem compactada no diretório correto, onde poderá ser reutilizada repetidamente para criar novas VMs a partir de um template otimizado.

---

## Parte 3: Validação e Integração com Terraform e Libvirt

### 6. Configuração do Terraform e Libvirt

Nesta seção, vamos configurar o **Terraform** para utilizar a imagem base otimizada e generalizada que criamos. O **Terraform** criará novas VMs no KVM usando **Libvirt**, e o **cloud-init** será responsável pela configuração dinâmica das VMs.

#### Passos para a Configuração:

1. **Arquivo `terraform.tfvars`**  
   Este arquivo conterá as variáveis de configuração, como o nome da VM, tamanho do disco, pool de armazenamento, chave SSH e o caminho da imagem otimizada.

```hcl
vm_template = {
  name            = "tf-vm-01"
  cpu             = 2
  memory          = 2048
  disksize        = 32
  storage_pool    = "default"
  hostname        = "tf-vm-01"
  os_image_name   = "tf_vm_01_image.qcow2"
  os_volume_name  = "tf_vm_01_volume.qcow2"
  network_name    = "default"
  cloud_init_file = "cloud-init.yml"
  os_image_url    = "/home/gean/kvm/templates/OL94-TPL.qcow2"
  ssh_key         = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOFu0apycWm7G+C5vebRXuAjNCPg2bIoReygFEGDFzYh your_email@example.com"
}
```

- **Explicação**:
  - **`name`**: Nome da VM.
  - **`cpu` e `memory`**: Configura o número de vCPUs e a memória RAM.
  - **`disksize`**: Tamanho do disco da VM em GB.
  - **`os_image_url`**: Caminho para o template otimizado.
  - **`ssh_key`**: Chave pública SSH que será injetada na VM via **cloud-init**.

2. **Arquivo `main.tf` para Definição da Infraestrutura**

Aqui, configuramos os recursos que o **Terraform** criará no KVM usando o provedor **Libvirt**. Isso inclui o volume da imagem do sistema, o disco de dados, o arquivo de **cloud-init**, e a VM em si.

```hcl
variable "vm_template" {
  type = map(any)
}

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

# Volume de imagem do sistema operacional
resource "libvirt_volume" "os_image" {
  name   = var.vm_template["os_image_name"]
  pool   = var.vm_template["storage_pool"]
  source = var.vm_template["os_image_url"]
  format = "qcow2"
}

# Volume de dados da VM
resource "libvirt_volume" "os_datas" {
  name           = var.vm_template["os_volume_name"]
  base_volume_id = libvirt_volume.os_image.id
  pool           = var.vm_template["storage_pool"]
  size           = var.vm_template["disksize"] * 1024 * 1024 * 1024 # Converte para bytes
}

# Cloud-Init para configuração dinâmica
data "template_file" "user_data" {
  template = file("${path.module}/${var.vm_template["cloud_init_file"]}")

  vars = {
    hostname = var.vm_template["hostname"]
    ssh_key  = var.vm_template["ssh_key"]
  }
}

resource "libvirt_cloudinit_disk" "cloudinit" {
  name      = "cloudinit.iso"
  user_data = data.template_file.user_data.rendered
  pool      = var.vm_template["storage_pool"]
}

# Definir a VM no KVM
resource "libvirt_domain" "domain" {
  name   = var.vm_template["name"]
  memory = var.vm_template["memory"]
  vcpu   = var.vm_template["cpu"]

  cpu {
    mode = "host-passthrough"
  }

  cloudinit = libvirt_cloudinit_disk.cloudinit.id

  network_interface {
    network_name   = var.vm_template["network_name"]
    wait_for_lease = true
  }

  console {
    type        = "pty"
    target_type = "virtio"
    target_port = "1"
  }

  disk {
    volume_id = libvirt_volume.os_datas.id
  }

  graphics {
    type        = "spice"
    listen_type = "address"
    autoport    = true
  }
}

# Output para exibir o IP da VM criada
output "ip" {
  value = libvirt_domain.domain.network_interface[0].addresses[0]
}
```

- **Explicação**:
  - **Volumes**: O volume da imagem do sistema e o volume de dados são definidos com base no template otimizado.
  - **Cloud-Init**: O arquivo de **cloud-init** é injetado para configurar dinamicamente as VMs (rede, SSH, etc.).
  - **VM**: Define a VM com o nome, quantidade de memória, CPU, e discos.
  - **Output**: O **Terraform** exibe o IP da VM assim que a criação estiver concluída.

3. **Arquivo `user_data.yml` para Cloud-Init**

Esse arquivo será usado pelo **cloud-init** para personalizar a nova VM com base nas variáveis passadas pelo **Terraform**.

```yaml
#cloud-config

# Configuração do Hostname
hostname: ${hostname}

# Criação de usuário
users:
  - name: gean
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: users, sudo
    shell: /bin/bash
    ssh-authorized-keys:
      - ${ssh_key}

# Definir senha root
chpasswd:
  list: |
    root:$6$R4tXC5apTx$f4WVAylB/SZ/0ppE7Zp4lurvzAhcm.BaU3xJKaoESu7cv13sR7RYVkjVQwxtvA9/vUggWu/a9N0L9EP1lg/Ez1
  expire: False

# Pacotes a serem instalados
packages:
  - qemu-guest-agent
  - bash-completion

# Atualização do sistema
package_update: true
package_upgrade: true
```

- **Explicação**:
  - **Hostname**: Definido dinamicamente via **cloud-init**.
  - **Usuário e SSH**: Cria um usuário com chave SSH injetada pelo **Terraform**.
  - **Atualizações e Pacotes**: Atualiza o sistema e instala pacotes essenciais, como o **qemu-guest-agent**, que permite a comunicação com a VM através do KVM.

---

### 7. Criando e Validando a Infraestrutura com Terraform

Agora que todos os arquivos de configuração estão prontos, vamos criar a infraestrutura e validar se a VM foi criada corretamente e se as personalizações via **cloud-init** foram aplicadas.

#### Comandos para Criar a Infraestrutura:

1. **Inicializar o Terraform**:
   ```bash
   terraform init
   ```

2. **Formatar e Validar os Arquivos**:
   ```bash
   terraform fmt
   terraform validate
   ```

3. **Planejar a Criação da Infraestrutura**:
   ```bash
   terraform plan
   ```

4. **Aplicar o Plano para Criar a Infraestrutura**:
   ```bash
   terraform apply
   ```

- **Explicação**:
  - O **Terraform** irá provisionar a VM no KVM, utilizando o template otimizado e generalizado. O **cloud-init** será injetado para personalizar a VM durante o boot.

---

### 8. Testes Finais

Após a criação da VM, é importante validar se ela foi corretamente configurada. Aqui estão alguns testes essenciais:

1. **Verifique se o **cloud-init** Concluiu Corretamente**:
   ```bash
   cloud-init status --long
   ```

   O comando acima exibirá o status das etapas do **cloud-init**.

2. **Conecte-se à VM via SSH**:
   Verifique se a chave SSH foi injetada corretamente e conecte-se à VM:

   ```bash
   ssh gean@<ip_da_vm>
   ```

3. **Verifique o Hostname e os Pacotes Instalados**:
   Após se conectar à VM, confirme se o hostname está correto e se os pacotes definidos foram instalados:

   ```bash
   hostnamectl
   dpkg -l | grep qemu-guest-agent
   ```

Se tudo estiver funcionando como esperado, o processo foi concluído com sucesso.

---

### Conclusão:

Neste tutorial, você aprendeu a criar uma imagem base minimalista usando **Kickstart**, garantindo que ela seja configurada para atender a requisitos de eficiência e automação. Utilizando ferramentas como **virt-sparsify** para otimizar o tamanho da imagem e **virt-sysprep** para remover informações específicas, você preparou uma imagem que pode ser usada como template reutilizável em infraestruturas virtualizadas.

Com a integração do **cloud-init**, suas VMs podem ser configuradas dinamicamente durante o processo de inicialização, permitindo a personalização de configurações como rede, SSH, hostname, e mais. A criação e gestão de VMs via **Terraform** e **Libvirt** garante que toda a infraestrutura seja escalável e alinhada com as melhores práticas de automação.

Agora, você possui um pipeline completo para criação e validação de imagens otimizadas, prontas para replicação e uso em ambientes de produção com **KVM**, **Terraform** e **cloud-init**, economizando tempo e recursos em cada novo deployment de VMs.

