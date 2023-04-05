---
layout: post
title: "[AWS-To] #01 Monitorando com AWS Managed Grafana [Portal]"
author: [rpaliosa]
date: 2023-03-27 00:01 -0300
categories: [AWS, AWS-To]
tags: [aws, devops, observabillity, monitoramento, grafana, infraestrutura, cloud]
---

### Saudações Pessoal!!!

O Gerente se rendeuuuu rs rs rs rs. <br>
Com enorme satisfação trago na  programação do Unicast Cloud o **1º How To da Laranjinha!!!** <br>
O Artigo de hoje fala sobre  **Monitoramento com AWS Managed GRAFANA!!!**

### **Sobre o AWS Managed Grafana**

O Amazon Managed Grafana é um serviço totalmente gerenciado em que você se preocupa com os Dashboards, acessos, níveis de permissões enquanto a AWS se preocupa com toda a infraestrutura que sustentará o Grafana!!!

Através do **AMG** (Amazon  Managed Grafana) é possível realizar as mesmas atividades que são feitas  em uma instalação do Grafana baseadas em Windows, Linux ou  Containers. Pode ser integrado a fontes de dados do Amazon CloudWatch, Amazon OpenSearch Service, AWS X-Ray, AWS IoT SiteWise, Amazon Timestream e Amazon Managed Service for Prometheus, além de também oferecer suporte a muitas outras fontes de dados, incluindo **AZURE e GCP!!!**

Os Dashboards são criados em um área chamada de **Workspaces**, e cada Workspace pode ser considerado como um Servidor Lógico independente, ou seja, cada Workspace possui sua URL própria para acesso ao Portal de Gerenciamento do Grafana, políticas de acesso e usuários ou grupo de usuários que poderão visualizar ou administrar os Dashboards e demais configurações;

Neste momento o serviço está disponível nas regiões de Ohio, N. Virgínia, Oregon, Seul, Cingapura, Sydney, Tóquio, Frankfurt, Irlanda e Londres, e para brincar no **"Playground"** a AWS **cobra por cada usuário ativo no Workspace!!**.
**"Dá um Ping"** no Portal AWS em <a href="https://aws.amazon.com/pt/grafana/pricing/" target="_blank">AMG Pricing </a> para conferir o tarifário do brinquedo!!! <br>

Para maiores detalhes **dê um mergulho** na Doc oficial em <a href="https://docs.aws.amazon.com/grafana/latest/userguide/what-is-Amazon-Managed-Service-Grafana.html" target="_blank">Amazon Managed Grafana. </a> 

### **Objetivo**

O objetivo deste artigo é **Implantar o Managed Grafana e Monitorar uma EC2**.

>**Observação:** Este artigo parte do princípio que o leitor já domina a criação de VPC e EC2 na AWS e tenha associado ao **IAM IDENTITY CENTER**  pelo menos 1 usuário para **autenticação SSO** !!!
{: .prompt-warning }

### **1. Implantação do Managed Grafana**

1.1 - Pesquise por **GRAFANA** na barra pesquisa e clique em **Amazon Grafana**.

![](/assets/img/64/awsgrafana01.png ){: "width=60%" }

1.2 - Clique em **Create workspace**. 

![](/assets/img/64/awsgrafana02.png ){: "width=60%" }

1.3 - O Workspace é criado em 4 Etapas. Neste passo escolha um nome e avance. 

![](/assets/img/64/awsgrafana03.png ){: "width=60%" }

1.4 - É possível selecionar 2 métodos de autenticação, por IAM Identity Center (SSO) e/ou por SAML. Neste exemplo foi utilizado o método SSO.

![](/assets/img/64/awsgrafana04.png ){: "width=60%" }

1.5 - O Monitoramento pode ser realizado em toda a conta ou em Unidade Organizacional. Selecione **Current Account** e em **Data Source**, selecione **Cloud Watch**. Clique em **Next**.

![](/assets/img/64/awsgrafana05.png ){: "width=60%" }

1.6 - Revise as informações e clique em **Create Workspace**. O processo de Deploy demora alguns minutos.

![](/assets/img/64/awsgrafana06.png ){: "width=60%" }

1.7 - A próxima etapa é atribuir ao menos 1 usuário que terá acesso ao **Painel de Administração do Grafana**. Clique em **Assign new user or Group**.

