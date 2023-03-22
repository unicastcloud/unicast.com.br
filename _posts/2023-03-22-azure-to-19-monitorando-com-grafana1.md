---
layout: post
title: "[Azure-To] #19 Monitorando Azure com Grafana [Portal]"
author: [rpaliosa]
date: 2023-03-22 07:00 -0300
categories: [Azure, Azure-To]
tags: [azure,microsoft,devops,observabillity,monitoramento,grafana,azuread,infraestrutura]
---

### Saudações Pessoal!!!

Na programação de hoje o assunto é **Monitoramento de Recursos do Azure através do GRAFANA!!!**.

### **Sobre o Grafana**

**"Dashboard anything. Observe everything"** É desta forma que o Grafana se apresenta em seu site oficial
<a href="https://grafana.com/grafana/" target="_blank">https://grafana.com/grafana/</a>

Também chamado por alguns de **Power BI** do Monitoramento e Observabilidade, esse camarada é uma robusta solução Open Source com grande destaque em áreas como **Tecnologia da Informação** e **Mercado Financeiro** devido a sua alta capacidade de se conectar a milhares de fontes de dados, gerar Dashboards e executar ações baseadas em comportamentos.

As informações chegam ao Grafana através de **Data Sources** e alguns players bem conhecidos da galera são Prometheus, ElasticSearch, Influx DB, MySQL, PostgreSQL, Zabbix e claro os principais Cloud Prividers **Azure, AWS e GCP**.

O Grafana é um Oceano de possibilidades, visite sempre o Site Oficial e se conecte em comunidades para se aprofundar ainda mais!!!

### **Objetivo**

O objetivo deste artigo é **Integrar o Grafana ao Azure para Monitorar Virtual Machine, SQL Database e VNET**.

>**Observação:** Este artigo sugere que o Leitor já possua um ambiente Azure com os seguintes recursos:
* 1 VM Linux (B2S) **(Utilizei Ubuntu 20.04)**
* 1 VM Windows 10 (B2S)
* 1 SQL Database 
{: .prompt-warning }


### **1. Implantação do Grafana**

1.1 - A partir da **VM Windows 10**, faça conexão SSH com a **VM Linux**, neste exemplo o nome da VM será **VM-Grafana-server** e IP Privado **10.10.0.4**.

>**Observação:** Os passos abaixo de instalação do **Grafana Enterprise** seguem a Doc Oficial em <a href="https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/" target="_blank">https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/</a>
{: .prompt-warning }

![](/assets/img/63/azgrafana1.png ){: "width=60%" }
---
1.2 - Atualizar repositório de pacotes <br>
```sudo apt update -y```

1.3 - Download e instalação da última versão estável <br>

1º ```sudo apt-get install -y apt-transport-https``` <br>
2º ```sudo apt-get install -y software-properties-common wget```<br>
3º ```sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key``` <br>
4º ```echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list``` <br>
5º ```sudo apt update -y``` <br>
6º ```sudo apt-get install grafana-enterprise```

1.4 - Startando o Grafana Server 

1º ```sudo systemctl daemon-reload``` <br>
2º ```sudo systemctl start grafana-server``` <br>
3º ```sudo systemctl status grafana-server``` <br>

![](/assets/img/63/azgrafana2.png ){: "width=60%" }

1.5 - Pressione **CTRL C** para retornar e habilite o **Grafana** para inicializar durante o Boot. <br>

```sudo systemctl enable grafana-server```

![](/assets/img/63/azgrafana3.png ){: "width=60%" }


### **2. Acessar Painel de Gerenciamento**

2.1 - Por padrão, o Grafana utiliza a porta TCP **3000**. 
Então abra o seu navegador e digite o **IP do Servidor Linux**. Neste exemplo é ```http://10.10.0.4:3000 ```

![](/assets/img/63/azgrafana4.png ){: "width=60%" }

>**Observação:** Caso não consiga acessar o **Painel Grafana**, crie uma regra de entrada para a porta **TCP 3000** no **NSG** do Adaptador de Rede da **VM-Grafana!!!**
{: .prompt-warning }

2.2 - Digite **admin** nos campos **Username e Password**

2.3 - **Welcome to Grafana!!!**. Se tudo deu certo nos passos anteriores, o Painel de Gerenciamento será exbido conforme imagem abaixo.

![](/assets/img/63/azgrafana5.png ){: "width=60%" }


### **3. Registrar o Grafana no Azure**

3.1 - Acessar o Portal do Azure com nível de **Privilégio Administrativo na Subscription**. Neste exemplo foi utilizado o usuário **Owner**.

3.2 - Acessar ítem **App Registrations** em **Azure Active Directory**.

![](/assets/img/63/azgrafana6.png ){: "width=60%" }

3.3 - Clique em **+New Registration** e siga as configurações abaixo. 

![](/assets/img/63/azgrafana7.png ){: "width=60%" }

3.4 - Agora é preciso atribuir uma **Role** de **Contributor** a Identidade do Grafana na **Subscription**.

![](/assets/img/63/azgrafana8.png ){: "width=60%" }

3.5 - Clique em **+ Add** e selecione **+ Add Role Assignment** e em seguida **Privileged Administrator Roles**

![](/assets/img/63/azgrafana9.png ){: "width=60%" }

3.6 - Selecione **Contributor** e **Next**.

