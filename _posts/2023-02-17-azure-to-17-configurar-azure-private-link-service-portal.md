---
layout: post
<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md
title: "[Azure-To] #17 Configurar Azure Private Link Service"
authors: [rpaliosa, asilva ]
date: 2023-02-17 09:00:00 -0300
=======
title: "[Azure-To] #17 Configurar Azure Private Link Service [Portal]"
authors: [asilva, rpaliosa]
date: 2023-02-20 09:00:00 -0300
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md
categories: [Azure, Azure-To]
tags: [azure, microsoft, network, privatelinkservice, az700]
---

Saudações Pessoal!!!

No cardápio de hoje vamos servir Azure Private Link Service. 

Masssss, antes de por a "mão na massa", vamos entender em quais momentos seria legal servir este "prato" blz...

### **Sobre Azure Private Link Service**

De forma objetiva, o Azure Private Link Service cria uma conexão **Privada** por dentro do **Backbone Microsoft**, entre VNET´s que podem estar inclusive em **Regiões Diferentes** e até mesmo, criar Conexão entre VNET´s de **Subscriptions Diferentes!!!** 

É isto mesmo que você está lendo, o **Azure Private Link Service** é uma alternativa de **Conexão Privada** para casos em que não se opte por Gateway de VPN ou Peering.

O diagrama abaixo demonstra uma arquitetura de Private Link Service **integrando 2 Vnet´s distintas de Subscritprions Distintas**, onde recursos da **Subscription A, na Região A** se comunicam com VM´s da **Subscription B, na Região B** por meio de um **Standard Load Balancer**

<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md
![](/assets/img/59/pvtls-01.png){:"width=60%"}
<br>
=======
![](/assets/img/59/pvtls01.png){:"width=60%"}
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md

Basicamente, a engrenagem roda com uma certa semelhança ao sistema **Cliente / Servidor** 

1. A VNET que terá o papel de **Servir**, precisa ter o recurso atrás de um **Standard Load Balancer.**
2. Um Private Link Service é criado e linkado ao **Frontend IP Interno** do Standard Load Balancer. 
3. Um Link **Service ID** (URI ou Alias) é compartilhado com o **Private Link** da **Subscription / Vnet** que terá a função de  **Cliente** do recurso. 
4. Do lado do ambiente **Cliente** é criado um **Private EndPoint** com as informações do **Service ID** bem como as configurações de DNS para registro do Private IP Address. 
5. O ambiente **Servidor** recebe um pedido para **Aprovar ou Rejeitar** a conexão. 
6. Em caso de aprovação, O ambiente **Cliente** inicia a conexão com o ambiente **Servidor**

### **Objetivo**

<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md
O objtivo deste artigo é criar um **Private Link Service** para que a Virtual Machine **vm-paris1** de uma **Subscription A, na Região France Central** acesse Servidores Apache localizados na **Subscription B, da Região East US - Virginia**. <br>
=======
Permitir que uma Virtual Machine de uma **Subscription A, na Região Central India** simulando a função de *Cliente* acesse servidores Apache em Virtual Machines localizadas na **Subscription B, da Região East US2**, simulando a função de *Servidor*.
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md
Detalhes no diagama abaixo:

![](/assets/img/59/pvtls-02.png){:"width=60%"}

<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md

>**Observação:** Este Artigo parte do princípio que o leitor já domina a criação de Máquinas Virtuais, Virtual Network e fundamentos do Private Endpoint**!!!
=======
>**Observação:** Este Artigo parte do princípio que o leitor já domina a criação de Máquinas Virtuais, Virtual Network e  Network Security Group **(NSG)**!!!
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md
{: .prompt-warning }

### **1. Criar ambiente da Subscription EAST US-Virginia**

<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md
1.1 - Criar o **Resource Group** ```RG-Virginia``` na região **EAST US**

1.2 - Criar a **VNET** ```Vnet1-Virginia``` com o Range de IP ```172.16.0.0/16``` e a **Subnet** ```Sub1-Virginia``` com Range de IP ```172.16.0.0/24```

