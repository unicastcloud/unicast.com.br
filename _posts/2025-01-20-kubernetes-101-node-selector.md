---
layout: post
title: "Kubernetes 101: Node Selector"
author: asilva
date: 2025-01-20 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, controlar onde os pods serão agendados é essencial para garantir a distribuição correta das cargas de trabalho. Nos artigos anteriores, exploramos **Taints e Tolerations** e **Node Affinity**, que oferecem formas avançadas de influenciar o agendamento dos pods. Agora, vamos falar sobre uma abordagem mais simples: o **Node Selector**.

O **Node Selector** é a maneira mais básica de restringir onde um pod será executado, permitindo que você especifique diretamente um rótulo (label) que o nó deve possuir para receber o pod.

## **O que é Node Selector?**

O **Node Selector** funciona ao vincular pods a nós específicos com base em labels. Quando um **Node Selector** é definido em um pod, o agendador do Kubernetes só o colocará em um nó que possua a label correspondente.

Diferente do **Node Affinity** — que permite regras mais complexas e preferências — o **Node Selector** é direto e binário: ou o nó tem a label correta ou não tem.

## **Exemplo de Node Selector**

Vamos adicionar um Node Selector a um pod para garantir que ele só seja agendado em nós com a label `disktype=ssd`.

**Passo 1: Adicionar um Label ao Nó**

````bash
kubectl label nodes node1 disktype=ssd
````

Isso adiciona a label `disktype=ssd` ao nó `node1`.

**Passo 2: Criar um Deployment com Node Selector**

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - name: meu-container
    image: nginx
````

Nesse exemplo, o pod só será agendado em um nó que tenha a label `disktype=ssd`. Caso nenhum nó tenha essa label, o pod ficará em estado Pending.

## **Comparação: Node Selector vs. Node Affinity**

Enquanto o Node Selector é simples e direto, o Node Affinity oferece muito mais flexibilidade para definir como os pods são agendados. Veja o comparativo:

| Característica         | Node Selector                                          | Node Affinity                                                    |
|------------------------|--------------------------------------------------------|-----------------------------------------------------------------|
| **Propósito**          | Direcionar pods para nós específicos com base em labels | Regras avançadas para agendamento baseado em labels             |
| **Configuração**        | Simples, via `nodeSelector`                            | Flexível, via `required` ou `preferred`                         |
| **Força da Restrição** | Binário: ou o nó tem o label ou não                    | Pode ser obrigatório ou preferencial                            |
| **Uso Comum**          | Configurações simples de agendamento                    | Distribuição avançada de workloads, combinando múltiplas regras |

Quando Usar cada Um?

- **Node Selector**: Para casos simples onde você só precisa garantir que um pod rode em nós com uma label específica.
- **Node Affinity**: Quando você precisa de regras mais avançadas, como definir preferências ou combinar múltiplos critérios.

## **Conclusão**

O **Node Selector** é uma ferramenta útil para cenários simples de agendamento no Kubernetes. Se você precisa de mais controle e flexibilidade, **Node Affinity** é a solução ideal. Escolher a ferramenta correta depende do nível de complexidade necessário para o seu cluster.

Resumo dos Benefícios:

- Fácil de configurar, direto ao ponto.
- Ideal para regras simples de agendamento.
- Um primeiro passo antes de avançar para Node Affinity.

Lembre-se de sempre testar seus workloads e avaliar qual abordagem se encaixa melhor nas necessidades da sua infraestrutura.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector" target="_blank">nodeSelector</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
