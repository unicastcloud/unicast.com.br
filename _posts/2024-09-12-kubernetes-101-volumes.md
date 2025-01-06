---
layout: post
title: "Kubernetes 101: Volumes"
author: asilva
date: 2024-09-12 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

Hoje vamos mergulhar no fascinante mundo dos **Volumes** no Kubernetes. Quem nunca ficou confuso tentando entender como gerenciar armazenamento persistente para seus Pods, não é mesmo? Pois é, no capítulo de hoje da série **"Kubernetes 101"**, vamos explorar tudo sobre **Volumes**, desde os conceitos básicos até exemplos práticos, ajudando você a gerenciar o armazenamento no Kubernetes de maneira eficiente e organizada.

## **O que é um volume no Kubernetes?**

Um **Volume** no Kubernetes é uma abstração de armazenamento que pode ser usada pelos contêineres dentro de um Pod. Diferente do armazenamento padrão do contêiner (que é temporário e desaparece quando o contêiner é reiniciado), os Volumes no Kubernetes permitem persistir dados de forma mais confiável.

Um Volume pode ser usado para compartilhar dados entre contêineres no mesmo Pod ou para armazenar dados que sobrevivam ao ciclo de vida de um contêiner.

## **Por que usar Volumes?**

Sem Volumes, os contêineres são efêmeros por natureza:

- Todos os dados gravados no sistema de arquivos de um contêiner são perdidos assim que ele reinicia.
- Compartilhar dados entre múltiplos contêineres no mesmo Pod seria inviável.

Com Volumes, você pode:

1. Persistir dados críticos entre reinicializações.
2. Compartilhar informações entre contêineres.
3. Integrar sistemas de armazenamento externos, como NFS, Amazon EBS ou Google Persistent Disks.

## **Tipos de Volumes no Kubernetes**

O Kubernetes oferece vários tipos de Volumes para atender diferentes casos de uso. Aqui estão alguns dos mais importantes:

| **Tipo de Volume**          | **Descrição**                                                                        |
|-----------------------------|--------------------------------------------------------------------------------------|
| `emptyDir`                  | Criado no nó onde o Pod está sendo executado. Ideal para armazenamento temporário.   |
| `hostPath`                  | Monta um diretório no sistema de arquivos do nó diretamente no Pod.                  |
| `nfs`                       | Conecta a um servidor de armazenamento NFS externo.                                  |
| `persistentVolume`          | Associado a um PersistentVolumeClaim para armazenamento persistente.                 |
| `configMap`                  | Armazena configurações e os monta como arquivos no contêiner.                         |
| `secret`                    | Monta dados sensíveis, como senhas e tokens, como arquivos ou variáveis de ambiente. |

## **Como declarar Volumes em Pods?**

Os Volumes são configurados no arquivo YAML do Pod, geralmente especificando:

1. O volume no nível do Pod.
2. Onde e como o volume será montado no contêiner.

Os Volumes são configurados no arquivo YAML do Pod, geralmente especificando:

Exemplo básico com **emptyDir**

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-emptydir
spec:
  containers:
  - name: meu-container
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: meu-volume
  volumes:
  - name: meu-volume
    emptyDir: {}
````

## Persistência com **PersistentVolumes** e **PersistentVolumeClaims**

Em cenários de produção, é comum que as aplicações precisem de armazenamento persistente, que não desapareça quando o Pod for reiniciado ou excluído. Para isso, o Kubernetes introduz o conceito de **PersistentVolumes (PV)** e **PersistentVolumeClaims (PVC)**. Vamos entender cada um deles e como funcionam juntos.

**O que é um PersistentVolume (PV)?**

O **PersistentVolume** é um recurso de armazenamento que o administrador do cluster configura previamente. Ele representa uma unidade de armazenamento física ou lógica dentro do cluster. Pode ser:

- Um disco físico anexado a um nó (por exemplo, um diretório local ou NFS).
- Um disco virtual gerenciado por um provedor de nuvem (como AWS EBS, GCP Persistent Disk, ou Azure Disk).

Um PV é independente de qualquer Pod e fica disponível no cluster até ser requisitado por um **PersistentVolumeClaim**.

**O que é um PersistentVolumeClaim (PVC)?**

Um **PersistentVolumeClaim** é uma solicitação de armazenamento feita pelos desenvolvedores ou aplicações.

- Pense no PVC como uma "ordem de serviço" para o Kubernetes encontrar um PV que atenda às suas necessidades.
- O PVC define o tamanho, o modo de acesso (leitura/escrita), e outras características necessárias.

Quando um PVC é criado, o Kubernetes faz o "match" (associação) com um PV disponível que atenda aos critérios do PVC.

**Como funcionam juntos?**

1. **Administração de PVs**: O administrador configura os PVs no cluster, especificando o tipo de armazenamento, capacidade e outras configurações.
2. **Requisição de PVCs**: Os desenvolvedores criam PVCs com as especificações que suas aplicações precisam.
3. **Associação automática:** O Kubernetes associa o PVC a um PV disponível que tenha as características requisitadas.
4. **Montagem no Pod**: O PVC pode então ser usado como um Volume nos Pods, permitindo que os contêineres acessem o armazenamento persistente.

**Exemplo completo: PersistentVolume e PersistentVolumeClaim**

Aqui vai um exemplo prático para esclarecer o processo.

1. Criando um PersistentVolume (PV)

````yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: meu-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data
````

Detalhes:

