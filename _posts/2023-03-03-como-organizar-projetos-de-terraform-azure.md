---
layout: post
title: "Como organizar projetos de Terraform [Azure]"
author: asilva
date: 2023-03-03 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Hoje vamos falar de **Terraform**!

Primeiramente, vamos fazer uma breve abordagem do que é o **Terraform**.

O **Terraform** é uma ferramenta de **infraestrutura como código (IaC)** que permite definir, criar e gerenciar infraestruturas de forma automatizada e programática. Com o Terraform, é possível descrever a infraestrutura em um arquivo de configuração declarativo e, em seguida, usar esse arquivo para provisionar e gerenciar os recursos necessários em nuvens públicas, privadas ou em ambientes locais. O Terraform é capaz de gerenciar recursos de diferentes provedores de nuvem, permitindo que equipes de desenvolvimento e operações trabalhem juntas para criar e gerenciar infraestruturas de forma mais eficiente e escalável. Além disso, o Terraform oferece recursos para versionamento, controle de acesso, validação e visualização de alterações, tornando mais fácil a colaboração e a manutenção de infraestruturas complexas.

Um dos aspectos mais importantes da utilização do Terraform é a organização do código.

Organizar projetos em Terraform é uma prática crucial para garantir que sua infraestrutura esteja bem gerenciada, escalável e segura. Uma boa organização permitirá que você mantenha facilmente seu código e faça alterações rapidamente, sem comprometer a integridade de sua infraestrutura.

Existem diferentes maneiras de estruturar sua configuração de Terraform, e a escolha depende das necessidades e complexidade do seu projeto. Neste artigo, exploraremos algumas abordagens comuns de organização de projetos Terraform no Microsoft Azure.

### **1. Monolítica**

Esta é a forma mais simples e básica de organizar um projeto Terraform. Todos os recursos são declarados em um único arquivo main.tf. Embora seja fácil de criar e entender, esse método pode se tornar complicado à medida que o projeto cresce e se torna mais complexo. Além disso, se houver uma mudança em um recurso específico, todo o arquivo principal terá que ser atualizado.

**Exemplo de estrutura de código:**

```
📦01_terraform_monolithic
 ┣ 📜.gitignore
 ┣ 📜README.md
 ┗ 📜main.tf
```

**Exemplo de recurso:**

```bash
# Azure Provider source and version being used
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=2.83.0"
    }
  }
}

# Configure the Microsoft Azure Provider and identity
provider "azurerm" {
  features {}
}

# Configure Resources

# Virtual Machine
variable "prefix" {
  default = "unicastcloud"
}

resource "azurerm_resource_group" "rg" {
  name     = "${var.prefix}-resources"
  location = "eastus"
}

resource "azurerm_virtual_network" "main" {
  name                = "${var.prefix}-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "internal" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.2.0/24"]
}

resource "azurerm_network_interface" "main" {
  name                = "${var.prefix}-nic"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "testconfiguration1"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_virtual_machine" "main" {
  name                  = "${var.prefix}-vm"
  location              = azurerm_resource_group.rg.location
  resource_group_name   = azurerm_resource_group.rg.name
  network_interface_ids = [azurerm_network_interface.main.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }
  storage_os_disk {
    name              = "myosdisk1"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }
  os_profile {
    computer_name  = "hostname"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }
  os_profile_linux_config {
    disable_password_authentication = false
  }
  tags = {
    ProjectName  = "demo-unicast"
    Env          = "Azure"
    Owner        = "Unicast Cloud"
    BusinessUnit = "Unicast Cloud"
    ServiceClass = "Gold"
  }
}
```

Mais detalhes no repositório: <a href="https://github.com/asilvajunior/devops-case-studies/tree/main/01_terraform_monolithic" target="_blank">Terraform on Azure Reference Monolithic Sample</a> 

### **2. Multi Monolítica:**

Nesta abordagem, cada ambiente é separado em um diretório diferente, cada um com seu próprio arquivo main.tf. Isso torna mais fácil gerenciar recursos específicos do ambiente, pois há um arquivo separado para cada ambiente. No entanto, isso pode levar a uma duplicação desnecessária de código, pois recursos comuns são duplicados em cada arquivo.

**Exemplo de estrutura de código:**

```
📦02_terraform_multi_monolithic
 ┣ 📂dev
 ┃ ┗ 📜main.tf
 ┣ 📂prod
 ┃ ┗ 📜main.tf
 ┣ 📜.gitignore
 ┗ 📜README.md
```

**Exemplo de recurso:**

**DEV**

