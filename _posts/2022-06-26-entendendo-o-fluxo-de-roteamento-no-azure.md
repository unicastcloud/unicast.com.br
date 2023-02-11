---
layout: post
title: "Entendendo o fluxo de roteamento no Azure"
author: asilva
date: 2022-06-25 09:00:00 -0500
categories: [Azure, Network]
tags: [azure, microsoft, network, rotas, udr]
---

Fala galera! Seis tão baum?

Hoje vamos abordar como funciona o processo de seleção de rotas no Microsoft Azure.

Em cenários menores, dificilmente você terá a necessidade de trabalhar com tabela de rotas no Azure, mas em ambientes maiores não tenha dúvida que você precisa ter conhecimento de como tudo funciona.

Estou falando das UDRs (User-Defined Routes) ou rotas definidas pelo usuário. Um cenário muito comum de uso de UDRs no Azure é a utilização de um Firewall de rede, que pode ser o Azure Firewall ou um firewall de terceiros como Palo 
Alto, Checkpoint, Fortinet entre outros.

>**Trago verdades 1:** para bom entendimento deste tópico é essencial que você tenha em mente que estamos falando basicamente de REDES, não somente redes no Azure, e sim redes como essência. Se você não tem um bom entendimento sobre os conceitos de rede, provavelmente este conteúdo irá gerar dúvidas e será de difícil compreensão.
{: .prompt-warning }

>**Trago verdades 2:** Azure, AWS ou GCP são apenas plataformas que abstraem hardware e rede para você, no fim você precisa saber sobre sistemas operacionais (Windows e Linux) e redes.
{: .prompt-warning }

>**Qualquer um que diga:** você não precisa ter muitos conhecimentos em sitemas operacionais, redes ou que meia dúzia de comandos já é o suficiente para ter uma posição de **Arquiteto em Cloud**, está em outro planeta ou está fazendo aquele marketing agressivo (**para não falar outra palavra**) de seu curso/mentoria para vender sonhos!
>
>Então, meu **jovem Padawan**, pare de querer subir 10 degraus de uma vez e vamos fazer o **básico bem-feito**, garanto que assim você será um excelente profissional, seja em cloud ou não.
{: .prompt-tip }

![](/assets/img/27/route1.png){: .shadow style="max-width: 50%" }

## **Rotas de sistema**

O Azure cria automaticamente rotas do sistema e atribui as rotas a cada sub-rede em uma rede virtual (vnet). Você não pode criar rotas de sistema nem removê-las, mas pode substituir algumas rotas do sistema por rotas personalizadas, utilizando UDRs.

Ao adicionar máquinas virtuais (VMs) a uma rede virtual (VNet) no Azure, você não precisa de nenhum tipo de configuração de rede para que elas se comuniquem, mesmo que as VMs estejam em sub-redes diferentes. O mesmo vale para a comunicação das VMs com a Internet.

Sempre que uma rede virtual é criada, o Azure cria automaticamente as seguintes rotas de sistema para cada sub-rede na rede virtual:

| **Fonte**            | **Prefixos de endereço**      | **Tipo de próximo salto** |
|:--------------------:|:-----------------------------:|:-------------------------:|
| Predefinição         | Exclusivo para a rede virtual | Rede virtual              |
| Predefinição         | 0.0.0.0/0                     | Internet                  |
| Predefinição         | 10.0.0.0/8                    | Nenhum                    |
| Predefinição         | 172.16.0.0/12                 | Nenhum                    |
| Predefinição         | 192.168.0.0/16                | Nenhum                    |
| Predefinição         | 100.64.0.0/10                 | Nenhum                    |

* **Rede virtual:** roteia o tráfego entre os intervalos de endereços dentro do espaço de endereço de uma rede virtual. O Azure cria uma rota com um prefixo de endereço que corresponde a cada intervalo de endereços definido no espaço de endereço de uma rede virtual. 

* **Internet:** roteia o tráfego especificado pelo prefixo de endereço para a Internet. A rota padrão do sistema especifica o prefixo de endereço 0.0.0.0/0. Se você não substituir as rotas padrão, o Azure roteará o tráfego de qualquer endereço não especificado por um intervalo de endereços em uma rede virtual para a Internet.

* **Nenhum:** o tráfego roteado para o tipo de próximo salto nenhum é descartado, em vez de roteado para fora da sub-rede. O Azure cria automaticamente rotas padrão para os seguintes prefixos de endereço:
    * 1**0.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16:** Reservado para uso privado na **RFC 1918**.
    * **100.64.0.0/10:** Reservado na **RFC 6598**.

## **Rotas padrão do sistema (Opicional)**

Determinadas rotas só são adicionadas pelo Azure nas tabelas de rotas quando há uma configuração específica habilitada. 

| **Fonte**            | **Prefixos de endereço**      | **Tipo de próximo salto** | **Sub-rede dentro da <br /> rede virtual à qual a rota <br /> é adicionada** |
|:--------------------:|:------------------------------|:--------------------------|:----------------------------------------------------------------------|
| Predefinição         | Exclusivo para a rede virtual, <br /> por exemplo: 10.1.0.0/16 | Emparelhamento de VNet | Tudo |
| Gateway de <br /> rede virtual |  Prefixos anunciados no local <br /> via BGP ou configurados no <br /> gateway de rede local | Gateway de rede virtual | Tudo |
| Predefinição         | Múltiplo | VirtualNetworkServiceEndpoint | Somente a sub-rede para a <br /> qual um terminal de serviço <br /> está habilitado |

