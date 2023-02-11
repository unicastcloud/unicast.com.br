---
layout: post
title: "Configurando VPN Site to Site com Azure e MikroTik [CLI-Only]"
author: asilva
date: 2022-06-20 09:00:00 -0500
categories: [Azure, Network]
tags: [azure, microsoft, network, vnet, vpn, mikrotik, ipsec, vpngateway, s2s, powershell, cli, routeros]
---

Fala galera! Seis tão baum?

A Microsoft tem uma lista de dispositivos suportados e testados que funcionam com o Azure VPN Gateway. Infelizmente o Mikrotik não está nesta lista, o que significa que você está sozinho para descobrir como configurar a conexão VPN entre os dois ambientes.

Aqui no meu Homelab eu utlizo o Mikrotik como roteador central, ele gerência toda minha rede desde a conexão com ISP, DHCP, ACLs e controle de banda, além disso ainda tenho uma infinidade de recursos para usar e estudar, como OSPF, MPLS, BGP e por aí vai.

MikroTik é uma empresa da Letônia, fabricante de equipamentos para redes wireless e roteadores. Foi fundada em 1995 e se popularizou no mercado de provedores por conta da sua versatilidade e excelente custo-benefício.

O MikroTik possui vários modelos de dispositivos de rede, sugiro você dar uma olhada no <a href="https://mikrotik.com/" target="_blank"> site oficial</a> e conhecer um pouco mais sobre os produtos.

>**Atenção:** o IKEv2 foi introduzido na versão 6.38. Portanto, certifique-se de ter uma versão compatível para poder prosseguir com a configuração demonstrada neste artigo.
{: .prompt-warning }

### **Objetivo**

Implantar uma VPN Site to Site com Microsoft Azure e Mikrotik RouterOS.

#### **Cenário proposto**

![](/assets/img/26/mikrotik1.png){: "width=60%" }

Informações relevantes para a configuração do laboratório:

#### **Microsoft Azure**

- VNET: 10.0.0.0/24
- Public IP: 13.85.83.ZZ (trocar para seu cenário)

#### **Unicast on-premises**

- VNET: 172.16.0.0/24
- Public IP: 47.180.119.YY (trocar para seu cenário)

## **Fluxo do laboratório**

Para este laboratório iremos utilizar somente linha de comando (CLI) em ambos os lados, no lado do Azure vamos utilizar comandos de powershell e no lado do Mikrotik vamos utilizar a syntax própria do RouterOS que lembra bastante sistemas operacionais Linux.

Caso você queira reproduzir o laboratório via portal ou Winbox (interface GUI para gerenciar equipamentos Mikrotik), fique à vontade.

## **Configurando VPN Site-to-Site route-based no Azure**

Nesta seção, veremos passo a passo como configurar o Site-to-Site VPN no lado do Azure. 

### **1.1 Definindo as variáveis de rede**

```powershell
# Declare variables
  $VNetName  = "vnet-azure-to-mikrotik"
  $RG = "rg-azure-to-mikrotik"
  $Location = "East US"
  $SubName = "snet-azure"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/24"
  $SubPrefix = "10.0.0.0/25"
  $GWSubPrefix = "10.0.0.128/27"
  $VPNClientAddressPool = "172.16.0.0/24"
  $GWName = "vng-azure-to-mikrotik"
  $GWIPName = "pip-azure-to-mikrotik"
  $GWIPconfName = "gwipconf"
  $LNGName = "lng-azure-to-mikrotik"
  $ConName = "cn-azure-to-mikrotik"
  $ShareKey = "q1w2e3r4t5"
```

### **1.2 Criando o Resource Groups**

```powershell
New-AzResourceGroup -Name $RG -Location $Location
```

### **1.3 Criando a Virtual Network**

```powershell
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName $RG `
  -Location $Location `
  -Name $VNetName `
  -AddressPrefix $VNetPrefix1
```

### **1.4 Criando a Subnet**

```powershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name $SubName `
  -AddressPrefix $SubPrefix `
  -VirtualNetwork $virtualNetwork
```

### **1.5 Setando as configurações**
```powershell
$virtualNetwork | Set-AzVirtualNetwork
```

### **1.6 Adicionando a subnet do Virtual Network Gateway** 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $RG -Name $VNetName
Add-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix -VirtualNetwork $vnet
```

### **1.7 Definindo a configuração de subnet para a vnet**

```powershell
$vnet | Set-AzVirtualNetwork
```

### **1.8 Criando IP Público** 

```powershell
$gwpip= New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location `
 -AllocationMethod Dynamic
```

### **1.9 Criando a configuração do gateway**

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GWSubName -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

### **1.10 Criando o VPN Gateway**

```powershell
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
 -Location $Location -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1
```

### **1.11 Criando o Local Network Gateway**

```powershell
New-AzLocalNetworkGateway -Name $LNGName -ResourceGroupName $RG `
 -Location $Location -GatewayIpAddress '47.180.119.YY' -AddressPrefix $VPNClientAddressPool
```

### **1.12 Criando a conexão VPN**

