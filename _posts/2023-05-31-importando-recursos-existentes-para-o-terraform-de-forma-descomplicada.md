---
layout: post
title: "Importando recursos existentes para o Terraform de forma descomplicada"
author: asilva
date: 2023-05-31 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Importar recursos existentes para o Terraform pode ser um desafio. No nosso último artigo, **"Importando recursos existentes do Azure para o Terraform"**, discutimos esse processo e compartilhamos algumas dicas úteis. 

Agora, vamos abordar duas ferramentas populares que podem facilitar a importação de recursos: o **Terraformer** e o **Terrafy**. Vamos explorar cada uma delas e fornecer exemplos de casos de uso reais.

## **1. Dificuldades de importar recursos existentes para o Terraform**

Quando se trata de importar recursos existentes para o Terraform, os desafios podem surgir devido à necessidade de sincronizar o estado atual com a configuração gerenciada pelo Terraform. 

Isso pode envolver a criação de definições de recursos no formato **HCL (HashiCorp Configuration Language)** e a manutenção de um estado consistente. Felizmente, existem ferramentas que podem nos ajudar a simplificar esse processo.

## **2. O que é o Terraformer?**

O **Terraformer** é uma ferramenta desenvolvida pela equipe de engenharia do **Waze SRE (Site Reliability Engineering)** para facilitar a importação de recursos existentes de provedores de nuvem para o Terraform. Ele é uma opção poderosa e flexível para a importação de recursos do **Azure**, **AWS**, **Google Cloud Platform** e outros provedores de nuvem populares.

Com o **Terraformer**, você pode explorar a infraestrutura existente em seu provedor de nuvem e gerar automaticamente os arquivos de configuração do Terraform correspondentes. Isso permite que você reutilize recursos já existentes em seu ambiente de nuvem e gerencie-os de forma mais eficiente usando a infraestrutura como código fornecida pelo Terraform.

O **Terraformer** utiliza as APIs dos provedores de nuvem para obter informações sobre os recursos existentes, como máquinas virtuais, redes, bancos de dados, balanceadores de carga, entre outros. Ele analisa essas informações e gera arquivos de configuração do Terraform que representam fielmente a infraestrutura existente.

Essa abordagem automatizada simplifica o processo de importação de recursos para o Terraform, poupando tempo e esforço manual na criação dessas configurações. Além disso, o Terraformer suporta a importação incremental, permitindo que você atualize e gerencie recursos importados com facilidade.

## **3. Por que usar o Terraformer?**

O **Terraformer** oferece várias vantagens ao importar recursos existentes para o Terraform:

- **Automatização**: O Terraformer automatiza o processo de importação, poupando tempo e esforço manual.
- **Conformidade com as boas práticas do Terraform:** A ferramenta gera automaticamente arquivos de configuração do Terraform que seguem as melhores práticas recomendadas.
- **Suporte a vários provedores:** O Terraformer suporta diversos provedores de nuvem, permitindo importar recursos de diferentes ambientes.

## **4. Usando o Terraformer na prática**

Agora, vamos ver como utilizar o Terraformer na prática para importar um Network Security Group (NSG) do Azure para o Terraform.

**Instalação**: Para começar, instale o Terraformer seguindo as instruções do <a href="https://github.com/GoogleCloudPlatform/terraformer#installation" target="_blank">repositório oficial do Terraformer no GitHub</a>.

**Configuração das credenciais**: Antes de usar o Terraformer, é necessário configurar as credenciais do Azure. Você pode fazer isso definindo as variáveis de ambiente necessárias, como `ARM_SUBSCRIPTION_ID`, `ARM_TENANT_ID`, `ARM_CLIENT_ID` e `ARM_CLIENT_SECRET`. Essas variáveis são usadas para autenticar sua conta do Azure.

**Execução do Terraformer**: Agora, vamos executar o Terraformer para importar um NSG do Azure. Utilize o seguinte comando no terminal:

````bash
terraformer import azure --resources=nsg --filter=resource_group.name=my-resource-group --path=./output
````

- `--resources=nsg` indica que queremos importar um Network Security Group.
- `--filter=resource_group.name=my-resource-group` define que queremos importar o NSG de um determinado grupo de recursos chamado "my-resource-group".
- `--path=./output` especifica o diretório de saída onde os arquivos de configuração do Terraform serão gerados.