![](/assets/img/64/awsgrafana07.png ){: "width=60%" }

1.8 - Selecione o usuário e clique em **Assign User**.

![](/assets/img/64/awsgrafana08.png ){: "width=60%" }

1.9 - Por padrão o usuário recebe a **permissão Viewer**. Siga as estapas da imagem para alterar a permissão para **Admin**.

![](/assets/img/64/awsgrafana09.png ){: "width=60%" }

1.10 - Confirme que o usuário é Admin e clique no **Nome do Workspace** na parte superior da tela.

![](/assets/img/64/awsgrafana10.png ){: "width=60%" }

1.11 - A imagem abaixo apresenta informações importante do Workspace, como **URL do Painel de Gerenciamento Grafana**, **Modelo de Autenticação** e uma opção de migrar a versão do **Managed Grafana** para Enterprise **(NÃO SERÁ NECESSÁRIO FAZER O UPGRADE PARA DAR SEQUÊNCIA, OK!!!)**

![](/assets/img/64/awsgrafana11.png ){: "width=60%" }

### **2. Acesso e Configuração do Grafana**

2.1 - Conforme imagem anterior, clique na URL do **Grafana Workspace**.

2.2 - Autentique o usuário que foi adicionado no **Workspace** para acesso ao **Painel de Gerenciamento do Grafana**.

![](/assets/img/64/awsgrafana12.png ){: "width=60%" }
---
![](/assets/img/64/awsgrafana13.png ){: "width=60%" }
---
![](/assets/img/64/awsgrafana14.png ){: "width=60%" }

2.3 - Agora é hora de adicionar um **Data Source**. Clique na **engrenagem** no lado esquerdo da tela e em **Data Sources**.

![](/assets/img/64/awsgrafana15.png ){: "width=60%" }

2.4 - Na **Guia Data Sources**, clique em **Add Data Source**.

![](/assets/img/64/awsgrafana16.png ){: "width=60%" }

2.5 - Pesquise por **Cloud Watch**.

![](/assets/img/64/awsgrafana17.png ){: "width=60%" }

2.6 - Para o Grafana ter acesso ao **Cloud Watch** é necessária uma autenticação na conta **AWS**. Para este artigo utilizei **Access Key e Secret Key**. Clique em **Save e Test** e em seguida acesse a **Guia Permissions**

![](/assets/img/64/awsgrafana18.png ){: "width=60%" }

2.7 - Em **Permissions**, clique em **Enable**.

![](/assets/img/64/awsgrafana18-2.png ){: "width=60%" }


### **3. Configurando o Dashboard**

3.1 - Clique no **ícone +** e em **Dashboard**.

![](/assets/img/64/awsgrafana19.png ){: "width=60%" }

3.2 - Add a new Panel.

![](/assets/img/64/awsgrafana20.png ){: "width=60%" }

3.3 - Esta é uma etapa que exige **muita atenção!!!**.<br>
Observe bem a imagem abaixo e siga as configurações. Assim que a **instância EC2** for selecionada, o Dashboard deverá exibir as informações de **Processamento da CPU**.

![](/assets/img/64/awsgrafana21.png ){: "width=60%" }

3.4 - Para melhorar a visualização do Dashboard, do lado direito do Painel, certifique-se que o tipo de gráfico está em **Time Series**. Em **Panel Options**, insira o título deste painel.  

![](/assets/img/64/awsgrafana22.png ){: "width=60%" }

3.5 - Em **Graph Styles**, selecione **Lines**, **formato de Line** e clique em **APPLY**.

![](/assets/img/64/awsgrafana23.png ){: "width=60%" }

3.6 - E depois de tudo isso, o 1º Painel do Dashboard está na tela!!!!. <br>
Clique em **Salvar** conforme imagem. 

![](/assets/img/64/awsgrafana24.png ){: "width=60%" }

---
Eeeeee chegamos ao fim!!!!<br>
Porémmmm, caso você também navegue pelo **Planeta Azure** e queira monitorar recursos da **Azuzinha** aqui, simmmmm, é **totalmente possível**, ah, **GCP também heimmm!!!!!**, basta adiciona-lo em Data Source(s).<br>

Tkssss pela leitura e Até breve!!! 🍻🚀 
