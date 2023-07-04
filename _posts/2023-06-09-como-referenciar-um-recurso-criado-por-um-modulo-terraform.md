---
layout: post
title: "Como referenciar um recurso criado por um módulo Terraform"
date: 2023-06-09 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

este post, vamos discutir a importância de referenciar recursos criados por módulos Terraform e como fazer isso de forma eficiente. Aprender a referenciar corretamente os recursos é fundamental para garantir a integridade e a manutenibilidade de suas configurações de infraestrutura.

### **1. O que são Terraform e Módulos Terraform?**

Antes de mergulharmos nos detalhes sobre referenciar recursos de módulos Terraform, vamos entender o que são o Terraform e os módulos Terraform. O Terraform é uma ferramenta de infraestrutura como código (IaC) desenvolvida pela HashiCorp, que permite criar, modificar e gerenciar recursos de infraestrutura de forma declarativa. Com o Terraform, é possível descrever sua infraestrutura desejada em arquivos de configuração, chamados de arquivos de manifesto.

Os módulos Terraform, por sua vez, são blocos de construção reutilizáveis que encapsulam recursos e configurações específicos. Eles permitem criar componentes de infraestrutura consistentes, modularizados e compartilháveis. Os módulos podem ser criados por você mesmo ou disponibilizados pela comunidade, oferecendo uma maneira eficaz de reutilizar e compartilhar configurações.

Confira mais sobre módulos no Terraform em nosso artigo: <a href="https://unicast.com.br/posts/criando-seu-primeiro-modulo-no-terraform-azure/" target="_blank">Criando seu primeiro módulo no Terraform [Azure]</a>.

### **2. A Importância de referenciar módulos**

Quando você usa módulos Terraform em suas configurações, é essencial referenciar corretamente os recursos criados por esses módulos em outros lugares do seu código. Isso é importante para garantir que suas configurações estejam corretamente conectadas e que os recursos dependam uns dos outros da maneira esperada.

Ao referenciar um recurso de um módulo, você está estabelecendo uma dependência explícita entre esse recurso e outros recursos que o utilizam. Isso permite que o Terraform gerencie corretamente a ordem de criação, atualização e destruição desses recursos. Além disso, a referência adequada a recursos de módulos facilita a manutenção e a escalabilidade de suas configurações.

### **3. Identificar recursos e fontes de dados**

Primeiro, identifique os recursos e fontes de dados que podem ser separados em arquivos individuais. No nosso exemplo, temos um recurso de grupo de recursos, uma rede virtual e uma VM.

### **4. Exemplo de uso**

Vamos explorar um exemplo mais robusto para entender melhor como referenciar um recurso criado por um módulo Terraform em outro módulo no Microsoft Azure. Além disso, veremos como utilizar o `depends_on` para estabelecer a ordem correta de criação dos recursos.

Suponha que você esteja desenvolvendo uma infraestrutura na Azure e tenha dois módulos: um para provisionar uma instância de banco de dados SQL e outro para criar um servidor de aplicativos.

**Definindo a saída no módulo do banco de dados SQL**

No módulo responsável por criar a instância de banco de dados SQL, vamos definir uma saída que represente o nome do servidor de banco de dados criado. 

````bash
output "database_server_name" {
  value = azurerm_mssql_server.example.name
}
````

Essa saída será utilizada posteriormente para referenciar o servidor de banco de dados no outro módulo.

**Referenciando a saída do módulo na configuração do servidor de aplicativos**

No módulo responsável por criar o servidor de aplicativos, você pode referenciar a saída do módulo do banco de dados SQL. Suponha que você queira configurar a conexão do servidor de aplicativos com o banco de dados. Adicione o seguinte código no arquivo main.tf desse módulo:

````bash
module "database" {
  source = "./modules/database"

  // Configurações do módulo de banco de dados SQL
}

resource "azurerm_app_service" "example" {
  name                = "meu-servidor-de-aplicativos"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  depends_on = [
    module.database
  ]

  app_settings = {
    "DATABASE_SERVER_NAME" = module.database.database_server_name
  }
}
````

No exemplo acima, estamos utilizando a cláusula `depends_on` para estabelecer a dependência do recurso `azurerm_app_service.example` em relação ao módulo `module.database`. Isso garante que o Terraform criará o módulo do banco de dados SQL antes de criar o servidor de aplicativos.

Além disso, estamos referenciando a saída `database_server_name` do módulo de banco de dados SQL utilizando `module.database.database_server_name`. Essa referência permite que o servidor de aplicativos utilize corretamente o nome do servidor de banco de dados na sua configuração.

### **5. Conclusão**

É importante ressaltar que este foi um exemplo básico para ilustrar os conceitos de referência entre módulos e dependência. Na prática, você pode criar infraestruturas muito mais complexas e escaláveis usando esses conceitos.

Ao utilizar módulos Terraform e estabelecer corretamente as referências e dependências, você ganha flexibilidade e reutilização em sua infraestrutura como código. Pode criar componentes modulares que representam diferentes partes de sua arquitetura e combiná-los de forma inteligente para criar ambientes consistentes e confiáveis.

Lembre-se de consultar a documentação oficial do Terraform e do provedor Azure para obter informações detalhadas sobre como referenciar recursos e definir dependências específicas para suas configurações.

À medida que você se aprofunda no uso do Terraform, explorando seus recursos avançados e combinando módulos de maneira inteligente, poderá criar infraestruturas altamente automatizadas e eficientes na nuvem. A capacidade de referenciar recursos entre módulos é um recurso poderoso que ajudará você a alcançar esses objetivos.

Continue aprendendo e experimentando com o Terraform, explorando diferentes cenários e desafios. A prática e a experiência serão seus melhores aliados para dominar essa ferramenta de infraestrutura como código e impulsionar sua jornada na automação e provisionamento de recursos na nuvem Azure.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
