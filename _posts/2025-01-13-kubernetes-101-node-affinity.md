---
layout: post
title: "Kubernetes 101: Node Affinity"
author: asilva
date: 2025-01-13 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

O agendamento de pods no Kubernetes pode ser controlado de várias maneiras. No artigo anterior, exploramos **Taints e Tolerations**, que permitem restringir quais pods podem ser executados em determinados nós. Agora, vamos entender **Node Affinity**, uma funcionalidade que permite direcionar pods para nós específicos com base em labels.

**Node Affinity** é uma forma de definir regras de afinidade entre pods e nós, garantindo que certos pods sejam agendados em nós específicos de acordo com seus rótulos (labels).

## **O que é Node Affinity?**

O **Node Affinity** permite especificar regras de preferência ou exigência para agendar pods em nós que possuam determinadas características.

O **Node Affinity** pode ser definido de duas formas principais:

- **requiredDuringSchedulingIgnoredDuringExecution** (Obrigatório): O pod somente será agendado se a regra for satisfeita.
- **preferredDuringSchedulingIgnoredDuringExecution** (Preferencial): O pod tenta ser alocado de acordo com a regra, mas pode ser alocado em outros nós se necessário.

## **Exemplo de Node Affinity**

Vamos supor que temos um cluster com nós que possuem a label `disktype=ssd` e queremos garantir que nossos pods sejam executados somente nesses nós.

**Passo 1: Adicionar um Label ao Nó**

````bash
kubectl label nodes node1 disktype=ssd
````

Isso adiciona a label `disktype=ssd` ao nó `node1`.

**Passo 2: Criar um Deployment com Node Affinity**

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
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: disktype
                operator: In
                values:
                - ssd
      containers:
      - name: meu-container
        image: nginx
````

Neste exemplo, os pods somente serão agendados em nós que possuem `disktype=ssd`.

Caso queiramos apenas preferir esses nós, podemos substituir `requiredDuringSchedulingIgnoredDuringExecution` por `preferredDuringSchedulingIgnoredDuringExecution`.

## **Comparação: Node Affinity vs. Taints e Tolerations**

| **Característica**       | **Node Affinity**                                                | **Taints e Tolerations**                        |
|--------------------------|-----------------------------------------------------------------|-------------------------------------------------|
| **Propósito**            | Direcionar pods para nós específicos com base em labels          | Impedir/agendar pods em nós específicos          |
| **Configuração**          | Definido apenas nos pods                                         | Aplicado nos nós e nos pods                     |
| **Força da Restrição**   | Preferencial ou obrigatória (required ou preferred)             | NoSchedule, PreferNoSchedule, NoExecute         |
| **Uso Comum**            | Garantir que aplicações rodem em nós com certas características | Isolamento de workloads, nós dedicados          |

Quando Usar Cada Um?

- **Node Affinity**: Quando queremos garantir que um pod rode em um nó específico, mas sem impedir que outros pods sejam executados nele.
- **Taints e Tolerations:** Quando queremos evitar que determinados pods sejam agendados em um nó ou removê-los se já estiverem em execução.

## **Conclusão**

O **Node Affinity** é uma poderosa ferramenta para controlar onde os pods serão alocados no Kubernetes, garantindo melhor otimização dos recursos do cluster. Quando combinado com **Taints e Tolerations**, ele oferece um controle ainda mais refinado sobre o agendamento de workloads.

Resumo dos Benefícios:

- Melhor alocação de workloads com base em características dos nós.
- Controle granular sobre onde aplicações críticas são executadas.
- Melhor utilização de infraestrutura ao distribuir cargas corretamente.

Lembre-se de sempre testar seus workloads e avaliar qual abordagem se encaixa melhor nas necessidades da sua infraestrutura.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity" target="_blank">Affinity and anti-affinity</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
