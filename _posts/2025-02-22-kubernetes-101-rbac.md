---
layout: post
title: "Kubernetes 101: Role-Based Access Control (RBAC)"
author: asilva
date: 2025-02-22 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

O **Role-Based Access Control (RBAC)** é o mecanismo do Kubernetes para controlar quem pode acessar o quê dentro de um cluster. Com o **RBAC**, é possível definir políticas que determinam quais ações (como **criar**, **listar** ou **excluir** recursos) um **usuário**, **grupo** ou **service account** pode realizar.

RBAC é baseado em quatro principais componentes:

- **Role**: Concede permissões específicas dentro de um namespace.
- **RoleBinding**: Associa uma Role a um usuário, grupo ou service account em um namespace.
- **ClusterRole**: Define permissões em nível de cluster ou para recursos globais.
- **ClusterRoleBinding**: Associa uma ClusterRole a um usuário, grupo ou service account em todo o cluster.

Esses componentes trabalham juntos para oferecer um controle de acesso granular e seguro. O RBAC é habilitado por padrão no Kubernetes, mas caso precise garantir que ele está ativo, o servidor da **API** (**kube-apiserver**) deve ser iniciado com o modo de autorização configurado:

````yaml
kube-apiserver --authorization-mode=RBAC
````

## **Roles e RoleBindings**

**Role**

Um **Role** é um objeto que concede um conjunto de permissões (ações como **get**, **list**, **create**, etc.) sobre recursos específicos dentro de um namespace. **Roles** nunca podem ser usadas para conceder acesso fora do namespace em que estão definidas.

Exemplo de Role que permite listar e criar pods no namespace `default`:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" refere-se ao grupo core API
  resources: ["pods"]
  verbs: ["get", "list", "create"]
````

Explicação:

- **apiGroups**: O grupo de API ao qual o recurso pertence (pods estão no grupo core, representado por "").
- **resources**: Lista de recursos aos quais as permissões se aplicam.
- **verbs**: Ações permitidas — como `get`, `list`, `create`, `delete`, etc.

**RoleBinding**

O **RoleBinding** conecta uma Role a um ou mais sujeitos (usuário, grupo ou service account) em um namespace específico.

Exemplo de RoleBinding que atribui a Role `pod-reader ao usuário` `jane`:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
````

Explicação:

- **subjects**: Define quem receberá a permissão — pode ser User, Group ou ServiceAccount.
- **roleRef**: Faz referência à Role que está sendo vinculada.

Teste se o usuário `jane` pode listar pods no namespace `default`:

````bash
kubectl auth can-i list pods --namespace=default --as=jane
````

Se configurado corretamente, o resultado será:

````bash
yes
````

## **ClusterRoles e ClusterRoleBindings**

**ClusterRole**

Diferente de um Role, uma ClusterRole não está associada a um namespace específico. Ela pode definir permissões para:

- **Recursos não namespaced** (como Nodes ou PersistentVolumes).
- **Recursos namespaced em todos os namespaces** (por exemplo, listar pods em todos os namespaces).
- **Permissões em nível de cluster** (como gerenciar Namespaces).

Exemplo de ClusterRole que permite listar e assistir Nodes em todo o cluster:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin-role
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list", "watch"]
````

**ClusterRoleBinding**

Um **ClusterRoleBinding** conecta uma ClusterRole a um ou mais sujeitos, aplicando as permissões globalmente no cluster.

Exemplo de ClusterRoleBinding que concede permissões de admin ao usuário admin em todo o `cluster`:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
- kind: User
  name: admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin-role
  apiGroup: rbac.authorization.k8s.io
````

Teste se o usuário `admin` pode criar Nodes:

````bash
kubectl auth can-i create nodes --as=admin
````

## **Comparação: Role vs ClusterRole**

| Característica           | Role                      | ClusterRole                    |
| ------------------------ | ------------------------- | ------------------------------ |
| **Escopo**               | Namespace específico      | Cluster inteiro                |
| **Recursos gerenciados** | Pods, ConfigMaps, Secrets | Nodes, Namespaces, PVs         |
| **Uso comum**            | Controle granular por app | Acesso administrativo global   |
| **Vinculação**           | RoleBinding               | ClusterRoleBinding             |
| **Exemplo de permissão** | `get pods` em `default`   | `list nodes` em todo o cluster |

## **Testando permissões**

O comando `kubectl auth can-i` é uma ferramenta poderosa para validar permissões rapidamente.

Sintaxe:

````bash
kubectl auth can-i <ação> <recurso> --namespace=<namespace>
````

Exemplos:

Verificar se você pode listar pods no namespace `default`:

````bash
kubectl auth can-i list pods --namespace=default
````

Testar uma ação em nível de cluster:

````bash
kubectl auth can-i create nodes
````

Testar permissões como um usuário específico:

````bash
kubectl auth can-i delete services --as=jane --namespace=default
````

Checar todas as permissões de um usuário:

````bash
kubectl auth can-i --list --as=admin
````

Resultado: O comando retorna `yes` ou `no`, facilitando a verificação rápida.

## **Conclusão**

O **RBAC** no Kubernetes é essencial para controlar o acesso aos recursos do cluster de forma segura e flexível. Ao dominar **Roles**, **RoleBindings**, **ClusterRoles** e **ClusterRoleBindings**, você poderá configurar autorizações precisas para usuários, grupos e service accounts.

> **Dica**: Sempre teste permissões com `kubectl auth can-i` para garantir que as configurações estão corretas!
{: .prompt-info }

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/" target="_blank">Using RBAC Authorization</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
