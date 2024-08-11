---
layout: post
title: "Kubernetes 101: Namespaces"
author: asilva
date: 2024-07-30 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Chegamos a mais um capítulo da nossa série **“Kubernetes 101”**. Depois de explorar **Pods**, **ReplicaSets**, e **Deployments**, agora vamos mergulhar em um conceito fundamental para a organização e gerenciamento de recursos dentro de um cluster Kubernetes: **Namespaces**.

## **O que é um Namespace?**

Um **Namespace** é um recurso no Kubernetes que permite segmentar e isolar recursos dentro de um cluster. Imagine que seu cluster Kubernetes é um grande escritório. Os Namespaces seriam como departamentos dentro desse escritório, onde cada um tem seu próprio conjunto de recursos e políticas. Isso possibilita que diferentes equipes ou projetos coexistam no mesmo cluster sem interferir uns nos outros.

![](/assets/img/88/namespaces01.png){: h="25%" }

## **Benefícios dos Namespaces**

**Namespaces** trazem uma série de vantagens para o gerenciamento de recursos no Kubernetes, incluindo:

- **Isolamento**: Garantem que os recursos de uma aplicação ou equipe não interfiram nos recursos de outra.
- **Facilidade de Gerenciamento:** Permitem a organização lógica de recursos, facilitando a aplicação de políticas de segurança, quotas de recursos e acesso controlado.
- **Escalabilidade**: Ao organizar recursos em Namespaces, você pode gerenciar grandes clusters com múltiplos projetos de maneira mais eficiente.

## **Funcionamento dos Namespaces**

Os **Namespaces** permitem que você divida **logicamente** os recursos dentro do Kubernetes. Cada Namespace possui seu próprio conjunto de **Pods**, **Services**, e outros objetos, o que facilita o gerenciamento e a organização. Um Namespace é ideal para cenários onde você deseja segmentar ambientes como **desenvolvimento**, **teste** e **produção** dentro de um mesmo cluster.

Por padrão, o Kubernetes vem com alguns Namespaces predefinidos, como:

- **default**: O Namespace padrão para recursos que não têm um Namespace específico.
- **kube-system**: Utilizado para recursos criados pelo próprio Kubernetes.
- **kube-public**: Um Namespace especial que é acessível publicamente.
- **kube-node-lease**: Usado para gerenciar o ciclo de vida dos nodes.

````shell
NAME                 STATUS   AGE
default              Active   209d
kube-flannel          Active   209d
kube-node-lease      Active   209d
kube-public          Active   209d
kube-system          Active   209d
local-path-storage   Active   7d23h
````

![](/assets/img/88/namespaces02.gif){: h="25%" }

> Ao utilizar comandos como `kubectl get` sem especificar um namespace, o Kubernetes assume que o comando deve ser executado no namespace default. Isso significa que se você não indicar um namespace, o comando irá buscar informações no namespace padrão (**default**).
{: .prompt-info }

Você pode alterar o namespace padrão da sua configuração de contexto atual com o seguinte comando:

````shell
kubectl config set-context --current --namespace=<novo-namespace>
````

sso ajusta o namespace padrão para a sua sessão atual, e todos os comandos subsequentes que não especificarem um namespace usarão o novo namespace configurado.

## **Criando e gerenciando Namespaces**

Para criar e gerenciar **Namespaces** no Kubernetes, você pode utilizar tanto o método **declarativo** quanto o **imperativo**, dependendo das necessidades da sua operação.

**Exemplo de arquivo YAML para criar um Namespace**

Este método envolve a definição do estado desejado para o Namespace através de um arquivo YAML. Após a criação do arquivo, o Kubernetes aplica as configurações necessárias para que o estado real do cluster corresponda ao estado desejado.

````yaml
apiVersion: v1
kind: Namespace
metadata:
  name: exemplo-namespace
````

Comando para aplicar o arquivo e criar o Namespace:

````shell
kubectl apply -f exemplo-namespace.yaml
````

![](/assets/img/88/namespaces03.gif){: h="25%" }

**Exemplo de para criar um Namespace de forma imperativa**

O método imperativo é mais direto, permitindo a criação de Namespaces por meio de comandos no terminal. Este método é útil para operações rápidas ou testes, mas pode resultar em inconsistências se não for acompanhado da documentação adequada.

