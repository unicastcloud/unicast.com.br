---
layout: post
title: "[Azure-To] #15 Configurar Azure Container Instances [Portal]"
author: asilva
date: 2023-02-05 09:00:00 -0300
categories: [Azure, Azure-To]
tags: [azure, microsoft, aci, container, docker]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

### **Sobre Azure Container Instances**

O Azure Container Instances é um serviço de contêineres na nuvem fornecido pela Microsoft Azure que permite que os usuários executem facilmente contêineres sem precisar gerenciar a infraestrutura subjacente. Essa plataforma de serviço de contêineres é ideal para aqueles que precisam executar cargas de trabalho de curta duração e escalonamento rápido em contêineres.

Ao contrário das plataformas tradicionais de contêineres, o Azure Container Instances oferece uma opção de serviço sem servidor, o que significa que não há necessidade de gerenciar e configurar uma infraestrutura de servidor dedicada para executar contêineres. Isso torna o Azure Container Instances uma opção muito mais simples e flexível para quem precisa implantar aplicativos em contêineres na nuvem.

Neste post, exploraremos em mais detalhes o que é o Azure Container Instances, como funciona e como pode ser usado para executar contêineres de maneira rápida e fácil. Também veremos como essa plataforma pode ser integrada a outros serviços da nuvem do Azure para oferecer um ambiente de contêiner seguro, escalonável e confiável.

### **Objetivo**

Neste artigo, criaremos um contêiner usando Azure Container Instances (ACI) no Portal do Azure. O contêiner é um aplicativo da web Welcome to ACI que exibe uma página HTML estática.

### **1.1 Criando o Container Instance**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo**, na caixa de Pesquisa, digite **Container instances** e clique em **+ Add, + Create, + New**.

![](/assets/img/57/aci01.png){: "width=60%" }

Forneça os seguintes detalhes básicos para a nova instância de contêiner (deixe os padrões para todo o resto):

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Container name: **unilabcontainer**
- Region: **(US) East US**
- Image Source: **Docker Hub or other registry**
- Image type: **Public**
- Image: **mcr.microsoft.com/azuredocs/aci-helloworld**
- OS type: **Linux**
- Size: **Leave at the default**

![](/assets/img/57/aci02.png){: "width=60%" }

Configure a guia **Networking** (substitua xxxxx por letras e dígitos de forma que o nome seja globalmente exclusivo). Deixe todas as outras configurações em seus valores padrão.

DNS name label: **unilabcontainerdn0001**

![](/assets/img/57/aci03.png){: "width=60%" }

>Seu contêiner estará acessível publicamente em dns-name-label.region.azurecontainer.io. Se você receber uma mensagem de erro de nome DNS não disponível após a implantação, especifique um nome de DNS diferente (substituindo o xxxxx) e implante novamente.
{: .prompt-tip }

Clique em **Review and Create** para iniciar o processo de validação automática. Em seguida, clique em **Create** para criar a instância do contêiner.

### **2.1 Validando o deployment do Container Instance**

Nesta tarefa, verificaremos se a instância do contêiner está em execução garantindo que a página de boas-vindas seja exibida.

1. Após a conclusão da implantação, clique no link **Go to resource.**

![](/assets/img/57/aci04.png){: "width=60%" }

2. Em **Overview** **unilabcontainer**, certifique-se de que o Status do contêiner seja **Running**.

![](/assets/img/57/aci05.png){: "width=60%" }

3. Localize o nome de domínio (FQDN).

![](/assets/img/57/aci06.png){: "width=60%" }

Copie o **FQDN** do contêiner em uma nova guia do navegador da Web e pressione Enter. A página de boas-vindas deve ser exibida.

![](/assets/img/57/aci07.png){: "width=60%" }

Parabéns! Você implantou com êxito um aplicativo em um contêiner no Azure Container Instance.

É isso galera, espero que gostem!

Forte Abraço!