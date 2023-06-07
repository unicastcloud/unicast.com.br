---
layout: post
title: "Trabalhando com Workspaces no Terraform [Azure]"
author: asilva
date: 2023-05-10 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Sejam bem-vindos a mais um post da nossa série de artigos sobre **Terraform**. Hoje vamos mergulhar no mundo dos **workspaces** e descobrir como eles podem tornar o gerenciamento da sua infraestrutura ainda mais prático e eficiente. 

Então, pegue sua xícara de café e vamos lá!

### **1. O que é Terraform Workspaces?**

Se você já trabalha com **Terraform**, provavelmente já se deparou com a necessidade de gerenciar diferentes estados de configuração para ambientes distintos. É aí que entram os **workspaces**. O **Terraform** **Workspaces** é um recurso que permite criar e gerenciar múltiplos ambientes a partir de uma única configuração.

Em termos simples, se você está executando uma configuração de infraestrutura em um ambiente de desenvolvimento, essa mesma infraestrutura pode ser executada em um ambiente de produção, por exemplo. Com os **workspaces**, é possível isolar os estados de configuração para cada ambiente, evitando conflitos e facilitando a manutenção.

### **2. Como criar um novo Workspace?**

A criação de um novo **workspace** no Terraform é bastante simples. Basta executar o comando `terraform workspace new <nome-do-workspace>`. Por exemplo, se você deseja criar um **workspace** para o ambiente de desenvolvimento, poderia utilizar `terraform workspace new dev`. O Terraform criará um novo espaço de trabalho com o nome especificado e você estará pronto para configurar sua infraestrutura para esse ambiente.

### **3. Como criar vários workspaces no Terraform?**

Criar vários **workspaces** no Terraform é uma tarefa muito útil quando se trata de gerenciar diferentes ambientes. Como mencionado anteriormente, você pode criar um novo workspace utilizando o comando `terraform workspace new <nome-do-workspace>`. 

É possível criar quantos workspaces forem necessários, seja para ambientes de desenvolvimento, teste ou produção.

### **4. Como listar todos os workspaces no Terraform?**

Para listar todos os **workspaces** disponíveis no Terraform, basta executar o comando `terraform workspace list`. O Terraform retornará uma lista com todos os workspaces criados até o momento. Essa funcionalidade é útil para verificar quais ambientes estão configurados e facilitar a navegação entre eles.

### **5. Como alternar entre workspaces no Terraform?**

Agora que você possui vários **workspaces** criados, pode alternar entre eles facilmente. Utilize o comando `terraform workspace select <nome-do-workspace>` para selecionar o workspace desejado. 

Por exemplo, se você deseja alternar para o workspace de produção, execute `terraform workspace select prod`. O Terraform fará a transição para o workspace escolhido e você poderá configurar sua infraestrutura especificamente para esse ambiente.

### **6. Como excluir workspaces no Terraform?**

Embora seja possível criar quantos **workspaces** forem necessários, é importante também saber como excluir um **workspace** quando ele não for mais utilizado. Para excluir um workspace no Terraform, utilize o comando `terraform workspace delete <nome-do-workspace>`. Tenha cuidado ao utilizar essa funcionalidade, pois a exclusão de um workspace também excluirá o estado de configuração associado a ele. 

>Portanto, certifique-se de fazer backup dos dados importantes antes de prosseguir.
{: .prompt-warning }

### **7. Como criar uma VM usando um workspace de DEV e PROD?**

Vamos agora mergulhar em um exemplo prático de como criar uma máquina virtual (VM) usando **workspaces** de **desenvolvimento** (DEV) e **produção** (PROD) no Terraform, vamos focar no uso do provedor Azure.

**Configurando os workspaces**

Primeiro, vamos criar os **workspaces** para os ambientes de **DEV** e **PROD**. Abra um terminal e execute os seguintes comandos:

````bash
terraform workspace new dev
terraform workspace new prod
````

Esses comandos criarão os **workspaces** `"dev"` e `"prod"` no Terraform.

**Definindo as credenciais do Azure**

Antes de prosseguir, certifique-se de ter configurado corretamente as credenciais do Azure no Terraform. Isso inclui definir as variáveis de ambiente `ARM_SUBSCRIPTION_ID`, `ARM_CLIENT_ID`, `ARM_CLIENT_SECRET` e `ARM_TENANT_ID`. 

**Escrevendo a configuração do Terraform**

Agora, vamos criar um arquivo `main.tf` para definir a configuração da VM no Azure para cada ambiente.

````bash
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resource-group"
  location = "West Europe"
}

resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  resource_group_name = azurerm_resource_group.example.name

  security_rule {
    name                       = "allow-http"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "80"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }
}

resource "azurerm_public_ip" "example" {
  name                = "example-pip"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  allocation_method   = "Static"
}

resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "example-config"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.example.id
  }
}

resource "azurerm_virtual_machine" "example" {
  name                = "example-vm"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  network_interface_ids = [
    azurerm_network_interface.example.id
  ]
  vm_size              = "Standard_DS1_v2"
  delete_os_disk_on_termination = true

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "example-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
    disk_size_gb      = 30
  }

  os_profile {
    computer_name  = "examplevm"
    admin_username = "adminuser"

    admin_password = "P@ssw0rd1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }
}
````

**Aplicando a configuração**

Agora, vamos aplicar a configuração no ambiente correto. Para isso, selecione o workspace desejado com o comando `terraform workspace select <nome-do-workspace>`. 

Por exemplo:

````bash
terraform workspace select dev
````

Em seguida, execute os comandos a seguir para inicializar o diretório de trabalho e aplicar a configuração:

````bash
terraform init
terraform apply
````

Repita os mesmos passos para o workspace de produção **(PROD)**.

### **8. Conclusão**

Os workspaces no Terraform são uma ferramenta poderosa para o gerenciamento de diferentes ambientes de infraestrutura. Com eles, você pode manter estados de configuração isolados e simplificar a implementação e manutenção de sua infraestrutura. 

Utilize os comandos apresentados neste post para criar, listar, alternar e excluir workspaces, e aproveite os benefícios de uma abordagem mais organizada e flexível em seus projetos.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
