---
layout: post
title: "Trabalhando com múltiplos ambientes no Terraform [Azure]"
author: asilva
date: 2023-03-18 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Se você já trabalhou com **Terraform**, sabe que é uma ferramenta incrível para criar e gerenciar infraestruturas de maneira consistente e automatizada. Uma das maiores vantagens do **Terraform** é a sua capacidade de trabalhar com diferentes ambientes, o que permite a criação de várias infraestruturas independentes e que podem ser gerenciadas de maneira individual.

Mas como podemos gerenciar várias infraestruturas usando Terraform sem repetir código e mantendo tudo organizado e escalável? 

Bem, a resposta é usar boas práticas de gerenciamento de configuração, como o famoso **"Don't Repeat Yourself" (DRY)**, que é um princípio fundamental de programação que afirma que a informação deve ser armazenada em um único local e referenciada em outros locais, em vez de ser duplicada.

Isso significa que precisamos criar uma estrutura de gerenciamento de configuração que nos permita criar infraestruturas para diferentes ambientes, como desenvolvimento, teste e produção, sem repetir o mesmo código em cada ambiente.

Com esse princípio em mente, vamos explorar duas estratégias principais para gerenciar infraestrutura em múltiplos ambientes no Terraform: separando por diretórios ou por workspaces.

### **1. Separando por diretórios**

A primeira estratégia consiste em separar as definições de recursos por diretórios, um para cada ambiente.

Essa estratégia é simples e direta: você cria um diretório para cada ambiente e mantém os arquivos de configuração do Terraform separados. Cada ambiente tem seus próprios arquivos de configuração, o que significa que você pode ajustar as configurações de cada ambiente de acordo com suas necessidades.

Por exemplo, suponha que você esteja usando o Microsoft Azure e queira criar uma instância de máquina virtual (VM) para cada ambiente. Você pode criar três diretórios, um para cada ambiente: dev, staging e produção. Em cada diretório, você cria um arquivo de configuração do Terraform chamado "main.tf" que contém a definição da sua VM. A estrutura de pastas ficaria assim:

```
├── environments
│   ├── development
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── staging
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── production
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── modules
│   └── ...
├── providers
│   └── ...
├── variables.tf
├── outputs.tf
├── main.tf
└── terraform.tfstate
```

Com essa estratégia, podemos ter uma configuração específica para cada ambiente, sem precisar repetir o mesmo código várias vezes. Usando variáveis ​​do Terraform, podemos configurar as diferenças entre cada ambiente.

```bash
# environments/development/main.tf

module "azure_vm" {
  source = "../../modules/azure/vm"
  vm_name = "dev-vm"
  vm_size = "Standard_B2s"
  os_disk_size_gb = "30"
  admin_username = var.admin_username
  admin_password = var.admin_password
  resource_group_name = "dev-rg"
  location = "eastus"
}
```

```bash
# environments/staging/main.tf

module "azure_vm" {
  source = "../../modules/azure/vm"
  vm_name = "staging-vm"
  vm_size = "Standard_D2s_v3"
  os_disk_size_gb = "128"
  admin_username = var.admin_username
  admin_password = var.admin_password
  resource_group_name = "staging-rg"
  location = "eastus2"
}
```

Cada diretório dentro de ```environments``` contém as definições de recursos específicas para cada ambiente, enquanto o diretório ```modules``` contém módulos reutilizáveis que podem ser usados em vários ambientes.

Para usar essa estratégia, é necessário criar um arquivo terraform.tfvars em cada diretório, contendo as variáveis específicas de cada ambiente. 

Por exemplo, em **environments/dev/terraform.tfvars** podemos ter:

```bash
resource_group_name = "dev-rg"
vm_name = "dev-vm"
```

Em **environments/staging/terraform.tfvar**s, podemos ter:

```bash
resource_group_name = "staging-rg"
vm_name = "staging-vm"
```

E assim por diante.

Com essa estratégia, podemos manter uma estrutura organizada e facilmente identificável para cada ambiente, além de podermos aplicar alterações específicas para cada um deles. 

No entanto, pode ser difícil gerenciar vários ambientes ao mesmo tempo, uma vez que precisamos entrar em cada diretório para aplicar as alterações.

### **2. Separando por workspaces**

A segunda estratégia envolve o uso de **workspaces** no **Terraform** para manter o código para cada ambiente em um único diretório. Um **workspace** é uma instância isolada de um conjunto de recursos no **Terraform**. Por padrão, o Terraform tem um único workspace chamado "**default**", mas você pode criar novos workspaces para gerenciar recursos em diferentes ambientes. Por exemplo, você pode ter um workspace "**dev**", um "**stage**" e um "**prod**".

O **workspace** é um recurso do **Terraform** que permite que você mantenha vários conjuntos de recursos em um único diretório de código. Cada **workspace** tem seu próprio estado, permitindo que você mantenha diferentes recursos com as mesmas definições de recursos em arquivos de configuração separados.

Para utilizar **workspaces** no workspace primeiro, você precisa definir o **backend** do Terraform para armazenar o estado em um local comum para todos os workspaces. 

