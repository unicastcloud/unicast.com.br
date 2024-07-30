---
layout: post
title: "Kubernetes 101: POD"
author: asilva
date: 2024-07-08 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, um Pod é a unidade básica de execução de um aplicativo. Ele pode conter um ou mais **containers** que compartilham recursos de **rede** e **armazenamento**, funcionando como uma entidade de implantação e gerenciamento. Neste artigo vamos explorar em profundidade a estrutura dos Pods, padrões de aplicação, métodos de inicialização, e detalhes operacionais, essenciais para quem busca dominar Kubernetes.

## **Anatomia de um Pod**

**O que é um Pod?**

Um **Pod** é a menor unidade de implantação em **Kubernetes**, encapsulando um ou mais **containers**. Ele garante que esses containers compartilhem o mesmo endereço IP, namespace de rede, e volumes de armazenamento, facilitando a comunicação e a gestão de dados entre eles.

![](/assets/img/83/pod02.png){: "width=30%" } 

![](/assets/img/83/pod03.png){: h="25%" }

- **Namespace de rede compartilhado:** Todos os containers em um Pod compartilham o mesmo namespace de rede, permitindo a comunicação através de localhost. Isso é crucial para aplicações que requerem alta interação entre componentes, como microserviços.
- **Volumes compartilhados:** Volumes podem ser usados para persistência de dados e compartilhamento entre containers. Eles são definidos na especificação do Pod e montados em diretórios específicos dos containers.

**Componentes principais**

- **Containers**: A maioria dos Pods contém apenas um container, mas casos multi-container são usados para fornecer serviços complementares como logging ou proxying. Esses containers compartilham recursos e dependem uns dos outros para executar corretamente.
- **Volumes**: Volumes são usados para persistir dados e compartilhar entre containers. Eles podem ser de vários tipos, incluindo emptyDir (temporário), hostPath (diretório no nó do host), ou PersistentVolume (para armazenamento durável).
- **Metadados**: Metadados incluem labels, que são cruciais para a seleção de Pods por serviços e controladores, e annotations, que são usadas para armazenar informações adicionais sobre o Pod.

![](/assets/img/83/pod04.jpg){: "width=30%" } 

## **Padrões de aplicação multi-container**

![](/assets/img/83/pod05.png){: "width=30%" } 

**Padrão Sidecar**

Containers **sidecar** são usados para adicionar funcionalidades auxiliares a um aplicativo principal, como monitoramento, logging, ou proxy. Eles são executados ao lado do container principal e compartilham os mesmos recursos.

**Exemplos práticos:**

- **Agente de Log**: Um sidecar que coleta e envia logs para uma central de monitoramento.
- **Proxy de API**: Um sidecar que gerencia a comunicação segura entre o aplicativo e serviços externos.

 **Padrão Ambassador**

Containers **ambassador** são utilizados para gerenciamento de comunicação entre a aplicação e serviços externos. Eles agem como intermediários, simplificando a lógica de rede dentro do aplicativo.

**Exemplos práticos:**

- **Proxy de API:** Facilita a comunicação entre a aplicação e APIs externas, gerenciando autenticação e roteamento.
- **Gateway de Serviços:** Um ambassador pode servir como um gateway centralizado, gerenciando o tráfego de entrada e saída.

**Padrão Adapter**

Adapters são usados para adaptar a saída de um container para que ela se adeque a outro serviço ou processo. Eles podem modificar formatos de dados ou protocolos de comunicação.

**Exemplos práticos:**

- **Conversão de logs:** Um adapter que transforma logs de uma aplicação em um formato específico exigido por um sistema de monitoramento.
- **Transformação de dados:** Adapta dados de saída para integração com diferentes sistemas ou APIs.

## **A Maneira declarativa e imperativa de iniciar pods**

**Método Declarativo**

O método **declarativo** é preferido para ambientes de produção, pois facilita o controle de versões e a repetibilidade da configuração. Arquivos **YAML** são usados para descrever o estado desejado dos Pods, que é então aplicado ao cluster.

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: exemplo-pod
  labels:
    app: exemplo
spec:
  containers:
  - name: nginx
    image: nginx
````

- **Controle de versão:** YAMLs permitem rastrear mudanças na configuração dos Pods, facilitando rollback e auditoria.
- **Infraestrutura como código**: Promove a prática de infraestrutura como código, essencial para automação e consistência.

![](/assets/img/83/pod08.gif){: h="30%" }

**Método Imperativo**

O método **imperativo** é usado para comandos rápidos e diretos, como criar, atualizar ou excluir Pods. É útil para operações ad-hoc, mas não é ideal para gerenciamento a longo prazo.

````bash
kubectl run exemplo-pod --image=nginx
````

