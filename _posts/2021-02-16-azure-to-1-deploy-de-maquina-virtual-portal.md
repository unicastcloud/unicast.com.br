---
layout: post
title: "[Azure-To] #1 Deploy de Máquina Virtual [Portal]"
author: asilva
date: 2021-02-16 06:54:00 -0500
categories: [Azure, Azure-To]
tags: [maquinavirtual, azure, microsoft, infraestrutura]
---

Fala galera! Seis tão baum?

Hoje vou começar uma nova série aqui no site. O **Azure-To** será uma maneira de apresentar diversos assuntos sobre Azure de uma forma simples sem muita teoria. Nos moldes do clássico How-To, direto ao ponto!

E hoje vamos começar com o deploy mais básico no Azure, que é subir uma máquina virtual via portal.

### **Objetivo**

Implantar uma VM executando Windows Server 2016 Datacenter via portal.

### **1.1 Acessando o Portal**

Abra seu navegador e acesse o portal Azure em **http://portal.azure.com** e faça login usando sua conta da Microsoft.

### **1.2 Criando um recurso**

No portal da Azure clique em **Create a resource**

![](/assets/img/01/vm01.png){: .shadow style="max-width: 90%" }

### **1.3 Azure Marketplace**

Pesquise no Azure Marketplace por **Windows Server**. E selecione na lista de resultados de pesquisa.

No menu, escolha a opção **Datacenter [smalldisk] Windows Server 2016** e em seguida, clique em **Create**.

![](/assets/img/01/vm02.png){: .shadow style="max-width: 90%" }

### **1.4 Configuração da Máquina Virtual**

Agora basta inserir as informações de acordo com sua necessidade, para este laboratório vamos seguir com estas:

* Subscription: o nome da assinatura que você está usando neste laboratório
* Resource group: crie um novo resource group **lab01-RG**
* Virtual machine name: **lab01-vm0**
* Region: **(US) East US** (ou outra região de sua preferência)
* Availability options: **No infrastructure redundancy required**
* Image: **[smalldisk] Windows Server 2016 Datacenter**
* Size: **Standard B2s**
* Username: **labuser**
* Password: **Jn77a.lb1234**
* Public inbound ports: **Allow selected ports**
* Select inbound ports: **RDP 3389** (Aqui já vamos deixar habilitado o acesso remoto ao servidor)
* Already have a Windows license?: **No**

![](/assets/img/01/vm03.png){: .shadow style="max-width: 90%" }

Clique em **Next: Disks>**

### **1.5 Configurando os Discos**

OS disk type: **Standard HDD**

![](/assets/img/01/vm04.png){: .shadow style="max-width: 90%" }

Clique em Next: Networking >

### **1.6 Configurando a rede**

Na opção **Virtual Network**, clique em **Create new**. Use o mesmo nome de rede já atribuido anteriormente, modifique o **address range** e crie uma nova **subnet**.

Virtual network address range: 10.0.0.0/16
Subnet name: **subnet0**
Subnet address range: 10.0.0.0/24

![](/assets/img/01/vm05.png){: .shadow style="max-width: 90%" }

Para este laboratório não vamos alterar mais nenhuma opção, com isso clique em **Next: Management >** em seguida **Next: Advanced >** e **Next: Tags >** e por fim Next: **Review + create.**

### **1.7 Criando a Máquina Virtual**

Será exibida uma tela com o resumo de todas as configurações executadas bem como a validação delas.

![](/assets/img/01/vm06.png){: .shadow style="max-width: 90%" }

Agora basta clicar em Create e aguardar o deploy de sua VM.

É isso galera, o processo é bem simples. No próximo post irei demonstar como realizar a mesma configuração via PowerShell.

Espero que gostem, qualquer dúvida só postar nos comentários!

Forte abraço!