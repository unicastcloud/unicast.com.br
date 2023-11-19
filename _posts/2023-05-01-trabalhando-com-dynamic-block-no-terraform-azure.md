---
layout: post
title: "Trabalhando com dynamic block no Terraform [Azure]"
author: asilva
date: 2023-05-01 09:30 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Se você está acompanhando nossa série de artigos sobre **Terraform**, sabe que estamos cobrindo diversos tópicos importantes para quem está começando ou querendo se aprofundar na ferramenta. E hoje vamos falar sobre um conceito avançado, mas extremamente útil: o **dynamic block**.

O **dynamic block** é um recurso do **Terraform** que permite criar blocos de forma dinâmica em seu código. Ele se comporta de forma semelhante a um loop for ou for-each, permitindo iterar não apenas sobre um intervalo de valores, mas também criar blocos dinâmicos aninhados que podem ser complexos.

## **1. Por que usar o dynamic block?**

O **dynamic block** pode ser utilizado em diversas situações, mas sua principal vantagem é permitir que você crie blocos de recursos dinamicamente. 

Por exemplo, você pode utilizar o **dynamic block** para criar um conjunto de regras de segurança em um grupo de segurança de rede no Azure ou para criar um conjunto de máquinas virtuais em um conjunto de disponibilidade. Com o **dynamic block**, você pode definir uma lista de valores e iterar sobre essa lista para criar múltiplos blocos de recursos de uma só vez.

Embora o **dynamic block** possa ser um conceito difícil de entender no começo, ele oferece muitos benefícios. Ele permite que você crie blocos de recursos de forma dinâmica, o que pode economizar muito tempo e evitar erros manuais. Além disso, o dynamic block pode tornar seu código mais fácil de ler e manter, pois você pode iterar sobre listas de valores em vez de escrever manualmente cada bloco de recurso.

## **2. Como utilizar o dynamic block?**

O **dynamic block** consiste em três elementos principais:

- **Name of dynamic block**: é o nome do bloco dinâmico que você está criando. Ele pode ser qualquer nome que você escolher, mas deve ser único dentro do módulo ou arquivo.
- **for_each**: é a lista de valores que você está iterando. Cada valor na lista será usado para criar um bloco de recurso.
- **content**: é o conteúdo do bloco de recurso que será criado. Este é um bloco normal de recurso que contém as configurações que você deseja definir para cada recurso.

Para usar o **dynamic block**, primeiro você precisa definir a lista de valores que você deseja iterar. Em seguida, você pode usar o bloco dynamic para criar blocos de recursos com base nessa lista de valores. 

Veja um exemplo abaixo:

````bash
resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  dynamic "security_rule" {
    for_each = var.security_rules
    content {
      name                       = security_rule.value.name
      priority                   = security_rule.value.priority
      direction                  = security_rule.value.direction
      access                     = security_rule.value.access
      protocol                   = security_rule.value.protocol
      source_port_range          = security_rule.value.source_port_range
      destination_port_range     = security_rule.value.destination_port_range
      source_address_prefix      = security_rule.value.source_address_prefix
      destination_address_prefix = security_rule.value.destination_address_prefix
    }
  }
}
````

Nesse exemplo, estamos criando um grupo de segurança de rede no Azure e usando o dynamic block para iterar sobre a criação de regras de segurança. No dynamic block, estamos iterando sobre a variável `var.security_rules`, que contém uma lista de regras de segurança. Para cada regra de segurança na lista, criamos um novo bloco de recurso de regra de segurança.

Note que estamos utilizando a variável `security_rule.value` para acessar os valores de cada regra de segurança na lista. Essa sintaxe é usada dentro do dynamic block e é usada para acessar os valores das chaves dentro da lista. No exemplo acima, estamos acessando os valores das chaves `name`, `priority`, `direction`, etc.

## **3. Uso real do dynamic block**

