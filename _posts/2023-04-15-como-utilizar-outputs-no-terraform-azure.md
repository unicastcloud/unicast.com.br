---
layout: post
title: "Como utilizar outputs no Terraform [Azure]"
author: asilva
date: 2023-04-15 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Este é mais um post da série de artigos que estamos escrevendo sobre o **Terraform**, uma ferramenta de código aberto que permite criar, modificar e gerenciar infraestrutura de nuvem de forma declarativa.

Neste artigo, vamos falar sobre **outputs** no **Terraform**, uma funcionalidade que permite que você obtenha e imprima valores de recursos criados em sua infraestrutura de nuvem. Vamos ver como declarar **outputs** no Terraform, quais são os benefícios de usá-los e como usá-los na mensagem de impressão no terminal.

## **1. O que são outputs no Terraform?**

**Outputs** no **Terraform** são valores que você pode obter de recursos criados em sua infraestrutura de nuvem e usá-los em outros recursos ou em mensagens de impressão no terminal. Eles são definidos na seção `output` do arquivo de configuração do **Terraform** e podem ser acessados após a criação dos recursos.

Por exemplo, suponha que você criou uma instância do **Microsoft Azure** usando o **Terraform** e agora deseja acessar o endereço IP público dessa instância em um recurso de segurança. Você pode definir um **output** para o endereço IP público e usá-lo em outro recurso.

## **2. Como declarar outputs no Terraform?**

Para declarar um **output** no **Terraform**, você precisa definir o nome do **output** e o valor que ele deve conter. Aqui está um exemplo de declaração de **output** para um endereço IP público de uma instância do **Microsoft Azure**:

````bash
output "public_ip_address" {
  value = azurerm_public_ip.example.ip_address
}
````

Neste exemplo, definimos um output chamado `public_ip_address` que obtém o endereço IP público de uma instância do Microsoft Azure chamada `azurerm_public_ip.example`.

Você também pode definir outputs compostos, que contêm vários valores. Aqui está um exemplo de declaração de output composto:

````bash
output "vm" {
  value = {
    name = azurerm_virtual_machine.example.name
    id   = azurerm_virtual_machine.example.id
  }
}
````

Neste exemplo, definimos um output chamado `vm` que contém o nome e o ID de uma instância do Microsoft Azure chamada `azurerm_virtual_machine.example`.

## **3. Quais são os benefícios dos outputs do Terraform?**

Usar outputs no Terraform tem vários benefícios. Aqui estão alguns dos principais:

- **Organização**: Usando outputs, você pode separar a definição dos recursos da obtenção dos valores desses recursos, tornando sua configuração mais organizada e fácil de entender.
- **Reutilização**: Ao definir um output para um valor, você pode reutilizá-lo em outros recursos, evitando a repetição de valores e economizando tempo.
Comunicação: Outputs também podem ser usados para imprimir mensagens no terminal, permitindo que você comunique informações importantes sobre a infraestrutura criada.

## **4. Como utilizar outputs no terminal?**

Você pode usar a mensagem de impressão no terminal para exibir informações sobre a infraestrutura criada, como o endereço IP público de uma instância ou o nome de um recurso. Para fazer isso, basta usar a interpolação de string do Terraform e o valor do output que você deseja imprimir. 

Aqui está um exemplo de como imprimir o endereço IP público de uma instância do Microsoft Azure:

````bash
output "public_ip_address" {
  value = azurerm_public_ip.example.ip_address
}

# Imprime o endereço IP público da instância
output "print_public_ip_address" {
  value = "O endereço IP público da instância é ${azurerm_public_ip.example.ip_address}"
}
````

Neste exemplo, definimos um output chamado `public_ip_address` que obtém o endereço IP público de uma instância do Microsoft Azure. Em seguida, definimos um segundo output chamado `print_public_ip_address` que usa a interpolação de string para imprimir o endereço IP público no terminal.

Para ver o valor do output no terminal, basta executar o seguinte comando:

````bash
terraform output print_public_ip_address
}
````

Isso exibirá a seguinte mensagem:

````bash
O endereço IP público da instância é xxx.xxx.xxx.xxx.
````

## **5. Usando um output em outro recurso**

Neste exemplo, vamos criar uma instância do Microsoft Azure e um NSG que usa o endereço IP público da instância. Vamos usar um **output** para obter o endereço IP público da instância e usá-lo no NSG.

````bash
# Cria uma instância do Microsoft Azure
resource "azurerm_virtual_machine" "example" {
  name                  = "example-vm"
  location              = "eastus"
  resource_group_name   = "example-resource-group"
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  os_profile {
    computer_name  = "example-vm"
    admin_username = "exampleuser"
    admin_password = "examplepassword"
  }

  os_disk {
    name              = "example-os-disk"
    caching           = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
}

# Define um output para o endereço IP público da instância
output "public_ip_address" {
  value = azurerm_public_ip.example.ip_address
}

# Cria um recurso de segurança que usa o endereço IP público da instância
resource "example_security_group" "example" {
  name                  = "example-security-group"
  location              = "eastus"
  resource_group_name   = "example-resource-group"
  security_rules {
    name                       = "allow_ssh"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "22"
    source_address_prefix      = "Internet"
    destination_address_prefix = azurerm_public_ip.example.ip_address
  }
}
````

Neste exemplo, criamos uma instância do Microsoft Azure e definimos um **output** chamado `public_ip_address` que obtém o endereço IP público da instância. Em seguida, criamos um NSG chamado `example_security_group` que usa o endereço IP público da instância em sua configuração. Observe que usamos a interpolação de string para inserir o valor do output no NSG.

## **6. Conclusão**

Os **outputs** no **Terraform** são uma ferramenta útil para extrair informações de recursos criados e reutilizá-las em outros recursos ou para exibi-las no terminal. Eles são fáceis de declarar e podem ser usados em vários cenários diferentes. 

Esperamos que este artigo tenha sido útil para ajudá-lo a entender como usar **outputs** no **Terraform** e como eles podem beneficiar seus projetos de infraestrutura como código. 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
