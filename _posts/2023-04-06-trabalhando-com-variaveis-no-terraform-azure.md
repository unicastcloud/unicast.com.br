---
layout: post
title: "Trabalhando com variáveis no Terraform [Azure]"
author: asilva
date: 2023-04-06 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Se você está trabalhando com **Terraform**, sabe que uma das tarefas mais importantes é lidar com variáveis. O **Terraform** é uma ferramenta poderosa que permite criar, alterar e destruir infraestruturas em nuvem de forma eficiente, mas sem variáveis, você pode acabar se perdendo em um mar de código. Neste guia definitivo, vamos explorar o uso de variáveis no **Terraform**, desde os conceitos básicos até algumas técnicas avançadas. 

Pronto para aprender tudo sobre trabalhar com variáveis no Terraform?

### **1. O que são variáveis no Terraform?**

Antes de começarmos a trabalhar com variáveis no Terraform, é importante entender o que elas são e por que são importantes. Em resumo, as variáveis são um mecanismo para armazenar e reutilizar valores que podem ser usados em vários locais dentro do seu código Terraform. Isso pode incluir informações de autenticação, configurações de rede e outras informações que precisam ser repetidamente utilizadas em seu código. As variáveis são essenciais para criar código reutilizável, facilitar a manutenção e reduzir a possibilidade de erros.

### **2. Declaração de variáveis no Terraform**

Agora que entendemos a importância das variáveis no Terraform, é hora de aprender como declará-las em seu código. A declaração de variáveis é feita usando o bloco de código `variable`. Você pode criar um novo bloco de variáveis em qualquer lugar do seu código Terraform, mas é uma boa prática mantê-las agrupadas no início do seu código. A sintaxe básica para a declaração de variáveis é a seguinte:

````bash
variable "nome_da_variavel" {
  type = tipo_da_variavel
}
````

### **3. Tipos de variáveis no Terraform**

O Terraform oferece vários tipos de variáveis que podem ser usados em seu código, dependendo da necessidade. Aqui estão alguns dos tipos de variáveis mais comuns que você encontrará:

**string**

O tipo `string` é usado para armazenar uma cadeia de caracteres, como um nome de usuário ou uma senha. A sintaxe para declarar uma variável do tipo `string` é a seguinte:

````bash
variable "nome_da_variavel" {
  type = string
}
````

**Exemplo:**

````bash
variable "resource_group_name" {
  type = string
  default = "my-resource-group"
}

resource "azurerm_resource_group" "example" {
  name = var.resource_group_name
  location = "West US"
}
````

Neste exemplo, criamos uma variável chamada `"resource_group_name"` do tipo string e definimos um valor padrão de `"my-resource-group"`. Em seguida, usamos essa variável ao criar um grupo de recursos no Azure. A variável `"resource_group_name"` é usada como o nome do grupo de recursos.

**number**

O tipo `number` é usado para armazenar valores numéricos, como um número de porta ou um valor de configuração. A sintaxe para declarar uma variável do tipo `number` é a seguinte:

````bash
variable "nome_da_variavel" {
  type = number
}
````

**Exemplo:**

````bash
variable "disk_size_gb" {
  type = number
  default = 128
}

resource "azurerm_managed_disk" "example" {
  name = "my-disk"
  location = "West US"
  resource_group_name = "my-resource-group"
  storage_account_type = "Standard_LRS"
  disk_size_gb = var.disk_size_gb
}
````

Neste exemplo, criamos uma variável chamada `"disk_size_gb"` do tipo number e definimos um valor padrão de 128. Em seguida, usamos essa variável ao criar um disco gerenciado no Azure. A variável `"disk_size_gb"` é usada como o tamanho do disco.

**bool**

O tipo `bool` é usado para armazenar valores booleanos, como verdadeiro ou falso. A sintaxe para declarar uma variável do tipo `bool` é a seguinte:

````bash
variable "nome_da_variavel" {
  type = bool
}
````

**Exemplo:**

````bash
variable "enable_firewall" {
  type = bool
  default = true
}

resource "azurerm_firewall" "example" {
  name = "my-firewall"
  location = "West US"
  resource_group_name = "my-resource-group"
  depends_on = [
    azurerm_subnet.example
  ]
  dynamic {
    content {
      firewall_policy {
        stateful = true
        ...
        enabled = var.enable_firewall
      }
    }
  }
}
````

