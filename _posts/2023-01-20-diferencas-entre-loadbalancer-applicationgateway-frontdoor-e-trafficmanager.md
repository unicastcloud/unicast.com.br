---
layout: post
title: "Difenrenças entre: Load Balancer, Application Gateway, Front Door e Traffic Manager"
author: asilva
date: 2023-01-20 09:00:00 -0300
categories: [Azure, Network]
tags: [azure, microsoft, infraestrutura, networking, loadbalancer, waf, gateway]
---

Fala galera! Seis tão baum?

Estou preparando uma série de artigos relacionados a configuração de Load Balancers no Azure, mas antes, entendo que seja legal fazer um overview de todas as opções que temos disponíveis.

A Microsoft oferece uma variedade de serviços de balanceamento de carga, gateway de aplicativos, front-end e gerenciamento de tráfego. Neste artigo, vamos explorar cada um desses serviços e como eles podem ser usados ​​para melhorar a disponibilidade, escalabilidade e desempenho de sua infraestrutura de nuvem.

![](/assets/img/52/lb1.png){: "width=60%" }

### **O que é um Load Balancer?**

Antes de estudar sobre Load Balancer no Azuer, é importante que você saiba o que é um Load Balancer!

Um load balancer é um **dispositivo** ou **software** que distribui o tráfego entrante entre vários servidores ou instâncias de uma aplicação. Ele é projetado para garantir alta disponibilidade, escalabilidade e desempenho para sua infraestrutura de aplicativos.

Existem vários benefícios em usar um load balancer, incluindo:

- **Disponibilidade**: Um load balancer pode distribuir o tráfego entre várias instâncias de uma aplicação, garantindo que o tráfego seja distribuído de forma equilibrada e evitando problemas de sobrecarga em uma única instância. Isso garante alta disponibilidade para sua aplicação.
- **Escalabilidade**: Um load balancer pode ajudar a garantir que sua aplicação possa escalar de acordo com a demanda, adicionando ou removendo instâncias da aplicação conforme necessário.
- **Desempenho**: Um load balancer pode ajudar a garantir que o tráfego seja distribuído de forma eficiente entre as instâncias da aplicação, melhorando o desempenho geral da aplicação.
- **Segurança**: Alguns load balancer oferecem recursos de segurança, como proteção contra ataques DDoS e suporte a certificados SSL.

Em resumo, as empresas precisam de um load balancer para garantir alta disponibilidade, escalabilidade e desempenho para sua infraestrutura de aplicativos. Ele também pode ajudar a garantir a segurança da aplicação.

### **Load Balancer no Azure**

**Azure Load Balancer,** **Azure Application Gateway**, **Azure Front Door** e **Azure Traffic Manager** são quatro opções de serviços de balanceamento de carga oferecidos pela Microsoft, cada um com seus próprios recursos e usos específicos. 

A escolha entre **Azure Load Balancer**, **Azure Application Gateway**, **Azure Front Door** ou **Azure Traffic Manager** dependerá das necessidades específicas de sua aplicação. 

E por este motivo, é muito importante que você conheça as características de cada serviço. Desta forma, você terá uma melhor compreensão e estará preparado para tomar uma decisão correta sobre qual serviço usar para sua aplicação/projeto.

Vamos explorar cada um desses serviços!

### **Azure Load Balancer**

O Azure Load Balancer é um serviço de balanceamento de carga que distribui o tráfego entrante entre várias instâncias de uma aplicação. Ele pode ser configurado para balancear a carga entre instâncias de uma mesma região ou entre regiões, garantindo que o tráfego seja distribuído de forma equilibrada e evitando problemas de sobrecarga em uma única instância. Ele também oferece recursos de alta disponibilidade, como failover automático em caso de falha de uma instância.

![](/assets/img/52/lb2.png){: "width=60%" }

#### **Algumas características do Azure Load Balancer:**

- Funciona na camada 4 (Transport Layer) do modelo OSI.
- Balanceamento de carga entre instâncias de uma mesma região ou entre regiões.
- Recursos de alta disponibilidade, como failover automático.
- Suporte a protocolos como TCP, UDP e HTTP/HTTPS.
- Ideal para aplicações que precisam garantir alta disponibilidade e escalabilidade.

