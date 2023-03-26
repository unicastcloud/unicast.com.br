---
layout: post
title: "10 Boas Praticas para projetos de Terraform"
author: asilva
date: 2023-03-11 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Com o aumento da popularidade da infraestrutura como código (IaC), o **Terraform** se tornou uma ferramenta essencial para gerenciar a infraestrutura em nuvem.

Neste artigo, vamos explorar algumas boas práticas que podem ajudar a tornar sua configuração do Terraform mais organizada, eficiente e fácil de se manter. 

Esta é mais uma continuação dos artigos que temos publicado sobre **Terraform**, mostrando como tornar o seu uso mais produtivo e seguro. Então, se você já trabalha com o Terraform ou está pensando em começar, continue lendo para descobrir essas dicas valiosas.

### **1. Use remote state**

O **Remote State** é um recurso do Terraform que permite armazenar o estado da infraestrutura em um local centralizado, como um bucket do S3 na AWS ou um blob do Azure Storage na Microsoft Azure. Isso pode ajudar a manter o estado da infraestrutura consistente em todas as instâncias do Terraform, o que pode ajudar a evitar erros e garantir que todos estejam trabalhando com a versão mais recente da configuração.

Isso permite que sua equipe trabalhe em conjunto e evita conflitos de versão, além de permitir compartilhar o estado entre diferentes ambientes. 

Imagine que você precisa gerenciar sua infraestrutura em nuvem em um projeto com várias pessoas. Se cada pessoa usar um arquivo de estado diferente, pode ser difícil rastrear o que foi alterado em cada um dos arquivos.

O armazenamento remoto do estado é uma das melhores e mais importantes práticas do Terraform.

### **2. Utilize o modelo de partial configuration**

**Partial Configuration** é um modelo de configuração do Terraform que permite dividir a configuração em vários arquivos e módulos. Isso pode ajudar a manter a configuração organizada e modular, o que pode torná-la mais fácil de entender e manter. Além disso, isso pode ajudar a promover a reutilização de código e a facilitar a colaboração entre membros da equipe.

A utilização do modelo de **partial configuration** no Terraform não apenas ajuda na organização do seu código, mas também pode auxiliar na estratégia de não utilizar hard codes e informações de login e secrets. Você pode gerenciar variáveis sensíveis de forma mais segura, evitando que informações importantes sejam expostas acidentalmente. 

### **3. Utilize uma convenção de nomenclatura consistente**

Usar uma **convenção de nomenclatura** consistente pode parecer uma prática simples, mas pode ajudar muito na organização e na legibilidade do seu código. Além disso, seguir uma convenção padrão pode ajudar a manter uma estrutura consistente em todos os projetos, facilitando a colaboração entre a equipe.

Ao definir uma **convenção de nomenclatura**, leve em consideração o tipo de recurso que está sendo criado, o ambiente em que está sendo utilizado e a finalidade do recurso. Por exemplo, você pode prefixar seus recursos com um identificador que indique a região geográfica em que estão localizados, ou adicionar um sufixo que indique a finalidade do recurso.

Alguns exemplos de convenções de nomenclatura que você pode considerar para seus recursos:

- Use letras minúsculas separadas por traços para nomes de recursos
- Utilize um prefixo para identificar o ambiente em que o recurso está sendo criado (por exemplo, "prod-" para produção, "dev-" para desenvolvimento)
- Use sufixos para identificar o tipo de recurso (por exemplo, "-lb" para load balancers, "-vm" para máquinas virtuais)
- Evite usar caracteres especiais ou abreviações confusas
- Mantenha a nomenclatura consistente em todos os projetos.

Seguir uma **convenção de nomenclatura** consistente pode ajudar a manter a organização e a legibilidade do seu código, facilitando a colaboração com sua equipe e tornando a manutenção da infraestrutura mais fácil.

### **4. Use módulos sempre que possível**

O uso de **módulos** no Terraform é uma prática fundamental para manter a organização e a reutilização de código em sua infraestrutura. Ao agrupar recursos relacionados em um único módulo, você pode tornar sua configuração mais modular, reutilizável e fácil de manter.

Além disso, o uso de **módulos** pode ajudar a promover a padronização e a consistência em toda a sua infraestrutura, uma vez que você pode definir padrões de nomenclatura, estrutura e configuração dentro de seus **módulos**.

Ao criar **módulos**, é importante pensar em sua estrutura e em como eles serão usados em sua infraestrutura. Por exemplo, você pode criar módulos para gerenciar recursos como instâncias de máquinas virtuais, banco de dados, redes, etc.

Algumas dicas para ajudar você a usar **módulos** de forma eficiente no Terraform:

- Pense em termos de abstração e agrupe recursos relacionados em um módulo
- Defina uma interface clara para o seu módulo, documentando o que o módulo faz e como ele deve ser usado
- Siga uma convenção de nomenclatura consistente para seus módulos
- Evite hard codes de variáveis em seus módulos
- Certifique-se de que seus módulos sejam testados e validados antes de serem usados em produção.

