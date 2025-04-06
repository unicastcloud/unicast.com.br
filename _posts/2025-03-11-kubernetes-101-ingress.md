---
layout: post
title: "Kubernetes 101: Ingress"
author: asilva
date: 2025-03-11 09:20:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, k8s, cluster, cloudnative, containers, docker, microservices]
---

Fala galera! Seis tão baum?

No ecossistema **Kubernetes**, gerenciar o tráfego de entrada para os serviços é fundamental para garantir a disponibilidade e a segurança das aplicações. Embora os **Services** forneçam uma maneira básica de expor aplicações, o **Ingress** oferece uma solução mais robusta e flexível para o roteamento de tráfego **HTTP/HTTPS**. 

Com o **Ingress**, é possível implementar recursos avançados como roteamento baseado em **hostname** e **path**, suporte a **TLS**, **reescrita de URLs** e **balanceamento de carga**.​

## **O que é um Ingress?**

O **Ingress** é um recurso do Kubernetes que gerencia o acesso externo aos serviços dentro de um cluster, geralmente para tráfego **HTTP** e **HTTPS**. Ele permite:​

![](/assets/img/108/ingress01.png){: "style="max-width: 50%" }

- **Roteamento por hostname**: Direciona o tráfego com base no nome do host (por exemplo, `app1.example.com`, `app2.example.com`).​
- **Roteamento por path**: Encaminha solicitações com base no caminho da URL (por exemplo, `/api`, `/frontend`).​
- **Suporte a TLS**: Oferece terminação SSL/TLS para criptografar o tráfego.​
- **Reescrita e redirecionamento de URLs**: Modifica as URLs das solicitações conforme necessário.​
- **Balanceamento de carga**: Distribui o tráfego entre múltiplas instâncias de um serviço.​
- **Autenticação e controle de acesso**: Implementa políticas de segurança para proteger os serviços.

O Ingress foi introduzido para atender à necessidade de uma configuração declarativa e centralizada para expor aplicações web no **Kubernetes**, sem depender exclusivamente de **LoadBalancers**, que podem ser limitados ou onerosos em ambientes de nuvem. 

Com o tempo, o **Ingress** evoluiu para suportar recursos como **TLS**, **múltiplos hosts**, **redirecionamento** e **autenticação**. Atualmente, existem diversos **Ingress Controllers disponíveis**, cada um com suas particularidades e integrações específicas com provedores de nuvem.

> É importante notar que o **Kubernetes** não inclui um **Ingress Controller** por padrão; é necessário instalar um controlador compatível, como NGINX, Traefik, HAProxy, Istio, entre outros. 
{: .prompt-warning }

## **Principais Ingress Controllers** 

Como dito anteriormente, é importante entender que o recurso **Ingress** por si só é apenas uma `definição de regra` no **Kubernetes**. Ele não funciona sozinho. Para que as regras de roteamento funcionem de fato, é necessário um **Ingress Controller**, que é quem implementa esse comportamento na prática.

O **Kubernetes** não vem com um **Ingress Controller embutido** — você precisa instalar um por conta própria. Existem vários disponíveis, cada um com suas particularidades. Abaixo estão os mais usados e suas principais características:

**NGINX Ingress Controller**

- O mais popular e amplamente utilizado.
- Fácil de instalar (via Helm ou YAMLs diretos).
- Suporta TLS, redirecionamentos, reescritas, autenticação básica, canary releases, rate limiting, etc.
- Mantido como projeto oficial dentro da comunidade Kubernetes.
- Ideal para a maioria dos casos de uso.

<a href="https://kubernetes.github.io/ingress-nginx" target="_blank">NGINX Ingress Controller</a> 

**HAProxy Ingress**

- Baseado no conhecido proxy HAProxy.
- Alta performance, suporte sólido a TCP e HTTP.
- Recomendado para quem já usa HAProxy fora do Kubernetes.
- Suporta autenticação, WAF, e customizações avançadas.

<a href="https://www.haproxy.com/documentation/kubernetes/latest/" target="_blank">HAProxy Ingress</a> 

**Traefik**

- Foco em simplicidade e observabilidade.
- Configuração dinâmica via CRDs.
- Painel web nativo com métricas e status.
- Ótima integração com Let’s Encrypt para TLS automático.
- Bom para ambientes com mudanças frequentes.

<a href="https://doc.traefik.io/traefik/" target="_blank">Traefik</a> 

**Istio (Gateway + VirtualService)**

- Parte do service mesh Istio.
- Substitui o Ingress tradicional por objetos como Gateway e VirtualService.
- Oferece roteamento L7 avançado, segurança mTLS, e observabilidade de ponta.
- Ideal para arquiteturas complexas e ambientes com Service Mesh.

<a href="https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/" target="_blank">Istio</a> 

**Kong Ingress Controller**

- Baseado no API Gateway Kong.
- Oferece plugins para autenticação JWT, rate-limiting, cache, etc.
- Ótimo para cenários de API Management.
- Compatível com Gateway API.

<a href="https://docs.konghq.com/kubernetes-ingress-controller/" target="_blank">Kong Ingress Controller</a> 

**Application Gateway Ingress Controller (AGIC) – Microsoft Azure**

Para clusters **Kubernetes** rodando no **Azure** (como **AKS**), você pode utilizar o **Azure Application Gateway como Ingress Controller**. Essa integração é feita por meio do **AGIC** (**Application Gateway Ingress Controller**), que permite que o **Application Gateway** gerencie o tráfego de entrada com base nos recursos Ingress definidos no cluster.

- Gerenciado pelo Azure, você aproveita um recurso nativo da infraestrutura da nuvem sem precisar instalar e manter um ingress controller - tradicional.
- Suporte a TLS, redirecionamentos e WAF (Web Application Firewall) nativamente integrados.
- Observabilidade nativa via Azure Monitor e integração com o Azure Front Door.
- Alta disponibilidade embutida por padrão no Application Gateway.
- Suporte completo a rotas baseadas em host e path, respeitando as regras definidas nos manifests do Kubernetes.
- Pode ser usado como único ponto de entrada (entrypoint) externo do cluster em arquiteturas corporativas.

## **Como escolher o melhor Ingress Controller?**

A seleção do controlador apropriado deve considerar fatores como complexidade do ambiente, requisitos de desempenho e necessidades específicas de recursos.

- Simplicidade e comunidade forte? → NGINX
- TLS automático e painel embutido? → Traefik
- Service Mesh e segurança avançada? → Istio
- Alta performance com controle granular? → HAProxy
- Gestão de APIs robusta? → Kong
- Solução nativa em cloud e recursos como WAF (AKS) → Application Gateway Ingress Controller (AGIC)

> Dica 1: Você também pode rodar mais de um Ingress Controller no mesmo cluster, desde que cada Ingress seja associado ao seu respectivo controller via anotações.
{: .prompt-tip }

> Dica 2: Outros provedores de nuvem também oferecem suas próprias soluções:
  **AWS** → AWS Load Balancer Controller
  **GCP** → Google Cloud Load Balancer com GKE Ingress
{: .prompt-tip }

## **Diferença entre Service e Ingress**

Embora ambos sejam usados para expor aplicações, **Services** e **Ingress** têm propósitos distintos:​

![](/assets/img/108/ingress02.png){: "style="max-width: 50%" }

- **Service (NodePort/LoadBalancer)**: Fornece acesso básico aos Pods, expondo-os internamente ou externamente. Suporta tráfego TCP e UDP, mas carece de recursos avançados de roteamento HTTP.​
- **Ingress**: Gerencia o tráfego HTTP/HTTPS com regras flexíveis, permitindo roteamento avançado, terminação TLS e outras funcionalidades.

**#Comparativo: Service vs Ingress**

| **Característica**             | **Service (NodePort / LoadBalancer)**                              | **Ingress**                                                        |
|--------------------------------|--------------------------------------------------------------------|--------------------------------------------------------------------|
| **Propósito**                  | Expor Pods dentro ou fora do cluster                               | Gerenciar tráfego HTTP/HTTPS com regras avançadas                  |
| **Tipos de tráfego**           | TCP e UDP                                                          | HTTP e HTTPS                                                       |
| **Roteamento por path/host**   | ❌ Não suporta                                                     | ✅ Suporta (por host e por caminho)                                |
| **Suporte a TLS (HTTPS)**      | ❌ Necessita configuração manual por Service                       | ✅ Nativo, com suporte a múltiplos certificados                    |
| **Balanceamento de carga**     | ✅ Sim, mas simples (via kube-proxy)                               | ✅ Sim, geralmente com controle mais granular                      |
| **Requer Ingress Controller**  | ❌ Não                                                             | ✅ Sim                                                             |
| **Casos de uso**               | Aplicações simples, serviços TCP/UDP, backends sem roteamento HTTP | APIs, sites, apps com múltiplos caminhos e domínios personalizados |
| **Escalabilidade**             | Limitada dependendo do tipo (NodePort é o mais limitado)           | Alta, principalmente com controllers como NGINX ou Traefik         |
| **Integração com DNS**         | Indireta (ponto fixo no LoadBalancer ou IP do NodePort)            | Direta (baseada em nome de host)                                   |


## Técnicas de Roteamento com Ingress

O Ingress permite a implementação de técnicas avançadas de roteamento para atender a diferentes cenários:

**Roteamento Baseado em Hostname**

Permite direcionar o tráfego para diferentes serviços com base no nome do host.​

````yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hostname-routing
spec:
  rules:
  - host: app1.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
  - host: app2.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
````

Neste exemplo, o tráfego destinado a `app1.example.com` é encaminhado para `app1-service`, enquanto o tráfego para `app2.example.com` é direcionado para `app2-service`. ​

**Roteamento Baseado em Path**

Direciona solicitações para diferentes serviços com base no caminho da URL.

````yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-routing
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /service1
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
      - path: /service2
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 80
````

Aqui, as solicitações para `example.com/service1` são encaminhadas para `service1`, e as para `example.com/service2` vão para `service2`. ​

**TLS com Ingress**

Ingress também suporta terminação **TLS**, permitindo servir conteúdo criptografado via HTTPS. Para isso, você precisa de um Secret do tipo tls com a chave privada e o certificado.

````yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
spec:
  tls:
    - hosts:
        - secure.example.com
      secretName: tls-secret
  rules:
    - host: secure.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: secure-service
                port:
                  number: 443
````

O Secret pode ser criado assim:

````bash
kubectl create secret tls tls-secret \
  --cert=/caminho/para/certificado.crt \
  --key=/caminho/para/privada.key
````

**Canary Releases com Ingress**

Uma prática comum em ambientes de produção é a liberação gradual de versões — conhecida como **canary release**. Com Ingress (em conjunto com anotações e alguns controladores como o NGINX), é possível direcionar uma porcentagem do tráfego para uma nova versão do serviço.

Exemplo com NGINX usando header-based routing:

````yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: canary-ingress
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-by-header: "Canary"
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: myapp-v2
                port:
                  number: 80
````

Esse Ingress só será usado se o cabeçalho HTTP Canary: true estiver presente na requisição — ideal para testes controlados em produção.

## Testando seu Ingress

Após criar um Ingress, é importante testar:

1. DNS resolvido corretamente: certifique-se de que `app.example.com` aponta para o IP externo do Ingress Controller (LoadBalancer ou IP do Node).
2. Curl simples para validar roteamento:

````bash
curl http://app.example.com/service1
curl -H "Host: app1.example.com" http://<Ingress-IP>
````

3. Verificação de TLS:

````bash
curl https://secure.example.com --cacert certificado.crt
````

## **Comportamento entre Namespaces**

**Ingress** é um recurso **namespaced**, ou seja, ele só pode encaminhar tráfego para `Services` **no mesmo namespace**, a menos que o controlador permita configurações avançadas como `externalName` ou `cross-namespace routing`.

Se quiser que múltiplos apps em namespaces diferentes compartilhem um mesmo domínio (ex: `*.example.com`), considere uma arquitetura com **Ingress centralizado + services expostos por `NodePort` ou `LoadBalancer` entre namespaces**, ou utilize **Gateway API** (modelo mais novo que está substituindo o Ingress em alguns casos).

## **Ingress vs Gateway API**

O **Ingress** é a solução padrão desde versões iniciais do **Kubernetes**, mas vem sendo gradualmente complementado pela **Gateway API**, que oferece:

- Separação de responsabilidades (infraestrutura vs time de app)
- Suporte nativo a múltiplos protocolos (HTTP, TCP, gRPC)
- Maior extensibilidade via CRDs
- Modelagem mais clara e moderna

Se você está começando ou mantendo clusters existentes, o **Ingress** ainda é o padrão, mas vale acompanhar a evolução da **Gateway API**.


## Dicas Práticas

- Use ferramentas como **k9s** ou **Lens** para inspecionar facilmente recursos de Ingress.
- Configure **redirects** e **rewrites** diretamente via anotações específicas do Ingress Controller.
- Para ambientes com T**L**S automatizado, considere usar `cert-manager` com **Let's Encrypt**.
- Sempre monitore os **logs** do Ingress Controller — eles mostram muito sobre o que está ou não está funcionando.

## **Conclusão**

**Ingress** é um dos componentes mais poderosos e essenciais no **Kubernetes**, permitindo um controle detalhado sobre como o tráfego externo acessa seus serviços. Com ele, é possível construir estratégias de roteamento inteligentes, seguras e flexíveis, simplificando o gerenciamento de aplicações web em ambientes de produção.

Ele também é o ponto onde o Kubernetes se conecta ao mundo real: ao seu DNS, às suas políticas de segurança e à experiência do usuário. Dominar o uso de Ingress significa dominar um dos principais pilares da arquitetura moderna em nuvem.

Você pode acessar a documentação oficial aqui: <a href="https://kubernetes.io/pt-br/docs/concepts/services-networking/ingress/" target="_blank">Ingress</a>

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
