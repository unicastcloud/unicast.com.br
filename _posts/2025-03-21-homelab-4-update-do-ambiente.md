---
layout: post
title: "[Homelab] #4 Update do ambiente"
author: asilva
date: 2025-03-21 09:00:00 -0500
categories: [Homelab, Unicast Lab]
tags: [homelab, network, docker, kubernetes, raspberrypi, ansible, devops, terraform, gitops, k8s, k3s, cluster, routing]
---

Fala galera! Seis tão baum?

Depois do update no meu cluster de Raspberry Pi, seguimos com as atualizações do home lab. Como mencionei anteriormente, estou montando um **PiNAS** — um **NAS caseiro com Raspberry Pi**. Recentemente finalizei a compra dos componentes, e agora, com tudo em mãos, chegou a hora de começar a brincadeira.

![](/assets/img/110/rpi-homelab-01.png){: "width=60%" }

Este artigo não é somente sobre o **PiNAS**, mas sim sobre toda a evolução do meu home lab. Vou compartilhar com vocês o que mudou no meu setup, o que funcionou e o que ainda precisa melhorar.

## **Atualizando o Cluster Kubernetes**

Para quem acompanha meu projeto, talvez se lembre que meus Raspberry Pi são alimentados via **PoE (Power over Ethernet)**. Para isso, cada placa possui um **HAT** com dissipadores passivos e um mini cooler.

![](/assets/img/110/rpi-homelab-02.jpg){: "width=60%" }

No entanto, o primeiro problema surgiu aí: com o tempo, esses mini fans começaram a fazer barulho excessivo. Como são muito pequenos, não têm manutenção adequada — uma simples limpeza não resolveu. 

**Resultado**: quando as quatro placas estavam ligadas, o barulho atrapalhava bastante o dia a dia no escritório.

Por isso, deixei o cluster desligado por um bom tempo, só ligando em caso de necessidade. Mas esse não era o objetivo. Quero meu cluster funcionando **24/7**.

**Solução temporária: remover os fans**

A solução que encontrei foi radical: removi os fans. O silêncio foi imediato — uma maravilha! Mas aí veio o outro problema: temperatura. A combinação placa + HAT PoE sem ventilação virou praticamente uma churrasqueira. Lembrou os antigos AMD Duron "du quente"...

![](/assets/img/110/rpi-homelab-03.png){: "width=60%" }

## **Novo Rack e reestruturação física**

Diante do problema, fui atrás de um novo case onde pudesse instalar um sistema de resfriamento mais robusto, silencioso e com possibilidade de manutenção.

**O novo rack**

Encontrei um kit no AliExpress com cinco baias, ideal para acomodar:

- O cluster Kubernetes
- Meu DNS com Pi-hole
- E ainda sobrou espaço para o Raspberry Pi 3 (apelidado de Pi-hole), que agora alimenta os fans frontais via USB.

A distribuição atual ficou assim:

- Na base: Pi 3 (Pi-hole)
- Acima: Worker 1, Worker 2, Worker 3
- No topo: Master Node

O novo rack usa fans de 8 cm, que são mais silenciosos do que os dos HATs e estão conseguindo manter uma temperatura aceitável no cluster. A construção do rack é razoável, com alguns pequenos problemas, mas no geral, estou satisfeito: consegui agrupar tudo em um só lugar, liberando espaço para o PiNAS.

## **Problemas com energia e nova solução**

Outro desafio no meu home lab sempre foi a questão da alimentação elétrica. Com tantos dispositivos, era um caos de cabos, fontes e tomadas. Desta vez, decidi investir em um hub USB de alta potência para organizar tudo e alimentar periféricos e iluminação do escritório.

**Lista de dispositivos que precisava alimentar:**

1. Raspberry Pi 3 (Pi-hole-) via USB-C x Micro USB
2. Raspberry Pi 5 (PiNAS) + HAT SATA via USB-C x DC 12V (5.5x2.5mm)
3. Painel de LED Divoom via USB-A
4. Letreiro de LED via USB-A

**A solução: Carregador Rocoren GaN 200W com 6 portas**

![](/assets/img/110/rpi-homelab-04.png){: "width=60%" }

**Especificações principais:**

- **USB-C**
  - C1: Até 100W
  - C2: Até 65W
  - C3: Até 45W
  - C4: Até 30W

