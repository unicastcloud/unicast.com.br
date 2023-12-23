---
layout: post
title: "[Homelab] #2 Update do ambiente"
author: asilva
date: 2022-07-02 09:00:00 -0500
categories: [Homelab, Unicast Lab]
tags: [homelab, network, docker, kubernetes, raspberrypi, ansible, devops, terraform, gitops, k8s, k3s, cluster, routing]
---

Fala galera! Seis tão baum?

Segundo update do Homelab da Unicast Cloud, essa semana recebi mais alguns periféricos e acessórios que comprei no Ali Express.

Na medida do possível, estou começando a montar os hardwares e dando início nos testes.

## **Essa semana chegou os seguintes itens**

- 2 x Raspberry Pi 4 de 4GB
- 4 x POE Hat para Raspberry Pi 4
- 2 x Cartões micro SD 1TB

## **Problemas encontrados**

Problema é o que não faltou essa semana, precisei quebrar a cabeça e testar várias possibilidades para tentar reverter os encontrados.

A premissa mais importante do meu projeto, seria ligar meu cluster raspberry pi utilizando a tecnologia PoE, com isso precisei comprar um HAT POE para acoplar no raspberry.

Bom, o maior problema foi justamente com o PoE.

## **Primeiro problema:**

Ao acoplar o HAT no raspberry pi, você precisa utilizar espaçadores de 2,5mm para parafusar o conjunto e para minha surpresa o espaçador que vem o HAT e menor que o necessário. Logo, não tem muito o que fazer, preciso arrumar algo para ajustar a diferença e conseguir parafusar o HAT.

Como mencionei no primeiro post, o case para o cluster veio errado e vou precisar comprar mais um para poder compor todas as partes, nele, vei alguns spacers de acrílico, vai me fazer falta lá na frente, mas é o que tenho de momento para resolver o problema de espaçamento do HAT.

![](/assets/img/28/home2-01.jpg){: "width=60%" }

Resolver "mais ou menos", o spacer acabou sendo um pouco maior e com isso, quando o HAT está acoplado ele fica um pouco acima, não encaixando o conector por inteiro na GPIO do raspberry pi.

![](/assets/img/28/home2-02.jpg){: "width=60%" }

Outro detalhe é no espaçador entre as placas, com o HAT acoplado o rasbberry fica maior e com isso a lâmina de acrílico fica pegando na placa, a alternativa foi dobrar os espaçadores, não ficou feio, mas também não ficou bonito. E novamente, vai me fazer falta lá na frente.

![](/assets/img/28/home2-03.jpg){: "width=60%" }

Conclusão, resolvi os problemas, pero no mucho!

Comprei um kit de espaçadores de 2.5mm no aliespress, e espero que com ele eu consiga resolver os problemas do HAT e do rack.

![](/assets/img/28/home2-04.png){: "width=60%" }

## **Segundo problema**

Ao começar os primeiros testes para validar o funcionamento do raspberry pi com o HAT PoE, me deparei com algo muito estranho. Ao tentar acessar o sistema operacional, vi que o PI4 estava demorando muito para pegar IP via DHCP, e depois que ele de fato estava com conectividade, o acesso via SSH também estava extremamente lento.

A nível de não ter como usar, um simples **apt-get update** estava demorando quase 30 minutos. 

Na minha cabeça, as primeiras causas seriam: HAT PoE ou cartão SD.

Sem problemas, tenho outros HATs e cartões, bora testar!

Troca cartão, troca HAT e o problema persiste, comecei a pensar que era o HAT, pois como não tenho multímetro não tinha como aferir se estava chegando energia necessária para o PI4. Apesar que a olho nu estava tudo funcionando sem maiores problemas.

![](/assets/img/28/home2-05.png){: "width=60%" }

Algo que era até simples de eliminar se eu tivesse uma fonte para ligar o PI4, a que tenho aqui só serve no PI3. Desta forma ficou somente a validação do achômetro.

Como não tinha como validar se o HAT estava com problemas, o que também estava meio que tirando de cabeça, pois testei 4 peças e todas com o mesmo comportamento, foquei nos cartões.

Testei vários cartões de capacidade diferente e o problema de lentidão persistia.

Como os cartões são **Xing Ling**, resolvi fazer um teste com um cartão descente de 256GB da Sandisk Extreme Pro que uso na câmera.

Mesmo problema. 

Ou seja, hora de condenar o HAT PoE. Não satisfeito, e nervoso é claro, resolvi fazer um último teste, peguei meu cartão de 16GB Sandisk Ultra que está no PI3 com Pi-Hole instalado e pluguei no PI4 com HAT PoE.

