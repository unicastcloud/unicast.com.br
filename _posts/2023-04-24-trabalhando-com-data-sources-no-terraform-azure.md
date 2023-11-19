---
layout: post
title: "Trabalhando com data sources no Terraform [Azure]"
author: asilva
date: 2023-04-24 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Estamos de volta com mais um post da série de artigos sobre **Terraform**. Se você ainda não conhece essa ferramenta, recomendamos que leia nossos artigos anteriores para entender melhor do que se trata.

Neste artigo, vamos falar sobre como trabalhar com **Data Sources** no **Terraform**. Se você já está familiarizado com o **Terraform**, deve saber que ele é uma ferramenta incrível para gerenciamento de infraestrutura como código. E uma das grandes vantagens do **Terraform** é a possibilidade de usar **Data Sources** para obter informações de recursos já existentes.

## **1. Mas o que é um Data Source?**

Um **Data Source** no **Terraform** é um tipo de recurso que permite obter informações de um recurso existente. Diferentemente dos recursos que criamos com o Terraform, os **Data Sources** não criam nada, eles apenas fornecem informações para serem usadas em outros recursos. Eles são muito úteis quando precisamos de informações sobre um recurso que já existe e que precisamos usar em outros recursos que estamos criando.

## **2. Por que utilizar Data Sources?**

A principal vantagem de usar **Data Sources** é que eles permitem que possamos reutilizar recursos existentes em nosso ambiente de infraestrutura. Quando precisamos criar novos recursos, é muito comum precisarmos de informações que já existem em outros recursos, como o ID de um recurso, o nome de uma rede, entre outras informações. Em vez de criar tudo do zero, podemos usar os **Data Sources** para obter essas informações e usá-las em nossos recursos.

## **3. Como utilizar Data Sources?**

A sintaxe para criar um **Data Source** no Terraform é bastante simples. Primeiro, precisamos definir o tipo de **Data Source** que queremos usar (por exemplo, `"azurerm_resource_group"`). Em seguida, precisamos especificar os argumentos necessários para obter as informações que queremos. 

Por exemplo, se queremos obter informações sobre um grupo de recursos no Microsoft Azure, precisamos especificar o nome do grupo de recursos.

Aqui está um exemplo de código que usa um **Data Source** para obter informações sobre um grupo de recursos no **Microsoft Azure**:

````bash
data "azurerm_resource_group" "example" {
  name = "my-resource-group"
}
````

Neste exemplo, estamos usando o **Data Source** `"azurerm_resource_group"` para obter informações sobre o grupo de recursos com o nome `"my-resource-group"`. Observe que definimos um novo bloco `"data"` e especificamos o tipo de **Data Source** que queremos usar. Em seguida, especificamos o nome do recurso e os argumentos necessários para obter as informações.

Agora que sabemos como criar um **Data Source**, podemos usá-lo em outros recursos. Aqui está um exemplo de como podemos usar as informações obtidas do **Data Source** para criar uma VM no Azure:

````bash
resource "azurerm_virtual_machine" "example" {
  name                  = "my-vm"
  location              = data.azurerm_resource_group.example.location
  resource_group_name   = data.azurerm_resource_group.example.name
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = "Standard_DS1_v2"
}
````

Neste exemplo, estamos criando uma VM no Azure. Observe que estamos usando as informações obtidas do **Data Source** para definir a localização da VM e o nome do grupo de recursos. Essas informações são essenciais para criar a VM no ambiente correto.

## **4. Exemplo de uso**

Agora que entendemos como os **Data Sources** funcionam no **Terraform**, vamos ver um exemplo real de como eles podem ser usados em um ambiente Azure.

Suponha que temos um ambiente com vários grupos de recursos e VMs criadas no Azure. Agora, queremos criar uma nova VM e usar a mesma rede virtual (VNet) que já foi criada em outro grupo de recursos. Para fazer isso, podemos usar um **Data Source** para obter informações sobre a VNet e usá-las na criação da nova VM.

Primeiro, precisamos criar o **Data Source** para obter informações sobre a VNet. Aqui está o código para isso:

````bash
data "azurerm_virtual_network" "example" {
  name                = "my-vnet"
  resource_group_name = "existing-resource-group"
}
````

Neste exemplo, estamos usando o Data Source `"azurerm_virtual_network"` para obter informações sobre a VNet com o nome `"my-vnet"` que está no grupo de recursos `"existing-resource-group"`. Observe que estamos especificando os mesmos argumentos que usaríamos se estivéssemos criando a VNet como um novo recurso.

Agora que temos as informações da VNet, podemos usá-las na criação da nova VM. Aqui está o código para isso:

````bash
resource "azurerm_virtual_machine" "example" {
  name                  = "my-new-vm"
  location              = "eastus"
  resource_group_name   = "new-resource-group"
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "my-os-disk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Premium_LRS"
  }

  os_profile {
    computer_name  = "my-new-vm"
    admin_username = "adminuser"
    admin_password = "P@ssw0rd1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

  depends_on = [azurerm_network_interface.example]
}

resource "azurerm_network_interface" "example" {
  name                = "my-nic"
  location            = "eastus"
  resource_group_name = "new-resource-group"

  ip_configuration {
    name                          = "my-nic-ipconfig"
    subnet_id                     = data.azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}

data "azurerm_subnet" "example" {
  name                 = "my-subnet"
  virtual_network_name = data.azurerm_virtual_network.example.name
  resource_group_name  = data.azurerm_virtual_network.example.resource_group_name
}
````

Neste exemplo, estamos criando uma nova **VM** no Azure e usando a mesma **VNet** de outro grupo de recursos. Observe que estamos usando o **Data Source** que criamos anteriormente para obter informações sobre a **VNet**. Estamos especificando o nome e o grupo de recursos da nova VM, além de outras informações como tamanho da VM, imagem do sistema operacional, nome da interface de rede, entre outras.

## **5. Conclusão**

Os **Data Sources** são uma poderosa ferramenta no Terraform que nos permitem reutilizar recursos existentes em nosso ambiente de infraestrutura. Eles nos permitem criar recursos de maneira mais eficiente, evitando duplicação de esforços e minimizando a possibilidade de erros.

Ao usar **Data Sources**, podemos obter informações sobre recursos existentes, como redes virtuais, grupos de recursos e outros recursos, e usá-las na criação de novos recursos. Isso pode ser especialmente útil em ambientes complexos, onde há muitos recursos interdependentes.

No Azure, o Terraform oferece suporte a uma ampla variedade de **Data Sources**, incluindo Virtual Machines, Virtual Networks, Subnets, Network Security Groups, Key Vaults e muito mais.

Esperamos que este post tenha sido útil para você entender melhor como os **Data Sources** funcionam no Terraform e como usá-los em seu ambiente Azure.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!

