---
layout: post
title: "Kubernetes 101: ReplicaSet"
author: asilva
date: 2024-07-13 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Neste artigo, vamos explorar em detalhes o conceito de **ReplicaSet** no **Kubernetes**. Enquanto os **Pods** representam a unidade básica de execução no **Kubernetes**, os **ReplicaSets** são cruciais para garantir a resiliência e a escalabilidade dos aplicativos. 

Vamos entender o que é um **ReplicaSet**, como ele funciona, e como ele é essencial para a operação de serviços confiáveis em um cluster **Kubernetes**.

## **O que é um ReplicaSet?**

Um **ReplicaSet** é um controlador que assegura que um número especificado de réplicas de um Pod estejam rodando em um determinado momento. Ele é responsável por criar e gerenciar esses Pods, garantindo que o serviço permaneça disponível mesmo em casos de falhas ou picos de demanda.

**Por que usar ReplicaSets?**

- **Disponibilidade**: Garante que um número mínimo de réplicas esteja sempre rodando.
- **Escalabilidade**: Facilita o aumento ou redução de réplicas conforme a necessidade.
- **Tolerância a falhas**: Recria Pods automaticamente em caso de falhas.

**Anatomia de um ReplicaSet**

Um ReplicaSet é definido através de um manifesto **YAML** ou **JSON**. Este manifesto inclui:

- **apiVersion**: Especifica a versão da API Kubernetes.
- **kind**: Indica o tipo de objeto (ReplicaSet).
- **metadata**: Inclui informações como o nome e rótulos do ReplicaSet.
- **spec**: Contém a especificação do ReplicaSet, incluindo o número de réplicas desejado, o seletor de Pods, e o template dos Pods.

````yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: webserver
  labels:
    app: webserver
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: webserver
  template:
    metadata:
      labels:
        tier: webserver
    spec:
      containers:
      - name: webserver
        image: nginx:1.17
        ports:
        - containerPort: 80
````

**Componentes principais**

- **Replicas**: Define quantas instâncias do Pod devem estar rodando.
- **Selector**: Critérios usados para identificar os Pods gerenciados pelo ReplicaSet.
- **Template**: Define a especificação dos Pods que serão criados, incluindo containers, imagens, volumes, etc.

## **Funcionamento do ReplicaSet**

**Monitoramento e manutenção de réplicas**

O **ReplicaSet** é responsável por garantir que o número especificado de réplicas de um Pod esteja em execução no cluster. Ele faz isso monitorando continuamente o estado atual dos Pods e comparando-o com o estado desejado definido na sua especificação. Caso seja detectada uma discrepância, o ReplicaSet realiza ações corretivas de forma automática para alinhar o estado real ao estado desejado:

- **Criação de Novos Pods**: Se o número de réplicas em execução estiver abaixo do desejado (por exemplo, devido a falhas de Pods ou aumento de demanda), o ReplicaSet cria novos Pods para preencher a lacuna e garantir que o serviço permaneça disponível e responsivo.
- **Deleção de Pods Excedentes:** Se o número de réplicas exceder o desejado (por exemplo, após uma redução manual de réplicas), o ReplicaSet deleta os Pods extras para otimizar o uso de recursos e evitar desperdício.

![](/assets/img/84/replicaset01.png){: h="25%" }

**Processo de criação e escalabilidade de Pods**

- **Criação**: Quando um ReplicaSet é inicialmente criado, ele começa a lançar os Pods conforme especificado no campo spec.replicas. O ReplicaSet coordena a distribuição desses Pods pelos nós do cluster, respeitando as restrições de recursos e afinidades especificadas.
- **Escalabilidade**: A escalabilidade é um aspecto crucial para o gerenciamento de cargas de trabalho dinâmicas. Alterações no campo `spec.replicas` do ReplicaSet permitem aumentar ou diminuir o número de Pods em resposta a mudanças na carga de trabalho ou nas necessidades de recursos. Este ajuste pode ser realizado manualmente ou através de escaladores automáticos (como o Horizontal Pod Autoscaler) que respondem a métricas de desempenho em tempo real.

![](/assets/img/84/replicaset02.png){: h="25%" }

## **Criando um ReplicaSets**

No método **declarativo**, você define o estado desejado de um recurso, como um ReplicaSet, em arquivos **YAML** ou **JSON**. Estes arquivos especificam detalhes como o número de réplicas, a imagem do container, e outras configurações relevantes. Depois de definir o estado desejado, o arquivo é aplicado ao cluster utilizando o comando `kubectl apply`. O Kubernetes então trabalha para alinhar o estado atual do cluster ao estado desejado especificado, criando ou removendo Pods conforme necessário.

Exemplo de arquivo YAML para um ReplicaSet:

````yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
````

Exemplo de comando:

````bash
kubectl apply -f replicaset.yaml
````

![](/assets/img/84/replicaset03.gif){: h="30%" }

## **Escalabilidade de réplicas**

A escalabilidade dos Pods pode ser gerenciada de forma **declarativa** ou **imperativa**. No método declarativo, você pode editar o arquivo de configuração e aplicar as mudanças, enquanto no método imperativo, você pode usar o comando `kubectl scale`.

**Método Declarativo**

Exemplo de edição declarativa:

Atualize o campo replicas no arquivo YAML e aplique:

````yaml
spec:
  replicas: 5
````

````bash
kubectl replace -f replicaset.yaml
````

![](/assets/img/84/replicaset04.gif){: h="30%" }

Outra abordagem é usar o comando `kubectl edit` para editar o recurso diretamente no cluster:

````bash
kubectl edit replicaset nginx-replicaset
````

Isso abrirá o editor padrão, permitindo a edição direta do **ReplicaSet**. No entanto, é importante notar que se você fizer mudanças diretamente no cluster usando `kubectl edit`, o arquivo de **manifesto original** não será atualizado automaticamente. 

![](/assets/img/84/replicaset05.gif){: h="30%" }

Isso pode levar a inconsistências entre o estado atual do cluster e o estado desejado descrito nos arquivos de configuração. Para manter a consistência, você deve atualizar o arquivo de manifesto e reaplicar usando `kubectl replace`:

**Método Imperativo**

Exemplo de comando:

````bash
kubectl scale replicaset nginx-replicaset --replicas=5
````

![](/assets/img/84/replicaset06.gif){: h="30%" }

## **Conclusão**

Os **ReplicaSets** desempenham um papel fundamental na arquitetura do **Kubernetes** ao garantir a alta disponibilidade e resiliência das aplicações. Ao manter um número desejado de Pods em execução, os ReplicaSets ajudam a equilibrar a carga de trabalho e a garantir que as aplicações continuem operando mesmo diante de falhas de Pod ou nó.

A compreensão dos métodos declarativos e imperativos para gerenciar ReplicaSets permite uma gestão mais flexível e eficaz, seja através de arquivos de configuração ou comandos diretos. Além disso, a capacidade de escalar replicas facilmente proporciona uma adaptabilidade essencial para lidar com variações na demanda de carga.

Entender o funcionamento dos ReplicaSets é um passo crucial para qualquer administrador de Kubernetes, preparando o terreno para o uso de recursos mais avançados, como **Deployments**, que adicionam funcionalidades de rollout e rollback para gestão de versões de aplicações.

Com isso, espero que este artigo tenha fornecido uma visão clara e detalhada sobre como os ReplicaSets operam e como você pode utilizá-los para fortalecer a resiliência e a escalabilidade de suas aplicações no Kubernetes.

É isso galera, espero que gostem!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
