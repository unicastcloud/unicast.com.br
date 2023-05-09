---
layout: post
title: "Trabalhando com provisioners no Terraform [Azure]"
author: asilva
date: 2023-04-22 09:30 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Sejam bem-vindos a mais um artigo da nossa série sobre **Terraform**. Hoje vamos falar sobre os Terraform **Provisioners**, um recurso poderoso que pode ajudar muito na gestão das suas infraestruturas.

### **1. Uma visão geral de provisioners no Terraform**

Para começar, vamos dar uma visão geral sobre os **provisioners** no **Terraform**. **Provisioners** são recursos que permitem a execução de scripts e comandos depois que os recursos são criados ou atualizados. Eles são usados para realizar tarefas que não podem ser feitas com recursos nativos do **Terraform**, como a instalação de software, a configuração de serviços e outras atividades específicas.

Existem três tipos de provisioners no Terraform: **file**, **local-exec** e **remote-exec**. 

Vamos entender um pouco mais sobre cada um deles.

### **2. file**

O provisioner **file** é utilizado para criar arquivos ou diretórios em máquinas provisionadas. Ele pode ser usado para carregar arquivos de configuração, scripts e outros recursos necessários para o funcionamento dos serviços.

Para utilizar o provisioner **file**, é necessário especificar o caminho do arquivo que será criado e o seu conteúdo. 

Abaixo, segue um exemplo de código que cria um arquivo chamado `"example.txt"` em uma instância do Azure:

````bash
resource "azurerm_linux_virtual_machine" "example" {
  // Configurações da máquina virtual

  provisioner "file" {
    source      = "files/example.txt"
    destination = "/home/user/example.txt"
  }
}
````

### **3. local-exec**

O provisioner **local-exec** é utilizado para executar comandos em máquinas provisionadas. Ele pode ser usado para realizar tarefas como a instalação de pacotes, a configuração de serviços e outras atividades específicas.

Para utilizar o provisioner **local-exec**, é necessário especificar o comando que será executado. 

Abaixo, segue um exemplo de código que executa o comando `"sudo apt-get update"` em uma instância do Azure:

````bash
resource "azurerm_linux_virtual_machine" "example" {
  // Configurações da máquina virtual

  provisioner "local-exec" {
    command = "sudo apt-get update"
  }
}
````

### **4. remote-exec**

O provisioner **remote-exec** é utilizado para executar comandos em máquinas provisionadas através de uma conexão SSH. Ele pode ser usado para realizar tarefas como a instalação de pacotes, a configuração de serviços e outras atividades específicas.

Para utilizar o provisioner **remote-exec**, é necessário especificar o comando que será executado e as credenciais de SSH. 

Abaixo, segue um exemplo de código que executa o comando `"sudo apt-get update"` em uma instância do Azure através de uma conexão SSH:

````bash
resource "azurerm_linux_virtual_machine" "example" {
  // Configurações da máquina virtual

  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install nginx -y"
    ]

    connection {
      type        = "ssh"
      user        = "username"
      private_key = file("~/.ssh/id_rsa")
    }
  }
}
````

### **5. Comparação entre, file, local-exec e remote-exec**

Cada provisioner tem suas próprias vantagens e desvantagens, e o seu uso depende do cenário em que ele será aplicado. 

Abaixo, segue uma tabela comparativa entre os três tipos de provisioners:

|                  | **file**                 | **local-exec**              | **remote-exec**                             |
|------------------|:------------------------:|:---------------------------:|:-------------------------------------------:|
| **O que faz**    | Cria arquivos/diretórios | Executa comandos localmente | Executa comandos através de uma conexão SSH |
| **Vantagens**    | Não requer conexão SSH	  | Pode ser usado para executar comandos complexos | Pode ser usado em máquinas sem acesso direto à internet|
| **Desvantagens** | Não é adequado para comandos	complexos ou interativos |          | Pode ser mais lento do que o provisioner local-exec em ambientes locais |
| **Quando usar**  | Quando você precisa criar arquivos	| Quando você precisa executar comandos simples	| Quando você precisa executar comandos complexos ou remotos |

### **6. Exemplo de uso**

