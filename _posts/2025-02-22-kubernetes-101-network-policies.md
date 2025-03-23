---
layout: post
title: "Kubernetes 101: Network Policies"
author: asilva
date: 2025-02-22 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, o tráfego de rede entre os Pods, por padrão, é aberto — qualquer Pod pode se comunicar com outro, independentemente do namespace ou das aplicações em execução. Para controlar esse comportamento e impor regras específicas, usamos as **Network Policies**.

As **Network Policies** permitem definir como os Pods podem se comunicar entre si e com outros recursos externos, garantindo segurança e isolamento em ambientes de produção.

## **O que são Network Policies?**

Uma **NetworkPolicy** é um recurso no Kubernetes que especifica como os grupos de Pods podem se comunicar entre si e com outros endpoints. Essas regras são baseadas em três aspectos principais:

- **Pods de origem:** quem está tentando se comunicar.
- **Pods de destino:** quem vai receber a comunicação.
- **Portas e protocolos:** por onde e como essa comunicação ocorre.

As políticas são "deny-all" por padrão — ou seja, quando você cria uma Network Policy, ela começa bloqueando tudo e só permite o que for explicitamente declarado.

>**Importante**: Elas não têm efeito a menos que o cluster tenha um CNI (Container Network Interface) que suporte Network Policies, como o Calico. Se o CNI não for compatível com políticas, como no caso do Flannel, você poderá criar as regras, mas elas não terão efeito. Além disso, você não receberá erros ao aplicar as políticas, mas elas simplesmente não funcionarão.
{: .prompt-warning }

Aqui estão algumas CNI (Container Network Interfaces) relevantes para Network Policies no Kubernetes:

1. **Calico** – Uma das opções mais populares, suporta políticas avançadas de segurança e roteamento BGP.
2. **Cilium** – Baseado em eBPF, oferece alto desempenho e segurança refinada para políticas de rede.
3. **Weave Net** – Suporta Network Policies com uma configuração simples e integrada ao Kubernetes.

## **Por que usar Network Policies?**

Como mencionado anteriormente, por padrão, o Kubernetes permite todo o tráfego de rede entre Pods e de Pods para destinos externos. Isso significa que, sem Network Policies, qualquer Pod pode se comunicar livremente com outros Pods e serviços na rede.

- **Segurança**: Evita acessos não autorizados e limita a exposição de serviços.
- **Controle de tráfego**: Permite isolar serviços críticos e limitar o acesso a eles.
- **Redução de superfície de ataque**: Impede comunicação entre Pods desnecessários, reduzindo o risco de propagação de falhas ou ataques.

## **Como funciona?**

Network Policies são aplicadas no nível de tráfego de rede (não no nível de Pods ou containers).

**Estrutura básica de uma Network Policy:**

- **Pod Selector**: Define quais Pods a regra se aplica.
- **Ingress**: Controla o tráfego de entrada (de outros Pods ou fontes externas).
- **Egress**: Controla o tráfego de saída (para outros Pods ou destinos externos).
- **Policy Types**: Define se a política vai aplicar ao tráfego de Ingress ou Egress.

- A policy **permite tráfego de entrada (Ingress)** de Pods com o label app: allowed-app para Pods com o label app: my-app.
- **Comportamento Default**: Se não houver uma Network Policy definida, todos os Pods podem se comunicar livremente.

Como primeiro passo, é comum criar uma **Network Policy** que **bloqueia todo o tráfego**, como por exemplo, entre Pods e serviços críticos como um banco de dados.

Para isso, você cria uma policy chamada, por exemplo, `db-policy`, e usa **labels e selectors** para associá-la ao Pod que você quer proteger.

A política vai bloquear qualquer tráfego até que você defina explicitamente quais Pods ou fontes podem se comunicar com o Pod protegido.

**Exemplo de política para negação padrão de todo tráfego de entrada**

Você pode criar uma política de isolamento de entrada "padrão" para um namespace criando uma NetworkPolicy que seleciona todos os pods, mas não permite nenhum tráfego de entrada para esses pods.

````yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
````

## Tipos de seletores 

Há quatro tipos de seletores que podem ser especificados em uma seção ingress from ou egress to:

**podSelector**

O campo **podSelector** seleciona pods com base em seus rótulos e determina a quais pods a política se aplica.

**namespaceSelector**

O campo **namespaceSelector** permite selecionar namespaces específicos e aplicar regras de política de rede a todos os pods dentro desses namespaces.

**namespaceSelector e podSelector**

Uma única entrada to/from que especifica namespaceSelector e podSelector seleciona Pods específicos dentro de namespaces específicos. 

**ipBlock**

