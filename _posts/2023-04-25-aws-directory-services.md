---
layout: post
title: "Fundamentos sobre AWS Directory Services [Portal]"
author: [rpaliosa]
date: 2023-04-26 00:01 -0300
categories: [AWS, AWS-To]
tags: [aws, active directory, iam, identidade, infraestrutura, cloud]
---

### Saudações Pessoal!!!

Antes de falar sobre o Serviço de Diretório dentro da **laranjinha** , vou pontuar que até poucos anos atrás, sempre mantive a minha cabeça focada no planeta **Microsoft** e aos que também possuem o mesmo histórico profissional, provavelmente vão concordar com a ideia de que se tem algo pelo qual o Windows Server deva ser respeitado e a equipe do **tio Bill** aplaudida, é pela eficiência do **Active Directory** e por tudo o que ele representa dentro do ambiente de administração de redes!!!

Claro, pra você que possui milhares de KM rodados no **Planeta Linux** deve estar pensando, com muita empatia rs rs, **"Sabe de nadaaaa inocenteeee rs rs"**, mas acredito que mesmo dentro da **turminha do pinguin** os mais sensatos também vão reconhecer que de fato, o Active Directory da Microsoft é um serviço que funciona muito bemmmm!!!

O **Active Directory** é um serviço fundamental dentro do ambiente de rede por fazer entre outras funções a autenticação, o gerenciamento e o controle de acesso de usuários e dispositivos. É baseado no LDAP **(Lightweight Directory Access Protocol)**  que é um protocolo para serviços de diretório que organiza dados por hierarquia, permitindo que os usuários de uma rede local ou pública localizem dados sobre organizações, indivíduos e outros recursos, como dispositivos, arquivos e aplicações.

O protocolo LDAP é uma versão "leve" do Directory Access Protocol (DAP), que faz parte do padrão X.500, um padrão para serviços de diretório em uma rede.  


### **1. Visão geral do AWS Directory Services**

O **AWS Directory Service** fornece de forma completa ou parcial o Microsoft Active Directory (AD) como um **Serviço Gerenciado** ou seja, você **não se preocupa com o gerenciamento da infraestrutura computacional**. 

Também é arquitetado sob protocolo LDAP permitindo que as identidades do Active Directory possam se integrar com os recursos na AWS, respeitando as principais características do Microsoft Active Directory On Premises.

A **AWS** oferece até o momento, as seguintes opções de Serviço de Diretório baseados no Active Directory e protocolo LDAP.

* AWS Managed Microsoft AD
* AD Connector
* Simple AD

### **2. AWS Managed Microsoft AD**

Por ser um **Serviço totalmente Gerenciado**, o **Managed Microsoft AD** oferece:

- Monitoramento que localiza e substitui automaticamente os controladores de domínio com falha.
- Alta disponibilidade uma vez que que é implementado em várias regiões da AWS, pois os diretórios são considerados infraestruturas de missão crítica.
- Snapshots automáticos diários.

Neste serviço a AWS proporciona em CLOUD experiência similar ao cenário On Premises, permitindo inclusive integração com Microsoft 365, SharePoint e aplicativos personalizados baseados em .NET e SQL Server.

- Baseado em Windows Server 2019 com suporte a florestas e domínio desde a versão 2012 R2.
- Pode ser integração com Active Directory On premises **ou** operar de forma independente em Cloud.
- Gerenciar usuários e grupos
- Single Sign On (SSO) permitindo que através de uma conta do Active Directory seja possível autenticar e gerenciar o ambiente AWS
- Políticas baseadas em grupo permitindo que você controle usuários e dispositivos usando objetos de política de grupo (GPOs) nativos do Active Directory.
- Simplificar a implantação e o gerenciamento de cargas de trabalho do Linux e do Microsoft Windows baseadas na nuvem
- Suporte a autenticação multifator (MFA), integrando-se à sua infraestrutura de MFA existente baseada em RADIUS para fornecer uma camada adicional de segurança

>**Observação:** O AWS Microsoft AD não oferece suporte ao modo de replicação em que ocorre a replicação para um AD local.
{: .prompt-warning }

O AWS Microsoft AD está disponível em 2 edições:

