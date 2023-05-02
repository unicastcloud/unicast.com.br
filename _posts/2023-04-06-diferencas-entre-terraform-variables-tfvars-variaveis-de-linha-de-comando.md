---
layout: post
title: "Diferenças entre Terraform variable.tf, terraform.tfvars e variáveis de linha de comando"
author: asilva
date: 2023-04-06 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Se você é novo no Terraform ou está procurando uma maneira melhor de gerenciar suas variáveis, este post é para você! 

As variáveis ​​no Terraform são usadas para inserir valores em seu código. Com variáveis, você pode separar as configurações dos códigos e configurar facilmente diferentes ambientes. 

Existem três tipos diferentes de variáveis no Terraform:

- **Variáveis locais** - definidas dentro de um módulo e só podem ser usadas dentro dele.
- **Variáveis de entrada** - definidas fora do módulo e passadas como argumentos para ele.
- **Variáveis ​​de ambiente** - armazenadas em um arquivo de ambiente.

Neste post, vamos discutir as diferenças entre **variable.tf**, **terraform.tfvars** e **variáveis de linha de comando** e como usar cada uma delas para gerenciar suas configurações do Azure com o Terraform.

### **1. Por que usar variáveis no Terraform?**

As variáveis ​​são uma parte fundamental do Terraform, pois permitem que você passe valores para seu código sem precisar hard-coded. Isso torna seu código mais flexível e reutilizável, permitindo que você o use em diferentes ambientes sem ter que fazer mudanças significativas. Além disso, ao separar as variáveis ​​do seu código, você pode proteger informações confidenciais, como senhas e chaves de API.

### **2. Usando o arquivo variable.tf**

O arquivo `variable.tf` é usado para definir variáveis que serão usadas em todo o seu código. Este arquivo é definido como um módulo e contém as definições de variáveis. A vantagem de usar o `variable.tf` é que ele mantém todas as variáveis em um só lugar, o que torna mais fácil gerenciar e manter a consistência em seu código. Por exemplo, digamos que você esteja criando uma infraestrutura no Azure e queira definir a região do Azure a ser usada. Aqui está um exemplo de código usando `variable.tf` para definir essa variável:

````bash
variable "azure_region" {
  type    = string
  default = "eastus"
}
````

Usando o arquivo **variable.tf**, você pode definir facilmente outras variáveis como o nome do grupo de recursos, o tamanho da instância ou a versão do sistema operacional, que serão usadas em todo o seu código.

### **3. Usando o arquivo terraform.tfvars**

O arquivo `terraform.tfvars` é um arquivo separado que contém valores para as variáveis definidas em variable.tf. Este arquivo é opcional, mas é uma maneira fácil de definir valores padrão para suas variáveis. A vantagem de usar o `terraform.tfvars` é que ele pode ser facilmente compartilhado com outros membros da equipe ou usado em diferentes ambientes. Por exemplo, digamos que você queira definir o nome de um recurso do Azure que será usado em todo o código. Aqui está um exemplo de código usando o `terraform.tfvars` para definir essa variável:

````bash
azure_resource_name = "my-azure-resource"
````

Ao usar o arquivo **terraform.tfvars**, você pode definir facilmente outras variáveis como o nome do grupo de recursos, o tamanho da instância ou a versão do sistema operacional que serão usadas em todo o seu código.

### **4. Usando variáveis de linha de comando**

As variáveis de linha de comando são usadas para passar valores para variáveis no momento da execução. Isso é útil se você quiser substituir valores em tempo real. A vantagem de usar variáveis de linha de comando é que você pode passar valores diferentes para as variáveis cada vez que executa o comando do Terraform. Por exemplo, digamos que você queira definir o tamanho da instância do Azure de maneira dinâmica. Aqui está um exemplo de código usando variáveis de linha de comando para definir essa variável:

````bash
terraform plan -var "azure_instance_size=Standard_D2_v3"
````

Ao usar variáveis de linha de comando, você pode definir facilmente outras variáveis como a região do Azure, o nome do grupo de recursos ou a versão do sistema operacional.

### **5. Conclusão**

Ao escolher entre **variable.tf**, **terraform.tfvars** e **variáveis de linha de comando**, é importante considerar as necessidades do seu projeto e da sua equipe. Usar o arquivo variable.tf pode ser a melhor opção se você tiver várias variáveis a serem definidas e quiser manter todas em um só lugar. 

Usar o arquivo terraform.tfvars pode ser uma boa opção se você quiser definir valores padrão para suas variáveis ou compartilhar facilmente esses valores com outros membros da equipe. E, finalmente, usar variáveis de linha de comando pode ser a melhor opção se você quiser substituir valores em tempo real ou passar valores diferentes para as variáveis cada vez que executa o comando do Terraform.

Compreender as diferenças entre variable.tf, terraform.tfvars e variáveis de linha de comando é essencial para aproveitar ao máximo o Terraform ao provisionar recursos de infraestrutura no Azure. Esperamos que este post tenha sido útil para você e sua equipe. 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
