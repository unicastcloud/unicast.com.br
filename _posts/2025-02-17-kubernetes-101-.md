---
layout: post
title: "Kubernetes 101: Security Contexts"
author: asilva
date: 2025-02-17 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, a segurança das aplicações não se limita apenas ao controle de acesso via RBAC (Role-Based Access Control) ou ao uso de Service Accounts. Um aspecto crucial para garantir que os Pods e Containers operem com o mínimo de privilégios necessários é o uso dos **Security Contexts**.

O Security Context permite definir configurações de segurança em diferentes níveis:

- **Pods**: Configurações que afetam todos os containers dentro de um Pod.
- **Containers**: Configurações específicas para cada container individual.

Isso inclui definir permissões de usuário, grupos, privilégios de root, controles de acesso ao sistema de arquivos e mais.

## **Configurações de Security Context**

Principais atributos

Aqui estão algumas das configurações mais comuns que podem ser usadas nos Security Contexts:

| Atributo                     | Nível          | Descrição                                               |
|------------------------------|----------------|---------------------------------------------------------|
| **runAsUser**                | Pod/Container  | Define o UID com o qual o processo será executado.      |
| **runAsGroup**               | Pod/Container  | Define o GID para o processo.                           |
| **fsGroup**                  | Pod            | Especifica o GID que será usado para volumes montados.  |
| **allowPrivilegeEscalation** | Container      | Permite ou nega a elevação de privilégios.              |
| **privileged**               | Container      | Define se o container será executado como privilegiado. |
| **readOnlyRootFilesystem**   | Container      | Monta o sistema de arquivos root como somente leitura.  |

## **Exemplos práticos**

**Definindo Security Context em um Pod**

Neste exemplo, o Pod cria um container que roda com um usuário específico (`UID 1000`) e monta o sistema de arquivos root como somente leitura:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsUser: 1000
    fsGroup: 2000
  containers:
    - name: secure-container
      image: busybox
      securityContext:
        readOnlyRootFilesystem: true
        allowPrivilegeEscalation: false
      command: ["sleep", "3600"]
````

Explicação:

- O Pod roda todos os processos com o UID `1000`.
- Volumes montados herdarão o grupo `2000`.
- O container:
  - Usa um sistema de arquivos root somente leitura.
  - Proíbe a elevação de privilégios.

**Definindo Security Context em Containers específicos**

Se precisar de configurações diferentes para cada container dentro de um Pod multi-container, você pode definir Security Contexts individualmente:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
    - name: container-1
      image: nginx
      securityContext:
        runAsUser: 1001
        privileged: false
    - name: container-2
      image: busybox
      securityContext:
        runAsUser: 1002
        allowPrivilegeEscalation: true
````

Explicação:

- O `container-1` roda com o UID `1001` e sem privilégios.
- O `container-2` roda com o UID `1002` e permite elevação de privilégios.

## **Ligação com RBAC e Service Accounts**

Os Security Contexts se integram perfeitamente com o controle de acesso RBAC e Service Accounts (abordados nos artigos anteriores da série):

- O Security Context **controla como** um processo roda dentro de um container.
- O RBAC e Service Accounts controlam o que esse processo pode acessar dentro do cluster.

Por exemplo, mesmo que um Pod tenha permissões administrativas via RBAC, o Security Context pode impedir que os containers rodem como root ou que alterem o sistema de arquivos.

## **Testando e validando Security Contexts**

Você pode validar as configurações de segurança com o comando:

````bash
kubectl exec -it <pod-name> -- id
````

Isso mostrará o UID e GID atuais com os quais o container está rodando.

Para testar se um container consegue alterar o sistema de arquivos root (verificando o `readOnlyRootFilesystem`):

````bash
kubectl exec -it <pod-name> -- touch /test-file
````

Se configurado corretamente, isso resultará em um erro:

````bash
touch: cannot touch '/test-file': Read-only file system
````

## **Conclusão**

Os **Security Contexts** são uma parte vital da segurança no Kubernetes, permitindo que você reforce as políticas de segurança ao nível dos containers e Pods. Ao combiná-los com RBAC e Service Accounts, você obtém uma abordagem robusta para proteger suas aplicações.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/tasks/configure-pod-container/security-context/" target="_blank">Configure a Security Context for a Pod or Container</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
