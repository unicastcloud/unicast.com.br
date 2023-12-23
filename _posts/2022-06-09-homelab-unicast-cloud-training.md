---
layout: post
title: "[Homelab] Unicast Cloud Training"
author: asilva
date: 2022-06-09 09:00:00 -0500
categories: [Homelab, Unicast Lab]
tags: [homelab, network, docker, kubernetes, raspberrypi, ansible, devops, terraform, gitops, k8s, k3s, cluster, routing]
---

Fala galera! Seis tão baum?

Este é o primeiro de uma série de artigos que irei produzir por aqui, documentando minhas experiências com meu homelab.

Antes de mais nada, vamos ajustar algumas ideias.

Uma das melhores maneiras de aprender sobre sistemas, aplicativos e tecnologia em geral é criando laboratórios práticos.

Hoje em dia é muito fácil simular um ambiente de estudo/desenvolvimento em casa, qualquer PC mais atual tem a capacidade de rodar máquinas virtuais utilizando softwares como virtualbox, vmware ou até mesmo docker. 

E se mesmo assim, você não tenha um PC suficientemente bom para criar seus laboratórios, você pode facilmente executá-lo em qualquer provedor de nuvem pública, a exemplo do Microsoft Azure.

Hoje em dia estamos bem! Na minha época não era assim...

![](/assets/img/23/homelab1.jpeg){: "width=60%" } _Raspberry Pi 3B_

Antigamente não existia computação em nuvem, computador em casa era luxo, internet, só discada e depois da meia noite, se não seu pai teria que vender um rim para poder pagar a conta telefônica. LoL :)

Brincadeiras à parte, antigamente não era muito fácil aprender ou mesmo testar seus conhecimentos, era necessário estar trabalhando na área para ter contato com toda essa tecnologia. 

Não tinha muito o que fazer, era ficar de olho em hardware usado e quando alguém não queria mais a gente levava para casa.

Nos últimos anos eu acabei utilizando bastante meu PC, fiz um bom upgrade e deixei ele suficientemente bom para suportar meus laboratórios virtuais, porém, a maior parte dos meus laboratórios são executados no Azure.

Se você já me acompanha a algum tempo, sabe que estou produzindo material técnico para o meu canal no YouTube Unicast Cloud Training, e este homelab será parte fundamental do material que irei compartilhar com vocês.

Montar um homelab pode ser extremamente divertido e muito educativo. Se você estiver pensando em construir um, comece pequeno e vá expandindo no seu próprio ritmo. 

![](/assets/img/23/homelab2.jpeg){: "width=60%" } _TP-Link TL-SG1008P_

O meu caso não é diferente, vou começar com o que tenho e vou completar com alguns equipamentos novos e usados.

E é por este motivo que quero documentar todo o processo, pois vai ser bem bacana ver a evolução física e lógica deste projeto.

Na minha opinião, esse é o maior benefício de um homelab. É pura diversão, um playground. É o lugar onde posso colocar em prática todo meu amor pela tecnologia. Simplesmente é um lugar para me divertir enquanto aprimoro minhas habilidades.

Dito isso, criei um repositório no **GitHub** para documentar e centralizar todas as informações do Homelab. <https://github.com/asilvajunior/homelab>

Aproveita e dá uma estrelinha lá para fortalecer LoL :)

## **1. Objetivo do projeto**

- Criar conteúdo técnico para o canal do Youtube.
- Estudar e melhorar conhecimentos em DevOps e IaC.
- Criar laboratório de estudos para certificações.

## **2. Premissas do projeto**

- Suportar e executar cargas de trabalho reais
- Baixo consumo energético
- Baixo custo de investimento
- Baixo ruído 
- Pouco espaço físico
- Escalável

## **3. Componentes do projeto**

- [X] Roteador MikroTik hAP Ac2 
- [X] Switch PoE TP-Link TL-SG1008P
- [X] Dell OptiPlex 3050
- [X] Raspberry Pi 3B
- [ ] Raspberry Pi 4 (4GB) 
- [ ] Raspberry Pi 4 (8GB) 
- [ ] PoE HAT
- [ ] Rack Mout
- [X] Carões SD classe 10
- [ ] Patch Cord UTP CAT6

## **4. Estado atual do projeto**

| Device                 | CPU        | RAM   | Storage  | Purpose    | Note                |
| -----------------------|------------|-------|----------|------------|---------------------|
| **Dell OptiPlex 3050** | I7-6700    | 16GB  | 500GB    | Hypervisor | Comprei usado no ML |
| **TP-Link TL-SG1008P** | N/A        | N/A   | N/A      | Switch PoE | Comprei novo no ML  |
| **MikroTik hAP Ac2**   | IPQ-4018   | 128MB | 16MB     | Router     | Já tinha            |
| **Raspberry Pi 3B**    | Cortex-A53 | 1GB   | 64GB     | Pi-hole    | Já tinha            |

É isso galera, espero que gostem.

Forte abraço a todos!