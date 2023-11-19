---
layout: post
title: "[Azure-To] #12 Configurar Network Security Group (NSG) [Portal]"
author: asilva
date: 2023-01-23 09:00:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, infraestrutura, vnet, virtualnetwork, networking, nsg]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

## **Sobre Network Security Group (NSG) no Azure**

Os Network Security Groups (NSGs) funcionam no Microsoft Azure como ferramentas de firewall de nível de rede que permitem controlar o tráfego de entrada e saída de uma rede virtual. Eles permitem criar regras de firewall para permitir ou negar tráfego baseado em critérios como endereço IP, porta e protocolo.

Ao criar um NSG, você pode especificar regras de firewall para permitir ou negar tráfego de entrada e saída para sua rede virtual. Cada regra consiste em critérios de correspondência, como endereço IP de origem, endereço IP de destino, protocolo e porta, e uma ação (permitir ou negar). As regras são avaliadas na ordem em que são criadas, e o primeiro conjunto de regras que corresponde ao tráfego é aplicado.

Os NSGs podem ser aplicados a nível de sub-rede ou nível de máquina virtual. Quando um NSG é aplicado a uma sub-rede, todas as máquinas virtuais na sub-rede são afetadas pela regra. Quando um NSG é aplicado a uma máquina virtual específica, apenas essa máquina virtual é afetada pela regra.

Além disso, os NSGs podem ser associados a várias máquinas virtuais, permitindo que você aplique várias regras de firewall para uma única máquina virtual. Isso permite uma maior flexibilidade na configuração de segurança de sua rede virtual.

Os NSGs também podem ser integrados com outras ferramentas de segurança, como o Azure Security Center e o Azure Advanced Threat Protection, para fornecer uma proteção adicional para sua rede virtual.

## **Objetivo**

Neste artigo, criaremos uma rede virtual, implantaremos uma máquinas virtual nessa rede virtual e as configuraremos um network security group para permitir e controlar o tráfego de entrada e saída deste recurso.

## **1.1 Criar máquina virtual para validação e testes**

Vamos criar uma máquina virtual para implantar nossa VNET e realizar os testes com nosso **network security group (NSG)**.

Como é um processo simples e já documentado aqui no site, não vou me atentar em descrever todos os passos.

Você pode consultar os seguintes artigos:

- [Azure-To] #1 Deploy de Máquina Virtual [Portal](https://unicast.com.br/posts/azure-to-1-deploy-de-maquina-virtual-portal/)
- [Azure-To] #2 Deploy de Máquina Virtual [PowerShell](https://unicast.com.br/posts/azure-to-2-deploy-de-maquina-virtual-powershell/)
- [Azure-To] #3 Deploy de Máquina Virtual [Azure CLI](https://unicast.com.br/posts/azure-to-3-deploy-de-maquina-virtual-azure-cli/)

Basta escolher um método para o deploy e adicionar as duas VMS na rede virtual (VNET) que criamos.

No meu caso, estou utilizando o sistema operacional **Windows Server 2019 Datacenter**, na guia Básico, preencha as seguintes informações (deixe os padrões para todo o resto):

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Virtual machine name: **vm-unicast-01**
- Region: **Brazil South** (ou outra região de sua preferência)
- Image: **Windows Server 2019 Datacenter Gen 2**
- Size: **Standard D2s v3**
- Administrator account username: **labuser**
- Administrator account password: **Pa$$w0rd1234**
- Inbound port rules: **None**

![](/assets/img/53/nsg01.png){: "width=60%" }

Na aba **Networking**, configure da seguinte forma:

NIC network security group: **None**

![](/assets/img/53/nsg02.png){: "width=60%" }

Deixe os padrões restantes e clique no botão **Review+Create**.

Na máquina virtual, clique em **Networking**, revise a guia **Inbound port rules**, observe que não há nenhum **network security group** associado à interface de rede da máquina virtual ou à sub-rede à qual a interface de rede está anexada.

![](/assets/img/53/nsg03.png){: "width=60%" }

## **2.1 Criar Network Security Group (NSG)**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo**, na caixa de Pesquisa, digite **Network Security Groups** e clique em **+ Add, + Create, + New**.

![](/assets/img/53/nsg04.png){: "width=60%" }

**Especifique as configurações do recurso:**

Siga as etapas necessárias para configurar seu NSG.