1.3 - Criar a Virtual Machine chamada **vm-apache1** conforme imagens a seguir:  

![](/assets/img/59/pvtls-virginia01.png){:"width=60%"}
---
![](/assets/img/59/pvtls-virginia02.png){:"width=60%"}
---
![](/assets/img/59/pvtls-virginia03.png){:"width=60%"}
---

>**Observação:** A Virtual Machine deve ser criada com o atributo **NONE** para **Public IP** . Concluir a criação da VM sem nenhuma outra configuração!!!
=======
1. Criar o **Resource Group** ```RG-Servidor``` na região **EAST US 2**
2. Criar a **VNET** ```Vnet1-Servidor``` com o Range de IP ```192.168.0.0/16``` e a **Subnet** ```Sub1-Servidor``` com Range de IP ```192.168.0.0/24```
3. Criar o **Network Security Group** chamado ```NSG1``` e associar a Subnet **Sub1-Servidor**
4. Criar a Virtual Machine chamada **VM-Apache1** conforme descrição imagens abaixo:  

![](/assets/img/59/pvtls03.png){:"width=60%"}

![](/assets/img/59/pvtls04.png){:"width=60%"}

>**Observação:** A Virtual Machine deve ser criada com o atributo **NONE** para **Public IP** e **Nic Network Security Group** !!!
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md
{: .prompt-warning }

1.4 - Após concluir a criação da **VM-APACHE1**, acessar o painel de administração, selecionar no lado esquerdo da tela a opção **Run Command**, **RunShellScript** e digitar o script que fará a instalação do Servidor apache, conforme imagem abaixo: <br>

<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md
![](/assets/img/59/pvtls-virginia04.png){:"width=60%"}
=======
#### **1.2 - Instalar Servidor Apache nas VMs**

Acessar o Painel de Administração da VM **Apache1** e na Categoria **Operations**, clicar em **Run Command** e depois em **RunShellScript**

![](/assets/img/59/pvtls06.png){:"width=60%"}

Digitar o script conforme a figura a baixo e clicar em **Run**

![](/assets/img/59/pvtls07.png){:"width=60%"}
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md

```bash
apt update -y
apt install apache2 -y
cd /var/www/html
rm -f index.html
echo "APACHE 1 EM SERVIDOR VIRGINIA" > index.html
```
<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md
>**Observação:** Aguardar a conclusão da execução sem sair da tela. Será exibido um log abaixo do **Botão Run** indicando que o Servidor Apache foi instalado!!!
=======

O resultado do Script deve retornar mensagem similar a figura abaixo.

![](/assets/img/59/pvtls08.png){:"width=60%"}

>**Observação:** Para criar a **vm-apache2** basta executar novamente as etapas **1.4**, **1.5**, **1.6** e **1.7**, porém, alterando a linha **echo** do Script para:
```bash
echo "SERVIDOR APACHE 02" > index.html
```
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md
{: .prompt-warning }

1.5 - Para a criação da **VM-APACHE2**, repetir os passos **1.3** e **1.4**, porém, fazendo uma alteração na última linha do Script, conforme imagem abaixo:

![](/assets/img/59/pvtls-virginia05.png){:"width=60%"}

>**Observação:** Aguardar a conclusão da execução sem sair da tela. Será exibido um log abaixo do **Botão Run** indicando que o Servidor Apache foi instalado!!!
{: .prompt-warning }


### **2. Criar Load Balancer Interno para vm-apache1 e vm-apache2**

<<<<<<< HEAD:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service.md

2.1 - Na Barra Superior de Pesquisa do Azure, pesquisar por **Load Balancers**

![](/assets/img/59/pvtls-virginia06.png){:"width=60%"}

2.2 - Selecione **Load Balancer** e clique em **+ Create**

2.3 - Configure conforme imagem abaixo e clique em **+ Frontend IP Configuration**

![](/assets/img/59/pvtls-virginia07.png){:"width=60%"}

2.4 - Clique em **+ Add a Frontend IP Configuration**

