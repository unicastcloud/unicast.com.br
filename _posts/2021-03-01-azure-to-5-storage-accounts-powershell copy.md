---
layout: post
title: "[Azure-To] #5 Storage Accounts [PowerShell]"
author: asilva
date: 2021-03-01 06:54:00 -0500
categories: [Azure, Azure-To]
tags: [blobstorage, storage, fileshare, azure, microsoft, infraestrutura, poweshell]
---

Fala galera! Seis tão baum?

Dica rápida neste Azure-To, iremos aprender como criar uma Storage Account via PowerShell.

A dica é simples, mas o intuito é lhe encorajar a utilizar mais comandos via CLI. Afim de você automatizar as tarefas do dia a dia e aumentar seu nível técnico.

### **Objetivo**

Criar uma Storage Account via Powershell

#### **1.1 Definindo as variáveis de rede**

```powershell
$resourceGroup  = "lab01-RG"
$location       = "EastUS"
$saName 	= "storagename01"
$sku		= "Standard_LRS"
$kind		= "StorageV2"
```

### **2.1 Criando o Resource Groups**

```powershell
New-AzResourceGroup -Name $ResourceGroup -Location $Location
```

### **3.1 Criando a Storage Account**

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $saName -Location $location -SkuName $sku -Kind $kind
```

É isso, agora é só aguardar o deploy.

Você pode juntar todos os comandos em um único arquivo e ir alterando as variáveis de acordo com suas necessidades.

Espero que gostem!

Forte Abraço