```powershell
$gateway1 = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$local = Get-AzLocalNetworkGateway -Name $LNGName -ResourceGroupName $RG
New-AzVirtualNetworkGatewayConnection -Name $ConName -ResourceGroupName $RG `
-Location $Location -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -ConnectionProtocol IKEv2 -RoutingWeight 10 -SharedKey $ShareKey
```

## **Configurando VPN Site-to-Site no Mikrotik RouterOS**

Nesta seção, veremos passo a passo como configurar o Site-to-Site VPN no lado do Mikrotik. 

Lembrando mais uma vez:

>**IMPORTANTE**: Embora demonstremos o Mikrotik neste artigo, é importante mencionar que a Microsoft não suporta a configuração do dispositivo diretamente. Caso você tenha problemas, entre em contato com o fabricante do dispositivo para obter suporte adicional e instruções de configuração.
{: .prompt-danger }

>**Atenção:** o IKEv2 foi introduzido na versão 6.38. Portanto, certifique-se de ter uma versão compatível para poder prosseguir com a configuração demonstrada neste artigo.
{: .prompt-warning }

>No meu laboratório estou utilizando um MikroTik hAP Ac2 com a versão 6.49.2 do RouterOS.
{: .prompt-info }

Como mencionei, você pode utilizar o **Winbox** para realizar as configuraçãos no MikroTik utilizando uma interface gráfica.

### **2.1 Configurando o IPSEC Profile**

```bash
/ip ipsec profile
set [ find default=yes ] dh-group=modp1024 enc-algorithm=aes-256,aes-128,3des nat-traversal=no
```

### **2.2 Criando o IPSEC Peer**

```bash
/ip ipsec peer
add address=13.85.83.ZZ/32 comment="Unicastlab - VPN Azure to Mikrotik" exchange-mode=ike2 \
    local-address=47.180.119.YY name=mikrotik-to-azure
```

### **2.3 Criando o IPSEC Identity**

```bash
/ip ipsec identity
add comment="Unicastlab - VPN Azure to Mikrotik" peer=mikrotik-to-azure secret=q1w2e3r4t5
```

### **2.4 Configurando o IPSEC Proposal**

```bash
/ip ipsec proposal
set [ find default=yes ] enc-algorithms=aes-256-cbc,aes-128-cbc lifetime=7h30m
```

### **2.5 Criando o IPSEC Policy**

```bash
/ip ipsec policy
add comment="Unicastlab - VPN Azure to Mikrotik" dst-address=10.0.0.0/24 peer=mikrotik-to-azure \
    src-address=172.16.0.0/24 tunnel=yes
```

### **2.6 Criando regras de NAT**

```bash
/ip firewall nat
add action=accept chain=srcnat comment="Unicastlab - VPN Azure to Mikrotik (SRCNAT)" \
    dst-address=10.0.0.0/24 log=yes src-address=172.16.0.0/24
add action=accept chain=dstnat comment="Unicastlab - VPN Azure to Mikrotik (DSTNAT)" \
    dst-address=172.16.0.0/24 log=yes src-address=10.0.0.0/24
```

### **2.7 [Extra] Configurando o Bypass de pacotes (Tabela Raw)**

Caso você esteja com contrack habilitado, você precisa fazer um bypass dos pacotes antes que cheguem até a
connection tracking, assim será possível pingar e acessar as máquinas sem problemas.

A tabela RAW pode lhe ajudar a reduzir drasticamente a sobrecargas na CPU, vale a pena dar uma estudada.

**As ações mais comuns da tabela RAW são:**

- Drop: Usada para dropar pacotes antes que ele avance para processos internos.
- No track: Usada para fazer com que pacotes façam um bypass da connection tracking.

```bash
/ip firewall raw
add action=notrack chain=prerouting dst-address=10.0.0.0/24 src-address=172.16.0.0/24
add action=notrack chain=prerouting dst-address=172.16.0.0/24 src-address=10.0.0.0/24
```

### **2.8 [Extra] Configurando o TCP MSS (Tabela Mangle)**

Outra dica interessante é o TCP MSS, quando você tem um alto volume de pacotes TCP no túnel VPN é normal ter descartes de pacotes. Com a Mangle você consegue fazer marcas especiais e modificar alguns campos do cabeçalho IP, desta forma é possível melhorar o desempenho no fluxo de dados.

```bash
/ip firewall mangle
add action=change-mss chain=forward comment="Unicastlab - VPN Azure to Mikrotik - TCP MSS" \
    dst-address=10.0.0.0/24 new-mss=1360 passthrough=yes protocol=tcp tcp-flags=syn
```

## **Validando a conexão VPN**

Agora já podemos validar a conexão de ambos os lados e fazer um teste de acesso.

>Faça o deploy de uma VM no Azure e teste a conexão com seu ambiente on-premises.
{: .prompt-tip }

### **Mikrotik**

```bash
ip ipsec active-peers print 
```

```bash
Flags: R - responder, N - natt-peer 
 #    ID                   STATE              UPTIME          PH2-TOTAL
 0    ;;; Unicastlab - VPN Azure to Mikrotik
      13.85.83.ZZ         established        31m53s                  1
```

### **Azure (Powershell)**

```powershell
Get-AzVirtualNetworkGatewayConnection -Name cn-azure-to-mikrotik -ResourceGroupName rg-azure-to-mikrotik
```

```powershell
RoutingWeight           : 10
ConnectionStatus        : Connected
EgressBytesTransferred  : 84146
IngressBytesTransferred : 48761
TunnelConnectionStatus  : []
IngressNatRules         : []
EgressNatRules          : []
```

É isso galera, espero que gostem.

Forte abraço a todos!