![](/assets/img/59/pvtls-virginia08.png){:"width=60%"}

2.5 - Configure o **Frontend IP** conforme imagem a seguir:

![](/assets/img/59/pvtls-virginia09.png){:"width=60%"}

>**Observação:** O Frontend IP pode ser um IP atribuído dinamicamente pelo Azure ou definido de forma Fixa pelo Administrador. Neste exemplo foi definido o IP ```172.16.0.250```.
Será através deste IP que a **VM-PARIS1** acessará os Servidores Apache!!!
{: .prompt-warning }

2.6 - Avance para a Guia **Backend Pool** e siga as configurações conforme a próxima imagem e no fim da página **Clique em Save**

![](/assets/img/59/pvtls-virginia10.png){:"width=60%"}

2.7 - Em **inbounbd Rules** clique em **+ Add a Load balancing Rule** e siga as instruções abaixo, com atenção ao ítem **Health Probe - Create New**

![](/assets/img/59/pvtls-virginia11.png){:"width=60%"}

2.8 - Para finalizar a criação do Load Balancer, clique em **Review + Create** e a seguir em **Create**

![](/assets/img/59/pvtls-virginia12.png){:"width=60%"}
---
![](/assets/img/59/pvtls-virginia13.png){:"width=60%"}


### **3. Criar Private Link Service para vm-apache1 e vm-apache2**

3.1 - Digite na **Barra de Pesquisa** do Azure ``` Private Link Service``` e clique no serviço.

![](/assets/img/59/pvtls-virginia14.png){:"width=60%"}

3.2 -  Clique em **+ Create**

![](/assets/img/59/pvtls-virginia15.png){:"width=60%"}

3.3 - Em Basics, Seleciona o Resource Group e digite o nome da Instância. Clique em **Outbound Settings** para proceguir.

![](/assets/img/59/pvtls-virginia16.png){:"width=60%"}

3.4 - Selecione as configurações conforme próxima imagem e continue para **Access Security**

![](/assets/img/59/pvtls-virginia17.png){:"width=60%"}

3.5 - Este é um ponto que exige **Muita Atenção!**<br>
Para que seja possível a comunicação entre **Subscriptions Diferentes** é necessário selecionar a opção **Anyone with your alias**, clicar em **Add Subscription** e do lado direito da tela **Inserir a Subscription que hospeda a VM que deverá consultar os Servidores Apache**; <br>
Neste exemplo foi usada a Subscription **France Central** que hospeda a **VM-PARIS1**.

![](/assets/img/59/pvtls-virginia18.png){:"width=60%"}
---
![](/assets/img/59/pvtls-0.png){:"width=60%"}

3.6 - Clique em **Next Tags** e conclua a criação do Private Link Service.

![](/assets/img/59/pvtls-virginia19.png){:"width=60%"}

3.7 - Retorne a **Barra de Pesquisa** do Azure, procure por **Private Link Service** e selecione a conexão conforme imagem:

![](/assets/img/59/pvtls-virginia20.png){:"width=60%"}

3.8 - Do lado esquerdo, clique em **Propriedades** e procure pelo atributo **Alias**. <br>
Esta **String** será utilizada durante a criação do **Private Endpoint** na Subscription **France Central** que hospeda a **VM-PARIS1**

![](/assets/img/59/pvtls-virginia21.png){:"width=60%"}

3.9 - Após a conclusão da configuração na Subscription **France Central**, o ítem **Private Endpoint Connections** será exibido semelhante ao mostrada na próxima imagem:

![](/assets/img/59/pvtls-virginia22.png){:"width=60%"}

>**Observação:** O ítem **Private Endpoint Connections** exibe se a relação de confiança entre as 2 Subscriptions ocorreu com sucesso (Approved). Também é possivel **Rejeitar** ou **Remover** a conexão!!!
{: .prompt-warning }


### **4. Configurar ambiente para vm-paris1 em France Central**

A **vm-paris1** será implantada na Região **France Central** em uma **Subscription diferente** de onde foi implantado os Servidores Apache **(vm-apache1 e vm-apache2)**,

