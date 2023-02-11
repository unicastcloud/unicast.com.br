---
layout: post
title: "[Azure-To] #8 Configurar Azure Bastion [Portal]"
author: asilva
date: 2021-04-07 06:54:00 -0500
categories: [Azure, Azure-To]
tags: [bastion, azure, microsoft, infraestrutura, segurança, acessoremoto, paas]
---

Fala galera! Seis tão baum?

Faz tempo que não faço um poste mais técnico então vamos lá. Seguindo nossa série Azure-To hoje vou mostrar como configurar o Azure Bastion.

### **Sobre o Azure Bastion**

O serviço Azure Bastion é um serviço PaaS totalmente gerenciado por plataforma que você pode fornecer dentro de sua rede virtual. Ele fornece conectividade RDP/SSH segura e perfeita às suas máquinas virtuais diretamente no portal Azure sobre o Protocolo SSL.

Quando você se conecta via Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. O Bastion fornece conectividade RDP e SSH seguras a todas as VMs da rede virtual em que está provisionada.

Como ele funciona no protocolo SSL, não é necessário nenhum software adicional do que um navegador regular.

O Bastion é implantando na rede virtual. O usuário acessa o portal do Azure, seleciona a VM que deseja acessar via Bastion e em seguida é capaz de abrir uma sessão RDP/SSH no próprio navegador.

### **Objetivo**

Configurar Azure Bastion

>Durante o processo de implantação do Bastion, você precisará criar dois recursos: uma sub-rede dedicada em sua rede virtual com as seguintes características:
{: .prompt-info }

- [X] O nome da sub-rede dedicada deve ser AzureBastionSubnet.
- [X] Você deve usar uma sub-rede de pelo menos /27 ou sub-rede maior.

Um IP público que deve atender às seguintes características:

- [X] O endereço IP público deve estar na mesma região do recurso do Bastion.
- [X] O Azure Bastion suporta apenas o Standard Public IP SKU.

### **1.1 Configurando o Azure Bastion via Portal**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo** , na caixa de Pesquisa, digite **Bastion** e clique em **Enter** para obter os resultados da pesquisa.

![](/assets/img/04/bastion1.jpg){: "width=60%" }

**Especifique as configurações do recurso Bastion:**

Você pode criar uma rede virtual no portal durante esse processo ou usar uma rede virtual existente. Se você estiver usando uma rede virtual existente, verifique se a rede virtual existente têm espaços de endereço livres suficientes para acomodar os requisitos de sub-rede do Bastion.

>Neste exemplo eu já tenho uma vnet criada, então vou adicionar um novo espaço de rede e sub-rede para essa vnet.
{: .prompt-warning }

* A sub-rede precisa ser nomeada **AzureBastionSubnet** .
* A sub-rede precisa ter pelo menos /27 ou maior.

![](/assets/img/04/bastion2.png){: "width=60%" }

Clique em **Gerenciar configuração de sub-rede**.

Na aba **Configurações**, crie um novo **espaço de endereço.**

![](/assets/img/04/bastion3.jpg){: "width=60%" }

Agora crie uma nova **sub-rede.**

Volte a configuração do Bastion e complete as informações:

* Subscription: o nome da assinatura que você está usando neste * laboratório
* Resource group: selecione seu grupo de recursos ou crie um novo.
* Nome: **bastion-vms**
* Region: **Brazil South** (ou outra região de sua preferência)
* Tier: **Basic**
* Rede virtual: **RG-unicast-vnet**
* Sub-rede: **AzureBastionSubnet (10.0.1.0/27)**
* Endereço de IP público : **Criar um novo**
* Nome do endereço IP público: **RG-unicast-vnet-ip**
* SKU do endereço IP público: **Standard**

![](/assets/img/04/bastion4.jpg){: "width=60%" }

Agora basta clicar em **Revisar + criar.**

### **2.1 Conecte-se a uma máquina virtual**

No portal do Azure vá até a máquina virtual à qual você deseja se conectar e selecione **Conectar**. Selecione **Bastion** no menu suspenso.

![](/assets/img/04/bastion5.png){: "width=60%" }

Observe que esta VM não tem um endereço IP público, o que significa que eu não posso me conectar a ela a partir da internet.

![](/assets/img/04/bastion6.png){: "width=60%" }

Depois de selecionar o Bastion, clique em **Usar o Bastion.**

![](/assets/img/04/bastion7.jpg){: "width=60%" }

Na página **Conectar-se usando o Azure Bastion**, insira o nome de usuário e a senha para sua máquina virtual e selecione **Conectar**.

![](/assets/img/04/bastion8.jpg){: "width=60%" }

Agora uma nova aba será aberta e você estará logado em sua VM.

![](/assets/img/04/bastion10.jpg){: "width=60%" }

A conexão RDP com essa máquina virtual via Bastion será aberta diretamente no portal do Azure (via HTML5) usando a porta 443 e o serviço Bastion.

Se você quiser saber mais sobre o Azure Bastion, confira este link: <https://docs.microsoft.com/en-us/azure/bastion/bastion-overview>

É isso galera, espero que gostem!

Forte Abraço!