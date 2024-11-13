---
layout: post
title: "Encaminhamento de Porta para Máquinas Virtuais KVM com iptables, Firewalld e UFW"
date: 2024-10-14 10:00:00 -0300
categories: [linux, kvm, networking, iptables, firewald, ufw]
---

## **Sumário**

1. [Encaminhamento de Porta com iptables](#encaminhamento-de-porta-com-iptables)
2. [Encaminhamento de Porta com Firewalld](#encaminhamento-de-porta-com-firewalld)
3. [Encaminhamento de Porta com UFW](#encaminhamento-de-porta-com-ufw)


## **Introdução**

O **encaminhamento de portas** (port forwarding) é uma técnica essencial para permitir que o tráfego de redes externas ou do host KVM seja redirecionado para serviços específicos que estão rodando em máquinas virtuais. No caso do KVM (Kernel-based Virtual Machine), esse recurso é particularmente útil para acessar VMs de fora do ambiente de rede local, sem precisar configurar IPs públicos ou abrir múltiplas portas diretamente no firewall do host.

Neste tutorial, veremos como configurar o redirecionamento de portas usando três ferramentas populares: **iptables**, **Firewalld**, e **UFW**. Além disso, será abordada a necessidade de habilitar o **IP forwarding**, crucial para o tráfego fluir corretamente entre o host e as VMs. Cada método será apresentado de forma detalhada, com exemplos práticos, garantindo que você possa configurar o redirecionamento de portas de maneira eficaz em seu ambiente KVM.


## Encaminhamento de Porta com iptables

### **Cenário**
Neste cenário, o objetivo é redirecionar o tráfego da porta **2222** no host para a porta **22** (SSH) da máquina virtual KVM, cujo IP é **192.168.122.76**.

### **Parte 1: Pré-requisitos**

#### 1. Verificar a rede da VM
- Certifique-se de que a VM está conectada à **rede NAT** padrão criada pelo KVM (interface **virbr0**), que usa o intervalo de IPs `192.168.122.0/24`.
- Verifique o IP da VM usando:
  ```bash
  sudo virsh domifaddr nome-da-vm
  ```
  Vamos usar **192.168.122.76** como exemplo de IP da VM.

#### 2. Instalar `iptables` (se necessário)
Certifique-se de que o **iptables** esteja instalado no host KVM.

- No **Debian/Ubuntu**:
  ```bash
  sudo apt install iptables
  ```

- No **CentOS/RHEL/Fedora**:
  ```bash
  sudo yum install iptables
  ```

---

### **Parte 2: Habilitar o IP Forwarding (Necessário para o redirecionamento)**

Para garantir que o tráfego redirecionado chegue corretamente à máquina virtual, você deve habilitar o **IP forwarding** no host.

#### Passo 1: Verificar o status do IP forwarding

Verifique se o IP forwarding está ativado:

```bash
cat /proc/sys/net/ipv4/ip_forward
```

Se o valor retornado for **0**, o IP forwarding está desativado e precisa ser ativado.

#### Passo 2: Ativar o IP forwarding temporariamente

Para ativar o IP forwarding temporariamente (até o próximo reboot), use o seguinte comando:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

#### Passo 3: Ativar o IP forwarding permanentemente

Para garantir que o IP forwarding seja mantido após uma reinicialização, edite o arquivo **`/etc/sysctl.conf`** e adicione a seguinte linha:

```bash
net.ipv4.ip_forward = 1
```

Depois, aplique as alterações:

```bash
sudo sysctl -p
```

### **Parte 3: Configuração do Encaminhamento de Porta (Port Forwarding)**

Aqui, usaremos o **iptables** para redirecionar o tráfego que chega à porta **2222** no host para a porta **22** da VM.

#### Passo 1: Permitir tráfego de encaminhamento na interface da VM (virbr0)

Essa regra garante que o tráfego TCP que chega à interface **virbr0** (rede da VM) seja aceito e encaminhado corretamente.

```bash
sudo iptables -I FORWARD -o virbr0 -p tcp -d 192.168.122.76 --dport 22 -j ACCEPT
```

- **-I FORWARD**: Insere a regra na cadeia `FORWARD`.
- **-o virbr0**: Aplica a regra à interface `virbr0`.
- **-p tcp**: Especifica o protocolo TCP.
- **-d 192.168.122.76**: Define o destino como o IP da VM.
- **--dport 22**: Especifica que o destino é a porta **22** (SSH).
- **-j ACCEPT**: Permite (aceita) o tráfego.

#### Passo 2: Configurar o NAT para redirecionar a porta externa

Agora, configuramos uma regra na tabela **NAT** para redirecionar o tráfego que chega à porta **2222** do host para a porta **22** da VM.

```bash
sudo iptables -t nat -I PREROUTING -p tcp --dport 2222 -j DNAT --to 192.168.122.76:22
```

- **-t nat**: Aplica a regra à tabela **NAT** (Network Address Translation).
- **-I PREROUTING**: Insere a regra na cadeia **PREROUTING**.
- **-p tcp**: Define o protocolo TCP.
- **--dport 2222**: Porta de entrada no host.
- **DNAT --to 192.168.122.76:22**: Redireciona o tráfego para o IP da VM (porta 22).

---

### **Parte 4: Verificar e Gerenciar as Regras do `iptables`**

Após adicionar as regras, você pode verificar se elas foram aplicadas corretamente.

#### Passo 3: Verificar as regras do `iptables`

1. **Verificar as regras da cadeia `FORWARD`**:
   ```bash
   sudo iptables -L FORWARD -nv --line-number
   ```

2. **Verificar as regras da cadeia `PREROUTING` na tabela NAT**:
   ```bash
   sudo iptables -t nat -L PREROUTING -nv --line-number
   ```

Esses comandos listam todas as regras e mostram o número da linha, pacotes e bytes processados.

---

### **Parte 5: Testar o Encaminhamento de Porta**

Agora que as regras do `iptables` foram configuradas, é hora de testar o redirecionamento de porta para garantir que tudo esteja funcionando corretamente.

#### Passo 4: Testar o acesso SSH pela porta redirecionada

Você pode testar o acesso à máquina virtual (VM) utilizando o SSH na porta **2222** do host. Isso verifica se o redirecionamento de porta do host para a VM está funcionando como esperado.

1. **Usar o SSH para acessar a VM**:
   ```bash
   ssh -p 2222 usuario@ip-do-host
   ```

   - **-p 2222**: Define que a conexão será feita na porta **2222** do host.
   - **usuario**: Nome do usuário na VM.
   - **ip-do-host**: O endereço IP ou nome de domínio do host KVM.

Se o SSH conectar corretamente, significa que o redirecionamento de porta foi configurado com sucesso.

---

### **Parte 6: Gerenciar e Excluir Regras do `iptables`**

Caso você precise remover ou ajustar as regras do `iptables`, aqui estão os passos necessários.

#### Passo 5: Remover uma regra específica

Para remover uma regra, você precisa identificar o **número da linha** da regra que deseja remover. Use os comandos abaixo para listar as regras e encontrar o número correto.

1. **Listar as regras da cadeia `PREROUTING` na tabela NAT**:
   ```bash
   sudo iptables -t nat -L PREROUTING -nv --line-number
   ```

2. **Remover uma regra específica da tabela NAT**:

   Suponha que a regra esteja na **linha 1**. Para removê-la, execute:
   ```bash
   sudo iptables -t nat -D PREROUTING 1
   ```

3. **Listar as regras da cadeia `FORWARD`**:
   ```bash
   sudo iptables -L FORWARD -nv --line-number
   ```

4. **Remover uma regra da cadeia `FORWARD`**:

   Para remover uma regra específica na linha **1**, por exemplo:
   ```bash
   sudo iptables -D FORWARD 1
   ```

#### Passo 6: Salvar as regras do `iptables`

As regras do `iptables` não são persistentes, ou seja, elas são perdidas após reiniciar o sistema. Para garantir que as regras sejam preservadas após uma reinicialização, você precisa salvá-las.

- No **Debian/Ubuntu**:
  ```bash
  sudo iptables-save > /etc/iptables/rules.v4
  ```

- No **CentOS/RHEL**:
  ```bash
  sudo service iptables save
  ```

Esses comandos garantem que as regras sejam aplicadas automaticamente na próxima inicialização do sistema.

---

### **Conclusão**

Com essas etapas, você configurou o **encaminhamento de portas (port forwarding)** no **iptables** para redirecionar o tráfego da porta **2222** no host para a porta **22** de uma máquina virtual no KVM. O procedimento também inclui como gerenciar, ajustar ou remover essas regras, bem como garantir que elas sejam persistentes após reinicializações.

Essa configuração permite um controle flexível sobre o tráfego de rede, facilitando o acesso remoto a serviços rodando em máquinas virtuais por meio do host KVM.

---
## Encaminhamento de Porta com Firewalld

## Configurando Encaminhamento de Porta (Port Forwarding) com Firewalld

O **Firewalld** oferece uma maneira mais simples e gerenciável de configurar o redirecionamento de portas. Neste exemplo, vamos redirecionar o tráfego que chega à porta **2222** no host para a porta **22** da máquina virtual KVM com o IP **192.168.122.76**.

### **Parte 1: Verificar e Instalar o Firewalld**

Antes de configurar o Firewalld, é necessário garantir que ele esteja instalado e em execução no sistema.

#### Passo 1: Verificar o status do Firewalld

Verifique se o Firewalld está instalado e rodando:

```bash
sudo systemctl status firewalld
```

#### Passo 2: Instalar o Firewalld (se necessário)

Se o Firewalld não estiver instalado, você pode instalá-lo com os seguintes comandos:

- No **Debian/Ubuntu**:
  ```bash
  sudo apt install firewalld
  ```

- No **CentOS/RHEL/Fedora**:
  ```bash
  sudo yum install firewalld
  ```

#### Passo 3: Iniciar o Firewalld

Inicie o Firewalld (caso ele não esteja em execução) e habilite-o para iniciar automaticamente na inicialização do sistema:

```bash
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

---

### **Parte 2: Habilitar o IP Forwarding**

Antes de configurar o redirecionamento de portas no Firewalld, é importante garantir que o **IP forwarding** esteja habilitado no host para que o tráfego seja roteado corretamente.

#### Passo 4: Verificar se o IP forwarding está ativado

Para verificar se o IP forwarding está ativado, execute o comando:

```bash
cat /proc/sys/net/ipv4/ip_forward
```

Se o valor retornado for **0**, o IP forwarding está desativado.

#### Passo 5: Ativar o IP forwarding temporariamente

Ative o IP forwarding temporariamente:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

#### Passo 6: Ativar o IP forwarding permanentemente

Para garantir que o IP forwarding seja ativado em cada inicialização, edite o arquivo `/etc/sysctl.conf` e adicione a linha:

```bash
net.ipv4.ip_forward = 1
```

Em seguida, aplique as alterações:

```bash
sudo sysctl -p
```

---

### **Parte 3: Configurar a Zona do Firewalld**

No Firewalld, as regras de firewall são aplicadas a **zonas** de rede. Vamos verificar em qual zona a interface de rede da VM (geralmente **virbr0**) está associada.

#### Passo 7: Verificar as interfaces e suas zonas

Use o seguinte comando para listar as interfaces de rede ativas e suas zonas:

```bash
sudo firewall-cmd --get-active-zones
```

Aqui, a interface **virbr0** (rede NAT do KVM) deve estar associada a uma zona, como **public** ou **trusted**. Se a interface não estiver associada a uma zona apropriada, você pode movê-la.

#### Passo 8: Associar a interface a uma zona (opcional)

Caso a interface **virbr0** precise ser movida para uma zona, você pode usar o seguinte comando para movê-la para a zona **trusted**:

```bash
sudo firewall-cmd --zone=trusted --add-interface=virbr0 --permanent
```

Depois, recarregue o Firewalld para aplicar a mudança:

```bash
sudo firewall-cmd --reload
```

---

### **Parte 4: Adicionar o Redirecionamento de Porta (Port Forwarding)**

Agora vamos configurar o redirecionamento de porta no Firewalld para permitir que o tráfego da porta **2222** do host seja encaminhado para a porta **22** da máquina virtual com o IP **192.168.122.76**.

#### Passo 9: Adicionar a regra de redirecionamento

Execute o comando abaixo para adicionar a regra de redirecionamento de porta:

```bash
sudo firewall-cmd --zone=public --add-forward-port=port=2222:proto=tcp:toaddr=192.168.122.76:toport=22 --permanent
```

- **--zone=public**: Define a zona onde a regra será aplicada.
- **--add-forward-port=port=2222**: Especifica a porta do host (2222) a ser redirecionada.
- **proto=tcp**: Define o protocolo TCP.
- **toaddr=192.168.122.76**: Define o endereço IP de destino (a VM).
- **toport=22**: Porta de destino na VM (SSH).

#### Passo 10: Recarregar o Firewalld

Recarregue o Firewalld para aplicar as novas regras:

```bash
sudo firewall-cmd --reload
```

#### Passo 11: Verificar as regras de redirecionamento

Você pode verificar se a regra foi adicionada corretamente com o seguinte comando:

```bash
sudo firewall-cmd --zone=public --list-forward-ports
```

---

### **Parte 5: Testar o Encaminhamento de Porta**

Agora que o redirecionamento foi configurado, vamos testar o acesso à VM pela nova porta no host.

#### Passo 12: Testar o acesso SSH via porta redirecionada

Tente acessar a máquina virtual utilizando o SSH pela porta **2222** do host:

```bash
ssh -p 2222 usuario@ip-do-host
```

- **-p 2222**: Especifica a porta do host a ser usada para a conexão.
- **usuario**: Nome do usuário da VM.
- **ip-do-host**: IP ou hostname do host KVM.

Se a conexão for estabelecida, o redirecionamento de porta está funcionando corretamente.

---

### **Parte 6: Remover ou Modificar Regras no Firewalld**

Você pode querer remover ou ajustar as regras de redirecionamento de porta no Firewalld no futuro.

#### Passo 13: Listar as regras de redirecionamento de porta

Para listar todas as regras de redirecionamento de porta em uma zona:

```bash
sudo firewall-cmd --zone=public --list-forward-ports
```

#### Passo 14: Remover uma regra de redirecionamento

Se você quiser remover uma regra de redirecionamento de porta, use o comando:

```bash
sudo firewall-cmd --zone=public --remove-forward-port=port=2222:proto=tcp:toaddr=192.168.122.76:toport=22 --permanent
```

Após remover a regra, recarregue o Firewalld:

```bash
sudo firewall-cmd --reload
```

### **Conclusão**

O Firewalld oferece uma maneira flexível e simples de configurar o **encaminhamento de porta** em um host KVM. Com o Firewalld, você pode facilmente adicionar, modificar e remover regras de redirecionamento de porta, além de garantir que elas sejam persistentes após reinicializações.

---

## Encaminhamento de Porta com UFW

## Configurando Encaminhamento de Porta (Port Forwarding) com UFW

O **UFW (Uncomplicated Firewall)** facilita a configuração de regras de firewall no Linux, sendo bastante utilizado em distribuições como **Ubuntu**. Com ele, também é possível configurar o redirecionamento de portas (port forwarding) para máquinas virtuais KVM.

### **Parte 1: Verificar e Instalar o UFW**

Antes de configurar o redirecionamento de portas, certifique-se de que o UFW esteja instalado e habilitado.

#### Passo 1: Verificar o status do UFW

Verifique se o UFW está ativo e rodando no sistema:

```bash
sudo ufw status
```

- Se o UFW estiver **inativo**, você verá a mensagem `Status: inactive`.

#### Passo 2: Instalar e habilitar o UFW (se necessário)

Se o UFW não estiver instalado, você pode instalá-lo e ativá-lo com os seguintes comandos:

- No **Debian/Ubuntu**:
  ```bash
  sudo apt update
  sudo apt install ufw
  sudo ufw enable
  ```

#### Passo 3: Permitir o tráfego na porta do redirecionamento

Antes de configurar o redirecionamento, você precisa garantir que a porta que será utilizada no host (no caso, **2222**) esteja permitida no firewall.

```bash
sudo ufw allow 2222/tcp
```

Isso permite o tráfego na porta **2222** usando o protocolo TCP.

---

### **Parte 2: Habilitar o IP Forwarding**

Para que o redirecionamento de portas funcione corretamente, o **IP forwarding** deve estar habilitado. Vamos verificar e ativá-lo, se necessário.

#### Passo 4: Verificar o status do IP forwarding

Verifique se o IP forwarding está habilitado com o seguinte comando:

```bash
cat /proc/sys/net/ipv4/ip_forward
```

Se o retorno for **0**, isso significa que o IP forwarding está desativado.

#### Passo 5: Ativar o IP forwarding temporariamente

Para habilitar o IP forwarding temporariamente (até a próxima reinicialização do sistema), execute:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

#### Passo 6: Ativar o IP forwarding permanentemente

Para garantir que o IP forwarding permaneça ativado após reinicializações, edite o arquivo `/etc/sysctl.conf` e adicione a seguinte linha:

```bash
net.ipv4.ip_forward = 1
```

Em seguida, aplique as alterações com o comando:

```bash
sudo sysctl -p
```

---

### **Parte 3: Configurando o Redirecionamento de Porta no UFW**

O UFW utiliza o **iptables** por trás das cenas para aplicar as regras de firewall, mas simplifica o processo de configuração. Para configurar o redirecionamento de portas, você precisa editar manualmente o arquivo de regras do UFW.

#### Passo 7: Editar o arquivo de regras do UFW

1. Abra o arquivo de configuração do UFW que contém as regras **antes** que o tráfego passe pelas cadeias do firewall:

   ```bash
   sudo nano /etc/ufw/before.rules
   ```

2. No topo do arquivo, logo após a linha que diz `# rules.before`, adicione o seguinte bloco de configuração para redirecionar o tráfego:

   ```bash
   # NAT table rules for port forwarding
   *nat
   :PREROUTING ACCEPT [0:0]
   :POSTROUTING ACCEPT [0:0]

   # Redirecionar tráfego da porta 2222 no host para a porta 22 da VM (192.168.122.76)
   -A PREROUTING -p tcp --dport 2222 -j DNAT --to-destination 192.168.122.76:22

   # Garantir que o tráfego de saída use o IP correto do host
   -A POSTROUTING -s 192.168.122.0/24 -o ens3 -j MASQUERADE

   COMMIT
   ```

   - **PREROUTING**: Redireciona o tráfego que chega à porta **2222** no host para a porta **22** da VM.
   - **POSTROUTING**: Usa o **MASQUERADE** para garantir que o tráfego de saída da VM passe corretamente pelo host. Substitua **ens3** pela sua interface de rede do host.

3. Salve o arquivo e saia do editor (`CTRL + O` para salvar e `CTRL + X` para sair no **nano**).

#### Passo 8: Editar o arquivo de configuração do UFW

Para garantir que o redirecionamento funcione corretamente, edite o arquivo de configuração padrão do UFW.

1. Abra o arquivo `/etc/default/ufw`:

   ```bash
   sudo nano /etc/default/ufw
   ```

2. Altere a linha `DEFAULT_FORWARD_POLICY` de **DROP** para **ACCEPT**:

   ```bash
   DEFAULT_FORWARD_POLICY="ACCEPT"
   ```

3. Salve o arquivo e saia.

---

### **Parte 4: Aplicar e Recarregar o UFW**

Depois de configurar o redirecionamento de porta e o IP forwarding, você precisa recarregar o UFW para que as mudanças tenham efeito.

#### Passo 9: Recarregar o UFW

Recarregue o UFW com o seguinte comando:

```bash
sudo ufw reload
```

---

### **Parte 5: Testar o Encaminhamento de Porta**

Com o redirecionamento de porta configurado e o UFW recarregado, você pode testar o acesso à máquina virtual através da porta **2222** no host.

#### Passo 10: Testar o acesso SSH via porta redirecionada

Tente se conectar à máquina virtual utilizando o SSH pela porta **2222** no host:

```bash
ssh -p 2222 usuario@ip-do-host
```

- **-p 2222**: Especifica a porta **2222** no host.
- **usuario**: Nome do usuário na VM.
- **ip-do-host**: IP ou nome do host KVM.

Se a conexão for estabelecida corretamente, o redirecionamento de porta foi configurado com sucesso.

---

### **Parte 6: Gerenciar e Remover Regras no UFW**

Caso você precise ajustar ou remover o redirecionamento de porta, siga os passos abaixo.

#### Passo 11: Editar ou remover as regras de redirecionamento

1. Para remover ou ajustar as regras de redirecionamento de porta, edite novamente o arquivo `/etc/ufw/before.rules` e faça as alterações necessárias.

2. Após as alterações, recarregue o UFW:

   ```bash
   sudo ufw reload
   ```

3. Para remover a regra que permite o tráfego na porta **2222**:

   ```bash
   sudo ufw delete allow 2222/tcp
   ```

---

### **Conclusão**

O UFW simplifica bastante a configuração de **encaminhamento de portas (port forwarding)** no Linux, permitindo redirecionar tráfego do host para máquinas virtuais KVM. Através do UFW, é possível garantir que as regras sejam aplicadas de forma persistente, além de permitir fácil gerenciamento e ajustes.

---
