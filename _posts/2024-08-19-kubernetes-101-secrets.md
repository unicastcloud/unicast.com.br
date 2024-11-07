---
layout: post
title: "Kubernetes 101: Secrets"
author: asilva
date: 2024-08-19 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No capítulo de hoje da nossa série "**Kubernetes 101"**, vamos falar sobre **Secrets**, uma ferramenta indispensável no Kubernetes para armazenar dados sensíveis, como senhas, tokens de API, e certificados. Se você já conhece os ConfigMaps, pode pensar nos Secrets como uma versão mais segura e apropriada para informações confidenciais. Contudo, é importante entender as nuances de segurança dos Secrets no Kubernetes.

## **O que é um Secret?**

Um **Secret** é um objeto do Kubernetes que permite armazenar informações sensíveis em pares chave-valor. Esses dados são codificados em base64 e mantidos no etcd, o banco de dados interno do Kubernetes, mas vale lembrar que essa codificação não é o mesmo que criptografia. 

Ela apenas oculta os valores de uma visualização direta, mas não os protege contra acesso indevido. Se você realmente precisa de criptografia para garantir a confidencialidade dos dados, deve configurar criptografia no etcd.

Por padrão, o Kubernetes oferece apenas essa codificação em **base64**, mas a partir da versão `1.13`, ele também permite a criptografia dos Secrets em repouso no etcd, o que é altamente recomendado em ambientes de produção para fortalecer a segurança.

## **Como criar um Secret**

Existem duas maneiras principais de criar um Secret no Kubernetes: **declarativa** (com arquivos YAML) e imperativa (com comandos **kubectl**).

**Exemplo Declarativo**

Para criar um Secret que contenha informações de banco de dados, defina um arquivo YAML como o seguinte:

````yaml
apiVersion: v1
kind: Secret
metadata:
  name: meu-secret
  namespace: default
type: Opaque
data:
  DB_USER: YWRtaW4=          # admin
  DB_PASSWORD: c2VuaGExMjM=  # senha123
````

Para codificar um valor em base64, você pode usar o comando:

````bash
echo -n 'admin' | base64
````

> Lembre-se: a codificação em base64 apenas ofusca o valor, mas não garante segurança. Certifique-se de proteger o acesso aos Secrets e considerar criptografia.

**Exemplo Imperativo**

Para criar um Secret de forma imperativa, você pode usar o seguinte comando:

````bash
kubectl create secret generic meu-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASSWORD=senha123
````

## **Consumindo Secrets em Pods**

Você pode utilizar Secrets em Pods como variáveis de ambiente ou montá-los como arquivos de volume, da mesma forma que ConfigMaps.

**Exemplo de Secret como variável de ambiente**

Para expor um Secret como variáveis de ambiente dentro de um Pod, adicione a referência da seguinte forma:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-secret
spec:
  containers:
    - name: meu-container
      image: nginx
      envFrom:
        - secretRef:
            name: meu-secret
````

**Exemplo de Secret como arquivo em volume**

Outra opção é montar o Secret como um volume, onde cada chave se torna um arquivo:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-secret-volume
spec:
  containers:
    - name: meu-container
      image: nginx
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret
  volumes:
    - name: secret-volume
      secret:
        secretName: meu-secret
````

Neste exemplo, o Secret será montado em `/etc/secret`, onde cada chave estará em um arquivo separado.

## **Segurança dos Secrets**

Embora o Kubernetes facilite o uso de Secrets, é importante tomar medidas extras para protegê-los:

- Criptografia em repouso: Habilite a criptografia dos Secrets no etcd para ambientes de produção.
- Controle de acesso: Use controles rigorosos de RBAC (Role-Based Access Control) para limitar o acesso aos Secrets.
- Configuração de nodes confiáveis: Lembre-se de que qualquer administrador do nó onde o Pod está rodando pode ter acesso aos volumes montados contendo os Secrets.

Os Secrets são armazenados em etcd codificados em base64, mas, sem criptografia configurada, um backup ou acesso não autorizado ao etcd pode expor os dados. Portanto, habilitar criptografia em repouso e monitorar o acesso são práticas recomendadas.

## **Testando Secrets**

Para verificar se os Secrets foram aplicados corretamente, você pode realizar testes similares aos de ConfigMaps:

**1. Verificando Variáveis de Ambiente**

Execute um Pod temporário:

````bash
kubectl run -it --rm curl-pod --image=alpine -- /bin/sh
````

No terminal do Pod, liste as variáveis de ambiente para ver se as do Secret foram carregadas:

````bash
env | grep DB_
````

Ou exiba uma variável específica:

````bash
echo $DB_USER
````

**2. Verificando Arquivos Montados como Volumes**

Se o Secret foi montado como um volume, verifique os arquivos:

````bash
# Liste os arquivos montados
ls /etc/secret

# Verifique o conteúdo de um dos arquivos
cat /etc/secret/DB_USER
````

Esse comando exibirá o valor configurado para `DB_USER` no Secret.

## **Conclusão**

Os **Secrets** no Kubernetes são ferramentas poderosas para gerenciar informações sensíveis, mas exigem cuidados adicionais para garantir a segurança. A configuração de criptografia para o etcd, o uso de RBAC e outras medidas de segurança ajudam a proteger esses dados críticos. 

E, como sempre, não se esqueça de consultar a documentação oficial do Kubernetes para mais detalhes e práticas recomendadas sobre o uso de Secrets!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/configuration/secret/" target="_blank">Secrets</a>

É isso, galera! Espero que tenham gostado!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
