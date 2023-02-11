---
layout: post
title: "Overview Azure Virtual Machine Scale Sets e Amazon EC2 Auto Scaling"
author: rpaliosa
date: 2023-02-09 09:00:00 -0500
categories: [Azure, Alta Disponibilidade]
tags: [azure, microsoft, aws, amazon, network, ha, altadisponibilidade]
---

Saudações Rede!!!

O Post de hoje é um rápido Overview sobre Azure Virtual Machine Scale Sets e Amazon EC2 Auto Scaling.

![](/assets/img/54/az-aws01.jpeg){: "width=60%" }

De forma sucinta, tanto o Virtual Machine Scale Set do Azure quanto o EC2 Auto Scaling da AWS possuem 2 objetivos principais.

1. O aumento ou diminuição do poder computacional de uma Virtual Machine , como vCPU e RAM, o que também recebe o nome de "Escala Vertical", porém, NÃO GARANTE alta disponibilidade.

2. O aumento ou diminuição da qtd de Virtual Machine, também chamado de "Escala Horizontal", função crucial quando o OBJETIVO É ALTA DISPONIBILIDADE.

Tanto no Azure quanto na AWS os Scales Verticais e Horizontais podem ocorrer, principalmente, baseados em métricas de Processamento ou Consumo de memória Ram.

Vale destacar também que quando o objetivo do Auto Scale é proporcionar Alta Disponibilidade com o incremento de VM's, entra em ação um camarada fundamental, o Load Balancer, que basicamente fará a distribuição das cargas de trabalho de forma a evitar sobrecarga em uma das VM's do grupo de escala ou o redirecionamento quando houver indisponibilidade em algumas destas VM's.

Complemente o conhecimento em:

 <a href="https://learn.microsoft.com/pt-br/azure/virtual-machine-scale-sets/overview" target="_blank">O que são Conjuntos de Dimensionamento de Máquinas Virtuais?</a> 

  <a href="https://docs.aws.amazon.com/pt_br/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html" target="_blank">O que é o Amazon EC2 Auto Scaling?</a> 