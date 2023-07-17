---
layout: post
title: "Terraform 1.5 traz importação e verificações orientadas por configuração"
author: asilva
date: 2023-06-16 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

O Terraform é uma ferramenta popular para provisionamento de infraestrutura como código. A cada nova versão, a equipe de desenvolvimento adiciona melhorias e recursos para aprimorar a experiência do usuário. 

A <a href="https://www.hashicorp.com/blog/terraform-1-5-brings-config-driven-import-and-checks" target="_blank"> versão 1.5</a> do Terraform traz duas funcionalidades importantes: **importação de recursos existentes** **(Config-driven import)** e **verificações orientadas por configuração** **(Enhanced validation with checks)**. 

Neste artigo, exploraremos em detalhes esses recursos e discutiremos como eles podem beneficiar os usuários do Terraform.

### **1. Importação de Recursos Existente**

A importação de recursos existentes é um recurso muito aguardado pelos usuários do Terraform. Anteriormente, quando um usuário começava a gerenciar sua infraestrutura com o Terraform, era necessário criar todos os recursos do zero. Com a versão **1.5**, é possível importar recursos existentes em um estado do Terraform, permitindo que você comece a gerenciá-los facilmente.

Imagine que você já tenha provisionado um serviço de armazenamento em nuvem da Azure manualmente. Agora, com a importação de recursos, você pode adicionar esse storage ao seu estado do Terraform. Isso significa que você poderá gerenciá-lo como qualquer outro recurso definido no código Terraform. 

Isso facilita a adoção do Terraform em projetos já em andamento e evita a necessidade de recriar recursos manualmente.

### **2. Verificações Orientadas por Configuração**

Outra melhoria importante do Terraform **1.5** são as verificações orientadas por configuração. Essa funcionalidade permite que você defina regras e políticas para garantir que sua infraestrutura esteja configurada corretamente. Anteriormente, a validação de configurações era feita principalmente por meio de ferramentas externas ou scripts personalizados.

Agora, com as verificações orientadas por configuração, você pode adicionar verificações diretamente ao seu código Terraform. 

Por exemplo, você pode definir regras para garantir que determinadas configurações de segurança estejam presentes em todos os recursos provisionados. Isso ajuda a garantir a conformidade com as políticas internas e a reduzir erros de configuração.

### **3. Exemplo de uso**

Vamos ver alguns exemplos de uso desses recursos na prática.

**Importação de Recursos Existente**

Suponha que você tenha provisionado um banco de dados no serviço de banco de dados do Azure manualmente. Para importar esse recurso no Terraform, você pode executar o seguinte comando:

No módulo responsável por criar a instância de banco de dados SQL, vamos definir uma saída que represente o nome do servidor de banco de dados criado. 

````bash
terraform import azurerm_sql_database.example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/databases/mydatabase
````

Isso adicionará o recurso do banco de dados ao estado do Terraform e permitirá que você gerencie-o por meio do código.

**Verificações Orientadas por Configuração**

Vamos supor que você deseje garantir que todas as suas máquinas virtuais no Azure tenham tags especificadas. Você pode adicionar a seguinte verificação ao seu código Terraform:

````bash
resource "azurerm_virtual_machine" "example" {
  # Configurações do recurso

  tags = {
    Environment = "Production"
  }

  # Verificação
  lifecycle {
    prevent_destroy = true
    ignore_changes = [
      tags,
    ]

    pre_apply {
      # Verifica se as tags obrigatórias estão presentes
      condition = var.prevent_vm_tags && length(keys(self.tags)) < 1
      error_message = "Este recurso requer pelo menos uma tag: Environment."
    }
  }
}
````

Nesse exemplo, a verificação garante que as máquinas virtuais no Azure tenham pelo menos uma tag: `"Environment"`. Se alguma máquina virtual não cumprir essa regra, o Terraform exibirá um erro durante a execução do plano ou da aplicação.

### **4. Conclusão**

O Terraform **1.5** trouxe duas funcionalidades importantes: importação de recursos existentes e verificações orientadas por configuração. Esses recursos permitem que os usuários gerenciem facilmente recursos já provisionados e garantam a conformidade de sua infraestrutura. Ao adotar essas novas funcionalidades, é possível agilizar o gerenciamento de infraestrutura e reduzir erros de configuração.

É importante ressaltar que já temos dois artigos anteriores abordando a importação de recursos no Terraform, fornecendo informações valiosas sobre como aproveitar esse recurso. 

Em breve, traremos um novo artigo que explorará em profundidade as novas funcionalidades da versão **1.5** do Terraform, permitindo que você maximize o potencial dessa ferramenta no provisionamento de sua infraestrutura.

Continue acompanhando nossos artigos e atualizações para estar sempre atualizado com as últimas novidades do Terraform e aprender novas maneiras de otimizar e simplificar o gerenciamento da sua infraestrutura como código.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!