O dynamic block pode ser utilizado em várias situações, como na criação de recursos no **Azure**, **AWS**, **GCP** e outros provedores. Um exemplo prático é a criação de um cluster **Kubernetes** no **Azure Kubernetes Service (AKS)**, onde é possível utilizar o dynamic block para habilitar ou não recursos como o Azure Defender e o cluster privado.

Eu gosto bastante dessa abordagem quando vou criar meus modulos no Terraform. 

Para habilitar ou desabilitar o **Azure Defender**, podemos definir uma variável booleana no arquivo de variáveis do Terraform (tfvars), por exemplo:

````bash
enable_defender = true
````

Em seguida, podemos utilizar o dynamic block para criar o recurso de Azure Defender apenas se a variável `enable_defender` for verdadeira:

````bash
resource "azurerm_kubernetes_cluster" "aks_cluster" {
  name                = var.cluster_name
  location            = var.location
  resource_group_name = var.resource_group_name
  dns_prefix          = var.dns_prefix

  # ...

  dynamic "addon_profile" {
    for_each = var.enable_defender ? [1] : []
    content {
      oms_agent {
        enabled = true
      }
    }
  }
}
````

Neste exemplo, o recurso de Azure Defender é criado apenas se a variável `enable_defender` for verdadeira. Para isso, utilizamos o dynamic block para criar o bloco de recurso `addon_profile` apenas se a variável for verdadeira.

Da mesma forma, podemos utilizar o dynamic block para criar um cluster Kubernetes privado no AKS. Para isso, podemos definir uma variável booleana no arquivo de variáveis do Terraform, por exemplo:

````bash
enable_private_cluster = true
````

Em seguida, podemos utilizar o dynamic block para criar o recurso de cluster privado apenas se a variável `enable_private_cluster` for verdadeira:

````bash
resource "azurerm_kubernetes_cluster" "aks_cluster" {
  name                = var.cluster_name
  location            = var.location
  resource_group_name = var.resource_group_name
  dns_prefix          = var.dns_prefix

  # ...

  dynamic "private_cluster" {
    for_each = var.enable_private_cluster ? [1] : []
    content {
      network_profile {
        network_plugin = "azure"
        dns_service_ip = "10.0.0.10"
        service_cidr   = "10.0.0.0/16"
      }

      private_cluster_enabled = true
      private_dns_zone_id     = azurerm_private_dns_zone.private_dns_zone.id
    }
  }
}
````

Neste exemplo, o recurso de cluster privado é criado apenas se a variável `enable_private_cluster` for verdadeira. Para isso, utilizamos o dynamic block para criar o bloco de recurso `private_cluster` apenas se a variável for verdadeira.

## **4. Conclusão**

Exploramos a funcionalidade do dynamic block no Terraform. Vimos que essa é uma técnica avançada, que pode ser difícil no começo, mas que traz muitos benefícios para o gerenciamento de infraestrutura.

O dynamic block, basicamente, nos permite criar blocos de recursos dinâmicos, iterando sobre um conjunto de valores. Isso pode ser útil em muitos cenários, como a criação de recursos em lote, ou a configuração de múltiplos recursos com as mesmas configurações básicas.

Além disso, o dynamic block também nos permite criar blocos dinâmicos aninhados, o que significa que podemos criar configurações mais complexas para nossos recursos.

Vimos exemplos de como usar o dynamic block em conjunto com a plataforma Microsoft Azure, criando recursos de AKS e de network security group. Usamos o dynamic block para habilitar ou não recursos de forma dinâmica, tornando nosso código mais flexível e reutilizável.

Existem muitas outras possibilidades que podemos explorar com o dynamic block no Terraform, como a criação dinâmica de recursos em vários provedores de nuvem, ou a criação de recursos com base em dados externos, como um banco de dados ou um arquivo CSV.

Esperamos que este artigo tenha sido útil para você e tenha ajudado a entender melhor o que é o dynamic block e como usá-lo em seus projetos Terraform.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!