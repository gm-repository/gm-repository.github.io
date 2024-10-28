---
title: "Expansão de Volumes Lógicos em VMs KVM com Terraform"
date: 2023-10-28 12:12:12 -0300
tags:
  - KVM
  - Terraform
  - LVM
  - Virtualização
  - Infraestrutura como Código
layout: post
excerpt: "Aprenda como expandir volumes lógicos em VMs gerenciadas pelo KVM utilizando o Terraform para provisionamento e configuração automatizada."
---


### Sumário

- [Introdução](#introdução)
- [Objetivo do Artigo](#objetivo-do-artigo)
- [Pré-requisitos](#pré-requisitos)
- [Passo a Passo Detalhado](#passo-a-passo-detalhado)
- [Adicionando e Configurando Volumes Físicos (PV), Grupos de Volumes (VG) e Volumes Lógicos (LV)](#adicionando-e-configurando-volumes-físicos-pv-grupos-de-volumes-vg-e-volumes-lógicos-lv)
- [Expandindo Volumes Lógicos (LV)](#expandindo-volumes-lógicos-lv)
- [Expansão do Armazenamento](#expansão-do-armazenamento)
- [Conclusão](#conclusão)


# **Expansão de Volumes Lógicos em VMs KVM com Terraform**

## Introdução

Com o crescimento constante dos dados e a flexibilidade exigida pela virtualização, o gerenciamento dinâmico de armazenamento tornou-se uma prática fundamental para equipes de TI. Especialmente em ambientes virtualizados, como os baseados no KVM (Kernel-based Virtual Machine), a capacidade de ajustar o armazenamento de volumes lógicos (LVM) permite que administradores de sistemas expandam e reorganizem volumes de forma eficiente. 

Neste artigo, vamos explorar como criar e expandir volumes lógicos em uma máquina virtual gerenciada pelo KVM usando o Terraform. O Terraform é uma ferramenta de Infraestrutura como Código (IaC) que facilita a criação e o gerenciamento de infraestruturas complexas, permitindo a automação do provisionamento de recursos, incluindo volumes de armazenamento.

O foco deste guia é mostrar, em passos detalhados, como configurar volumes físicos (PV), grupos de volumes (VG) e volumes lógicos (LV), além de expandi-los conforme a necessidade. A abordagem facilita o gerenciamento de armazenamento e torna o processo escalável, com operações de adição e redimensionamento sem interrupção de serviço.

---

## Objetivo do Artigo

Neste artigo, você aprenderá a:

- Configurar uma máquina virtual no KVM com Terraform.
- Adicionar e configurar volumes físicos e grupos de volumes.
- Criar e expandir volumes lógicos (LV) para atender a cenários de aumento de dados.
- Realizar o redimensionamento do sistema de arquivos para que ele possa utilizar o espaço expandido.

O público-alvo inclui administradores de sistemas, engenheiros de DevOps e desenvolvedores que desejam dominar o gerenciamento dinâmico de volumes lógicos em VMs KVM e Terraform. Além disso, para aqueles que possuem algum conhecimento em LVM e desejam aplicá-lo a ambientes virtualizados, este guia oferece uma abordagem prática e simplificada para a expansão de armazenamento.

---

## Pré-requisitos

Para seguir este guia, alguns conhecimentos e ferramentas são necessários para o correto provisionamento e expansão de volumes lógicos em uma VM KVM com Terraform.

### 1. **Conhecimentos Necessários**

   - **Familiaridade com Linux**: É importante ter um entendimento básico de comandos do Linux, já que a maioria das operações será executada em um sistema Linux.
   - **Conceitos de LVM (Logical Volume Manager)**: Recomenda-se entender os conceitos de PV (Volume Físico), VG (Grupo de Volume) e LV (Volume Lógico) para acompanhar o processo.
   - **Noções de Virtualização com KVM**: Conhecimento em operar máquinas virtuais (VMs) usando o KVM, um dos hipervisores de código aberto mais populares para Linux.
   - **Infraestrutura como Código (IaC) com Terraform**: Familiaridade com a sintaxe e o fluxo de trabalho do Terraform permitirá que você personalize as configurações conforme necessário.

### 2. **Ferramentas Necessárias**

Para configurar o ambiente, vamos usar **KVM** e **libvirt** para virtualização e o **Terraform** para gerenciamento e automação dos recursos. Caso precise de instruções detalhadas para instalar e configurar essas ferramentas em um ambiente Ubuntu, consulte o post completo no blog [Instalação e Configuração do KVM/LIBVIRT e Terraform no Ubuntu](https://geanmartins.com.br/posts/terraform-kvm/), onde cada etapa de configuração é explicada com detalhes.

### 3. **Configuração do Ambiente**

   - **Arquivo de Configuração `main.tf`**: Este arquivo conterá as definições de recursos do Terraform, incluindo a VM, volumes e rede. Ele será fornecido e explicado na seção de configuração do Terraform.
   - **Arquivo de Configuração `cloud_init.yml`**: Este arquivo será usado para configurar automaticamente o usuário e o hostname na VM.
   - **Acesso Root ou Sudo no Host**: Para executar comandos que exigem privilégios administrativos, como criação e conexão de volumes.

---

## Passo a Passo Detalhado

Nesta seção, configuraremos o ambiente KVM para provisionar uma máquina virtual com o Terraform. Em seguida, faremos a expansão do armazenamento da VM utilizando volumes físicos (PV), grupos de volumes (VG) e volumes lógicos (LV).

### 1. **Definindo o Ambiente KVM no Terraform**

Para começar, crie um arquivo chamado `main.tf`, que será usado para definir a infraestrutura da VM no KVM. Esse arquivo instrui o Terraform a se conectar ao KVM usando o provedor `libvirt`, a provisionar a máquina virtual e a configurar seu armazenamento, rede e recursos de hardware.

#### Configuração do Arquivo `main.tf`

Aqui está uma configuração de exemplo para o `main.tf`:

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
  name   = "file-server.qcow2"
  pool   = "default"
  source = "/home/gean/kvm/templates/ol8-amd64.qcow2" # Caminho da imagem base da VM
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

resource "libvirt_domain" "file-server" {
  name   = "file-server"
  memory = "2048" # Memória em MB
  vcpu   = 2      # Número de CPUs

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
  value = libvirt_domain.file-server.network_interface[0].addresses[0]
}
```

### 2. **Configurando o Cloud-init**

O próximo passo é criar um arquivo `cloud_init.yml` para configurar automaticamente o usuário e o hostname da VM durante o provisionamento. O cloud-init permite que você automatize configurações iniciais, como a criação de usuários e a autorização de chaves SSH.

#### Configuração do Arquivo `cloud_init.yml`

```yaml
#cloud-config

users:
  - name: gean
    gecos: "Gean Martins"
    sudo: "ALL=(ALL) NOPASSWD:ALL"
    shell: /bin/bash
    lock_passwd: false  
    ssh-authorized-keys:
      - ${file("~/.ssh/tfvms.pub")} # Caminho da chave SSH pública

ssh_pwauth: true  

chpasswd:
  list: |
     gean: $6$kp7ay8JwVMNBTlL1$xf/nfw3WWePI3PhzccOTaaNXiDVrhIBck6i4pKJ89897u3/xNbDXc5zf0LInnCN0HkP4A/jVbQVk3qTMt4hq/1 
  expire: false
    
runcmd:
  - hostnamectl set-hostname file-server
```

### 3. **Executando o Terraform para Provisionamento da VM**

Agora, com os arquivos `main.tf` e `cloud_init.yml` prontos, podemos iniciar o processo de provisionamento da VM com o Terraform. Siga os passos abaixo para inicializar e aplicar a configuração:

```bash
terraform init          # Inicializa o ambiente do Terraform
terraform fmt           # Formata o arquivo para padronização
terraform validate      # Valida a sintaxe do arquivo Terraform
terraform plan          # Mostra o plano de execução
terraform apply         # Aplica as configurações
```

Após a execução do comando `terraform apply`, você verá uma saída com o endereço IP da VM, indicando que os recursos foram criados com sucesso.

```bash
Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

ip = "192.168.122.236"
```

### 4. **Verificando o Status da VM**

Para garantir que a VM foi provisionada corretamente, execute o comando abaixo para listar as VMs em execução:

```bash
virsh list
 Id   Name          State
-----------------------------
 2    file-server   running
```

E para verificar os discos conectados à VM:

```bash
virsh domblklist file-server
 Target   Source
-------------------------------------------------------
 vda      file-server.qcow2
 hdd      /home/gean/kvm/images/cloudinit_resized.iso
```

---

## Adicionando e Configurando Volumes Físicos (PV), Grupos de Volumes (VG) e Volumes Lógicos (LV)

Nesta seção, vamos adicionar um novo disco à máquina virtual para expandir o armazenamento. Em seguida, configuraremos esse disco para ser utilizado como um volume físico (PV), adicioná-lo a um grupo de volumes (VG) e, finalmente, criar volumes lógicos (LV) que poderão ser montados e utilizados pelo sistema de arquivos.

### 1. **Adicionando um Novo Disco à VM**

O primeiro passo é adicionar um novo disco à VM usando o `virsh`. Este disco será configurado para armazenamento adicional.

1. Crie o novo volume de disco com o comando abaixo. Neste exemplo, o disco é criado com capacidade de 4GB e em formato `raw`:
   ```bash
   virsh vol-create-as --pool default --name file-server-vdb.img --capacity 4G --format raw
   ```

2. Anexe o disco recém-criado à VM:
   ```bash
   virsh attach-disk file-server /home/gean/kvm/images/file-server-vdb.img vdb --targetbus virtio --persistent
   ```

3. Confirme que o disco foi anexado corretamente, verificando a lista de discos da VM:
   ```bash
   virsh domblklist file-server
   Target   Source
   -------------------------------------------------------
   vda      file-server.qcow2
   vdb      /home/gean/kvm/images/file-server-vdb.img
   hdd      /home/gean/kvm/images/cloudinit_resized.iso
   ```

---

### 2. **Configurando o Volume Físico (PV)**

Agora que o disco foi adicionado, vamos configurar o dispositivo `/dev/vdb` como um Volume Físico (PV) para que ele possa ser gerenciado pelo LVM.

1. Conecte-se à VM usando o SSH:
   ```bash
   ssh gean@<IP_da_VM>
   ```

2. Verifique se o disco `/dev/vdb` está listado como um dispositivo disponível:
   ```bash
   lsblk
   ```

   A saída deve mostrar o novo dispositivo `/dev/vdb` com 4GB de espaço disponível.

3. Use o comando `pvcreate` para configurar o disco como um volume físico:
   ```bash
   sudo pvcreate /dev/vdb
   ```

4. Verifique o status dos volumes físicos:
   ```bash
   sudo pvs
   ```

   A saída deve confirmar que `/dev/vdb` foi configurado com sucesso como um PV.

---

### 3. **Criando um Grupo de Volume (VG)**

Com o volume físico (PV) criado, o próximo passo é configurar um grupo de volume (VG) para gerenciar o espaço de armazenamento.

1. Crie um novo grupo de volume chamado `vg_data` e adicione o volume físico `/dev/vdb` a ele:
   ```bash
   sudo vgcreate vg_data /dev/vdb
   ```

2. Confirme a criação do VG verificando com o comando `vgs`:
   ```bash
   sudo vgs
   ```

   A saída deve mostrar o novo VG `vg_data` com 4GB de espaço disponível.

---

### 4. **Criando Volumes Lógicos (LV)**

Agora que o grupo de volumes está configurado, vamos criar volumes lógicos (LV) dentro dele. Esses LVs serão utilizados como partições de armazenamento montáveis.

1. Crie um volume lógico de 2GB chamado `alunos`:
   ```bash
   sudo lvcreate -L 2G -n alunos vg_data
   ```

2. Em seguida, crie outro volume lógico chamado `professor` que utiliza 50% do espaço restante no grupo de volumes:
   ```bash
   sudo lvcreate -l 50%FREE -n professor vg_data
   ```

3. Verifique o status dos volumes lógicos:
   ```bash
   sudo lvs
   ```

   A saída deve mostrar os LVs `alunos` e `professor` com os tamanhos definidos.

---

### 5. **Montando os Volumes Lógicos**

Por fim, vamos formatar os volumes lógicos com o sistema de arquivos XFS e montá-los em diretórios específicos.

1. Formate o volume `alunos` com o sistema de arquivos XFS:
   ```bash
   sudo mkfs.xfs /dev/vg_data/alunos
   ```

2. Formate o volume `professor` com o sistema de arquivos XFS:
   ```bash
   sudo mkfs.xfs /dev/vg_data/professor
   ```

3. Crie os diretórios onde os volumes serão montados:
   ```bash
   sudo mkdir /mnt/alunos
   sudo mkdir /mnt/professor
   ```

4. Monte os volumes nos diretórios criados:
   ```bash
   sudo mount /dev/vg_data/alunos /mnt/alunos/
   sudo mount /dev/vg_data/professor /mnt/professor/
   ```

5. Verifique se os volumes estão montados corretamente usando `df -h`:
   ```bash
   df -h
   ```

---


## Expandindo Volumes Lógicos (LV)

Ao longo do tempo, a necessidade de armazenamento pode aumentar, especialmente em ambientes dinâmicos. Para acomodar esse crescimento, o LVM permite expandir facilmente os volumes lógicos existentes, e o sistema de arquivos XFS permite redimensionar o espaço sem desmontar o volume, garantindo uma operação contínua.

### 1. **Simulação de Dados no LV**

Para ilustrar o processo de expansão, vamos simular um cenário em que o volume lógico `professor` está próximo de sua capacidade total. Isso demonstrará a necessidade de expandir o volume.

1. Gere dados aleatórios no volume `professor` para preencher o espaço existente:
   ```bash
   sudo dd if=/dev/urandom of=/mnt/professor/file-01.txt bs=1M count=900
   ```
   
   Esse comando grava cerca de 900 MB de dados no volume `professor`.

2. Verifique o espaço utilizado no volume `professor`:
   ```bash
   sudo df -h | grep professor
   ```
   
   A saída mostrará que o volume está quase cheio, criando um cenário onde a expansão do volume é necessária.

---

### 2. **Expandindo o Volume Lógico (LV)**

Com o volume `professor` próximo de sua capacidade, vamos expandi-lo. O LVM facilita esse processo com o comando `lvextend`, que aumenta o tamanho do volume conforme especificado.

1. Verifique o espaço livre disponível no grupo de volume `vg_data`:
   ```bash
   sudo vgs | grep vg_data
   ```

2. Expanda o volume lógico `professor` em 512 MB:
   ```bash
   sudo lvextend -L +512M /dev/vg_data/professor
   ```

   A saída deve indicar que o volume `professor` foi expandido com sucesso.

3. Confirme o novo tamanho do volume `professor`:
   ```bash
   sudo lvdisplay /dev/vg_data/professor
   ```

   A saída deve mostrar o tamanho atualizado do volume lógico.

---

### 3. **Redimensionando o Sistema de Arquivos XFS**

Após expandir o volume lógico, é necessário redimensionar o sistema de arquivos para que ele utilize o novo espaço disponível. Com o XFS, essa operação pode ser realizada com o sistema de arquivos montado, o que facilita o processo sem necessidade de interromper o serviço.

1. Use o comando `xfs_growfs` para redimensionar o sistema de arquivos no volume `professor`:
   ```bash
   sudo xfs_growfs /mnt/professor
   ```

   A saída do comando indicará que o sistema de arquivos foi expandido para cobrir o novo espaço.

2. Verifique o novo espaço disponível no volume `professor` usando `df -h`:
   ```bash
   df -h | grep professor
   ```

   A saída deve mostrar o aumento de espaço disponível no volume montado.

---


## Expansão do Armazenamento

Em alguns casos, apenas expandir um volume lógico existente não é suficiente. Quando o grupo de volumes (VG) atinge sua capacidade máxima, é necessário adicionar um novo disco físico para expandir o armazenamento. Nesta seção, mostraremos como adicionar um disco físico, configurá-lo como um PV e, em seguida, integrá-lo ao grupo de volumes existente para suportar novas expansões de LVs.

### 1. **Adicionar um Novo Disco ao Sistema**

Começaremos adicionando um novo disco à VM, usando o `virsh`, assim como fizemos anteriormente.

1. No host, crie o novo volume de disco com o comando abaixo. Neste exemplo, o disco é criado com capacidade de 4GB e no formato `raw`:
   ```bash
   virsh vol-create-as --pool default --name file-server-vdc.img --capacity 4G --format raw
   ```

2. Anexe o disco recém-criado à VM:
   ```bash
   virsh attach-disk file-server /home/gean/kvm/images/file-server-vdc.img vdc --targetbus virtio --persistent
   ```

3. Verifique se o disco foi anexado corretamente:
   ```bash
   virsh domblklist file-server
   ```

   A saída deve mostrar o novo dispositivo `vdc` como parte dos discos da VM.

---

### 2. **Criando um Novo Volume Físico (PV)**

Agora, com o novo disco `/dev/vdc` conectado à VM, vamos configurá-lo como um PV para que ele possa ser adicionado ao grupo de volumes `vg_data`.

1. Conecte-se à VM via SSH:
   ```bash
   ssh gean@<IP_da_VM>
   ```

2. Verifique se o disco `/dev/vdc` está disponível:
   ```bash
   lsblk
   ```

   A saída deve mostrar o novo dispositivo `/dev/vdc` com 4GB de espaço.

3. Use o comando `pvcreate` para configurar o novo disco como um volume físico:
   ```bash
   sudo pvcreate /dev/vdc
   ```

4. Confirme a criação do PV verificando com `pvs`:
   ```bash
   sudo pvs
   ```

   A saída deve mostrar `/dev/vdc` como um novo volume físico.

---

### 3. **Expandindo o Grupo de Volumes (VG)**

Com o novo volume físico (PV) criado, vamos adicioná-lo ao grupo de volumes existente, `vg_data`. Isso aumentará a capacidade total do grupo, permitindo a expansão dos volumes lógicos associados.

1. Use o comando `vgextend` para adicionar o PV `/dev/vdc` ao grupo de volumes `vg_data`:
   ```bash
   sudo vgextend vg_data /dev/vdc
   ```

2. Confirme a expansão do VG usando `vgs`:
   ```bash
   sudo vgs
   ```

   A saída deve mostrar o grupo `vg_data` com a capacidade aumentada, incluindo o novo PV.

---

### 4. **Expandindo o Volume Lógico (LV)**

Agora que o grupo de volumes foi expandido, podemos aumentar o volume lógico `professor` para utilizar o espaço adicional.

1. Expanda o volume lógico `professor` em mais 2GB:
   ```bash
   sudo lvextend -L +2G /dev/vg_data/professor
   ```

   A saída deve confirmar que o volume `professor` foi expandido com sucesso.

2. Para redimensionar o sistema de arquivos XFS e utilizar o novo espaço, execute:
   ```bash
   sudo xfs_growfs /mnt/professor
   ```

3. Verifique o novo espaço disponível no volume `professor` com `df -h`:
   ```bash
   df -h | grep professor
   ```

   A saída deve mostrar o espaço atualizado e disponível para uso.

---

Essa etapa finaliza o processo de expansão de armazenamento adicionando novos discos e integrando-os ao grupo de volumes. Esse método de expansão modular permite ajustar a capacidade de armazenamento da VM de maneira contínua, mantendo a flexibilidade e o aproveitamento máximo dos recursos.


## Conclusão

Expandir volumes lógicos em uma máquina virtual gerenciada pelo KVM usando o Terraform oferece uma abordagem eficiente e escalável para gerenciar armazenamento em ambientes virtualizados. Neste artigo, abordamos desde a configuração inicial do ambiente e o provisionamento de uma VM até a criação e expansão de volumes lógicos. Esse processo simplifica o gerenciamento de armazenamento e permite que a infraestrutura se adapte facilmente ao crescimento dos dados.

Ao longo do tutorial, você aprendeu como:

- Provisionar uma VM no KVM com o Terraform.
- Configurar e gerenciar volumes físicos (PV), grupos de volumes (VG) e volumes lógicos (LV) com o LVM.
- Realizar expansões de volumes lógicos e redimensionar o sistema de arquivos XFS sem interromper o funcionamento da VM.
  
Essas técnicas são valiosas em ambientes de produção, onde a flexibilidade e a capacidade de redimensionamento dinâmico são essenciais. Além disso, a combinação do Terraform com o LVM permite um gerenciamento padronizado e replicável, que pode ser automatizado conforme as necessidades da infraestrutura.

### Recomendações Finais

- **Planejamento de Armazenamento**: Avalie regularmente o consumo de espaço e antecipe a necessidade de expansão para evitar limites críticos.
- **Backups e Snapshots**: Antes de realizar expansões ou redimensionamentos significativos, considere criar backups ou snapshots dos volumes.
- **Automação e Monitoramento**: Ferramentas de monitoramento de capacidade e alertas podem ajudar a automatizar futuras expansões de volumes, tornando o processo ainda mais eficaz.

A expansão de volumes lógicos com Terraform e KVM é um método poderoso para atender às demandas de ambientes em constante crescimento, permitindo que a infraestrutura seja dimensionada de forma rápida e controlada. 

--- 