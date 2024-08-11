---
layout: post
title: "Kubernetes 101: Deployment"
author: asilva
date: 2024-07-17 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Chegamos a mais um capítulo da nossa série "**Kubernetes 101"**. Depois de explorar os **Pods** e **ReplicaSets**, vamos mergulhar no conceito de **Deployments**, um componente fundamental para gerenciar e escalar aplicações de forma eficiente no Kubernetes.

## **O que é um Deployment?**

Um **Deployment** é um recurso essencial no Kubernetes que permite gerenciar a implantação e o ciclo de vida das aplicações de maneira declarativa. Ele define o estado desejado de uma aplicação e garante que o Kubernetes ajuste o estado atual para corresponder ao estado desejado, mesmo em face de falhas ou mudanças. Ao especificar um Deployment, você pode controlar a criação, atualização e exclusão de Pods de forma automatizada e consistente.

![](/assets/img/85/deployment01.png){: h="25%" }

O **Deployment** gerencia **ReplicaSets**, que são responsáveis por manter o número correto de réplicas de Pods em execução. Dessa forma, o Deployment facilita a gestão de aplicações, proporcionando recursos como atualizações contínuas (**rollouts**) e reversões (**rollbacks**) automáticas, garantindo alta disponibilidade e resiliência da sua aplicação.

## **Funcionamento de um Deployment**

**Gerenciamento de ReplicaSets**

Deployments são responsáveis por criar e gerenciar ReplicaSets, que asseguram que o número desejado de Pods esteja sempre em execução. Cada vez que você atualiza um Deployment, o Kubernetes cria um novo ReplicaSet e escala o antigo para baixo, garantindo uma transição suave e sem interrupções.

![](/assets/img/85/deployment02.png){: h="25%" }

**Exemplo de gerenciamento de Deployment, ReplicaSet e Pods**

Vamos criar um Deployment e observar a criação dos ReplicaSets e Pods associados.

**Criação do Deployment**

Arquivo YAML para criar um Deployment:

````yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: exemplo-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: exemplo-app
  template:
    metadata:
      labels:
        app: exemplo-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.17
        ports:
        - containerPort: 80
````

Comando para aplicar o Deployment:

````shell
kubectl apply -f exemplo-deployment.yaml
````

Exibição dos recursos criados"

Vamos verificar os recursos criados pelo Deployment:

````shell
kubectl get deployments
````

Output esperado:

````shell
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
exemplo-deployment  3/3     3            3           1m
````

````shell
kubectl get replicasets
````

Output esperado:

````shell
NAME                           DESIRED   CURRENT   READY   AGE
exemplo-deployment-abc123      3         3         3       1m
````

````shell
kubectl get pods
````

Output esperado:

````shell
NAME                                 READY   STATUS    RESTARTS   AGE
exemplo-deployment-abc123-1a2b3c     1/1     Running   0          1m
exemplo-deployment-abc123-4d5e6f     1/1     Running   0          1m
exemplo-deployment-abc123-7g8h9i     1/1     Running   0          1m
````

Relação entre Deployment, ReplicaSet e Pods

- **Deployment**: `exemplo-deployment`
- **ReplicaSet**: `abc123`
- **Pods**: `1a2b3c`

Ficando assim a representação dos objetos: **exemplo-deployment-abc123-1a2b3c**

![](/assets/img/85/deployment04.gif){: h="25%" }

## **Atualizações e Rollouts**

Deployments suportam atualizações contínuas, permitindo que você faça mudanças na aplicação de forma gradual. Isso é particularmente útil para evitar downtime e garantir que a aplicação funcione corretamente antes que todas as instâncias sejam atualizadas.

![](/assets/img/85/deployment03.png){: h="25%" }

**Como Funciona o Rollout**

Quando você atualiza um Deployment, o Kubernetes cria um novo ReplicaSet para a nova versão dos Pods. O processo de rollout substitui gradualmente os Pods do antigo ReplicaSet pelos novos Pods, conforme especificado na atualização. Isso garante que a aplicação continue a funcionar durante o processo de atualização.

Comando para atualizar um Deployment:

````shell
kubectl set image deployment/exemplo-deployment nginx=nginx:1.18
````

Visualizando o status do rollout:

````shell
kubectl rollout status deployment/exemplo-deployment
````

Output esperado:

````shell
deployment "exemplo-deployment" successfully rolled out
````

![](/assets/img/85/deployment05.gif){: h="25%" }

## **Rollback**

Em caso de problemas durante uma atualização, os Deployments permitem reverter para uma versão anterior. Essa capacidade de rollback é crucial para manter a estabilidade dos serviços e minimizar o impacto de erros.

**Como realizar o Rollback**

O Kubernetes mantém um histórico de revisões de um Deployment, o que facilita a reversão para uma versão anterior se necessário.

Comando para reverter um Deployment para a versão anterior:

````shell
kubectl rollout undo deployment/exemplo-deployment
````

Verificando a revisão do Deployment:

````shell
kubectl rollout history deployment/exemplo-deployment
````

Output esperado:

````shell
deployments "exemplo-deployment"
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
````

Se precisar reverter para uma revisão específica:

````shell
kubectl rollout undo deployment/exemplo-deployment --to-revision=1
````

![](/assets/img/85/deployment06.gif){: h="25%" }

**Comandos Adicionais: Pause, Resume e Restart**

Além de rollback, o Kubernetes oferece comandos para pausar, resumir e reiniciar rollouts, proporcionando maior controle sobre o processo de atualização.

**Pause**

Pausar um rollout permite interromper temporariamente o processo de atualização. Isso pode ser útil se você precisar investigar um problema antes de prosseguir com a atualização.

Comando para pausar um rollout:

````shell
kubectl rollout pause deployment/exemplo-deployment
````

**Resume**

Resumir um rollout continua o processo de atualização após ele ter sido pausado.

Comando para retomar um rollout pausado:

````shell
kubectl rollout resume deployment/exemplo-deployment
````

**Restart**

Reiniciar um rollout força todos os Pods a serem recriados, útil para aplicar novas configurações ou resolver problemas persistentes.

Comando para reiniciar um rollout:

````shell
kubectl rollout restart deployment/exemplo-deployment
````

## **Conclusão**

Os Deployments são uma ferramenta poderosa no Kubernetes, proporcionando controle granular sobre o ciclo de vida das aplicações. Eles complementam os ReplicaSets, oferecendo funcionalidades adicionais como rollouts, rollbacks, pausas, retomadas e reinicializações, essenciais para ambientes de produção. Essa flexibilidade garante atualizações contínuas e seguras, minimizando downtime e mantendo a estabilidade dos serviços.

Além de ler este artigo, encorajo fortemente que você consulte a documentação oficial do Kubernetes para obter informações mais detalhadas e atualizadas. A documentação oficial é uma excelente fonte de conhecimento e irá complementar o que foi abordado aqui.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/workloads/controllers/deployment/" target="_blank">Deployment</a>

Nos próximos artigos, exploraremos mais a fundo as práticas recomendadas para Deployments e outros recursos avançados do Kubernetes. Vamos abordar como otimizar a utilização dos Deployments, estratégias de atualização, e como solucionar problemas comuns.

Fique ligado!

É isso galera, espero que gostem!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