- Subscription: o nome da assinatura que você está usando neste * laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Name: **nsg-unicast-01**
- Region: **Brazil South** (ou outra região de sua preferência)

![](/assets/img/53/nsg05.png){: "width=60%" }

1. Clique em **Review+Create** e depois da validação clique em **Create**.
2. Após a criação do NSG, clique em **Go to resource.**
3. Em **Settings**, clique em **Network interfaces** e depois em **Associate**.

![](/assets/img/53/nsg06.png){: "width=60%" }

4. Selecione a interface de rede identificada na tarefa anterior.

![](/assets/img/53/nsg07.png){: "width=60%" }

![](/assets/img/53/nsg08.png){: "width=60%" }

## **2.2 Configurar regras de inbound para permitir acesso RDP**

Nesta tarefa, permitiremos o tráfego **RDP** para a máquina virtual configurando uma regra de porta de segurança de entrada.

1. No portal do Azure, navegue até a da máquina virtual criada.
2. No painel **Overview**, clique em **Connect**.
3. Tente se conectar à máquina virtual selecionando **RDP**, baixando e executando o arquivo **RDP**. Por padrão, o **NSG** não irá permitir o acesso. Feche a janela de erro.

![](/assets/img/53/nsg09.png){: "width=60%" }

4. Vá na máquina virtual, em **Settings**, clique em **Networking** e observe as regras de **inbound** para o network security group anexado à interface de rede, ela nega todo o tráfego de entrada, exceto o tráfego dentro da rede virtual e do balanceador de carga.
5. Na guia **Inbound port rules**, clique em **Add inbound port rule**. Clique em **Add** quando terminar.

**Especifique as configurações do recurso:**

- Source: Any
- Source port ranges: *
- Destination: Any
- Destination port ranges: 3389
- Protocol: TCP
- Action: Allow
- Priority: 300
- Name: AllowAnyRDPInbound

![](/assets/img/53/nsg10.png){: "width=60%" }

Selecione Adicionar e aguarde o provisionamento da regra, em seguida, tente novamente o acesso **RDP** na máquina virtual. Desta vez, você deve ter sucesso.

![](/assets/img/53/nsg11.png){: "width=60%" }

![](/assets/img/53/nsg12.png){: "width=60%" }

## **2.3 Configurar regras de outbound para negar o acesso a Internet**

Nesta tarefa, vamos trabalhar de forma inversa, criaremos uma regra de outbound que negará o acesso à Internet de nossa máquina virtual, em seguida, testaremos se a regra está funcionando.

1. Continue na sessão **RDP** da sua máquina virtual.
2. Depois que a máquina iniciar, abra o navegador de internet.
3. Verifique se você pode acessar **https://www.google.com** (Você precisará trabalhar com os pop-ups de segurança aprimorada do IE).

![](/assets/img/53/nsg13.png){: "width=60%" }

4. De volta ao portal do Azure, navegue de volta para a máquina virtual.
5. Em **Settings**, clique em **Networking** e em **Outbound port rules**.
6. Observe que há uma regra, **AllowInternetOutbound**. Esta é uma regra padrão e não pode ser removida.

![](/assets/img/53/nsg14.png){: "width=60%" }

7. Clique em **Add outbound port rule** no NSG da máquina virtual (anexado à interface de rede) e configure uma nova regra de segurança de **outbound** com uma prioridade mais alta que negará o tráfego da Internet. Clique em **Add** quando terminar.

**Especifique as configurações do recurso:**

- Source: Any
- Source port ranges: *
- Destination: Service Tag
- Destination service tag: Internet
- Destination port ranges: *
- Protocol: TCP
- Action: Deny
- Priority: 4000
- Name: DenyInternet

![](/assets/img/53/nsg15.png){: "width=60%" }

![](/assets/img/53/nsg16.png){: "width=60%" }

1. Clique em **Add** e retorne à VM com acesso via **RDP**.
2.  Verifique se você pode acessar **https://www.google.com**. (Você precisará trabalhar com os pop-ups de segurança aprimorada do IE).

![](/assets/img/53/nsg17.png){: "width=60%" }

De forma simples, testamos o funcionamento do **Network Security Group (NSG)** nas duas maneiras possíveis, regras de entrada **(inbound)** e regras de saída **(outbound)**.

É isso galera, espero que gostem!

Forte Abraço!