* **Emparelhamento de rede virtual (VNet):** quando você cria um emparelhamento de rede virtual entre duas redes virtuais, uma rota é adicionada para cada intervalo de endereços dentro do espaço de endereço de cada rede virtual para a qual um emparelhamento é criado.

* **Gateway de rede virtual:** uma ou mais rotas com o gateway de rede virtual listado como o próximo salto são adicionados quando um gateway de rede virtual é adicionado a uma rede virtual. 

* **VirtualNetworkServiceEndpoint:** os endereços IP públicos para determinados serviços são adicionados à tabela de rotas pelo Azure quando você habilita um ponto de extremidade de serviço para o recurso. 

## **Rotas personalizadas UDR (User-Defined Routes)**

Para a maioria dos ambientes, você precisará apenas das rotas do sistema já definidas pelo Azure. No entanto, pode ser necessário criar uma tabela de rotas e adicionar uma ou mais rotas em casos específicos.

As UDRs têm preferência no processo de seleção de rotas do Azure. 

Uma UDR é identificada por seu nome e requer que o prefixo de endereço e o tipo de próximo salto sejam selecionados. Existem 5 tipos de próximos saltos disponíveis para uma UDR no Azure:

* Dispositivo virtual
* Gateway de rede virtual
* Nenhum
* Rede virtual
* Internet

* **Dispositivo virtual:** requer a especificação de um endereço IP do dispositivo que receberá o tráfego. Útil para utilização de firewalls e dispositivos de análise de tráfego.

* **Gateway de rede virtual:** pode ser usado apenas para gateways VPN. O ExpressRoute utiliza a propagação de rede baseada em BGP, portanto, nenhuma rota personalizada pode ser criada para usar o ExpressRoute Gateway. 

* **Nenhum:** tipo de próximo salto que descarta o tráfego correspondente.

* **Rede virtual:** é usado em topologias quando você deseja que o tráfego flua entre sub-redes em uma única Vnet para ir por meio do dispositivo virtual. 
    * **Exemplo:** o tráfego de uma sub-rede para outra em uma Vnet por padrão é roteado por meio da rota do sistema padrão. Você pode substituir isso selecionando o próximo salto como um dispositivo virtual. Para manter o fluxo de tráfego direto na sub-rede, você pode criar uma rota com o próximo salto da rede virtual para o prefixo da sub-rede.

* **Internet:** utilizada quando você deseja rotear explicitamente o tráfego destinado a um prefixo de endereço para a Internet ou se deseja tráfego destinado a serviços do Azure com endereços IP públicos mantidos na rede de backbone do Azure.

## **Processo de seleção de rota**

As sub-redes dependem de rotas do sistema até que uma tabela de rotas seja associada a ela. Uma vez que exista uma associação, o roteamento é feito com base no **Longest Prefix Match (LPM)** entre as rotas definidas pelo usuário e as rotas do sistema. Se houver mais de uma rota com o mesmo **LPM**, uma rota será selecionada com base em sua origem na seguinte ordem:

* Rota definida pelo usuário
* Rota BGP
* Rota do sistema

 >**Observação:** As rotas do sistema para tráfego relacionado à rede virtual, peerings de rede virtual ou pontos de extremidade de serviço de rede virtual são rotas preferenciais, mesmo que as rotas BGP sejam mais específicas.
 {: .prompt-info }

**Exemplo:**

| **Fonte**            | **Prefixos de endereço**      | **Tipo de próximo salto** |
|:--------------------:|:-----------------------------:|:-------------------------:|
| Predefinição         | 0.0.0.0/0                     | Internet                  |
| Do utilizador        | 0.0.0.0/0                     | gateway de rede virtual   |

Quando o tráfego é destinado a um endereço IP fora dos prefixos de endereço de quaisquer outras rotas na tabela de rotas, o Azure seleciona a rota com a origem do usuário, porque as rotas definidas pelo usuário têm prioridade mais alta do que as rotas padrão do sistema.

Veja bem, vamos voltar novamente no tema em que começei este artigo, pois é necessário reforçar o quao importante é ter uma base sólida em concetios básicos, que neste caso, estamos falando de **Redes**.

Se você já tem uma boa base em redes de computadores, provavelmente não está assustado(a) com nada do que falamos até agora, tudo parece seguir o básico, e assim tem que ser.

Vou dar um exemplo para que fique claro a importância da base:

A Cisco é uma das maiores fabricantes de equipamentos de rede do planeta, quando falamos sobre protocolos de roteamento dinâmico, é normal você conhecer sobre distâncias administrativas.

>**Importante:** Os valores de distância administrativa podem ser difirente entre fabricantes.
{: .prompt-warning }

