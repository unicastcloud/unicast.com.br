---
layout: post
title: "Criando seu primeiro módulo no Terraform [Azure]"
author: asilva
date: 2023-04-03 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Continuando com nossos artigos relacionados ao **Terraform**, hoje vamos falar sobre a criação de um módulo para facilitar a utilização de nosso código. Se você ainda não sabe o que é um módulo no **Terraform**, pode conferir nossos artigos anteriores para entender melhor.

Confira aqui:

- <a href="https://unicast.com.br/posts/como-organizar-projetos-de-terraform-azure/" target="_blank">Como organizar projetos de Terraform [Azure]</a>
- <a href="https://unicast.com.br/posts/10-boas-praticas-para-projetos-terraform/" target="_blank">10 Boas Praticas para projetos de Terraform</a>
- <a href="https://unicast.com.br/posts/trabalhando-com-multiplos-ambientes-no-terraform-azure/" target="_blank">Trabalhando com múltiplos ambientes no Terraform [Azure]</a>

Basicamente, um módulo é um conjunto de recursos do Terraform que podem ser reutilizados em vários projetos, tornando o código mais legível e fácil de manter. Além disso, a criação de módulos também permite a colaboração entre equipes.

### **1. Preparando o ambiente**

Antes de começar a criar seu primeiro módulo no **Terraform**, é importante que você configure seu ambiente de desenvolvimento. 

Vamos começar com nossa estrutura de pastas para nosso código. É importante seguir as melhores práticas e utilizar o formato modular para deixar o código o mais utilizável possível.

```
📦01_projeto_unicast
 ┣ 📂modules
 ┃ ┣ 📂azure-storage-account-module
 ┃ ┃ ┣ 📜README.md
 ┃ ┃ ┣ 📜main.tf
 ┃ ┃ ┣ 📜outputs.tf
 ┃ ┃ ┗ 📜variables.tf
 ┣ 📜.gitignore
 ┣ 📜.terraform-docs.yml
 ┣ 📜README.md
 ┗ 📜main.tf
```

Agora que já entendemos a estrutura básica de um módulo no **Terraform**, vamos ver um exemplo prático de como criar um módulo de **storage account** com **blob storage** no **Microsoft Azure**.

### **2. Criando nosso primeiro módulo no Terraform**

Ao criar um módulo **Terraform**, é altamente recomendável seguir a documentação oficial do módulo, que está disponível no site do **Terraform**. Ao consultar a documentação, você pode verificar quais entradas são possíveis para o módulo, bem como quais entradas são **obrigatórias** e **opcionais**.

No nosso exemplo de criação de um módulo **Terraform** para provisionar uma **storage account** com um **blob storage** no **Azure**, podemos consultar a documentação oficial do módulo **AzureRM Storage Account** no site do Terraform. Essa documentação contém todas as informações necessárias para criar um módulo que provisione uma storage account no Azure.

Na documentação do módulo, você encontrará uma lista completa de entradas possíveis, bem como exemplos de como usá-las em um módulo **Terraform.** Seguir a documentação oficial do módulo é uma das melhores práticas recomendadas pelo **Terraform**, garantindo que você esteja criando seu módulo de maneira eficiente e seguindo as práticas recomendadas.

Aqui está o link para a documentação do módulo **AzureRM Storage Account**, que pode ser usado como referência:

- <a href="https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/storage_account" target="_blank">azurerm_storage_account</a>

O nosso módulo estará dentro da pasta **azure-storage-account-module**, então vamos criar a estrutura de pastas para o nosso código:

No arquivo ```main.tf``` (do modulo), vamos adicionar o código para criação da storage account com bloco de storage:

```
resource "azurerm_storage_account" "storage-account" {
  name                     = var.storage_account_name
  resource_group_name      = var.resource_group_name
  location                 = var.location
  account_tier             = var.account_tier
  account_replication_type = var.account_replication_type

  tags = var.tags
}

resource "azurerm_storage_container" "storage-container" {
  name                  = var.storage_container_name
  storage_account_name  = azurerm_storage_account.storage-account.name
  container_access_type = "private"

  depends_on = [
    azurerm_storage_account.storage-account
  ]
}
```

Este código cria uma storage account com um bloco de storage, conforme mencionado anteriormente.

No arquivo ```variables.tf```, vamos adicionar as variáveis que serão utilizadas pelo ```main.tf```:

