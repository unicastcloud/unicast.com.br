---
layout: post
title: "Configurando Azure DDoS Protection"
author: asilva
date: 2022-08-22 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, network, dos, ddos]
---

Fala galera! Seis tão baum?

Hoje vamos abordar e configurar o Azure DDoS Protection em seu ambiente.

Ataques de DDoS (Distributed Denial of Service) são uma das maiores preocupações de disponibilidade para qualquer ambiente. Um ataque de DDoS tenta esgotar os recursos do ambiente de forma que fique indisponível para os usuários.

Existem muitas razões para que alguém queria deixar seu ambiente indisponível, seja por motivos financeiros, concorrência, protestos, segurança ou mesmo status.

**O importante é:** no mundo globalizado em que vivemos hoje e com o boom da transformação digital em plenos pulmões, ninguém quer ficar com seu ambiente indisponível e virar capa de jornal (não impressso, mas digital LoL).

![](/assets/img/32/ddos01.png){: "width=60%" }

Existem vários tipos de ataques DDoS, os mais comuns são:

- Volumétrico
- UDP Flood
- NTP Flood
- SYN Flood
- Layer 7

Ambos vão explorar brechas na segurança do seu ambiente, os volumétricos são, de longe, o tipo mais comum, para quem trabalha ou já trabalhou com telecom sabe do que estou falando.

Basicamente é tomar uma pancada absurda de tráfego na sua rede, em alguns casos superior a 100 Gbps, aí meu amigo(a), na maioria dos casos é abraço!

![](/assets/img/32/ddos02.gif){: "width=60%" }

Mas fique calmo pequeno gafanhoto, a Microsoft já mitigou um ataque DDoS com tráfego de 3,47 Tb/s até então um dos maiores da história, pois na semana passada o Google registrou e mitigou o maior deles, em apenas dois minutos, o ataque passou de 100 mil requisições por segundo (RPS) para 46 milhões de requisições por segundo (RPS).

![](/assets/img/32/ddos03.jpg){: "width=60%" } _Dados da Microsoft_

![](/assets/img/32/ddos04.jpg){: "width=60%" } _Dados do Google_

>**Importante:** independente de fabricante ou solução, mitigar um ataque DDoS não é barato, mas não mitiga-lo pode ser mais caro ainda.
{: .prompt-warning }

Recado dado, vamos nessa!

### **Objetivo**

Implantar Azure DDoS Protection e configurar alertas.

### **1.1 Criando DDoS Protection Plan**

O protection plan define um conjunto de redes virtuais que têm proteção contra DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção para seu ambiente e vincular redes virtuais de várias assinaturas em um único tenant do AAD ao mesmo plano.

No portal da Azure procure pelo recurso **DDoS protection plans** e em seguida **create**

Agora basta inserir as informações de acordo com sua necessidade.

* Subscription: o nome da assinatura que você está usando neste laboratório
* Resource group: crie um novo resource group ou selecione o grupo desejado
* name: **ddos-unicast-plan**
* Region: **(US) East US** (ou outra região de sua preferência)

Clique em **Review + create**

![](/assets/img/32/ddos05.png){: "width=60%" }

### **1.2 Habilitando DDoS Protection na VNET**

Agora vamos habilitar o DDoS em nossa VNET.

Você pode habilitar o DDoS Protection Standard de duas maneiras.

1 - No momento que estiver criando sua VNET.

![](/assets/img/32/ddos06.png){: "width=60%" }

2 - Com uma VNET já existente.

![](/assets/img/32/ddos07.png){: "width=60%" }

E fim, está pronto! É muito simples habilitar a proteção em qualquer rede virtual nova ou existente, e ela não exige nenhum aplicativo ou alterações de recursos.

![](/assets/img/32/ddos08.png){: "width=60%" }

Poucos passos e muita proteção para seu ambiente.