E para minha surpresa! Insta DHCP, insta ssh e OS fluindo rápido e lindo como deveria!

Por um lado, estava feliz que estava funcionando, por outro, uma dúvida sem tamanho, se não é o HAT, então são os cartões **Xing Ling**, tá, mas por que o cartão de 256GB da Sandisk Extreme Pro não funcionou bem?

Até agora, não sei explicar! Preciso procurar mais sobre, pois não tenho ideia do que pode ser. A relação do sistema estar lento por conta do cartão, faz sentido, uma vez que o SD é o HD do PI4, mas essa relação de tamanho em GBs com o HAT não faz o menor sentido.

Bom, hora de arriscar, fui ao mercado livre e comprei 2 x cartões da Sandisk 32GB Ultra, a exemplo do meu de 16G que está no PI3 e que estava funcionando perfeitamente no PI4 + HAT PoE.

O cartão é o mesmo, mas a quantidade de GBs diferente, isso foi proposital até mesmo para tirar de cabeça essa relação absurda entre o tamanho do SD e o HAT PoE.

Com a chegada dos novos cartões já fui logo instalando o sistema para testar, com aquele frio na barriga, mas fui!

Não é à toa que nem abri o segundo, qualquer coisa ainda existia um mundo onde poderia pedir a devolução!

Mas para minha felicidade, mesmo que parcial (descobri que os cartões **Xing Ling**, não prestam e vou ter que ver o que fazer com eles) o PI4 pegou DHCP muito rápido e a exemplo do cartão de 16GB, funcionou perfeitamente!

Ou seja, descoberto o problema. Os cartões **Xing Ling**, são uma porcaria e não funcionou de forma alguma, porém, ainda vou fazer um teste com eles no PI3 com fonte de alimentação, ainda acho que sim, tem algo de errado quando o PI4 está com o HAT PoE, é meio doido, mas faz algum sentido.

![](/assets/img/28/home2-06.jpg){: "width=60%" }

Agora, a pergunta que fica é: por que o cartão original da Sandisk Ultra funciona e o Extreme Pro não?

Eu não tenho ideia, só sei que nos próximos PIs vou usar o mesmo modelo e tentar não ter dor de cabeça.

Aliás, depois que o primeiro deu certo, testei o segundo cartão no segundo conjunto de PI4 + HAT PoE e funcionou perfeitamente.

Ou seja, por hora dá para seguir a vida e começar os testes com o cluster Kubernetes!

![](/assets/img/28/home2-07.jpg){: "width=60%" }

## **Atualizando os componentes do projeto**

- [X] Roteador MikroTik hAP Ac2 
- [X] Switch PoE TP-Link TL-SG1008P
- [X] Dell OptiPlex 3050
- [X] Raspberry Pi 3B
- [X] Raspberry Pi 4 (4GB) 
- [ ] Raspberry Pi 4 (8GB) 
- [X] PoE HAT
- [X] Rack Mout (parcialmente)
- [X] Cartões SD classe 10
- [X] Patch Cord UTP CAT6
- [X] Case Retroflag SuperPI para PI3

## **Conclusão**

Bom, mesmo diante dos problemas encontrados até agora, o projeto está andando, precisa de alguns ajustes, mas já trouxe alguns aprendizados. E de certo modo, eu já esperava ter alguns destes problemas, o fato de comprar na china, demorar para chegar, você está olhando somente uma foto, enfim, muitas possibilidades de dar algo errado.

Mas tudo tem seu lado bom, o fato de ter que resolver esses problemas não é ruim, pois na vida real é assim, quem nunca foi reestruturar um datacenter e teve vários destes problemas?

Faltou um parafuso, porca gaiola, um switch que foi para outro rack e agora o cordão ótico não dá o tamanho, várias coisas acontecem mesmo que você tenha planejado tudo, tenha mapeado cada item que seria necessário na hora da atividade, sempre algo foge do controle, e nessas horas que você precisa estar preparado para contornar e achar a melhor solução.

E é por isso que eu digo a você: é muito interessante você ter um homelab, pois ele vai trazer vários cenários do dia a dia de um profissional de TI, sem a abstração de nuvem que estamos acostumados hoje.

Coisas simples que talvez você não saiba ou já faz muito tempo que fez, como crimprar um RJ45!

E tenha sempre em mente, você não irá acertar de primeira, vai ter algum prejuízo aqui ou ali, escolhas boas e ruins, mas faz parte do jogo!

Por mais que você tenha alguma dor de cabeça, você irá se divertir bastante com a experiência de ter um homelab.

Logo, trago mais atualizações do projeto!

Forte abraço a todos!


