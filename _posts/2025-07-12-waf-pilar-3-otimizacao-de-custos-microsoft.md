---
layout: post
title: "WAF: Pilar 3 - Otimização de Custos (Microsoft)"
author: asilva
date: 2025-07-12 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, waf, custos, finops, arquitetura]
---

Fala galera! Seis tão baum?

Seguimos firme na nossa série sobre o **Well-Architected Framework (WAF)** da Microsoft, e hoje vamos falar sobre um dos pilares mais populares — e muitas vezes mais negligenciados: **Otimização de Custos**.

Se você ainda não viu os artigos anteriores, dá uma olhada:

- [WAF: O que é o Well-Architected Framework da Microsoft](https://unicast.com.br/posts/waf-o-que-e-o-well-architected-framework-da-microsoft/){:target="_blank"}
- [WAF: Pilar 1 - Confiabilidade](https://unicast.com.br/posts/waf-pilar-1-confiabilidade/){:target="_blank"}
- [WAF: Pilar 2 - Segurança](https://unicast.com.br/posts/waf-pilar-2-seguranca/){:target="_blank"}

## **Pilar Otimização de Custos: mais valor, menos desperdício**

O objetivo aqui não é simplesmente “gastar menos”, mas sim **gastar melhor**. A ideia é maximizar o valor entregue por cada workload, equilibrando desempenho, confiabilidade e segurança com o orçamento disponível.

- Documentação oficial: [Cost Optimization design principles - Microsoft Learn](https://learn.microsoft.com/en-us/azure/well-architected/cost-optimization/principles){:target="_blank"}  
- Avaliação oficial: [Well-Architected Cost Optimization Assessment](https://techcommunity.microsoft.com/blog/coreinfrastructureandsecurityblog/customer-offerings-well-architected-cost-optimization-assessment/3680045){:target="_blank"} 

## **Práticas recomendadas para otimização de custos no Azure**

### 1. **Crie uma cultura de FinOps**

- Envolva times de tecnologia, finanças e produto
- Use tags para rastrear gastos por equipe ou projeto
- Estabeleça ownership sobre recursos e orçamentos

> Dica: Cada time deve saber quanto consome e como pode melhorar — isso muda o jogo.

### 2. **Monitore e analise continuamente**

- Use Azure Cost Management + Billing para visualizar gastos
- Configure alertas e orçamentos por subscription ou grupo
- Avalie tendências e identifique picos inesperados

- Ferramenta útil: [Azure Cost Management](https://learn.microsoft.com/pt-br/azure/cost-management-billing/){:target="_blank"}

### 3. **Aproveite descontos e benefícios**

- Use **Azure Reservations** para workloads previsíveis
- Ative o **Azure Hybrid Benefit** para licenças existentes
- Avalie uso de **Spot VMs** para cargas não críticas

- [Azure Reservations](https://learn.microsoft.com/pt-br/azure/cost-management-billing/reservations/){:target="_blank"}  
- [Azure Hybrid Benefit](https://learn.microsoft.com/pt-br/azure/virtual-machines/windows/hybrid-use-benefit-licensing/){:target="_blank"}

### 4. **Dimensione corretamente seus recursos**

- Evite superprovisionamento (VMs grandes demais, storage ocioso)
- Use escalabilidade automática e desligamento fora do horário comercial
- Crie ambientes sob demanda e destrua quando não forem mais usados

> Dica: Nem todo ambiente precisa simular produção — economize em Dev/Test.

### 5. **Otimize por uso e por tarifa**

| Estratégia               | Benefício                                  |
|--------------------------|--------------------------------------------|
| **Uso sob demanda**      | Flexibilidade, ideal para cargas variáveis |
| **Reservas e planos**    | Economia para cargas previsíveis           |
| **SKUs adequados**       | Evita pagar por recursos que não usa       |
| **Ambientes temporários**| Reduz custo em testes e homologações       |

## **Exemplo prático: Cortando custos sem cortar valor**

Uma empresa rodava 20 VMs em tamanho Standard_D4s_v3 para ambientes de homologação que ficavam ligados 24/7. Ao aplicar o Pilar de Otimização de Custos, ela:

- Redimensionou para D2s_v3
- Ativou desligamento automático fora do horário comercial
- Usou Azure Reservations para produção
- Aplicou tags e alertas de orçamento

> Resultado: economia de mais de 40% sem impacto na operação

## **Conclusão**

O Pilar Otimização de Custos não é sobre “economizar por economizar” — é sobre **gastar com inteligência**, com visibilidade e com responsabilidade.

No próximo artigo da série, vamos explorar o Pilar **Excelência Operacional**, com foco em automação, monitoramento e melhoria contínua.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte abraço!  
