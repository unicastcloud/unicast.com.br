---
layout: post
title: "CAF: Pilar 3 - Pronto (Ready) (Microsoft)"
author: asilva
date: 2025-04-25 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, caf, waf, landingzone]
---

Fala galera! Seis tão baum?

Depois de falarmos sobre os pilares de **Estratégia** e **Planejamento**, chegou a hora de botar a mão na massa e preparar o terreno para a adoção de nuvem com o Azure. Seja bem-vindo ao **Pilar 3 - Pronto (Ready)**!

👉 Se você ainda não leu os artigos anteriores, dá uma passada por lá:
- [CAF: Pilar 1 - Estratégia](https://unicast.com.br/posts/caf-pilar-1-estrategia-microsoft/)
- [CAF: Pilar 2 - Planejamento](https://unicast.com.br/posts/caf-pilar-2-planejamento-microsoft/)

## **Pilar Pronto (Ready): preparando a fundação da nuvem**

O Pilar **Pronto** — ou **Ready** no original — tem como objetivo principal **preparar a fundação técnica e organizacional** necessária para adoção da nuvem de forma segura, escalável e alinhada às diretrizes da organização.

Aqui é onde você estrutura os ambientes, define padrões técnicos, cria práticas de segurança e governança e se prepara para começar a migrar ou construir workloads.

- Documentação oficial: [CAF - Ready](https://learn.microsoft.com/pt-br/azure/cloud-adoption-framework/ready/)

## **Etapas práticas para aplicar o Pilar Ready**

### 1. **Crie uma fundação bem definida**

Monte uma estrutura mínima com:

- Grupos de gerenciamento (Management Groups)
- Subscriptions separadas por ambiente (Prod, Dev, Test)
- Política de tags obrigatórias
- Naming conventions para recursos

Ferramenta útil: [CAF Ready Checklist](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/checklist/)

### 2. **Implemente uma Landing Zone**

A **Landing Zone** é um ambiente preparado com padrões de governança, segurança e conectividade. Você pode:

- Criar sua própria com Terraform ou Bicep
- Usar os Azure Verified Modules recomendados pela Microsoft
- Aproveitar arquiteturas como Enterprise-Scale ou Start Small, Expand

- [Design de Landing Zones](https://learn.microsoft.com/pt-br/azure/cloud-adoption-framework/ready/landing-zone/design-areas/)

### 3. **Configure práticas de segurança e identidade**

Aqui você já pode aplicar:

- Azure Policy e Blueprints
- Azure Key Vault para gestão de segredos
- Azure AD para autenticação e autorização
- Defender for Cloud para visibilidade de riscos

- [Microsoft Defender for Cloud - Overview](https://learn.microsoft.com/en-us/azure/defender-for-cloud/)

### 4. **Planeje conectividade e rede**

- Use Azure Virtual Network + Subnets bem definidas
- Configure DNS privado e regras de firewall
- Avalie uso de Azure Firewall, Bastion, NSG e Private Link

- Ferramenta útil: [CAF Network Topology Guide](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/network-topologies/)

## **Exemplo prático: Fundação robusta evita retrabalho**

Imagine que uma empresa começou migrando workloads sem configurar uma fundação mínima. Resultado?

- Nomes inconsistentes
- Falta de controle de acesso
- Custo desgovernado
- Rede bagunçada

Se ela tivesse aplicado o Pilar Ready, teria criado:

- Subscriptions organizadas por ambiente
- Políticas de acesso centralizadas no Azure AD
- Network isolada e segura
- Monitoramento desde o início

> Evitou refatoração, retrabalho e sustos com segurança

## **Conclusão**

O Pilar Pronto (Ready) é onde você **constrói a base da casa**. Sem ele, qualquer workload implantado estará sobre solo instável.

Nos próximos artigos, vamos explorar o Pilar **Adoção**, onde as cargas começam a ser migradas ou desenvolvidas na nuvem — com foco em entrega de valor real.

Compartilha aí com a galera da nuvem, e comenta o que achou do artigo!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