Neste exemplo, criamos uma variável chamada `"enable_firewall"` do tipo bool e definimos um valor padrão de `true`.

**list**

O tipo `list` é usado para armazenar uma lista de valores, como uma lista de endereços IP ou nomes de usuários. A sintaxe para declarar uma variável do tipo `list` é a seguinte:

````bash
variable "nome_da_variavel" {
  type = list(tipo_do_elemento)
}
````

**Exemplo:**

````bash
variable "allowed_ip_addresses" {
  type    = list(string)
  default = ["192.168.1.1/24", "10.0.0.1/24"]
}

resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  dynamic "security_rule" {
    for_each = var.allowed_ip_addresses
    content {
      name                       = "AllowAll"
      priority                   = 1001
      direction                  = "Inbound"
      access                     = "Allow"
      protocol                   = "*"
      source_port_range          = "*"
      destination_port_range     = "*"
      source_address_prefix      = security_rule.value
      destination_address_prefix = "*"
    }
  }
}
````

Nesse exemplo, definimos uma variável de lista chamada `"allowed_ip_addresses"` para armazenar uma lista de endereços IP permitidos no grupo de segurança de rede. Em seguida, usamos um bloco `"dynamic"` para iterar sobre a lista de endereços IP e criar uma regra de segurança de entrada para cada endereço IP.

**map**

O tipo `map` é usado para armazenar um conjunto de pares de chave-valor, como configurações de aplicativos ou informações de autenticação. A sintaxe para declarar uma variável do tipo `map` é a seguinte:

````bash
variable "nome_da_variavel" {
  type = map(tipo_da_chave, tipo_do_valor)
}
````

**Exemplo:**

````bash
variable "vms" {
  type = map(object({
    vm_size     = string
    os_disk     = object({
      storage_account_type = string
      disk_size_gb         = number
    })
    data_disks  = list(object({
      storage_account_type = string
      disk_size_gb         = number
    }))
  }))
}

resource "azurerm_virtual_machine" "example" {
  for_each = var.vms

  name                  = each.key
  location              = var.location
  resource_group_name   = var.resource_group_name
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = each.value.vm_size

  storage_os_disk {
    name              = "osdisk-${each.key}"
    caching           = "ReadWrite"
    storage_account_type = each.value.os_disk.storage_account_type
    disk_size_gb         = each.value.os_disk.disk_size_gb
  }

  storage_data_disk {
    name              = "datadisk-${each.key}-${count.index}"
    caching           = "ReadWrite"
    lun               = count.index
    storage_account_type = each.value.data_disks[count.index].storage_account_type
    disk_size_gb         = each.value.data_disks[count.index].disk_size_gb
    create_option     = "Empty"
  }
}
````

Neste exemplo, criamos uma variável chamada `vms` que é um mapa que contém as propriedades necessárias para criar cada máquina virtual. Então, usamos o `for_each` para iterar sobre cada item no mapa e criar uma instância de máquina virtual para cada um.

### **4. Usando variáveis no Terraform**

Agora que você sabe como declarar variáveis no Terraform, é hora de aprender como usá-las em seu código. Para usar uma variável em seu código Terraform, basta chamar o nome da variável usando a sintaxe `${var.nome_da_variavel}`. Por exemplo, se você tiver uma variável chamada `nome` declarada em seu código, você pode usá-la da seguinte maneira:

````bash
resource "azurerm_resource_group" "exemplo_rg" {
  name     = "${var.nome_do_rg}"
  location = "West Europe"
}
````

### **5. Trabalhando com variáveis no Terraform**

Agora que você aprendeu os conceitos básicos de declaração e uso de variáveis no Terraform, é hora de aprender algumas técnicas avançadas. Aqui estão algumas dicas para ajudá-lo a trabalhar com variáveis no Terraform de forma mais eficiente:

**Variáveis padrão**

Você pode definir um valor padrão para uma variável usando o atributo `default`. Isso significa que, se nenhum valor for fornecido para a variável, o valor padrão será usado. Aqui está um exemplo:

````bash
variable "nome" {
  type    = string
  default = "João"
}
````

