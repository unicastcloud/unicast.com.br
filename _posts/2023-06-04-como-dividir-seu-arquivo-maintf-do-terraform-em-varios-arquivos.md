---
layout: post
title: "Como dividir seu arquivo main.tf do Terraform em vários arquivos"
date: 2023-06-04 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Hoje vamos abordar um tópico empolgante e extremamente útil para todos os desenvolvedores que utilizam o Terraform: como dividir o seu arquivo main.tf em vários arquivos menores. Isso mesmo, vamos explorar uma técnica que vai revolucionar a maneira como você organiza o seu código e o torna mais eficiente!

Se você já trabalhou com projetos de grande escala no Terraform, provavelmente já se deparou com um arquivo main.tf gigantesco e desafiador de gerenciar. Mas não se preocupe, estamos aqui para te ajudar a resolver esse problema e aprimorar a sua experiência com o Terraform.

Neste artigo, vamos mostrar a você as melhores práticas para dividir o seu arquivo main.tf em partes menores e mais gerenciáveis. Vamos explorar passo a passo como identificar os recursos e fontes de dados, criar arquivos separados, migrar os blocos de código e definir variáveis e outputs. Além disso, também vamos abordar a importância de separar as configurações de providers e versions em arquivos dedicados.

Ao final deste artigo, você terá em mãos todas as ferramentas necessárias para transformar o seu código do Terraform em uma obra de arte organizada e de fácil manutenção.

### **1. Por que dividir o arquivo main.tf em vários arquivos?**

À medida que o seu código Terraform cresce, o arquivo `main.tf` pode se tornar extenso e difícil de gerenciar. Dividir esse arquivo em partes menores tem diversos benefícios:

- **Organização**: A separação lógica dos recursos, variáveis e outputs torna o código mais legível e fácil de entender.
- **Reusabilidade**: Ao dividir o código em módulos e arquivos específicos, você pode reutilizar essas partes em outros projetos.
- **Colaboração em equipe**: Ao dividir o código em arquivos menores, diferentes membros da equipe podem trabalhar em partes específicas sem conflitos constantes.
- **Manutenção simplificada:** Ao isolar partes do código, as atualizações e correções podem ser realizadas com mais facilidade e segurança.

Agora que entendemos os benefícios, vamos ver como dividir o arquivo main.tf.

### **2. Como dividir o arquivo main.tf**

Vamos começar examinando um exemplo de código que cria uma Máquina Virtual (VM) no Azure, onde todas as configurações estão em um único arquivo main.tf.

````bash
# main.tf

terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.0"
    }
  }
}

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resource-group"
  location = "West Europe"
}

resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

# Mais configurações e recursos aqui...

output "vm_ip" {
  value = azurerm_virtual_machine.example.public_ip_address
}
````

Agora vamos dividir esse arquivo em partes menores para facilitar a manutenção e a compreensão.

### **3. Identificar recursos e fontes de dados**

Primeiro, identifique os recursos e fontes de dados que podem ser separados em arquivos individuais. No nosso exemplo, temos um recurso de grupo de recursos, uma rede virtual e uma VM.

### **4. Criar arquivos .tf separados para recursos e fontes de dados**

Agora, crie arquivos `.tf` separados para cada recurso e fonte de dados identificados. Vamos criar os seguintes arquivos:

`resource_group`
`virtual_network`
`virtual_machine`

Cada recurso terá seu conjunto completo de arquivos: `main.tf`, `outputs.tf` e `variables.tf`

Vamos começar com o recurso `resource_group`:

````bash
# main.tf

resource "azurerm_resource_group" "example" {
  name     = "example-resource-group"
  location = "West Europe"
}
````

Em seguida, vamos criar o recurso `virtual_network`:

````bash
# main.tf

resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}
````

Agora, vamos migrar o bloco de código para criar a VM `virtual_machine`:

````bash
# main.tf

resource "azurerm_virtual_machine" "example" {
  name                  = "example-vm"
  location              = azurerm_resource_group.example.location
  resource_group_name   = azurerm_resource_group.example.name
  vm_size               = "Standard_DS1_v2"
  delete_os_disk_on_termination = true
  delete_data_disks_on_termination = true

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "example-os-disk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Premium_LRS"
    disk_size_gb      = 30
  }

  os_profile {
    computer_name  = "example-vm"
    admin_username = "adminuser"

    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

  network_interface {
    name                      = "example-nic"
    primary                   = true
    network_security_group_id = azurerm_network_security_group.example.id
    ip_configuration {
      name                          = "example-nic-config"
      subnet_id                     = azurerm_subnet.example.id
      private_ip_address_allocation = "Dynamic"
    }
  }

  depends_on = [
    azurerm_virtual_network.example
  ]
}
````

