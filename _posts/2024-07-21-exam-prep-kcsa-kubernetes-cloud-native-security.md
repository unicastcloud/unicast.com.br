---
layout: post
title: "[Exam-Prep] Kubernetes and Cloud Native Security Associate (KCSA)"
author: asilva
date: 2024-07-21 08:00:00 -0500
categories: [Certificação, Exam-Prep]
tags: [certificação, devops, kubernetes, cloudnative, cncf, automacao, iac, cicd, container, linux, linuxfoundation, sre, sysadmin, observabilidade]
---

Fala, galera! Como vocês estão?

Hoje, quero compartilhar com vocês sobre a certificação **KCSA: Kubernetes and Cloud Native Security Associate**, que conquistei no dia 21 de julho de 2024.

![](/assets/img/83/kcsa01.png){: "width=50%" }

Falando sobre o exame, ele tem nível associate, similar ao do exame **KCNA**: Kubernetes and Cloud Native Associate, que eu já fiz e sobre o qual escrevi um artigo aqui no site.

Você pode conferir aqui: <a href="https://unicast.com.br/posts/exam-prep-kcna-kubernetes-cloud-native/" target="_blank">[Exam-Prep] Kubernetes and Cloud Native Associate (KCNA)</a>

A exemplo do exame **KCNA**, esta certificação não tem o nível de complexidade de uma **CKA**, **CKAD** e **CKS**, que são exames práticos.

O **KCSA** é um exame de múltipla escolha com um limite de tempo de 90 minutos e 60 questões, e custa a bagatela de **$250**, incluindo o **retake**.

Eu sei, o meme continua o mesmo, mas também vale a mesma premissa que sempre falo: para nós, brasileiros, e mesmo para quem ganha em dólar, as provas da **Linux Foundation** são caras. Não acho que justifique pagar o preço cheio nelas, principalmente nestes exames de nível associate.

![](/assets/img/83/kcsa02.jpg){: "width=60%" }

Minha recomendação é esperar pelos descontos. Ultimamente, a Linux Foundation tem feito muitas promoções, e no mínimo você consegue 40% de desconto (que foi meu caso; paguei $150). Não é o mundo perfeito, mas já ajuda muito, especialmente em provas como a CKA, CKAD e CKS, que custam $395 somente o exame com retake.

Se serve de consolo, o retake te deixa mais tranquilo para fazer a prova. Se falhar, você remarca a prova e está tudo certo.

Porém, é o que sempre falo para meus alunos e mentorados: você é dono da sua carreira, você dita o ritmo, você não pode ficar esperando pelo mercado. Vai ter horas em que você vai precisar escolher, e pagar esses valores em uma prova de certificação pode ser um passo importante para o seu futuro profissional.

No meu momento profissional, essa certificação não muda em nada o cenário, mas pessoalmente sim. Eu sei onde quero chegar e o que preciso para atingir meus objetivos. Então, é isso: eu preciso fazer o que deve ser feito. Isso implica em dedicação para os estudos e investimentos financeiros. Não existe milagre, não existe atalho. O mais preparado prospera, pense sempre nisso!

Mas voltando para o exame:

Eu já venho estudando para o exame CKAD: Kubernetes for Developers, então tive a brilhante ideia de fazer o KCSA pelos seguintes motivos:

1. Validar meu conhecimento atual em Kubernetes.
2. Avaliar o nível dos meus estudos.
3. Aproveitar os estudos do tópico 6. Understanding Security.

O tópico **6. Understanding Security da CKAD** traz bastante conteúdo que cai no KCSA, pois este exame de segurança é focado em Kubernetes e Cloud Native.

Aqui, já tenho uma ressalva: se por um lado o KCNA é um bom exame para começar no mundo do Kubernetes, eu não aconselho fazer este exame sem ter uma boa base de Kubernetes e aplicações Cloud Native.

Eu diria que o melhor momento para fazer essa prova é justamente após uma CKA ou CKAD, usando-a como preparação para uma CKS.

No meu caso, eu segui este raciocínio:

> KCNA --> CKA --> KCSA --> preparando para a CKAD.
{: .prompt-info }

No geral, só vá para essa prova com algum conhecimento de Kubernetes.

Segue a definição da Cloud Native Computing Foundation:

