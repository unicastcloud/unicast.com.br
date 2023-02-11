---
layout: post
title: "[Azure-To] #3 Deploy de Máquina Virtual [Azure CLI]"
author: asilva
date: 2021-02-19 06:54:00 -0500
categories: [Azure, Azure-To]
tags: [maquinavirtual, azure, microsoft, infraestrutura, azurecli]
---

Fala galera! Seis tão baum?

Seguindo com nossa série Azure-To, desta vez vamos fazer o deploy de uma máquina virtual Linux utilizando o Azure CLI.

### **Objetivo**

Implantar uma VM executando Ubuntu Server 18.04 LTS via Azure CLI.

#### **Para o deploy de nossa VM vamos:**

* Criar um resource group
* Criar uma virtual network e uma subnet
* Criar um network security group e as regras para acesso a nossa VM.
* Criar uma virtual machine com o sistema operacional Ubuntu Server 18.04 LTS

Da mesma forma que no post anterior [[Azure-To] #2 Deploy de Máquina Virtual [PowerShell]](https://unicast.com.br/posts/azure-to-2-deploy-de-maquina-virtual-powershell/) apresentei para vocês os comandos em forma de script. Aqui também vamos utilizar a mesma metodologia.

### **1.1 Definindo as variáveis de rede**

```bash
ResourceGroup='lab02-RG'
Location='eastus'
vNetName='lab02-RG-vnet'
AddressSpace='10.0.0.0/16'
SubnetIPRange='10.0.0.0/24' 
SubnetName='subnet0'
nsgName='lab02-RG-nsg'
```

### **2.1 Criando o Resource Groups**

```bash
az group create --name $ResourceGroup --location $Location
```

### **3.1 Criando a Virtual Network**

```bash
az network vnet create -g $ResourceGroup -n $vNetName --address-prefix $AddressSpace --subnet-name $SubnetName --subnet-prefix $SubnetIPRange
```

### **4.1 Criando o Network Security Group**

```bash
az network nsg create --resource-group $ResourceGroup --name $nsgName
az network nsg rule create --resource-group $ResourceGroup --nsg-name $nsgName --name allow-vm-access --access Allow --protocol Tcp --direction Inbound --priority 100 --source-address-prefix "*" --source-port-range "*" --destination-address-prefix "*" --destination-port-range 22
```
Perceba que neste comando já estamos criando uma regra de inbound com a priority 100 e destino a porta 22. Assim já teremos o acesso remoto habilitado na criação da VM.

### **5.1 Definindo as variáveis da máquina virtual**

```bash
vmName='lab02-vm0'
pubName='Canonical'
offerName='UbuntuServer'
skuName='18.04-LTS'
vmSize='Standard_DS2_v2'
pipName=''$vmName'-pip' 
nicName=''$vmName'-nic'
osDiskName=''$vmName'-OsDisk'
osDiskType='Standard_LRS'
urn=''$pubName':'$offerName':'$skuName''
```

### **6.1 Definindo as credinciais de administrador**

```bash
adminUsername='labuser'
adminPassword='Jn77a.lb1234'
```

### **7.1 Criando IP público e interface de rede NIC**

```bash
az network public-ip create --name $pipName --resource-group $ResourceGroup
az network nic create --resource-group $ResourceGroup --name $nicName --vnet-name $vNetName --subnet $SubnetName --public-ip-address $pipName --network-security-group $nsgName
```

### **8.1 Criando a máquina virtual**

```bash
az vm create --resource-group $ResourceGroup --name $vmName --image $urn:latest --os-disk-name $osDiskName --size $vmSize --storage-sku $osDiskType --admin-username $adminUsername --admin-password $adminPassword --nics $nicName
```

É isso, agora é só aguardar o provisionamento de sua VM.

A exemplo de nosso script em PowerShell, você também pode fazer o deploy de máquinas virtuais Windows, porém será necessário alguns ajustes.

Se você colocar os comandos lado a lado, irá perceber que existe uma similaridade entre os dois.

Minha dica é que você pratique um pouco de cada, sinta qual lhe agrada mais, e ai sim faça uma escolha para poder aprofundar um pouco mais os estudos.

Espero que gostem!

Forte Abraço