- **Uso em desenvolvimento:** Útil em ambientes de desenvolvimento e testes rápidos.
- **Limitações em produção:** Menos controlável e rastreável em comparação com o método declarativo.

## **Entrando nos detalhes dos pods**

**Ciclo de vida dos Pods**

Entender o ciclo de vida dos Pods é crucial para gerenciamento eficaz. Cada Pod passa por várias fases desde sua criação até a exclusão.

![](/assets/img/83/pod06.png){: "width=30%" } 

Fases do ciclo de vida:

- **Pending**: O Pod foi criado, mas um ou mais dos containers ainda estão em processo de inicialização ou agendamento.
- **Running**: Todos os containers foram inicializados com sucesso e o Pod está em execução.
- **Succeeded**: Todos os containers no Pod terminaram a execução com sucesso e o Pod não será reiniciado.
- **Failed**: Ocorreu uma falha em um ou mais containers e eles não serão reiniciados.
- **Unknown**: Estado indeterminado onde não há informações claras sobre o que o Pod está fazendo ou sua localização.
- **CrashLoopBackoff**: O container falha ao iniciar e o Kubernetes tenta reiniciá-lo repetidamente, com intervalos crescentes entre as tentativas.

**Criação de um Pod**

Para entender como os Pods são criados no Kubernetes, vamos examinar um exemplo detalhado do processo:

![](/assets/img/83/pod07.webp){: "width=30%" } 

1. O kubectl ou qualquer outro cliente de API envia a especificação do Pod para o servidor de API.
2. O servidor de API grava o objeto Pod no armazenamento de dados etcd. Após a gravação ser bem-sucedida, uma confirmação é enviada de volta ao servidor de API e ao cliente.
3. O servidor de API reflete a mudança no estado do etcd.
4. O Kube-scheduler vê que um novo objeto Pod foi criado no servidor de API, mas não está vinculado a nenhum nó.
5. O kube-scheduler atribui um nó ao Pod e atualiza o servidor de API.
6. Esta mudança é então propagada para o armazenamento de dados etcd. O servidor de API também reflete essa atribuição de nó em seu objeto Pod.
7. O Kubelet em cada nó mantém observadores que monitoram o servidor de API. O Kubelet no nó de destino vê que um novo Pod foi atribuído a ele.
8. O Kubelet inicia o Pod em seu nó, chamando o Docker (ou outro runtime de container) e atualiza o estado do container de volta ao servidor de API.
9. O servidor de API persiste o estado do Pod no etcd.

**Container probes**

As sondas de container (container **probes**) são usadas no Kubernetes para monitorar a saúde e o status dos containers em um Pod. Elas fornecem informações importantes para o plano de controle do Kubernetes, permitindo decisões sobre o estado dos containers e Pods.

> Fique tranquilo, eu pretendo abordar e aprofundar este assunto em outros artigos!
{: .prompt-info }

**Tipos de probes**

- **Liveness Probes**: Verificam se um container está em execução e saudável. Se a sonda de liveness falhar, o container é reiniciado automaticamente.
- **Readiness Probes**: Determinam quando um container está pronto para começar a aceitar tráfego. Isso é útil quando um container precisa de tempo para inicializar ou concluir uma tarefa antes de poder lidar com o tráfego.
- **Startup Probes**:** Verificam se um container foi iniciado com sucesso e está inicializado. Ao contrário das sondas de liveness e readiness, as sondas de startup são executadas apenas

Cada tipo de probe tem uma finalidade diferente e é usada em diferentes estágios do ciclo de vida de um contêiner.

## **Conclusão**

Os Pods são a pedra angular do Kubernetes, servindo como a unidade mínima de computação que pode ser criada, gerenciada e escalada dentro de um cluster. Compreender a anatomia dos Pods, incluindo seus componentes e padrões de aplicação, é essencial para qualquer administrador ou desenvolvedor que deseja aproveitar ao máximo o Kubernetes.

Os métodos declarativo e imperativo de gerenciamento de Pods oferecem flexibilidade para diferentes cenários, desde ambientes de desenvolvimento ágil até operações robustas em produção. Adicionalmente, as sondas de container e o ciclo de vida dos Pods são aspectos cruciais para garantir a saúde e a disponibilidade das aplicações.

A criação de Pods é um processo que envolve múltiplos componentes do Kubernetes, desde o envio da especificação até a atribuição e execução em um nó específico. Isso exemplifica a coordenação complexa que o Kubernetes realiza para manter os aplicativos funcionando de forma eficiente e resiliente.

Com tudo isso em mente, o conhecimento sobre Pods é o primeiro passo para dominar esta poderosa plataforma de orquestração de containers.

É isso galera, espero que gostem!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
