---
layout: post
title: "[Azure-To] #4 Storage Accounts [Portal]"
author: asilva
date: 2021-02-22 06:54:00 -0500
categories: [Azure, Azure-To]
tags: [blobstorage, storage, fileshare, azure, microsoft, infraestrutura]
---

Fala galera! Seis tão baum?

Neste Azure-To iremos aprender como criar uma Storage Account via Portal e implementar os serviços de Blob Storage e File Storage.

### **Objetivo**

* Criar uma Storage Account
* Implementar o Azure Blob Storage
* Implementar o Azure File Storage

### **1.1 Acessando o Portal**

Abra seu navegador e acesse o portal Azure em **http://portal.azure.com** e faça login usando sua conta da Microsoft.

### **1.2 Criando um recurso**

No portal da Azure clique em **Create a resource**

![](/assets/img/02/storage1.png){: "width=60%" }

### **1.3 Azure Marketplace**

Pesquise no Azure Marketplace por  Storage account e clique em Create

![](/assets/img/02/storage2.png){: "width=60%" }

### **1.4 Configuração da Máquina Virtual**

Agora basta inserir as informações de acordo com sua necessidade, para este laboratório vamos seguir com estas:

* Subscription: o nome da assinatura que você está usando neste laboratório
* Resource group: crie um novo resource group **lab01-RG**
* Storage account name:  qualquer nome válido e único entre 3 e 24 caracteres consistindo de letras e dígitos minúsculos
* Location: **(US) East US** (ou outra região de sua preferência)
* Performance: **Standard**
* Account Kind:  **Storage (general purpose v2)**
* Replication :  **Locally-redundant storage (LRS)**

![](/assets/img/02/storage3.png){: "width=60%" }

Clique em **Review + create**, e em seguida **Create**

### **2.1 Criando o serviço de Blob Storage**

No portal Azure, navegue até a storage account que você criou e clique em **Containers**.

Clique em **Container**, crie um novo contêiner chamado **lab01-container** com o **Public access level** definido para **Private (no anonymous access).**

Em seguida clique em **Create**.

![](/assets/img/02/storage4.png){: "width=60%" }

Pronto, seu Blob Storage já está criado, agora você já pode acessa-lo e fazer o upload de seus arquvios.

### **3.1 Criando o serviço de File Storage**

Agora vamos criar nosso File Share

No portal Azure, navegue até a storage account que você criou e clique em **File shares.**

Clique em File share, crie um novo File share chamado **lab01-share** e defina a Quota desejada.

Em seguida clique em **Create**.

![](/assets/img/02/storage5.png){: "width=60%" }

Pronto, seu File Share já está criado, agora você já pode acessa-lo e fazer o upload de seus arquvios.

### **3.2 Mapeamento de arquivos File Share**

Com seu File Share criado, você poderá realizar o mapeamento em seu computador, da mesma maneira que faria com um compartilhamento de rede local.

Navegue até o **File Share** que você criou, clique em **Connect**. No menu a direita será exibido as informações necessárias para efetuar o compartilhamento com seu computador. Basta selecionar o **Sistema Operacional**, cópiar o código e utiliza-lo em seu computador.

![](//assets/img/02/storage6.png){: "width=60%" }

É isso galera, o processo é bem simples.

Espero que gostem, qualquer dúvida só postar nos comentários!

Forte abraço!