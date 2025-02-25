---
layout: post
title: "Kubernetes 101: Resource Limits"
author: asilva
date: 2025-01-29 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, a gestão eficiente dos recursos (**CPU** e **memória**) é fundamental para garantir a estabilidade do cluster e evitar que um pod consuma mais do que deveria, afetando outras aplicações. Para isso, utilizamos **Resource Requests** e **Resource Limits**.

Neste artigo, vamos explorar como funcionam os limites de recursos, como configurá-los e monitorá-los, e a importância de definir corretamente esses valores para manter um ambiente Kubernetes saudável.

## **O que são Resource Requests e Limits?**

No Kubernetes, ao definir recursos para um pod, você pode especificar dois tipos de valores:

- **Requests**: a quantidade mínima de CPU e memória garantida para o pod. O agendador usa esses valores para decidir em qual nó colocar o pod.
- **Limits**: o limite máximo de CPU e memória que o pod pode consumir. Se um pod tentar exceder esses valores, o Kubernetes pode restringir ou até encerrar o pod.

Sintaxe básica:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
spec:
  containers:
  - name: meu-container
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
````

- CPU é medido em milicores (1000m = 1 core).
- Memória é medida em bytes (Mi = Mebibytes).

## **Como funciona na prática**

1. **Criando um pod com Resource Limits**

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-limited-pod
spec:
  containers:
  - name: stress-container
    image: polinux/stress
    command: ["stress"]
    args: ["--cpu", "1", "--vm", "1", "--vm-bytes", "100M", "--vm-hang", "1"]
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
````

Aplicar o pod:

````bash
kubectl apply -f resource-limited-pod.yaml
````

2. **Checando o consumo de recursos**

Você pode monitorar o uso de CPU e memória dos pods com o comando:

````bash
kubectl top pod
````

Exemplo de saída:

````bash
NAME                       CPU(cores)   MEMORY(bytes)
resource-limited-pod       300m         110Mi
````

Se os limites forem ultrapassados, você verá o pod sendo encerrado ou ficando no estado OOMKilled.

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: stress-test
spec:
  containers:
  - name: stress-container
    image: polinux/stress
    command: ["stress"]
    args: ["--cpu", "2", "--vm", "1", "--vm-bytes", "200M", "--vm-hang", "1"]
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
````

Aplicar o pod:

````bash
kubectl apply -f stress-test.yaml
````

Verificando o status do pod:

````bash
kubectl describe pod stress-test
````

Saída esperada ao ultrapassar o limite:

````bash
State:          Terminated
Reason:         OOMKilled
````

## **QoS Classes (Classes de Qualidade de Serviço)**

O Kubernetes classifica os pods em diferentes classes de **QoS**, dependendo de como você configura requests e limits:

- **Guaranteed**: quando requests e limits são iguais para todos os recursos.
- **Burstable**: quando requests são definidos, mas os limits são maiores.
- **BestEffort**: quando nenhum request ou limit é definido.

Exemplo:

````bash
kubectl get pod resource-limited-pod -o custom-columns="NAME:.metadata.name,QOS:.status.qosClass" 
````

Saída esperada 

````bash
NAME                   QOS
resource-limited-pod   Burstable
````

## **Conclusão**

Configurar Resource Limits corretamente é essencial para manter a estabilidade e a performance dos seus clusters Kubernetes. Requests e limits ajudam a:

- Garantir que cada pod tenha os recursos mínimos necessários.
- Proteger o cluster contra pods que consomem recursos excessivos.
- Classificar pods em QoS, permitindo maior previsibilidade no agendamento.

Lembre-se de sempre testar seus workloads e avaliar qual abordagem se encaixa melhor nas necessidades da sua infraestrutura.

>Não defina valores arbitrários! Monitore o uso real dos pods com kubectl top pod e ajuste requests e limits conforme necessário.
{: .prompt-warning }

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/" target="_blank">Resource Management for Pods and Containers</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
