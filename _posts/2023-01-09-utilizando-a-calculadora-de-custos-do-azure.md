---
layout: post
title: "Utilizando a calculadora de custos do Azure"
author: asilva
date: 2023-01-09 09:00:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, budget, costmanagement, calculator, pricing]
---

Fala galera! Seis tão baum?

Há muitas maneiras de executar um aplicativo ou diferentes serviço no Azure, níveis de serviço ou opções podem ter grandes efeitos sobre os custos do ambiente.

Tomar medidas para reduzir e controlar os custos de infraestrutura em nuvem, é um papel contínuo no dia a dia do profissional Azure. 

A Calculadora de Preços do Azure é comumente usada para avaliar os custos de diferentes combinações de serviços do Azure. Isso é valioso ao implantar novas cargas de trabalho ou expandir significativamente as cargas de trabalho existentes no Azure.

Neste artigo, utilizaremos a calculadora de preços do Azure para gerar uma estimativa de custo.

## **Sobre a Calculadora do Azure**

A Calculadora de preços do Azure é uma ferramenta que pode ser utilizada para estimar custos de execução dos serviços no Azure. Ela fornece a estimativa de uma solução completa usando uma combinação de serviços do Azure. 

A calculadora permite que os usuários insiram uma variedade de informações, incluindo o número de serviços do Azure a serem usados, o tamanho dos dados e o número de horas necessárias para concluir a tarefa.

## **1.1 Configurando a calculadora de preços**

No navegador, navegue até a página <a href="https://azure.microsoft.com/en-us/pricing/calculator/"> **Azure Pricing Calculator**</a>.

![](/assets/img/48/calc01.png){: "width=60%" }

No menu a esquerda, clique em **Compute** e em seguida selecione **Virtual Machines**. Role para baixo para ver os detalhes da máquina virtual.

![](/assets/img/48/calc02.png){: "width=60%" }

Substitua o texto **Your Estimate** por nomes mais descritivos para sua estimativa. 

![](/assets/img/48/calc03.png){: "width=60%" }

Agora, vamos criar nossa estimativa de custos para uma máquina virtual, modifique as configurações de acordo com sua necessidade.

![](/assets/img/48/calc04.png){: "width=60%" }

Em **Saving Options**, selecione o modelo mais adequado para sua necessiade, aproveite para explorar as opções de reserva e verificar a diferença de custos.

![](/assets/img/48/calc05.png){: "width=60%" }

Em **Managed OS Disks** modifique as configurações de acordo com sua necessidade.

![](/assets/img/48/calc06.png){: "width=60%" }

Em **Bandwidth** modifique as configurações de acordo com sua necessidade.

![](/assets/img/48/calc07.png){: "width=60%" }

Pronto, já temos todas as informações referente a nossa máquina virtual, vamos adicionar mais um recurso para simular uma estimativa real de projeto.

![](/assets/img/48/calc08.png){: "width=60%" }

Volte ao menu principal, selecione **Networking** e em seguida **Application Gateway**. Role para baixo para ver os detalhes do application gateway.

![](/assets/img/48/calc09.png){: "width=60%" }

Modifique as configurações de acordo com sua necessidade.

![](/assets/img/48/calc10.png){: "width=60%" }

## **2.1 Revisando a estimativa de custos**

Agora, revisaremos os resultados da Calculadora de preços do Azure.

Role até a parte inferior da página para ver o total Custo mensal estimado.

![](/assets/img/48/calc11.png){: "width=60%" }

Explore as várias opções disponíveis na calculadora, altere a moeda para **Reais** e selecione **Export** para baixar uma cópia da estimativa para exibição off-line no formato Microsoft Excel (.xlsx).

![](/assets/img/48/calc12.png){: "width=60%" }

É isso galera, espero que gostem!

Forte Abraço!