---
layout: post
title: "Kubernetes 101: Docker vs ContainerD"
author: asilva
date: 2024-06-26 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No universo dos contêineres, os runtimes são peças fundamentais para o funcionamento eficiente e seguro das aplicações. No Kubernetes, a escolha do runtime pode impactar significativamente o desempenho e a escalabilidade dos clusters. 

Neste artigo, vamos explorar dois dos principais runtimes de contêiner: **Docker** e **ContainerD**. Vamos entender suas origens, arquiteturas, funcionalidades e como eles se comparam na prática, especialmente em um ambiente Kubernetes.

## **História e evolução**

**Docker**

![](/assets/img/82/runtime01.png){: "width=10%" } 

O <a href="https://docker.com" target="_blank"> Docker</a> foi lançado em 2013 pela Docker, Inc. e rapidamente revolucionou o ecossistema de contêineres. Antes do Docker, a tecnologia de contêineres já existia, mas era complexa e difícil de usar. O Docker simplificou drasticamente a criação, distribuição e execução de contêineres, proporcionando uma ferramenta poderosa e fácil de usar. Com sua interface intuitiva e robusta, o Docker tornou a tecnologia de contêineres acessível a desenvolvedores e operadores, promovendo a adoção generalizada de práticas de desenvolvimento de aplicações cloud-native e arquiteturas de microserviços.

A arquitetura inicial do **Docker** era monolítica, integrando diversas funcionalidades como o Docker Daemon, a CLI (Command Line Interface) e o Docker Compose em uma única plataforma. Isso permitia aos usuários gerenciar todo o ciclo de vida dos contêineres com um único conjunto de ferramentas, simplificando processos como a construção de imagens, o gerenciamento de redes e volumes, e a orquestração de contêineres em clusters pequenos.

A relação entre **Docker** e **Kubernetes** começou a se formar à medida que ambos se tornavam padrões de fato em seus respectivos domínios. Kubernetes, lançado em 2014 pelo **Google**, foi inicialmente projetado para funcionar com Docker, aproveitando suas capacidades para gerenciar contêineres de maneira eficiente. A combinação de Docker e Kubernetes permitiu a criação de ambientes de orquestração de contêineres escaláveis e resilientes, acelerando a adoção de infraestruturas modernas de TI e promovendo a evolução do desenvolvimento de software para um paradigma mais ágil e eficiente.

**ContainerD**

![](/assets/img/82/runtime02.png){: "width=40%" } 

O <a href="https://containerd.io" target="_blank"> ContainerD</a> teve suas origens como parte integrante do Docker, atuando inicialmente como um componente fundamental dentro da arquitetura monolítica do Docker. Em 2017, a Docker, Inc. decidiu separar o **ContainerD** como um projeto independente e transferi-lo para a <a href="https://containerd.io" target="_blank"> Cloud Native Computing Foundation (CNCF).</a> Esta decisão foi motivada pela necessidade de simplificar a arquitetura do Docker e tornar o ContainerD mais modular, eficiente e independente.

Como resultado dessa separação, o **ContainerD** foi projetado para ser um runtime de contêineres leve, focado em desempenho e simplicidade. Ele oferece todas as funcionalidades essenciais para gerenciar o ciclo de vida dos contêineres, desde o download de imagens até a execução, parada e remoção dos contêineres.

A partir da versão **1.24 do Kubernetes**, o **ContainerD** se tornou o runtime padrão, substituindo o Docker. Esta mudança foi motivada pela integração nativa do **ContainerD** com a **Container Runtime Interface (CRI)** do Kubernetes, proporcionando um desempenho otimizado e uma gestão de recursos mais eficiente.

## **Arquitetura no ambiente Kubernetes**

**Docker no Kubernetes**

No ambiente **Kubernetes**, o **Docker** funciona como o runtime de contêineres através de uma camada de compatibilidade chamada **Docker Shim**. A arquitetura se configura da seguinte forma:

![](/assets/img/82/runtime03.png){: "width=40%" } 

- **Docker Daemon:** Gerencia a execução dos contêineres. O Kubelet interage com o Docker Daemon através do Docker Shim.
- **Docker Shim:** Uma camada intermediária que permite ao Kubelet comunicar-se com o Docker Daemon.

Essa arquitetura, embora funcional, introduz uma camada extra de complexidade e overhead, o que pode impactar a eficiência e o desempenho.

