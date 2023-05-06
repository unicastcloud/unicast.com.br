---
layout: post
title: "Gerenciando gastos no Azure com Azure Cost Management"
author: asilva
date: 2021-06-24 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, budget, costmanagement]
---

Fala galera! Seis tão baum?

O consumo de serviços do Microsoft Azure requer uma mudança na mentalidade do modelo CapEx para o modelo OpEx. Ou seja, ao contrário da infraestrutura tradicional onde você paga por hardware e infraestrutura de suporte, no Microsoft Azure você paga pelo serviço utilizado com base no seu uso mensal.

Por isso, a visibilidade de seus gastos ao usar a nuvem do Microsoft Azure é de extrema importância, e se você não está de olho nos gastos, os custos podem sair mais alto do que o planejado.

Neste artigo iremos falar sobre o Azure Cost Management, uma solução gratuita oferecida aos clientes do Microsoft Azure através do portal. Ele fornece informações sobre seus custos e utilização de todos os serviços do Azure. A solução fornece insights e relatórios e pode fornecer dados sobre o uso de outros provedores de nuvem utilizados pela sua empresa.

Para utilizar o Azure Cost Management, vá até a barra de pesquisas e procure por **Cost Management**.

![](/assets/img/05/cost1.png){: "width=60%" }

Em **Cost analysis**, você terá uma visão geral dos custos do seu ambiente.

![](/assets/img/05/cost2.png){: "width=60%" }

Na opção **Filters**, podemos selecionar vários filtros de gerenciamento, é possível filtrar os custos por assinatura, grupo de recursos, tipos de recurso, TAGs e muito mais.

![](/assets/img/05/cost3.jpg){: "width=60%" }

As TAGs são uma maneira simples e eficaz de gerenciar custos no Microsoft Azure. Com elas é possível filtrar ou agrupar recursos que você deseja gerenciar custos.

Você poderia por exemplo: marcar todo o seu ambiente de Azure Virtual Desktop com a TAG **AVD** e o valor **Host Pool**.

Desta forma, utilizando o Azure Cost Management, será possível filtrar somente os custos referente aos recursos utilizados pelo Host Pool de seu Azure Virtual Desktop.

Você também pode exportar os dados de seu ambiente para um conta de armazenamento ou mesmo utilizar esses dados no Power BI.

![](/assets/img/05/cost4.jpg){: "width=60%" }

Agora escolha o escopo de cobrança que deseja gerenciar, você pode gerenciar seus custos por assinaturas ou por grupo de recursos.

![](/assets/img/05/cost5.png){: "width=60%" }

Em **Time Filters**, você pode selecionar a janela de tempo que deseja gerenciar, por padrão já temos algumas opções pré-definidas, mas você também pode personalizar sua pesquisa com a opção **Custom date range**.

![](/assets/img/05/cost6.png){: "width=60%" }

Espero que gostem do material.

Forte abraço a todos!

