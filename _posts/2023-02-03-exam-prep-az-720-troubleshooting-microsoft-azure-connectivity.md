---
layout: post
title: "[Exam-Prep] AZ-720 Troubleshooting Microsoft Azure Connectivity"
author: asilva
date: 2023-02-03 09:00:00 -0500
categories: [Certificação, Exam-Prep]
tags: [certificação, azure, microsoft, network, az720, troubleshooting]
---

Fala galera! Seis tão baum?

Matei a saudade de fazer mais um exame de certificação da Microsoft e como é de costume, vou compartilhar um pouco da minha experiencia com vocês.

E o desafio da vez foi a certificação: **Certified: Azure Support Engineer for Connectivity Specialty**, que para obtê-la, você precisa passar no exame: **AZ-720: Troubleshooting Microsoft Azure Connectivity.**

Quando a Microsoft anunciou este exame eu fiquei muito empolgado, como muitos sabem, trabalhei boa parte da minha vida com redes e sou apaixonado pelo tema, logo, estava muito animado em fazer este exame. 

O exame de certificação **AZ-720: Troubleshooting Microsoft Azure Connectivity** é um exame de certificação de nível avançado que avalia as habilidades e conhecimentos dos profissionais em solucionar problemas de conectividade em ambientes Microsoft Azure. O exame testa a capacidade dos profissionais em identificar e solucionar problemas de conectividade comuns, como configurações de rede, configurações de segurança, problemas de performance e questões relacionadas ao transporte de dados. Além disso, o exame também avalia a compreensão dos profissionais sobre a implantação e o gerenciamento de soluções de conectividade em grandes ambientes Azure. Obter a certificação AZ-720 é uma prova da capacidade dos profissionais em solucionar problemas de conectividade de forma eficiente e eficaz, e pode ser uma vantagem significativa para sua carreira.

### **Um pouco de contexto**

**Troubleshooting** é o processo de identificar e resolver problemas ou falhas em sistemas, equipamentos ou aplicativos. É uma habilidade crítica para profissionais de TI e é aplicada ao longo de todo o ciclo de vida de uma solução, desde a implantação até a manutenção.

O processo de Troubleshooting pode incluir a análise de logs, a realização de testes de diagnóstico, a resolução de problemas de configuração e a correção de problemas de software ou hardware. O objetivo final é identificar e corrigir a fonte do problema e restaurar o funcionamento normal da solução o mais rapidamente possível.

Imagine que um usuário esteja tendo problemas para acessar recursos em uma rede Azure a partir de uma rede on-premises via VPN site-to-site. O processo de Troubleshooting poderia incluir os seguintes passos:

- **Verificação de logs:** Verificar os logs da VPN site-to-site para identificar possíveis erros ou problemas.
- **Testes de diagnóstico:** Realizar testes de ping e traceroute entre as redes para identificar possíveis problemas de rede.
- **Verificação de configuração:** Verificar a configuração da VPN site-to-site, incluindo as configurações de segurança, para garantir que estão corretas e configuradas -corretamente.
- **Verificação de recursos:** Verificar se o acesso aos recursos em questão está permitido na rede Azure e se os recursos em questão estão ativos e acessíveis.
- **Resolução de problemas:** Corrigir qualquer problema identificado, incluindo a configuração da VPN site-to-site, a configuração de segurança e a disponibilidade de -recursos.
- **Verificação de resolução:** Verificar se o acesso aos recursos em questão foi restaurado após a resolução do problema.

Este é apenas um exemplo de Troubleshooting em uma VPN site-to-site entre uma rede on-premises e uma rede Azure. 

O processo de Troubleshooting pode variar dependendo da solução específica e do tipo de problema enfrentado. No entanto, a abordagem geral é a mesma: identificar e corrigir a fonte do problema para restaurar o funcionamento normal da solução o mais rapidamente possível.

Bom, por hora isso que importa, logo eu vou trazer um artigo somente sobre este tema! Voltando ao exame...

### **Os temas cobrados no exame são:**

