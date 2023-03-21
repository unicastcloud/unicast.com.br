---
layout: post
title: "Diferença entre Infrastructure as Code (IaC) e Configuration Management (CM)"
author: asilva
date: 2023-03-07 09:20 -0300
categories: [DevOps, Automação de TI]
tags: [devops, terraform, hashicorp, automacao, iac, cicd, azure, ansible, redhat, chef, puppet, cloudformation, bicep, saltstack]
---

Fala galera! Seis tão baum?

Duas abordagens comuns de automação de TI são **Infrastructure as Code (IaC)** e **Configuration Management (CM)**. Neste artigo, exploraremos a diferença entre **IaC** e **CM** e como cada abordagem é usada na prática.

![](/assets/img/62/iac01.png){: "width=60%" }

### **Por que automatizar?**

Automação de TI tem sido uma ferramenta valiosa para empresas de todos os tamanhos há muitos anos. Com a crescente complexidade da infraestrutura de TI e a necessidade de eficiência e agilidade, a automação tornou-se ainda mais importante. 

Automatizar processos de TI pode economizar tempo, reduzir erros e aumentar a consistência.

**Benefícios e Importância da Automação de TI**

Antes de mergulharmos na diferença entre **IaC** e **CM**, é importante entender por que a automação de TI é tão importante. 

- **Redução de Erros:** Quando as tarefas de TI são executadas manualmente, há uma chance maior de erro humano. A automação pode reduzir esses erros e aumentar a precisão.
- **Consistência**: Quando as tarefas são executadas manualmente, pode haver variação nos resultados. A automação pode garantir que as mesmas tarefas sejam executadas da mesma forma toda vez.
- **Tempo**: A automação pode executar tarefas muito mais rapidamente do que os humanos, permitindo que as equipes de TI se concentrem em atividades mais estratégicas.
- **Escalabilidade**: A automação pode ajudar a equipe de TI a lidar com cargas de trabalho maiores e mais complexas.

Agora, vamos examinar a diferença entre **IaC** e **CM**.

### **O que é Infrastructure as Code (IaC)**

**Infrastructure as Code (IaC)** é uma abordagem para gerenciar e provisionar infraestrutura de TI usando código. Com **IaC**, a infraestrutura é tratada como se fosse software, com todo o código-fonte armazenado em um repositório de controle de versão. Quando a infraestrutura precisa ser provisionada ou alterada, um script é executado para fazer as alterações necessárias. 

Exemplos populares de ferramentas **IaC** incluem **Terraform**, **Bicep**, **CloudFormation** e **Pulumi**.

**IaC** é usado para gerenciar recursos de infraestrutura em **nuvem**, como servidores, redes, instâncias de banco de dados e outros serviços. Ao usar IaC, as equipes de TI podem provisionar e gerenciar infraestrutura rapidamente e de forma consistente, sem a necessidade de intervenção manual. 

![](/assets/img/62/iac02.png){: "width=60%" }

### **Configuration Management (CM)**

**Configuration Management (CM)** é uma abordagem para gerenciar e manter o estado de sistemas de TI. Com **CM**, as configurações de software são gerenciadas e mantidas por meio de scripts e ferramentas de automação. 

Exemplos populares de ferramentas **CM** incluem **Ansible**, **Puppet**, **SaltStack** e **Chef**.

**CM** é usado para garantir que os sistemas estejam configurados corretamente e permaneçam nesse estado ao longo do tempo. Por exemplo, se um servidor precisa ter um determinado software instalado e configurado para ser executado corretamente, o CM garante que o software seja instalado e configurado corretamente e mantido nesse estado ao longo do tempo. O CM também é usado para gerenciar atualizações e patches de software em vários sistemas de forma consistente.

![](/assets/img/62/iac03.png){: "width=60%" }

### **Diferenças entre IaC e CM**

Embora **IaC** e **CM** sejam abordagens de **automação de TI**, há diferenças importantes entre as duas. A principal diferença é que IaC é usado para gerenciar a infraestrutura, enquanto CM é usado para gerenciar a configuração de sistemas de TI.

> IaC se concentra em provisionar recursos de infraestrutura de forma consistente, enquanto CM se concentra em manter o estado correto de sistemas de TI ao longo do tempo.

