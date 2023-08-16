---
layout: post
title: "[Azure-To] #22 Configurar Azure Front Door [Portal]"
author: asilva
date: 2023-07-03 09:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, infraestrutura, networking, loadbalancer, waf, gateway, applicationgateway, frontdoor]
---

Fala galera! Seis tão baum?

Vamos lá para mais uma sequência de artigos sobre Load Balancers no Microsoft Azure!

### **Sobre Azure Front Door**

O **Azure Front Door** é um serviço de roteamento de tráfego global e balanceamento de carga da Microsoft. Ele oferece uma maneira altamente escalável e confiável de direcionar o tráfego de entrada para aplicativos baseados na nuvem, melhorando a disponibilidade e o desempenho para os usuários finais em diferentes regiões geográficas. O Front Door atua como um ponto de entrada na borda da rede da Microsoft, direcionando o tráfego para recursos hospedados em várias regiões do Azure.

**Principais recursos do Azure Front Door:**

- Roteamento Global: O Front Door direciona o tráfego do usuário para a região mais próxima, com base na localização do usuário, para reduzir a latência e melhorar o desempenho.
- Balanceamento de Carga: Distribui o tráfego entre várias instâncias do aplicativo para otimizar o desempenho e evitar sobrecargas.
- Alta Disponibilidade: Oferece failover automático entre regiões em caso de falhas, garantindo alta disponibilidade e confiabilidade.
- Segurança: Fornece recursos de segurança, como proteção contra DDoS (negação de serviço distribuída) e SSL/TLS para proteger as comunicações.
- Gerenciamento de Tráfego: Permite definir políticas de roteamento com base em regras, como porcentagem de tráfego direcionado para diferentes versões do aplicativo.
- Análise e Monitoramento: Oferece insights detalhados sobre o tráfego e o desempenho do aplicativo por meio de integração com serviços de monitoramento, como o Azure Monitor e o Azure Application Insights.
- Integração com o Azure CDN: Pode ser integrado ao Azure Content Delivery Network (CDN) para melhorar ainda mais o desempenho e a distribuição de conteúdo.

O **Azure Front Door** é uma solução poderosa para empresas que buscam fornecer aplicativos de alta disponibilidade e desempenho global para seus usuários. Ele ajuda a otimizar a experiência do usuário final, garantindo que o tráfego seja direcionado de maneira eficiente e segura para as regiões apropriadas do Azure.

### **Objetivo**

Neste artigo, você definirá uma configuração para o **Azure Front Door** que irá agrupar duas instâncias de um aplicativo Web executado em diferentes regiões do Azure. 

Essa configuração irá direcionar o tráfego para o site mais próximo que executa o aplicativo. O Azure Front Door monitora continuamente o aplicativo Web. 

Iremos testar o failover automático para o próximo site disponível quando o site mais próximo estiver indisponível. 

![](/assets/img/74/frontdoor01.png){: "width=60%" }

### **1.1 Criando os aplicativos Web (Web Apps)**

Este lab requer duas instâncias de um aplicativo Web executado em diferentes regiões do Azure. Ambas as instâncias do aplicativo da Web são executadas no modo Ativo/Ativo, portanto, qualquer uma delas pode receber tráfego. Esta configuração difere de uma configuração Ativa/Stand-By, onde se atua como um failover.

Na página inicial do Portal do Azure, clique em ‘**Create a resource**’ em seguida na página Novo, na caixa de Pesquisa, digite **Web App** e clique em + Add, + Create, + New.

![](/assets/img/74/frontdoor02.png){: "width=60%" }

Forneça os seguintes detalhes:

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Name: você precisa utilizar um nome único para o web app **unicastlabwepapp-01**
- Publish: **Code**
- Runtime stack: **NET 6 (LTS)**
- Operating System: **Windows** 
- Region: **Brazil South**
- Windows Plan: crie um novo plano **myAppServicePlanBrazilSouth**
- Princing Plan: **Standard S1 100 total ACU, 1.75 GB memory**

![](/assets/img/74/frontdoor03.png){: "width=60%" }

Selecione **Review + create** e depois **Create.**

Agora, siga os mesmo passos para criar o segundo Web App, lembrando de selecionar este em uma nova região.

Forneça os seguintes detalhes:

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Name: você precisa utilizar um nome único para o web app **unicastlabwepapp-02**
- Publish: **Code**
- Runtime stack: **NET 6 (LTS)**
- Operating System: **Windows** 
- Region: **(US) East US**
- Windows Plan: crie um novo plano **myAppServicePlanEastUs**
- Princing Plan: **Standard S1 100 total ACU, 1.75 GB memory**

![](/assets/img/74/frontdoor04.png){: "width=60%" }

Ficando assim:

![](/assets/img/74/frontdoor05.png){: "width=60%" }

### **2.1 Criando o Azure Front Door**

Agora, vamos criar e configurar o Azure Front Door para direcionar o tráfego do usuário com base na latência mais baixa entre os dois servidores de aplicativos Web.

Na página inicial do Portal do Azure, clique em ‘**Create a resource**’ em seguida na página Novo, na caixa de Pesquisa, digite **Front Door** e clique em + Add, + Create, + New.

![](/assets/img/74/frontdoor06.png){: "width=60%" }

Forneça os seguintes detalhes:

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Resource group location: aceite os valores default.
- Name: você precisa utilizar um nome único para o front door **unicastlabfrontdoor**
- Tier: **Standard**
- Endpoint Name: **FDendpoint**
- Origin Type: **App Service**
- Origin host name: o nome do webapp criado anteriormente. 

![](/assets/img/74/frontdoor07.png){: "width=60%" }

Selecione **Review + create** e depois **Create.**

Com o recurso criado, vá até o Front Door e na aba **Origin Groups**, selecione o grupo de origem criado. 

![](/assets/img/74/frontdoor08.png){: "width=60%" }

Para atualizar o grupo de origem, clique em **Add an origin** e selecione o segundo Web App que criamos.

![](/assets/img/74/frontdoor09.png){: "width=60%" }

![](/assets/img/74/frontdoor10.png){: "width=60%" }

E por fim clique em **Update.**

### **3.1 Testando o Azure Front Door**

Depois de criar o Front Door, leva alguns minutos para que a configuração seja implantada globalmente. Assim que estiver concluído, podemos acessar o frontend criado.

No Front Door, na aba **Overview**, localize o **endpoint hostname** e copie o endereço.

![](/assets/img/74/frontdoor11.png){: "width=60%" }

Abra seu navegador e acesse a URL que copiamos. A página default do App Service é similar a está:

![](/assets/img/74/frontdoor12.png){: "width=60%" }

Agora, vamos testarmos o failover global do **Azure Front Door**, volte ao portal do azure, selecione seus **App Services**, e vamos fazer o **stop** de um dos nossos recursos.

Selecione um dos serviços e clique em **Stop.**

![](/assets/img/74/frontdoor13.png){: "width=60%" }

Pode haver um atraso enquanto o aplicativo da web para. Se você receber uma página de erro em seu navegador, atualize a página.

Volte para o seu navegador e selecione Atualizar. Você deve ver a mesma página de informações.

Agora, para validar que estamos mesmo com o failover global do **Azure Front Door** funcionando, volte ao portal do azure, selecione o nosso segundo **App Services**, e vamos fazer o **stop** do serviço.

![](/assets/img/74/frontdoor14.png){: "width=60%" }

Volte para o seu navegador e selecione Atualizar. Desta vez, você deve ver uma mensagem de erro.

![](/assets/img/74/frontdoor15.png){: "width=60%" }

É isso, **Azure Front Door** configurado e validado!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