O uso de **módulos** no Terraform pode ajudar a tornar sua infraestrutura mais organizada, modular e fácil de manter. Certifique-se de seguir as práticas recomendadas e as convenções de nomenclatura para criar módulos eficientes e reutilizáveis.

### **5. Tire proveito dos Dynamic Blocks**

Os **Dynamic Blocks** são uma das funcionalidades mais poderosas do Terraform. Eles permitem que você crie blocos de recursos dinâmicos com base em dados externos, o que pode ajudar a simplificar sua configuração e torná-la mais flexível.

Com **Dynamic Blocks**, você pode criar recursos que dependem de dados externos, como variáveis, listas ou mapas. Por exemplo, você pode criar um recurso que depende do número de instâncias que você deseja criar ou do tamanho de uma lista de endereços IP.

Algumas dicas para aproveitar ao máximo os **Dynamic Blocks** no Terraform:

- Pense em quais recursos podem se beneficiar do uso de Dynamic Blocks
- Use Dynamic Blocks para criar recursos que variam em tamanho ou dependem de dados externos
- Evite duplicação de código usando Dynamic Blocks em vez de criar recursos separados para cada instância ou elemento de dados
- Use a sintaxe correta para Dynamic Blocks, seguindo a documentação oficial do Terraform
- Certifique-se de que seus Dynamic Blocks estejam formatados e validados corretamente antes de implantar sua configuração.

Ao usar Dynamic Blocks no Terraform, você pode tornar sua configuração mais dinâmica e flexível, permitindo que ela se adapte a mudanças em seus dados externos.

### **6. Tire proveito dos Dynamic Blocks**

O Terraform oferece suporte a loops e condicionais em sua linguagem **HCL (HashiCorp Configuration Language)**, o que pode tornar a criação de recursos e configurações muito mais eficiente e fácil de gerenciar.

Ao usar loops e condicionais no Terraform, você pode:

- Criar vários recursos de uma só vez, sem precisar escrever cada um individualmente
- Modificar os valores dos recursos com base em dados externos ou lógica condicional
- Evitar a duplicação de código ao criar recursos semelhantes.

Algumas dicas para aproveitar ao máximo os loops e condicionais no Terraform:

- Identifique onde loops e condicionais podem ser úteis na sua configuração
- Use a sintaxe correta para loops e condicionais, seguindo a documentação oficial do Terraform
- Certifique-se de que seus loops e condicionais estejam formatados e validados corretamente antes de implantar sua configuração
- Considere usar funções externas ou variáveis para ajudar a gerenciar seus loops e condicionais.

Existem dois tipos de loops que o Terraform aceita:

**count**: o count é uma propriedade que pode ser definida em um bloco de recurso ou módulo, e que determina quantas instâncias desse recurso ou módulo serão criadas. Ele é útil quando você precisa criar várias instâncias do mesmo recurso ou módulo com configurações semelhantes.

Por exemplo, suponha que você queira criar várias instâncias do recurso **azurerm_virtual_machine** no Azure. Em vez de escrever cada instância individualmente, você pode usar o count para especificar o número de instâncias que deseja criar:

```bash
resource "azurerm_virtual_machine" "vm" {
  count = 3

  name                  = "my-vm-${count.index}"
  location              = var.location
  resource_group_name   = var.resource_group_name
  network_interface_ids = [azurerm_network_interface.nic.id]

  ...
}
```

Nesse exemplo, o Terraform criará três instâncias do recurso **azurerm_virtual_machine**, cada uma com um nome exclusivo baseado no índice do loop.

**for_each**: o for_each é um recurso mais avançado que permite criar várias instâncias de um recurso ou módulo com configurações diferentes. Ele é útil quando você precisa criar várias instâncias do mesmo recurso ou módulo com configurações diferentes.

Por exemplo, suponha que você tenha uma lista de recursos que deseja criar no Azure. Em vez de escrever cada recurso individualmente, você pode usar o for_each para especificar as configurações para cada recurso:

```bash
locals {
  resources = {
    "resource1" = {
      name = "resource1"
      size = "small"
    },
    "resource2" = {
      name = "resource2"
      size = "large"
    },
    "resource3" = {
      name = "resource3"
      size = "medium"
    }
  }
}

resource "azurerm_virtual_machine" "vm" {
  for_each = local.resources

  name                  = "my-${each.value.name}-vm"
  location              = var.location
  resource_group_name   = var.resource_group_name
  network_interface_ids = [azurerm_network_interface.nic.id]
  vm_size               = each.value.size

  ...
}
```

Nesse exemplo, o Terraform criará três instâncias do recurso **azurerm_virtual_machine**, cada uma com um nome e tamanho diferentes, com base nas configurações definidas na lista de recursos.

Ao usar loops e condicionais no Terraform, você pode economizar tempo e esforço ao criar e gerenciar seus recursos. 

### **7. Utilize Workspaces**

Utilizar o **Workspace** no Terraform é uma prática recomendada para separar recursos de diferentes ambientes, como **desenvolvimento**, **homologação** e **produção**. Cada workspace contém um estado separado dos recursos gerenciados pelo Terraform, permitindo que diferentes configurações e recursos sejam gerenciados separadamente sem interferir uns nos outros.

