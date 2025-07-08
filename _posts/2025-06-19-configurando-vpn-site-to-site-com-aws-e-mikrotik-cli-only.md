---
layout: post
title: "Configurando VPN Site to Site com AWS e MikroTik [CLI-Only]"
author: asilva
date: 2025-06-19 08:00:00 -0500
categories: [Cloud, AWS]
tags: [aws, cli, infraestrutura, cloud]
---

Fala galera! Seis tão baum?

Há algum tempo escrevi um artigo sobre como configurar uma <a href="https://unicast.com.br/posts/configurando-vpn-site-to-site-com-azure-e-mikrotik-cli-only/" target="_blank">VPN Site-to-Site entre o Azure e um MikroTik</a> totalmente via linha de comando. Foi um baita aprendizado prático, principalmente porque o **MikroTik** não é oficialmente suportado como dispositivo de VPN no **Azure** — o que nos obriga a entender bem os detalhes técnicos.

Agora, nesta nova etapa de estudos e trabalho com **AWS Provider**, resolvi revisitar o tema **VPN Site-to-Site**, mas desta vez com a **Amazon Web Services (AWS)**. E adivinha? A AWS fornece templates de configuração específicos para **MikroTik**, o que já facilita bastante a vida — mesmo que ainda não seja um vendor oficialmente homologado como Cisco ou Juniper.

Então, bora para mais um hands-on técnico usando MikroTik + AWS, tudo via CLI, como gostamos!

## **Objetivo**

Implantar uma VPN Site-to-Site com AWS e MikroTik RouterOS usando IKEv2, garantindo conectividade segura entre nuvem e rede on-premises.

## **Cenário proposto**

- AWS (Cloud)
  - VPC: `10.10.0.0/24`
  - Tunnel IP AWS: `35.172.99.11` e `44.212.169.18`
  - Gateway: `vgw-aws-to-mikrotik`

- On-Premises (Homelab)
  - Rede local: `172.16.0.0/24`
  - Public IP MikroTik: `203.0.113.1`
  - RouterOS: `6.49.2+`

## **Criando a infraestrutura na AWS via AWS CLI**

Você precisa ter o **AWS CLI** instalado e configurado com uma conta com permissões de VPC, EC2 e VPN.

### **1. Criar a VPC**

````bash
aws ec2 create-vpc \
  --cidr-block 10.10.0.0/24 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=vpc-aws-mikrotik}]'
````

### **2. Criar a subnet e o internet gateway**

````bash
aws ec2 create-subnet \
  --vpc-id vpc-xxxxxxxxxxxxxxxxx \
  --cidr-block 10.10.0.0/24 \
  --availability-zone us-east-1a
````

````bash
aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway \
  --internet-gateway-id igw-xxxxxxxxxxxxxxxxx \
  --vpc-id vpc-xxxxxxxxxxxxxxxxx
````

### **3. Criar a tabela de rotas e associar à subnet**

````bash
aws ec2 create-route-table \
  --vpc-id vpc-xxxxxxxxxxxxxxxxx \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=rtb-aws-mikrotik}]'
````

````bash
aws ec2 associate-route-table \
  --subnet-id subnet-xxxxxxxxxxxxxxxxx \
  --route-table-id rtb-xxxxxxxxxxxxxxxxx
````

````bash
aws ec2 create-route \
  --route-table-id rtb-xxxxxxxxxxxxxxxxx \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id igw-xxxxxxxxxxxxxxxxx
````

### **4. Criar o Virtual Private Gateway (VGW)**

````bash
aws ec2 create-vpn-gateway \
  --type ipsec.1 \
  --amazon-side-asn 65001 \
  --tag-specifications 'ResourceType=vpn-gateway,Tags=[{Key=Name,Value=vgw-aws-to-mikrotik}]'
````

````bash
aws ec2 attach-vpn-gateway \
  --vpn-gateway-id vgw-xxxxxxxxxxxxxxxxx \
  --vpc-id vpc-xxxxxxxxxxxxxxxxx
````

### **5. Criar o Customer Gateway (CGW)**

````bash
aws ec2 create-customer-gateway \
  --type ipsec.1 \
  --public-ip 203.0.113.1 \
  --bgp-asn 65000 \
  --tag-specifications 'ResourceType=customer-gateway,Tags=[{Key=Name,Value=cgw-mikrotik}]'
````

### **6. Criar a VPN Connection**

````bash
aws ec2 create-vpn-connection \
  --type ipsec.1 \
  --customer-gateway-id cgw-xxxxxxxxxxxxxxxxx \
  --vpn-gateway-id vgw-xxxxxxxxxxxxxxxxx \
  --options StaticRoutesOnly=true \
  --tag-specifications 'ResourceType=vpn-connection,Tags=[{Key=Name,Value=vpn-aws-to-mikrotik}]'
````

### **7. Associar rota estática (para rede on-prem)**

````bash
aws ec2 create-vpn-connection-route \
  --destination-cidr-block 172.16.0.0/24 \
  --vpn-connection-id vpn-xxxxxxxxxxxxxxxxx
````

### **8. Obter as configurações da VPN**

Você pode extrair a PSK (pre-shared key) usando:

````bash 
aws ec2 describe-vpn-connections --vpn-connection-ids vpn-xxxxxxxxxxxxxxxxx \
  --query 'VpnConnections[0].CustomerGatewayConfiguration' --output text
