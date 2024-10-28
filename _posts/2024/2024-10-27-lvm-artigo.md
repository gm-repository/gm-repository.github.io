---
layout: post
title: "Gerenciamento Avançado de Volumes Lógicos com LVM em Ambientes KVM e Terraform"
date: 2024-10-27 22:22:22 -0300
categories: [LVM, KVM, Terraform, Virtualização, Infraestrutura]
tags: [LVM, KVM, Terraform, snapshots, espelhamento, provisionamento dinâmico]
summary: "Um guia completo para configurar e gerenciar volumes lógicos usando LVM em ambientes KVM com automação pelo Terraform. Inclui operações avançadas como snapshots, espelhamento e provisionamento dinâmico."
---

### Sumário
1. [Introdução](#introdução)  
2. [Infraestrutura KVM com Terraform](#infraestrutura-kvm-com-terraform)  
3. [Configuração e Gerenciamento de LVM](#configuração-e-gerenciamento-de-lvm)  
4. [Criando e Gerenciando Logical Volumes (LVs)](#criando-e-gerenciando-logical-volumes-lvs)   
5. [Formatação e Montagem de LVs](#formatação-e-montagem-de-lvs)  
6. [Operações Avançadas com LVM](#operações-avançadas-com-lvm)  
7. [Mesclagem de Snapshots com o Volume Original](#mesclagem-de-snapshots-com-o-volume-original)  
8. [Provisionamento Dinâmico (Thin Provisioning)](#provisionamento-dinâmico-thin-provisioning)  
9. [Considerações Finais e Práticas Recomendadas](#considerações-finais-e-práticas-recomendadas)  
10. [Conclusão](#conclusão)  


### Introdução

#### Visão Geral
A **Gerência de Volumes Lógicos (LVM)** oferece uma abordagem eficiente e flexível para o gerenciamento de discos, especialmente em ambientes onde a elasticidade de armazenamento é crucial. Com o LVM, é possível realizar redimensionamento dinâmico de volumes e adicionar ou remover discos, permitindo uma gestão de recursos simplificada.

#### Objetivo
Este estudo apresenta uma configuração prática de LVM em um ambiente virtualizado usando **KVM** e gerenciamento de infraestrutura com **Terraform**. Serão abordadas etapas como a criação de volumes físicos (PVs), grupos de volumes (VGs) e volumes lógicos (LVs), além de operações avançadas, como provisionamento dinâmico, snapshots, espelhamento e redimensionamento de volumes.

---

### Infraestrutura KVM com Terraform

Para a criação do ambiente virtualizado e configuração inicial, utilizamos o **Terraform** com o provedor **Libvirt** para gerenciar máquinas virtuais em uma infraestrutura KVM.

#### Código de Configuração - `main.tf`

O código abaixo mostra a configuração inicial do Terraform para provisionamento do ambiente KVM com um volume de imagem do sistema operacional (OS) e uma unidade de inicialização `cloud-init`:

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
  name   = "lvm-lfce.qcow2"
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

resource "libvirt_domain" "lvm-lfce" {
  name   = "lvm-lfce"
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
  value = libvirt_domain.lvm-lfce.network_interface[0].addresses[0]
}
```

#### Explicação do Código

1. **Provedor Libvirt**: Configura o provedor `libvirt`, especificando a URI para o KVM. Esse provedor permite o gerenciamento de recursos KVM pelo Terraform.
2. **Volume do SO**: Cria um volume de disco baseado no arquivo `ol8-amd64.qcow2`, utilizado como sistema operacional base para a VM.
3. **Configuração Cloud-init**: Usa um arquivo `cloud-init` para configurar o ambiente inicial da VM, adicionando um usuário com permissão `sudo`.
4. **Definição da VM**: A VM `lvm-lfce` é configurada com 2 GB de memória e 2 vCPUs, além de utilizar `host-passthrough` para compatibilidade com a CPU física.
5. **Interface de Rede e Console**: Define a interface de rede com `network_name` e configura o console como `pty`, permitindo acesso serial.
6. **Saída do IP**: Ao final do provisionamento, o Terraform exibe o endereço IP da VM para acesso.


#### Código de Configuração - `cloud_init.yml`
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
  - hostnamectl set-hostname lvm-lfce
```

#### Criando a Infraestrutura

Para iniciar a infraestrutura, execute os seguintes comandos:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

Essa etapa conclui o provisionamento da máquina virtual com todas as configurações necessárias para começar o gerenciamento de volumes lógicos (LVM).

---

### Configuração e Gerenciamento de LVM

#### Adicionando Discos Virtuais

Após a criação da infraestrutura inicial, é necessário adicionar discos virtuais para configurar o LVM. O exemplo abaixo utiliza o comando `virsh` para criar e anexar três discos adicionais de 10 GB cada:

```bash
virsh vol-create-as --pool default --name lvm-lfcs-vdb.img --capacity 10G --format raw
virsh vol-create-as --pool default --name lvm-lfcs-vdc.img --capacity 10G --format raw
virsh vol-create-as --pool default --name lvm-lfcs-vdd.img --capacity 10G --format raw

virsh attach-disk lvm-lfce /home/gean/kvm/images/lvm-lfcs-vdb.img vdb --targetbus virtio --persistent
virsh attach-disk lvm-lfce /home/gean/kvm/images/lvm-lfcs-vdc.img vdc --targetbus virtio --persistent
virsh attach-disk lvm-lfce /home/gean/kvm/images/lvm-lfcs-vdd.img vdd --targetbus virtio --persistent
```

Esses discos adicionais serão utilizados como **Physical Volumes (PVs)** para compor os volumes lógicos.

#### Definindo os Physical Volumes (PVs)

Com os discos criados, é possível configurá-los como Physical Volumes (PVs) usando o comando `pvcreate`. Estes PVs formarão a base para a criação do Volume Group (VG):

```bash
sudo pvcreate /dev/vdb /dev/vdc /dev/vdd
```

**Visualizando PVs Criados**:
Após criar os PVs, use `pvs` ou `pvdisplay` para verificar a criação:

```bash
sudo pvs
sudo pvdisplay
```

#### Criando o Volume Group (VG)

Um **Volume Group (VG)** agrupa vários PVs, permitindo que o espaço disponível seja gerenciado de maneira unificada. Neste exemplo, o VG denominado `myvol` é criado com os três PVs configurados:

```bash
sudo vgcreate myvol /dev/vdb /dev/vdc /dev/vdd
```

**Exibindo Detalhes do VG**:
Use `vgdisplay` para exibir informações detalhadas sobre o VG recém-criado:

```bash
sudo vgdisplay myvol
```

Neste ponto, o `myvol` possui aproximadamente 30 GB, distribuídos entre os três PVs, com um **Physical Extent (PE)** padrão de 4 MB.

---

### Criando e Gerenciando Logical Volumes (LVs)

#### Criando Logical Volumes

Um **Logical Volume (LV)** representa a unidade de armazenamento que será montada e utilizada diretamente. Aqui, criamos um LV denominado `12extents` com 48 MB, distribuindo os PEs entre os PVs para otimizar a performance de I/O:

```bash
sudo lvcreate -l 12 -n 12extents myvol /dev/vdb:1-4 /dev/vdc:1-4 /dev/vdd:1-4
```

#### Opções de Distribuição de Dados

Ao criar LVs, é possível especificar a distribuição dos dados entre os discos para aumentar a performance. O comando `lvcreate` com a opção `-i` (stripe) permite criar volumes distribuídos entre múltiplos discos, garantindo maior velocidade de leitura e escrita:

```bash
sudo lvcreate -l 12 -i3 -n volstriped myvol
```

**Exibindo LVs Criados**:
Use o comando `lvs` para verificar os volumes lógicos criados:

```bash
sudo lvs
```

---

### Formatação e Montagem de LVs

Após a criação dos Logical Volumes, o próximo passo é formatá-los e montá-los em pontos específicos do sistema de arquivos. Neste exemplo, utilizamos o sistema de arquivos **XFS** para o volume `volstriped`:

```bash
sudo mkfs.xfs /dev/myvol/volstriped
```

Em seguida, crie um diretório e monte o volume:

```bash
sudo mkdir /mnt/lvm1
sudo mount /dev/myvol/volstriped /mnt/lvm1
```

**Verificando Montagem**:
Para verificar se o volume foi montado corretamente, use o comando `df`:

```bash
df -hT | grep myvol
```

Esta etapa conclui a configuração e montagem inicial dos Logical Volumes.

---

### Operações Avançadas com LVM

#### Espelhamento de Volumes

O espelhamento de volumes lógicos funciona como uma configuração **RAID 1**, garantindo redundância de dados. Quando configurado, o LVM mantém uma cópia dos dados em outro Physical Volume (PV), de modo que, em caso de falha, os dados possam ser recuperados a partir do espelho.

Para criar um volume lógico espelhado, utilize a opção `-m`, onde o número especificado representa o número de cópias:

```bash
sudo lvcreate -L 10M -m1 -n mirror myvol
```

Neste exemplo, o LV `mirror` é criado com uma cópia dos dados, sendo armazenada em dois PVs distintos do VG `myvol`.

**Verificando o Volume Espelhado**:
Para visualizar o volume espelhado, use o comando `lvdisplay`:

```bash
sudo lvdisplay myvol/mirror
```

#### Redimensionamento de Volumes Lógicos

A capacidade de redimensionar Logical Volumes é uma das principais vantagens do LVM. Com os comandos `lvextend` e `lvreduce`, é possível aumentar ou diminuir o tamanho de um LV conforme necessário. 

- **Expansão de um LV**: O comando abaixo expande o LV `mirror` em 12 MB:

    ```bash
    sudo lvextend -L +12M myvol/mirror
    ```

- **Redução de um LV**: A redução de um LV requer que o sistema de arquivos seja ajustado primeiro. Antes de reduzir o LV, o volume deve ser desmontado e um backup é recomendável, especialmente em sistemas de arquivos **XFS**:

    ```bash
    sudo umount /mnt/mirror
    sudo e2fsck -f /dev/myvol/mirror
    sudo resize2fs -p /dev/myvol/mirror 12M
    sudo lvreduce -L -12M /dev/myvol/mirror
    ```

**Atenção**: Ao reduzir um LV, sempre faça uma verificação de integridade com `e2fsck` e redimensione o sistema de arquivos com `resize2fs` para evitar corrupção de dados.

#### Snapshots

Os snapshots no LVM são cópias pontuais que capturam o estado atual de um LV. São extremamente úteis para **backups**, **testes de scripts** ou **implementação de software** sem risco de modificar o volume original.

1. **Criando um Snapshot**: O exemplo abaixo cria um snapshot denominado `snap` do volume `volsnapshot`, com 5 MB de espaço alocado:

    ```bash
    sudo lvcreate -s -n snap -L 5M /dev/myvol/volsnapshot
    ```

2. **Monitorando o Snapshot**: Para acompanhar o uso de espaço no snapshot, utilize o comando `lvs`:

    ```bash
    sudo lvs
    ```

    À medida que alterações são feitas no volume original, o snapshot armazena o estado anterior dos dados, preenchendo seu espaço conforme necessário. Caso o espaço do snapshot seja completamente ocupado, o Linux o marcará como inválido, interrompendo o monitoramento das alterações.

3. **Configuração para Expansão Automática do Snapshot**: É possível configurar o LVM para que o snapshot seja automaticamente expandido ao atingir um limite de uso. Isso é feito editando o arquivo `/etc/lvm/lvm.conf`:

    ```cfg
    # snapshot_autoextend_threshold = 100
    snapshot_autoextend_threshold = 50
    # snapshot_autoextend_percent = 20
    snapshot_autoextend_percent = 50
    ```

    Com essa configuração, o snapshot será estendido automaticamente em 50% de seu tamanho ao atingir o limite de 50%.

---

### Mesclagem de Snapshots com o Volume Original

Para reverter um volume lógico ao estado de um snapshot, o comando `lvconvert` pode ser usado para mesclar o snapshot com o volume original, restaurando o conteúdo:

```bash
sudo lvconvert --merge /dev/myvol/snap
```

Esse comando reverte o LV ao estado no momento do snapshot, removendo o snapshot automaticamente após o processo.

---

### Provisionamento Dinâmico (Thin Provisioning)

O **Thin Provisioning** permite provisionar mais espaço lógico do que o espaço físico disponível, alocando capacidade sob demanda. Essa técnica é útil quando o espaço de armazenamento total necessário não será imediatamente consumido.

1. **Criação do Thin Pool**: Primeiramente, crie um Thin Pool, que define a quantidade de espaço para volumes com provisionamento dinâmico:

    ```bash
    sudo lvcreate -L 100M --thinpool thinpool myvol
    ```

2. **Criação de Thin Volumes**: Com o Thin Pool configurado, é possível criar um Thin Volume. O exemplo abaixo cria um volume de 1 GB, embora o Thin Pool tenha apenas 100 MB:

    ```bash
    sudo lvcreate -V 1G --thin -n thin_volume myvol/thinpool
    ```

3. **Montando o Thin Volume**: Após criar o volume, formate-o e monte-o no sistema de arquivos:

    ```bash
    sudo mkfs.ext4 /dev/myvol/thin_volume
    sudo mkdir /mnt/lvmthin
    sudo mount /dev/myvol/thin_volume /mnt/lvmthin
    ```

---

### Considerações Finais e Práticas Recomendadas

O **Logical Volume Manager (LVM)** é uma ferramenta essencial para ambientes de TI que exigem flexibilidade e eficiência no uso de recursos de armazenamento. A capacidade de gerenciar volumes dinamicamente permite aos administradores otimizar a alocação de espaço e ajustar a infraestrutura de armazenamento de acordo com as necessidades da organização.

#### Práticas Recomendadas para o Uso do LVM

1. **Planejamento de Alocação de Recursos**:
   - Antes de configurar o LVM, analise as necessidades de armazenamento, considerando o crescimento de dados e a utilização de cada aplicação.
   - Use o **Thin Provisioning** com cautela e monitoramento ativo para evitar falta de espaço físico, especialmente em ambientes críticos.

2. **Monitoramento de Utilização**:
   - Configure monitoramento contínuo para verificar a utilização dos **Volume Groups** e **Thin Pools**. Ferramentas como **Nagios**, **Zabbix** ou scripts customizados podem enviar alertas quando a utilização de espaço atingir limites críticos.
   - Para volumes lógicos com snapshots, ajuste as configurações de expansão automática no arquivo `/etc/lvm/lvm.conf`, assegurando que o espaço do snapshot seja suficiente para captar alterações no volume original sem comprometer a integridade dos dados.

3. **Snapshots para Backups e Testes**:
   - Use snapshots para realizar backups pontuais, garantindo a integridade dos dados em momentos críticos, como antes de uma atualização de sistema.
   - Para ambientes de desenvolvimento, snapshots permitem a experimentação sem afetar os dados originais. Após concluir os testes, restaure o volume original a partir do snapshot ou descarte o snapshot sem comprometer o sistema.

4. **Espelhamento para Redundância de Dados**:
   - Em ambientes onde a alta disponibilidade é essencial, implemente volumes espelhados para garantir que os dados possam ser recuperados em caso de falha de um PV.
   - Certifique-se de que os PVs que participam do espelhamento estejam em dispositivos físicos diferentes para evitar pontos únicos de falha.

5. **Procedimentos de Redimensionamento de Volumes**:
   - Expanda volumes lógicos de forma proativa quando houver previsão de crescimento na utilização do armazenamento.
   - Reduzir um volume lógico deve ser feito com extremo cuidado, especialmente em sistemas de arquivos como **XFS** que não suportam redução nativa. Em tais casos, crie um backup completo, recrie o volume com o tamanho desejado e restaure os dados.

6. **Backup e Recuperação**:
   - Mesmo com as funcionalidades avançadas do LVM, mantenha backups regulares para garantir a recuperação de dados em caso de falhas.
   - Para volumes críticos, configure o LVM com suporte a **espelhamento** e **snapshots**. Utilize também uma política de backup fora do site para cópias de segurança em caso de desastres.

#### Segurança e Gerenciamento de Acessos

A configuração do LVM deve seguir boas práticas de segurança:
- Limite o acesso aos comandos LVM somente a administradores de sistema autorizados.
- Implemente auditoria para rastrear alterações nas configurações do LVM e ações executadas em volumes lógicos e físicos, principalmente em sistemas críticos.

---

### Conclusão

O uso do **Logical Volume Manager (LVM)** em ambientes KVM traz uma série de benefícios em termos de flexibilidade e eficiência no gerenciamento de armazenamento. Neste artigo, abordamos desde a criação e configuração básica de volumes lógicos até operações avançadas, como **snapshots**, **espelhamento** e **provisionamento dinâmico**, oferecendo uma visão prática e aplicada para administradores de sistemas e engenheiros de infraestrutura.

Ao seguir as práticas recomendadas aqui detalhadas, administradores podem não só melhorar o desempenho e disponibilidade dos recursos de armazenamento, mas também estabelecer uma política de gerenciamento eficiente e segura. O LVM, aliado ao poder de virtualização do KVM e à automação do Terraform, possibilita uma infraestrutura ágil e responsiva, adaptando-se rapidamente às demandas do ambiente corporativo.