Basicamente a **Distância Administrativa** é um parâmetro utilizado no roteamento de redes com a finalidade de que um roteador, ao ser informado que um destino pode ser alcançado por dois caminhos diferentes por protocolos de roteamento diferentes, possa tomar a decisão de qual é o melhor caminho.

**O padrão utilizado pelos equipamentos da Cisco são:**

| **Protocolo**       | **distância administrativa** |
|:-------------------:|:----------------------------:|
Diretamente conectado | 0   | 
Rota estática         | 1   |
EIGRP Rota sumarizada | 5   |
External BGP          | 20  |
Internal EIGRP        | 90  |
IGRP                  | 100 |
OSPF                  | 110 |
IS-IS                 | 115 |
RIP                   | 120 |
EGP                   | 140 |
ODR                   | 160 |
External EIGRP        | 170 |
Internal BGP          | 200 |
Desconhecida          | 255 |

Ou seja, perceba que rotas diretamente conectadas e rotas estáticas vão sempre ter prioridade de sleção aos outros protocolos de roteamente dinâmico.

E é basicamente o que estamos fazendo no Azure, você pode entender que as rotas de sistema são rotas dinâmicas, e quando você cria uma tabela de rota (UDR), você basicamente está criando uma rota estática, logo ela terá preferência na seleção de rotas, como vimos no exemplo acima.

## **Exemplos de roteamento**

**Cenário 1:** Configuração simples com uma Vnet, duas sub-redes e algumas VMs em cada sub-rede, ambas se comunicam de forma implicita por conta das rotas de sistema.

![](/assets/img/27/route2.png){: .shadow style="max-width: 80%" }

**Cenário 2:** Utilizando UDR para encaminhar o tráfego de rede para o Azure Firewall.

![](/assets/img/27/route3.png){: .shadow style="max-width: 80%" }

>**Importante:** As UDRs são aplicadas apenas ao tráfego que sai de uma sub-rede. você não pode criar rotas para especificar como o tráfego chega a uma sub-rede **pela Internet**. 
{: .prompt-warning }

**Cenário 3:** Utilizando UDR para encaminhar o tráfego de rede para o Virtual Appliance e comunicação com rede on-premises via Virtual Network Gateway.

![](/assets/img/27/route4.png){: .shadow style="max-width: 80%" }

Este cenário é muito parecido com o mencionado acima, mas ele tem uma particularidade.

>**Importante:** As UDRs são aplicadas apenas ao ambiente do Azure. Neste cenário, se você quiser adicionar um dispositivo NVA entre sua rede local e o Azure, precisará criar uma rota que encaminha todo o tráfego ao ambiente on-premises para o virtual appliance. Caso contrário, o tráfego de entrada fluirá pelo gateway VPN ignorando o virtual appliance.
{: .prompt-warning }

## **Exemplo de fluxo local para Azure**

 Para dar suporte ao tráfego on-premises para nosso ambiente Azure, é necessária criar uma tabela de rotas no **GatewaySubnet** onde o Gateway VPN está implantado.

**Alguns pontos importantes:**

* Um gateway de VPN ou gateway de ExpressRoute fica na frente do Firewall do Azure ou do Gateway de Aplicativo.
* O Firewall do Azure não oferece suporte a **DNAT** para endereços IP privados. Por isso, você deve usar UDRs para enviar tráfego de entrada ao Firewall do Azure dos gateways de VPN ou do ExpressRoute.
* Mesmo que todos os clientes atuem localmente ou no Azure, o Gateway de Aplicativo do Azure e o Firewall do Azure precisam ter endereços IP públicos. Os endereços IP públicos permitem à Microsoft gerenciar os serviços.

![](/assets/img/27/route5.png){: .shadow style="max-width: 80%" }

No diagrama acima podemos verificar que quando o tráfego sai do Gateway VPN (2), a UDR na sub-rede do gateway para 10.0.0.0/16 o enviará para o Load Balancer interno e em seguida para o Firewall do Azure. 

Como mencionei acima, o Firewall do Azure não gera tráfego de NAT de origem, portanto, os pacotes de entrada e saída atravessarão o mesmo Load Balancer interno e terão endereços e portas de origem e destino idênticos (porém invertidos). 

Caso você tenha uma conexão ExpressRoute entre sua rede on-premises e o Azure, você pode utilizar o BGP para propagar rotas de sua rede local para o Azure. Essas rotas BGP são usadas da mesma maneira que as rotas do sistema e as UDRs em cada sub-rede do Azure.

>**Importante:** Você pode configurar seu ambiente no Azure para usar o force tunneling por meio de sua rede local criando uma rota para a sub-rede 0.0.0.0/0 que usa o gateway de VPN como o próximo salto. No entanto, isso só funciona se você estiver usando um gateway VPN. Para o ExpressRoute, o force tunneling é configurado por meio do BGP.
{: .prompt-warning }

Bom é isso, sei que é muita informação, mas a ideia de um conteúdo denso como este, é trazer diversos aspectos sobre um determinado tema, com exemplos e conceitos. 

Não deixe de conferir a documentação oficial: <a href="https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-udr-overview" target="_blank"> Virtual network traffic routing</a> 

Espero que gostem.

Forte abraço a todos!
