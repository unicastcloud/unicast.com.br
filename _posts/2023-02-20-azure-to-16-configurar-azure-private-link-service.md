---
layout: post
title: "[Azure-To] #16 Configurar Azure Private Link Service"
author: asilva
date: 2023-02-20 09:00:00 -0300
categories: [Azure, Azure-To]
tags: [azure, microsoft, network, private link service, az700]
---

Saudações Pessoal!!!

No cardápio de hoje vamos servir Azure Private Link Service. <br>
Masssss, antes de por a "mão na massa", vamos entender em quais momentos seria legal servir este "prato" blz...

### **Sobre Azure Private Link Service**

De forma objetiva, o Azure Private Link Service cria uma conexão **Privada** por dentro do **Backbone Microsoft**, entre VNET´s que podem estar inclusive em **Regiões Diferentes** e até mesmo, criar Conexão entre VNET´s de **Subscriptions Diferentes!!!** <br>
É isto mesmo que você está lendo, o **Azure Private Link Service** é uma alternativa de **Conexão Privada** para casos em que não se opte por Gateway de VPN ou Peering. <br>
O diagrama abaixo demonstra uma arquitetura de Private Link Service **integrando 2 Vnet´s distintas de Subscritprions Distintas**, onde recursos da **Subscription A, na Região A** se comunicam com VM´s da **Subscription B, na Região B** interligadas a um **Standard Load Balancer**

![](/assets/img/58/pvtls01.png){:"width=60%"}
<br>

Basicamente, a engrenagem roda com uma certa semelhança ao sistema **Cliente / Servidor** 

1. A VNET que terá o papel de **Servir**, precisa ter o recurso atrás de um **Standard Load Balancer.**
2. Um Private Link Service é criado e linkado ao **Frontend IP Interno** do Standard Load Balancer. 
3. Um Link **Service ID** (URI ou Alias) é compartilhado com o **Private Link** da **Subscription / Vnet** que terá a função de  **Cliente** do recurso. 
4. Do lado do ambiente **Cliente** é criado um **Private EndPoint** com as informações do **Service ID** bem como as configurações de DNS para registro do Private IP Address. 
5. O ambiente **Servidor** recebe um pedido para **Aprovar ou Rejeitar** a conexão. 
6. Em caso de aprovação, O ambiente **Cliente** inicia a conexão com o ambiente **Servidor**


### **Objetivo**

Permitir que uma Virtual Machine de uma **Subscription A, na Região EAST US2** acesse um Servidor Apache em uma Virtual Machine localizada na **Subscription B, da Região India Central**.

### **1.1 Criando o Azure**

- a 
- b 

>**Observação:** 
{: .prompt-warning }

### **2.1 Adicionando secrets no Azure Key Vault**

