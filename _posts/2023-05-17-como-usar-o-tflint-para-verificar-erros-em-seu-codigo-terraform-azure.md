---
layout: post
title: "Como usar o TFLint para verificar erros em seu código Terraform [Azure]"
author: asilva
date: 2023-05-17 09:00 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Bem-vindos de volta à nossa série de artigos sobre Terraform! Hoje vamos falar sobre uma ferramenta essencial para garantir a qualidade do seu código Terraform: o **TFLint**. 

Se você já passou por situações em que erros passaram despercebidos e acabaram causando problemas em sua infraestrutura, o **TFLint** está aqui para te ajudar! 

Vamos explorar o que é o **TFLint**, como ele funciona e como você pode usá-lo para verificar seu código Terraform. Vamos lá!

### **1. O que é o TFLint?**

O **TFLint** é uma ferramenta de análise estática de código específica para o Terraform. Ele verifica seu código em busca de erros, possíveis problemas e más práticas, seguindo as melhores práticas recomendadas pela comunidade Terraform.

Com o **TFLint**, você pode garantir que seu código esteja bem estruturado, evitando erros que possam comprometer sua infraestrutura.

### **2. Como o TFLint funciona?**

O **TFLint** funciona analisando os arquivos de código Terraform em busca de problemas comuns e recomendações de melhores práticas. Ele verifica aspectos como recursos obsoletos, recursos não utilizados, má formatação, falta de tags, entre outros. O **TFLint** usa regras pré-definidas para realizar essa análise e fornece mensagens de erro ou aviso caso algum problema seja encontrado.

### **3. Como instalar o TFLint?**

A instalação do **TFLint** é simples e direta. Siga os seguintes passos para instalar o **TFLint** em seu sistema:.

**Para Linux ou macOS:**

- Abra um terminal.
- Utilize um gerenciador de pacotes, como o Homebrew, para instalar o TFLint executando o seguinte comando:

````bash
brew install tflint
````

Aguarde até que a instalação seja concluída. O TFLint e suas dependências serão baixados e configurados automaticamente.

**Para Windows:**

- Abra um prompt de comando ou PowerShell.
- Faça o download do binário do TFLint a partir do repositório oficial no GitHub. Você pode encontrar o link correto para download na seção de releases do repositório.
- Extraia o conteúdo do arquivo baixado em uma pasta de sua escolha.
- Adicione o caminho da pasta onde o binário foi extraído ao seu PATH de sistema. Isso permitirá que você execute o TFLint a partir de qualquer diretório no prompt de comando ou PowerShell.

Após concluir esses passos, o TFLint estará instalado em seu sistema e pronto para uso.

**Verificando a instalação:**

Para verificar se a instalação do TFLint foi concluída com sucesso, abra um terminal ou prompt de comando e execute o seguinte comando:

````powershell
tflint --version
````

Isso exibirá a versão do **TFLint** instalada em seu sistema. Se você receber a informação da versão sem nenhum erro, isso significa que o **TFLint** foi instalado corretamente.

Agora que o **TFLint** está instalado, você pode prosseguir para a configuração e utilização dessa poderosa ferramenta para verificar seu código Terraform.

### **4. Como configurar o TFLint?**

Antes de utilizar o **TFLint**, você pode configurar as regras e personalizá-lo de acordo com suas necessidades. O **TFLint** suporta a criação de um arquivo de configuração chamado `.tflint.hcl`, onde você pode definir regras específicas, ignorar determinados erros e muito mais.

### **5. Exemplo de configuração do arquivo .tflint.hcl**

O arquivo `.tflint.hcl` é um arquivo de configuração usado pelo **TFLint** para personalizar o comportamento das verificações de código Terraform. Ele permite que você defina regras específicas, configure severidades de erros, exclua diretórios ou arquivos específicos da verificação e muito mais.

Aqui está um exemplo básico de um arquivo `.tflint.hcl` para uso com recursos do Microsoft Azure:

````bash
module "example_module" {
  enabled_rules = [
    "azure_region_not_supported",
    "azure_virtual_machine_no_tags",
  ]
  
  ignore_rule = [
    "terraform_backend_required",
    "terraform_deprecated_provider",
  ]

  exclude_paths = [
    "modules/*",
    "**/test/**",
  ]
}
````

