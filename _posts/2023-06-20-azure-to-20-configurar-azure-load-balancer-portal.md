---
layout: post
title: "[Azure-To] #20 Configurar Azure Load Balancer [Portal]"
author: asilva
date: 2023-06-20 09:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, infraestrutura, networking, loadbalancer]
---

Fala galera! Seis tão baum?

Faz um tempinho que não escrevo um artigos sobre Azure, então vamos lá para mais uma sequencia de artigos do Azure-To!

### **Sobre Azure Load Balancer**

O Azure Load Balancer é um serviço de balanceamento de carga de rede oferecido pela Microsoft Azure. Ele ajuda a distribuir o tráfego de entrada entre várias máquinas virtuais (VMs) ou instâncias de máquinas físicas em uma mesma rede, garantindo assim que os recursos sejam utilizados de maneira eficiente e que as aplicações permaneçam disponíveis e responsivas.

Principais características do Azure Load Balancer:

- **Balanceamento de Carga**: O serviço distribui o tráfego de entrada de forma equitativa entre as VMs ou instâncias de máquinas físicas, evitando sobrecargas em um único servidor.
- **Redundância**: O Load Balancer oferece alta disponibilidade, garantindo que, se um dos balanceadores falhar, outro assumirá suas funções, garantindo a continuidade do serviço.
- **Protocolos**: Ele suporta o balanceamento de carga para diferentes protocolos, como TCP, UDP, HTTP e HTTPS.
- **Balanceamento de carga interno e externo**: O Azure Load Balancer pode ser usado tanto para distribuir o tráfego entre máquinas dentro de uma rede (balanceamento de carga interno) quanto para disponibilizar aplicações para o público externo (balanceamento de carga externo).
- **Portas personalizadas**: É possível configurar o serviço para equilibrar o tráfego em portas específicas, o que é útil para diferentes tipos de aplicações e serviços.
- **Backend Pools**: As VMs ou instâncias físicas são agrupadas em "pools" de backend, permitindo o gerenciamento conjunto do tráfego direcionado a esse grupo de recursos.
- **Health Probes**: O Azure Load Balancer realiza verificações de integridade das VMs ou instâncias físicas para garantir que apenas servidores saudáveis recebam tráfego.
- **Session Persistence**: Para algumas aplicações que requerem que uma mesma sessão seja direcionada sempre para a mesma VM, o serviço suporta "session persistence" (persistência de sessão).

### **Objetivo**

Neste artigo, vamos criar um balanceador de carga interno.

As etapas para criar um balanceador de carga interno são muito semelhantes para criar um balanceador de carga público. A principal diferença é que, com um balanceador de carga público, o front-end é acessado por meio de um endereço IP público e você testa a conectividade de um host localizado fora de sua rede virtual; considerando que, com um balanceador de carga interno, o front-end é um endereço IP privado dentro de sua rede virtual e você testa a conectividade de um host dentro da mesma rede.

O diagrama abaixo ilustra o ambiente que você implantará neste exercício.

![](/assets/img/71/lb01.png){: "width=60%" }

### **1.1 Criando a Rede Virtual**



### **2.1 Criando servidores de back-end**



### **3.1. Criando o Load Balancer**



### **4.1 Criando os recursos do Load Balancer**



### **5.1 Criar máquinas virtuais para validação e testes**



### **6.1 Testando o Load Balancer**






Parabéns! Você configurou e testou um **Load Balancer** do Azure.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
