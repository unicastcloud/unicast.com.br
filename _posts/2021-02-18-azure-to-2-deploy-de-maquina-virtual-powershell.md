---
layout: post
title: "[Azure-To] #2 Deploy de Máquina Virtual [PowerShell]"
author: asilva
date: 2021-02-18 06:54:00 -0500
categories: [Cloud, Azure]
tags: [maquinavirtual, azure, microsoft, infraestrutura, poweshell]
---

Fala galera! Seis tão baum?

Seguindo com nossa série Azure-To no post anterior você aprendeu como criar uma máquina virtual via Portal Azure. Agora, vamos fazer o mesmo deploy utilizando o Azure PowerShell.

A princípio pode parecer uma pouco complicado, especialmente se você está começando agora e ainda não é familiarizado com o PowerShell, mas se você pretente trabalhar com Azure no dia a dia, você realmente precisa começar a usar o PowerShell.

## **Objetivo**

Implantar uma VM executando Windows Server 2016 Datacenter via PowerShell.

## **Para o deploy de nossa VM vamos:**

* Criar um resource group
* Criar uma virtual network e uma subnet
* Criar um network security group e as regras para acesso a nossa VM.
* Criar uma virtual machine com o sistema operacional Windows Server 2016

Vou apresentar a vocês os comandos em forma de script, eu prefiro trabalhar desta maneira, pois posso guardar as variáveis e dependendo da minha necessidade eu só modifico os parâmetros, não é necessário alterar os comandos e isso automatiza e agilza demais meu dia a dia.

Mas claro, nada lhe impede de utilizar os comandos de forma direta e sem variáveis.

Para o deploy você pode utilizar o Cloud Shell direto do Portal da azure ou diretamente da sua máquina utilizando o Power Shell com os modulos da Azure instalado.

Vamos lá!

## **1.1 Definindo as variáveis de rede**

```powershell
$ResourceGroup  = "lab01-RG"
$Location       = "EastUS"
$vNetName       = "lab01-RG-vnet"
$AddressSpace   = "10.0.0.0/16"
$SubnetIPRange  = "10.0.0.0/24" 
$SubnetName     = "subnet0"
$nsgName        = "lab01-RG-nsg"
```

## **2.1 Criando o Resource Groups**

```powershell
New-AzResourceGroup -Name $ResourceGroup -Location $Location
```

## **3.1 Criando a Virtual Network**

```powershell
$vNetwork = New-AzVirtualNetwork -ResourceGroupName $ResourceGroup -Name $vNetName -AddressPrefix $AddressSpace -Location $location
```

## **3.2 Criando a Subnet**

```powershell
Add-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNetwork -AddressPrefix $SubnetIPRange
```

## **3.3 Setando as configurações**

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vNetwork
```

## **4.1 Criando o Network Security Group**

```powershell
$nsgRuleVMAccess = New-AzNetworkSecurityRuleConfig -Name 'allow-vm-access' -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $location -Name $nsgName -SecurityRules $nsgRuleVMAccess
```
Perceba que neste comando já estamos criando uma regra de inbound com a priority 100 e destino a porta 3389. Assim já teremos o acesso remoto habilitado na criação da VM.

## **5.1 Definindo as variáveis da máquina virtual**

```powershell
$vNet   = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroup -Name $vNetName
$Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$nsg    = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NsgName
$vmName = "lab01-vm0"
$pubName    = "MicrosoftWindowsServer"
$offerName  = "WindowsServer"
$skuName    = "2016-Datacenter"
$vmSize     = "Standard_DS2_v2"
$pipName    = "$vmName-pip" 
$nicName    = "$vmName-nic"
$osDiskName = "$vmName-OsDisk"
$osDiskType = "Standard_LRS"
```

## **6.1 Definindo as credinciais de administrador**

```powershell
$adminUsername = 'labuser'
$adminPassword = 'Jn77a.lb1234'
$adminCreds    = New-Object PSCredential $adminUsername, ($adminPassword | ConvertTo-SecureString -AsPlainText -Force)
```

## **7.1 Criando IP público e interface de rede NIC**

```powershell
$pip = New-AzPublicIpAddress -Name $pipName -ResourceGroupName $ResourceGroup -Location $location -AllocationMethod Static 
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Subnet.Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## **8.1 Adicionando as configurações da máquina virtual**

```powershell
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize
Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

## **9.1 Setando os parâmetros do sistema operacional** 

```powershell
Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $adminCredsd
```

## **10.1 Setando a imagem utilizada na máquina virtual**

```powershell
Set-AzVMSourceImage -VM $vmConfig -PublisherName $pubName -Offer $offerName -Skus $skuName -Version 'latest'
```

## **11.1 Setando as configurações de disco**

```powershell
Set-AzVMOSDisk -VM $vmConfig -Name $osDiskName -StorageAccountType $osDiskType -CreateOption fromImage
```

## **12.1 Desabilitando o diagnóstico de boot**

```powershell
Set-AzVMBootDiagnostic -VM $vmConfig -Disable
```

## **13.1 Criando a máquina virtual**

```powershell
New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $vmConfig
```

É isso, agora é só aguardar o provisionamento de sua VM.

Você pode juntar todos os comandos em um único arquivo e ir alterando as variáveis de acordo com suas necessidades.

Você também consegue utilizar este mesmo script para o deploy de máquinas virtuais Linux, porém é necessário alguns ajustes.

Caso seja de interesse, postem nos comentários que disponibilizo um script já modificado.

Espero que gostem!

Forte Abraço
