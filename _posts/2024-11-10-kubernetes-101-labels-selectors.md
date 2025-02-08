---
layout: post
title: "Kubernetes 101: Label e Selectors"
author: asilva
date: 2024-12-10 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Hoje vamos falar sobre dois conceitos que são verdadeiras colunas vertebrais na organização e gerenciamento de objetos dentro do Kubernetes: **Labels e Selectors**. Se você já tentou filtrar ou organizar seus Pods, Services ou ConfigMaps e ficou confuso, este artigo vai desmistificar como essas ferramentas funcionam e como você pode utilizá-las para simplificar a administração do seu cluster.

## **O que são Labels?**

**Labels** são pares `chave-valor` atribuídos a objetos do Kubernetes como Pods, Services, ConfigMaps, e muitos outros. Eles são usados para categorizar e organizar os objetos no cluster de forma flexível, sem depender de uma hierarquia fixa.

Exemplos de Labels

Imagine que você tem um cluster com várias aplicações e ambientes. Você pode usar Labels para identificar esses objetos:

````yaml 
labels:
  app: minha-aplicacao
  tier: frontend
  environment: production
````

- **app**: Identifica a aplicação. Nesse caso, “minha-aplicacao”.
- **tier**: Especifica o papel do componente, como “frontend” ou “backend”.
- **environment**: Define o ambiente, como “production”, “staging” ou “dev”.

Esses Labels permitem que você filtre, organize e associe recursos de forma mais intuitiva.

## **O que são Selectors?**

**Selectors** são utilizados para filtrar e selecionar objetos com base em seus Labels. Eles são amplamente utilizados por recursos como Services, ReplicaSets, Deployments e outros para identificar os Pods ou objetos que devem gerenciar.

Existem dois tipos principais de Selectors:

- Equality-Based Selectors (Seletores baseados em igualdade).
- Set-Based Selectors (Seletores baseados em conjuntos).

**Equality-Based Selectors**

Os **Equality-Based Selectors** permitem filtrar objetos que possuem Labels iguais ou diferentes de um valor especificado. Exemplos:

- Igual (==):

````yaml 
selector:
  matchLabels:
    app: minha-aplicacao
````

Esse seletor seleciona todos os objetos que têm o Label `app: minha-aplicacao`.

- Diferente (!=):

````yaml 
selector:
  matchExpressions:
  - key: environment
    operator: NotIn
    values:
    - dev
````

Seleciona todos os objetos que não têm o Label `environment: dev`.

**Set-Based Selectors**

Os **Set-Based Selectors** permitem filtrar objetos com base em uma lista de valores. Eles suportam os seguintes operadores:

- **In**: Seleciona objetos com Labels correspondentes a um valor na lista.
- **NotIn**: Seleciona objetos com Labels não correspondentes a nenhum valor na lista.
- **Exists**: Seleciona objetos que possuem um determinado Label, independentemente do valor.
- **DoesNotExist**: Seleciona objetos que não possuem um determinado Label.

````yaml 
selector:
  matchExpressions:
  - key: app
    operator: In
    values:
    - minha-aplicacao
    - outra-aplicacao
````

Esse seletor retorna todos os objetos que têm o Label `app` com valores **“minha-aplicacao”** ou **“outra-aplicacao”**.

## **Exemplo prático: Service com Selectors**

Vamos ver como um Service utiliza Selectors para direcionar o tráfego para os Pods corretos.

**Pods com Labels**
Primeiro, criamos alguns Pods com Labels:

````yaml 
apiVersion: v1
kind: Pod
metadata:
  name: pod-frontend
  labels:
    app: minha-aplicacao
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-backend
  labels:
    app: minha-aplicacao
    tier: backend
spec:
  containers:
  - name: nginx
    image: nginx
````

Service com Selectors

Agora, criamos um Service que seleciona apenas os Pods do tipo “frontend”:

````yaml 
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: minha-aplicacao
    tier: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
````

- O selector filtra apenas os Pods com `app: minha-aplicacao` e `tier: frontend`.
- O tráfego enviado para o Service será redirecionado para esses Pods.

## **Boas práticas ao usar Labels e Selectors**

1. Use um padrão consistente para Labels: Certifique-se de que todos os Labels sigam uma convenção clara e compreensível pela equipe.
  - Exemplo: **app**, **tier**, **environment**, **release**.
2. Evite duplicidade: Utilize Labels únicos para evitar ambiguidades e conflitos nos Selectors.
3. Combine Labels e Namespaces: Labels funcionam melhor quando combinados com Namespaces para segregar aplicações em um cluster multi-tenant.
4. Teste seus Selectors: Antes de configurar recursos como Services e Deployments, use comandos como `kubectl get pods -l "key=value"` para verificar se seus Selectors estão filtrando os objetos desejados.

## **Verificando Labels no Kubernetes**

Você pode listar os Labels atribuídos aos objetos com o comando:

````shell
kubectl get pods --show-labels
````

Para filtrar os Pods com base em Labels:

````shell
kubectl get pods -l app=minha-aplicacao
````

Para adicionar ou atualizar Labels em um objeto existente:

````shell
kubectl label pod pod-frontend environment=staging
````

Para remover um Label:

````shell
kubectl label pod pod-frontend environment-
````

## **Conclusão**

**Labels** e **Selectors** são ferramentas poderosas que permitem organizar e gerenciar recursos no Kubernetes de forma eficiente. Ao combiná-los com Namespaces, você pode criar um ambiente bem estruturado e fácil de administrar.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/" target="_blank">Labels and Selectors</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
