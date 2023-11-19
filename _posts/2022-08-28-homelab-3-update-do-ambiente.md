---
layout: post
title: "[Homelab] #3 Update do ambiente"
author: asilva
date: 2022-08-28 09:00:00 -0500
categories: [Homelab, Unicast Lab]
tags: [homelab, network, docker, kubernetes, raspberrypi, ansible, devops, terraform, gitops, k8s, k3s, cluster, routing]
---

Fala galera! Seis tão baum?

Terceiro update do Homelab da Unicast Cloud, e pelo menos por hardware, espero que seja o último!

Acabei esperando para fazer um post só, pois estava esperando o restante dos hardwares e também dos acessórios/peças para ajustar os problemas do post anterior.

Como o cluster estava praticamente pronto, basicamente foi esperar pela chegada do quarto PI4 e começar os ajustes finos.

## **Novos itens**

- 1 x Raspberry Pi 4 de 4GB
- Parafusos de 2,5mm
- Espaçadores de 2,5mm

## **Sobre o primeiro problema encontrado:**

Com a chegada do kit de espaçadores, consegui resolver o problema do HAT POE e também do espaçamento entre as placas, pois agora consegui misturar várias medidas para ajustar uma altura legal.

Não muito alto como estava e nem muito apertado para comprometer a ventilação.

![](/assets/img/33/home3-01.jpg){: "width=60%" }

Sobre o espaçamento do HAT POE, consegue resolver com algumas porcas 2,5mm que vieram no kit de espaçadores.

![](/assets/img/33/home3-02.jpg){: "width=60%" }

![](/assets/img/33/home3-03.jpg){: "width=60%" }

Por aqui, entre mortos e feridos, salvaram se todos!

## **Sobre o segundo problema encontrado:**

Com os cartões SD originais, tudo certo, sistema funcionando perfeitamente e com excelente desempenho.

E para manter o padrão, todos PI4 estão com cartões SD classe 10 Sandisk (Original) de 32GB.

## **Atualizando os componentes do projeto**

- [X] 1 x Roteador MikroTik hAP Ac2 
- [X] 1 x Switch PoE TP-Link TL-SG1008P
- [X] 1 x Dell OptiPlex 3050
- [X] 1 x Raspberry Pi 3B
- [X] 4 x Raspberry Pi 4 (4GB) 
- [X] 4 x PoE HAT
- [X] 1 x Rack Mout
- [X] 4 x Cartões SD classe 10
- [X] 6 x Patch Cord UTP CAT6
- [X] 1 x Case Retroflag SuperPI para PI3

Basicamente é o mesmo setup, tirando somente a atualização do cluster, à princípio eu pensei em pegar um PI4 de 8GB e outros PI4s de 4G, mas no fim acabei optando somente pelo modelo de 4GB, pelo que estudei, não teria muita diferença de desempenho para meu laboratório, mas em preço sim.

Desta forma, o cluster ficou como padrão todos os PIs com 4GB.

Outro ponto importante é que no planejamento inicial também não estava nos planos 4 x PI4s e sim 3 unidades. Acabei optando por 4 unidades para ser possível trabalhar com multicluster no Kubernetes.

## **Novidades e próximos passos**

Se tem algo muito legal em ter um homelab é poder brincar e experimentar várias opções.

A primeira modificação foi no sistema de resfriamento do HAT POE, acabei trocando o FAN por um mais robusto e com RGB. Além de ter ficado mais silencioso, ficou muito show!!!

![](/assets/img/33/home3-04.gif){: "width=60%" }

![](/assets/img/33/home3-05.jpg){: "width=60%" }

A próxima modificação será no OptiPlex, hoje ele está com Windows 2019 Datacenter + Hyper-V, mas andei dando uma olhada nas últimas atualizações do Proxmox e o coração bateu forte!

Irei instalar o Proxmox para fazer alguns testes e assim que possível escrevo sobre o Hypervisor escolhido.

## **Conclusão**

Bom, é isso, agora é brincar e estudar! 

![](/assets/img/33/home3-06.gif){: "width=60%" }

Logo, trago mais atualizações do projeto!

Forte abraço a todos!