- **Integração de plataforma nativa**: nativamente integrado ao Azure. Inclui a configuração por meio do Portal do Azure. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Proteção imediata**: a configuração simplificada protege imediatamente todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma definição ou intervenção do usuário é necessária.
- **Monitoramento de tráfego Always On**: os padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
- **Ajuste adaptável**: a criação de perfil de tráfego inteligente aprende sobre o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção multicamada**: quando implantada com um WAF (firewall do aplicativo Web), a Proteção contra DDoS Standard protege na camada de rede (Camada 3 e 4, oferecida pelo Proteção contra DDoS do Azure Standard) e na camada de aplicativo (Camada 7, oferecida por um WAF). As ofertas WAF incluem o SKU WAF do Gateway de Aplicativo do Azure, bem como as ofertas de firewall de aplicativo Web de terceiros disponíveis no Azure Marketplace.
- **Escala de mitigação ampla**: todos os vetores de ataque L3/L4 podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
- **Análise de ataque**: obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, além de um resumo completo após o término dele. Transmita logs do fluxo de mitigação por streaming para o Microsoft Sentinel ou um sistema de SIEM (gerenciamento de informações e eventos de segurança) offline para monitoramento quase em tempo real durante um ataque.
- **Métricas de ataque**: métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque**: alertas podem ser configurados no início, durante e após a interrupção de um ataque, usando métricas de ataque internas. Os alertas integram-se ao software operacional, como logs do Microsoft Azure Monitor, Splunk, Armazenamento do Microsoft Azure, Email e o Portal do Azure.
- **Resposta Rápida contra DDo**S: entre em contato com a equipe de DRR (Resposta Rápida da Proteção contra DDoS) para ajuda na investigação e na análise de ataques. Para saber mais, veja Resposta Rápida de DDoS.
- **Garantia de custo**: receba crédito de serviço de transferência de dados e expansão de aplicativo para custos de recursos incorridos como resultado de ataques DDoS documentados.

### **2.1 Monitorando o ambiente**

Além de ativar a proteção de DDoS, é muito importante que você monitore o serviço e tenha informações atualizadas sobre a saúde de seu ambiente.

![](/assets/img/32/ddos09.png){: "width=60%" }

### **2.2 Configurando Alertas**

Ainda no **DDoS Protection Plan**, selecione **Alerts** na aba **monitoring**. 

Clique em **create** e em seguida **Alert rule**

![](/assets/img/32/ddos11.png){: "width=60%" }

Em **Scope** selecione o seu **IP público**

![](/assets/img/32/ddos12.png){: "width=60%" }

Em **Condition** selecione procure por **Under DDoS attack or not** e configure o **Threshold value** como 1, em seguida, clique em **Done**.

![](/assets/img/32/ddos13.png){: "width=60%" }

![](/assets/img/32/ddos14.png){: "width=60%" }

Em **Actions** clique em **Crerate action group** 

- Action group name: **acg-unicast**
- Display name: **acg-unicast**

![](/assets/img/32/ddos15.png){: "width=60%" }

- Notification type: Email/SMS message/Push/Voice
- Name: e-mail
- Email: seu e-mail para receber os alertas.

![](/assets/img/32/ddos16.png){: "width=60%" }

Clique em **Review + create**

![](/assets/img/32/ddos17.png){: "width=60%" }

Em **Details**, preencha a severidade e nome de seu alerta.

![](/assets/img/32/ddos18.png){: "width=60%" }

Clique em **Review + create**

![](/assets/img/32/ddos19.png){: "width=60%" }

Você deve receber um e-mail de confimação.

![](/assets/img/32/ddos20.png){: "width=60%" }

>**Dica:** Considere ativar o **Diagnostic setting** em seus IPs Públicos, coletar logs e métricas de seu DDoS Protection e enivar para uma Log Analytics.
{: .prompt-tip }

### **3.1 Validando o DDoS Protection**

É recomendável testar como o serviço de proteção irá responder a um ataque DDoS, e para isso você pode utilizar alguns sites parceiros da Microsoft para realizar um teste em seu ambiente.

>**Importante:** Você só pode simular ataques de DDoS usando parceiros aprovados pela Microsoft.
{: .prompt-warning }

Atualmente a Microsoft tem pareceria com dois sites:

- <a href="https://www.keysight.com/us/en/products/network-security/breakingpoint-cloud.html" target="_blank"> **BreakingPoint Cloud**</a>
- <a href="https://www.red-button.net/" target="_blank"> **Red Button**</a>

Os ambientes de simulação de parceiros são criados no Azure. Você só pode simular em endereços de IP públicos hospedados e que pertencem a uma assinatura do Azure, que será validada pelo Azure AD (Azure Active Directory) antes do teste.

![](/assets/img/32/ddos21.png){: "width=60%" } _Exemplo de teste com BreakingPoint Cloud_

![](/assets/img/32/ddos22.png){: "width=60%" } _Exemplo de relatório de testes do Red Button_

Após a conclusão do teste, você pode validar se o monitoramento está funcional.

Navegue até seu **IP Público**, **Monitoring** e **Metrics**.

Deixe visível as charts:

- Max Inbound packets dropped DDoS
- Max Under DDoS attack or not 

![](/assets/img/32/ddos23.png){: "width=60%" } _Exemplo de métrica de ataque, você deve ver que o valor muda de 0 para 1, como a imagem_

É isso galerinha, espero que gostem.

Forte abraço a todos!