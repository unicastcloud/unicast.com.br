---
layout: post
title: "Criando alerta de previsão de gastos no Azure"
author: asilva
date: 2022-11-01 09:00:00 -0500
categories: [Azure, Otimização de Custos]
tags: [azure, microsoft, budget, costmanagement]
---

Fala galera! Seis tão baum?

Bora falar sobre otimização de custos no Azure, assunto extremamente importante para você que gerencia um ambiente em cloud.

Já falamos em artigos anteriores sobre a importância de otimizar e gerenciar seus custos nos Azure, se você passou batido, calma, respira, da uma conferida nos tópicos anteriores, e volta aqui para deixar tudo no padrão!

Neste artigo vamos focar somente na criação de alertas com base na previsão de gastos do Azure.

### Aritgos Otimização de Custos

- <a href="https://unicast.com.br/posts/gerenciando-gastos-no-azure-com-azure-cost-management/">Gerenciando gastos no Azure com Azure Cost Management</a>
- <a href="https://unicast.com.br/posts/5-praticas-para-reduzir-seus-gastos-com-o-microsoft-azure/">5 práticas para reduzir seus gastos com o Microsoft Azure</a>

Então, sem mais delongas, vamos ao que interessa!

### Motivadores

Criar alertas com base as previsões de custos do Azure, pode ajudar a ajustar seus gastos antes que ele atinja a meta de orçamento mensal estipulada.

Pense nisso, se você vai atingir seu orçamento mensal no dia 20 do mês em vez do último dia, você gostaria de saber disso o mais cedo possível, não? 

![](/assets/img/42/alerts01.jpg){: "width=60%" }

Quanto mais cedo você souber, melhor poderá gerenciar os gastos.

### 1.1 Configurar alertas

Abra seu navegador e acesse o portal Azure em **http://portal.azure.com** e faça login usando sua conta da Microsoft.

No portal da Azure pesquise por **Cost Management + Billing** em seguida, selecione **Cost Management.**

![](/assets/img/42/alerts02.png){: "width=60%" }

Agora, clique em **Budgets** e em seguida **add**.

![](/assets/img/42/alerts03.png){: "width=60%" }

Na próxima tela, em **Create budget**, selecione os valores apropriados:

- **Budget scoping:** sua assinatura.
- **Budget name:** nome do seu alerta.
- Reset period: Monthly
- Provide an amount for the Budget: valor desejado do alerta

![](/assets/img/42/alerts04.png){: "width=60%" }

Clique em **next**.

Na tela **Set alerts** selecione o tipo como **Forecasted** e forneça a porcentagem do orçamento. 

![](/assets/img/42/alerts05.png){: "width=60%" }

Por exemplo, se você forneceu seu orçamento como **R$1,000** e selecionou a porcentagem do orçamento como **80%**, o alerta será acionado em **R$800,00**.

Emm seguida, configure o e-mail que irá receber as notificações.

Feito isso é só clicar em **create**.

Depois de configurar seu alerta, fique atento a e-mails como o abaixo. Se você estiver criando uma assinatura ou um orçamento de grupo de recursos, também poderá acionar a automação de alerta usando grupos de ação.

![](/assets/img/42/alerts06.png){: "width=60%" }

É isso galerinha, espero que gostem.

Forte abraço a todos!