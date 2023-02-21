---
layout: post
title: "[Azure-To] #17 Configurar Azure Private Link Service [Portal]"
authors: [asilva, rpaliosa]
date: 2023-02-20 09:00:00 -0300
categories: [Azure, Azure-To]
tags: [azure, microsoft, network, privatelinkservice, az700]
---

Saudações Pessoal!!!

No cardápio de hoje vamos servir Azure Private Link Service. 

Masssss, antes de por a "mão na massa", vamos entender em quais momentos seria legal servir este "prato" blz...

### **Sobre Azure Private Link Service**

De forma objetiva, o Azure Private Link Service cria uma conexão **Privada** por dentro do **Backbone Microsoft**, entre VNET´s que podem estar inclusive em **Regiões Diferentes** e até mesmo, criar Conexão entre VNET´s de **Subscriptions Diferentes!!!** 

É isto mesmo que você está lendo, o **Azure Private Link Service** é uma alternativa de **Conexão Privada** para casos em que não se opte por Gateway de VPN ou Peering.

O diagrama abaixo demonstra uma arquitetura de Private Link Service **integrando 2 Vnet´s distintas de Subscritprions Distintas**, onde recursos da **Subscription A, na Região A** se comunicam com VM´s da **Subscription B, na Região B** interligadas a um **Standard Load Balancer**

![](/assets/img/59/pvtls01.png){:"width=60%"}

Basicamente, a engrenagem roda com uma certa semelhança ao sistema **Cliente / Servidor** 

1. A VNET que terá o papel de **Servir**, precisa ter o recurso atrás de um **Standard Load Balancer.**
2. Um Private Link Service é criado e linkado ao **Frontend IP Interno** do Standard Load Balancer. 
3. Um Link **Service ID** (URI ou Alias) é compartilhado com o **Private Link** da **Subscription / Vnet** que terá a função de  **Cliente** do recurso. 
4. Do lado do ambiente **Cliente** é criado um **Private EndPoint** com as informações do **Service ID** bem como as configurações de DNS para registro do Private IP Address. 
5. O ambiente **Servidor** recebe um pedido para **Aprovar ou Rejeitar** a conexão. 
6. Em caso de aprovação, O ambiente **Cliente** inicia a conexão com o ambiente **Servidor**

### **Objetivo**

Permitir que uma Virtual Machine de uma **Subscription A, na Região Central India** simulando a função de *Cliente* acesse servidores Apache em Virtual Machines localizadas na **Subscription B, da Região East US2**, simulando a função de *Servidor*.
Detalhes no diagama abaixo:

![](/assets/img/59/pvtls02.png){:"width=60%"}

>**Observação:** Este Artigo parte do princípio que o leitor já domina a criação de Máquinas Virtuais, Virtual Network e  Network Security Group **(NSG)**!!!
{: .prompt-warning }

### **1. Criar ambiente da Subscription Servidor em EAST US 2**

1. Criar o **Resource Group** ```RG-Servidor``` na região **EAST US 2**
2. Criar a **VNET** ```Vnet1-Servidor``` com o Range de IP ```192.168.0.0/16``` e a **Subnet** ```Sub1-Servidor``` com Range de IP ```192.168.0.0/24```
3. Criar o **Network Security Group** chamado ```NSG1``` e associar a Subnet **Sub1-Servidor**
4. Criar a Virtual Machine chamada **VM-Apache1** conforme descrição imagens abaixo:  

![](/assets/img/59/pvtls03.png){:"width=60%"}

![](/assets/img/59/pvtls04.png){:"width=60%"}

>**Observação:** A Virtual Machine deve ser criada com o atributo **NONE** para **Public IP** e **Nic Network Security Group** !!!
{: .prompt-warning }

![](/assets/img/59/pvtls05.png){:"width=60%"}

#### **1.2 - Instalar Servidor Apache nas VMs**

Acessar o Painel de Administração da VM **Apache1** e na Categoria **Operations**, clicar em **Run Command** e depois em **RunShellScript**

![](/assets/img/59/pvtls06.png){:"width=60%"}

Digitar o script conforme a figura a baixo e clicar em **Run**

![](/assets/img/59/pvtls07.png){:"width=60%"}

```bash
apt update -y
apt install apache2 -y
cd /var/www/html
mv index.html index2.html
echo "SERVIDOR APACHE 01" > index.html
```

O resultado do Script deve retornar mensagem similar a figura abaixo.

![](/assets/img/59/pvtls08.png){:"width=60%"}

>**Observação:** Para criar a **vm-apache2** basta executar novamente as etapas **1.4**, **1.5**, **1.6** e **1.7**, porém, alterando a linha **echo** do Script para:
```bash
echo "SERVIDOR APACHE 02" > index.html
```
{: .prompt-warning }

### **2. Criar Load Balancer Interno para vm-apache1 e vm-apache2**

| **Recurso**           | **Descrição**                |
| ----------------------| :---------------------------:|
| Resouce Group         | ```RG-Servidor```            |
| Region                | ```EAST US2```               |
| Image                 | ```Ubuntu Server 20.04 LTS```|
| Size                  | ```B1S``` ou ```B2S```       |
| Disk                  | ```SSD Standard```           |
