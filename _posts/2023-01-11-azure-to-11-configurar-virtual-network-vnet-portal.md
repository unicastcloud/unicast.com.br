---
layout: post
title: "[Azure-To] #11 Configurar Virtual Network (VNET) [Portal]"
author: asilva
date: 2023-01-11 09:00:00 -0300
categories: [Azure, Azure-To]
tags: [azure, microsoft, infraestrutura, vnet, virtualnetwork, networking]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

### **Sobre Virtual Network no Azure**

A Rede Virtual do Azure (VNet) é uma abstração para sua rede privada no Azure. A VNet permite que muitos tipos de recursos do Azure, como Máquinas Virtuais (VM) do Azure, se comuniquem com segurança uns com os outros, com a Internet e com redes locais. A VNet é semelhante a uma rede tradicional que você operaria em seu próprio data center, mas traz benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

### **Objetivo**

Neste artigo, criaremos uma rede virtual, implantaremos duas máquinas virtuais nessa rede virtual e as configuraremos para permitir que uma máquina virtual comunique com a outra dentro dessa rede virtual.

### **1.1 Criar virtual network (VNET)**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo**, na caixa de Pesquisa, digite **Virtual networks** e clique em **Create** 

![](/assets/img/50/vnet01.png){: .shadow style="max-width: 70%" }

**Especifique as configurações do recurso:**

Siga as etapas necessárias para configurar sua vnet.

* Subscription: o nome da assinatura que você está usando neste * laboratório
* Resource group: selecione seu grupo de recursos ou crie um novo.
* Name: **vnet-unicastlab-001**
* Region: **Brazil South** (ou outra região de sua preferência)

![](/assets/img/50/vnet02.png){: .shadow style="max-width: 70%" }

Clique em **Next: IP Address**, configure de acordo com suas necessidades, para nosso laboratório, seguiremos com o **default**.

![](/assets/img/50/vnet03.png){: .shadow style="max-width: 70%" }

clique em **Next: Security**, configure de acordo com suas necessidades, para nosso laboratório, seguiremos com os padrões desabilitados.

![](/assets/img/50/vnet04.png){: .shadow style="max-width: 70%" }

Clique em **Review + Create**.

![](/assets/img/50/vnet05.png){: .shadow style="max-width: 70%" }

### **2.1 Criar máquinas virtuais para validação e testes**

Agora, precisamos validar se nossa vnet está funcionando corretamente.

Para isso, vamos criar duas máquinas virtuais.

Como é um processo simples e já documentado aqui no site, não vou me atentar em descrever todos os passos.

Você pode consultar os seguintes artigos:

- [Azure-To] #1 Deploy de Máquina Virtual [Portal](https://unicast.com.br/posts/azure-to-1-deploy-de-maquina-virtual-portal/)
- [Azure-To] #2 Deploy de Máquina Virtual [PowerShell](https://unicast.com.br/posts/azure-to-2-deploy-de-maquina-virtual-powershell/)
- [Azure-To] #3 Deploy de Máquina Virtual [Azure CLI](https://unicast.com.br/posts/azure-to-3-deploy-de-maquina-virtual-azure-cli/)

Basta escolher um método para o deploy e adicionar as duas VMS na rede virtual (VNET) que criamos.

No meu caso, estou utilizando 2 VMs com o sistema operacional **Ubuntu**.

![](/assets/img/50/vnet06.png){: .shadow style="max-width: 70%" }

### **3.1 Validar comunicação**

Para finalizar, vamos testar a resolução interna de nomes.

Acesse as duas VMs via **SSH** ou **RDP** caso tenha escolhido máquinas com sistema operacional Windows.

>Caso esteja utilizando VMs Windows: Antes de iniciar o laboratório, desative o firewall público e privado em sua máquina virtual abrindo o menu Iniciar > Configurações > Rede e Internet > Localizar o Firewall do Windows
{: .prompt-warning }

Vamos verificar se temos a comunicação interna entre as duas VMs:

- vm-01 (10.1.0.4)
- vm-02 (10.1.0.5)

Na **VM-01**, utilize o comando:

```bash
ping 10.1.0.5 -c 5
```

Na **VM-02**, utilize o comando:

```bash
ping 10.1.0.4 -c 5
```

O resultado deve ser:

![](/assets/img/50/vnet07.png){: .shadow style="max-width: 70%" }

É isso galera, espero que gostem!

Forte Abraço!