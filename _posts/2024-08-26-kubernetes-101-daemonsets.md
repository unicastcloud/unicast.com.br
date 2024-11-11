---
layout: post
title: "Kubernetes 101: DaemonSets"
author: asilva
date: 2024-08-26 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Bora pra mais um capítulo da nossa série **“Kubernetes 101”**! Hoje vamos falar sobre um recurso essencial para administrar containers que devem estar em todos os nós do cluster: o **DaemonSet**.

O **DaemonSet** é uma maneira prática de garantir que certas aplicações ou serviços essenciais sejam executados em cada nó do cluster. Isso é particularmente útil para tarefas de **monitoramento**, **registro (logging)**, e outras funções de infraestrutura que precisam cobrir todos os nós.

## **O que é um DaemonSet?**

Um **DaemonSet** é um tipo de controlador no Kubernetes que assegura que uma cópia específica de um Pod seja executada em cada nó do cluster. Diferente de um Deployment, que escala aplicações entre nós, o DaemonSet cria um Pod em cada nó assim que ele entra no cluster.

![](/assets/img/92/daemonsets01.png){: h="25%" }

**Casos de Uso para DaemonSets**

Os DaemonSets são ideais para tarefas como:

- **Monitoramento**: Ferramentas como Prometheus Node Exporter e Fluentd são frequentemente implantadas como DaemonSets para coletar métricas de desempenho e logs de cada nó.
- **Gerenciamento de rede**: Controladores de rede, como o CNI, podem ser executados como DaemonSets para manter a configuração de rede em cada nó.
- **Segurança**: Agentes de segurança (como o Falco) monitoram atividades suspeitas e são executados em cada nó para garantir cobertura total.

## **Como criar um DaemonSet**

Você pode criar um DaemonSet usando arquivos YAML de maneira declarativa. Vamos ver como isso funciona com um exemplo básico.

**Exemplo Declarativo**

Aqui está um exemplo simples de YAML para um DaemonSet que executa o nginx em cada nó do cluster:

````yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: meu-daemonset
spec:
  selector:
    matchLabels:
      app: meu-daemon
  template:
    metadata:
      labels:
        app: meu-daemon
    spec:
      containers:
      - name: meu-container
        image: nginx
````

Esse DaemonSet criará um Pod com o container `nginx` em cada nó do cluster.

````bash
kubectl apply -f meu-daemonset.yaml
````

## **Gerenciamento de DaemonSets**

Ao trabalhar com **DaemonSets**, é importante entender como gerenciar o ciclo de vida desses Pods em relação aos nós no cluster.

**Atualizando um DaemonSet**

Quando um DaemonSet é atualizado, o Kubernetes reinicia automaticamente os Pods em cada nó para aplicar a nova configuração. Contudo, vale lembrar que as atualizações não são tão rápidas quanto em Deployments, pois o objetivo principal é manter cada nó com uma cópia única e atualizada do Pod.

## **DaemonSets vs. Deployments**

É comum haver dúvidas sobre quando usar um DaemonSet em vez de um Deployment. Aqui estão algumas diferenças principais:

| DaemonSet                                 | Deployment                              |
|-------------------------------------------|-----------------------------------------|
| Garante 1 Pod por nó                      | Distribui réplicas de Pods entre nós    |
| Uso ideal para monitoramento e segurança  | Uso ideal para aplicações web ou APIs   |
| Adiciona Pods automaticamente em novos nós | Escalabilidade configurável pelo usuário |

## **Testando e monitorando um DaemonSet**

Depois de implantar um **DaemonSet**, é fundamental garantir que ele esteja funcionando conforme esperado.

**Comando para verificar o status**

Para verificar o status de um DaemonSet e visualizar em quais nós ele foi implantado, o número de Pods em execução e possíveis erros, você pode usar o comando:

````bash
kubectl get daemonset meu-daemonset -o wide
````

**Exemplo de output**

````bash
NAME             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
meu-daemonset    3         3         3       3            3           <none>          5m
````

Para obter mais detalhes específicos sobre os Pods e os nós em que estão executando, você pode listar os Pods relacionados ao DaemonSet:

````bash
kubectl get pods -l app=meu-daemonset -o wide
````

**Exemplo de output detalhado**

````bash
NAME                   READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
meu-daemonset-abc123   1/1     Running   0          5m    192.168.1.1  node-1         <none>           <none>
meu-daemonset-def456   1/1     Running   0          5m    192.168.1.2  node-2         <none>           <none>
meu-daemonset-ghi789   1/1     Running   0          5m    192.168.1.3  node-3         <none>           <none>
````

**Explicação dos campos**

- **DESIRED**: Quantos Pods o DaemonSet deveria estar executando (um por nó).
- **CURRENT**: Quantos Pods foram realmente criados.
- **READY**: Quantos Pods estão prontos e funcionando corretamente.
- **NODE**: O nome do nó onde o Pod do DaemonSet está em execução.
- **STATUS**: O estado atual do Pod (por exemplo, Running, CrashLoopBackOff se houver problemas).

## **Conclusão**

Os **DaemonSets** são uma ferramenta poderosa e versátil no Kubernetes para gerenciar serviços essenciais que precisam estar em cada nó. Com DaemonSets, você pode manter um controle confiável de agentes de monitoramento, logging, segurança e muito mais. 

E, como sempre, não se esqueça de consultar a documentação oficial do Kubernetes para mais detalhes e práticas recomendadas sobre o uso de **DaemonSets**!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/" target="_blank">DaemonSets</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
