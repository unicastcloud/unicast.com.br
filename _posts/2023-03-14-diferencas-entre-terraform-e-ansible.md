---
layout: post
title: "Diferenças entre Terraform e Ansible"
author: asilva
date: 2023-03-14 09:20 -0300
categories: [DevOps, Automação de TI]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure, ansible, redhat, chef, puppet, cloudformation, bicep, saltstack]
---

Fala galera! Seis tão baum?

Se você está acompanhando nossas postagens recentes, deve ter conferido nosso último artigo que explicava as diferenças entre **Infrastructure as Code (IaC) e Configuration Management (CM)**. Se ainda não leu, não se preocupe, deixaremos o link no final deste post para você conferir. 

Hoje, daremos continuidade a esse tema, mas agora vamos focar em duas ferramentas muito populares na comunidade de DevOps: **Terraform** e **Ansible**. Veremos as semelhanças e diferenças entre elas e entenderemos qual é a melhor escolha para cada tipo de ambiente. 

![](/assets/img/65/tfeansible01.png){: "width=60%" }

## **O que é o Terraform?**

**Terraform** é uma ferramenta de gerenciamento de infraestrutura que permite criar, modificar e gerenciar recursos de infraestrutura em diferentes provedores de nuvem, como **AWS**, **Google Cloud**, **Azure**, entre outros. Ele utiliza uma linguagem de configuração declarativa que permite descrever a infraestrutura desejada e, em seguida, cria essa infraestrutura com base nas especificações fornecidas. Além disso, o Terraform permite gerenciar a infraestrutura como código, o que significa que as configurações podem ser armazenadas em repositórios de controle de versão, como o Git.

**Site Oficial:** <a href="https://www.terraform.io/" target="_blank">https://www.terraform.io/</a>

## **O que é o Ansible?**

**Ansible** é uma ferramenta de gerenciamento de configuração que permite automatizar o provisionamento e configuração de recursos de infraestrutura. Ele é uma solução de automação open-source que usa uma abordagem baseada em agentless, ou seja, não é necessário instalar nenhum agente em cada servidor gerenciado. Ansible usa uma linguagem de configuração procedural, que permite descrever como um recurso deve ser criado ou configurado. O Ansible permite também gerenciar a infraestrutura como código e possui uma grande biblioteca de módulos que pode ser utilizada para gerenciar diversos tipos de recursos de infraestrutura.

**Site Oficial:** <a href="https://www.ansible.com/" target="_blank">https://www.ansible.com/</a>

## **Similaridades entre Terraform e Ansible**

Tanto o **Terraform** quanto o **Ansible** têm como objetivo automatizar o provisionamento e configuração de recursos de infraestrutura. Ambos permitem gerenciar a infraestrutura como código e armazenar as configurações em um repositório de controle de versão. Além disso, tanto o Terraform quanto o Ansible são ferramentas altamente escaláveis e podem ser usados para gerenciar grandes ambientes de infraestrutura.

## **Diferença entre Terraform vs. Ansible**

Embora ambas sejam usadas para implementar o conceito de Infrastructure as Code (IaC), elas têm abordagens diferentes e se concentram em aspectos distintos da automação de infraestrutura.

![](/assets/img/65/tfeansible02.png){: "width=60%" }

## **Orquestração vs. Gerenciamento de Configuração**

Uma das principais diferenças entre o **Terraform** e o **Ansible** é o seu foco. 

- O **Terraform** é projetado para orquestrar recursos de infraestrutura em vários provedores de nuvem, como Amazon Web Services, Google Cloud Platform e Microsoft Azure. Ele usa uma linguagem de modelagem simples para definir recursos, como servidores, redes e bancos de dados, e gerencia as dependências entre eles. 
- Já o **Ansible** é projetado para gerenciar a configuração do software instalado em cada um desses recursos. Ele executa "playbooks" para instalar pacotes, configurar arquivos de configuração e executar outras tarefas de gerenciamento de configuração em servidores individuais.

## **Declarativo vs. Processual**

Outra diferença importante entre **Terraform** e **Ansible** é a linguagem usada para definir as configurações. 

- O **Terraform** usa uma linguagem declarativa para definir a infraestrutura como código, o que significa que o usuário especifica o que deseja que seja criado e o Terraform se encarrega de criar os recursos necessários para atender a essa especificação. 
- Já o **Ansible** usa uma linguagem processual, em que o usuário especifica uma lista de tarefas que devem ser executadas para configurar um recurso de infraestrutura.

## **Gestão do Estado**

O **Terraform** e o **Ansible** também diferem em como gerenciam o estado da infraestrutura. 

- O **Terraform** armazena o estado atual da infraestrutura em um arquivo local e usa esse arquivo para determinar as alterações necessárias para atender às especificações de configuração. 
- O **Ansible**, por outro lado, não possui um mecanismo de gerenciamento de estado incorporado e depende da execução dos playbooks para garantir que a infraestrutura esteja em conformidade com as especificações de configuração.

## **Mutável vs. Imutável**

Outra diferença importante entre **Terraform** e **Ansible** é a abordagem de gerenciamento de estado. 

No contexto de gerenciamento de infraestrutura de TI, **mutável** se refere a uma abordagem em que os recursos são atualizados em seu estado existente, enquanto **imutável** se refere a uma abordagem em que os recursos são substituídos por novas instâncias com uma configuração atualizada. 

Na abordagem **mutável**, o estado do recurso é modificado para atender à nova configuração, enquanto na abordagem **imutável**, a configuração atualizada é aplicada a um novo recurso criado a partir de uma configuração conhecida.

Como a força do **Terraform** está em lidar com o **ciclo de vida da infraestrutura**, ele suporta melhor a imutabilidade da infraestrutura. É mais fácil provisionar um conjunto completamente novo de infraestrutura e desprovisionar o conjunto mais antigo usando o Terraform. No entanto, lidar com alterações de configuração não é algo que pode ser feito da maneira mais eficiente.

No que diz respeito às alterações de configuração, o **Ansible** vence a corrida, pois é principalmente uma ferramenta de gerenciamento de configuração. O Ansible também oferece suporte à imutabilidade da infraestrutura, oferecendo a criação de imagens de VM. No entanto, manter essas imagens adicionais requer esforços adicionais.

## **Conclusão**

Embora o **Terraform** e o **Ansible** possuam funcionalidades distintas, eles podem ser usados em conjunto para criar uma solução completa de automação de infraestrutura. O Terraform é ideal para gerenciar o ciclo de vida dos recursos de infraestrutura, enquanto o Ansible é ideal para garantir a conformidade de configurações e automatizar tarefas em servidores. 

Ao decidir qual ferramenta usar, é importante considerar as necessidades específicas do seu negócio e escolher a solução que melhor se adapte a elas.

Leia também: <a href="https://unicast.com.br/posts/diferencas-entre-infrastructure-as-code-iac-e-configuration-management-cm/" target="_blank">Diferença entre Infrastructure as Code (IaC) e Configuration Management (CM)</a>

É isso galera, espero que gostem!

Forte Abraço!
