---
layout: post
title: "Importando recursos existentes do Azure para o Terraform"
author: asilva
date: 2023-05-22 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Nesta série de artigos, estamos explorando as diversas funcionalidades e recursos dessa ferramenta poderosa para automação de infraestrutura. 

Hoje, vamos abordar um tema importante para quem está migrando para a infraestrutura como código: como importar recursos existentes do **Azure** para o **Terraform**. Vamos explorar o poderoso recurso do Terraform chamado "**import**" e entender como utilizá-lo de forma eficiente.

## **1. O que é o Terraform Import?**

O **Terraform** **Import** é um comando do Terraform que permite importar recursos existentes de provedores de infraestrutura para o estado do Terraform. Ele é usado para trazer recursos já existentes, criados manualmente ou por outros meios, para dentro do controle do Terraform, permitindo que você gerencie esses recursos como código.

Ao importar um recurso com o **Terraform Import**, você está trazendo as informações desse recurso para o estado do Terraform, o que permite ao Terraform ciente da existência desse recurso e também de suas configurações atuais.

Essa funcionalidade é útil quando você já possui infraestrutura existente e deseja começar a gerenciá-la com o Terraform. Ao importar recursos, você pode aproveitar todos os benefícios do Terraform, como automação, controle de versão e colaboração.

O **Terraform** **Import** é um recurso poderoso, mas também requer atenção aos detalhes. Cada provedor de infraestrutura tem sua própria sintaxe e identificadores únicos para os recursos. É importante consultar a documentação do provedor, como no caso do Azure, para entender como identificar corretamente os recursos que você deseja importar.

## **2. Como funciona a importação no Terraform?**

Quando você realiza a importação de um recurso, o Terraform cria uma entrada correspondente no estado do Terraform para representar esse recurso. Essa entrada no estado é utilizada para rastrear o estado atual do recurso e gerenciar as alterações subsequentes.

Ao importar um recurso, você está essencialmente dizendo ao Terraform: **"Ei, este recurso já existe, e eu quero gerenciá-lo usando o Terraform"**.

**Aqui está como o processo de importação funciona passo a passo:**

- **Definição do recurso no arquivo de configuração:** Antes de importar um recurso, você precisa definir sua configuração no arquivo de configuração do Terraform (*.tf). Nesse arquivo, você descreve as propriedades do recurso, como nome, localização, tamanho, etc., usando a sintaxe específica do provedor. No caso do Azure, você usaria o provedor `azurerm` e especificaria o tipo do recurso, como `azurerm_virtual_machine` ou `azurerm_resource_group`.
- **Identificação do recurso no provedor:** Após definir o recurso no arquivo de configuração, você precisa identificar o recurso específico no provedor de infraestrutura. Cada recurso no provedor tem um identificador único que o Terraform utiliza para localizá-lo. Por exemplo, no Azure, o identificador de um grupo de recursos seria algo como `/subscriptions/{subscription_id}/resourceGroups/{nome_grupo_recursos}`.
- **Execução do comando de importação:** Com o recurso definido no arquivo de configuração e o identificador do recurso no provedor, você pode executar o comando de importação no Terraform. 

O comando de importação tem a seguinte sintaxe:

````bash
terraform import <recurso_terraform> <identificador_recurso_provedor>
}
````

- `<recurso_terraform>`: É o nome do recurso que você definiu no arquivo de configuração do Terraform.
- `<identificador_recurso_provedor>`: É o identificador único do recurso no provedor de infraestrutura. Ele varia dependendo do provedor e do tipo de recurso.

Ao executar o comando de importação, o Terraform consulta o provedor de infraestrutura, localiza o recurso correspondente com base no identificador fornecido e obtém as informações necessárias para criar o estado correspondente no arquivo `terraform.tfstate`.

É importante ressaltar que a importação no Terraform não gera automaticamente a configuração correspondente no arquivo de configuração. Ela apenas traz o recurso para o estado do Terraform. Isso significa que, após a importação, você precisará atualizar manualmente o arquivo de configuração do Terraform para refletir as configurações desejadas.