Neste exemplo, configuramos o **TFLint** para verificar duas regras específicas: `azure_region_not_supported` e `azure_virtual_machine_no_tags`. Essas regras ajudam a garantir que você não esteja usando uma região não suportada pelo Azure e que todas as máquinas virtuais possuam tags.

Além disso, utilizamos a opção `ignore_rule` para excluir duas regras específicas: `terraform_backend_required` e `terraform_deprecated_provider`. Isso pode ser útil se você estiver utilizando um ambiente específico onde essas regras não são aplicáveis.

Por fim, usamos `exclude_paths` para definir quais diretórios ou arquivos devem ser excluídos da verificação. No exemplo acima, estamos excluindo qualquer arquivo dentro da pasta `modules/` e qualquer arquivo dentro de qualquer diretório que contenha a subpasta `test/`. Isso pode ser útil para evitar verificações em arquivos de teste ou em módulos externos.

Ao personalizar o arquivo `.tflint.hcl` de acordo com suas necessidades específicas, você pode adaptar as verificações do **TFLint** ao seu ambiente e garantir a conformidade do seu código Terraform com as melhores práticas.

### **6. Utilizando o TFLint para verificar seu código**

Agora que o **TFLint** está instalado e configurado, vamos ver como utilizá-lo para verificar seu código Terraform. Suponha que você tenha o seguinte código Terraform para criar uma instância do Azure Virtual Machine:

````bash
resource "azurerm_virtual_machine" "example_vm" {
  name                  = "example-vm"
  location              = "West US"
  resource_group_name   = "example-resource-group"
  vm_size               = "Standard_DS2_v2"
  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }
}
````

- Abra um terminal ou prompt de comando e navegue até o diretório onde seu código Terraform está localizado.
- Execute o comando tflint para iniciar a verificação:

````bash
tflint
````

O **TFLint** analisará seu código Terraform e apresentará as saídas na tela. Cada problema encontrado será exibido em um formato semelhante ao seguinte exemplo:

````bash
Error: Missing required tag "Name" in azurerm_virtual_machine.example_vm

  on main.tf line 3:
  3: resource "azurerm_virtual_machine" "example_vm" {
````

Nesse exemplo, o **TFLint** identificou que a tag obrigatória `"Name"` está faltando no recurso `azurerm_virtual_machine.example_vm` definido no arquivo `main.tf`. Ele fornece a localização do problema, destacando a linha específica onde ocorre.

O **TFLint** também fornece informações adicionais sobre cada problema encontrado. Por exemplo, ele pode exibir uma descrição do erro e uma sugestão para corrigi-lo:

````bash
Description: The "Name" tag is required for better resource identification and management.

Suggestion: Add the "Name" tag to the resource with an appropriate value.
````

Essas informações ajudam a entender o motivo do problema e fornecem diretrizes claras sobre como corrigi-lo.

Além disso, o **TFLint** atribui uma severidade a cada problema encontrado. Existem três níveis de severidade:

- **error**: Indica um problema crítico que precisa ser corrigido.
- **warning**: Indica um aviso sobre uma possível má prática ou problema menor.
- **info**: Fornece informações adicionais, mas não indica problemas específicos.

A severidade de cada problema é indicada na saída do **TFLint** para ajudar a priorizar as correções.

Ao utilizar o **TFLint**, é importante prestar atenção às saídas geradas e corrigir todos os problemas identificados. Isso garantirá que seu código Terraform esteja livre de erros e siga as melhores práticas recomendadas.

Se você deseja visualizar uma lista completa de todas as regras disponíveis e suas descrições, você pode executar o seguinte comando:

````bash
tflint --list-rules
````

Isso exibirá todas as regras suportadas pelo TFLint, permitindo que você explore mais a fundo as verificações que ele realiza.

Lembre-se de executar o **TFLint** regularmente à medida que você desenvolve seu código Terraform, para identificar e corrigir problemas antes de implantar sua infraestrutura.

### **7. Conclusão**

O **TFLint** é uma ferramenta poderosa para garantir a qualidade do seu código Terraform. Com sua análise estática, você pode identificar e corrigir erros e más práticas antes de implantar sua infraestrutura. Ao utilizar o **TFLint**, você evita dores de cabeça no futuro e garante um ambiente de infraestrutura mais confiável. 

Então, não deixe de adicionar o TFLint à sua caixa de ferramentas Terraform!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