```
variable "storage_account_name" {
  type        = string
  description = "Nome da Storage Account"
}

variable "resource_group_name" {
  type        = string
  description = "Nome do Resource Group"
}

variable "location" {
  type        = string
  description = "Localização da Storage Account"
}

variable "account_tier" {
  type        = string
  description = "Tipo de conta"
  default     = "Standard"
}

variable "account_replication_type" {
  type        = string
  description = "Tipo de replicação da conta"
  default     = "LRS"
}

variable "storage_container_name" {
  type        = string
  description = "Nome do Bloco de Storage"
}

variable "tags" {
  type        = map(string)
  description = "Tags adicionais para a Storage Account"
}
```

Por fim, no arquivo ```outputs.tf```, vamos definir o output que será exibido ao final da execução do código:

```
output "storage_container_url" {
  value = azurerm_storage_container.storage-container.primary_access_key
}
```

Pronto! Agora temos o nosso módulo de **storage accoun**t com **blob storage** no **Microsoft Azure**.

Mas como utilizá-lo em nosso projeto?

### **3. Configurando nosso primeiro módulo no Terraform**

Vamos configurar nosso arquivo ```main.tf``` na raiz do nosso projeto e utilizar o módulo que acabamos de criar:

```
provider "azurerm" {
  features {}
}

module "azure_storage_account" {
  source = "./azure-storage-account-module"

  storage_account_name      = "my-storage-account"
  resource_group_name       = "my-resource-group"
  location                  = "East US"
  account_tier              = "Standard"
  account_replication_type  = "LRS"
  storage_container_name    = "my-storage-container"
  tags = {
    environment = "dev"
    department  = "IT"
  }
}

output "storage_container_url" {
  value = module.azure_storage_account.storage_container_url
}
```

Nesse exemplo, utilizamos o provider ```azurerm``` para nos autenticar na nossa conta do Azure e, em seguida, chamamos o módulo que acabamos de criar.

Repare que estamos passando os valores das variáveis definidas em ```variables.tf``` para o módulo. Isso é feito através dos argumentos ```storage_account_name```, ```resource_group_name```, ```location```, ```account_tier```, ```account_replication_type```, ```storage_container_name``` e ```tags```.

Por fim, definimos o **output** ```storage_container_url``` para exibir o endereço de acesso ao bloco de storage criado.

### **4. Validando e utilizando nosso primeiro módulo no Terraform**

Agora, para validar e implantar esse recurso no Azure, precisamos executar alguns comandos no terminal.

Primeiro, vamos inicializar o nosso projeto, que irá baixar as dependências necessárias para a execução do código:

```bash
terraform init
```

Em seguida, vamos validar se não há nenhum erro de sintaxe ou de configuração no nosso código:

```bash
terraform validate
```

Se tudo estiver correto, podemos prosseguir para o próximo passo: planejar a nossa infraestrutura. Esse comando irá mostrar o que o **Terraform** irá fazer na nossa conta do Azure antes de executar o código:

```bash
terraform plan
```

Ao executar esse comando, você verá uma saída semelhante a esta:

```
Terraform will perform the following actions:

  # module.azure_storage_account.azurerm_storage_account.storage-account will be created
  + resource "azurerm_storage_account" "storage-account" {
      + access_tier                      = (known after apply)
      + account_encryption_source        = (known after apply)
      + account_kind                     = "StorageV2"
      + account_replication_type         = "LRS"
      + account_tier                     = "Standard"
      + account_type                     = (known after apply)
      + enable_blob_encryption           = (known after apply)
      + enable_file_encryption           = (known after apply)
      + enable_https_traffic_only        = (known after apply)
      + id                               = (known after apply)
      + is_hns_enabled                   = (known after apply)
      + kind                             = (known after apply)
      + location                         = "eastus"
      + name                             = "my-storage-account"
      + network_rulesets                 = (known after apply)
      + primary_access_key               = (sensitive value)
      + primary_blob_connection_string   = (sensitive value)
      + primary_blob_endpoint            = (known after apply)
      + primary_blob_host                = (known after apply)
      + primary_connection_string        = (sensitive value)
      + primary_file_endpoint            = (known after apply)
      + primary_location                 = (known after apply)
      + primary_queue_endpoint           = (known after apply)
      + primary_queue_host               = (known after apply)
      + primary_table_endpoint           = (known after apply)
      + primary_table_host               = (known after apply)
      + primary_web_endpoint             = (known after apply)
      + primary_web_host                 = (known after apply)
      + resource_group_name              = "my-resource-group"
      + secondary_access_key             = (sensitive value)
      + secondary_blob_connection_string = (sensitive value)
      + secondary_blob_endpoint          = (known after apply)
      + secondary_blob_host              = (known after apply)
      + secondary_connection_string      = (sensitive value)
      + secondary_file_endpoint          = (known after apply)
      + secondary_location               = (known after apply)
      + secondary_queue_endpoint         = (known after apply)
      + secondary_queue_host             = (known after apply)
      + secondary_table_endpoint         = (known after apply)
      + secondary_table_host             = (known after apply)
      + secondary_web_endpoint           = (known after apply)
      + secondary_web_host               = (known after apply)
      + tags                             = {
          + "environment" = "dev"
          + "project"     = "my-project"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

A saída mostrada pelo comando **terraform plan** nos informa que o Terraform irá criar uma ```azurerm_storage_account`` com as configurações que definimos no nosso código.

