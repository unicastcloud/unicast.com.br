---
layout: post
title: "[Azure-To] #21 Configurar Azure Application Gateway [Portal]"
author: asilva
date: 2023-06-25 09:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, infraestrutura, networking, loadbalancer, waf, gateway, applicationgateway]
---

Fala galera! Seis tão baum?

Vamos lá para mais uma sequência de artigos sobre Load Balancers no Microsoft Azure!

### **Sobre Azure Application Gateway**

O **Azure Application Gateway** é um serviço de entrega de aplicativos web da Microsoft Azure, projetado para otimizar o tráfego de aplicativos e melhorar a segurança, o desempenho e a escalabilidade de aplicações baseadas na web.

Aqui estão alguns pontos-chave sobre o Azure Application Gateway:

- Balanceamento de Carga
- Roteamento Baseado em URL
- SSL Offloading
- Web Application Firewall (WAF)
- Redimensionamento Automático:
- Health Probes
- Integração com Azure Monitor
- Suporte a Vários Protocolos

### **Objetivo**

Neste artigo, criaremos uma nova rede virtual para o Application Gateway. As instâncias do Application Gateway são criadas em sub-redes separadas. E por isso,precisamos criar duas sub-redes: uma para o Application Gateway e outra para os servidores de back-end.

Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site no Application Gateway, uma regra de roteamento de solicitação básica e duas máquinas virtuais no pool de back-end.

### **1.1 Criando o Application Gateway**

Na página inicial do Portal do Azure, clique em ‘**Create a resource**’ em seguida na página Novo, na caixa de Pesquisa, digite **Application Gateway** e clique em + Add, + Create, + New.

![](/assets/img/73/appgw01.png){: "width=60%" }

Forneça os seguintes detalhes básicos para a nova instância do application gateway.

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Application Gateway: **UnicastAppGateway**
- Region: **(US) East US**
- Virtual Network: **Create new**

![](/assets/img/73/appgw02.png){: "width=60%" }

Para VNET, forneça os seguintes detalhes:

- Name: **vnet-unicast-appw**
- Address range: **10.0.0.0/16**
- Subnet name: **snet-appgw-01**

![](/assets/img/73/appgw03.png){: "width=60%" }

Após a criação do recurso, adicionar a subnet de backend à VNET.

- Address range: **10.0.0.0/24**
- Subnet name: **snet-backend-01**
- Address range: **10.0.1.0/24**

![](/assets/img/73/appgw04.png){: "width=60%" }

Aceite os valores padrão para as outras configurações e selecione avançar.

Na guia **Frontends**, verifique se o tipo de endereço IP do frontend está definido como Público.

Selecione Adicionar novo para o endereço IP público e digite **pip-aapgw-01** para o nome do endereço IP público e selecione OK.

![](/assets/img/73/appgw05.png){: "width=60%" }

Na guia **Backends**, selecione Adicionar um **backend pool**.

Insira os seguintes valores para criar um backend pool vazio:

![](/assets/img/73/appgw06.png){: "width=60%" }

Na guia **Configuration**, você conectará o pool de front-end e back-end usando uma regra de roteamento.

Na coluna **Routing rules**, selecione Adicionar uma regra de roteamento.

![](/assets/img/73/appgw07.png){: "width=60%" }

Na guia **Listener**, insira ou selecione as seguintes informações:

![](/assets/img/73/appgw08.png){: "width=60%" }

Na guia **Backend targets**, insira ou selecione as seguintes informações:

![](/assets/img/73/appgw09.png){: "width=60%" }

![](/assets/img/73/appgw10.png){: "width=60%" }

Aceite os valores padrão para as outras configurações.

![](/assets/img/73/appgw11.png){: "width=60%" }

Selecione Next **Tags**, e em seguida, **Review + create**.

Revise as configurações na guia **Review + create**

![](/assets/img/73/appgw12.png){: "width=60%" }

### **2.1 Criar máquinas virtuais para validação e testes**

Agora, precisamos validar se nosso Application Gateway está funcionando corretamente.

Para isso, vamos criar duas máquinas virtuais e instalar o IIS em cada uma delas.

Como é um processo simples e já documentado aqui no site, não vou me atentar em descrever todos os passos.

Você pode consultar os seguintes artigos:

- [Azure-To] #1 Deploy de Máquina Virtual [Portal](https://unicast.com.br/posts/azure-to-1-deploy-de-maquina-virtual-portal/)
- [Azure-To] #2 Deploy de Máquina Virtual [PowerShell](https://unicast.com.br/posts/azure-to-2-deploy-de-maquina-virtual-powershell/)
- [Azure-To] #3 Deploy de Máquina Virtual [Azure CLI](https://unicast.com.br/posts/azure-to-3-deploy-de-maquina-virtual-azure-cli/)

Basta escolher um método para o deploy e adicionar as duas VMS na rede virtual (VNET) que criamos.

Porém, para facilitar este laboratório, já deixei um template pronto para adicionar as 2 VMs, adiciona-las em nossa VNET e instalar o IIS em cada uma.

Basta fazer o download no seguinte repositório: <a href="https://github.com/asilvajunior/azure-script-tools/tree/main/Azure%20VM%20IIS%20(APPGW)" target="_blank"> Azure VM IIS (APPGW)</a>

Siga os seguintes passos:

No portal do Azure, abra uma sessão do Cloud Shell com PowerShell.

![](/assets/img/73/appgw13.png){: "width=60%" }

Na barra de ferramentas do painel Cloud Shell, selecione o ícone **Upload/Download**  arquivos, no menu suspenso, selecione **Upload** e carregue os seguintes arquivos `01_az_backend.json` e `02_backend.parameters.json` no diretório inicial do Cloud Shell, um por um.

Agora, basta executar o seguinte comando:

````powershell
$RGName = "rg-unicast-appgw"
New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile 01_az_backend.json -TemplateParameterFile 02_az_backend.parameters.json
````

![](/assets/img/73/appgw14.png){: "width=60%" }

Quando o deploy estiver concluído, acesse a página inicial do portal do Azure, selecione Máquinas Virtuais e verifique se ambas as máquinas virtuais foram criadas.

![](/assets/img/73/appgw15.png){: "width=60%" }

### **3.1. Adicionando backend servers no backend pool**

No menu portal do Azure, selecione **All resources** ou procure e selecione Todos os recursos. Em seguida, selecione o Application Gateway criado.

1. Em **Settings**, selecione **Backend pools**.
2. Selecione **BackendPool**.
3. Na página de edição, em **Backend targets**, **Target type**, selecione Máquina virtual e adicione as duas máquinas virtuais criadas.

![](/assets/img/73/appgw16.png){: "width=60%" }

### **4.1 Testando o Application Gateway**

Vá até a aba **Overview** do application gateway, copie o IP público e cole em seu navegador.

![](/assets/img/73/appgw17.png){: "width=60%" }

Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e pode se conectar com êxito ao back-end.

![](/assets/img/73/appgw18.png){: "width=60%" }

Atualize o navegador várias vezes e você verá as conexões com a VM1 e VM2.

![](/assets/img/73/appgw19.png){: "width=60%" }

Parabéns! Você configurou e testou um **Application Gateway** do Azure.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
