---
layout: post
title: "Kubernetes 101: Services"
author: asilva
date: 2024-08-04 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No capítulo de hoje da nossa série **"Kubernetes 101"**, vamos explorar um dos conceitos mais essenciais no Kubernetes: os **Services**. Se você já leu sobre **Pods** e **Deployments**, agora é hora de entender como esses componentes se comunicam entre si e com o mundo externo. Vamos mergulhar nos detalhes de como os Services funcionam, suas variações, como escolher o tipo certo de Service, e como testá-los na prática.

## **O que é um Service?**

Um **Service** em **Kubernetes** é um recurso que abstrai a comunicação com um conjunto de Pods, permitindo que aplicações e outros recursos se comuniquem com seus Pods de maneira estável, independentemente de como eles são agendados ou escalados no cluster. Ele garante que o tráfego seja direcionado para os Pods corretos, mesmo que a infraestrutura subjacente mude.

![](/assets/img/89/service01.png){: h="25%" }

## **Service Discovery**

Quando você executa múltiplas instâncias de um serviço em um ambiente como o **Kubernetes**, surge a necessidade de **Service Discovery**. Esse conceito é fundamental para garantir que os serviços possam encontrar e se comunicar entre si, mesmo com a natureza dinâmica e efêmera dos ambientes modernos.

**Service Discovery** refere-se ao processo de identificar a localização e a comunicação com instâncias de serviços dentro de um ambiente distribuído. Em termos simples, é como um serviço encontra outros serviços e se conecta a eles. No **Kubernetes**, isso é facilitado pelo uso de **DNS** interno e o sistema de **Services**.

Tradicionalmente, antes da popularização dos containers, cada instância de um serviço era associada a um endereço IP fixo. No entanto, com a ascensão dos containers e a natureza dinâmica dos ambientes modernos, onde instâncias podem ser criadas e destruídas frequentemente, isso se tornou um desafio. O **Kubernetes** resolve esse problema utilizando seu sistema de **descoberta de serviços**, que permite que serviços internos se localizem e comuniquem uns com os outros sem precisar de configurações complexas.

O **Kubernetes** usa o **DNS** **interno** do cluster para resolver os endereços dos serviços. Quando você cria um Service, o Kubernetes configura automaticamente um registro DNS para ele. Isso permite que os Pods se comuniquem uns com os outros através de nomes de serviço em vez de endereços IP diretos. Isso facilita a comunicação, mesmo quando as instâncias do serviço estão mudando ou sendo escaladas.

![](/assets/img/89/service02.png){: h="25%" }

**Por que é importante?**

O **Service Discovery** é crucial para a construção de sistemas **escaláveis** e **resilientes**. Em um ambiente **Kubernetes**, onde serviços podem ser frequentemente adicionados, removidos ou atualizados, a capacidade de descobrir e se conectar a serviços de maneira eficiente é essencial para a operação contínua dos aplicativos.

Para uma exploração mais detalhada de como o Kubernetes lida com **Service Discovery** e as melhores práticas para configurar e gerenciar esses aspectos, fique atento aos nossos próximos artigos.

## **Como uma requisição chega ao Pod?**

Quando uma requisição é feita para um Service no Kubernetes, o processo de redirecionamento para os Pods associados é gerenciado de forma eficiente pelo próprio Kubernetes, garantindo balanceamento de carga e alta disponibilidade.

1. **Recepção da Requisição:** Quando uma requisição é feita para um Service, seja externamente através de um LoadBalancer ou internamente via um ClusterIP, o Kubernetes começa o processo de roteamento da requisição.

2. **Balanceamento de Carga:** O Kubernetes usa uma estratégia de balanceamento de carga para distribuir as requisições entre os Pods associados ao Service. Por padrão, ele utiliza o método de round-robin, que distribui as requisições de forma equitativa entre os Pods. Esta abordagem garante que nenhum Pod seja sobrecarregado e que o tráfego seja distribuído de maneira eficiente.

3. **Roteamento para o Pod:** O Service tem um seletor que identifica quais Pods devem receber o tráfego. Quando a requisição chega ao Service, ele encaminha a requisição para um dos Pods que correspondem a esse seletor. O Kubernetes gerencia a tabela de endereços IP dos Pods e garante que a comunicação entre o Service e os Pods seja feita corretamente, mesmo se Pods forem adicionados ou removidos.

4. **Detecção de Falhas:** Caso um Pod falhe ou seja substituído, o Kubernetes ajusta automaticamente o balanceamento de carga para garantir que o tráfego seja redirecionado apenas para os Pods em funcionamento. Isso evita interrupções no serviço e melhora a resiliência da aplicação.

5. **Saúde e Disponibilidade:** O Kubernetes realiza verificações periódicas de saúde (probes) nos Pods. Se um Pod não atender a essas verificações, ele será removido do pool de serviços até que esteja novamente em um estado saudável. Isso assegura que o tráfego seja sempre direcionado para Pods funcionais.