>A KCSA é uma certificação pré-profissional desenvolvida para candidatos interessados em avançar para o nível profissional por meio de uma compreensão demonstrada de conhecimentos e habilidades fundamentais de tecnologias de segurança no ecossistema nativo da nuvem.
>
>Um KCSA certificado confirmará o entendimento da configuração básica de segurança dos clusters Kubernetes para atender aos objetivos de conformidade.
>
>A KCSA demonstrará o conhecimento básico do candidato sobre a configuração básica de segurança dos clusters Kubernetes para atender aos objetivos de conformidade, incluindo sua capacidade de fortalecer os controles de segurança, testar e monitorar a segurança e participar na avaliação de riscos e vulnerabilidades de segurança.

## **Os temas cobrados no exame são:**

- Overview of Cloud Native Security: 14%
- Kubernetes Cluster Component Security: 22%
- Kubernetes Security Fundamentals: 22%
- Kubernetes Threat Model: 16%
- Platform Security: 16%
- Compliance and Security Frameworks: 10%

Segue o link oficial da prova: 

Segue a o link official da prova: <a href="https://training.linuxfoundation.org/certification/kubernetes-and-cloud-native-security-associate-kcsa/" target="_blank">Kubernetes and Cloud Native Security Associate (KCSA).</a>

## **Material utilizado**

Como mencionei, eu já estou estudando para o exame CKAD, então, para o KCSA, utilizei apenas um pequeno treinamento que encontrei por acaso no LinkedIn Learning. Não sei se vai ser muito útil compartilhar, pois você precisa ter o LinkedIn Premium para acessá-lo, mas foi somente o que usei para fazer o exame.

É um treinamento curto, mas cobre o necessário e deixa links para que você possa se aprofundar um pouco mais nos conceitos da prova.

## **Lista de materiais**

- Michael Levan - <a href="https://www.linkedin.com/learning/cert-prep-kubernetes-and-cloud-native-security-associate-kcsa/what-you-should-know?autoSkip=true&resume=false" target="_blank"> Kubernetes and Cloud Native Security Associate (KCSA) Cert Prep.</a>

Caso você não tenha acesso ao treinamento, vou deixar aqui alguns links importantes para se aprofundar, e você vai precisar deles mesmo assistindo ao treinamento acima:

- Rowan Baker - <a href="https://www.youtube.com/watch?v=AplluksKvzI" target="_blank">Threat Modelling: Securing Kubernetes Infrastructure & Deployments - Rowan Baker, ControlPlane</a>
-  CNCF Blog - <a href="https://www.cncf.io/blog/2022/04/12/a-map-for-kubernetes-supply-chain-security/" target="_blank">A MAP for Kubernetes supply chain security</a>
- OWASP Cheat Sheet Series - <a href="https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html" target="_blank">Kubernetes Security Cheat Sheet</a>
- Linkerd - <a href="https://linkerd.io/what-is-a-service-mesh/#" target="_blank">What is a service mesh?</a>
- CNCF Landscape - <a href="https://landscape.cncf.io/guide#introduction" target="_blank">Cloud Native Landscape</a>
- CIS Center for Internet Security - <a href="https://www.cisecurity.org/benchmark/kubernetes" target="_blank">Kubernetes CIS</a>
- Red Hat - <a href="https://www.redhat.com/en/topics/containers/compliance" target="_blank">Container and Kubernetes compliance considerations</a>

## **Sobre o exame**

Eu gostei bastante da prova. Como você precisa de um conhecimento prévio de Kubernetes e também sobre aspectos gerais de segurança, ela traz uma camada mais avançada quando comparada com o KCNA, que tem uma proposta parecida (em termos de exame).

E tome cuidado, se você entrar muito despretensioso, corre sérios riscos de reprovar, até porque você precisa atingir pelo menos 75% para passar no exame. O tempo de prova é de 90 minutos e é suficiente para resolvê-la.

Se você deseja obter conhecimentos fundamentais de segurança para Kubernetes, esta é uma ótima certificação para começar. Mesmo que você não faça o exame, o conhecimento geral que você obterá com ele será muito benéfico.

O conhecimento que você obtém com o KCSA é quase como um pré-requisito para o CKS. Você não precisa passar no KCSA para obter a CKS, mas o conhecimento que você ganha com o KCSA será transferido perfeitamente para o CKS.

Espero que gostem do material e que ele seja útil nos seus estudos.

Forte abraço a todos e boa prova!
