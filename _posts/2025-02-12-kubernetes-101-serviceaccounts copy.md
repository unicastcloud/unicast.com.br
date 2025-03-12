---
layout: post
title: "Kubernetes 101: Service Accounts"
author: asilva
date: 2025-02-12 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No Kubernetes, as **Service Accounts** (Contas de Serviço) são usadas para fornecer uma identidade aos processos que rodam dentro dos Pods. Ao contrário das contas de usuário, que são para pessoas, as **Service Accounts** são projetadas para aplicações e serviços automatizados.

Cada Pod no Kubernetes automaticamente recebe uma **Service Account**, a menos que uma específica seja atribuída. Isso permite que os Pods interajam com a API do Kubernetes de forma controlada.

## **O que é uma Service Account?**

Uma **Service Account** é um recurso do Kubernetes que representa uma identidade atribuída a um ou mais Pods. Ela permite que aplicações rodando dentro dos Pods realizem ações autenticadas, como listar pods, criar configmaps, ou acessar outros recursos da API.

Por padrão, cada namespace tem uma Service Account chamada `default`, automaticamente vinculada a novos Pods.

Para listar as Service Accounts de um namespace:

````bash
kubectl get serviceaccounts -n default
````

Saída esperada:

````bash
NAME      SECRETS   AGE
default   1         10d
````

## **Criando uma Service Account**

Podemos criar uma Service Account personalizada via YAML:

````yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
````

Ou de forma imperativa:

````bash
kubectl create serviceaccount my-service-account --namespace=default
````

Verifique a criação:

````bash
kubectl get serviceaccounts -n default
````

## **Associando Service Accounts a Pods**

Para atribuir uma Service Account específica a um Pod, usamos a chave `serviceAccountName` na definição do Pod:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-service-account
  namespace: default
spec:
  serviceAccountName: my-service-account
  containers:
  - name: nginx
    image: nginx
````

Após aplicar o YAML:

````bash
kubectl apply -f pod-with-service-account.yaml
````

Verifique a Service Account associada ao Pod:

````bash
kubectl get pod pod-with-service-account -n default -o jsonpath='{.spec.serviceAccountName}'
````

Resultado esperado:

````bash
my-service-account
````

## **Vinculando permissões: Roles e RoleBindings**

Uma Service Account, sozinha, não tem permissões suficientes para acessar a maioria dos recursos da API. Para conceder acesso, vinculamos a Service Account a uma **Role** ou **ClusterRole** usando um **RoleBinding** ou **ClusterRoleBinding**.

Exemplo: conceder permissão para listar pods no namespace `default`:

**Role**:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
````

**RoleBinding**:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
````

Aplicando ambos:

````bash
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml
````

Teste se a Service Account pode listar pods:

````bash
kubectl auth can-i list pods --as=system:serviceaccount:default:my-service-account
````

Resultado esperado:

````bash
yes
````

Se precisar de um lembrete sobre **Roles** e **RoleBindings**, confira nosso artigo anterior: <a href="https://unicast.com.br/posts/kubernetes-101-rbac/" target="_blank">Kubernetes 101: Role-Based Access Control (RBAC)</a>

## **Testando com um Pod**

Vamos testar a permissão em um Pod que usa essa Service Account:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-test-sa
  namespace: default
spec:
  serviceAccountName: my-service-account
  containers:
  - name: busybox
    image: busybox
    command: ["/bin/sh", "-c", "kubectl get pods"]
````

Aplicar o Pod:

````bash
kubectl apply -f pod-test-sa.yaml
````

Verificar os logs para garantir que a lista de Pods foi exibida:

````bash
kubectl logs pod-test-sa
````

Se tudo foi configurado corretamente, você verá a lista de Pods!

## **Conclusão**

As Service Accounts são fundamentais para garantir que suas aplicações interajam de maneira segura com a API do Kubernetes. Ao combinar Service Accounts com o RBAC (Roles e RoleBindings), você mantém um controle rigoroso sobre o que cada aplicação pode ou não fazer.

Não se esqueça de testar as permissões e revisar suas configurações de segurança regularmente!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/security/service-accounts/" target="_blank">Service Accounts</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