![](/assets/img/89/service03.png){: h="25%" }

## **Tipos de Services**

O Kubernetes oferece cinco tipos principais de Services, cada um com funcionalidades específicas:

- **ClusterIP**: Use para comunicações internas no cluster, como serviços de backend, bancos de dados, e caches.
- **NodePort**: Use para acesso externo limitado, geralmente em ambientes de desenvolvimento ou onde você implementará sua própria solução de balanceamento de carga.
- **LoadBalancer**: Escolha este para exposições públicas de aplicações, como APIs e websites, especialmente em ambientes de produção.
- **ExternalName**: Ideal para redirecionar o tráfego para serviços externos usando o DNS.
- **Headless**: Para descobertas de serviço mais avançadas ou quando você precisa lidar diretamente com os Pods, sem passar pelo Kubernetes Service proxy.

## **ClusterIP (Padrão)**

O **ClusterIP** é o tipo de Service padrão. Ele expõe o Service em um IP interno do cluster, que só é acessível de dentro do cluster. É ideal para comunicação interna entre Pods, como comunicação entre microserviços ou acesso a bancos de dados.

![](/assets/img/89/service04.png){: h="25%" }

Suponha que você já tenha um **Pod** com o label `app=nginx` no namespace `default`.

**Exemplo Declarativo:**

````yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: meu-service
spec:
  ports:
  - port: 80
  selector:
    run: nginx
````

![](/assets/img/89/service08.gif){: h="25%" }

**Exemplo Imperativo:**

````shell
kubectl expose pod nginx --port=80 --target-port=80 --name=meu-service --type=ClusterIP
````

**Testando o Service ClusterIP**

Para testar o **ClusterIP**, crie um **Pod** temporário com `curl` e verifique o acesso ao Service:

````shell
kubectl run curl-pod --image=alpine:latest -it --rm -- sh -c "apk add curl && curl http://meu-service.default.svc.cluster.local -I"
````

![](/assets/img/89/service09.gif){: h="25%" }

## **NodePort**

O **NodePort** expõe o Service em um porto estático em cada Node do cluster, tornando-o acessível externamente através do IP de qualquer Node e do porta especificada. Isso permite que você acesse o Service fora do cluster, utilizando o IP de qualquer Node e o NodePort configurado. 

Embora útil para desenvolvimento e testes, o **NodePort** não é geralmente recomendado para produção devido à faixa limitada de portas e à falta de balanceamento de carga integrado.

![](/assets/img/89/service05.png){: h="25%" }

**Exemplo Declarativo:**

````yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: meu-service-2
spec:
  ports:
  - port: 80
  selector:
    run: nginx
  type: NodePort
````

![](/assets/img/89/service10.gif){: h="25%" }

**Exemplo Imperativo:**

````shell
kubectl expose pod nginx --port=80 --target-port=80 --name=meu-service-2 --type=NodePort
````

**Testando o Service NodePort:**

Para testar o **NodePort**, você precisa acessar o **Service** usando o **IP** de qualquer **Node** no cluster e o porta especificada. O IP do Node pode ser encontrado com o comando:

````shell
kubectl get nodes -o wide
````

Este comando listará os Nodes do cluster com seus IPs. Use o IP de qualquer Node listado e o **NodePort** configurado para este service para acessar a aplicação. Por exemplo:

````shell
curl IP-do-Node:30007 -I
````

![](/assets/img/89/service11.gif){: h="25%" }

## **LoadBalancer**

Os **LoadBalancer** Services são expostos fora do seu cluster usando um recurso de balanceador de carga externo. Isso requer uma conexão com um provedor de balanceamento de carga, o que geralmente é alcançado integrando seu cluster ao ambiente de nuvem. Ao criar um serviço **LoadBalancer**, o Kubernetes provisiona automaticamente um novo componente de infraestrutura de balanceador de carga na sua conta de nuvem. Essa funcionalidade é configurada automaticamente quando você utiliza um serviço gerenciado de Kubernetes, como **Amazon EKS**, **Google GKE** ou **Azure AKS**.

Depois de criar um serviço **LoadBalancer**, você pode apontar seus registros **DNS** públicos para o endereço IP do balanceador de carga provisionado. Isso direcionará o tráfego para o seu Service no Kubernetes, garantindo que ele chegue aos Pods corretos. Portanto, os LoadBalancers são o tipo de Service que você deve usar normalmente quando precisa que uma aplicação seja acessível fora do Kubernetes.

![](/assets/img/89/service06.png){: h="25%" }

**Exemplo Declarativo no AKS (Azure Kubernetes Service):**

````yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: meu-service-3
spec:
  ports:
  - port: 80
  selector:
    run: nginx
  type: LoadBalancer
````

