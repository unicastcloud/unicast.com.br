---
layout: post
title: "Kubernetes 10 anos: Por que você precisa dominar essa ferramenta"
author: asilva
date: 2024-06-08 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

![](/assets/img/80/k8s01.svg){: "width=60%" } 

A exemplo da série de artigos sobre Terraform que fiz no site, quero aproveitar que o Kubernetes completou 10 anos para começar uma série de artigos relacionados ao mundo de containers, micro serviços e cloud native. Vamos mergulhar nessa jornada fascinante, começando pelo básico: o que é Kubernetes e por que você deveria se importar com ele.

## **O que é Kubernetes?**

![](/assets/img/80/k8s02.png){: "width=60%" } 

<a href="https://kubernetes.io/" target="_blank"> Kubernetes</a>, também conhecido como **K8s**, é uma plataforma open-source para automatizar a implantação, escalabilidade e gerenciamento de aplicações containerizadas, ou seja o famoso: **Orquestrador de Containers**. Originalmente desenvolvido pela <a href="https://www.google.com" target="_blank"> Google</a>, ele foi doado para a <a href="https://www.cncf.io/" target="_blank"> Cloud Native Computing Foundation (CNCF)</a>.

## **História do Kubernetes**

**O início**

Kubernetes foi criado pelo Google e lançado em 7 de junho de 2014, quando o primeiro commit com 250 arquivos e 47.501 linhas de código em Go, Bash e Markdown foi enviado ao GitHub. A ideia era resolver problemas que a empresa enfrentava internamente com sua própria plataforma de orquestração de containers, chamada Borg.

**A evolução**

Desde sua criação, Kubernetes passou por diversas atualizações e melhorias. A versão 1.0 foi lançada em julho de 2015, marcando sua primeira versão estável. A partir daí, a comunidade cresceu exponencialmente, com contribuições de grandes empresas e desenvolvedores ao redor do mundo.

**Doação para CNCF**

Em março de 2016, o Kubernetes foi doado para a CNCF, o que ajudou a solidificar seu papel como padrão de fato para orquestração de containers. Desde então, a CNCF tem coordenado o desenvolvimento do projeto, garantindo que ele permaneça atualizado e relevante.

**O projeto hoje**

Atualmente, Kubernetes continua a ser um dos projetos mais ativos e influentes da CNCF. A versão mais recente, lançada em março de 2024, é a versão 1.31, que trouxe várias melhorias, incluindo a remoção de aproximadamente 1,5 milhão de linhas de código de provedores de cloud específicos, reduzindo o tamanho dos binários principais em cerca de 40%.

Para mais detalhes sobre o histórico de lançamentos e as novas funcionalidades, você pode acessar a <a href="https://kubernetes.io/releases/" target="_blank">página de releases do Kubernetes.</a>

## **Por que usar Kubernetes?**

**Automação e Eficiência**

Kubernetes automatiza muitas das tarefas envolvidas na implantação e gerenciamento de aplicativos em containers, como escalabilidade automática, balanceamento de carga e auto-recuperação de falhas. Isso reduz a carga de trabalho dos desenvolvedores e administradores de sistemas.

**Escalabilidade**

Com Kubernetes, você pode facilmente escalar suas aplicações para cima ou para baixo, conforme a demanda. Isso é especialmente útil para empresas que lidam com variações de tráfego ao longo do tempo.

**Portabilidade**

Aplicações containerizadas em Kubernetes podem ser facilmente movidas entre diferentes ambientes (desenvolvimento, teste, produção) e até mesmo entre diferentes provedores de cloud, garantindo maior flexibilidade e evitando lock-in de fornecedor.

## **Kubernetes nos Principais Provedores de Cloud**

Com a popularidade crescente do Kubernetes, os principais provedores de cloud oferecem suas próprias versões gerenciadas do Kubernetes, facilitando ainda mais sua adoção. Vamos falar sobre os mais relevantes: EKS, AKS e GKE.

**Amazon Elastic Kubernetes Service (EKS)**

![](/assets/img/80/k8s03.png){: "width=60%" } 

O Amazon EKS é o serviço gerenciado de Kubernetes da AWS. Ele facilita a execução do Kubernetes na AWS, gerenciando a infraestrutura de controle (control plane) e permitindo que os usuários se concentrem na execução de suas aplicações. O EKS oferece integração com outros serviços da AWS, como IAM para controle de acesso e CloudWatch para monitoramento.

**Azure Kubernetes Service (AKS)**

![](/assets/img/80/k8s04.png){: "width=60%" } 

O Azure AKS é o serviço gerenciado de Kubernetes da Microsoft Azure. Ele simplifica a implantação e o gerenciamento de clusters Kubernetes, oferecendo recursos como integração com o Azure Active Directory, auto-escalabilidade e monitoramento com Azure Monitor. AKS também facilita a integração com outras ferramentas DevOps da Microsoft, como Azure DevOps.

**Google Kubernetes Engine (GKE)**

![](/assets/img/80/k8s05.png){: "width=60%" } 

O GKE, oferecido pelo Google Cloud, é o serviço gerenciado de Kubernetes que mais se aproxima da implementação original do Kubernetes, dado que foi desenvolvido pela própria Google. GKE oferece recursos avançados como balanceamento de carga integrado, atualizações automáticas de segurança e escalabilidade automática. Além disso, GKE se beneficia da vasta experiência da Google em orquestração de containers.

