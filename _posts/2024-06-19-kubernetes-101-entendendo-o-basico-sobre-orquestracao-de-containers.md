---
layout: post
title: "Kubernetes 101: Entendendo o básico sobre orquestração de containers"
author: asilva
date: 2024-06-19 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Chegou a hora de começar nossa série de artigos sobre Kubernetes!

A tecnologia de containers revolucionou a forma como desenvolvemos, implementamos e gerenciamos aplicações. Kubernetes, uma das ferramentas mais populares para a orquestração de containers, tornou-se um pilar essencial para aplicações modernas. Neste artigo, vamos explorar o básico sobre Kubernetes e entender por que ele é uma peça fundamental no ecossistema de containers.

## **Um pouco sobre Containers, Microserviços, Cloud Native e Docker**

**Containers**

Containers são unidades leves e portáteis de software que incluem tudo o que uma aplicação precisa para rodar: código, runtime, bibliotecas e configurações do sistema. Eles permitem que as aplicações sejam executadas de maneira consistente em diferentes ambientes, desde a máquina do desenvolvedor até os servidores de produção.

**Microserviços**

Microserviços são uma abordagem arquitetural para o desenvolvimento de software onde a aplicação é composta por pequenos serviços, cada um executando um processo de negócio específico e comunicando-se através de APIs bem definidas. Isso facilita a escalabilidade, a manutenção e a evolução das aplicações.

**Cloud Native**

O termo "Cloud Native" refere-se a um conjunto de práticas e tecnologias utilizadas para construir e executar aplicações escaláveis e resilientes em ambientes de nuvem. Aplicações Cloud Native são desenhadas para tirar proveito das vantagens da computação em nuvem, como escalabilidade elástica, automação e serviços gerenciados.

**Docker**

Docker é uma plataforma que facilita a criação, distribuição e execução de containers. Ele padroniza a forma como os containers são construídos e executados, proporcionando uma base sólida para a implementação de arquiteturas de microserviços e aplicações Cloud Native. Desde sua introdução, Docker popularizou o uso de containers ao simplificar significativamente o processo de empacotamento e distribuição de software. Isso permitiu que desenvolvedores e operações trabalhassem de maneira mais integrada e eficiente, criando um ecossistema de containers que é fácil de gerenciar e escalar.

## **O que é o Kubernetes?**

O **Kubernetes** é uma plataforma **open-source para a orquestração de containers**, desenvolvida originalmente pelo Google e atualmente mantida pela **Cloud Native Computing Foundation (CNCF)**. Ele automatiza a implantação, a escalabilidade, a manutenção e a operação de aplicações em containers, permitindo que os desenvolvedores e as equipes de operações se concentrem em entregar valor aos usuários.

## **Como o Kubernetes funciona?**

O Kubernetes é uma ferramenta complexa com uma arquitetura robusta e modular. Compreender como ele funciona é essencial para tirar o máximo proveito de suas capacidades. 

A arquitetura do Kubernetes é baseada em um modelo de **master-worker**, que inclui vários componentes distribuídos em nodes do cluster.

Vamos detalhar a arquitetura do Kubernetes e seus principais componentes.

![](/assets/img/81/k8s-101-a-1.png){: "width=40%" } 

**Master Node**

O Master Node é o cérebro do cluster Kubernetes. Ele gerencia a implantação e o estado desejado das aplicações. Os principais componentes do Master Node são:

- **Kube-apiserver**: Interface de comunicação do cluster, que expõe a API do Kubernetes. Todos os componentes internos e externos interagem com o cluster através desta API.
- **Etcd**: Armazena dados de configuração e estado do cluster de forma distribuída e consistente. É o banco de dados chave-valor de alta disponibilidade do Kubernetes.
- **Kube-scheduler**: Responsável por alocar pods aos nós disponíveis com base em requisitos de recursos e outras restrições.
- **Kube-controller-manager**: Conjunto de controladores que gerenciam o estado do cluster, como controle de replicação, endpoints e contas de serviço.

**Worker Nodes**

