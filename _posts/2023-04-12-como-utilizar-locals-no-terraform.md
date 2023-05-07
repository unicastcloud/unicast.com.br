---
layout: post
title: "Como utilizar Locals no Terraform"
author: asilva
date: 2023-04-12 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Sejam bem-vindos ao mais novo post da série sobre **Terraform**. Hoje, vamos falar sobre uma funcionalidade muito útil na hora de escrever código em **Terraform**: os **Locals**.

Os Locals são variáveis definidas dentro do arquivo .tf que podem ser utilizadas em qualquer parte do código. Eles permitem que você crie valores intermediários, que podem ser reutilizados várias vezes ao longo do arquivo, simplificando a leitura e manutenção do código.

Se você ainda não conhece os Locals no Terraform, continue lendo esse post que vamos explicar tudo detalhadamente.

### **1. O que são Locals no Terraform?**

Os **Locals** no **Terraform** são variáveis que são definidas dentro do arquivo `.tf`. Eles permitem que você crie valores intermediários, que podem ser reutilizados várias vezes ao longo do arquivo.

Os Locals são semelhantes às variáveis no código, mas com algumas diferenças. Eles são definidos dentro de um bloco `"locals"`, que pode estar presente em qualquer nível do código. Além disso, os Locals não são expostos fora do módulo, ou seja, eles só podem ser usados dentro do arquivo `.tf`.

### **2. Como usar Locals no Terraform?**

Para usar Locals no Terraform, você precisa definir uma variável dentro de um bloco "locals". Você pode fazer isso em qualquer parte do arquivo, desde que a variável seja definida antes de ser usada.

Abaixo está um exemplo de como definir um Local no Terraform:

````bash
locals {
  region = "eastus"
  vm_size = "Standard_DS2_v2"
}
````

Neste exemplo, criamos duas variáveis: region e `vm_size`. A primeira variável armazena a região na qual os recursos serão criados e a segunda armazena o tamanho da máquina virtual que será criada.

Para usar esses **Locals** no código, basta chamá-los pelo nome, como mostrado abaixo:

````bash
resource "azurerm_virtual_machine" "example" {
  name                  = "example-vm"
  location              = local.region
  resource_group_name   = azurerm_resource_group.example.name
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = local.vm_size
}
````

### **3. Exemplos práticos de como utilizar Locals no Terraform**

Para ajudar você a entender melhor como usar Locals no Terraform, vamos apresentar alguns exemplos práticos.

**Exemplo 1: Criando um recurso de armazenamento com Locals**

````bash
locals {
  resource_group_name = "my-resource-group"
  storage_account_name = "my-storage-account"
  location = "eastus"
}

resource "azurerm_resource_group" "example" {
  name     = local.resource_group_name
  location = local.location
}

resource "azurerm_storage_account" "example" {
  name                     = local.storage_account_name
  resource_group_name      = azurerm_resource_group.example.name
  location                 = local.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}
````

Neste exemplo, criamos um recurso de armazenamento com os **Locals**. O recurso de armazenamento é criado na região `"eastus"` e possui o nome `"my-storage-account"`.

**Exemplo 2: Criando uma máquina virtual com Locals**

````bash
locals {
  group_name      = "my-resource-group"
  location        = "eastus"
  vm_name         = "my-vm"
  vm_size         = "Standard_DS2_v2"
  admin_username  = "adminuser"
  admin_password  = "MyS3cr3tP@ssw0rd!"
}

resource "azurerm_resource_group" "example" {
  name     = local.group_name
  location = local.location
}

resource "azurerm_virtual_network" "example" {
  name                = "my-virtual-network"
  address_space       = ["10.0.0.0/16"]
  location            = local.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_subnet" "example" {
  name                 = "my-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefix       = "10.0.1.0/24"
}

resource "azurerm_network_interface" "example" {
  name                = "my-nic"
  location            = local.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "my-ip-config"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_virtual_machine" "example" {
  name                  = local.vm_name
  location              = local.location
  resource_group_name   = azurerm_resource_group.example.name
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = local.vm_size

  storage_os_disk {
    name              = "myosdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  os_profile {
    computer_name  = local.vm_name
    admin_username = local.admin_username
    admin_password = local.admin_password
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }
}
````

Neste exemplo, criamos uma máquina virtual com o uso de **Locals**. O recurso é criado com o nome `"my-vm"` na região `"eastus"` com o tamanho `"Standard_DS2_v2"`. Para permitir o acesso remoto, definimos o nome de usuário e senha na variável Local `"admin_username"` e `"admin_password"`. Também criamos uma rede virtual com um subnet e um adaptador de rede.

### **4. Conclusão**

Neste post, explicamos o que são os Locals no Terraform e como utilizá-los. Essa funcionalidade é muito útil para criar valores intermediários que podem ser reutilizados várias vezes no código.

Apresentamos também exemplos práticos de como usar Locals no Terraform para criar recursos no Microsoft Azure. Esperamos que esse post tenha ajudado você a entender melhor como usar os Locals em seus projetos de infraestrutura como código.

Fique atento para os próximos posts da série Terraform, onde abordaremos mais assuntos relacionados a essa ferramenta incrível!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
