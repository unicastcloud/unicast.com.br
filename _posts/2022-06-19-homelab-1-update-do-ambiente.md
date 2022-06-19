---
layout: post
title: "[Homelab] #1 Update do ambiente"
date: 2022-06-19 09:00:00 -0500
categories: [Homelab, Artigos]
tags: [homelab, network, docker, kubernetes, raspberrypi, ansible, devops, terraform, gitops, k8s, k3s, cluster, routing]
---

Fala galera!

Este é o primeiro update do Homelab da Unicast Cloud, e essa semana começou a chegar os primeiros periféricos e acessórios que comprei no AliExpress.

Com isso, já tenho algumas novidades para atualizar o projeto. Ainda tem muita coisa em trânsito pois eu fiz compras separadas para evitar uma possível taxa de importação. 

Mas como combinado, à medida que for chegando o material eu vou fazer o possível para ir documentando o processo.

Sem mais delongas vamos lá!

### **Essa semana chegou os seguintes itens**

- 3 x cartões micro sd de 64GB
- 1 x cartão micro sd de 128GB
- 3 x leitores de cartão sd usb 2.0
- 10 x patch cords cat6 UTP de 0,50cm
- 1 x case Retroflag superpi para raspberry pi 3
- 1 x case para cluster com 4 layers

### **Problemas encontrados**

Se você já importou alguma coisa da china, sabe que é comum ter alguns problemas com os produtos, seja por conta da qualidade, problema ou mesmo diferenças no produto comprado.

Até agora o saldo é positivo, os cartões de marca genérica estão funcionando sem maiores problemas e o restante dos itens veio de acordo com o anúncio.

De problemas, somente o case que vou utilizar no cluster de raspberry. Fiz a compra do case para ser utilizado com 4 placas, mas o vendedor acabou enviando uma das bases erradas e com isso só vou conseguir utilizar 3 placas.

Como é um produto da china e leva bastante tempo para chegar, pedir a troca está fora de cogitação. Como não é um item caro, vou acabar comprando outro kit para resolver o problema e ficar com algumas peças sobressalentes.

### **Em fase de testes**

Essa semana já coloquei o Switch PoE TP-Link TL-SG1008P e o Dell OptiPlex 3050 para funcionar.

Fiz a instalação do Windows Server 2019 Datacenter e configurei a role do Hyper-V. Criei uma VM com Debian 11 para testar a conectividade com a rede do laboratório e tudo está funcionando sem maiores problemas.

![](/assets/img/25/homelab1-1.png){: .shadow style="max-width: 80%" } _Windows Server 2019 Datacenter + Hyper-V + Debian 11_

Agora é só fazer os ajustes finos e deixá-lo pronto para as aulas.

Outro carinha que já entrou em testes/produção é o meu antigo Raspberry Pi 3, fiz a instalação do Pi-Hole e montei ele nessa linda e nostálgica case da Retroflag.

Simplesmente sensacional, não tem como não se apaixonar 😍😍😍!

Como mencionei no meu post no Linkedin: Este é o DNS server mais nostálgico que você já viu na vida! 🎮💻🐧

![](/assets/img/25/homelab1-2.jpeg){: .shadow style="max-width: 40%" }

![](/assets/img/25/homelab1-3.jpeg){: .shadow style="max-width: 40%" }

![](/assets/img/25/homelab1-4.jpeg){: .shadow style="max-width: 40%" }

![](/assets/img/25/homelab1-5.jpeg){: .shadow style="max-width: 40%" }

### **Sobre o Pi-Hole**

O Pi-Hole é um bloqueador de anúncios em nível de network que atua como um DNS em sua rede local, assim, quando você acessa algum site e esse site contém algum dos domínios da blacklist, a requisição para exibir o anúncio será bloqueada.

Para que isso funcione, você precisa definir o Pi-Hole como seu servidor DNS padrão. Desta forma ele será responsável por direcionar todo o tráfego da Internet para dentro e para fora da rede local. 

No meu caso, eu utilizo um Mikrotik como roteador central da minha rede, nele eu recebo a conexão com meu provedor de internet e também controlo todos os dispositivos da minha rede local.

O processo foi bem simples, após configurar o Pi-Hole só precisei modificar as configurações de DNS do meu DHCP para o IP do Pi-Hole. Desta forma, estou garantindo que nenhum dispositivo utilize outro DNS a não ser o interno.

Em apenas um dia de uso e já é possível ver bons resultados!

![](/assets/img/25/homelab1-6.png){: .shadow style="max-width: 80%" }

Além da funcionalidade de DNS sinkhole o Pi-Hole tem outras funções bem interessantes.

The Pi-hole® is a [DNS sinkhole](https://en.wikipedia.org/wiki/DNS_Sinkhole) that protects your devices from unwanted content without installing any client-side software.

- **Easy-to-install**: our versatile installer walks you through the process and takes less than ten minutes
- **Resolute**: content is blocked in _non-browser locations_, such as ad-laden mobile apps and smart TVs
- **Responsive**: seamlessly speeds up the feel of everyday browsing by caching DNS queries
- **Lightweight**: runs smoothly with [minimal hardware and software requirements](https://docs.pi-hole.net/main/prerequisites/)
- **Robust**: a command line interface that is quality assured for interoperability
- **Insightful**: a beautiful responsive Web Interface dashboard to view and control your Pi-hole
- **Versatile**: can optionally function as a [DHCP server](https://discourse.pi-hole.net/t/how-do-i-use-pi-holes-built-in-dhcp-server-and-why-would-i-want-to/3026), ensuring *all* your devices are protected automatically
- **Scalable**: [capable of handling hundreds of millions of queries](https://pi-hole.net/2017/05/24/how-much-traffic-can-pi-hole-handle/) when installed on server-grade hardware
- **Modern**: blocks ads over both IPv4 and IPv6
- **Free**: open source software that helps ensure _you_ are the sole person in control of your privacy

Segue link do projeto: <a href="https://github.com/pi-hole/pi-hole/blob/master/README.md" target="_blank"> Pi-Hole</a>

### **Atualizando os componentes do projeto**

- [X] Roteador MikroTik hAP Ac2 
- [X] Switch PoE TP-Link TL-SG1008P
- [X] Dell OptiPlex 3050
- [X] Raspberry Pi 3B
- [ ] Raspberry Pi 4 (4GB) 
- [ ] Raspberry Pi 4 (8GB) 
- [ ] PoE HAT
- [X] Rack Mout (parcialmente)
- [X] Carões SD classe 10
- [X] Patch Cord UTP CAT6
- [X] Case Retroflag SuperPI para PI3

### **Conclusão**

Estou muito animado com o progresso do projeto, o case do PI3 é um show à parte, deixou o cenário lindo por aqui, e quem vê acha mesmo que é um super Nintendo na sua versão miniatura.

O proposito foi alcançado LoL. 

O cluster K8S ainda vai demorar um pouco, pois eu tenho que adquirir os PI4, como não é algo relativamente barato, vai ter que ser em partes.

Mas com o **Dell OptiPlex 3050** em produção, já posso começar o material dos primeiros vídeos do canal do Youtube. Espero que em até 2 semanas já tenha o primeiro vídeo disponível no canal.

É isso galera, assim que for possível vou trazendo as atualizações do projeto!

Forte abraço a todos!
