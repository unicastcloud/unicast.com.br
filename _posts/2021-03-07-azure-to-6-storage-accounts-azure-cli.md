---
layout: post
title: "[Azure-To] #6 Storage Accounts [Azure CLI]"
author: asilva
date: 2021-03-07 06:54:00 -0500
categories: [Cloud, Azure]
tags: [blobstorage, storage, fileshare, azure, microsoft, infraestrutura, azurecli]
---

Fala galera! Seis tão baum?

Para finalizar nosso tópico sobre criação de Storage Accounts no Microsoft Azure.

Agora iremos aprender como cria-lo via Azure CLI.

Novamente a dica é simples, mas o intuito é lhe encorajar a utilizar mais comandos via CLI. Afim de você automatizar as tarefas do dia a dia e aumentar seu nível técnico.

## **Objetivo**

Criar uma Storage Account via Azure CLI.

## **1.1 Definindo as variáveis de rede**

```bash
resourceGroup='lab01-RG'
location='EastUS'
saName='storagename01'
sku='Standard_LRS'
kind='StorageV2'
```

## **2.1 Criando o Resource Groups**

```bash
az group create --name $resourceGroup --location $location
```

## **3.1 Criando a Storage Account**

```bash
az storage account create --name $saName --resource-group $resourceGroup --location $location --sku $sku --kind $kind
```

É isso, agora é só aguardar o deploy.

Você pode juntar todos os comandos em um único arquivo e ir alterando as variáveis de acordo com suas necessidades.

Espero que gostem!

Forte Abraço