No exemplo abaixo, o estado será armazenado no **Azure Blob Storage**.

```bash
terraform {
  backend "azurerm" {
    resource_group_name  = "tfstate-rg"
    storage_account_name = "tfstateacc"
    container_name       = "tfstate"
    key                  = "terraform.tfstate"
  }
}
```

Em seguida, você pode criar um novo **workspace** com o comando terraform workspace new, selecionar um **workspace** existente com o comando terraform workspace select e listar todos os workspaces com o comando terraform workspace list.

Por exemplo, para criar um novo workspace "**dev**":

```bash
$ terraform workspace new dev
Created and switched to workspace "dev"!
```

Por exemplo, para selecionar um outro workspace "**prod**":

```bash
$ terraform workspace select prod
```

Depois de criar o workspace, você pode definir os recursos para ele em seus arquivos de configuração. No exemplo abaixo, um grupo de recursos do **Azure** é criado para o workspace "**dev**":

```bash
# main.tf
resource "azurerm_resource_group" "rg" {
  name     = "rg-${terraform.workspace}"
  location = "eastus"
}
```

Nesse exemplo, ```${terraform.workspace}``` é usado para referenciar o nome do workspace atual.

Na estratégia de **workspaces**, não precisamos criar diretórios separados para cada ambiente, o que pode ser útil para gerenciar muitos ambientes.

**Exemplo de estrutura completa de arquivos para separação por workspaces:**

```
├── environments
│   ├── development
│   │   ├── dev.tfvars
│   ├── staging
│   │   ├── stage.tfvars
│   └── production
│       └── prod.tfvars
├── modules
│   └── ...
├── providers
│   └── ...
├── variables.tf
├── outputs.tf
├── main.tf
└── terraform.tfstate
```

Desta forma, você pode facilmente implantar recursos em diferentes ambientes, apenas selecionando o **environment** correto:

Por exemplo, implantar recursos no ambiente de produção "**prod**":

```bash
$ terraform workspace select prod
$ terraform plan -var-file=/environments/production/prod.tfvars
```

Isso criará um blob de armazenamento separado para cada workspace chamado **dev.tfstate** e **prod.tfstate**, armazenados no contêiner na conta de armazenamento especificada no arquivo **backend.tf**.

Agora, quando você executar ```terraform apply```, o **Terraform** usará o blob de armazenamento apropriado para cada ambiente, dependendo do workspace atual.

### **3. Comparando as duas estratégias**

Agora que já vimos as duas estratégias de organização de arquivos e pastas do Terraform para trabalhar com múltiplos ambientes, é importante comparar suas vantagens e desvantagens.

**Separando por diretórios:**

**Prós:**

- Cada ambiente tem sua própria configuração, o que significa que você pode ajustar as configurações de cada ambiente de acordo com suas necessidades.
- É fácil visualizar e gerenciar as configurações de cada ambiente.

**Contras:**

- Pode ser tedioso manter vários arquivos separados, especialmente se houver muitas semelhanças entre eles.
- É fácil esquecer de aplicar uma alteração em todos os ambientes, qualquer mudança no código precisa ser replicada em todos os ambientes.
- Requer duplicação de arquivos em cada ambiente.

**Separando por workspaces:**

**Prós:**

- Mais fácil de gerenciar muitos ambientes
- Usa uma única fonte de verdade para o código, não há duplicação, pois um único conjunto de arquivos de configuração é usado para todos os ambientes.
- Permite que você crie instâncias separadas da infraestrutura para cada ambiente usando workspaces.
- A alternância entre os workspaces é fácil e conveniente, permitindo a criação de várias infraestruturas sem precisar alterar o código Terraform.

**Contras:**

- Requer mais cuidado para evitar conflitos entre recursos em diferentes workspaces
- A configuração compartilhada precisa ser gerenciada com cuidado para garantir que seja aplicada em todos os workspaces

Em geral, a estratégia de separação por **workspaces** é mais **flexível** e **escalável** para projetos maiores, pois elimina a duplicação de código e permite que você use um único arquivo de configuração para todos os ambientes. 

No entanto, a estratégia de separação por diretórios pode ser mais fácil de implementar e entender para projetos menores ou menos complexos.

É importante escolher a estratégia que melhor atenda às necessidades específicas do seu projeto. Em alguns casos, pode até ser possível usar uma combinação de ambas as estratégias para atender às suas necessidades.

### **4. Conclusão**

Ao trabalhar com múltiplos ambientes no **Terraform**, existem várias estratégias que você pode usar. Neste artigo, discutimos duas estratégias: **separando por diretórios** e **separando por workspaces**. Ambas têm prós e contras, e cabe a você decidir qual é a melhor para a sua situação. 

Independentemente da estratégia que você escolher, é importante seguir o princípio **"não repita a si mesmo"** e criar módulos reutilizáveis para a configuração compartilhada. Dessa forma, você pode evitar repetições desnecessárias e manter a configuração centralizada e fácil de gerenciar.

Lembre-se de sempre buscar boas práticas e atualizações do Terraform para manter sua infraestrutura atualizada e segura.

É isso galera, espero que gostem!

Forte Abraço!