- **Standard Edition** é otimizada para ser um diretório principal para pequenas e médias empresas com até 5.000 funcionários. Ele fornece capacidade de armazenamento suficiente para suportar até 30.000 objetos de diretório, como usuários, grupos e computadores.

- **Enterprise Edition** foi projetada para oferecer suporte a organizações corporativas com até 500.000 objetos de diretório.

A imagem abaixo apresenta diversas possibilidades de uso no Managed AD pela AWS.

![](/assets/img/67/aws-ad01.png){: "width=60%" }

### **3. AD Connector**

O **AD Connector** é um gateway de diretório dentro da AWS que estabelece um link com um Active Directory Principal fora da AWS, seja host físico On Premises ou Virtualizado em Cloud Privada ou Pública.

AD Connector foi projetado para atender **2** tipos de demandas:

- Pequeno: projetado para organizações de até 500 usuários.
- Grande: projetado para organizações de até 5.000 usuários.

Quando os usuários fazem login nos aplicativos da AWS, o conector do AD encaminha solicitações de login para seus DCs do AD locais (fora da AWS).

É possível também associar instâncias do EC2 ao seu AD local por meio do AD Connector, além de permitir login no Console de gerenciamento da AWS usando seus AD DCs locais para autenticação.

Você pode usar o AD Connector para autenticação multifator usando infraestrutura MFA baseada em RADIUS bem como distribuir cargas de aplicativos em muitos AD Connectors para aumentar o desempenho. Não há limitações de usuários ou conexões.

>**Observação:** Não compatível com RDS SQL.
{: .prompt-warning }

### **4. Simple AD**

O Simple AD é um recurso LDAP  **Compatível** com o Active Directory mas é baseado em **SAMBA LINUX 4**, tendo como características:

- Um serviço barato compatível com os principais recursos do Active Directory 
- Diretório independente e totalmente gerenciado na nuvem AWS.
- AD simples é geralmente a opção mais barata.
- A melhor escolha para menos de 5.000 usuários caso não precise de recursos avançados do AD.
- Permite criar usuários e controlar o acesso a aplicativos na AWS.
- Gerenciar Grupos e contas de usuário.
- Aplicar políticas de grupo.
- Conecte-se com segurança a instâncias do EC2.
- SSO baseado em Kerberos.
- Oferece suporte para ingressar em instâncias do EC2 baseadas em Linux ou Windows.
- A AWS fornece monitoramento, instantâneos diários e serviços de recuperação.
- O Simple AD é compatível com WorkSpaces, WorkDocs, Workmail e QuickSight.
- Login no console de gerenciamento da AWS com contas de usuário do Simple AD.

**O Simple AD pode ser provisionado em 2 modalidades:**

- Small: suporta até 500 usuários (aproximadamente 2.000 objetos).
- Large: suporta até 5.000 usuários (aproximadamente 20.000 objetos).
A AWS cria dois servidores de diretório e servidores DNS em duas sub-redes diferentes em uma AZ.

**Simple AD NÃO suporta:**

- Atualizações dinâmicas de DNS.
- Extensões de esquema.
- Autenticação multifator.
- Comunicação sobre LDAPS.
- Cmdlets AD do PowerShell.
- Transferência de função FSMO.
- Não compatível com o servidor RDS SQL.
- Não oferece suporte a relações de confiança com outros domínios, recurso disponível apenas com **AWS MANAGED AD**.

### **5. Quanto custa a brincadeira ???**

Bom, até aqui vimos que a AWS é bem versátil no quesito Serviço de Diretório, masssss ao transferir parte da **"Dor de Cabeça"** de sustentar uma infra local para Cloud, seja ela qual for, não há como não gastar uns dinheirinhos rs rs.

Como a precificação depende de cenários, deixo como recomendação o link oficial AWS  em  <a href="https://aws.amazon.com/pt/directoryservice/pricing/?nc=sn&loc=3" target="_blank"> **Preços AWS Directory Service**</a>


Eeeeee chegamos ao fim!!!!<br>
Espero que este artigo tenha agregado um pouco mais de conhecimento!!!
Fique a vontade para trocar uma idéia sobre este e outros temas la no Linkedin!!!!
 
Tkssss pela leitura e Até breve!!! 🍻🚀 