- **USB-A**
  - A1/A2: Até 15W cada

**Distribuição das portas:**

- Pi 5 + HAT SATA: C1 (100W) + C2 (65W com saída 12V)
- Pi 3: C3 (45W)
- Painel e letreiro de LED: A1 e A2

![](/assets/img/110/rpi-homelab-05.png){: "width=60%" }

Para minha surpresa, tudo funcionou perfeitamente, especialmente o HAT SATA, que eu achava que daria problema. Usei um cabo USB-C PD de 12V para o conector DC exigido. A organização dos cabos ficou impecável — talvez a parte mais satisfatória de toda a reestruturação!

![](/assets/img/110/rpi-homelab-06.png){: "width=60%" }

## **Construção do PiNAS com OpenMediaVault**

Com a parte energética resolvida, avancei para a montagem do PiNAS.

**Componentes usados:**

- Raspberry Pi 5
- HAT SATA da RADXA
- HDs SSDs
- Pequeno rack reaproveitado do cluster antigo

![](/assets/img/110/rpi-homelab-07.png){: "width=60%" }

O HAT da RADXA vem com tudo necessário para instalação. Apenas adicionei um rack personalizado para combinar com o restante do cluster.

![](/assets/img/110/rpi-homelab-08.png){: "width=60%" }

**Instalação do OpenMediaVault (OMV)**

Depois da montagem física, instalei o OpenMediaVault, formatei os discos e configurei o RAID. Não entrarei em detalhes técnicos aqui, pois o foco do post é a jornada de construção, mas se quiserem, posso escrever outro artigo só sobre isso.

![](/assets/img/110/rpi-homelab-09.png){: "width=60%" }

Recomendo muito o vídeo do Caio Delgado, que tem praticamente o mesmo setup que o meu e fez um guia completo em vídeo/artigo.

Caio Delgado: <a href="https://www.youtube.com/watch?v=zJFJnzJMDfg" target="_blank">Build your Backup Server with Raspberry Pi TODAY!
</a> 

## **Resultado Final**

Agora, em termos de hardware, estou satisfeito. Não pretendo mudar nada por um bom tempo. A próxima etapa — e talvez a mais desafiadora — será a instalação e configuração de todos os serviços que desejo rodar no cluster.

Pretendo escrever artigos sobre cada uma das stacks, então vem muita coisa boa por aí!

## **Referências e Repositório**

Deixei as referências dos equipamentos utilizados:

- Unicast Cloud Homelab: <a href="https://github.com/asilvajunior/homelab" target="_blank">GitHub</a>  
- Rack Raspberry Pi: <a href="https://pt.aliexpress.com/item/1005003171683148.html?spm=a2g0o.order_list.order_list_main.67.3664caa4ebEN8i&gatewayAdapt=glo2bra" target="_blank">AliExpress</a> 
- Rocoren 200w 6 portas GAN: <a href="https://pt.aliexpress.com/item/1005007986945103.html?spm=a2g0o.order_list.order_list_main.62.3664caa4ebEN8i&gatewayAdapt=glo2bra" target="_blank">AliExpress</a> 
- Cabo USB-C x DC5525: <a href="https://pt.aliexpress.com/item/1005005960326074.html?spm=a2g0o.order_list.order_list_main.57.3664caa4ebEN8i&gatewayAdapt=glo2bra" target="_blank">AliExpress</a> 
- Cooler Argon THRML 30mm para Raspberry Pi 5: <a href="https://pt.aliexpress.com/item/1005006447904597.html?spm=a2g0o.order_list.order_list_main.52.3664caa4ebEN8i&gatewayAdapt=glo2bra" target="_blank">AliExpress</a> 
- Radxa Penta SATA HAT para Raspberry Pi 5: <a href="https://pt.aliexpress.com/item/1005006670976946.html?spm=a2g0o.order_list.order_list_main.47.3664caa4ebEN8i&gatewayAdapt=glo2bra" target="_blank">AliExpress</a> 
- Fan 8cm USB: <a href="https://pt.aliexpress.com/item/1005006620383270.html?spm=a2g0o.order_list.order_list_main.35.3664caa4ebEN8i&gatewayAdapt=glo2bra" target="_blank">AliExpress</a> 

Se este artigo ou o repositório te ajudaram de alguma forma, não esquece de passar por lá e deixar uma ⭐!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