Para implantar o recurso na nossa conta do Azure, podemos executar o comando:

```bash
terraform apply
```

```
Terraform will perform the following actions:

  # module.azure_storage_account.azurerm_storage_account.storage-account will be created
  + resource "azurerm_storage_account" "storage-account" {
      + access_tier                      = (known after apply)
      + account_encryption_source        = (known after apply)
      + account_kind                     = "StorageV2"
      + account_replication_type         = "LRS"
      + account_tier                     = "Standard"
      + account_type                     = (known after apply)
      + enable_blob_encryption           = (known after apply)
      + enable_file_encryption           = (known after apply)
      + enable_https_traffic_only        = (known after apply)
      + id                               = (known after apply)
      + is_hns_enabled                   = (known after apply)
      + kind                             = (known after apply)
      + location                         = "eastus"
      + name                             = "my-storage-account"
      + network_rulesets                 = (known after apply)
      + primary_access_key               = (sensitive value)
      + primary_blob_connection_string   = (sensitive value)
      + primary_blob_endpoint            = (known after apply)
      + primary_blob_host                = (known after apply)
      + primary_connection_string        = (sensitive value)
      + primary_file_endpoint            = (known after apply)
      + primary_location                 = (known after apply)
      + primary_queue_endpoint           = (known after apply)
      + primary_queue_host               = (known after apply)
      + primary_table_endpoint           = (known after apply)
      + primary_table_host               = (known after apply)
      + primary_web_endpoint             = (known after apply)
      + primary_web_host                 = (known after apply)
      + resource_group_name              = "my-resource-group"
      + secondary_access_key             = (sensitive value)
      + secondary_blob_connection_string = (sensitive value)
      + secondary_blob_endpoint          = (known after apply)
      + secondary_blob_host              = (known after apply)
      + secondary_connection_string      = (sensitive value)
      + secondary_file_endpoint          = (known after apply)
      + secondary_location               = (known after apply)
      + secondary_queue_endpoint         = (known after apply)
      + secondary_queue_host             = (known after apply)
      + secondary_table_endpoint         = (known after apply)
      + secondary_table_host             = (known after apply)
      + secondary_web_endpoint           = (known after apply)
      + secondary_web_host               = (known after apply)
      + tags                             = {
          + "environment" = "dev"
          + "project"     = "my-project"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
```

O Terraform está nos perguntando se realmente queremos implantar as mudanças descritas na saída. Se você estiver certo de que deseja prosseguir, digite **"yes"** e pressione **"Enter"**.

O Terraform começará a criar o recurso na sua conta do Azure e você verá uma saída semelhante a esta:

```bash
azurerm_storage_account.storage-account: Creating...
azurerm_storage_account.storage-account: Creation complete after 1m54s [id=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Storage/storageAccounts/my-storage-account]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Isso indica que a criação do recurso foi bem-sucedida. Agora podemos verificar o recurso criado no portal do Azure ou usando o comando **terraform show** para ver a representação do estado atual do nosso ambiente.

### **4. Conclusão**

Neste artigo, aprendemos a criar um módulo **Terraform** para provisionar uma **storage account** com um **blob storage** no **Microsoft Azure**. Vimos como seguir as melhores práticas ao criar um módulo **Terraform**, incluindo a estrutura de pastas para nosso código e o uso de arquivos **main.tf, variables.tf e outputs.tf**. 

Lembre-se de que ainda há muitas maneiras de melhorar este modelo, tornando-o mais reutilizável e adaptável a diferentes cenários. 

Por exemplo, podemos usar arquivos **tfvars** para armazenar variáveis e **workspaces** para separar o estado do Terraform para diferentes ambientes. Esses tópicos serão abordados em futuros artigos, portanto, fique atento para continuar aprendendo sobre como criar infraestrutura como código com **Terraform**.

Esperamos que você tenha achado este artigo útil e que ele tenha fornecido uma base sólida para criar seus próprios módulos Terraform. Lembre-se de seguir as melhores práticas e a documentação oficial do Terraform para criar seus módulos de forma eficiente e confiável

É isso galera, espero que gostem!

Forte Abraço!
