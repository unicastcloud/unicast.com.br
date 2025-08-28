---
layout: post
title: "Kubernetes v1.34 – A release que entrega sem quebrar?"
author: asilva
date: 2025-08-28 16:00:00 -0300
categories: [DevOps, Kubernetes]
tags: [kubernetes, release, v1.34, observabilidade, dra, devops]
---

Fala galera! Seis tão baum?

A nova versão do Kubernetes chegou — **v1.34 – Of Wind & Will** — e ela vem diferente. Nada de sustos, nada de correria pra ajustar API quebrada. Essa é uma daquelas releases que você pode atualizar com confiança, porque tudo nela foi pensado pra **melhorar sem atrapalhar**. Será mesmo? Bora ver juntos! 

São **58 melhorias** no total:

- 23 recursos promovidos para **Stable**
- 22 em **Beta**
- 13 em **Alpha**
- E o melhor: **zero depreciações**

- [Release oficial no blog do Kubernetes](https://kubernetes.io/blog/2025/08/27/kubernetes-v1-34-release/){:target="_blank"}  
- [Changelog completo no GitHub](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.34.md){:target="_blank"}

Bom, sem mais delongas, bora falar das principais novidades!

## DRA: alocação dinâmica de recursos agora é produção

O **Dynamic Resource Allocation (DRA)** finalmente chegou ao **GA**. Se você já sofreu pra alocar GPU, FPGA ou qualquer hardware especial, o DRA vai ser seu novo melhor amigo.

### Por que isso importa?

- Alocação **flexível e declarativa**  
- Visibilidade real sobre uso e falhas  
- Fim da dependência de device plugins engessados  

### Como funciona?

Novas APIs sob `resource.k8s.io/v1`:

- `ResourceClaim`
- `DeviceClass`
- `ResourceClaimTemplate`
- `ResourceSlice`

Exemplo: você pode pedir uma GPU com 16GB e compute capability > 7.0 usando **CEL expressions**. É granular, inteligente e **pronto pra produção**.

## Observabilidade de verdade com tracing nativo

Dois recursos que mudam o jogo chegaram ao **Stable**:

- **API Server Tracing (KEP-647)**
- **Kubelet Tracing (KEP-2831)**

Instrumentados com **OpenTelemetry**, eles trazem:

- Tracing de ponta a ponta: admission → etcd → kubelet  
- Propagação de contexto com Trace IDs  
- Visão unificada de operações  

### Exemplo prático:

Um pod travado em `ContainerCreating`? Agora você acompanha toda a linha do tempo, identifica o gargalo e correlaciona com o runtime. Sem grep, sem achismo.

## Segurança: tokens rotativos para image pull

O kubelet agora usa **ServiceAccount tokens projetados** para autenticar em registries privados. Em **Beta e ativado por padrão**, esse recurso substitui os secrets estáticos.

### Benefícios:

- Rotação automática  
- Escopo por workload  
- Redução da superfície de ataque  
- Alinhamento com práticas **zero-trust**

## Escalabilidade e controle refinados

### HPA com tolerância configurável (KEP-4951)

Agora você pode definir tolerância por workload:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  behavior:
    scaleUp:
      tolerance: 0.05
    scaleDown:
      tolerance: 0.15
```

Isso resolve um problema antigo: o modelo fixo de 10% para todo mundo.

- Em workloads grandes → resultava em centenas de pods sobrando.
- Em workloads sensíveis → deixava o sistema lento pra reagir.

### Agora você pode:

- Otimizar uso de recursos
- Reduzir custos com scale-down mais agressivo
- Melhorar performance com scale-up mais responsivo
- Adaptar o comportamento por tipo de aplicação

Uma mudança pequena no YAML, mas enorme na operação.

##  PreferSameZone e PreferSameNode

A antiga `PreferClose` foi deprecada. Agora temos:

- `PreferSameZone`: prioriza endpoints na mesma zona
- `PreferSameNode`: prioriza endpoints no mesmo nó

### Ideal pra:

- Edge computing
- Microserviços co-localizados
- Workloads de alta largura de banda

```yaml
apiVersion: v1
kind: Service
spec:
  trafficDistribution: PreferSameNode
```

## Alpha features que merecem atenção

### Pod Replacement Policy (KEP-3973)

Mais controle no rollout de pods:

- `TerminationStarted`: cria novos pods assim que os antigos começam a encerrar
- `TerminationComplete`: espera terminar pra criar novos

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  podReplacementPolicy: TerminationStarted
```

Essencial em ambientes com recursos limitados ou shutdowns demorados.

### KYAML: o YAML que não te sabota

Essa promete ser polêmica. O YAML já nos sabotou com indentação, o JSON é rígido demais, e o KYAML tenta unir o melhor dos dois mundos.

O que ele promete:

- Strings sempre com aspas
- Suporte a comentários
- Sintaxe consistente
- Sem bugs tipo `“NO = false”`

```yaml
apiVersion: "v1"
kind: "ConfigMap"
data: {
  country: "NO",
  version: "1.0"
}
```

As diferenças são sutis, mas importantes. Menos erro bobo, menos surpresa.

Use com: 

```bash
kubectl get -o kyaml
```

## Outras melhorias operacionais

- Resize de memória com política NotRequired, evitando OOM-kill
- CSI volumes com falha de montagem agora são detectados e pods são recriados
- Novas métricas para tracing de pods e operações do ResourceClaim controller

## O que isso significa pra quem opera

- **Platform Engineers**: DRA, tracing e políticas refinadas estão prontos pra virar abstração de plataforma. Sem medo de churn.
- **Security Teams**: Hora de migrar pra tokens rotativos e aposentar secrets long-lived.
- **FinOps**: HPA granular + políticas de rollout = controle real de custo vs. performance.
- **Devs**: KYAML traz mais segurança pra configs e tracing nativo acelera o debug.

## Conclusão

O Kubernetes v1.34 não é uma revolução — é uma evolução sólida. Sem quebra, sem susto, só entrega. E isso, pra quem vive produção, vale ouro.

Se quiser trocar ideia sobre essa release ou compartilhar como está usando os novos recursos, comenta aí!

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção abaixo!

Forte abraço!