## **3. Importando um Resource Group do Azure**

Vamos agora ao exemplo prático de como importar um recurso do Azure para o Terraform. Vamos considerar o cenário de importar um grupo de recursos existente no Azure.

Para importar um **Resource Group** existente do Azure para o Terraform, siga os passos abaixo:

**Passo 1: Definição do recurso no arquivo de configuração:**

No arquivo de configuração do Terraform (*.tf), você precisa definir o recurso `azurerm_resource_group` para representar o Resource Group. Aqui está um exemplo básico de como isso pode ser feito:

````bash
resource "azurerm_resource_group" "meu_resource_group" {
  name     = "nome_do_resource_group"
  location = "localizacao_do_resource_group"
}
````

**Passo 2: Identificação do Resource Group no Azure:**

Antes de prosseguir com a importação, você precisa obter o identificador exclusivo (ID) do Resource Group que deseja importar. No Azure, você pode obter essa informação através da **interface de linha de comando (CLI)** do Azure ou do **portal do Azure**. O identificador terá o seguinte formato: `/subscriptions/{subscription_id}/resourceGroups/{nome_do_resource_group}`.

**Via Portal do Azure**

Vá em Resource groups > meu_resource_group > Properties.

![](/assets/img/70/tf-impor01.png){: "width=60%" }

**Via CLI do Azure**

Utilize o comando `az group show`

````bash
az group show -n meu_resource_group
````

![](/assets/img/70/tf-import02.png){: "width=60%" }

**Passo 3: Execução do comando de importação:**

Com o recurso devidamente definido no arquivo de configuração e o identificador do Resource Group em mãos, você pode executar o comando de importação do Terraform. 

No terminal, utilize o seguinte comando:

````bash
terraform import azurerm_resource_group.meu_resource_group /subscriptions/{subscription_id}/resourceGroups/{nome_do_resource_group}
````

Substitua `{subscription_id}` pelo ID da assinatura do Azure e `{nome_do_resource_group}` pelo nome do seu Resource Group.

Ao executar o comando de importação, o Terraform irá contatar o Azure, localizar o Resource Group correspondente com base no identificador fornecido e obter as informações necessárias para criar o estado correspondente no arquivo `terraform.tfstate`.

Aqui está um exemplo de output que você pode esperar ao importar o Resource Group:

````bash
azurerm_resource_group.meu_resource_group: Importando do Azure...
azurerm_resource_group.meu_resource_group: Importação concluída!

Importação bem-sucedida! O Resource Group foi importado para o estado do Terraform. Agora você pode gerenciar esse recurso utilizando o Terraform.
````

Após a importação, você precisa atualizar manualmente o arquivo de configuração para refletir as configurações desejadas.

A importação de um **Resource Group** é apenas um exemplo. Você pode aplicar esse processo a outros recursos do Azure, como **máquinas virtuais**, **redes virtuais**, **bancos de dados**, entre outros.

A partir desse ponto, você poderá utilizar os recursos do Terraform para gerenciar e fazer alterações nesse Resource Group. Por exemplo, você pode atualizar as configurações do Resource Group no arquivo de configuração do Terraform, como adicionar tags, e em seguida, aplicar as alterações usando o comando terraform apply.

## **4. Conclusão**

A importação de recursos existentes do Azure para o Terraform é um processo essencial quando você deseja começar a gerenciar sua infraestrutura existente como código. O Terraform Import permite trazer recursos já criados manualmente ou por outros meios para dentro do controle do Terraform, proporcionando automação, controle de versão e colaboração.

Neste artigo, exploramos em detalhes o que é o Terraform Import, como funciona e como importar um Resource Group do Azure como exemplo. Vimos que a importação envolve a definição do recurso no arquivo de configuração, a identificação do recurso no provedor e a execução do comando de importação.

Embora a importação de infraestrutura existente para o Terraform exija esforço e atenção aos detalhes, os benefícios são significativos. Com o Terraform, você pode gerenciar sua infraestrutura de forma mais eficiente, escalável e reproduzível. 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