4.1 - Criar o **Resource Group** ```RG-Paris``` na região **France Central**

4.2 - Criar a **VNET** ```Vnet1-Paris``` com o Range de IP ```10.10.0.0/16``` e a **Subnet** ```Sub1-Paris``` com Range de IP ```10.10.0.0/24```

4.3 - Criar a Virtual Machine chamada **vm-paris1** conforme imagens a seguir:  

![](/assets/img/59/pvtls-paris1.png){:"width=60%"}
---
![](/assets/img/59/pvtls-paris2.png){:"width=60%"}
---
![](/assets/img/59/pvtls-paris3.png){:"width=60%"}
---
>**Observação:** A Virtual Machine deve ser criada com IP Público para realização dos testes!!!
{: .prompt-warning }


### **5. Configurar o Private Endpoint para vm-paris1**

5.1 - Na **Barra de Pesquisa** do Azure, pesquisar por ``` Private Endponit``` 

![](/assets/img/59/pvtls-paris4.png){:"width=60%"}

5.2 - Selecione **Private endpoints** e **+ Create**

![](/assets/img/59/pvtls-paris5.png){:"width=60%"}

5.3 - Em Basics, Selecione o Resource Group e os detalhes da Instância.

![](/assets/img/59/pvtls-paris6.png){:"width=60%"}

5.4 - **Atenção!**<br>
Conforme detalhado no **ítem 3.8** , é nesta etapa que será utilizado o **Alias** do **Private Link Services** da Subscription onde foram implantados os Servidores Apache.

![](/assets/img/59/pvtls-paris7.png){:"width=60%"}

5.5 - Siga para **Virtual Network** conforme imagem abaixo:

![](/assets/img/59/pvtls-paris8.png){:"width=60%"}

5.6 - Por se tratar de uma conexão entre Redes de Subscriptions diferentes, não é possível ativar a Integração com a Zona DNS. Avançar até **Review + Create**.

![](/assets/img/59/pvtls-paris9.png){:"width=60%"}

5.7 - Concluir a criação do Private Endpoint.

![](/assets/img/59/pvtls-paris10.png){:"width=60%"}

5.8 - Ao finalizar a criação do Private Endpoint, clicar em **Go to Resource** para chegar ao Painel de Administração, onde é possível confirmar o sucesso da conexão com a **Subscription** em **East US Virginia**

![](/assets/img/59/pvtls-paris11.png){:"width=60%"}


### **6. Testando o Private Link Service**

6.1 - Acessar o Painel de Administração da **vm-paris1** e confirmar o **Public IP Address**

![](/assets/img/59/pvtls-paris12.png){:"width=60%"}

6.2 - Através do **Powershell, Terminal Bash ou Putty**, conectar a **vm-paris1**,  e utilizar o comando ```curl 172.16.0.250``` para consultar os Servidores Apache **(vm-apache1 e vm-apache2)** que foram provionados na **Subscription em East US Virginia**.

![](/assets/img/59/pvtls-paris13.png){:"width=60%"}

>**Observação:** Embora a **vm-paris1** tenha sido acessada por IP Público, a comunicação com os Servidores Apache em **East US** ocorre de forma **privada** através do **Backbone Microsoft!!!**, sem a necessidade de **Peering ou Gateway de VPN!!!**
{: .prompt-warning }


O artigo termina por aqui, massss deixo como recomendação uma pesquisa mais profunda sobre **Azure Private Link Service!!!**

Grato pela leitura e Até breve!!! 🍻🚀 
=======
| **Recurso**           | **Descrição**                |
| ----------------------| :---------------------------:|
| Resouce Group         | ```RG-Servidor```            |
| Region                | ```EAST US2```               |
| Image                 | ```Ubuntu Server 20.04 LTS```|
| Size                  | ```B1S``` ou ```B2S```       |
| Disk                  | ```SSD Standard```           |
>>>>>>> 0b63a838ea3efb6c82fc3315b8664980ec18851d:_posts/2023-02-20-azure-to-17-configurar-azure-private-link-service-portal.md