#### **Cenários para utilizar o Azure Load Balancer:**

- Você tem uma aplicação web que precisa ser altamente disponível e escalável.
- Você tem várias instâncias de uma aplicação em uma região e precisa distribuir o tráfego entre elas de forma equilibrada.
- Você tem instâncias de uma aplicação em várias regiões e precisa distribuir o tráfego entre elas de forma equilibrada.

### **Azure Application Gateway**

O Azure Application Gateway é um gateway web que é usado para roteamento de tráfego baseado em URL. Ele é útil para aplicativos web que precisam de roteamento avançado, como roteamento baseado em regras de negócios ou roteamento de tráfego para diferentes aplicativos. Ele também oferece recursos de segurança, como proteção contra ataques DDoS e suporte a certificados SSL.

![](/assets/img/52/lb3.png){: "width=60%" }

#### **Algumas características do Azure Application Gateway:**

- Funciona na camada 7 (Aplication Layer) do modelo OSI.
- Roteamento de tráfego baseado em URL.
- Recursos de segurança, como proteção contra ataques DDoS e suporte a certificados SSL.
- Suporte a protocolos como HTTP e HTTPS.
- Ideal para aplicativos web que precisam de roteamento avançado.

#### **Cenários para utilizar o Azure Application Gateway:**

- Você tem uma aplicação web que precisa de roteamento avançado baseado em regras de negócios.
- Você precisa proteger sua aplicação web contra ataques DDoS.
- Você precisa suporte a certificados SSL para sua aplicação web.

### **Azure Front Door**

O Azure Front Door é um serviço de front-end que fornece roteamento global, cache e aceleração de conteúdo. Ele é projetado para aplicativos web que precisam de alta disponibilidade e desempenho global. Ele também oferece recursos de segurança, como proteção contra ataques DDoS e suporte a certificados SSL. Ele tem muitas opções de configuração e personalização, como balanceamento de carga, failover automático, criação de URL personalizadas e suporte a vários protocolos.

![](/assets/img/52/lb4.png){: "width=60%" }

#### **Algumas características do Azure Front Door:**

- Funciona na camada 7 (Aplication Layer) do modelo OSI.
- Roteamento global, cache e aceleração de conteúdo
- Recursos de segurança, como proteção contra ataques DDoS e suporte a certificados SSL
- Suporte a vários protocolos
= Ideal para aplicativos web que precisam de alta disponibilidade e desempenho global

#### **Cenários para utilizar o Azure Front Door:**

- Você tem uma aplicação web que precisa de alta disponibilidade e desempenho global.
- Você precisa de roteamento global, cache e aceleração de conteúdo.
- Você precisa personalizar URL's, protocolos, balanceamento de carga e failover automático.

### **Azure Traffic Manager**

O Azure Traffic Manager é um serviço de gerenciamento de tráfego que permite que você direcione o tráfego para diferentes instâncias de uma aplicação em diferentes regiões. Ele também oferece recursos de alta disponibilidade, como failover automático em caso de falha de uma instância. Ele é útil para aplicativos que precisam de escalabilidade global e desempenho melhorado para os usuários.

![](/assets/img/52/lb5.png){: "width=60%" }

#### **Algumas características do Azure Traffic Manager**

- Funciona na camada 7 (Aplication Layer) do modelo OSI.
- Direcionamento de tráfego para diferentes instâncias de uma aplicação em diferentes regiões.
- Recursos de alta disponibilidade, como failover automático.
- Ideal para aplicativos que precisam de escalabilidade global e desempenho melhorado para usuários em diferentes localizações geográficas.

#### **Cenários para utilizar o Azure Traffic Manager:**

- Você tem instâncias de uma aplicação em várias regiões e precisa direcionar o tráfego para as instâncias mais próximas do usuário.
- Você precisa garantir escalabilidade global e desempenho melhorado para usuários em diferentes localizações geográficas.
- Você precisa de failover automático em caso de falha de uma instância.

