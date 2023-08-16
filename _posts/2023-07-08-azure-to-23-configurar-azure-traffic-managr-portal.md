---
layout: post
title: "[Azure-To] #23 Configurar Azure Traffic Manager [Portal]"
author: asilva
date: 2023-07-08 09:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, infraestrutura, networking, loadbalancer, waf, gateway, applicationgateway, frontdoor, trafficmanager]
---

Fala galera! Seis tão baum?

Vamos lá para mais uma sequência de artigos sobre Load Balancers no Microsoft Azure!

### **Sobre Azure Front Door**

O **Azure Traffic Manager** é um serviço de roteamento de tráfego de **DNS** fornecido pela Microsoft Azure, projetado para melhorar a disponibilidade e o desempenho de aplicativos distribuídos globalmente. Ele permite direcionar o tráfego de entrada para várias regiões do Azure ou até mesmo para endpoints fora do Azure, como sites em servidores locais ou em outras nuvens.

**Principais recursos do Azure Traffic Manager:**

- **Roteamento de Tráfego:** O Traffic Manager usa o DNS para distribuir o tráfego de entrada entre várias instâncias do seu aplicativo. Isso pode ser baseado em várias estratégias, como roteamento de tráfego baseado em desempenho, roteamento geográfico ou roteamento por prioridade.
- **Disponibilidade e Redundância:** O serviço monitora constantemente a disponibilidade dos endpoints (pontos de extremidade) configurados e redireciona automaticamente o tráfego para endpoints saudáveis. Isso ajuda a melhorar a disponibilidade e a resiliência do seu aplicativo.
- **Desempenho Aprimorado:** Ao usar estratégias de roteamento baseadas em desempenho, o Traffic Manager direciona os usuários para o endpoint mais próximo e de melhor desempenho, com base em métricas como latência e disponibilidade.
- **Roteamento Geográfico:** Você pode usar o Traffic Manager para direcionar usuários para endpoints específicos com base na localização geográfica. Isso é útil para fornecer experiências personalizadas e otimizadas para usuários em diferentes regiões.
- **Configuração Flexível:** O Azure Traffic Manager é altamente configurável e permite ajustar várias opções, como intervalos de monitoramento, tempo limite e estratégias de roteamento, para atender às necessidades do seu aplicativo.
- **Suporte para Diferentes Tipos de Endpoints:** O serviço suporta vários tipos de endpoints, incluindo pontos de extremidade baseados em endereços IP, URLs ou nomes de domínio.
- **Integração com Azure Monitor:** O Azure Traffic Manager pode ser integrado ao Azure Monitor para coletar e analisar dados de tráfego e desempenho, permitindo uma visão detalhada do comportamento do seu aplicativo.

### **Objetivo**

Neste artigo, você definirá uma configuração para o **Azure Traffic Manager** que irá agrupar duas instâncias de um aplicativo Web executado em diferentes regiões do Azure. Uma atuará como um endpoint primário para o Traffic Manager e outra atuará como um endpoint de failover.

O Traffic Manager monitorará continuamente o aplicativo da Web e, se o site principal não estiver disponível, ele fornecerá failover automático para o site de backup.

![](/assets/img/75/traffic01.png){: "width=60%" }

### **1.1 Criando os aplicativos Web (Web Apps)**

Este lab requer duas instâncias de um aplicativo Web executado em diferentes regiões do Azure. Ambas as instâncias do aplicativo da Web são executadas no modo Ativo/Ativo, portanto, qualquer uma delas pode receber tráfego. Esta configuração difere de uma configuração Ativa/Stand-By, onde se atua como um failover.

Na página inicial do Portal do Azure, clique em ‘**Create a resource**’ em seguida na página Novo, na caixa de Pesquisa, digite **Web App** e clique em + Add, + Create, + New.

![](/assets/img/75/traffic02.png){: "width=60%" }

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

![](/assets/img/75/traffic03.png){: "width=60%" }

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

![](/assets/img/75/traffic04.png){: "width=60%" }

Ficando assim:

![](/assets/img/75/traffic05.png){: "width=60%" }

### **2.1 Criando o Azure Traffic Manager**

Agora, vamos criar e configurar o Azure Traffic Manager.

Na página inicial do Portal do Azure, clique em ‘**Create a resource**’ em seguida na página Novo, na caixa de Pesquisa, digite **Traffic Manager** e clique em + Add, + Create, + New.

![](/assets/img/75/traffic06.png){: "width=60%" }

Forneça os seguintes detalhes:

- Name: você precisa utilizar um nome único para o front door **unicast-TMProfile01**
- Routing method: **Priority**
- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Resource group location: **Brazil South**.

![](/assets/img/74/frontdoor07.png){: "width=60%" }

Selecione **Create.**

### **3.1 Adicionando endpoints no Traffic Manager**

Agora, vamos adicionar o site Brazil South como endpoint primário para rotear todo o tráfego do usuário. Em seguida, vamos adicionar o site de East Us como um endpoint de failover. Se o endpoint primário ficar indisponível, o tráfego será roteado automaticamente para o endpoint de failover.

No Azure Traffic Manager, na aba **Endpoints**, clique em **Add** e forneça os seguintes detalhes:

![](/assets/img/75/traffic08.png){: "width=60%" }

Selecione **Add** novamente e repita os passos para criar o endpoint de failover. Use as mesmas configurações, exceto pelas informações abaixo.

- Name: **myFailoverEndpoint**
- Target resource: 	unicastlabwepapp-02 (East US)
- Priority:	**2**

![](/assets/img/75/traffic09.png){: "width=60%" }

Definir uma prioridade de 2 significa que o tráfego será roteado para esse ponto de extremidade de failover se o ponto de extremidade primário configurado não estiver íntegro.

Em **Settings**, selecione **Configuration** e, em seguida, atualize as configurações do monitor de endpoint **Protocol** para **HTTPS** e **Port** para **443** e selecione **Save**.

![](/assets/img/75/traffic10.png){: "width=60%" }

Os dois novos endpoints são exibidos no perfil do Traffic Manager. Observe que após alguns minutos o status do Monitoramento deve mudar para **Online**.

![](/assets/img/75/traffic11.png){: "width=60%" }

### **4.1 Testando o Azure Front Door**

Agora, vamos testar se nosso Traffic Manager está funcionando e enviando o tráfego com êxito para nossos endpoints.

No Traffic Manager, na aba **Overview**, localize o **DNS name** e copie o endereço.

![](/assets/img/75/traffic12.png){: "width=60%" }

Abra seu navegador e acesse a URL que copiamos. A página default do App Service é similar a está:

![](/assets/img/75/traffic13.png){: "width=60%" }

Atualmente, todo o tráfego está sendo enviado para o ponto de extremidade primário conforme você definiu como Prioridade 1.

Para testar se o endpoint de failover está funcionando corretamente, você precisa desabilitar o site primário.

No Traffic Manager, na aba **Endpoints**, clique no **myPrimaryEndpoint**, **Status**, selecione **Disabled** e, em seguida, selecione Save.

![](/assets/img/75/traffic14.png){: "width=60%" }

No Traffic Manager, na aba **Overview**, o status do monitor para **myPrimaryEndpoint** agora deve ser **Disabled** .

![](/assets/img/75/traffic15.png){: "width=60%" }

Volte para o seu navegador e selecione Atualizar. Você deve ver a mesma página de informações.

Como o endpoint primário não estava disponível, o tráfego foi roteado para o endpoint de failover para permitir que o site ainda funcione.

É isso, **Azure Traffic Manager** configurado e validado!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