```bash
# Azure Provider source and version being used
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=2.83.0"
    }
  }
}

# Configure the Microsoft Azure Provider and identity
provider "azurerm" {
  features {}
}

# Configure Resources

# Virtual Machine
variable "prefix" {
  default = "unicastcloud"
}

resource "azurerm_resource_group" "rg" {
  name     = "${var.prefix}-resources-dev"
  location = "eastus"
}

resource "azurerm_virtual_network" "main" {
  name                = "${var.prefix}-network-dev"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "internal" {
  name                 = "internal-dev"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.2.0/24"]
}

resource "azurerm_network_interface" "main" {
  name                = "${var.prefix}-nic-dev"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "testconfiguration1"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_virtual_machine" "main" {
  name                  = "${var.prefix}-vm-dev"
  location              = azurerm_resource_group.rg.location
  resource_group_name   = azurerm_resource_group.rg.name
  network_interface_ids = [azurerm_network_interface.main.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }
  storage_os_disk {
    name              = "myosdisk1"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }
  os_profile {
    computer_name  = "hostname"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }
  os_profile_linux_config {
    disable_password_authentication = false
  }
  tags = {
    ProjectName  = "demo-unicast"
    Env          = "Azure"
    Owner        = "Unicast Cloud"
    BusinessUnit = "Unicast Cloud"
    ServiceClass = "Gold"
    Env          = "dev"
  }
}
```

**PROD**

```bash
# Azure Provider source and version being used
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=2.83.0"
    }
  }
}

# Configure the Microsoft Azure Provider and identity
provider "azurerm" {
  features {}
}

# Configure Resources

# Virtual Machine
variable "prefix" {
  default = "unicastcloud"
}

resource "azurerm_resource_group" "rg" {
  name     = "${var.prefix}-resources-prod"
  location = "eastus"
}

resource "azurerm_virtual_network" "main" {
  name                = "${var.prefix}-network-prod"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "internal" {
  name                 = "internal-prod"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.2.0/24"]
}

resource "azurerm_network_interface" "main" {
  name                = "${var.prefix}-nic-prod"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "testconfiguration1"
    subnet_id                     = azurerm_subnet.internal.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_virtual_machine" "main" {
  name                  = "${var.prefix}-vm-prod"
  location              = azurerm_resource_group.rg.location
  resource_group_name   = azurerm_resource_group.rg.name
  network_interface_ids = [azurerm_network_interface.main.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }
  storage_os_disk {
    name              = "myosdisk1"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }
  os_profile {
    computer_name  = "hostname"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }
  os_profile_linux_config {
    disable_password_authentication = false
  }
  tags = {
    ProjectName  = "demo-unicast"
    Env          = "Azure"
    Owner        = "Unicast Cloud"
    BusinessUnit = "Unicast Cloud"
    ServiceClass = "Gold"
    Env          = "prod"
  }
}
```

Mais detalhes no repositório: <a href="https://github.com/asilvajunior/devops-case-studies/tree/main/02_terraform_multi_monolithic" target="_blank">Terraform on Azure Reference Multi Monolithic</a> 

### **3. Modular:**

O uso de módulos permite a reutilização de código em vários projetos Terraform. Em vez de definir todos os recursos em um único arquivo, cada recurso é colocado em seu próprio módulo, com um arquivo separado para cada recurso. Isso torna mais fácil gerenciar e reutilizar o código, pois os módulos podem ser facilmente importados em outros projetos.

**Exemplo de estrutura de código:**

```
📦03_terraform_modular
 ┣ 📂modules
 ┃ ┣ 📂dns
 ┃ ┃ ┗ 📜main.tf
 ┃ ┗ 📂vm
 ┃ ┃ ┗ 📜main.tf
 ┣ 📜.DS_Store
 ┣ 📜.gitignore
 ┣ 📜.terraform-docs.yml
 ┣ 📜Makefile
 ┣ 📜README.md
 ┗ 📜main.tf
```

**Exemplo de recurso:**

```bash
# Azure Provider source and version being used
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=2.83.0"
    }
  }
}

# Configure the Microsoft Azure Provider and identity
provider "azurerm" {
  features {}
}

# Modules

# Virtual Machine
module "vm" {
  source = "./modules/vm"
}

module "dns" {
  source = "./modules/dns"
}
```

Mais detalhes no repositório: <a href="https://github.com/asilvajunior/devops-case-studies/tree/main/03_terraform_modular" target="_blank">Terraform on Azure Reference Modular</a> 

### **4. Multi Ambiente:**

Esta abordagem envolve o uso de workspaces e arquivos tfvars separados para cada ambiente. Os workspaces permitem que você execute o mesmo código em vários ambientes, enquanto os arquivos tfvars permitem que você configure as variáveis específicas de cada ambiente. Isso torna mais fácil gerenciar recursos para diferentes ambientes e permite que você execute o mesmo código em diferentes ambientes.