**Variáveis sensíveis**

Se você estiver trabalhando com informações sensíveis, como senhas ou chaves de API, é importante protegê-las de olhos curiosos. O Terraform oferece suporte a variáveis sensíveis que podem ser criptografadas usando a chave `sensitive`. Aqui está um exemplo:

````bash
variable "senha" {
  type     = string
  sensitive = true
}
````

**Variáveis de ambiente**

Você também pode definir variáveis de ambiente que podem ser usadas em seu código Terraform. Isso é especialmente útil se você estiver trabalhando com informações que podem variar dependendo do ambiente, como URLs de bancos de dados ou chaves de API. Aqui está um exemplo:

````bash
variable "url_do_banco" {
  type    = string
  default = "${env.BANCO_URL}"
}
````

**Variáveis em arquivos externos**

Se você tiver muitas variáveis em seu código Terraform, pode ser útil manter essas variáveis em um arquivo externo. Isso pode ajudar a manter seu código limpo e organizado. Aqui está um exemplo:

````bash
variable "variaveis_externas" {
  type    = map(string)
  default = {}
}

locals {
  variaveis = merge(
    var.variaveis_externas,
    {
      nome_do_arquivo = file("${path.module}/variaveis_externas.tfvars")
    }
  )
}
````

### **6. Perguntas frequentes**

**O que acontece se eu não declarar uma variável?**

Se você não declarar uma variável em seu código Terraform, receberá um erro de sintaxe. É importante declarar todas as variáveis que você planeja usar em seu código, para que o Terraform possa validar sua configuração.

**Posso alterar o valor de uma variável durante a execução do código?**

Não, você não pode alterar o valor de uma variável durante a execução do código Terraform. As variáveis são definidas no momento da execução do código e não podem ser alteradas posteriormente. Se você precisar alterar o valor de uma variável, terá que atualizar seu código e reaplicá-lo.

**Como posso compartilhar variáveis entre módulos Terraform?**

Você pode compartilhar variáveis entre módulos Terraform usando a função `module`. A função `module` permite que você acesse as saídas de outros módulos, o que pode ser útil para compartilhar informações entre módulos. Aqui está um exemplo:

````bash
module "exemplo1" {
  source = "./exemplo1"
}

module "exemplo2" {
  source = "./exemplo2"

  variavel_compartilhada = module.exemplo1.saida_da_variavel
}
````

Neste exemplo, estamos acessando a saída de uma variável em `exemplo1` e compartilhando-a com `exemplo2`.

**O que é um arquivo de variáveis Terraform?**

Um arquivo de variáveis Terraform é um arquivo que contém valores de variáveis para um determinado ambiente. Por exemplo, você pode ter um arquivo de variáveis para o ambiente de desenvolvimento e outro para o ambiente de produção. Os arquivos de variáveis são geralmente usados para manter informações que podem variar entre ambientes, como endereços IP ou chaves de API.

**Como posso usar um arquivo de variáveis Terraform?**

Para usar um arquivo de variáveis Terraform, basta passar o nome do arquivo como um argumento na linha de comando do Terraform. Aqui está um exemplo:

````bash
terraform apply -var-file=variaveis.tfvars
````
Neste exemplo, estamos passando o arquivo `variaveis.tfvars` como um argumento para o comando `apply`.

**O que devo fazer se uma variável estiver faltando em meu código Terraform?**

Se uma variável estiver faltando em seu código Terraform, você receberá um erro informando que a variável é obrigatória. Para corrigir o erro, você precisará fornecer um valor para a variável em seu código ou passar o valor como um argumento na linha de comando do Terraform.

### **7. Conclusão**

Trabalhar com variáveis no Terraform é uma habilidade fundamental que todo usuário do Terraform deve possuir. É importante saber como declarar variáveis, usá-las em seu código e como compartilhá-las entre módulos. Com este guia, você deve estar pronto para começar a trabalhar com variáveis em seu código Terraform.

Lembre-se sempre de declarar suas variáveis com cuidado, proteger informações sensíveis e usar variáveis padrão quando apropriado. Com prática e experiência, você se tornará um especialista em trabalhar com variáveis no Terraform.

É isso galera, espero que gostem!

Forte Abraço!
