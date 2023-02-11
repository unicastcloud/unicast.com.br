---
layout: post
title: "[Azure-To] #10 Configurar DNS privado com registro automático [Portal]"
author: asilva
date: 2023-01-06 09:00:00 -0300
categories: [Azure, Azure-To]
tags: [azure, microsoft, infraestrutura, dns]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

### **Sobre o Azure DNS**

O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. 

### **Objetivo**

Configurar resolução de nomes DNS para o domínio Unicast Lab. Você criará uma zona DNS privada chamada unicastlab.com.be, e vinculará as VNETSs para registro e resolução, em seguida, criará duas máquinas virtuais para testar a configuração.

### **1.1 Criar zona de DNS privada**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo** , na caixa de Pesquisa, digite **Private DNS zones** e clique em **Create** 

![](/assets/img/47/dns01.png){: "width=60%" }

**Especifique as configurações do recurso:**

Siga as etapas necessárias para configurar o DNS.

* Subscription: o nome da assinatura que você está usando neste * laboratório
* Resource group: selecione seu grupo de recursos ou crie um novo.
* Name: **unicastlab.com.br**
* Region: **Brazil South** (ou outra região de sua preferência)

![](/assets/img/47/dns02.png){: "width=60%" }

![](/assets/img/47/dns03.png){: "width=60%" }

![](/assets/img/47/dns04.png){: "width=60%" }

### **2.1 Criar Virtual Network**

Vamos criar um VNET para habilitar o registro automático no Azure DNS.

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo** , na caixa de Pesquisa, digite **Virtual network** e clique em **Create** 

![](/assets/img/47/dns05.png){: "width=60%" }

**Especifique as configurações do recurso:**

Siga as etapas necessárias para configurar o VNET.

* Subscription: o nome da assinatura que você está usando neste * laboratório
* Resource group: selecione seu grupo de recursos ou crie um novo.
* Name: **unicastlab.com.br**
* Region: **Brazil South** (ou outra região de sua preferência)

![](/assets/img/47/dns06.png){: "width=60%" }

![](/assets/img/47/dns07.png){: "width=60%" }

![](/assets/img/47/dns08.png){: "width=60%" }

### **2.2 Habilitar o registro automático no Azure DNS**

Acesse a zona de DNS privada que criamos no início do artigo.

Selecione no menu a esquerda, **Virtual network links** e sem seguida, clique em **Add**.

![](/assets/img/47/dns09.png){: "width=60%" }

Configure as informações e selecione a VNET que criamos anteriormente.

![](/assets/img/47/dns10.png){: "width=60%" }

Pronto, agora já podemos testar nosso recurso de auto registro.

![](/assets/img/47/dns11.png){: "width=60%" }

### **3.1 Criar máquinas virtuais para validação e testes**

Agora, precisamos validar se nossa zona de DNS privada está funcionando corretamente.

**Precisamos verificar:**

- Se a resolução de nomes está funcional.
- Se o auto registro de máquinas está funcional.

Para isso, vamos criar duas máquinas virtuais.

Como é um processo simples e já documentado aqui no site, não vou me atentar em descrever todos os passos.

Você pode consultar os seguintes artigos:

- [Azure-To] #1 Deploy de Máquina Virtual [Portal](https://unicast.com.br/posts/azure-to-1-deploy-de-maquina-virtual-portal/)
- [Azure-To] #2 Deploy de Máquina Virtual [PowerShell](https://unicast.com.br/posts/azure-to-2-deploy-de-maquina-virtual-powershell/)
- [Azure-To] #3 Deploy de Máquina Virtual [Azure CLI](https://unicast.com.br/posts/azure-to-3-deploy-de-maquina-virtual-azure-cli/)

Basta escolher um método para o deploy e adicionar as duas VMS na rede virtual (VNET) que criamos.

No meu caso, estou utilizando 2 VMs com o sistema operacional **Ubuntu**.

![](/assets/img/47/dns12.png){: "width=60%" }

### **3.2 Verificar registros automático de DNS**

Após a criação de nossas VMs, vamos validar se o registro de nomes foi ativado de forma automática em nossa zona de DNS.

Na página inicial do portal do Azure, selecione **Private DNS zones**, selecione **unicast.com.br.**

![](/assets/img/47/dns13.png){: "width=60%" }

Verifique se os registros de host (A) estão listados para ambas as VMs, conforme abaixo:

![](/assets/img/47/dns14.png){: "width=60%" }

### **4.1 Validar resolução de nomes**

Para finalizar, vamos testar a resolução interna de nomes.

Acesse as duas VMs via **SSH** ou **RDP** caso tenha escolhido máquinas com sistema operacional Windows.

>Lembrando que não estamos em ambiente produtivo, e por facilidade as VMs estão com IPs públicos para facilitar o acesso remoto.
{: .prompt-warning }
Vamos verificar se temos a resolução interna de nomes para:

- vm-01.unicastlab.com.br (10.0.0.4)
- vm-02.unicastlab.com.br (10.0.0.5)

Na **VM-01**, utilize o comando:

```bash
ping vm-02.unicastlab.com.br -c 5
```

Na **VM-02**, utilize o comando:

```bash
ping vm-02.unicastlab.com.br -c 5
```

O resultado deve ser:

![](/assets/img/47/dns15.png){: "width=60%" }

Parabéns! Você criou uma zona DNS privada, adicionou uma resolução de nome e um link de registro automático e testou a resolução de nome em sua configuração.

É isso galera, espero que gostem!

Forte Abraço!