- **capacity**: Define o tamanho do volume (neste caso, 5Gi).
- **accessModes**: Define como o volume pode ser acessado:
  - **ReadWriteOnce**: Montado como leitura e escrita por apenas um Pod.
  - **ReadOnlyMany**: Montado como somente leitura por múltiplos Pods.
  - **ReadWriteMany**: Montado como leitura e escrita por múltiplos Pods.
- **persistentVolumeReclaimPolicy**: Indica o que acontece com o PV depois que o PVC associado for excluído. Neste caso, a política é Retain (o PV será mantido).
- **hostPath**: Define o caminho físico do volume no nó.

2. Criando um PersistentVolumeClaim (PVC)

````yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: meu-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
````

Detalhes:

- O PVC solicita um armazenamento de **5Gi** e especifica que ele deve ser acessado em modo de leitura e escrita por um único Pod.
- Assim que este PVC for criado, o Kubernetes procurará automaticamente por um PV que atenda a essas especificações (neste caso, nosso **meu-pv**).

3. Usando o PVC no Pod

Agora, vamos criar um Pod que utiliza o PVC como um Volume:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-pv
spec:
  containers:
  - name: meu-container
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: meu-volume
  volumes:
  - name: meu-volume
    persistentVolumeClaim:
      claimName: meu-pvc
````

Explicação do exemplo:

- O Pod monta o PVC chamado `meu-pvc` no contêiner no caminho `/data`.
- Qualquer dado gravado em `/data` será armazenado no **PersistentVolume** associado, garantindo persistência mesmo se o Pod for reiniciado ou excluído.

Verificando o status

Você pode verificar o status do **PV** e **PVC** com os comandos abaixo:

````bash
# Listar PersistentVolumes
kubectl get pv meu-pv
````

Exemplo de Output:

````bash
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                STORAGECLASS   AGE
meu-pv   10Gi       RWO            Retain           Bound       default/meu-pvc      standard       5m
````

Explicação:

- **NAME**: Nome do `PersistentVolume`, neste caso `meu-pv`.
- **CAPACITY**: Tamanho alocado (10Gi).
- **ACCESS MODES**: Os modos de acesso permitidos (RWO - ReadWriteOnce).
- **RECLAIM POLICY**: Política de recuperação do volume após ser liberado (Retain).
- **STATUS**: Mostra se o volume está `Bound` (vinculado) ou `Available` (disponível).
- **CLAIM**: Informa o `PersistentVolumeClaim` associado (neste caso, `default/meu-pvc`).
- **STORAGECLASS**: Classe de armazenamento (padrão: `standard`).
- **AGE**: Tempo desde a criação do volume.

````bash
# Listar PersistentVolumeClaims
kubectl get pvc meu-pvc
````

Exemplo de Output:

````bash
NAME       STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
meu-pvc    Bound    meu-pv   10Gi       RWO            standard       5m
````

Explicação:

- **NAME**: Nome do `PersistentVolumeClaim`, neste caso `meu-pvc`.
- **STATUS**: Mostra se o PVC está `Bound` (vinculado a um volume).
- **VOLUME**: Nome do `PersistentVolume` associado (neste caso, `meu-pv`).
- **CAPACITY**: Tamanho solicitado (10Gi).
- **ACCESS MODES**: Modos de acesso solicitados (RWO - ReadWriteOnce).
- **STORAGECLASS**: Classe de armazenamento usada (padrão: `standard`).
- **AGE**: Tempo desde a criação do claim.

````bash
# Verificando os volumes montados em um Pod
kubectl describe pod meu-pod
````

Exemplo de Output:

````bash
Volumes:
  meu-volume:
    Type: PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName: meu-pvc
    ReadOnly: false
...
Mounts:
  /data from meu-volume (rw)
````

Explicação:

- **Volumes**: Lista os volumes configurados no Pod. Neste caso, o volume `meu-volume` é do tipo `PersistentVolumeClaim` e está vinculado ao PVC `meu-pvc`.
- **Mounts**: Mostra o local onde o volume foi montado no contêiner (`/data`) e o tipo de acesso (`rw` - leitura e escrita).

## **Políticas de Reclaim**

As políticas de reclaim controlam o que acontece com um PV quando o PVC associado é excluído. Existem três opções principais:

- **Retain**: O PV é mantido e precisa ser liberado manualmente para reutilização.
- **Recycle**: O PV é limpo (os dados são apagados) e fica disponível para outro PVC.
- **Delete**: O PV é deletado automaticamente (recomendado para armazenamento dinâmico em nuvem).

## **Armazenamento Dinâmico (StorageClass)**

Para automatizar ainda mais o processo, você pode usar **StorageClasses**, que permitem ao Kubernetes criar PersistentVolumes dinamicamente, sem necessidade de configuração manual dos PVs.

Quando você define um PersistentVolumeClaim (PVC) que especifica uma classe de armazenamento, o Kubernetes utiliza o StorageClass correspondente para provisionar um PersistentVolume (PV) automaticamente. Isso é especialmente útil em ambientes de nuvem, como Azure, AWS ou GCP.

Em um próximo artigo da série, exploraremos o StorageClass em detalhes!

## **Conclusão**

Os **Volumes** são uma parte essencial para trabalhar com Kubernetes, fornecendo maneiras flexíveis e eficientes de gerenciar o armazenamento em Pods. Com uma boa compreensão dos diferentes tipos de Volumes e seus casos de uso, você pode construir aplicações mais resilientes e escaláveis.

E, como sempre, não se esqueça de consultar a documentação oficial do Kubernetes para mais detalhes e práticas recomendadas sobre o uso de **Volumes**!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/storage/volumes/" target="_blank">Volumes</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