- Troubleshoot business continuity issues (5–10%)
- Troubleshoot hybrid and cloud connectivity issues (20–25%)
- Troubleshoot Platform as a Service issues (5–10%)
- Troubleshoot authentication and access control issues (15–20%)
- Troubleshoot networks (25–30%)
- Troubleshoot VM connectivity issues (5–10%)

Segue o link do conteúdo completo que é cobrado na prova: <a href="https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWVJHf" target="_blank">Skills Measured.</a> 

### **Material de estudo**

Por se tratar de um exame relativamente novo, temos poucos materiais de estudo. Minha dica é focar no Learning Path Oficial da Microsoft.

São mais de 6h de conteúdo, e na minha visão, é o suficiente para lhe preparar para o exame.

<a href="https://learn.microsoft.com/en-us/training/paths/azure-support-engineer-for-connectivity-specialty/" target="_blank"> Learning Path: Azure Support Engineer for Connectivity Specialty</a>

Lembrando que este exame é indicado para o profissional que já tem uma vivência com ambientes Azure, ou já tenha prestado o exame **AZ-700**.

Estamos falando de um exame de resolução de problemas e não de implantação de recursos. 

Resolução de problemas não é algo que aprendemos em livros, é prática e dia a dia.

É possível um candidato que ainda não trabalha com Azure ser aprovado no exame? Sim, sem dúvidas!  

Porém, o domínio de um bom **Troubleshoot** vem dos perrengues que a gente passa no dia a dia, não tem como.

### **Sobre o exame**

Bom, particularmente eu gostei muito do exame. O lance de ter uma prova focada em resolução de problemas é muito show!

Eu creio que ela irá ajudar muitos os profissionais que trabalham com ambientes Azure, principalmente na medida que ela for recebendo updates. Saber realizar um bom troubleshooting é essencial na carreira do profissional de TI.

Mas como já mencionei no início do artigo, este não é um exame para faze inicial e sim para aqueles que já tenham alguma experiência com ambientes Azure. 

Sem dúvidas que você pode simular vários cenários em seu ambiente de testes, mas não será o suficiente. Um exemplo: na minha prova caiu várias perguntas sobre ExpressRoute, e sem o dia a dia, sem a prática, não teria como responder certas questões.

Uma conexão ExpressRoute não é algo que você pode simular em um ambiente de testes, você vai precisar trabalhar em algum ambiente que tenho o recurso.

Outro ponto importante são os comandos em CLI (Command Line Interface) seja em AzCLI ou Powershell, não é muito comum dos profissionais mais novos utilizarem comandos em CLI para administrar recursos no Azure, porém, o exame cobre bastante este tema.

Minha prova contou com 51 questões e um estudo de caso, me surpreendi com a quantidade de questões no estudo de caso, de longe, este é o maior que já respondi, foram 13 questões.

Eu particularmente gosto muito mais do estudo de caso a perguntas abertas. No estudo de caso você tem um começo, meio e fim, e por se tratar de uma prova de Troubleshooting isso é perfeito, pois você tem contexto para trabalhar nas questões.

Mas como nem tudo são flores, como você tem bastante material para ler e interpretar o tempo joga contra!

Sim, talvez este seja o maior ponto de atenção para quem deseja prestar o exame, se você não administrar bem o tempo, vai ficar sem responder questões.

Sinceramente eu não me lembro o tempo de duração da minha prova, mas quando fui para as questões abertas, vi que estava faltando 54 minutos para o fim do exame!

E como eu sei que não demorei tanto no estudo de caso, a prova não tem muito tempo! Fique ligado!

De resto, nada para se assustar. De atenção, somente os pontos que mencionei acima e claro, siga neste exame depois de já ter uma boa base no Azure.

>Uma sugestão é: AZ-900 > AZ-104 > AZ-700 e aí sim AZ-720.
{: .prompt-tip }

Outra dica é ter uma boa base de redes, conhecer um pouco de Modelo OSI, Firewall, ACLs, Load Balancer, DNS e VPNs. 

Ou seja, este exame une teoria e prática, você irá precisar dos dois para vencer essa batalha!

É isso galera, espero que gostem do material e que ele seja útil nos seus estudos.

Forte abraço a todos e boa prova!