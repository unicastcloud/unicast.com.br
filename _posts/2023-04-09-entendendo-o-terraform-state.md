---
layout: post
title: "Entendendo o Terraform State"
author: asilva
date: 2023-04-09 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Estamos de volta com mais um post da nossa série sobre **Terraform**. Hoje vamos falar sobre um dos conceitos mais importantes dessa ferramenta: o **Terraform state**. Se você já utiliza o Terraform, sabe que ele é responsável por gerenciar a infraestrutura como código, ou seja, você define toda a sua infraestrutura em arquivos de configuração e o Terraform se encarrega de provisionar todos os recursos necessários na nuvem. 

Mas como ele faz isso? É aí que entra o **Terraform state**. Vamos entender melhor como funciona.

### **1. O que é o Terraform state?**

O Terraform state é um arquivo que contém toda a informação sobre a infraestrutura que você provisionou com o Terraform. Ele armazena informações sobre os recursos criados, seus atributos e dependências. O Terraform usa o state para saber o que precisa ser criado, atualizado ou destruído na sua infraestrutura.

### **2. Por que o Terraform state é importante?**

O Terraform state é importante porque é a fonte de verdade da sua infraestrutura. Sem ele, o Terraform não saberia o que foi criado e não seria possível gerenciar a infraestrutura como código. Além disso, o Terraform state permite que você faça alterações incrementais na sua infraestrutura de forma segura e previsível. Você pode ver o que mudou no seu estado e o que será alterado na próxima execução.

### **3. State local e State remoto**

Existem dois tipos de Terraform state: local e remoto. O Terraform local state é armazenado em um arquivo no seu sistema de arquivos local. Isso é útil para testes locais e pequenos projetos, mas pode ser problemático em ambientes de equipe ou projetos maiores. Por exemplo, se dois membros da equipe estiverem trabalhando no mesmo projeto, eles terão que compartilhar o arquivo de estado local, o que pode levar a conflitos e problemas de sincronização.

O Terraform remote state, por outro lado, é armazenado em um backend remoto, como o Terraform Cloud ou o Azure Storage Account. Isso permite que múltiplos membros da equipe compartilhem o mesmo estado de forma segura e eficiente. Além disso, o state remoto é mais seguro e confiável do que o state local, pois é armazenado em um local externo e pode ser versionado.

### **4. Inconsistência entre o Terraform state e o deploy manual**

Uma das principais preocupações ao trabalhar com o Terraform é garantir que o state esteja atualizado e consistente com a infraestrutura real na nuvem. Quando você cria um recurso pelo **Terraform**, o **state** é atualizado com as informações do recurso criado. No entanto, se você criar um recurso diretamente pelo portal, o Terraform não saberá sobre essa mudança e o state ficará desatualizado.

Caso ainda haja deploy manual, isso irá gerar inconsistências e dois ambientes distintos.

Por isso, é fundamental que o time tenha uma boa maturidade na hora de usar o Terraform em sua infraestrutura.

O time deve entender que, uma vez que o Terraform é adotado, essa deve ser a única maneira de fazer deploy no ambiente.

É importante criar um processo bem definido para o uso do Terraform, incluindo a política de não fazer alterações manuais na infraestrutura e garantir que todos os membros da equipe estejam cientes e sigam essa política. Além disso, é fundamental fazer uso de state remoto e versionamento do state, para garantir a consistência e a segurança do projeto.

### **5. Configurando o Terraform state no Azure**

Agora que entendemos o que é o Terraform state e a diferença entre state local e remoto, vamos ver como configurá-lo no Azure. Aqui está um exemplo de código que usa o Azure Storage Account como backend remoto:

````bash
terraform {
  backend "azurerm" {
    storage_account_name = "mytfstatestorageaccount"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}
````

Este código configura o **backend remoto** do Azure para usar uma conta de armazenamento chamada `"mytfstatestorageaccount"`, um contêiner chamado `"tfstate"` e um arquivo de estado chamado `"prod.terraform.tfstate"`. Observe que o nome da conta de armazenamento e o nome do contêiner precisam ser exclusivos globalmente, portanto, escolha nomes que não tenham sido usados antes.

### **6. Boas práticas de utilização do Terraform state**

Aqui estão algumas boas práticas para utilizar o Terraform state:

- Use state remoto: armazenar o state em um backend remoto, como o Terraform Cloud ou um serviço de armazenamento em nuvem, é a melhor opção para projetos de equipe ou projetos maiores. Isso ajuda a evitar conflitos e problemas de sincronização. Além disso, o state remoto é mais seguro e confiável do que o state local.
- Não edite o state manualmente: o Terraform é responsável por gerenciar o state. Nunca edite manualmente o arquivo de state, pois isso pode levar a inconsistências e erros.
- Versione o state: versionar o state permite que você volte a um estado anterior da sua infraestrutura, se necessário. Use uma ferramenta de controle de versão, como o Git, para versionar o seu state.
- Use workspaces: workspaces permitem que você crie múltiplos ambientes com diferentes states. Por exemplo, você pode ter um workspace para produção e outro para desenvolvimento. Isso ajuda a manter a consistência e a organização do seu projeto.

### **7. Conclusão**

O Terraform state é um conceito fundamental do Terraform que permite que você gerencie sua infraestrutura como código. Com ele, você pode provisionar, atualizar e destruir recursos de forma segura e previsível. Além disso, o uso de state remoto ajuda a evitar conflitos e problemas de sincronização em projetos de equipe.

Espero que este post tenha sido útil para entender melhor o Terraform state e como configurá-lo no Azure. Fique atento aos próximos posts da série para continuar aprendendo sobre essa poderosa ferramenta de gerenciamento de infraestrutura.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