Ao usar **workspaces**, é importante garantir que a nomenclatura seja consistente e significativa para que os desenvolvedores possam entender facilmente qual workspace estão trabalhando e quais recursos estão sendo gerenciados. Por exemplo, é uma boa prática utilizar nomes como "dev", "staging" e "prod" para denotar os diferentes ambientes.

Além disso, é importante lembrar que alguns recursos podem não ser compatíveis com a criação de workspaces, portanto, é importante considerar cuidadosamente quais recursos devem ser criados em cada workspace. Por exemplo, recursos que envolvem segredos, chaves de API ou outros recursos sensíveis devem ser cuidadosamente gerenciados em cada workspace para garantir que as credenciais corretas sejam usadas em cada ambiente.

Ao utilizar **workspaces** no Terraform, é possível gerenciar ambientes separados com maior eficiência e segurança, o que é crucial para projetos de grande escala e que exigem alta disponibilidade e confiabilidade.

### **8. Sempre formate e valide seu código**

A formatação e validação do código Terraform é uma prática muito importante para garantir que o código seja consistente e esteja livre de erros. 

O Terraform possui ferramentas integradas para formatação e validação do código, como o **terraform fmt** e o **terraform validate**, que são usados para garantir que o código esteja bem escrito e livre de erros.

O comando **terraform fmt** é usado para formatar o código Terraform, garantindo que ele siga as melhores práticas e que esteja estruturado corretamente. Ele pode ser usado para formatar um único arquivo ou vários arquivos de uma só vez, tornando a formatação do código rápida e fácil.

Já o comando **terraform validate** é usado para validar o código Terraform e garantir que não haja erros. Ele pode ser usado para verificar se os recursos estão definidos corretamente, se as dependências estão corretas e se as variáveis foram definidas corretamente. Isso ajuda a garantir que o código Terraform esteja livre de erros e pronto para ser implantado.

É importante lembrar que a formatação e validação do código Terraform devem ser realizadas regularmente durante o desenvolvimento, para garantir que o código seja consistente e esteja sempre em conformidade com as melhores práticas. Além disso, é importante garantir que todos os desenvolvedores envolvidos no projeto usem as mesmas ferramentas e padrões de formatação e validação, para garantir a consistência do código e evitar erros de sintaxe e lógica.

Ao seguir as melhores práticas de formatação e validação do código Terraform, é possível garantir que o código seja consistente, livre de erros e pronto para ser implantado em qualquer ambiente.

### **9. Utilize as extensões do Terraform em suas IDEs**

O Terraform possui diversas extensões disponíveis para as principais IDEs do mercado, como o **Visual Studio Code**, que é uma das mais populares. Essas extensões ajudam a tornar o desenvolvimento com Terraform mais produtivo e eficiente, oferecendo recursos como auto-completação de código, validação de sintaxe em tempo real, realce de sintaxe e muito mais.

No VS Code, por exemplo, a extensão **"HashiCorp Terraform"** é uma das mais utilizadas, e permite que os usuários possam se beneficiar desses recursos. 

Ao utilizar essas extensões, você ganha mais agilidade no desenvolvimento e diminui a chance de erros em seu código. Portanto, é altamente recomendado que você explore as opções disponíveis e escolha aquela que melhor se adapta ao seu fluxo de trabalho.

### **9. Utilize ferramentas adicionais para aumentar o potencial do Terraform**

Existem diversas ferramentas adicionais que podem ajudar a aumentar o potencial do Terraform e melhorar ainda mais a experiência de desenvolvimento. 

Aqui estão algumas delas:

- **tflint**: é uma ferramenta de análise de código estático para Terraform que ajuda a identificar possíveis erros de sintaxe, estilo e configuração, além de fornecer sugestões para melhorar o código.
- **checkov**: é outra ferramenta de análise de código estático que ajuda a identificar riscos de segurança e conformidade em suas configurações de infraestrutura.
- **terratest**: é uma biblioteca de testes para infraestrutura em nuvem que permite criar testes automatizados para sua infraestrutura e validar se ela está funcionando conforme o esperado.
- **terraform-docs**: é uma ferramenta que ajuda a gerar documentação para seus módulos Terraform automaticamente, seguindo padrões de documentação estabelecidos.
- **infracost**: é uma plataforma de gerenciamento de custos para infraestrutura em nuvem que ajuda a monitorar e otimizar seus gastos com recursos na nuvem.

Ao utilizar essas ferramentas, você pode melhorar ainda mais a qualidade do seu código, aumentar a segurança e conformidade da sua infraestrutura, automatizar testes e documentação, além de controlar melhor seus custos na nuvem.

Existem muitas outras boas práticas que você pode seguir para aproveitar ao máximo o Terraform em seus projetos. No entanto, seguindo as dez dicas que foram apresentadas aqui, você já terá um ótimo ponto de partida para estruturar e organizar seus projetos Terraform de forma eficiente.

É isso galera, espero que gostem!

Forte Abraço!
