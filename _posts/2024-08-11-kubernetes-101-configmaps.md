---
layout: post
title: "Kubernetes 101: ConfigMaps"
author: asilva
date: 2024-08-11 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No capítulo de hoje da nossa série **"Kubernetes 101"**, vamos explorar um dos conceitos mais essenciais no Kubernetes: os **ConfigMaps**. No Kubernetes, os ConfigMaps permitem armazenar configurações de aplicação em arquivos separados, facilitando a manutenção e a atualização de parâmetros sem a necessidade de alterar o código ou reconstruir imagens de contêineres. ConfigMaps são especialmente úteis para armazenar variáveis de configuração que podem variar entre ambientes (desenvolvimento, teste, produção), mantendo a consistência e a flexibilidade na configuração das aplicações. 

## **O que é um ConfigMap?**

Um **ConfigMap** é um objeto do Kubernetes que armazena dados de configuração em pares chave-valor, permitindo que essas configurações sejam facilmente injetadas em contêineres. Com ConfigMaps, você pode externalizar a configuração das suas aplicações, separando-a da lógica do aplicativo e da imagem do contêiner, o que possibilita um gerenciamento mais seguro e escalável.

## **Como criar um ConfigMap**

Você pode criar um **ConfigMap** no Kubernetes usando tanto o método declarativo (**YAML**) quanto o imperativo (comandos **kubectl**).

**Exemplo Declarativo**

Para criar um ConfigMap com uma configuração de banco de dados, você pode definir um arquivo YAML como este:

````yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: meu-configmap
  namespace: default
data:
  DB_HOST: "mysql.database.local"
  DB_PORT: "3306"
  DB_USER: "admin"
  DB_PASSWORD: "senha123"
````

**Exemplo Imperativo**

Para criar um ConfigMap usando o comando imperativo, você pode usar o seguinte:

````bash
kubectl create configmap meu-configmap \
  --from-literal=DB_HOST=mysql.database.local \
  --from-literal=DB_PORT=3306 \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASSWORD=senha123
````

## **Consumindo ConfigMaps em Pods**

Existem várias maneiras de consumir um ConfigMap dentro de um Pod, incluindo como variáveis de ambiente, arquivos em um volume ou argumentos para o contêiner.

**Exemplo de ConfigMap como variável de ambiente**

Para acessar o ConfigMap no seu contêiner como variáveis de ambiente, adicione-o ao Pod da seguinte forma:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-configmap
spec:
  containers:
    - name: meu-container
      image: nginx
      envFrom:
        - configMapRef:
            name: meu-configmap
````

**Exemplo de ConfigMap como arquivo em volume**

Você também pode montar o ConfigMap como um volume, onde cada chave será um arquivo separado:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-configmap-volume
spec:
  containers:
    - name: meu-container
      image: nginx
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: meu-configmap
````

No exemplo acima, o ConfigMap será montado no contêiner em `/etc/config`, e cada chave será representada por um arquivo separado.

**Testando ConfigMaps**

Para testar um ConfigMap, crie um Pod temporário com um contêiner que possua curl ou um comando de shell para verificar se as variáveis ou os arquivos de configuração foram corretamente configurados.

**Exemplo de Teste**

Usando um contêiner baseado em Alpine com curl:

````bash
kubectl run curl-pod --image=alpine --rm -i --tty -- /bin/sh
````

Depois de criar um Pod temporário com kubectl run, você pode verificar se as variáveis de ambiente do ConfigMap foram carregadas corretamente:

Dentro do shell do Pod, você pode usar o comando env ou echo para verificar as variáveis:

````bash
# Liste todas as variáveis de ambiente e veja se as do ConfigMap estão presentes
env | grep DB_

# Ou exiba uma variável específica, como DB_HOST, para verificar seu valor
echo $DB_HOST
````

Esses comandos exibirão as variáveis de ambiente `DB_HOST`, `DB_PORT`, `DB_USER`, e `DB_PASSWORD` se foram corretamente aplicadas a partir do ConfigMap.

Se o ConfigMap foi montado como um volume, você poderá verificar se os arquivos foram criados no caminho especificado (`/etc/config` neste exemplo):


````bash
# Dentro do Pod, liste os arquivos montados
ls /etc/config

# Verifique o conteúdo de um dos arquivos
cat /etc/config/DB_HOST
````

Esse comando cat `/etc/config/DB_HOST` mostrará o conteúdo da chave `DB_HOST` configurada no ConfigMap. Você pode repetir o comando para cada chave que estiver presente.

## **Conclusão**

Os **ConfigMaps** são uma ferramenta poderosa no Kubernetes, permitindo gerenciar configurações de maneira centralizada e segura. Isso facilita a manutenção, atualização e teste das configurações sem necessidade de alterar a imagem do contêiner. Ao utilizar ConfigMaps, você melhora a flexibilidade e segurança das suas aplicações no cluster.

E, como sempre, não se esqueça de consultar a documentação oficial do Kubernetes para mais detalhes e práticas recomendadas sobre o uso de ConfigMaps!

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/docs/concepts/configuration/configmap/" target="_blank">ConfigMaps</a>

É isso, galera! Espero que tenham gostado!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
