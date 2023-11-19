---
layout: post
title: "Utilizando a calculadora de TCO do Azure"
author: asilva
date: 2023-01-12 09:00:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, budget, costmanagement, calculator, pricing, tco]
---

Fala galera! Seis tão baum?

Para quem trabalha, ou vai trabalhar com **cloud computing**, saber sobre os conceitos de **TCO** **(Total Cost of Ownership)** ou **Custo Total de Propriedade**, é muito importante.

Ao lidar com **TCO**, você deve observar dois novos conceitos:

**Capex**, que são despesas de capital, e **Opex**, que são despesas operacionais. 

Você precisará gastar dinheiro de uma forma ou de outra para capacitar os serviços e aplicativos que administram os negócios, mas o objetivo, é claro, é ter um sistema confiável e de alto desempenho pelo menor valor necessário. E é aqui que entra em jogo a análise de TCO.

## **Sobre a Calculadora de TCO do Azure**

A Calculadora de TCO ajuda você a entender as áreas de custo que afetam seus aplicativos hoje, como hardware de servidor, licenças de software, eletricidade e mão de obra. Recomendando um conjunto de serviços equivalentes no Azure que darão suporte aos seus aplicativos. 

A análise mostrará cada área de custo com uma estimativa de seus gastos locais versus gastos no Azure. E o resultado é um relatório detalhado que mostra quanto dinheiro você pode economizar migrando para o Azure.

## **1.1 Adicionando Workloads na calculadora de TCO**

No navegador, navegue até a página <a href="https://azure.microsoft.com/en-us/pricing/tco/calculator/"> **Total Cost of Ownership (TCO) Calculator**</a>.

![](/assets/img/49/tco01.png){: "width=60%" }

Para adicionar detalhes de sua infraestrutura de servidor local, clique em **+ Add server workload** para definir suas cargas de trabalho.

![](/assets/img/49/tco02.png){: "width=60%" }

**Defina os seguintes valores:**

- **Name**: Servers: Windows VMs
- **Workload**: Windows/Linux server
- **Environment**: Virtual Machines
- **Operating system**: Windows
- **Operating System License**: Datacenter
- **VMs**: 50
- **Virtualization**: Hyper-V
- **Core**(s): 8
- **RAM(GB)**: 16
- **Optimize by**: CPU
- **Windows Server 2008/2008 R2**: Off

Selecione novamente **+ Add server workload** para definir as denmais cargas de trabalho.

- **Name**: Servers: Linux VMs
- **Workload**: Windows/Linux server
- **Environment**: Virtual Machines
- **Operating system**: Linux
- **Servers**: 50
- **Procs per server**: 1
- **Core(s) per proc**: 4
- **RAM(GB)**: 8
- **Optimize by**: CPU
- **GPU**: None

![](/assets/img/49/tco03.png){: "width=60%" }

Agora, vamos adicionar os detalhes da nossa infraestrutura de **storage** e **networking**.

Para adicionar detalhes de storage, clique em **+ Add storage**.

**Defina os seguintes valores:**

- **Name**: Server Storage
- **Storage type**: Local Disk/SAN
- **Disk type**: HDD
- **Capacity**: 60 TB
- **Backup**: 120 TB
- **Archive**: 0 TB

![](/assets/img/49/tco04.png){: "width=60%" }

Em networking, defina os seguintes valores:

- **Outbound bandwidth**: 15 TB
- **Destination Region**: Brazil South

![](/assets/img/49/tco05.png){: "width=60%" }

Clique em **Next**.

Ajuste a moeda para **Brazil - Real (R$) BRL**.

![](/assets/img/49/tco06.png){: "width=60%" }

Explore as opções e faça os ajustes necessários, em seguida, clique em **Next**.

## **2.1 Revisando a estimativa de custos de TCO**

Agora, revisaremos os resultados da Calculadora de TCO do Azure.

Em View report, modifique as informações de acordo com sua necessidade, em nosso exemplo, vamos analisar os seguintes custos:

- **Timeframe**: 3 Years
- **Region**: Brazil South

![](/assets/img/49/tco07.png){: "width=60%" }

![](/assets/img/49/tco08.png){: "width=60%" }

![](/assets/img/49/tco09.png){: "width=60%" }

No final da página você pode fazer o download completo do relatório.

![](/assets/img/49/tco10.png){: "width=60%" }

É isso galera, espero que gostem!

Forte Abraço!