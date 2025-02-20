---
layout: post
title: "Kubernetes 101: Taints e Tolerations"
author: asilva
date: 2025-01-07 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, o agendamento de pods nos nós do cluster pode ser controlado de diversas formas. Uma dessas formas é através do uso de **Taints** e **Tolerations**, que permitem restringir quais pods podem ser executados em determinados nós. Isso é útil para reservar nós para workloads específicos, impedir a alocação de certos pods em nós críticos e criar regras personalizadas de agendamento.

Neste artigo, vamos entender o conceito de **Taints** e **Tolerations**, como eles funcionam e como podemos usá-los na prática.

## **O que são Taints e Tolerations?**

**Taints (Restrições nos Nós)**

Um **Taint** é uma restrição aplicada a um nó, indicando que certos pods não devem ser agendados nele, a menos que tenham uma **Toleration** correspondente.

A sintaxe de um Taint é:

````bash
kubectl taint nodes <nome-do-no> <chave>=<valor>:<efeito>
````

Os efeitos possíveis são:

- **NoSchedule**: impede que novos pods sejam agendados no nó, a menos que tenham uma Toleration correspondente.
- **PreferNoSchedule**: evita a alocação de pods, mas não impede completamente.
- **NoExecute**: remove imediatamente os pods existentes que não tenham uma Toleration válida.

Exemplo de aplicação de um Taint em um nó chamado node1:

````bash
kubectl taint nodes node1 dedicated=infra:NoSchedule
````

Isso significa que apenas pods que possuam a **Toleration** correspondente poderão ser agendados nesse nó.

## **Tolerations (Permissão nos Pods)**

Uma **Toleration** permite que um pod seja executado em um nó que tenha um **Taint** correspondente.

A sintaxe para adicionar uma **Toleration** a um pod é a seguinte:

````yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "infra"
  effect: "NoSchedule"
````

Exemplo de um Deployment com Toleration:

````yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minha-aplicacao
spec:
  replicas: 2
  selector:
    matchLabels:
      app: minha-aplicacao
  template:
    metadata:
      labels:
        app: minha-aplicacao
    spec:
      tolerations:
      - key: "dedicated"
        operator: "Equal"
        value: "infra"
        effect: "NoSchedule"
      containers:
      - name: meu-container
        image: nginx
````

Com essa configuração, os pods desse Deployment poderão ser agendados em nós que possuem o Taint `dedicated=infra:NoSchedule`.

## **Exemplo Completo: usando Taints e Tolerations**

**Passo 1: Adicionar um Taint ao Nó**

Primeiro, aplicamos um **Taint** ao nó `node1`:

````bash
kubectl taint nodes node1 dedicated=infra:NoSchedule
````

**Passo 2: Criar um Pod com Toleration**

Criamos um pod que pode ser agendado no nó `node1`:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-toleration
spec:
  tolerations:
  - key: "special"
    operator: "Equal"
    value: "storage"
    effect: "NoSchedule"
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]
````

**Passo 3: Verificar o agendamento**

Para verificar onde o pod foi alocado, usamos:

````bash
kubectl get pods -o wide
````

Saída esperada:

````bash
NAME                 READY   STATUS    NODE
pod-com-toleration   1/1     Running   node1
````

Se o pod não tivesse a **Toleration**, ele não seria agendado no nó `node1`.

## **Boas Práticas no uso de Taints e Tolerations**

Aqui estão algumas boas práticas ao utilizar **Taints** e **Tolerations**:

- **Evite Taints excessivos:** Usar muitos Taints pode tornar o agendamento de pods mais complexo do que o necessário.
- **Combine com Node Labels**: Usar Taints junto com Node Labels e Node Selectors pode fornecer um controle mais refinado sobre onde os pods rodam.
- **Atenção ao NoExecute:** O efeito`NoExecute` pode remover pods existentes, então use-o com cuidado para evitar interrupções inesperadas.

## **Conclusão**

O uso de **Taints** e **Tolerations** é essencial para o controle refinado do agendamento de workloads no Kubernetes. Ele permite:

- Isolamento de workloads sensíveis, garantindo que apenas determinados pods possam rodar em nós específicos.
- Melhoria na organização do cluster, evitando sobrecarga em certos nós.
- Gerenciamento eficiente de infraestrutura, separando workloads de diferentes categorias (exemplo: aplicações críticas vs. testes).

Ao utilizar **Taints** e **Tolerations**, é possível criar ambientes mais seguros e organizados, garantindo um melhor aproveitamento dos recursos do cluster.

Lembre-se de sempre testar seus workloads e avaliar qual abordagem se encaixa melhor nas necessidades da sua infraestrutura.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/" target="_blank">Taints and Tolerations</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
