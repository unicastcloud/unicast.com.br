---
layout: post
title: "Configurar BGP no Azure VPN Gateway"
author: asilva
date: 2022-01-30 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, network, vnet, bgp, roteamento, vpn, ipsec, vpngateway]
---

Fala galera! Seis tão baum?

BGP é a sigla de **Border Gateway Protocol** e é definido pela <a href="https://datatracker.ietf.org/doc/html/rfc4271" target="_blank"> **RCF 4271**</a> como protocolo de roteamento padrão comumente usados na Internet para trocar informações de roteamento entre duas ou mais redes. 

O BGP troca informações entre Sistemas Autônomos, ou AS. Que representa uma entidade que controla um grupo de prefixos de rede. Um AS está associado a um Número de Sistema Autônomo ou ASN. Este número pode ser um número de 16 bits (64510 possibilidades) ou 32 bits (4 bilhões de possibilidades). 

Existem ASNs públicos que são utilizados por entidades externas. Qualquer operadora de telecomunicações, ISPs, faculdades, ou mesmo órgãos governamentais que precisam se comunicar na internet, devem ter um número de ASN para estabelecer peering e anunciar seus prefixos na internet.

No Brasil quem cuida dos registros de AS bem como à alocação dos prefixos IPv4 e IPv6 é o Registro.br.

Para trocar informações de forma privada com o BGP, não é necessário usar um registro na internet, mas é necessário usar um ASN privado, que pode ser um ASN entre 64512 e 65534 para ASN de 16 bits ou de 4200000000 a 4294967294 para ASN de 32 bits.

Neste artigo não vou me aprofundar nos detalhes do BGP, mas sugiro que você estude o tema pois a aderência de uso em ambientes cloud está cada vez maior.

Vou deixar aqui a indicação de um artigo no <a href="https://wiki.brasilpeeringforum.org/w/P%C3%A1gina_principal" target="_blank"> BPF (Brasil Peering Forum)</a> escrito pelo <a href="https://wiki.brasilpeeringforum.org/w/Usu%C3%A1rio:Leonardo.furtado" target="_blank"> Leonardo Furtado</a>, que se não é, é um dos que mais entende de redes/roteamento no Brasil.

O artigo é: <a href="https://wiki.brasilpeeringforum.org/w/O_Minimo_que_Voce_precisa_saber_sobre_o_BGP" target="_blank"> O mínimo que você precisa saber sobre BGP.</a>

Linkedin: <a href="https://www.linkedin.com/in/leofurtadonyc/" target="_blank"> Leonardo Furtado</a>

Discord: <a href="https://discord.gg/Vmf4Xdn23V" target="_blank" > https://discord.gg/Vmf4Xdn23V</a>  

Voltando ao contexto de redes virtuais no Azure, o BGP permite que Gateways de VPN do Azure e seus dispositivos de VPN locais, troquem "rotas" que informarão os dois gateways sobre a disponibilidade e acessibilidade dos prefixos anunciados.

Por padrão, a configuração do gateway VPN é baseada em política de rota. Você declara em ambos os lados os prefixos a serem anunciados e a mágica acontece. 

Exemplo:  Azure 10.0.0.0/24 e Local 172.16.0.0/24.

Em vários cenários, este modelo irá atender sem maiores problemas, porém, em ambientes em maiores e que utilizam redes no modelo Hub e Spoke, novos prefixos podem ser adicionados ao longo do tempo.

Sempre que você adicionar uma nova VNET ou um novo prefixo, você terá que alterar as configurações do túnel VPN, e este modelo de configuração de roteamento estático é difícil de ser gerenciado e não é nenhum pouco escalável.

Alguns cenários de utilização do BGP no Azure.

O diagrama a seguir, mostra um exemplo de suporte a vários túneis entre uma VNet e um site local com failover automático baseado em BGP:

![](/assets/img/17/bgp1.png){: "width=60%" }

O diagrama a seguir mostra um exemplo de topologia de vários saltos com vários caminhos que podem transitar o tráfego entre as duas redes locais por meio de gateways de VPN do Azure nas Redes Microsoft:

![](/assets/img/17/bgp2.png){: "width=60%" }

E para contornar este problema, a solução é habilitar o BGP em seu Gateway de VPN do Azure.

Para este artigo, não vou me atentar na configuração do túnel VPN, vou considerar que você já tem uma conexão estabelecida entre o Azure e sua rede local.

Mesmo assim, caso haja necessidade, posso abordar este tema em um outro artigo.

Sem mais delongas, vamos para a configuração.

### **1.1 Configurando virtual network gateway**

Primeiro, vamos habilitar o BGP em nossa virtual network gateway. Essa configuração é referente ao BGP do lado do Azure. Iremos definir o IP e o ASN que irá fechar o peering com o ambiente local.

Vá em **configuration**, clique em **Configure BGP** e habilite o **Gateway Private IPs.**

![](/assets/img/17/bgp3.png){: "width=60%" }

Precisamos definir o ASN que será utilizado para cada rede, ambiente Azure e ambiente local. Em nosso exemplo, utilizaremos ASN de 16 bits. Você também pode usar um ASN público caso tenha. Mas a melhor prática é utilizar um ASN privado.

Em **Autonomous system number (ASN)**, vamos definir o ASN do lado do Azure como **65010**.

Feito isso, clique em save.

### **2.1 Configurando local network gateway**

Agora, vamos configurar as informações referente ao nosso ambiente local. Será algo bem parecido com a virtual network gateway, mas agora vamos apontar o IP e ASN configurados em nossa rede local.

Vá em **configuration**, clique em **Configure BGP settings.**

Em nosso exemplo, vamos utilizar o IP **172.16.0.254** e o ASN **65020**.

![](/assets/img/17/bgp4.png){: "width=60%" }

Feito isso, clique em save.

### **3.1 Configurando a conexão**

Já com nossa virtual network gateway e local network gateway configurados no Azure, precisamos habilitar o BGP em nossa conexão.

Vá em **configuration**, em BGP clique em **enable**.

![](/assets/img/17/bgp5.png){: "width=60%" }

Feito isso, clique em save.

### **4.1 Verificando as rotas BGP**

Com nossa conexão estabelecida, podemos verificar se estamos aprendendo as rotas vai protocolo BGP.

Vá em na sua virtual network gateway, **monitoring, BGP peers.**

![](/assets/img/17/bgp6.png){: "width=60%" }

Veja que temos as informações referente a sessão BGP com nosso ambiente local.

Verificando o ambiente ao lado local, também posso ver o status da conexão BGP com o Azure, bem como os prefixos aprendidos dinamicamente.

![](/assets/img/17/bgp7.png){: "width=60%" }

É isso galera, BGP é um mundo a parte, continuem os estudos e pratiquem bastante.

Um forte abraço a todos.