**Exemplo de estrutura de código:**

```
📦04_terraform_modular_multi_env
 ┣ 📂.terraform
 ┃ ┗ 📂modules
 ┃ ┃ ┗ 📜modules.json
 ┣ 📂env
 ┃ ┣ 📂dev
 ┃ ┃ ┗ 📜dev.tfvars
 ┃ ┣ 📂prd
 ┃ ┃ ┗ 📜prod.tfvars
 ┃ ┗ 📂qa
 ┃ ┃ ┗ 📜qa.tfvars
 ┣ 📂modules
 ┃ ┣ 📂dns
 ┃ ┃ ┣ 📜README.md
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┃ ┗ 📂vm
 ┃ ┃ ┣ 📜README.md
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┣ 📜.DS_Store
 ┣ 📜.gitignore
 ┣ 📜README.md
 ┣ 📜main.tf
 ┣ 📜providers.tf
 ┣ 📜variables.tf
 ┗ 📜versions.tf
```

**Exemplo de recurso:**

**MAIN.TF**

```bash
# Virtual Machine
module "vm" {
  source = "./modules/vm"

  prefix = var.prefix
  location = var.location
  tags = var.tags
  subnet_name = var.subnet_name
  address_space = var.address_space
  address_prefixes = var.address_prefixes
  ip_conf_name = var.ip_conf_name
  private_ip_address_allocation  = var.private_ip_address_allocation
  vm_size = var.vm_size
  publisher = var.publisher
  offer = var.offer
  sku = var.sku
  os_disk_name = var.os_disk_name
  caching = var.os_caching
  os_create_option = var.os_create_option
  managed_disk_type = var.managed_disk_type
  computer_name = var.computer_name
  admin_username = var.admin_username
  admin_password = var.admin_password
  disable_password_authentication = var.disable_password_authentication
}

module "dns" {
  source = "./modules/dns"

  prefix = var.prefix
  location = var.location
  dns_name = dns_name
}
```

**DEV.TFVARS**

```bash
# Global variables
location  = "eastus"
prefix = "unicastcloud-dev"
tags = {
    ProjectName  = "demo-unicast-dev"
    Env          = "Azure"
    Owner        = "Unicast Cloud"
    BusinessUnit = "Unicast Cloud"
    ServiceClass = "Gold"
    Env          = "dev"
}

# DNS variables
dns_name = unciastcloud.io

# Virtual Machine variables
subnet_name = "internal"
address_space = "10.0.2.0/24"
address_prefixes = "10.0.0.0/16"
ip_conf_name = "testconfiguration1"
private_ip_address_allocation  = "Dynamic"
vm_size = "Standard_DS1_v2"
publisher = "Canonical"
offer = "UbuntuServer"
sku = "16.04-LTS"
os_disk_name = "myosdisk1"
os_caching = "ReadWrite"
os_create_option = "FromImage"
managed_disk_type = "Standard_LRS"
computer_name = "hostname"
admin_username = "testadmin"
admin_password = "Password1234!"
disable_password_authentication = false
```

Mais detalhes no repositório: <a href="https://github.com/asilvajunior/devops-case-studies/tree/main/04_terraform_modular_multi_env" target="_blank">Terraform on Azure Reference Modular Multi Environment Sample</a> 

Organizar seus projetos em Terraform pode parecer uma tarefa pequena, mas é uma parte importante do processo de desenvolvimento. 

Em resumo, a organização adequada do seu projeto Terraform é fundamental para garantir que sua infraestrutura seja gerenciada corretamente. A escolha da estrutura depende das necessidades do seu projeto, e as abordagens apresentadas aqui podem ser adaptadas para atender às suas necessidades específicas. Se você está começando um novo projeto, é recomendável optar por uma abordagem modular e multi ambiente, que permitirá uma escalabilidade fácil e uma gestão mais eficiente dos seus recursos.

Independentemente da abordagem escolhida, é importante manter a consistência na organização do seu código Terraform e seguir as melhores práticas para evitar conflitos e erros. Além disso, é fundamental documentar sua estrutura e criar um processo de revisão de código para garantir que as mudanças em sua infraestrutura sejam devidamente validadas antes de serem implementadas.

Com uma organização adequada, o Terraform pode ser uma ferramenta poderosa para gerenciar a infraestrutura do seu projeto no Microsoft Azure e garantir um ambiente seguro, escalável e confiável.

É isso galera, espero que gostem!

Forte Abraço!