![](/assets/img/89/service12.gif){: h="25%" }

No exemplo acima, `loadBalancerIP` pode ser um IP público estático previamente configurado no Azure. Se você não especificar `loadBalancerIP`, o **AKS** atribuirá automaticamente um IP público.

**Exemplo Imperativo no AKS:**

````shell
kubectl expose pod nginx --port=80 --target-port=80 --name=meu-service-3 --type=LoadBalancer
````

**Testando o Service LoadBalancer no AKS:**

Para obter o IP externo atribuído ao serviço **LoadBalancer**, você pode usar o seguinte comando:

````shell
kubectl get svc meu-service-3
````

````shell
curl <IP-externo-do-LoadBalancer>
````

![](/assets/img/89/service13.gif){: h="25%" }

## **ExternalName**

O **ExternalName** mapeia um Service para um nome de** DNS externo**, redirecionando o tráfego para fora do cluster. Ele é útil para quando você precisa acessar serviços externos como se estivessem dentro do cluster, sem necessidade de proxies adicionais.

![](/assets/img/89/service07.png){: h="25%" }

**Exemplo Declarativo:**

````yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: meu-service-4
  name: meu-service-4
spec:
  externalName: minha-aplicacao-externa.com
  selector:
    app: meu-service-4
  type: ExternalName
````

**Exemplo Imperativo:**

````shell
kubectl create service externalname meu-service-4 --external-name=minha-aplicacao-externa.com
````

## **Headless Services**

Um **Headless** Service permite que você direcione diretamente para os Pods sem passar pelo proxy de serviços do Kubernetes. Isso é útil quando você deseja lidar diretamente com os endereços IP dos Pods, por exemplo, em sistemas de descoberta de serviços mais avançados ou quando utiliza stateful applications.

**Exemplo Declarativo:**

````yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: meu-headless-service
spec:
  clusterIP: None
  ports:
  - port: 80
  selector:
    run: nginx
````

**Exemplo Imperativo:**

````shell
kubectl expose pod nginx --port=80 --name=meu-headless-service --cluster-ip=None 
````

## **Qual tipo de Service devo usar?**

A escolha do tipo de Service correto depende de como sua aplicação será acessada:

| Característica                 | ClusterIP                                              | NodePort                                                                 | LoadBalancer                                                          | ExternalName                                                              | Headless                                                              |
|--------------------------------|--------------------------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------|-----------------------------------------------------------------------|
| **Acessibilidade**             | Interna                                                | Externa                                                                  | Externa                                                               | Interna                                                                   | Interna                                                               |
| **Caso de uso**                | Expor Pods para outros Pods no cluster                 | Expor Pods em uma porta específica de cada Node                           | Expor Pods usando um recurso de balanceador de carga de nuvem         | Configurar um registro DNS CNAME que resolve para um endereço especificado  | Interface com sistemas de descoberta de serviços externos             |
| **Indicado para**              | Comunicações internas entre workloads                  | Acessar workloads fora do cluster, para uso em desenvolvimento ou testes | Servir aplicativos web e APIs acessíveis publicamente em produção     | Desacoplar workloads de dependências diretas em URLs de serviços externos | Networking avançado que evita o proxy automático do Kubernetes        |
| **Tipo de conexão do cliente** | Endereço IP ou nome DNS interno estável                | Porta no endereço IP do Node                                             | Endereço IP do balanceador de carga externo                           | Endereço IP ou nome DNS interno estável                                   | Endereço IP ou nome DNS interno estável que também permite resolução DNS dos IPs dos Pods por trás do Service |
| **Dependências externas**      | Nenhuma                                                 | Porta livre em cada Node                                                  | Componente de Load Balancer (geralmente cobrado pelo provedor de nuvem) | Nenhuma                                                               | Nenhuma                                                                |

## **Conclusão**

Os **Services** no Kubernetes desempenham um papel essencial ao facilitar a comunicação tanto entre seus **Pods** quanto com o mundo **exterior**. A escolha do tipo de Service correto é **crucial** para garantir que sua aplicação seja acessível da forma adequada, funcione de maneira eficiente e mantenha-se segura. Seja para expor uma aplicação internamente com um **ClusterIP** ou para permitir o acesso externo com um **LoadBalancer**, entender as particularidades de cada tipo de Service é fundamental.

Sempre que possível, **teste** e **valide cuidadosamente** a configuração dos seus Services antes de implementá-los em produção. Isso não apenas assegura que sua aplicação estará funcionando conforme o esperado, mas também evita potenciais problemas de segurança e acessibilidade que poderiam surgir de uma configuração incorreta.

E, como sempre, não se esqueça de consultar a documentação oficial do Kubernetes para mais detalhes e práticas recomendadas sobre o uso de Services!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/services-networking/service/" target="_blank">Services</a>

É isso, galera! Espero que tenham gostado!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
