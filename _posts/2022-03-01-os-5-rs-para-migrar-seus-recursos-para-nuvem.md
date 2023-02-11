---
layout: post
title: "Os 5 R’S para migrar seus recursos para nuvem"
author: asilva
date: 2022-03-01 09:00:00 -0500
categories: [Azure, Frameworks]
tags: [arquitetura, azure, microsoft, caf, 5rs, migrate, rehost, refactor, rearchitect, rebuild, replace ]
---

Fala galera! Seis tão baum?

Transformação digital, talvez essa seja a frase mais comentada no mundo dos negócios. Não é um assunto novo, mas com o advento da pandemia a transformação digital não só chegou como estamos vivendo intensamente este momento.

O mundo já se acostumou com as facilidades que a tecnologia proporciona. E agora, empresas e profissionais de TI tem grandes desafios para atingir o nível de excelência que os consumidores esperam do mundo digital.

A computação em nuvem demonstrou ser uma ferramenta eficaz para lidar com uma variedade de problemas imprevisíveis. <a href="https://www.gartner.com/en/newsroom/press-releases/2021-04-21-gartner-forecasts-worldwide-public-cloud-end-user-spending-to-grow-23-percent-in-2021" target="_blank"> De acordo com a previsão do Gartner</a>, em 2021, os gastos globais em nuvem pública chegarão a US$ 332,3 bilhões, aumentando 23,1% em relação a 2020. Em parte, a causa desse crescimento são as restrições e desafios causados ​​pela pandemia de COVID-19.

Hoje, migrar para a nuvem não é mais uma opção, é uma obrigação. Empresas que não tem capacidade de escalar rapidamente ou que não possuem alta disponibilidade, definitivamente estão fora do jogo.

Há muitas razões pelas quais as empresas decidem migrar sua infraestrutura de TI para a nuvem. Poderia ficar horas escrevendo sobre isso, mas o artigo de hoje não tem essa intenção.

Migar uma infraestrutura local para a nuvem é um grande desafio, requer muito trabalho, planejamento e não se limita a uma equipe de TI. Desta forma, é importante garantir que o processo de migração para a nuvem não seja considerado apenas um projeto de TI, vai muito além disso e requer atenção de todos os stakeholders.

Cada empresa tem seus requisitos de negócios, portanto, seguirá uma estratégia diferente de migração. 

É muito importante entender o ambiente que será migrado, conhecer as interdependências, mapear o que será fácil e difícil de migrar, e qual será a estratégia ou ferramenta utilizada para migração destes recursos.

Uma abordagem adequada na hora de migrar cada um de seus recursos e em que ordem os fazer, irá minimizar os riscos envolvidos nessa jornada.

***Tenha sempre em mente:***

> Tempo, Esforço, Custo = **Riscos de Migração**
{: .prompt-tip }

### **Estratégias de migração para nuvem**

Antes de iniciar uma migração, você deve ter algumas perguntas em mente:

* Por que estou migrando meu ambiente para a nuvem?
* Quais recursos de minha infraestrutura vou migrar?
* Este recurso será modernizado?
* Como eu planejo migrá-los?

Essas e outras perguntas, podem ajudar no mapeamento das necessidades e auxiliar na criação indicadores de desempenho (KPIs) facilitando a medição do sucesso da migração para a nuvem.

É muito importante que o motivo da migração seja bem definido, bem como os resultados esperados.

### **Estratégia 5 R’S para migrar recursos para nuvem**

Vamos entender a estratégia 5R para migrar workloads para a nuvem da Microsoft. Mas antes, vale lembrar que o modelo utilizado pela Microsoft é um pouco diferente do original 5R’s da Gartner. E isso também acontece com o modelo de outros provedores em nuvem como AWS e GCP.

### **Rehost**

Também conhecido como migração lift-and-shift, é o processo em mover seus workloads para nuvem sem modificações. Essa estratégia envolve menor esforço e risco de migração, e é um dos modelos mais utilizados.

### **Refactor**

No modelo da Microsoft, o refactor é conhecido por ajustar um workload para o modelo baseado em PaaS (plataforma como serviço) deixando de lado o modelo convencional IaaS (infraestrutura como serviço). Nessa estratégia, poucas otimizações são feitas antes de migrar para a nuvem.

### **Rearchitect**

O Rearchitect presente no modelo da Microsoft se concentra na modificação e na extensão da funcionalidade do aplicativo e na base de código para otimizar a arquitetura do aplicativo para a escalabilidade da nuvem. Pode ser uma aplicação monolítica migrando para micro serviços ou levar banco de dados relacionais e não relacionais para uma solução de banco de dados totalmente gerenciada.

### **Rebuild**

A abordagem de rebuild leva a modificação do código-fonte e significa reescrever o aplicativo do zero. Essa decisão é tomada quando as soluções atuais não atendem às necessidades do negócio ou quando um aplicativo legado não pode ser executado em nuvem. Reconstruir aplicativos geralmente requer grandes investimentos de tempo e dinheiro.

### **Replace**

Às vezes, aplicativos SaaS (software como serviço) podem fornecer toda a funcionalidade necessária para o workload a ser migrado. Nesses casos, pode ser mais interessante substitui-lo por completo e eliminar o esforço de rearchitect e rebuild da aplicação. Essa abordagem requer pouco esforço, uma vez que só é necessária a carga de dados no novo workload.

![](/assets/img/18/5r1.png){: "width=60%" }

Embora a migração para a nuvem traga muitos benefícios, o processo em si é desafiador. Ao olhar para o gráfico acima, podemos concluir que planejamento e estratégia são as bases para uma migração de sucesso.

É isso galera, espero que gostem.

Forte abraço.