---
title: "Otimização do Windows 10 em uma Máquina Virtual KVM"
date: 2024-09-29 11:33:00 -0300
categories: [virtualizacao, KVM, windows, otimização]
tags: [KVM, Windows 10, virtualização, desempenho, SPICE, VirtIO]
summary: "Aprenda a otimizar o desempenho do Windows 10 em uma máquina virtual utilizando o KVM, com configurações de CPU, memória, disco e ajustes no Windows."
---

## Sumário

1. [Verificar Suporte à Virtualização de Hardware](#passo-1-verificar-suporte-à-virtualização-de-hardware)
2. [Configurar a Máquina Virtual no KVM](#passo-2-configurar-a-máquina-virtual-no-kvm)
3. [Otimizações no Windows 10](#passo-3-ajustes-no-windows-10)
4. [Otimizações de Disco](#passo-4-otimizações-de-disco)
5. [Manutenção e Monitoramento](#passo-5-manutenção-e-monitoramento)
6. [Instalar e Configurar SPICE Guest Tools](#passo-6-instalar-o-spice-guest-tools)
7. [Testar e Verificar Funcionalidades do SPICE](#passo-7-configurar-spice-no-virt-manager)
8. [Testar e Verificar Funcionalidades do SPICE](#parte-8-testar-e-verificar-funcionalidades-do-spice)


## **Tutorial: Otimizando o Windows 10 em uma Máquina Virtual KVM**

Este tutorial ensinará como otimizar o desempenho do Windows 10 em uma máquina virtual utilizando o KVM (Kernel-based Virtual Machine). Seguindo esses passos, você poderá melhorar significativamente a velocidade e a eficiência da sua máquina virtual.

---

### **Passo 1: Verificar Suporte à Virtualização de Hardware**

Para garantir que sua máquina física suporte a virtualização, siga os passos abaixo.

#### **A) Verificar no Sistema Operacional do Host (Linux)**

1. Execute o seguinte comando no terminal para verificar se a virtualização de hardware (Intel VT-x ou AMD-V) está ativada:
   ```bash
   egrep -c '(vmx|svm)' /proc/cpuinfo
   ```
   - Se o retorno for **0**, a virtualização de hardware não está habilitada, e será necessário ativá-la no BIOS/UEFI.
   - Se o retorno for um valor maior que 0, a virtualização de hardware está habilitada e você pode prosseguir.

#### **B) Habilitar Virtualização no BIOS/UEFI**

Caso o suporte à virtualização de hardware esteja desabilitado, siga estas instruções para habilitá-lo:

1. **Reinicie sua máquina** e acesse o BIOS/UEFI. As teclas mais comuns para acessar o BIOS são **F2**, **F10**, **DEL**, ou **ESC**, dependendo da sua máquina.
   
2. No menu do BIOS/UEFI, procure uma opção relacionada à virtualização. Ela pode aparecer com nomes como:
   - **Intel Virtualization Technology** (Intel VT-x)
   - **AMD-V**
   - **SVM Mode** (para processadores AMD)

3. Habilite essa opção, salve as configurações e reinicie o sistema.

4. Após o reinício, execute novamente o comando `egrep -c '(vmx|svm)' /proc/cpuinfo` no terminal para confirmar que a virtualização de hardware foi ativada.

---

## **Parte 2: Configurar a Máquina Virtual no KVM**

Agora que a virtualização de hardware está habilitada, vamos configurar a máquina virtual no KVM.

### **Passo 2: Configurar a Máquina Virtual no KVM**

#### **A) Usar VirtIO Drivers**

Os **VirtIO drivers** são essenciais para melhorar o desempenho de dispositivos como disco e rede. Aqui está como instalá-los.

1. **Baixar a ISO dos VirtIO drivers**:
   - Acesse o site oficial do Fedora Project para baixar a ISO mais recente dos drivers:  
     [VirtIO ISO - Fedora Project](https://fedoraproject.org/wiki/Windows_Virtio_Drivers).

2. **Montar a ISO**:
   - No **virt-manager**, vá até as configurações da máquina virtual.
   - Adicione a ISO dos drivers VirtIO como uma segunda unidade de CD/DVD, montando-a como um dispositivo de CD.

3. **Instalar os Drivers**:
   - Durante a instalação do Windows, quando solicitado, instale os drivers de rede e disco diretamente da ISO do VirtIO.

4. **Explicação dos Benefícios**:
   - **VirtIO Network**: Aumenta significativamente a velocidade de transferência de dados e a eficiência da rede, pois usa uma interface de rede paravirtualizada.
   - **VirtIO Block (Disco)**: Melhora o desempenho de leitura/escrita em disco, permitindo acesso mais rápido ao armazenamento.

#### **B) Melhorar o Desempenho da CPU**

Utilizar o modo `host-passthrough` para a CPU permitirá que a VM Windows use as instruções completas da CPU física, o que resulta em melhor desempenho.

1. **Editar o XML da VM**:
   - Abra o arquivo de configuração XML da máquina virtual usando o seguinte comando:
     ```bash
     virsh edit <nome-da-vm>
     ```

2. **Alterar o modo da CPU**:
   - Encontre a seção `<cpu>` no XML e ajuste o parâmetro para `host-passthrough`:
     ```xml
     <cpu mode='host-passthrough'/>
     ```

3. **Por que usar `host-passthrough`?**
   - Isso permite que a VM tenha acesso direto às instruções da CPU real (como **SSE**, **AVX**, etc.), melhorando o desempenho, especialmente em tarefas de processamento intenso.

#### **C) Definir a Afinidade de CPU (CPU Pinning)**

**CPU Pinning** é o processo de associar vCPUs da VM a núcleos físicos específicos do host, o que reduz a troca de núcleos e melhora o desempenho.

1. **Determinar os Núcleos Físicos Disponíveis no Host**:
   - Utilize o comando `lscpu` para visualizar os núcleos disponíveis no host:
     ```bash
     lscpu
     ```

2. **Definir a Afinidade da vCPU**:
   - Associe as vCPUs da VM a núcleos físicos específicos utilizando o comando:
     ```bash
     virsh vcpupin <nome-da-vm> <vCPU> <número-do-núcleo-host>
     ```

3. **Exemplo**:
   - Se sua VM tem 2 vCPUs e seu host tem 4 núcleos, você pode vincular a vCPU 0 ao núcleo 2 e a vCPU 1 ao núcleo 3:
     ```bash
     virsh vcpupin <nome-da-vm> 0 2
     virsh vcpupin <nome-da-vm> 1 3
     ```

#### **D) Ajustar Memória RAM e Ballooning**

1. **Alocar Memória RAM**:
   - No **virt-manager**, configure pelo menos **4 GB de RAM** para a máquina virtual Windows 10. Se possível, utilize **8 GB** para um desempenho ainda mais fluido.

2. **Evitar o Uso de Memory Ballooning**:
   - O **Memory Ballooning** é uma técnica que permite ao sistema host ajustar dinamicamente a quantidade de RAM usada pela VM, dependendo da demanda. Embora útil em algumas situações, pode prejudicar o desempenho em VMs com uso intensivo de memória.
   - Certifique-se de que o Ballooning está desabilitado, especialmente se a VM estiver sob alta carga de trabalho.

#### **E) Habilitar Aceleração Gráfica (GPU e 3D)**

Se você planeja usar a máquina virtual para tarefas que exigem gráficos avançados, como software CAD ou edição de vídeo, configurar a aceleração 3D ou o **GPU passthrough** pode melhorar muito a performance.

1. **Ativar Aceleração 3D**:
   - No **virt-manager**, vá até as configurações da VM, em **Video**, e escolha o modelo **Virtio** com a opção de aceleração 3D habilitada.

2. **Passagem de GPU (GPU Passthrough)**:
   - Se você quiser que a VM Windows tenha acesso direto à GPU física do host (por exemplo, para jogos ou design gráfico), pode configurar o **GPU passthrough**. Isso requer hardware específico e um pouco mais de configuração, incluindo a instalação de drivers de GPU no Windows.
   - Consulte a documentação do KVM e da sua distribuição para detalhes sobre como habilitar **PCI passthrough**.

---

**Conclusão desta etapa:**

Agora que sua VM está configurada com o suporte adequado de hardware, drivers otimizados e recursos como CPU pinning e aceleração gráfica, ela estará pronta para rodar com muito mais eficiência. Na próxima parte, vamos revisar os ajustes no Windows 10 para otimizar ainda mais o desempenho interno da VM.

---

### **Passo 3: Ajustes no Windows 10**

#### **A) Desabilitar Efeitos Visuais**

Os efeitos visuais do Windows, como animações, sombras e transparências, consomem recursos da CPU e da memória. Desabilitar esses efeitos ajuda a liberar esses recursos para outras tarefas mais importantes.

1. **Acessar as Configurações de Desempenho**:
   - Pressione `Win + R`, digite `sysdm.cpl` e pressione **Enter**.
   - Na aba **Avançado**, clique em **Configurações** na seção **Desempenho**.

2. **Desativar os Efeitos Visuais**:
   - Na janela **Opções de Desempenho**, selecione **Ajustar para obter melhor desempenho**.
   - Isso desabilitará todos os efeitos visuais desnecessários. Se preferir, pode personalizar e ativar apenas os efeitos que desejar manter.

3. **Aplicar as Configurações**:
   - Clique em **Aplicar** e depois em **OK** para confirmar as alterações.

#### **B) Configurar o Plano de Energia**

O Windows 10, por padrão, usa um plano de energia equilibrado, que tenta otimizar o consumo de energia em detrimento do desempenho. Em uma VM, especialmente se não houver preocupações com economia de energia, mudar para o plano **Alto Desempenho** garantirá que o sistema use toda a potência disponível da CPU.

1. **Acessar as Opções de Energia**:
   - Pressione `Win + X` e selecione **Opções de Energia**.
   - Na nova janela, clique em **Configurações de energia adicionais** (no lado direito).

2. **Escolher o Plano de Energia**:
   - Selecione o plano **Alto Desempenho**. Esse plano evita que a CPU reduza sua velocidade ou entre em estados de economia de energia durante o uso.

3. **Configurações Avançadas de Energia** (Opcional):
   - Se quiser ajustar o plano com mais detalhes, clique em **Alterar configurações do plano** e depois em **Alterar configurações avançadas de energia**.

#### **C) Desativar Serviços Desnecessários**

O Windows 10 executa vários serviços em segundo plano que podem ser desnecessários em uma máquina virtual, especialmente se for usada apenas para tarefas específicas.

1. **Abrir o Gerenciador de Serviços**:
   - Pressione `Win + R`, digite `services.msc` e pressione **Enter**.

2. **Identificar e Desativar Serviços**:
   - Abaixo estão alguns serviços que você pode desativar para liberar recursos:
     - **Windows Search**: Se você não usar a pesquisa do Windows frequentemente, desativar esse serviço reduz o uso de CPU e disco.
     - **SysMain (Superfetch)**: Otimiza o desempenho em sistemas físicos, mas em uma VM pode consumir recursos desnecessariamente.
     - **Print Spooler**: Desative se você não utilizar impressoras na VM.
   
   Para desativar um serviço:
   - Clique com o botão direito no serviço desejado e selecione **Propriedades**.
   - No campo **Tipo de inicialização**, selecione **Desativado**.
   - Clique em **Parar** se o serviço já estiver em execução, e depois em **OK**.

#### **D) Desativar Programas de Inicialização**

Programas que iniciam automaticamente com o Windows podem prejudicar o desempenho, consumindo recursos desde o momento em que a VM é ligada.

1. **Abrir o Gerenciador de Tarefas**:
   - Pressione `Ctrl + Shift + Esc` para abrir o **Gerenciador de Tarefas**.

2. **Verificar Programas de Inicialização**:
   - Vá até a aba **Inicializar** para visualizar todos os programas que são carregados na inicialização do Windows.
   
3. **Desativar Programas Desnecessários**:
   - Clique com o botão direito sobre os programas que você não precisa que iniciem automaticamente e selecione **Desativar**.

4. **Programas Recomendados para Desativar**:
   - **OneDrive** (se não for usado para sincronização de arquivos).
   - **Atualizadores automáticos de software** que não são essenciais.

---

### **Conclusão desta Etapa**

Ao aplicar essas otimizações, o Windows 10 rodando em sua máquina virtual consumirá menos recursos, resultando em um desempenho mais fluido. Essas modificações garantem que o Windows não gaste memória, CPU e disco com tarefas que não são críticas para o seu uso específico.

---

### **Passo 4: Otimizações de Disco**

As otimizações de disco são importantes para melhorar o desempenho de leitura e gravação de dados, especialmente em uma máquina virtual onde o disco pode ser um gargalo de performance.

#### **A) Configurar o Controlador de Disco para VirtIO**

Usar o **VirtIO** como controlador de disco proporciona acesso mais rápido e eficiente ao armazenamento, pois o VirtIO é otimizado para operação em ambientes virtualizados.

1. **Configurar o VirtIO no Virt-Manager**:
   - No **virt-manager**, com a VM desligada, vá até as **Configurações da VM**.
   - Na seção **Disco** (ou **Armazenamento**), clique no disco principal e altere o controlador para **VirtIO**.
   
2. **Editar o XML (opcional)**:
   - Caso prefira fazer manualmente, abra o arquivo XML da VM com o comando:
     ```bash
     virsh edit <nome-da-vm>
     ```
   - Dentro da configuração do disco, defina o barramento do disco como **VirtIO**:
     ```xml
     <disk type='file' device='disk'>
       <driver name='qemu' type='qcow2' cache='none'/>
       <source file='/caminho/para/disco.qcow2'/>
       <target dev='vda' bus='virtio'/>
     </disk>
     ```

3. **Instalar os Drivers VirtIO no Windows (se ainda não estiverem instalados)**:
   - Lembre-se de que você precisa dos drivers VirtIO instalados dentro do Windows para que ele reconheça o controlador VirtIO corretamente. Utilize a **ISO do VirtIO** que montamos anteriormente no tutorial para instalar os drivers.

#### **B) Usar Cache "None" para Discos Virtuais**

Configurar o cache de disco corretamente evita duplicidade de cache entre o sistema host e a VM, o que pode causar sobrecarga no uso de memória e impactar o desempenho.

1. **O que é Cache "None"**:
   - Quando o cache de disco está ativado tanto no host quanto na VM, isso pode criar um efeito de "cache duplicado", que consome mais recursos sem um ganho significativo de desempenho.

2. **Configurar o Cache no Virt-Manager**:
   - No **virt-manager**, vá até as **Configurações do Disco** da VM.
   - Encontre a opção **Cache** e defina-a como **None** (Nenhum). Isso faz com que a VM acesse diretamente o armazenamento no host.

3. **Editar o XML (opcional)**:
   - Se preferir configurar manualmente, abra o XML da VM e defina a configuração de cache:
     ```xml
     <driver name='qemu' type='qcow2' cache='none'/>
     ```

#### **C) Utilizar Discos SSD**

Se possível, armazene suas VMs em discos SSD para obter uma melhoria significativa na performance de leitura e gravação. A utilização de SSDs reduz a latência de acesso a dados e melhora o desempenho geral.

1. **Mover a VM para SSD**:
   - Se sua VM ainda estiver em um disco rígido mecânico (HDD), considere migrá-la para um SSD, especialmente se for uma máquina virtual usada para tarefas que exigem alta performance de disco, como bancos de dados ou edição de vídeo.
   
2. **Converter a VM para um disco NVMe (opcional)**:
   - Para melhorar ainda mais o desempenho em hardware compatível, você pode configurar a VM para usar discos **NVMe**. O NVMe oferece desempenho superior ao SATA, sendo ideal para VMs que realizam operações intensivas de leitura e escrita.

---

### **Passo 5: Manutenção e Monitoramento**

Manter o sistema em boas condições e monitorar o desempenho da VM regularmente é essencial para garantir que os ajustes continuem funcionando de forma eficiente.

#### **A) Ajustar Pagefile no Windows**

O **arquivo de paginação** (pagefile) do Windows é utilizado como uma extensão da memória RAM. Configurar seu tamanho adequadamente evita que o sistema ajuste dinamicamente o tamanho, o que pode causar picos de consumo de disco.

1. **Acessar as Configurações do Pagefile**:
   - No Windows, vá até **Configurações Avançadas do Sistema** (pressione `Win + R`, digite `sysdm.cpl` e pressione **Enter**).
   - Na aba **Avançado**, clique em **Configurações** na seção **Desempenho** e, em seguida, vá até a aba **Avançado**.

2. **Configurar o Tamanho do Arquivo de Paginação**:
   - Na seção **Memória Virtual**, clique em **Alterar**.
   - Desmarque a opção **Gerenciar automaticamente o tamanho do arquivo de paginação**.
   - Selecione o drive principal (geralmente C:) e escolha a opção **Tamanho Personalizado**.
   - Defina o tamanho inicial e máximo. Como recomendação geral, o tamanho do pagefile deve ser 1,5 a 2 vezes o tamanho da memória RAM da VM.

3. **Exemplo de Configuração**:
   - Se a VM possui 8 GB de RAM, defina o tamanho inicial e máximo para 12 GB a 16 GB (12288 MB a 16384 MB).

#### **B) Monitorar a VM com `virt-top` e `iostat`**

Monitorar o desempenho da VM ajuda a identificar gargalos de CPU, disco ou memória e fazer ajustes conforme necessário.

1. **Usar o `virt-top` para Monitorar CPU e Memória**:
   - O `virt-top` é uma ferramenta útil para monitorar o uso de CPU e memória pelas VMs no host KVM. Instale-o com:
     ```bash
     sudo apt install virt-top
     ```
   - Execute o comando `virt-top` para visualizar o consumo de recursos de cada VM.

2. **Usar `iostat` para Monitorar Disco**:
   - O `iostat` é uma ferramenta que monitora o desempenho de I/O do disco. Instale com:
     ```bash
     sudo apt install sysstat
     ```
   - Execute `iostat -xm 1` para monitorar a taxa de leitura e gravação do disco e identificar possíveis gargalos.

3. **Analisar Resultados**:
   - Verifique se há picos elevados de uso de CPU, memória ou disco e ajuste os recursos da VM conforme necessário (alocação de vCPUs, RAM ou armazenamento).

---

### **Conclusão desta Etapa**

Com essas otimizações de disco e o monitoramento contínuo, sua VM estará bem ajustada para garantir um desempenho estável e eficiente, mesmo sob carga. Monitorar o sistema regularmente também permite identificar problemas antes que eles impactem seriamente a performance.

---

## **Parte 6: Instalar e Configurar o SPICE Guest Tools no Windows 10**

O **SPICE Guest Tools** é essencial para melhorar a experiência gráfica e a integração do Windows 10 em uma máquina virtual KVM. Ele fornece suporte para redimensionamento dinâmico de tela, copiar/colar entre host e VM, redirecionamento de dispositivos USB e áudio.

### **Passo 6: Instalar o SPICE Guest Tools**

#### **A) Baixar o SPICE Guest Tools**

1. **Acesse o link oficial** para baixar o **SPICE Guest Tools**:
   - [Download Spice Guest Tools](https://www.spice-space.org/download.html).
   
2. **Baixar o arquivo .exe**:
   - No site, baixe a versão mais recente do **SPICE Guest Tools** para Windows.

#### **B) Instalar o SPICE Guest Tools no Windows 10**

1. **Montar a imagem ISO do SPICE** (se estiver usando a ISO):
   - No **virt-manager**, vá até as configurações da VM e adicione a ISO do **SPICE Guest Tools** como uma unidade de CD/DVD (caso prefira usar o instalador da ISO).
   - Caso tenha baixado o arquivo `.exe`, simplesmente copie-o para dentro da VM.

2. **Instalar o pacote SPICE Guest Tools**:
   - Dentro da VM Windows, acesse a unidade de CD/DVD (se estiver usando a ISO) ou localize o arquivo `.exe` baixado.
   - Execute o instalador **spice-guest-tools.exe** e siga as instruções na tela.
   
3. **Reinicie a máquina virtual**:
   - Após a instalação, reinicie a VM para garantir que todas as mudanças entrem em vigor.

#### **C) O que o SPICE Guest Tools Instala**

O **SPICE Guest Tools** instala vários componentes importantes que otimizam o desempenho e a interação da VM com o host:

1. **Driver de vídeo QXL**:
   - Melhora o desempenho gráfico e permite o redimensionamento dinâmico da janela da VM.
   
2. **Drivers de entrada (teclado e mouse)**:
   - Facilita a captura de entrada entre o host e a VM sem a necessidade de capturar/descapturar manualmente o mouse.

3. **Redirecionamento de dispositivos USB**:
   - Permite redirecionar dispositivos USB conectados ao host diretamente para a VM.

4. **Suporte a clipboard**:
   - Habilita o uso de copiar/colar entre o host e a máquina virtual.

5. **Transferência de áudio**:
   - Facilita a reprodução de áudio da VM no host, ideal para máquinas que utilizam som.

---

## **Parte 7: Configurar SPICE no Virt-Manager**

Agora que o SPICE Guest Tools está instalado, precisamos configurar corretamente o **Virt-Manager** para tirar proveito dos recursos do SPICE.

### **Passo 7: Configurar SPICE no Virt-Manager**

#### **A) Configurar Vídeo para Usar QXL**

O **QXL** é um driver de vídeo otimizado para VMs que utilizam o protocolo SPICE. Ele oferece melhor desempenho gráfico e redimensionamento automático da janela.

1. **Abra as Configurações da VM**:
   - No **virt-manager**, com a VM desligada, vá até **Editar > Configurações de Hardware**.

2. **Configurar o Modelo de Vídeo como QXL**:
   - Na aba **Video**, selecione **QXL** como o modelo de vídeo.

3. **Aceleração 3D** (Opcional):
   - Se necessário, habilite a aceleração 3D para melhorar o desempenho gráfico da VM ao executar tarefas mais exigentes.

#### **B) Definir SPICE como o Servidor de Exibição**

O SPICE precisa estar configurado como o servidor de exibição da VM para permitir a integração completa com os recursos instalados.

1. **Configurar o Protocolo Gráfico**:
   - Ainda nas configurações de hardware da VM, clique em **Display** e selecione **SPICE** como o protocolo de exibição.

2. **Ativar Escala Automática**:
   - Habilite a opção de **Escala Automática** para que a resolução da VM ajuste dinamicamente conforme o redimensionamento da janela da máquina virtual.

3. **Verificar Outras Configurações**:
   - Certifique-se de que as portas de **redirecionamento USB** e **áudio** estão habilitadas para permitir a passagem desses dispositivos.

---

## **Parte 8: Testar e Verificar Funcionalidades do SPICE**

Com tudo configurado, é hora de testar os recursos e garantir que o SPICE esteja funcionando corretamente na sua VM.

### **Passo 8: Testar e Verificar Funcionalidades do SPICE**

#### **A) Redimensionamento Dinâmico da Tela**

1. **Teste o redimensionamento da janela**:
   - Abra a VM no **virt-manager** e redimensione a janela da máquina virtual.
   - Verifique se a resolução da tela do Windows 10 se ajusta automaticamente ao novo tamanho da janela.

#### **B) Testar Clipboard (Copiar/Colar)**

1. **Copie texto ou arquivos**:
   - Tente copiar e colar texto ou arquivos entre o host e a máquina virtual.
   - Verifique se o recurso funciona corretamente para ambos os lados (host para VM e VM para host).

#### **C) Testar Redirecionamento de Dispositivos USB**

1. **Conecte um dispositivo USB ao host**:
   - No **virt-manager**, clique em **Redirecionar Dispositivo USB** e selecione o dispositivo que deseja conectar à VM.

2. **Verifique o funcionamento dentro da VM**:
   - O dispositivo USB deve aparecer na VM como se estivesse diretamente conectado.

#### **D) Testar Transferência de Áudio**

1. **Execute um arquivo de áudio ou vídeo**:
   - Abra um arquivo de áudio ou vídeo dentro da VM e verifique se o som é transmitido corretamente para o host.

---

## **Conclusão desta Etapa**

A instalação do **SPICE Guest Tools** e a configuração adequada do **Virt-Manager** oferece uma série de melhorias na interação e usabilidade da máquina virtual. Com o SPICE, você pode desfrutar de gráficos otimizados, copiar/colar entre host e VM, além de redirecionamento de USB e áudio, tudo isso proporcionando uma experiência muito mais fluida ao usar o Windows 10 em uma máquina virtual KVM.

---

### **Conclusão Geral**

Seguindo todas as etapas deste tutorial, você terá uma máquina virtual Windows 10 rodando em um ambiente KVM otimizado para o melhor desempenho possível. Com a virtualização de hardware habilitada, drivers VirtIO instalados, ajustes de CPU e memória, otimizações de disco e o SPICE Guest Tools configurado, sua VM será mais rápida, eficiente e interativa, tanto em termos de performance quanto de usabilidade.