````

Ou obter o modelo completo para MikroTik:

````bash
aws ec2 get-vpn-connection-device-sample-configuration \
  --vpn-connection-id vpn-xxxxxxxxxxxxxxxxx \
  --vpn-connection-device-type-id mikrotik \
  --internet-key-exchange-version ikev1
````

A PSK estará visível como `Secret`: nesse output.

## **Configurando VPN Site-to-Site no MikroTik RouterOS**

>**Atenção:** Testado em MikroTik hAP Ac2 com RouterOS 6.49.2+. IKEv2 é obrigatório — presente a partir da v6.38.
{: .prompt-warning }

### **2.1 IPSEC Profile**

````bash
/ip ipsec profile
set [ find default=yes ] dh-group=modp1024 enc-algorithm=aes-256,aes-128,3des nat-traversal=no
````

### **2.2 IPSEC Peer**

````bash
/ip ipsec peer
add address=35.172.99.11/32 comment="AWS VPN Peer" exchange-mode=ike2 \
    local-address=203.0.113.1 name=mikrotik-to-aws
````

### **2.3 IPSEC Identity**

````bash
/ip ipsec identity
add peer=mikrotik-to-aws secret=CHANGEME_SECRET_HERE comment="AWS VPN PSK"
````

### **2.4 IPSEC Proposal**

````bash
/ip ipsec proposal
set [ find default=yes ] enc-algorithms=aes-256-cbc,aes-128-cbc lifetime=7h30m
````

### **2.5 IPSEC Policy**

````bash
/ip ipsec policy
add dst-address=10.10.0.0/24 peer=mikrotik-to-aws src-address=172.16.0.0/24 tunnel=yes comment="VPN to AWS"
````

### **2.6 Regras de NAT**

````bash
/ip firewall filter
add chain=input protocol=ipsec-esp action=accept comment="Allow IPsec ESP"
add chain=input protocol=udp port=500,4500 action=accept comment="Allow IKE"
````

### **2.7 [Extra] Tabela RAW (Bypass conntrack)**

````bash
/ip firewall raw
add action=notrack chain=prerouting dst-address=10.10.0.0/24 src-address=172.16.0.0/24
add action=notrack chain=prerouting dst-address=172.16.0.0/24 src-address=10.10.0.0/24
````

**Por que usar RAW com notrack?**

"Essa regra serve para bypassar o connection tracking do MikroTik. Quando pacotes de VPN passam por esse módulo, podem ocorrer inconsistências como falha em ping, problemas com NAT e uso excessivo de CPU. O notrack garante que o roteador apenas encaminhe o pacote sem tentar “entender” a conexão — ideal para túneis IPsec, que já carregam seu próprio estado."

### **2.8 [Extra] Ajuste de TCP MSS**

````bash
/ip firewall mangle
add chain=forward protocol=tcp tcp-flags=syn dst-address=10.10.0.0/24 action=change-mss new-mss=1360 comment="Ajuste MSS VPN AWS"
````

**Por que ajustar o TCP MSS?**

"O cabeçalho adicional introduzido por túneis IPsec reduz o tamanho do MTU. Sem esse ajuste, conexões TCP maiores podem ser fragmentadas ou até descartadas. Ao forçar um MSS menor, evitamos fragmentação e melhoramos a estabilidade da VPN, especialmente com serviços web e SSH."

## Validando a conexão VPN

````bash
aws ec2 describe-vpn-connections --vpn-connection-ids vpn-xxxxxxxxxxxxxxxxx \
  --query 'VpnConnections[0].VgwTelemetry'
````

Saída esperada:

````bash
[
  {
    "Status": "UP",
    "OutsideIpAddress": "200.140.15.YY",
    "LastStatusChange": "...",
    "AcceptedRouteCount": 1
  }
]
````

## **Encerrando e apagando recursos (opcional)**

````bash
# Substitua os valores com os IDs usados no seu ambiente
VPN_CONNECTION_ID="vpn-06f280a7985d5f7bc"
CUSTOMER_GATEWAY_ID="cgw-0d549fed432c06bd4"
VIRTUAL_GATEWAY_ID="vgw-08501e239e713c294"

# Exclui a conexão VPN
aws ec2 delete-vpn-connection --vpn-connection-id $VPN_CONNECTION_ID

# Exclui o Customer Gateway
aws ec2 delete-customer-gateway --customer-gateway-id $CUSTOMER_GATEWAY_ID

# Desanexa o Virtual Private Gateway (se estiver anexado a uma VPC)
VPC_ID=$(aws ec2 describe-vpn-gateways --vpn-gateway-ids $VIRTUAL_GATEWAY_ID --query "VpnGateways[0].VpcAttachments[0].VpcId" --output text)
aws ec2 detach-vpn-gateway --vpn-gateway-id $VIRTUAL_GATEWAY_ID --vpc-id $VPC_ID

# Exclui o Virtual Private Gateway
aws ec2 delete-vpn-gateway --vpn-gateway-id $VIRTUAL_GATEWAY_ID
````

## **Conclusão**

Mais uma vez conseguimos integrar nosso querido MikroTik com um ambiente cloud — agora com a poderosa AWS!

- Tudo via linha de comando
- Configuração segura com IKEv2
- Ótima base para estudar BGP, failover e automações com Terraform

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender um pouco sobre AWS! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte abraço a todos!
