---
layout: post
title: "AWS US-EAST-1: O apagão de 20 de outubro que deixou o Brasil e o mundo na emergência"
author: asilva
date: 2025-10-22 09:00:00 -0300
categories: [Cloud, AWS]
tags: [aws, us-east-1, cloud, falha, infraestrutura]
---

Fala galera! Seis tão baum?

Na última **segunda-feira, 20 de outubro de 2025**, uma falha crítica na região **US-EAST-1 da AWS** provocou instabilidade em sites e aplicativos por cerca de **15 horas**.  

Empresas como **iFood**, **Mercado Livre** e **PicPay** no Brasil, além de gigantes globais como **Prime Video**, **Zoom**, **Duolingo**, **Snapchat** e até jogos como **Fortnite**, foram afetados.  

![](/assets/img/114/awsdown01.jpeg){: "width=60%" }

Segundo a própria AWS, mais de **500 empresas** sofreram impactos diretos durante o apagão.

## Linha do tempo do incidente

- **04h11 (horário de Brasília)** — A AWS identificou **taxas de erro significativas** no **DynamoDB** e em serviços de **EC2** na região **US-EAST-1 (N. Virginia)**.  
- **Manhã e tarde** — O [Downdetector](https://downdetector.com.br/){:target="_blank"} registrou picos de reclamações em aplicativos de delivery, bancos digitais, e-commerces e plataformas de streaming.  
- **18h30** — A AWS reportava ainda **76 recursos com instabilidade** e **66 já resolvidos** em sua página de status.  
- **19h01 (horário de Brasília)** — A AWS informou que **todos os serviços haviam retornado ao funcionamento normal**, encerrando oficialmente o incidente.

## O que causou a falha?

A falha ocorreu na região **US-EAST-1**, o maior e mais antigo grupo de data centers da AWS, localizado no norte da Virgínia (EUA).  
A empresa possui atualmente **38 regiões de data centers** no mundo, incluindo uma no Brasil.

### Principais pontos técnicos:

- **DynamoDB**:  
  - Foram registradas **taxas de erro significativas** no serviço de banco de dados NoSQL da AWS.  
  - O problema estava ligado ao **processo de resolução de DNS**, que traduz nomes de domínio em endereços IP.  
  - Isso afetou a comunicação entre serviços internos e externos, gerando falhas em cascata.  

- **EC2 (Elastic Compute Cloud)**:  
  - Empresas tiveram dificuldades para **criar novas instâncias**.  
  - O EC2, que deveria escalar elasticamente, ficou limitado, impedindo que workloads críticos fossem expandidos.  

- **Impacto em cadeia**:  
  - Serviços como **IAM**, **Cognito**, **S3** e **CloudWatch** apresentaram falhas.  
  - Pipelines de CI/CD, APIs REST e workloads de produção ficaram paralisados.  
  - Segundo a AWS, medidas de mitigação foram aplicadas gradualmente até a recuperação total.

## Impacto global

A falha afetou **milhões de usuários** e **centenas de empresas**. Alguns exemplos:

- **Brasil**: iFood, Mercado Livre e PicPay ficaram fora do ar ou com instabilidade.  
- **Global**: Alexa, Zoom, Duolingo, Snapchat, Fortnite, Prime Video e Slack relataram falhas.  
- **Infraestrutura crítica**: pipelines de CI/CD, autenticação de usuários e integrações de APIs ficaram indisponíveis.  

Esse incidente reforça como a dependência de uma única região pode gerar **efeitos globais em cadeia**.

## Por que sempre a US-EAST-1?

A região **US-EAST-1 (N. Virginia)** é a mais antiga e utilizada da AWS. Isso significa:

- Hospeda **serviços core** da AWS.  
- É a **região default** para muitos SDKs e integrações.  
- Possui **dependências internas** com serviços globais.  
- É usada por milhares de clientes como **ponto de entrada**.  

Essa centralização transforma a US-EAST-1 em um **ponto de falha crítico**.

## Histórico de falhas na US-EAST-1

| Data     | Evento                        | Impacto                                                 |  
|----------|-------------------------------|---------------------------------------------------------|
| Out/2025 | Falha em DynamoDB e EC2 (DNS) | 15h de instabilidade global                             |
| Dez/2021 | Problema em rede interna      | EC2, S3, Lambda ficaram indisponíveis                    |
| Nov/2020 | Falha no Kinesis              | Afetou CloudWatch, Cognito e apps de streaming          |
| Jul/2018 | Interrupção no S3             | Sites e apps que usam S3 como backend ficaram fora do ar |

## Comparando crises: AWS vs. CrowdStrike

É interessante notar como o mercado reage de forma diferente a incidentes de grande escala, dependendo de **quem é o responsável** e de **como a crise é resolvida**.

- Em **julho de 2024**, a **CrowdStrike** sofreu uma falha global causada por uma atualização defeituosa.  
  - O resultado foi um verdadeiro colapso: **computadores travaram no mundo inteiro**, gerando a famosa tela azul no Windows.  
  - As ações da empresa despencaram mais de **20% em um único dia**, e sua reputação levou meses para se recuperar.  

- Já no caso da **AWS**, apesar do impacto massivo em serviços digitais, o mercado tratou o episódio de forma diferente:  
  - A interrupção foi dolorosa, mas **indireta** para a maioria dos usuários finais.  
  - A AWS é vista como **“too big to fail”** — grande demais para falhar.  
  - Investidores assumem que a Amazon sempre se recuperará, pois a economia global **depende de sua infraestrutura**.  

Esse contraste mostra um paradoxo da centralização digital: quanto mais uma empresa se torna **essencial para o funcionamento da sociedade**, mais **imune a consequências de mercado** ela parece ser.

## Como mitigar esse tipo de falha?

Incidentes como o da **US-EAST-1** mostram que não basta confiar apenas no provedor de nuvem. É preciso **arquitetar resiliência** desde o início. Algumas práticas recomendadas:

### Adote estratégias Multi-Region e Multi-Zone
- Nunca concentre todos os workloads em uma única região.  
- Distribua serviços críticos em **múltiplas zonas de disponibilidade** ou até em **provedores diferentes (multi-cloud)**.  
- Isso garante alta disponibilidade mesmo quando uma região inteira falha.

### Habilite sistemas de failover inteligentes
- Configure **Route 53** com health checks e políticas de failover.  
- Use **Global Accelerator** para redirecionar tráfego automaticamente.  
- Combine com **replicação de dados** (S3 Replication, RDS cross-region, DynamoDB Global Tables).  
- O objetivo é manter usuários conectados, mesmo quando uma região está fora do ar.

### Invista em monitoramento independente
- A AWS monitora sua própria infraestrutura, mas você deve monitorar a sua.  
- Use ferramentas como **Datadog**, **New Relic**, **Grafana** ou dashboards customizados.  
- Isso permite detectar falhas antes que os clientes percebam e agir proativamente.

### Teste regularmente seus planos de DR
- Conduza **simulações de desastre** e pratique **chaos engineering**.  
- Testes revelam pontos fracos antes que um incidente real os exponha.  
- Defina e valide **RTO (Recovery Time Objective)** e **RPO (Recovery Point Objective)** para cada workload.

### Comunique-se de forma transparente durante falhas
- Tenha um **playbook de incidentes** pronto, com responsáveis e canais de comunicação definidos.  
- Informe clientes e stakeholders com **atualizações rápidas e honestas**.  
- Transparência preserva a confiança, mesmo em momentos críticos.

## Links oficiais e úteis

- [AWS Health Dashboard](https://health.aws.amazon.com/health/status){:target="_blank"}  
- [Blog oficial da AWS](https://aws.amazon.com/blogs/aws/){:target="_blank"}  
- [What's New na AWS](https://aws.amazon.com/about-aws/whats-new/){:target="_blank"}  
- [Guia de Disaster Recovery na AWS](https://aws.amazon.com/architecture/disaster-recovery/){:target="_blank"}  
- [Amazon Route 53](https://aws.amazon.com/route53/){:target="_blank"}  
- [AWS Global Accelerator](https://aws.amazon.com/global-accelerator/){:target="_blank"}  

## Lições aprendidas

Esse incidente reforça que **mesmo os maiores provedores de nuvem não estão imunes a falhas**.  
A diferença entre **quedas prolongadas** e **continuidade de serviço** está nas **decisões arquiteturais** que tomamos:

- Projetar para **resiliência multi-region**.  
- Implementar **planos de DR testados**.  
- Garantir **observabilidade independente**.  
- Ter **playbooks de comunicação** claros para incidentes.  

Se você ainda depende exclusivamente de uma única região, é hora de repensar sua estratégia.  

A nuvem é poderosa, mas só entrega confiabilidade quando usada com boas práticas.

Compartilha esse post com seu time de infraestrutura, DevOps e arquitetura.  

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção abaixo!

Forte abraço!