O ipBlock é usado para especificar blocos de endereço IP que têm permissão para acessar ou acesso negado ao pod. Ele pode ser usado para definir um bloco CIDR ou um único endereço IP.

![](/assets/img/105/netpol01.png){: "style="max-width: 50%" }

## **Exemplo de comunicação entre Frontend, API e Database**

Vamos pensar num cenário de uma aplicação com **backend**/**frontend** separados por namespaces e namespace de administração que precisa ter acesso a ambos os namespaces (frontend e backend).

- O web pod (frontend) precisa se comunicar com o api pod (backend).
- O acesso ao web pod deve ser permitido a partir de todos os namespaces."
- O administrador precisa ter acesso aos pods da web/api.

**Criar um namespace de frontend e implantar o web pod:**

````bash
$ kubectl create ns frontend
$ kubectl label namespaces frontend role=frontend
$ kubectl run web --image=nginx --labels=app=web --port 80 -n frontend
$ kubectl expose pod web --type=ClusterIP --port=80 -n frontend
````

**Criar um namespace de backend e implantar o pod de API:**

````bash
$ kubectl create ns backend
$ kubectl label namespaces backend role=backend
$ kubectl run api --image=nginx --labels=app=api --port 80 -n backend
$ kubectl expose pod api --type=ClusterIP --port=80 -n backend
````

**Criar o namespace do administrador e implantar o pod do administrador:**

````bash
$ kubectl create ns admin
$ kubectl label namespaces admin role=admin
$ kubectl run admin --image=nginx --labels=app=admin --port 80 -n admin
$ kubectl expose pod admin --type=ClusterIP --port=80 -n admin
````

**Criar um namespace de teste e implantar o pod de teste:**

````bash
$ kubectl create ns test
$ kubectl run test --image=nginx --labels=app=test --port 80 -n test
$ kubectl expose pod test --type=ClusterIP --port=80 -n test
````

Este namespace/pod será usado para testes, nenhuma política de rede será instalada neste namespace.

Valide se as conexões entre todos os pods em diferentes namespaces são permitidas.

````bash
$ kubectl exec -it web -n frontend -- curl api.backend
$ kubectl exec -it web -n frontend -- curl admin.admin
$ kubectl exec -it admin -n admin -- curl web.frontend
$ kubectl exec -it admin -n admin -- curl api.backend
$ kubectl exec -it api -n backend -- curl web.frontend
$ kubectl exec -it api -n backend -- curl admin.admin
````

## **Testes de comunicação**

Agora, vamos criar uma política de rede e aplicá-la nos namespaces frontend, backend e admin.

````yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: deny-access-all-namespaces
spec:
  podSelector:
    matchLabels:
  ingress:
  - from:
    - podSelector: {}
````

````bash
$ kubectl exec -it test -n test -- curl api.backend
$ kubectl exec -it test -n test -- curl admin.admin
$ kubectl exec -it test -n test -- curl web.frontend
````

Você deve ver a mensagem de 'Tempo limite de conexão esgotado' em vez de 'Bem-vindo'.

Agora, vamos permitir entrada no pod da **API** a partir do pod da **Web**. Criar a política de rede e implantar no namespace do **backend**.

````yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: allow-api-from-frontend
spec:
  podSelector:
    matchLabels:
      app: api
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          role: frontend
````

````bash
$ kubectl exec -it web -n frontend -- curl api.backend
````

Você deve ver a mensagem de 'Bem-vindo'. 

Agora, vamos permitir entrada no **web** pod de todos os **namespaces**. Criar a política de rede e implantar no namespace **frontend**.

````yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: allow-web-from-all-namespaces
spec:
  podSelector:
    matchLabels:
      app: web
  ingress:
  - from:
    - namespaceSelector: {}
````

````bash
$ kubectl exec -it test -n test -- curl web.frontend
````

Você deve ver a mensagem de 'Bem-vindo'. 

E para finalizar, vamos permitir entrada em pods **api**/**web** para pod **admin**. Modificar política de rede do pod **api** e implantá-lo no namespace **backend**.

````yaml
ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          role: admin
````

>Você precisa adicionar na política de rede para API, porque o web pod já permite entrada de todos os namespaces.
{: .prompt-info }

````bash
$ kubectl exec -it admin -n admin -- curl api.backend
$ kubectl exec -it admin -n admin -- curl web.frontend
````

Você deve ver a mensagem de 'Bem-vindo' em ambos. 

## **Conclusão**

As Network Policies são essenciais para fortalecer a segurança e o isolamento de aplicações dentro de um cluster Kubernetes. Ao definir regras claras para tráfego de entrada e saída, você reduz superfícies de ataque e mantém a comunicação sob controle.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/services-networking/network-policies/#networkpolicy-resource" target="_blank">Network Policies</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