Podemos ver que são muitas etapas. O **kubelet** envia uma mensagem para o **dockershim**. O **dockershim** traduz. O **dockershim** envia para o **Docker**. O **Docker** envia para o **containerd**. Muitas mensagens são enviadas de aplicativo para aplicativo até que algo finalmente aconteça. Um grande motivo pelo qual os desenvolvedores do Kubernetes queriam remover o Docker é que há muitos intermediários. Há muita coisa acontecendo, muitas etapas para iniciar um contêiner.

**ContainerD no Kubernetes**

Com a adoção do **ContainerD**, a arquitetura no ambiente **Kubernetes** torna-se mais direta e eficiente:

![](/assets/img/82/runtime04.png){: "width=40%" } 

- **ContainerD Daemon:** Gerencia diretamente os contêineres, sem necessidade de uma camada intermediária como o Docker Shim.
- **CRI (Container Runtime Interface):** O Kubelet interage diretamente com o ContainerD através do CRI, o que simplifica a comunicação e melhora a eficiência.

A arquitetura com **ContainerD** elimina a necessidade do Docker Shim, resultando em uma integração mais nativa e uma redução na complexidade e no overhead.

## **Funcionalidades e comparações**

Como mencionado, inicialmente o **Kubernetes** utilizava o Docker como seu runtime principal. A utilização do Docker Shim permitia essa integração, mas adicionava uma camada extra de complexidade e overhead.

Com a evolução do **Kubernetes**, a comunidade começou a adotar o **ContainerD** como o runtime preferido. **ContainerD** oferece uma integração mais nativa e direta com Kubernetes, eliminando a necessidade do Docker Shim e reduzindo a complexidade. Esta mudança resultou em melhorias significativas de desempenho e eficiência.

**ContainerD** demonstra um desempenho superior ao **Docker** quando integrado ao **Kubernetes**, principalmente devido à sua arquitetura mais enxuta e à eliminação do Docker Shim. Testes e benchmarks revelam que **ContainerD** consome menos recursos de **CPU** e **memória**, resultando em tempos de inicialização de contêineres mais rápidos e melhor escalabilidade.

Em cenários de alta escala, onde a eficiência de recursos é crucial, **ContainerD** se destaca. Sua arquitetura modular possibilita o gerenciamento eficaz de grandes volumes de contêineres, proporcionando melhor desempenho em clusters **Kubernetes** com centenas ou milhares de pods.

## **Outras alternativas**

Além de **Docker** e **ContainerD**, existem outros runtimes de contêineres relevantes e que estão sob a égide da** Cloud Native Computing Foundation (CNCF)**:

**CRI-O**

<a href="https://cri-o.io/" target="_blank"> CRI-O</a> é um runtime de contêineres leve, desenvolvido especificamente para Kubernetes. Focado em conformidade com a especificação CRI (Container Runtime Interface) do Kubernetes, CRI-O é projetado para ser minimalista, mantendo apenas as funcionalidades essenciais para a execução de contêineres. Ele oferece uma alternativa viável ao ContainerD, proporcionando uma integração direta e eficiente com o Kubernetes.

**Kata Containers**

 <a href="https://katacontainers.io/" target="_blank"> Kata Containers</a>  combina as melhores características dos contêineres e das máquinas virtuais (VMs), oferecendo uma solução de contêineres com isolamento baseado em virtualização. Ideal para cenários que exigem maior segurança, Kata Containers é projetado para ser leve e rápido, enquanto fornece um nível adicional de isolamento de segurança.

**rkt**

<a href="https://github.com/rkt/rkt" target="_blank"> rkt</a> (pronunciado "rocket") é um runtime de contêineres desenvolvido pela CoreOS, que agora faz parte da CNCF. rkt foi projetado com um foco em segurança, modularidade e simplicidade, proporcionando uma alternativa robusta ao Docker. Ele oferece um modelo de execução mais seguro e flexível, permitindo a execução de contêineres com diferentes níveis de isolamento e segurança.

## **Conclusão**

A transição do **Docker** para o **ContainerD** como runtime padrão no **Kubernetes** representa uma evolução significativa na orquestração de contêineres em ambientes de produção. O ContainerD oferece uma solução mais leve, eficiente e integrada para gerenciar contêineres em larga escala. Sua arquitetura modular e sua compatibilidade com a especificação CRI do Kubernetes tornam-no uma excelente escolha, proporcionando melhor desempenho, escalabilidade e eficiência de recursos.

Embora o **Docker** continue sendo uma ferramenta robusta, o **ContainerD** e outras alternativas como o **CRI-O** estão se estabelecendo como as melhores opções para ambientes Kubernetes devido às suas capacidades otimizadas e integração nativa.

É isso galera, espero que gostem!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
