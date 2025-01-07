---
layout: post
title: "Kubernetes 101: StorageClass"
author: asilva
date: 2024-10-07 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

O **StorageClass** é um recurso essencial no Kubernetes, responsável por gerenciar o armazenamento dinâmico no cluster. Ele automatiza o provisionamento de volumes persistentes, integrando-se com provedores de nuvem, como Azure, e torna a gestão de armazenamento mais eficiente e flexível. Neste artigo, vamos explorar em detalhes o que é o StorageClass, como ele funciona e como configurá-lo usando o Azure como exemplo.

## **O que é StorageClass?**

No **Kubernetes**, um **StorageClass** define `"classes de armazenamento"` para provisionar volumes de forma dinâmica. Ele permite que você especifique parâmetros como o tipo de armazenamento, nível de redundância e políticas de recuperação diretamente na configuração.

O **StorageClass** elimina a necessidade de criar manualmente **PersistentVolumes (PVs)**, provisionando automaticamente o armazenamento necessário quando um **PersistentVolumeClaim (PVC)** é criado.

## **Por que usar StorageClass?**

O uso do StorageClass oferece diversas vantagens:

- **Automação**: Criação automática de volumes persistentes sob demanda.
- **Flexibilidade**: Suporte para diferentes tipos de armazenamento (SSD, HDD, NFS, etc.).
- **Integração com provedores de nuvem**: Suporte nativo para serviços como Azure Disk, Azure File, AWS EBS, entre outros.
- **Escalabilidade**: Ideal para ambientes dinâmicos onde o armazenamento precisa ser gerenciado de forma eficiente.

## **Criando um StorageClass no Azure**

**Exemplo básico: Azure Disk**

Vamos configurar um StorageClass para provisionar volumes usando o **Azure Disk**, um serviço de armazenamento gerenciado pela Microsoft.

1. Criando o StorageClass

O arquivo YAML abaixo define um StorageClass que usa o Azure Disk:

````yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-disk-standard
provisioner: kubernetes.io/azure-disk
parameters:
  skuName: Standard_LRS
reclaimPolicy: Delete
````

- **provisioner**: Define o provisionador usado pelo Azure Disk.
- **skuName**: Especifica o tipo de disco (exemplo: `Standard_LRS` ou `Premium_LRS`).
- **reclaimPolicy**: Define o comportamento ao excluir um PVC. Neste caso, o volume será excluído automaticamente.

Comando para aplicar o StorageClass:

````bash
kubectl apply -f azure-disk-storageclass.yaml
````

2. Criando um PersistentVolumeClaim (PVC)

Agora, criamos um PVC que solicita um volume com base no StorageClass:

````yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: meu-pvc-azure
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: azure-disk-standard
````

- **accessModes**: Define como o volume será acessado (`ReadWriteOnce` para acesso exclusivo por um nó).
- **storage**: Solicita 10 GB de armazenamento.
- **storageClassName**: Associa o PVC ao StorageClass criado anteriormente.

Comando para aplicar o PVC:

````bash
kubectl apply -f pvc-azure.yaml
````

3. Usando o PVC em um Pod

Com o PVC pronto, ele pode ser montado em um Pod:

````yaml 
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod-azure
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: "/mnt/storage"
      name: volume-disk
  volumes:
  - name: volume-disk
    persistentVolumeClaim:
      claimName: meu-pvc-azure
````

Comando para criar o Pod:

````bash
kubectl apply -f pod-azure.yaml
````

4. Verificando o Status

Para verificar os StorageClasses disponíveis:

````bash
kubectl get storageclass
````

Saída esperada:

````bash
NAME                  PROVISIONER               RECLAIMPOLICY   VOLUMEBINDINGMODE   AGE
azure-disk-standard   kubernetes.io/azure-disk  Delete          Immediate           15m
````

Para listar os PersistentVolumes e PersistentVolumeClaims:

````bash
kubectl get pv
kubectl get pvc
````

Saída esperada para kubectl get pvc:

````bash
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS        AGE
meu-pvc-azure   Bound    pvc-12345678-90ab-cdef-ghij-klmnopqrstuv   10Gi       RWO            azure-disk-standard 10m
````

Neste exemplo, o volume provisionado será montado no caminho `/mnt/storage` dentro do contêiner.

## **Políticas de Recuperação (ReclaimPolicy)**

As políticas de recuperação determinam o que acontece com os volumes persistentes quando o PVC associado é excluído:

- **Delete**: O volume é excluído automaticamente.
- **Retain**: O volume é mantido para análise ou reutilização manual.

No exemplo anterior, usamos a política `Delete`. Para reter o volume, basta alterar a configuração do StorageClass:

````yaml
reclaimPolicy: Retain
````

## **Outros parâmetros úteis**

O StorageClass permite a customização de vários aspectos do armazenamento:

- **skuName**: Define o tipo de disco (exemplo: `Standard_LRS` ou `Premium_LRS`).
- **kind**: Pode ser configurado como `Shared` ou `Managed` no Azure Disk.
- **fsT-ype**: Define o sistema de arquivos usado (exemplo: `ext4` ou `xfs`).

## **Conclusão**

O **StorageClass** é uma ferramenta poderosa no Kubernetes que simplifica e automatiza a gestão de armazenamento dinâmico, especialmente em ambientes que utilizam provedores de nuvem como o Azure. Com o uso adequado de StorageClasses, você pode criar soluções mais flexíveis, escaláveis e alinhadas às necessidades da sua aplicação.

E, como sempre, não deixe de consultar a documentação oficial do Kubernetes para mais detalhes e práticas recomendadas sobre o uso de **StorageClass**!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/storage/storage-classes/" target="_blank">StorageClass</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