Agora, vamos para um exemplo de uso real que envolve os três tipos de provisioners disponíveis no Terraform. Neste exemplo, vamos criar uma máquina virtual no Azure, instalar o Apache HTTP Server e criar um arquivo HTML simples.

Para isso, vamos precisar de um recurso de máquina virtual, um recurso de rede virtual e um recurso de grupo de segurança de rede.

Aqui está o código Terraform para a criação desses recursos:

````bash
resource "azurerm_resource_group" "example" {
  name     = "example-resource-group"
  location = "East US"
}

resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  address_prefixes     = ["10.0.1.0/24"]
  virtual_network_name = azurerm_virtual_network.example.name
  resource_group_name  = azurerm_resource_group.example.name
}

resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = null
  }
}

resource "azurerm_linux_virtual_machine" "example" {
  name                = "example-vm"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  size                = "Standard_B1s"
  admin_username      = "adminuser"
  network_interface_ids = [
    azurerm_network_interface.example.id,
  ]
  os_disk {
    name              = "example-osdisk"
    caching           = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }
}
````

Agora, vamos adicionar um provisioner `file` para criar um arquivo HTML simples na máquina virtual:

````bash
resource "azurerm_linux_virtual_machine" "example" {
  // ...

  provisioner "file" {
    source = "index.html"
    destination = "/var/www/html/index.html"
  }
}
````

Em seguida, adicionamos um provisioner `local-exec` para instalar o **Apache HTTP Server** na máquina virtual:

````bash
resource "azurerm_linux_virtual_machine" "example" {
  // ...

  provisioner "file" {
    source = "index.html"
    destination = "/var/www/html/index.html"
  }

  provisioner "local-exec" {
    command = "sudo apt-get update && sudo apt-get install -y apache2"
  }
}
````

Agora, vamos adicionar o provisioner `remote-exec` para iniciar o serviço **Apache HTTP Server** na máquina virtual:

````bash
resource "azurerm_linux_virtual_machine" "example" {
  // ...

  provisioner "file" {
    source = "index.html"
    destination = "/var/www/html/index.html"
  }

  provisioner "local-exec" {
    command = "sudo apt-get update && sudo apt-get install -y apache2"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo systemctl start apache2",
      "sudo systemctl enable apache2"
    ]
  }
}
````

E, finalmente, crie um arquivo chamado `index.html` com o seguinte conteúdo:

````html
<!DOCTYPE html>
<html>
  <head>
    <title>Terraform + Azure</title>
  </head>
  <body>
    <h1>Terraform + Azure = ❤️</h1>
    <p>
````

Com esses três provisioners, podemos criar uma máquina virtual, instalar o Apache HTTP Server e criar um arquivo HTML simples. Vale lembrar que o provisioner `remote-exec` também pode ser usado para executar comandos em outras máquinas virtuais na mesma rede.

Para executar este código, basta colocar os arquivos `index.html` e `main.tf` no mesmo diretório e, em seguida, executar os seguintes comandos:

````bash
terraform init
terraform apply
````

Com isso, o Terraform criará os recursos no Azure e executará os provisioners para configurar a máquina virtual. Após a execução, você poderá acessar a página web em **http://<endereço-ip-da-máquina-virtual>**.

### **7. Conclusão**

Os provisioners no Terraform são uma ótima opção para realizar tarefas específicas que não podem ser feitas com os recursos nativos do Terraform. Cada tipo de provisioner tem suas próprias vantagens e desvantagens, e é importante entender as diferenças entre eles para escolher o mais adequado para o seu cenário.

No entanto, confesso que não utilizo frequentemente os provisioners em minha rotina de trabalho. Na maioria das vezes, prefiro utilizar o Ansible para configurar a infraestrutura e deixar o Terraform responsável apenas pelo provisionamento. 

Isso não significa que os provisioners não sejam uma excelente opção para determinados casos de uso. Na verdade, essa funcionalidade é mais uma opção poderosa que o Terraform oferece, capaz de atender às necessidades de diferentes projetos.

Espero que este artigo tenha sido útil e esclarecedor para você entender o que são os provisioners no Terraform e como utilizá-los em seus projetos.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!

