---
layout: post
title: "Alterar endereçamento de rede do Azure Application Gateway"
author: asilva
date: 2022-01-09 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, network, applicationgateway]
---

Fala galera! Seis tão baum?

Caso você tenha a necessidade de modificar o endereçamento de IP de um Application Gateway, segue este tutorial que será muito útil a você.

É possível modificar uma vnet e subnet de um application gateway existente em sua infraestrutura, porém não de forma gráfica via portal do Azure.

Para essa atividade será necessário rodar alguns comandos via linha de comando CLI.

É um procedimento simples, porém, vale lembrar que haverá algum tempo de indisponibilidade durante a mudança.

## **Objetivo**

Modificar endereçamento de rede alocado para o Azure Application Gateway.

## **Motivação**

Imagine o seguinte cenário:

Você tem a vnet 10.0.0.0/24 dividida em várias subnets, onde você tem alocado para seu application gateway o endereço 10.0.0.224/28.

Em um /28 temos um total de 16 IPs onde o endereço de rede da subnet é o 10.0.0.224 e o último endereço é o de broadcast 10.0.0.239. Desta forma, é possível utilizar 14 endereços nesta rede.

Quem já trabalha com Azure, sabe que também devemos considerar as reservas que o provider faz.

Neste caso seria: 10.0.0.224 - 10.0.0.239 (11 + 5 Azure reserved addresses)

Com isso, teremos 11 endereços válidos para uso e não 14.

Caso você precise aumentar o poder computacional de seu application gateway, você deve considerar a quantidade de IPs que você tem alocada para este recurso.

Em nosso exemplo, temos 11 endereços válidos.

![](/assets/img/15/appgw1.png){: "width=60%" }

Se tentarmos aumentar a capacidade de instancias para 12 por exemplo, teremos o seguinte erro:

![](/assets/img/15/appgw2.png){: "width=60%" }

Ou seja, não temos endereços válidos para aumentar a capacidade acima de 11.

Para evitar que você tenha que criar um novo application gateway e configurá-lo novamente, segue os passos para alocarmos uma nova vnet e subnet para o recurso.

Lembrando que você pode criar uma nova vnet e subnet ou mesmo manter a mesma vnet com apenas mais uma subnet.

Para este exemplo, criarei mais uma subnet, utilizando a mesma vnet que já estava configurada para o application gateway.

## **Cenário atual**

**Address space:** 10.0.0.0/24

![](/assets/img/15/appgw3.png){: "width=60%" }

**Subnets:**

![](/assets/img/15/appgw4.png){: "width=60%" }

Veja que estou criando uma nova subnet, pois não tenho mais endereços disponíveis no /24 para aumentar o /28 do application gateway.

## **1.1 Criar nova subnet**

Vamos criar mais uma subnet em nossa vnet-unicastlab. O endereço será 10.1.0.0/24.

![](/assets/img/15/appgw5.png){: "width=60%" }

![](/assets/img/15/appgw6.png){: "width=60%" }

## **2.1 Parando o Application Gateway**

Você pode utilizar o Cloud Shell.

![](/assets/img/15/appgw7.png){: "width=60%" }

```bash
az network application-gateway stop 
--subscription NOME_DA_SUA_ASSINATURA \
--resource-group NOME_DO_RESOURCE_GROUP_DO_APPGW \
--name NOME_DO_APPGW
```

## **2.2 Modificando a subnet**

Rode o comando para ober as informações necessárias.

```bash
az network application-gateway show \
--subscription NOME_DA_SUA_ASSINATURA \
--resource-group NOME_DO_RESOURCE_GROUP_DO_APPGW \
--name NOME_DO_APPGW
```

Você terá uma saída em JSON como está:

![](/assets/img/15/appgw8.png){: "width=60%" }

Para modificar nossa subnet, precisamos copiar o subnet id autal e apontar a nova subnet em nosso comando. Ficando assim:

```bash
az network application-gateway update \
--subscription NOME_DA_SUA_ASSINATURA \
--resource-group NOME_DO_RESOURCE_GROUP_DO_APPGW \
--name NOME_DO_APPGW
--set gatewayIpConfigurations[0].subnet.id='/subscriptions/NOME_DA_SUA_ASSINATURA/resourceGroups/NOME_DO_RESOURCE_GROUP_DO_APPGW/providers/Microsoft.Network/virtualNetworks/NOME_VNET/subnets/NOME_DA_NOVA_SUBNET/'
```

Verifique se seu application gateway já está com a nova subnet configurada.

![](/assets/img/15/appgw9.png){: "width=60%" }

![](/assets/img/15/appgw10.png){: "width=60%" }

## **3.1 Iniciando o Application Gateway**

Por padrão, ao executar o comando para mudança da subnet, o application gateway já é iniciado, caso ele ainda esteja com o status de stop, você pode rodar o seguinte comando:

```bash
az network application-gateway start \
--subscription NOME_DA_SUA_ASSINATURA \
--resource-group NOME_DO_RESOURCE_GROUP_DO_APPGW \
--name NOME_DO_APPGW
```

Você pode confirmar o status de seu application gateway em: configurações, propriedades.

![](/assets/img/15/appgw11.png){: "width=60%" }

Como fizemos a adição de um /24, agora é possível aumentar a capacidade de nosso application gateway em até 125 instancias, que é o máximo permitido.

![](/assets/img/15/appgw12.png){: "width=60%" }

![](/assets/img/15/appgw13.png){: "width=60%" }

E isso ai galera, espero que gostem e que o artigo seja útil a vocês.

Forte abraço.