### **Segurança e WAF (Web Application Firewall)**

Tanto o **Azure Application Gateway** quanto o **Azure Front Door** possuem suporte a **WAF (Web Application Firewall)**.

Ambos possuem a função de WAF embutida, que permite proteger sua aplicação web contra ataques comuns de aplicação web, como SQL injection, XSS (Cross-site scripting) entre outros.

O WAF (Web Application Firewall) do Azure Application Gateway e Azure Front Door funciona monitorando o tráfego entrante e bloqueando ou permitindo o acesso baseado em regras predefinidas ou personalizadas.

O WAF é uma camada adicional, que analisa o tráfego entrante e compara-o com regras de segurança predefinidas ou personalizadas. Ele também oferece relatórios e alertas para ajudá-lo a monitorar e gerenciar suas regras de segurança.

Já o Azure Load Balancer e o Azure Traffic Manager não possuem opção de WAF embutida, eles possuem modelos de segurança diferentes:

**Azure Load Balancer:** O Azure Load Balancer oferece recursos de segurança básicos, como suporte a certificados SSL para criptografar o tráfego entre o cliente e o load balancer, e possibilidade de configurar regras de segurança para limitar o acesso a recursos específicos. Ele também oferece a opção de criar regras de firewall para restringir o tráfego entrante e saída, mas ele não possui opção de WAF (Web Application Firewall) embutida.

**Azure Traffic Manager:** é um serviço DNS baseado que é projetado para direcionar o tráfego para diferentes instâncias de uma aplicação em diferentes regiões. Ele não é projetado para lidar com segurança de forma direta, mas ele pode ser usado em conjunto com outros serviços de segurança para proteger sua aplicação.

Por exemplo, você pode usar o Azure Traffic Manager para direcionar o tráfego para instâncias de uma aplicação protegida por um Azure Load Balancer ou um Azure Application Gateway, que oferecem recursos de segurança adicionais.

### **Comparativo Técnico**

Todos serviços de Load Balancer da Microsoft são projetados para melhorar a disponibilidade, escalabilidade e desempenho de sua infraestrutura de nuvem. Cada um desses serviços tem seus próprios recursos e usos específicos, e o escolher o correto dependerá das necessidades de sua aplicação.

|                     | **Load Balancer** | **Application Gateway** | **Traffic Manager** | **Front Door**   |
|---------------------|:-----------------:|:-----------------------:|:-------------------:|:----------------:|
| **OSI Layer**       | 4                 | 7                       | 7                   | 7                |
| **Health Probes**   | TCP/HTTP          | HTTP/HTTPS              | HTTP/HTTPS/TCP      | HTTP/HTTPS       |
| **SKU's**           | Basic/Standard    | Basic/Standard          |                     |                  |
| **Load Balancing**  | Global            | Regional                | Global              | Global           |
| **Work's at**       |                   | Any IP Address          | VMs                 | DNS CNAME        |
| **TCP/UDP**         | TCP/UDP           | HTTP/HTTPS/HTTP2/WS     | DNS                 | HTTP/HTTPS/HTTP2 |
| **Sticky Sessions** | Supported         | Supported               | Supported           | Supported        |
| **Traffic Control** | NSG               | NSG                     |                     |                  |
| **WAF**             |                   | WAF                     |                     | WAF              |

### **Árvore de decisão para balanceamento de carga no Azure**

O fluxograma a seguir ajudará você a escolher uma solução de balanceamento de carga para seu aplicativo. O fluxograma orienta você por um conjunto de critérios de decisão essenciais que geram uma recomendação.

![](/assets/img/52/lb6.png){: "width=60%" }

**Fonte**: <a href="https://learn.microsoft.com/pt-br/azure/architecture/guide/technology-choices/load-balancing-overview#decision-tree-for-load-balancing-in-azure"> **Documentação Oficial**</a>.

Lembre-se que é importante avaliar as necessidades de sua aplicação e comparar os recursos e benefícios de cada serviço antes de tomar uma decisão. 

É isso galera, espero que gostem!

Forte Abraço!