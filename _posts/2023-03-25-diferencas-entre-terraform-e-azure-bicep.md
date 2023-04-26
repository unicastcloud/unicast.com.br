---
layout: post
title: "Diferenças entre Terraform e Azure Bicep"
author: asilva
date: 2023-03-25 09:20 -0300
categories: [DevOps, Automação de TI]
tags: [devops, terraform, hashicorp, bicep, arm, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Provavelmente você já ouviu falar de **Terraform** e **Azure Bicep**. Ambas as ferramentas são usadas para provisionar recursos na plataforma **Azure**, mas cada uma tem suas próprias características e benefícios. 

Neste artigo, vamos explorar as diferenças entre **Terraform** e **Azure Bicep** e ajudá-lo a escolher a melhor opção para suas necessidades.

Antes de mergulharmos nas diferenças entre Terraform e Azure Bicep, é importante entender como cada uma das ferramentas funciona. 

### **1. Terraform**

Terraform é uma ferramenta de infraestrutura como código (IaC) que permite definir, criar e gerenciar infraestrutura em uma variedade de plataformas, incluindo Azure. Ele usa uma linguagem declarativa para descrever a infraestrutura desejada e é executado em uma variedade de ambientes, incluindo desktops, servidores e em nuvem.

Aqui está um exemplo de como provisionar uma máquina virtual no Azure usando Terraform:

```bash
resource "azurerm_virtual_machine" "example" {
  name                  = "example-vm"
  location              = "westus2"
  resource_group_name   = "example-resource-group"
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = "Standard_DS1_v2"

  storage_os_disk {
    name              = "example-os-disk"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  os_profile {
    computer_name  = "examplevm"
    admin_username = "adminuser"
    admin_password = "password"
  }

  tags = {
    environment = "test"
  }
}
```

### **2. Azure Bicep**

**Azure Bicep** é uma linguagem de **DSL** para provisionamento de recursos no Azure. Ela oferece uma sintaxe mais concisa e intuitiva do que o **Azure Resource Manager (ARM) templates**, tornando mais fácil criar e gerenciar recursos no Azure. Além disso, Azure Bicep suporta herança, modularidade e validação de tipo, tornando mais fácil criar e gerenciar recursos complexos.

```
param location string = 'eastus'
param vmName string = 'myVM'
param adminUsername string = 'myUsername'
param adminPassword securestring = 'myPassword'

resource myVnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: 'myVnet'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'default'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
    ]
  }
}

resource myNIC 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: '${vmName}-nic'
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig'
        properties: {
          subnet: {
            id: myVnet.subnets[0].id
          }
          privateIPAllocationMethod: 'Dynamic'
        }
      }
    ]
  }
}

resource myVM 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  dependsOn: [
    myNIC
  ]
  properties: {
    hardwareProfile: {
      vmSize: 'Standard_DS1_v2'
    }
    storageProfile: {
      imageReference: {
        publisher: 'MicrosoftWindowsServer'
        offer: 'WindowsServer'
        sku: '2016-Datacenter'
        version: 'latest'
      }
      osDisk: {
        name: '${vmName}-osdisk'
        caching: 'ReadWrite'
        createOption: 'FromImage'
        diskSizeGB: 128
      }
    }
    osProfile: {
      computerName: vmName
      adminUsername: adminUsername
      adminPassword: adminPassword
    }
    networkProfile: {
      networkInterfaces: [
        {
          id: myNIC.id
        }
      ]
    }
  }
}
```

### **3. Comparando as duas ferramentas**

Ambas as ferramentas, **Terraform** e **Azure Bicep**, têm suas vantagens e desvantagens e são ótimas opções para provisionamento de infraestrutura no Azure. Embora o **Azure Bicep** seja uma ferramenta relativamente nova, ele já se mostrou uma alternativa sólida ao **Terraform**. 

- O **Terraform** tem a vantagem de ser uma ferramenta mais amplamente usada e suporta uma ampla variedade de provedores em nuvem, tornando-o uma opção ideal para organizações com ambientes em nuvem heterogêneos. Sua linguagem declarativa é fácil de entender e pode ser usada para provisionar uma ampla variedade de recursos. No entanto, pode ser mais difícil de aprender e usar do que Azure Bicep, e sua sintaxe pode ser mais verbosa em comparação.

- O **Azure Bicep** é uma opção melhor para organizações que usam principalmente o Azure, já que é especificamente projetado para o Azure. Sua sintaxe mais concisa e intuitiva torna mais fácil criar e gerenciar recursos no Azure, e suporta herança, modularidade e validação de tipo, tornando mais fácil criar e gerenciar recursos complexos. No entanto, sua funcionalidade pode ser limitada em comparação com Terraform, e sua adoção pode ser mais difícil para organizações com pouca experiência em IaC.

| **Características**                                   | **Terraform**     | **Azure Bicep**         |
|-------------------------------------------------------|:-----------------:|:-----------------------:|
| **Linguagem de Definição de <br /> Infraestrutura**   | Hashicorp Configuration <br /> Language (HCL) | Própria linguagem com sintaxe <br /> semelhante ao JSON | 
| **Suporte a outros provedores**                       | Sim               | Não                     | 
| **Tipagem**                                           | Fraca             | Forte                   | 
| **Modularidade**                                      | Sim               | Sim                     | 
| **Documentação**                                      | Boa               | Boa                     | 
| **Comunidade**                                        | Grande e ativa    | Em crescimento          | 
| **Suporte a múltiplos ambientes**                     | Sim               | Sim                     | 
| **Curva de Aprendizado**                              | Média             | Baixa                   | 
| **Experiência do usuário**                            | Boa               | Boa                     | 
| **Integração com o Azure**                            | Bom	              | Excelente               | 
| **Suporte a templates**                               | Sim	              | Sim	                    | 
| **Suporte a controle de versão**                      | Sim	              | Sim	                    | 
| **Preço**                                             | Grátis            | Grátis                  | 

No entanto, é importante lembrar que o mercado ainda é mais aderente a utilizar o **Terraform** devido à sua popularidade e tempo de existência.

Apesar de ser uma ferramenta mais recente, o **Azure Bicep** tem uma curva de aprendizado mais baixa e uma sintaxe semelhante ao **JSON**, o que facilita a escrita de código para desenvolvedores que já possuem familiaridade com a linguagem. Além disso, a integração com o Azure é excelente e permite uma experiência de usuário mais fluída. 

Por outro lado, o **Terraform** tem uma grande comunidade e suporte a vários provedores além do Azure, o que pode ser um fator importante para projetos que utilizam serviços de múltiplos provedores.

Portanto, ao escolher entre as duas ferramentas, é importante levar em consideração as necessidades específicas do seu projeto e a familiaridade da equipe com cada uma delas. Ambas são boas opções para o provisionamento de infraestrutura no Azure e, independentemente da escolha, é possível obter bons resultados.

É isso galera, espero que gostem!

Forte Abraço!
