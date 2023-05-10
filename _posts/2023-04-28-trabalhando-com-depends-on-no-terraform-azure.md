---
layout: post
title: "Trabalhando com depends_on no Terraform [Azure]"
author: asilva
date: 2023-04-28 09:20 -0300
categories: [DevOps, Terraform]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure]
---

Fala galera! Seis tão baum?

Vamos para mais um post da nossa série sobre **Terraform**. Hoje, vamos falar sobre uma funcionalidade muito útil e importante do Terraform: o **depends_on**.

O **depends_on** é uma maneira de especificar dependências entre recursos no Terraform. Ele é usado para garantir que um recurso seja criado ou atualizado antes que outro seja criado. Isso é especialmente útil quando você tem recursos que dependem de outros recursos para funcionar corretamente. Com o **depends_on**, você pode controlar a ordem em que os recursos são criados e atualizados, garantindo que a infraestrutura seja criada de maneira consistente e confiável.

A seguir, vamos explicar mais detalhadamente o que é o **depends_on**, como utilizá-lo e quais são seus benefícios.

### **1. O que é o depends_on?**

O **depends_on** é um argumento que pode ser usado em um recurso no Terraform para especificar dependências entre recursos. Quando você usa o **depends_on**, você está dizendo ao Terraform que um determinado recurso depende de outro recurso para funcionar corretamente.

O **depends_on** é usado para garantir que a ordem de criação ou atualização dos recursos seja a correta. Ele pode ser usado para garantir que um recurso seja criado antes de outro recurso, ou para garantir que um recurso seja atualizado antes de outro recurso.

### **2. Por que utilizar o depends_on?**

Existem várias razões pelas quais você pode querer usar o **depends_on** no Terraform. Aqui estão alguns exemplos:

- Você tem recursos que dependem de outros recursos para funcionar corretamente. Por exemplo, você pode ter um recurso que depende de um banco de dados para funcionar corretamente. Com o depends_on, você pode garantir que o banco de dados seja criado antes do recurso que depende dele.
- Você quer controlar a ordem em que os recursos são criados ou atualizados. Com o depends_on, você pode especificar a ordem em que os recursos são criados ou atualizados, garantindo que a infraestrutura seja criada de maneira consistente e confiável.
- Você quer garantir que um recurso seja criado ou atualizado antes de outro recurso. Com o depends_on, você pode garantir que um recurso seja criado ou atualizado antes de outro recurso, garantindo que a infraestrutura esteja em um estado consistente.

### **3. Como utilizar o depends_on?**

Para utilizar o **depends_on** no Terraform, basta adicionar o argumento depends_on a um recurso. Aqui está um exemplo:

````bash
resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  address_space       = ["10.0.0.0/16"]
  location            = "westus2"
  resource_group_name = azurerm_resource_group.example.name
  depends_on          = [azurerm_resource_group.example]
}
````

Neste exemplo, estamos criando uma rede virtual no Microsoft Azure. Estamos usando o **depends_on** para garantir que a rede virtual seja criada somente depois que o grupo de recursos (`azurerm_resource_group`) tenha sido criado.

Além disso, você pode usar o **depends_on** para especificar dependências entre recursos de maneira mais complexa. Por exemplo, você pode usar o **depends_on** para garantir que um recurso seja atualizado somente depois que outro recurso tenha sido atualizado. 

Aqui está um exemplo:

````bash
resource "azurerm_managed_disk" "example" {
  name                 = "example-disk"
  location             = "eastus"
  resource_group_name  = "example-resource-group"
  storage_account_type = "Standard_LRS"
  create_option        = "Empty"
  disk_size_gb         = 128
}

resource "azurerm_virtual_machine" "example" {
  name                  = "example-vm"
  location              = "eastus"
  resource_group_name   = "example-resource-group"
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "example-os-disk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Premium_LRS"
    disk_size_gb      = 128
  }

  depends_on = [
    azurerm_managed_disk.example
  ]
}
````

Neste exemplo, estamos criando uma instância de máquina virtual no Microsoft Azure. Estamos usando o depends_on para garantir que a instância de máquina virtual seja atualizada somente depois que o disco (`azurerm_managed_disk`) tenha sido atualizado.

Outro exemplo de uso do **depends_on** é com a utilização de módulos no Terraform.

Suponha que você esteja usando um **módulo** **Terraform** para criar um cluster **Kubernetes** no Microsoft Azure e um outro módulo para criar a **rede virtual** e os recursos de segurança. Você quer garantir que o módulo **Kubernetes** seja criado somente depois que o módulo de rede seja criado, para que os recursos do cluster possam se conectar corretamente à rede.

Para fazer isso, você pode usar o **depends_on** no módulo **Kubernetes** para garantir que ele seja criado somente depois que o módulo de rede seja criado. Aqui está um exemplo de código que usa o **depends_on** com dois módulos Terraform:

````bash
module "network" {
  source = "terraform-azurerm-network"
  
  resource_group_name  = "example-resource-group"
  location             = "eastus"
  virtual_network_name = "example-vnet"
  subnet_name          = "example-subnet"
  
  depends_on = [
    azurerm_resource_group.example_resource_group
  ]
}

module "kubernetes_cluster" {
  source = "terraform-azurerm-kubernetes-cluster"
  
  resource_group_name  = "example-resource-group"
  location             = "eastus"
  kubernetes_version   = "1.21.2"
  vm_size              = "Standard_DS2_v2"
  node_count           = 3
  ssh_public_key       = file("~/.ssh/id_rsa.pub")
  
  depends_on = [
    module.network
  ]
}
````

Neste exemplo, estamos usando o módulo `"terraform-azurerm-network"` para criar a rede virtual e os recursos de segurança no Microsoft Azure e o módulo `"terraform-azurerm-kubernetes-cluster"` para criar o cluster Kubernetes. Estamos usando o depends_on no módulo Kubernetes para garantir que ele seja criado somente depois que o módulo de rede seja criado.

Observe que o **depends_on** está sendo aplicado no **nível do módulo**, em vez de ser aplicado em recursos individuais. Isso é importante porque os módulos são responsáveis por criar um conjunto de recursos, e precisamos garantir que todos esses recursos sejam criados corretamente.

É importante lembrar que o **depends_on** não garante a ordem de execução de recursos, mas sim a ordem em que os recursos são criados ou atualizados. Se você precisa garantir a ordem de execução de recursos, você deve usar o argumento **depends_on** em combinação com o argumento **lifecycle**.

### **4. Conclusão**

O **depends_on** é uma funcionalidade muito útil e importante do **Terraform**. Ele permite que você especifique dependências entre recursos, controlando a ordem em que os recursos são criados ou atualizados. Com o **depends_on**, você pode garantir que a infraestrutura seja criada de maneira consistente e confiável, especialmente quando você tem recursos que dependem de outros recursos para funcionar corretamente.

Neste post, explicamos o que é o **depends_on**, por que utilizá-lo e como utilizá-lo no **Terraform**. Além disso, demos exemplos de código para ilustrar como o **depends_on** pode ser utilizado em ambientes reais no **Microsoft Azure**.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!