Outra diferença importante é que IaC geralmente é usado em ambientes em nuvem, enquanto CM é usado em ambientes on-premise e em nuvem. Isso ocorre porque a infraestrutura em nuvem é geralmente provisionada e gerenciada usando APIs, tornando a abordagem IaC mais fácil de usar.

### **Tudo junto e misturado**

Embora IaC e CM possam ser usados separadamente, a combinação das duas abordagens pode ser muito poderosa. Ao usar IaC e CM em conjunto, é possível provisionar recursos de infraestrutura e, em seguida, gerenciar sua configuração ao longo do tempo de forma consistente e automatizada. Isso pode levar a um gerenciamento mais eficiente e seguro de sistemas de TI, além de reduzir a carga de trabalho manual dos administradores de sistemas. 

### **O estado da arte**

Além de serem usados em conjunto, a integração de **IaC** e **CM** em uma esteira de **CI/CD** pode proporcionar ainda mais benefícios. Ao adicionar IaC e CM em um fluxo de CI/CD, é possível provisionar recursos de infraestrutura, configurá-los automaticamente e implantar o software no topo dessa infraestrutura. 

Isso significa que todo o processo de implantação pode ser automatizado e testado com mais facilidade, tornando o processo mais seguro e consistente.

Por exemplo, se um desenvolvedor precisa implantar uma nova versão do software em um ambiente de teste, a esteira de CI/CD pode provisionar a infraestrutura necessária automaticamente usando IaC. Em seguida, a esteira pode configurar automaticamente a infraestrutura usando CM e implantar o software no topo da infraestrutura. Isso garante que todo o processo seja consistente, seguro e automatizado, reduzindo a possibilidade de erros humanos e aumentando a velocidade de entrega.

A combinação de **IaC** e **CM** em uma esteira de **CI/CD** pode ajudar a alcançar o **estado da arte em automação de TI**, proporcionando uma solução completa e automatizada para provisionamento de infraestrutura, configuração de sistemas e implantação de software.

### **E no mundo real?**

Um exemplo de automação em nuvem usando **IaC** e **CM** pode ser implementado no **Microsoft Azure**, usando as ferramentas **Terraform** para **IaC** e **Ansible** para **CM**. Para automatizar todo o processo de provisionamento de infraestrutura, configuração de sistemas e implantação de software, podemos usar o **Azure Pipelines** como nossa esteira de **CI/CD**.

Para começar, podemos criar um repositório no **Azure DevOps** para armazenar nossos arquivos de configuração de infraestrutura e de sistema. Em seguida, podemos configurar uma esteira de **CI/CD no Azure Pipeline**s para disparar o processo de automação sempre que houver uma nova alteração no repositório.

O primeiro passo é usar o **Terraform** para provisionar os recursos de infraestrutura no Azure, como máquinas virtuais, redes virtuais e outros recursos. Isso pode ser feito criando arquivos de configuração do **Terraform (como .tf)** para descrever a infraestrutura que desejamos provisionar.

Em seguida, podemos usar o **Ansible** para configurar automaticamente a infraestrutura, instalando e configurando software nos sistemas. Isso pode ser feito criando arquivos de configuração do **Ansible (como .yml)** para descrever os passos necessários para configurar cada sistema.

Finalmente, podemos usar o **Azure Pipelines** para implantar o software em cima da infraestrutura configurada. Isso pode ser feito criando um **pipeline** de implantação que compile o software, crie um pacote de implantação e o implante automaticamente em cima da infraestrutura configurada.

Com essa abordagem, todo o processo de automação pode ser disparado automaticamente sempre que houver uma nova alteração no repositório, proporcionando uma solução completa de automação.

### **Conclusão**

**IaC** e **CM** são abordagens importantes para automação de TI, permitindo que a infraestrutura e os sistemas de TI sejam gerenciados de forma consistente e segura. 

Ao combinar essas abordagens em uma esteira de **CI/CD**, é possível obter uma solução completa e automatizada para provisionamento de infraestrutura, configuração de sistemas e implantação de software. 

Usando ferramentas populares como **Terraform**, **Ansible** e **Azure Pipelines**, é possível implementar essa abordagem em um ambiente de nuvem como o **Microsoft Azure,** permitindo que as empresas aproveitem ao máximo a automação de TI para aumentar a eficiência, reduzir erros e aumentar a escalabilidade.

É isso galera, espero que gostem!

Forte Abraço!