3.7 - Na Guia **Members**, selecione **User, Group or Service Principal** e no ítem  **+ Select Members** procure por **Grafana**.

![](/assets/img/63/azgrafana10.png ){: "width=60%" }

3.8 - Clique em **Review + Assign** e confirme se a **Coluna Type** está como **APP**

![](/assets/img/63/azgrafana11.png ){: "width=60%" }

3.9 - A guia **Role Assignments** será exibida conforme abaixo

![](/assets/img/63/azgrafana12.png ){: "width=60%" }


### **4. Vincular as Credenciais do Azure ao Grafana**

4.1 - No Azure AD, acesse **App Registration**, selecione  **Grafana** e copie para um **editor de texto** de sua preferência os
campos **Application (client) ID** e **Directory (tenant) ID**

![](/assets/img/63/azgrafana13.png ){: "width=60%" }

4.2 - No Menu Esquerdo, clique em **Certificates & secrets** e em **Client Secrets** selecione **+ New Client Secret**.

![](/assets/img/63/azgrafana14.png ){: "width=60%" }

4.3 - A função do Secret é gerar uma chave de Autenticação que será utilizada pelo Grafana. O tempo de validade da Secret pode ser customizado. Neste Exemplo configurei para 5 dias.

![](/assets/img/63/azgrafana15.png ){: "width=60%" }

4.4 - Copie o Campo **Value** conforme fez no **ítem 4.1**

![](/assets/img/63/azgrafana16.png ){: "width=60%" }


### **5. Vincular Azure ao Grafana**

5.1 - Na Console de Gerenciamento do Grafana, clicar na **Engrenagem** a **Esquerda** e depois em **Data Sources**

![](/assets/img/63/azgrafana17.png ){: "width=60%" }

5.2 - Em **Data Source**, digite no campo de pesquisa **Azure** e selecione **Azure Monitor**

![](/assets/img/63/azgrafana18.png ){: "width=60%" }

5.3 - Insira nos campos correspondentes os dados informados pelo **Azure** conforme os ítens **4.1 e 4.4**, clique em **Load Subscriptions** e observe que o Grafana exibirá os dados da **subscription**. Clique em **Save & Test**.

![](/assets/img/63/azgrafana19.png ){: "width=60%" }


### **6. Criar Dashboard para Monitorar o Servidor Linux-Grafana**

6.1 - Conforme próxima imagem, clique em **+ New Dashboard**

![](/assets/img/63/azgrafana20.png ){: "width=60%" }

6.2 - Clique em **Add a new Panel**

![](/assets/img/63/azgrafana21.png ){: "width=60%" }

6.3 - Observe que o Campo **Data Source** está configurado como **Azure Monitor**. Clique em **Select a Resource** e procure pela **VM GRAFANA SERVER**.

![](/assets/img/63/azgrafana22.png ){: "width=60%" }

6.4 - Neste exemplo, será monitorado o Processamento da VM através do campo **Metric**. Selecione **Percentage CPU** e veja que rapidamente o **Painel Superior** exibirá o **monitoramento da CPU**. Clique em **Apply** no **Canto Superior Direito**

![](/assets/img/63/azgrafana23.png ){: "width=60%" }

6.5 - A próxima imagem mostra o resultado da configuração. 

![](/assets/img/63/azgrafana24.png ){: "width=60%" }


### **7. Criar Dashboard para Monitorar a Virtual Network**

7.1 - Clique no Ícone **Add Panel** conforme imagem e depois em **Add a New Panel**

![](/assets/img/63/azgrafana25.png ){: "width=60%" }

7.2 - Em **Select a Resource**, procure por **Network Interfaces** e clique em **Apply**. 

![](/assets/img/63/azgrafana26.png ){: "width=60%" }

7.3 - Em **Metric** selecione **Packets Sent** e **Apply** no canto Superior Direito.

![](/assets/img/63/azgrafana27.png ){: "width=60%" }

7.4 - Nesta etapa o Grafana já possui 2 Paineis no Dashboard.

![](/assets/img/63/azgrafana28.png ){: "width=60%" }

### **8. Criar Dashboard para Monitorar SQL Database**

8.1 - Clique novamente no Ícone **Add Panel** conforme imagem e depois em **Add a New Panel**

![](/assets/img/63/azgrafana25.png ){: "width=60%" }

8.2 - Em **Select a Resource**, procure por **SQL Databases** e clique em **Apply**. 

![](/assets/img/63/azgrafana29.png ){: "width=60%" }

8.3 - Em **Metric**, procure por **CPU Percentage**. 

![](/assets/img/63/azgrafana30.png ){: "width=60%" }

8.4 - Clique em **Apply** no canto superior direto para visualizar o Dashboard com os **3 Painéis**. 
Para salvar o Dashboard clique no ícone correspondente conforme imagem!.

![](/assets/img/63/azgrafana31.png ){: "width=60%" }

---

Eeeeee chegamos ao fim!!!!<br>
Claro, concordo com você, este artigo não cobriu 5% do que o Grafana pode fazer, mas o **objetivo de mostrar como fazer a integração com Azure** foi concluído. 
Deixo como sugestão explorar onde alterar o **Título do Painel** e **opções de Gráficos** e claro, caso tenha despertado o interesse, navegar ainda mais no **Grafana!!!**.
 
Tkssss pela leitura e Até breve!!! 🍻🚀 
