---
layout: post
title: "[Azure-To] #9 Configurar Azure App Service [Portal]"
author: asilva
date: 2022-11-07 09:00:00 -0500
categories: [Azure, Azure-To]
tags: [azure, microsoft, infraestrutura, web, appservice, app, webapp]
---

Fala galera! Seis tão baum?

Faz tempo que não faço uma sequencia dos artigos do Azure-To, então vamos nessa!

### **Sobre o Azure App Service**

O Serviço de Aplicativo Web (App Service) permite que você rapidamente crie, implante e dimensione aplicativos Web, móveis e de API de nível empresarial executados em qualquer plataforma. Atende aos rigorosos requisitos de desempenho, escalabilidade, segurança e conformidade utilizando uma plataforma totalmente gerenciada pela Microsoft.

### **Objetivo**

Configurar Azure App Service

### **1.1 Configurando o Azure App Service via Portal**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo** , na caixa de Pesquisa, digite **Web App** e clique em **Create** 

![](/assets/img/43/webapp1.png){: "width=60%" }

**Especifique as configurações do recurso:**

Siga as etapas necessárias para configurar o aplicativo web.

* Subscription: o nome da assinatura que você está usando neste * laboratório
* Resource group: selecione seu grupo de recursos ou crie um novo.
* Nome: **unicastappdemo**
* Publish: **Code**
* Runtime: selecione a stack de sua preferência.
* Operating System: **Linux**
* Region: **Brazil South** (ou outra região de sua preferência)

![](/assets/img/43/webapp2.png){: "width=60%" }

Agora, precisamos criar nosso **Service Plan**.

Um plano do Serviçodefine um conjunto de recursos de computação para execução de um aplicativo Web. Um ou mais aplicativos podem ser configurados para serem executados nos mesmos recursos de computação (ou no mesmo plano do Serviço).

Em **Linux Plan (Brazil South)** clique em **Create New** e em seguida adicione o nome de seu service plan e clique em **OK**.

![](/assets/img/43/webapp3.png){: "width=60%" }

Em **Pricing Plan**, clique em **Change size**, selecione o plano que faça mais sentido com suas necessidades. Mosso objetivo é desenvolvimento e teste, então, vamos selecionar **Dev/Test** em seguida, **Apply** e **Next: Deployment**.

![](/assets/img/43/webapp4.png){: "width=60%" }

![](/assets/img/43/webapp5.png){: "width=60%" }

### **2.1 Sessão Deployment**

Nesta sessão, você pode habilitar o GitHub Actions para implantar continuamente seu aplicativo. O GitHub Actions é uma estrutura de automação que pode criar, testar e implantar seu aplicativo sempre que um novo commit for feito em seu repositório. Se seu código estiver no GitHub, escolha seu repositório e adicione seu arquivo de fluxo de trabalho para implantar automaticamente seu aplicativo no App Service. 

![](/assets/img/43/webapp6.png){: "width=60%" }

Em nosso caso, não iremos utilizar a integração com o GitHub, desta forma, basta cliquer em **Next: Networking**.

### **3.1 Sessão Networking**

Os aplicativos Web podem ser provisionados com o endereço de entrada público na Internet ou isolado em uma rede virtual do Azure. Os Web Apps também podem ser provisionados com tráfego de saída capaz de alcançar endpoints em uma rede virtual e serem controlados por grupos de segurança de rede ou afetados por rotas de rede virtual. Por padrão, seu aplicativo está exposto à Internet e não poderá acessar uma rede virtual. Esses aspectos também podem ser alterados após o provisionamento do aplicativo.

![](/assets/img/43/webapp7.png){: "width=60%" }

Em nosso caso, não iremos utilizar está opção no momento, clique em **Next: Monitoring**.

### **4.1 Sessão Monitoring**

Os insights de aplicativo do Azure Monitor são um serviço de gerenciamento de desempenho de aplicativos (APM) para desenvolvedores e profissionais de DevOps. Habilite-o abaixo para monitorar automaticamente seu aplicativo. Ele detectará anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. 

![](/assets/img/43/webapp8.png){: "width=60%" }

Para este laboratório não iremos habilitar o application insights, mas para um ambiente produtivo, recomento fortemente habilitar o **Log Analytics com Application Insights**, clique em **Next: Tags**.

### **4.1 Sessão Tags**

Adicione as Tags em seu recurso e clique em **Next: Review + create**.

![](/assets/img/43/webapp9.png){: "width=60%" }

Após a revisão, clique em **Create**.

![](/assets/img/43/webapp10.png){: "width=60%" }

### **5.1 Validando a Aplicação**

Acesse o **App Service** que criamos e em seguida, clique na **URL** de destino.

![](/assets/img/43/webapp11.png){: "width=60%" }

Agora você pode ver que nosso aplicativo está ativo na nuvem do Azure.

![](/assets/img/43/webapp12.png){: "width=60%" }

Caso seu código não esteja no GitHub, vá em **Deployment Center** e configure sua aplicação.

![](/assets/img/43/webapp13.png){: "width=60%" }

É isso galera, espero que gostem!

Forte Abraço!