**Analisar os arquivos gerados**: Após a execução do comando, o Terraformer irá analisar o NSG no grupo de recursos especificado e gerar os arquivos de configuração do Terraform no diretório de saída. Vamos dar uma olhada em como esses arquivos se parecem.

No diretório de saída, você encontrará um arquivo `.tf` para cada recurso importado. No caso do NSG, o arquivo terá uma estrutura semelhante a esta:

````bash
resource "azurerm_network_security_group" "nsg" {
  name                = "my-nsg"
  location            = "eastus"
  resource_group_name = "my-resource-group"

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

  security_rule {
    name                       = "allow-ssh"
    priority                   = 200
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "22"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  // Outras regras de segurança podem ser definidas aqui, se existirem.
}
````

Esse arquivo de configuração do Terraform define o NSG e suas regras de segurança correspondentes. O **Terraformer** gera `tf/` `json` e `tfstate` da infraestrutura existente.

Resumindo, este é o fluxo de trabalho do Terraformer:

1. Os usuários especificam os recursos a serem importados (um por um ou especificando um escopo, com alguns recursos de filtro)
2. **terraformer** importa os recursos para o estado do terraform.
3. **terraformer** converte o estado em configuração com base no esquema do provedor (**terraform providers schema**)
4. Adiciona dependências cruzadas de recursos à configuração.

## **5. O que é o Terrafy?**

O **Terrafy** é uma ferramenta desenvolvida para simplificar a importação de recursos existentes do Microsoft Azure para o Terraform. Diferentemente do Terraformer, que é uma ferramenta mais abrangente, o **Terrafy** é específico para o Azure e oferece recursos especializados para facilitar a migração de recursos desse provedor para o Terraform.

O **Terrafy** foi projetado com o objetivo de simplificar o processo de importação, fornecendo uma configuração intuitiva e direta. Ele se concentra em oferecer suporte a recursos específicos do Azure, como Azure Functions, Azure Logic Apps, entre outros.

Com o **Terrafy**, você pode importar recursos do Azure para o Terraform de maneira mais eficiente. A ferramenta utiliza a API do Azure para obter informações sobre os recursos existentes, como máquinas virtuais, grupos de recursos, redes, políticas, entre outros. Em seguida, o Terrafy gera os arquivos de configuração do Terraform correspondentes.

## **6. Por que usar o Terrafy?**

O **Terrafy** oferece vantagens semelhantes ao Terraformer, com foco específico no Azure:

- **Simplicidade**: O Terrafy foi projetado para ser fácil de usar, com uma configuração simplificada.
- **Suporte especializado para o Azure**: O Terrafy oferece recursos específicos do Azure, como a importação de recursos do Azure Policy.

## **7. Usando o Terrafy na prática**

Agora, vamos explorar o uso do Terrafy para importar um Network Security Group (NSG) do Azure para o Terraform.

**Instalação**: Comece instalando o Terrafy seguindo as instruções disponíveis no <a href="https://github.com/Azure/aztfexport#install" target="_blank">repositório oficial do Terrafy no GitHub</a>.

**Configuração das credenciais**: Assim como no Terraformer, é necessário configurar as credenciais do Azure antes de usar o Terrafy. Consulte a documentação do Terrafy para obter instruções detalhadas sobre como configurar as credenciais.

**Execução do Terrafy**: Com o Terrafy instalado e as credenciais configuradas, podemos executar o Terrafy para importar um NSG do Azure. Utilize o seguinte comando:

````bash
terrafy import nsg my-resource-group
````

Nesse exemplo, estamos importando um NSG do grupo de recursos chamado `"my-resource-group"`.

**Analisar os arquivos gerados**: Após a execução do comando, o Terrafy analisará o NSG no grupo de recursos especificado e gerará os arquivos de configuração do Terraform. Vamos ver como esses arquivos são estruturados.

Os arquivos de configuração gerados pelo Terrafy para o NSG serão semelhantes a este exemplo:

````bash
resource "azurerm_network_security_group" "nsg" {
  name                = "my-nsg"
  location            = "eastus"
  resource_group_name = "my-resource-group"

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

  security_rule {
    name                       = "allow-ssh"
    priority                   = 200
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "22"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  // Outras regras de segurança podem ser definidas aqui, se existirem.
}
````

Esse arquivo de configuração do Terraform define o NSG e suas regras de segurança correspondentes.

Resumindo, este é o fluxo de trabalho do Terrafy:

- Executar **aztfy** <resource group name>a partir da linha de comando
- **aztfy** identifica automaticamente os recursos existentes que residem no grupo de recursos
- **aztfy** pede ao usuário para inserir o identificador de recurso do Terraform para cada recurso do Azure
- **aztfy** importa automaticamente cada recurso para o estado e converter o estado em uma configuração de terraform válida

## **8. Comparando o Terraformer x Terrafy**

Ao comparar o Terraformer e o Terrafy para importar recursos existentes do Azure para o Terraform, é importante entender suas diferenças e considerar suas respectivas vantagens. 

**Terraformer:**

- Suporta vários provedores de nuvem, incluindo Azure, AWS e Google Cloud Platform.
- Permite importar uma ampla variedade de recursos, como máquinas virtuais, redes, bancos de dados, balanceadores de carga, entre outros.
- É uma solução abrangente para importação de recursos existentes de diferentes provedores de nuvem.
- Requer configurações adicionais e ajustes manuais nos arquivos de configuração gerados.
- Oferece maior flexibilidade e suporte para recursos além do Azure.

**Limitações:**

- Código manual necessário para suportar um novo recurso. ou seja, você só pode importar os recursos suportados pelo terraformer, mesmo que sejam suportados pelo provedor
- A configuração gerada não tem garantia de validade
- As dependências cruzadas de recursos precisam ser mantidas manualmente por recurso e por provedor. Isso também não é confiável e pode estar incorreto.

**Terrafy:**

- Focado exclusivamente no Azure, oferecendo suporte especializado para importações nesse provedor de nuvem.
- Possui uma configuração simplificada e intuitiva.
- Inclui recursos específicos do Azure, como importação de recursos do Azure Policy, Azure Functions, Azure Logic Apps, entre outros.
- Projetado para facilitar a importação de recursos do Azure para o Terraform.
- Oferece uma experiência simplificada e direcionada para usuários que trabalham exclusivamente com o Azure.

**Limitações:**

- Você precisa precisa do terraform para gravar os arquivos .tfy
- Você precisa identificar os recursos para importar
- A configuração gerada não garante ser válida
- Sem dependências cruzadas de recursos

## **9. Conclusão**

Após testar ambas as ferramentas, é evidente que elas podem funcionar como uma maneira conveniente de codificar a infraestrutura existente, mas cada uma delas possui suas limitações. 

O **Terrafy** se destaca em termos de facilidade de uso, pois não requer muitas pré-configurações. Além disso, sua interface de linha de comando (CLI) oferece uma visão clara sobre quais recursos podem ou não podem ser importados.

Por outro lado, o **Terraformer** pode apresentar algumas configurações irrelevantes nos arquivos importados, o que pode resultar em erros e exigir ajustes para criar um plano de sucesso. Os arquivos gerados também tendem a ser estáticos, exigindo refatoração adicional para aumentar a capacidade de reutilização, como a definição de variáveis, saídas e até mesmo a criação de módulos reutilizáveis.

É importante ressaltar que os exemplos testados foram relativamente simples, e é fácil imaginar que um grupo de recursos possa conter muito mais elementos do que apenas uma máquina virtual ou um NSG. 

Em conclusão, é indiscutivelmente mais vantajoso e mais fácil definir a infraestrutura desde o início, em vez de importá-la posteriormente. No entanto, se a importação for necessária, é reconfortante saber que existem ferramentas disponíveis para acelerar esse processo, e tanto o **Terraformer** quanto o **Terrafy** podem ajudar a superar um dos maiores obstáculos: a definição dos recursos.

Cabe a você escolher a ferramenta que melhor se adapta às suas necessidades, considerando a facilidade de uso, a flexibilidade, as configurações personalizáveis ​​e outros fatores relevantes para o seu projeto. Lembre-se de avaliar cuidadosamente as limitações e considerar a complexidade da sua infraestrutura existente antes de decidir qual ferramenta utilizar.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
