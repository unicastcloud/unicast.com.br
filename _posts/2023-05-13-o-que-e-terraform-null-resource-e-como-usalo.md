---
layout: post
title: "O que é Terraform null_resource e como usá-lo?"
author: asilva
date: 2023-05-13 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Hoje vamos falar sobre um recurso muito útil chamado **null_resource**. Se você está acompanhando nossa série, já deve estar familiarizado com o **Terraform** e como ele pode ajudar na automação e provisionamento de recursos de infraestrutura na nuvem. 

O **null_resource** é mais uma ferramenta poderosa que o **Terraform** oferece para lidar com situações específicas em que não é necessário criar nenhum recurso, mas ainda é necessário executar ações.

Vamos nos aprofundar nos detalhes do **null_resource** e ver como ele pode ser usado para tarefas que não exigem a criação de recursos de infraestrutura. Vamos lá!

### **1. O que é o null_resource?**

O **null_resource** é um recurso especial fornecido pelo Terraform que permite executar ações sem criar nenhum recurso físico na infraestrutura. Em vez de provisionar um recurso como uma instância de máquina virtual, um banco de dados ou uma rede, o **null_resource** pode ser usado para executar comandos locais, scripts, chamadas de API ou qualquer outra ação que não precise ser representada por um recurso específico.

No Terraform, os recursos **null_resource** são úteis quando você precisa executar um script localmente, interagir com uma API externa, realizar tarefas de pós-provisionamento ou até mesmo como uma forma de realizar operações customizadas e flexíveis.

### **2. Casos de uso do null_resource**

Agora que entendemos o que é o **null_resource**, vamos explorar alguns dos casos de uso em que ele pode ser aplicado:

- **Execução de scripts locais:** O null_resource pode ser usado para executar scripts diretamente no computador em que o Terraform está sendo executado. Isso é útil quando você precisa fazer configurações adicionais após o provisionamento de recursos.
- **Chamadas de API:** Com o null_resource, você pode fazer chamadas de API para serviços externos, como notificar um serviço de monitoramento sobre a criação de um recurso ou enviar uma solicitação a um provedor de serviços externo.
- **Operações customizadas:** Caso você precise executar tarefas customizadas que não estão disponíveis como recursos nativos do Terraform, o null_resource permite que você crie suas próprias lógicas e ações.

### **3. O que é um trigger no null_resource?**

No **null_resource**, um trigger é um evento ou condição que dispara a execução das ações definidas no recurso. Os triggers permitem que você controle quando o **null_resource** deve ser executado, garantindo que as ações ocorram apenas quando necessário.

Existem diferentes tipos de triggers disponíveis para o **null_resource**, como:

- **Arquivos de origem:** O null_resource pode ser acionado quando um arquivo ou diretório específico é alterado. Isso permite que você reaja a mudanças em arquivos relevantes para suas ações.
- **Variáveis:** Ao usar variáveis, você pode definir quando o null_resource deve ser acionado com base em determinados valores. Por exemplo, quando uma variável de ambiente atinge um certo estado.
- **Dependências:** Ao especificar dependências em outros recursos do Terraform, você pode acionar o null_resource quando esses recursos forem modificados ou atualizados.

### **4. Exemplo do null_resource com trigger**

Agora vamos mostrar um exemplo prático de como usar o **null_resource** com um trigger para executar uma ação após a criação de um recurso no Microsoft Azure.

````bash
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Recurso criado com sucesso!"
  }

  triggers = {
    resource_id = azurerm_virtual_machine.example.id
  }
}
````

Nesse exemplo, estamos usando o **null_resource** em conjunto com o provisionador `local-exec` para executar um simples comando de impressão após a criação de uma instância de máquina virtual no Azure.

Explicando o código:

- `null_resource.example` é o nome do recurso `null_resource` que estamos criando.
- `provisioner "local-exec"` especifica que queremos executar um comando local.
- `command = "echo Recurso criado com sucesso!"` é o comando que será executado quando o `null_resource` for acionado.
- `triggers` define o trigger para esse `null_resource`, que é baseado no ID da instância de máquina `virtual azurerm_virtual_machine.example.id`.

Com esse exemplo, após a criação da instância de máquina virtual no Azure, o Terraform executará o comando echo Recurso criado com sucesso!, que é apenas um exemplo simples. 

Você pode personalizar esse comando para realizar qualquer ação necessária após a criação do recurso.

### **5. Conclusão**

O **null_resource** é uma ferramenta poderosa no Terraform que permite executar ações sem criar recursos de infraestrutura. Com sua flexibilidade, você pode executar scripts locais, fazer chamadas de API e executar tarefas customizadas. Além disso, os triggers permitem controlar quando as ações devem ser executadas, adicionando ainda mais controle ao seu fluxo de trabalho.

Agora que você conhece o **null_resource** e seus benefícios, você pode utilizá-lo em seus projetos do Terraform para realizar ações adicionais e personalizadas durante o provisionamento da infraestrutura na nuvem.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
