---
layout: post
title: "Como utilizar loops no Terraform [Azure]"
author: asilva
date: 2023-04-18 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Aqui estamos novamente para falar sobre **Terraform**. Hoje vamos falar sobre **loops** e como eles podem ajudar a tornar o seu código mais modular e fácil de gerenciar. 

Se você já utiliza o **Terraform** em seus projetos, com certeza já deve ter se deparado com situações em que precisou repetir determinadas configurações para diversos recursos ou componentes. Para isso, existem três tipos de loops no Terraform: **for**, **for_each** e **count**. 

Neste artigo, vamos explorar cada um deles e apresentar suas principais diferenças e exemplos de uso em ambientes reais.

### **1. Uma visão geral de loops no Terraform**

Antes de entrarmos nos detalhes de como usar **loops** no **Terraform**, vamos dar uma olhada em uma visão geral do que é um **loop**. Um **loop** é uma estrutura de controle que permite executar uma ou mais instruções várias vezes, dependendo de uma condição definida pelo desenvolvedor. 

Existem três tipos de loops no Terraform:

- **For**
- **For_each**
- **Count**

Cada tipo de loop tem seus próprios casos de uso e é importante entender as diferenças entre eles para escolher o mais adequado para o seu projeto.

### **2. For Loop**

O loop **for** é usado para iterar sobre um bloco de código um número específico de vezes. Isso é útil quando você sabe exatamente quantas vezes deseja executar uma ação. O loop for pode ser usado em conjunto com a string directive e expressions para gerar valores dinâmicos.

Por exemplo, imagine que você queira criar várias instâncias em uma única declaração de recurso. Você pode usar o loop **for** para criar várias instâncias e, em seguida, usar a string directive para criar nomes de recursos dinamicamente. 

Veja um exemplo de código abaixo:

````bash
resource "azurerm_virtual_machine" "vm" {
  for_each = {
    for i in range(3):
    "${i}" => {
      name = "vm-${i}"
    }
  }
  name = "${each.value.name}"
  ...
}
````

Este código criará três instâncias com nomes `"vm-0"`, `"vm-1"` e `"vm-2"`. Observe que estamos usando a expressão `"range(3)"` para criar uma lista com três elementos `(0, 1 e 2)` e depois estamos usando a string directive para criar o nome de cada instância.

### **3. For_each**

O loop **for_each** é usado para iterar sobre uma coleção de recursos e criar uma instância para cada recurso. Isso é útil quando você precisa criar um conjunto de recursos que não podem ser criados com um único bloco de código. O loop **for_each** é usado em conjunto com expressions.

Por exemplo, imagine que você queira criar um conjunto de máquinas virtuais em um ambiente de desenvolvimento. Você pode usar o loop **for_each** para criar uma instância para cada ambiente de desenvolvimento. 

Veja um exemplo de código abaixo:

````bash
variable "environments" {
  type = map(object({
    subnet_id = string
  }))
  default = {
    dev = {
      subnet_id = "subnet-dev"
    }
    staging = {
      subnet_id = "subnet-staging"
    }
    prod = {
      subnet_id = "subnet-prod"
    }
  }
}

resource "azurerm_virtual_machine" "vm" {
  for_each = var.environments
  name = "vm-${each.key}"
  subnet_id = each.value.subnet_id
  ...
}
````

Este código criará uma instância para cada ambiente de desenvolvimento: `"vm-dev"`, `"vm-staging"` e `"vm-prod"`. Observe que estamos usando o loop `for_each` para iterar sobre a variável `"environments"` e criar uma instância para cada ambiente.

### **4. Count**

O loop **count** é um pouco diferente dos outros loops, pois ele é usado para criar um número específico de recursos. Em vez de iterar por uma lista de recursos, o loop **count** cria uma instância para cada item em uma lista específica. Você pode usar o loop **count** em combinação com o parameter para definir o número de recursos que você deseja criar. 

Veja um exemplo de código abaixo:

````bash
variable "subnet_count" {
  default = 4
}