## **A Importância de Aprender a Gerenciar Kubernetes Gerenciados**

**Facilidade e Confiabilidade**

Os serviços gerenciados de Kubernetes como EKS, AKS e GKE lidam com muitas das complexidades de configurar e manter um cluster Kubernetes, como atualizações de segurança e escalabilidade automática. Isso permite que os desenvolvedores e operadores se concentrem em construir e executar aplicações, em vez de gerenciar a infraestrutura subjacente.

**Integração com Outros Serviços**

Esses serviços gerenciados oferecem integração direta com uma ampla gama de serviços nativos de seus respectivos provedores de cloud, como armazenamento, redes, monitoramento e segurança. Isso facilita a criação de soluções completas e integradas, aumentando a eficiência e a produtividade.

**Habilidades Valorizadas**

Dominar Kubernetes nos principais provedores de cloud é uma habilidade altamente valorizada no mercado de trabalho. Profissionais que sabem como configurar, operar e otimizar clusters Kubernetes gerenciados são muito procurados, especialmente em ambientes de TI modernos que adotam práticas de DevOps e arquitetura de micro serviços.

## **Certificações Kubernetes**

 Linux Foundation oferece várias certificações que podem validar seu conhecimento e habilidades em Kubernetes:

**Kubernetes and Cloud Native Associate (KCNA)**

![](/assets/img/80/k8s06.png){: "width=40% height=40%" w-50 .left } A certificação KCNA é uma introdução aos conceitos e ferramentas fundamentais do Kubernetes e do ambiente cloud native. É ideal para iniciantes que desejam entender o básico antes de avançar para certificações mais complexas.

**Certified Kubernetes Administrator (CKA)**

![](/assets/img/80/k8s07.png){: "width=40% height=40%" w-50 .left } A certificação CKA testa o conhecimento prático sobre a administração de um cluster Kubernetes. É ideal para administradores de sistemas que desejam provar sua competência em Kubernetes.

**Certified Kubernetes Application Developer (CKAD)**

![](/assets/img/80/k8s08.png){: "width=40% height=40%" w-50 .left } A certificação CKAD é focada em desenvolvedores de aplicações que utilizam Kubernetes. Ela testa as habilidades de criação, configuração e implantação de aplicações em um cluster Kubernetes.

**Kubernetes and Cloud Native Security Associate (KCSA))**

![](/assets/img/80/k8s09.png){: "width=40% height=40%" w-50 .left } A certificação KCSA é voltada para profissionais que desejam aprofundar seus conhecimentos em segurança cloud native e Kubernetes. Ela cobre práticas de segurança essenciais para proteger ambientes Kubernetes e cloud native.

**Certified Kubernetes Security Specialist (CKS)**

![](/assets/img/80/k8s10.png){: "width=40% height=40%" w-50 .left } A certificação CKS é voltada para profissionais que desejam demonstrar suas habilidades em segurança dentro de ambientes Kubernetes. Ela cobre aspectos avançados de segurança, como configuração segura de clusters e resposta a incidentes de segurança.

**Kubestronaut Program**

![](/assets/img/80/k8s11.png){: "width=60%" } 

A Linux Foundation oferece um programa especial chamado "**Kubestronaut**" para profissionais que obtêm e mantêm ativas cinco certificações Kubernetes: CKA, CKAD, CKS, KCNA e KCSA. Tornar-se um Kubestronaut demonstra um alto nível de competência e dedicação ao ecossistema Kubernetes e cloud native.

Para mais informações sobre as certificações, visite a <a href="https://kubernetes.io/releases/" target="_blank">página oficial da Linux Foundation.</a> 

## **Sugestões de Aprendizado para Iniciantes**

Se você está começando sua jornada com Kubernetes, aqui estão algumas recomendações de recursos e materiais de estudo:

**Documentação Oficial**

A documentação oficial do Kubernetes é um excelente ponto de partida. Ela cobre desde os conceitos básicos até as configurações mais avançadas:

<a href="https://kubernetes.io/docs/home/" target="_blank">Documentação do Kubernetes</a>  

**Livros Recomendados**

Livros são uma excelente forma de aprofundar seu conhecimento sobre Kubernetes. 

Kubernetes Básico: Mergulhe no futuro da infraestrutura: <a href="https://a.co/d/4BNrUYI" target="_blank">Comprar</a>  

**Cursos Online**

Existem vários cursos online que podem ajudar você a entender e dominar Kubernetes. E se você é assinante e faz parte da comunidade da TFTEC Prime, você pode ser meu aluno no treinamento: **Azure Kubernetes Services AKS.**

![](/assets/img/79/news03.png){: "width=60%" } 

**Treinamento AKS:** <a href="https://portal.tftecprime.com.br/m/c/curso-aks" target="_blank"> Página oficial do treinamento</a>

## **Conclusão**

Dominar Kubernetes pode parecer desafiador no começo, mas os benefícios de automação, escalabilidade e portabilidade fazem o esforço valer a pena. 

Mas não se preocupe, eu estarei aqui para ajudar com uma série de artigos que vão descomplicar o uso dessa poderosa ferramenta. Fique ligado e embarque nessa jornada comigo!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Nos vemos em breve, meus amigos!

Forte Abraço!