Comando para criar um Namespace de forma imperativa:

````shell
kubectl create namespace exemplo-namespace-2
````

Após a criação, você pode visualizar os Namespaces existentes com:

````shell
kubectl get namespaces
````

Após a criação, você pode visualizar os Namespaces existentes com:

````shell
NAME                 STATUS   AGE
default              Active   209d
kube-flannel          Active   209d
kube-node-lease      Active   209d
kube-public          Active   209d
kube-system          Active   209d
local-path-storage   Active   7d23h
exemplo-namespace    Active   1m
````

![](/assets/img/88/namespaces04.gif){: h="25%" }

## Comunicação entre Namespaces

Uma dúvida comum é sobre a comunicação entre Pods que residem em diferentes Namespaces. Por padrão, a comunicação entre Namespaces no Kubernetes é permitida. Isso significa que um Pod em um Namespace pode se comunicar com um Pod em outro Namespace, desde que saiba o endereço IP ou o nome do serviço do Pod de destino.

No entanto, é possível implementar regras de **Network Policies** para restringir essa comunicação, garantindo maior segurança e isolamento entre os diferentes ambientes.

Vamos configurar um exemplo básico para testar a comunicação entre dois Pods em diferentes Namespaces usando a imagem do busybox. Este exemplo será mais direto e focado apenas na comunicação entre Pods via IP.

````yaml
apiVersion: v1
kind: Namespace
metadata:
  name: namespace-a

---
apiVersion: v1
kind: Namespace
metadata:
  name: namespace-b

---
apiVersion: v1
kind: Pod
metadata:
  name: pod-a
  namespace: namespace-a
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]  # Mantém o Pod em execução

---
apiVersion: v1
kind: Pod
metadata:
  name: pod-b
  namespace: namespace-b
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]  # Mantém o Pod em execução
````

Passos para testar a comunicação:

Aplicar o Arquivo YAML:

Aplique o arquivo YAML acima.

````shell
kubectl apply -f namespaces-communication.yaml
````

Obtenha os IPs dos Pods:

````shell
kubectl get pods -n namespace-a -o wide 
kubectl get pods -n namespace-b -o wide 
````

Verificar a comunicação entre Pods:

Agora, você pode verificar se o **pod-a** em `namespace-a` consegue se comunicar com o **pod-b** em `namespace-b`. Para isso, vamos executar um comando de **ping** no `pod-a` para o `pod-b`:

````shell
kubectl exec -n namespace-a pod-a -- ping <IP-do-Pod-em-namespace-b> -c 4
````

![](/assets/img/88/namespaces05.gif){: h="25%" }

## Gerenciamento de recursos com Namespaces

Namespaces oferecem uma maneira eficiente de gerenciar e controlar os recursos dentro de um cluster Kubernetes. Com eles, você pode definir quotas de recursos, como CPU e memória, para garantir que cada Namespace utilize apenas a quantidade de recursos que lhe foi alocada.

Além disso, é possível limitar o número de objetos que um Namespace pode criar, como Pods ou Services. Essa abordagem garante que nenhum ambiente ou projeto ultrapasse seus limites de recursos, contribuindo para a estabilidade e a performance do cluster como um todo.

## **Conclusão**

**Namespaces** são uma ferramenta crucial para a organização e o isolamento de recursos no Kubernetes. Eles permitem que você compartilhe um cluster entre diferentes projetos ou equipes, garantindo que os recursos sejam bem segmentados e controlados. Contudo, é essencial estar atento à comunicação entre Namespaces, já que ela pode impactar a segurança e o desempenho da sua aplicação, especialmente em ambientes de produção.

Além de ler este artigo, encorajo fortemente que você consulte a documentação oficial do Kubernetes para obter informações mais detalhadas e atualizadas. A documentação oficial é uma excelente fonte de conhecimento e irá complementar o que foi abordado aqui.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/" target="_blank">Namespaces</a>

No próximo artigo, vamos explorar os **Services** em Kubernetes, um conceito fundamental para gerenciar a comunicação entre Pods e permitir o acesso às suas aplicações. Fique ligado!

É isso, galera! Espero que tenham gostado!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
