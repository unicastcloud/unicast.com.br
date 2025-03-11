---
layout: post
title: "Kubernetes 101: StatefulSets"
author: asilva
date: 2024-12-17 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, os workloads podem ser gerenciados por diferentes tipos de controladores. Enquanto os Deployments são amplamente utilizados para aplicações stateless, os StatefulSets são essenciais para aplicações stateful, como bancos de dados distribuídos, sistemas de mensageria e outras aplicações que exigem identificação consistente e armazenamento persistente.

## **O que é um StatefulSet?**

**StatefulSet** é um controlador de Kubernetes projetado para gerenciar aplicações stateful. Diferente de um Deployment, que cria réplicas idênticas de um pod sem garantir ordem ou identidade, os StatefulSets garantem que cada pod tenha:

- Um nome fixo e previsível.
- Ordem de inicialização e terminação.
- Volume persistente exclusivo para armazenar dados.

Isso é fundamental para aplicações que necessitam de identidade fixa, como bancos de dados PostgreSQL, MongoDB, Cassandra, e sistemas distribuídos como Apache Kafka e Zookeeper.

## **Estrutura de um StatefulSet**

Um **StatefulSet** tem os seguintes componentes principais:

- **Pods nomeados**: Cada pod recebe um nome previsível com sufixos sequenciais, como `meu-app-0`, `meu-app-1`, etc.
- **Headless Service**: Um Service sem IP próprio (ClusterIP: None) para resolução de DNS entre os pods.
- **PersistentVolumeClaims (PVCs)**: Cada pod tem seu próprio volume persistente e não compartilha com outros pods.

**Exemplo de StatefulSet com Banco de Dados**

Vamos criar um StatefulSet para um banco de dados PostgreSQL com armazenamento persistente:

````yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: "postgres"
  replicas: 2
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        env:
        - name: POSTGRES_USER
          value: "admin"
        - name: POSTGRES_PASSWORD
          value: "secret"
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 2Gi
````

Esse exemplo cria um cluster PostgreSQL com dois pods, cada um com seu volume persistente.

## **Verificando o StatefulSet**

Após criar o StatefulSet, podemos verificar seu status com os seguintes comandos:

````bash
kubectl get statefulsets
````

Saída esperada:
````bash
NAME          READY   AGE
postgres      2/2     10m
````
Para visualizar os pods criados:

````bash
kubectl get pods -l app=postgres
````

Saída esperada:

````bash
NAME            READY   STATUS    RESTARTS   AGE
postgres-0      1/1     Running   0          10m
postgres-1      1/1     Running   0          9m
````

Verificando os volumes persistentes:

````bash
kubectl get pvc
````

Saída esperada:

````bash
NAME                STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
postgres-storage-0  Bound    pvc-xyz  2Gi        RWO            standard       10m
postgres-storage-1  Bound    pvc-abc  2Gi        RWO            standard       9m
````

## StatefulSet vs Deployment

| Característica                 | StatefulSet                            | Deployment                         |
|--------------------------------|----------------------------------------|------------------------------------|
| **Identidade dos Pods**        | Sim (fixa)                             | Não                                |
| **Ordem de inicialização**     | Garantida                              | Não                                |
| **Volume Persistente**         | Individual                             | Compartilhado/Volátil              |
| **Uso comum**                  | Bancos de dados, sistemas distribuídos | Aplicações stateless, web apps     |
| **Atualização Rolling Update** | Sim (controlada)                       | Sim (rápida)                       |

## **Quando usar StatefulSets?**

StatefulSets são recomendados para:

- Bancos de dados distribuídos (PostgreSQL, MySQL, MongoDB, Cassandra)
- Mensageria (Kafka, RabbitMQ)
- Aplicativos que necessitam de armazenamento persistente e identificadores fixos
- Aplicativos que precisam de uma ordem de inicialização controlada

Se sua aplicação não precisar dessas funcionalidades, um Deployment é uma escolha mais simples e eficiente.

## **Conclusão**

**StatefulSets** são um recurso essencial para rodar workloads **stateful** no Kubernetes, garantindo identidade fixa, volumes persistentes e ordem de inicialização. Se você estiver trabalhando com bancos de dados, sistemas distribuídos ou qualquer aplicação que necessite de consistência, um StatefulSet é a melhor escolha.

Por outro lado, se você precisa de escalabilidade rápida e gerenciamento flexível sem necessidade de persistência, os Deployments são a opção mais indicada.

Lembre-se de sempre testar seus workloads e avaliar qual abordagem se encaixa melhor nas necessidades da sua infraestrutura.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/" target="_blank">StatefulSets</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