variable "virtual_network_cidr" {
  default = "10.0.0.0/16"
}

resource "azurerm_virtual_network" "example" {
  name = "example-vnet"
  address_space = [var.virtual_network_cidr]
  location = "East US"
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_subnet" "example" {
  count = var.subnet_count
  name = "example-subnet-${count.index}"
  address_prefix = "10.0.${count.index}.0/24"
  virtual_network_name = azurerm_virtual_network.example.name
  resource_group_name = azurerm_resource_group.example.name
}
````

Neste exemplo, criamos uma rede virtual com base no valor da variável `"virtual_network_cidr"`, que é definida como `"10.0.0.0/16"`. Em seguida, criamos quatro sub-redes com base no valor da variável `"subnet_count"`, que é definida como 4.

Usamos a diretiva `count` para criar quatro instâncias do recurso azurerm_subnet. Usamos o parâmetro `index` do loop count para garantir que cada recurso tenha um nome exclusivo. O nome da sub-rede é definido como `"example-subnet-${count.index}"`, onde `${count.index}` é o índice do loop count atual.

### **5. Comparação entre, for, for_each e count**

Agora que já discutimos os três tipos de loops no Terraform, vamos fazer uma comparação entre eles para ajudá-lo a decidir qual usar em diferentes situações.

**For**

- O loop for é útil quando você precisa iterar por uma lista de valores estáticos, como uma lista de regiões onde deseja criar instâncias de recursos.
- Ele é mais simples e fácil de entender do que os outros dois loops.
- Ele cria uma instância para cada item em uma lista de valores estática.

**For_each**

- O loop for_each é útil quando você precisa criar instâncias de recursos com base em uma lista dinâmica de valores, como uma lista de usuários em uma conta.
- Ele é mais poderoso do que o loop for e permite criar recursos com base em listas dinâmicas.
- Ele cria uma instância para cada item em uma lista de valores dinâmicos.

**Count**

- O loop count é útil quando você precisa criar um número específico de instâncias de recursos.
- Ele é o mais simples dos três loops e é fácil de entender.
- Ele cria uma instância para cada item em uma lista de valores estáticos e é usado em combinação com o parameter count para definir o número de instâncias a serem criadas.

No geral, o loop **for** é o mais simples e fácil de entender dos três loops, mas é limitado a listas estáticas de valores. O loop **count** é semelhante ao loop for, mas é usado para criar um número específico de instâncias de recursos. O loop **for_each** é o mais poderoso dos três loops e pode ser usado para criar recursos com base em listas dinâmicas de valores.

### **6. Conclusão**

Espero que este artigo tenha sido útil para você entender melhor os loops no Terraform e como usá-los em seus projetos. Sei que o tema de loops pode ser difícil de entender de primeira, mas são extremamente importantes para quem utiliza o Terraform.

Cada um dos três loops **for**, **for_each** e **count** tem suas próprias vantagens e desvantagens, e escolher o loop certo para sua situação específica é fundamental. A documentação oficial do Terraform é um excelente recurso para se aprofundar em cada um dos loops e entender as diferenças.

- <a href="https://www.terraform.io/docs/language/expressions/for.html" target="_blank">For</a> 
- <a href="https://www.terraform.io/docs/language/meta-arguments/for_each.html" target="_blank">For_each</a> 
- <a href="https://www.terraform.io/docs/language/meta-arguments/count.html" target="_blank">Count</a> 

A melhor maneira de aprender a usar os loops no Terraform é experimentar com cada um deles e entender como eles funcionam. Comece com os exemplos mais simples e, em seguida, vá expandindo para listas dinâmicas e expressões mais complexas.

Por fim, estamos comprometidos em fornecer mais recursos para ajudá-lo a entender e utilizar os loops no Terraform. Fique atento a mais artigos e até vídeos em nosso canal do YouTube sobre este assunto, pois sabemos que há uma complexidade envolvida e estamos aqui para ajudá-lo a dominá-la.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!

