---
layout: post
title: "[Azure-To] #13 Configurar Azure Functions [Portal]"
author: asilva
date: 2023-01-27 09:00:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, functions, paas]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

## **Sobre Azure Functions**

O Azure Functions é uma plataforma de computação em nuvem altamente escalável e flexível da Microsoft, que permite a criação e execução de pequenos pedaços de código, conhecidos como "funções", sem se preocupar com a infraestrutura subjacente. Com o Azure Functions, você pode facilmente criar soluções de back-end para aplicativos web, dispositivos móveis e IoT, além de integrar diferentes serviços e aplicativos na nuvem.

O Azure Functions oferece várias características e vantagens que o tornam uma opção atraente para aplicativos em nuvem. Aqui estão algumas das mais importantes:

1. **Escalabilidade automática:** O Azure Functions se adapta automaticamente à demanda de tráfego, aumentando ou diminuindo o número de instâncias conforme necessário. Isso significa que você não precisa se preocupar com a administração da infraestrutura subjacente.
2. **Suporte a vários linguagens de programação:** O Azure Functions suporta uma ampla gama de linguagens de programação, incluindo C#, Java, JavaScript, Python, PHP e muito mais. Isso significa que você pode escolher a linguagem que melhor atenda às suas necessidades.
3. **Integração fácil com outros serviços:** O Azure Functions pode ser facilmente integrado a outros serviços do Azure, como o Azure Event Grid, o Azure Cosmos DB e o Azure Storage, entre outros. Isso permite que você crie soluções completas e integradas sem precisar se preocupar com a infraestrutura subjacente.
4. **Preço baseado no uso:** Você só paga pelo que usa com o Azure Functions, o que significa que você não precisa se preocupar com despesas desnecessárias ou recursos ociosos. Além disso, o Azure Functions oferece uma opção de preço gratuito para que você possa experimentar antes de se comprometer com uma compra.
5. **Execução de tarefas programadas:** O Azure Functions permite a execução de tarefas programadas, o que significa que você pode automatizar tarefas repetitivas e programá-las para ocorrer em momentos específicos.
6. **Alta disponibilidade:** O Azure Functions é projetado para oferecer alta disponibilidade, o que significa que você pode ter certeza de que suas funções estarão sempre disponíveis quando você precisar.

## **Objetivo**

Neste artigo, criaremos um Function App para exibir uma mensagem Hello quando houver uma solicitação HTTP.

## **1.1 Criando um Function App**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo**, na caixa de Pesquisa, digite **Function App** e clique em **+ Add, + Create, + New**.

![](/assets/img/55/functions01.png){: "width=60%" }

Na guia Basic do Function App, especifique as seguintes configurações (substitua xxxx no nome da função por letras e dígitos de forma que o nome seja globalmente exclusivo e deixe todas as outras configurações com seus valores padrão):

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Function App name: **function-xxxx**
- Publish: **Code**
- Runtime stack: **.NET**
- Version: **6**
- Region: **East US**
- Operating System: **Windows** 

![](/assets/img/55/functions02.png){: "width=60%" }

Clique em **Review+Create** e, após a validação bem-sucedida, clique em **Create** para começar a provisionar e implantar seu novo Aplicativo no Azure.

Aguarde a notificação de que o recurso foi criado. Quando a implantação for concluída, clique em Ir para o recurso. 

![](/assets/img/55/functions03.png){: "width=60%" }

## **2.1 Criando uma function trigger HTTP**

Nesta tarefa, usaremos a função Webhook + API para exibir uma mensagem quando houver uma solicitação HTTP.

1. No **Function App**, clique no aplicativo de funções recém-criado.
2. No aplicativo, na seção **Functions**, clique em Functions e em seguida, clique em **+ Add, + Create, + New.**

![](/assets/img/55/functions04.png){: "width=60%" }

3. Na janela à direita. Na seção **Select a template**, clique no **HTTP trigger**. Clique em Adicionar.

![](/assets/img/55/functions05.png){: "width=60%" }

4. Em **HttpTrigger1** na seção **Developer**, clique em **Code+Test.**
5. **Em Code+Test**, revise o código gerado automaticamente e observe que o código foi projetado para executar uma solicitação HTTP e registrar informações. Além disso, observe que a função retorna uma mensagem Hello com um nome.

![](/assets/img/55/functions06.png){: "width=60%" }

6. Clique em **Get function URL** da função na seção superior do editor de funções.

Assegure-se de que o valor na lista suspensa **Key** esteja definido como **default** e clique em Copiar para copiar o URL da função.

![](/assets/img/55/functions07.png){: "width=60%" }

## **2.2 Validando o funcionamento**

Chegou a hora de testar nossa função!

Abra uma nova guia do navegador e cole o URL da função copiada na barra de endereços do navegador. Quando a página for solicitada, a função será executada. Observe a mensagem retornada informando que a função requer um nome no corpo da solicitação.

![](/assets/img/55/functions08.png){: "width=60%" }

Acrescente **&name=yourname** ao final da URL.

O resultado será uma URL semelhante a está:

![](/assets/img/55/functions09.png){: "width=60%" }

Quando você pressiona enter, sua função é executada e cada chamada é rastreada. 

Para visualizar os traces, retorne ao Portal **HttpTrigger1** | Code + Test e clique em **Monitor**. Você pode configurar o Application Insights selecionando o **timestamp** de **data/hora** e clicando em **Run query in Application Insights.**

![](/assets/img/55/functions10.png){: "width=60%" }

Parabéns! Você criou um aplicativo de funções para exibir uma mensagem Hello quando houver uma solicitação HTTP.

É isso galera, espero que gostem!

Forte Abraço!