Os Worker Nodes são responsáveis por executar as cargas de trabalho em containers. Eles reportam seu estado ao Master Node e recebem instruções dele. Os principais componentes dos Worker Nodes são:

- **Kubelet**: Agente que roda em cada node e garante que os containers estão rodando dentro dos pods. Ele se comunica com o Kube-apiserver para receber instruções.
- **Kube-proxy**: Mantém regras de rede nos nodes e permite a comunicação de rede dentro do cluster. Ele gerencia o balanceamento de carga e a comunicação entre os serviços.
- **Container Runtime**: O software que roda os containers. O Kubernetes suporta vários runtimes e atualmente o **containerd** é o container runtime padrão.

## **Comunicação e fluxo de dados**

![](/assets/img/81/k8s-101-a-2.png){: "width=40%" } 

1. **Kube-apiserver**: Recebe solicitações de criação, modificação ou exclusão de recursos (como pods, serviços, etc.) através da API RESTful.
2. **Etcd**: Armazena o estado desejado e atual do cluster.
3. **Kube-scheduler**: Analisa os pods não alocados e os distribui nos nodes disponíveis com base em recursos e políticas definidas.
4. **Kube-controller-manager**: Monitora o estado do cluster e garante que o estado atual corresponda ao estado desejado, executando ações corretivas quando necessário.
5. **Kubelet**: Recebe instruções do Kube-apiserver para iniciar ou parar containers e monitora a saúde dos pods.
6. **Kube-proxy**: Gerencia as regras de rede para permitir a comunicação entre os serviços do Kubernetes.

## **Quais problemas o Kubernetes resolve?**

Com a popularização do Docker, o uso de containers se tornou comum em muitos ambientes de desenvolvimento e produção. No entanto, à medida que as aplicações começaram a escalar, surgiram novos desafios. Gerenciar um grande número de containers de forma manual tornou-se inviável, e foi aí que o Kubernetes entrou em cena.

Gerenciar containers individualmente pode ser uma tarefa árdua, especialmente quando se lida com centenas ou milhares de instâncias. Kubernetes automatiza esse processo, permitindo que os containers sejam implantados, escalados e mantidos de forma eficiente.

O Kubernetes oferece mecanismos internos para balanceamento de carga e descoberta de serviços. Isso significa que, ao implementar um serviço, você não precisa se preocupar em configurar balanceadores de carga externos ou mecanismos de descoberta de serviço. Kubernetes faz isso automaticamente, distribuindo o tráfego de rede para garantir que as aplicações permaneçam acessíveis e performáticas.

Uma das características mais poderosas do Kubernetes é sua capacidade de autocura. Se um container falha, Kubernetes detecta o problema e reinicia automaticamente o container, garantindo alta disponibilidade e resiliência das aplicações. Pode ajustar automaticamente o número de containers com base na carga de trabalho atual. 

Além disso, se uma atualização causar problemas, você pode facilmente reverter para uma versão anterior, minimizando o impacto no usuário final.

## **Porque usar o Kubernetes**

Uma das maiores vantagens do Kubernetes é a automação de tarefas repetitivas, como implantação, escalabilidade e manutenção de containers. Isso libera tempo e recursos das equipes de desenvolvimento e operações, permitindo que se concentrem em inovação e no desenvolvimento de novas funcionalidades.

É compatível com múltiplos ambientes de nuvem, incluindo nuvens públicas, privadas e on-premises. Isso proporciona uma grande flexibilidade, permitindo que as organizações escolham o ambiente que melhor atenda às suas necessidades sem ficarem presas a um único provedor de nuvem.

Suporta diferentes tipos de cargas de trabalho, desde aplicações stateless até aquelas que requerem persistência de dados. Além disso, Kubernetes pode ser estendido com operadores personalizados, permitindo que você automatize a gestão de qualquer tipo de aplicação ou serviço.

A comunidade Kubernetes é uma das maiores e mais ativas no mundo open-source. Isso significa que há um grande número de contribuidores e uma vasta gama de ferramentas e extensões disponíveis para melhorar e personalizar seu ambiente Kubernetes.

É isso galera, espero que gostem!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