Observe que, ao migrar os blocos de código para arquivos separados, referenciamos outros recursos usando o formato `azurerm_resource_group.example.location` e `azurerm_resource_group.example.name`. Isso garante que a ordem correta de criação e dependências seja mantida.

Ao finalizar essa etapa, você terá todos os recursos e fontes de dados divididos em arquivos `.tf` individuais, o que facilitará a manutenção e o entendimento do código.

Por fim, crie arquivos separados para **variáveis** e **outputs**. Esses arquivos podem ser usados para definir variáveis comuns e especificar os dados que você deseja expor após a execução do Terraform.

Aqui está um exemplo do arquivo `variables.tf`:

````bash
# variables.tf

variable "location" {
  description = "Azure region"
  type        = string
  default     = "West Europe"
}
````

E um exemplo do arquivo `outputs.tf`:

````bash
# outputs.tf

output "vm_ip" {
  value = azurerm_virtual_machine.example.public_ip_address
}
````

Após dividir o arquivo `main.tf` em arquivos menores, a estrutura do seu diretório **Terraform** ficará semelhante a esta:

````
📦terraform_modular_multi_env
 ┣ 📂.terraform
 ┃ ┗ 📂modules
 ┃ ┃ ┗ 📜modules.json
 ┣ 📂modules
 ┃ ┣ 📂resource_group
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┃ ┗ 📂virtual_network
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┃ ┗ 📂virtual_machine
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
````

Além de separar recursos, fontes de dados, **variáveis** e **outputs**, é importante também dividir as configurações de **providers** e **versions** em arquivos separados. Vamos adicionar essas divisões ao nosso exemplo:

Aqui está um exemplo do arquivo `providers.tf`:

````bash
# providers.tf

terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.0"
    }
  }
}

provider "azurerm" {
  features {}
}
````

E um exemplo do arquivo `versions.tf`:

````bash
# versions.tf

terraform {
  required_version = ">= 0.15.0"
}
````

Ao separar as configurações de **providers** e **versions**, seu diretório Terraform ficará com a seguinte estrutura:

````
📦terraform_modular_multi_env
 ┣ 📂.terraform
 ┃ ┗ 📂modules
 ┃ ┃ ┗ 📜modules.json
 ┣ 📂modules
 ┃ ┣ 📂resource_group
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┃ ┗ 📂virtual_network
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┃ ┗ 📂virtual_machine
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┣ 📜.gitignore
 ┣ 📜README.md
 ┣ 📜main.tf
 ┣ 📜providers.tf
 ┣ 📜variables.tf
 ┗ 📜versions.tf
````

### **5. Validando e testando o funcionamento**

Depois de dividir o seu código em arquivos menores, você pode validar e testar o funcionamento usando comandos do Terraform. Certifique-se de que todas as dependências entre os arquivos estejam corretamente configuradas.

Você pode executar o comando `terraform init` para inicializar o diretório Terraform e instalar os provedores necessários.

Em seguida, utilize o comando `terraform plan` para visualizar as alterações planejadas e verificar se não há erros ou problemas de configuração.

Por fim, execute o comando `terraform apply` para aplicar as alterações no ambiente Azure.

### **6. Conclusão**

Dividir o arquivo `m`ain.tf` do Terraform em vários arquivos é uma prática recomendada para organizar e simplificar o gerenciamento do código. Neste artigo, vimos como realizar essa divisão passo a passo, criando arquivos separados para recursos, fontes de dados, variáveis, outputs, providers e versions.

Ao adotar essa abordagem, você obtém benefícios como maior **organização**, **reusabilidade**, **colaboração em equipe** e **manutenção simplificada**.

Esperamos que este guia completo tenha sido útil para você compreender como dividir o arquivo main.tf no Terraform. 

Lembre-se de sempre verificar a documentação oficial do Terraform e adaptar as práticas recomendadas às necessidades específicas do seu projeto.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
