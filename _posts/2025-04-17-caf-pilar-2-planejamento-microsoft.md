---
layout: post
title: "CAF: Pilar 2 - Planejamento (Microsoft)"
author: asilva
date: 2025-04-17 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, caf, waf, landingzone]
---

Fala galera! Seis tão baum?

No artigo anterior falamos sobre o **Pilar Estratégia** do CAF — onde definimos as motivações e objetivos da jornada para a nuvem. Se você ainda não conferiu, dá uma olhadinha aqui:  

[CAF: Pilar 1 - Estratégia](https://unicast.com.br/posts/caf-pilar-1-estrategia-microsoft/){:target="_blank"}

Agora vamos para o **Pilar 2 - Planejamento**, onde a estratégia começa a tomar forma e se transforma num plano de ação estruturado, com times, responsabilidades e governança.

## **Pilar Planejamento: transformando visão em execução**

O Pilar **Planejamento** do Cloud Adoption Framework ajuda a sua organização a **organizar seus esforços**, conectar times e atribuir responsabilidades para a jornada de adoção da nuvem.

Esse momento é essencial para alinhar expectativas, identificar lacunas de conhecimento e garantir que todos estejam **na mesma página** antes de começar a colocar a mão na massa.

- Fonte oficial: [CAF - Planejamento](https://learn.microsoft.com/pt-br/azure/cloud-adoption-framework/plan/){:target="_blank"}

## **Etapas práticas para aplicar o Pilar Planejamento**

### 1. **Defina as funções e estruturas organizacionais**

Mapeie os principais papéis envolvidos no projeto:

| Função/Equipe                         | Responsabilidade Principal                           |
|---------------------------------------|------------------------------------------------------|
| **Cloud Strategy Team**               | Alinha estratégia com liderança de negócios          |
| **Cloud Governance Team**             | Cria políticas e padrões de conformidade             |
| **Cloud Center of Excellence (CCoE)** | Lidera implementação técnica, dissemina conhecimento |
| **Equipes de produto/aplicação**      | Migrar e evoluir workloads                           |
| **Suporte & Operações**               | Sustentar ambiente pós-migração                      |

- Recurso útil: [Cloud Operating Model](https://learn.microsoft.com/pt-br/azure/cloud-adoption-framework/plan/cloud-operating-model/){:target="_blank"}

### 2. **Identifique lacunas de conhecimento**

Use uma avaliação de prontidão técnica e organizacional. Exemplos de perguntas:

- Os times de TI conhecem os principais serviços da nuvem?
- Existe equipe de segurança familiarizada com Azure Policy ou Defender?
- Há ferramentas de automação e IaC em uso (ex: Terraform, Bicep)?

- Ferramenta útil: [Cloud Skills Readiness Plan](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/plan/skills-readiness-plan/){:target="_blank"}

### 3. **Crie um plano de adoção inicial**

Organize os primeiros passos:

- Defina a **linha do tempo** e marcos do projeto
- Identifique os workloads candidatos
- Planeje migração ou construção cloud-native
- Inclua indicadores de sucesso (KPI)

- [Migration Readiness Checklist](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/plan/migration-considerations/){:target="_blank"}

### 4. **Estabeleça práticas de governança**

Antes de migrar qualquer recurso, alinhe:

- Naming conventions
- Políticas de acesso e segurança
- Estrutura de grupos de gerenciamento e subscription
- Tags obrigatórias e controle de custos

- Ferramenta útil: [CAF Governance Benchmark](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/plan/governance-benchmark/){:target="_blank"}

## **Exemplo prático: Planejamento que evita dores de cabeça**

Imagine uma empresa que já definiu sua estratégia para a nuvem, mas começa a migrar workloads sem uma governança mínima.

**Resultado**: subscriptions bagunçadas, recursos sem tags, contas duplicadas, e custos fora de controle.

Ao aplicar o Pilar Planejamento corretamente, essa mesma empresa teria:

- Criado uma estrutura de grupos de gerenciamento
- Organizado subscriptions por ambiente (Prod, Dev, Test)
- Definido políticas com Azure Policy
- Planejado seus custos e ciclos de vida com tags

A diferença? Economia, rastreabilidade e controle total.

## **Conclusão**

O Pilar Planejamento é o elo entre **sonho e entrega**. Aqui, as intenções estratégicas viram planos concretos, com pessoas, times e padrões bem definidos.

Nos próximos artigos da série, vamos abordar o Pilar **Pronto (Ready)**, onde começamos a construir a base técnica e organizacional para receber workloads com segurança e governança.

Compartilha aí com a galera da nuvem, e comenta o que achou do artigo!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
