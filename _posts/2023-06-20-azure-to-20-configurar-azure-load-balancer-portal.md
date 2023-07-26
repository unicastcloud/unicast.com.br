---
layout: post
title: "[Azure-To] #20 Configurar Azure Load Balancer [Portal]"
author: asilva
date: 2023-06-20 09:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, infraestrutura, networking, loadbalancer]
---

Fala galera! Seis tão baum?

Faz um tempinho que não escrevo um artigos sobre Azure, então vamos lá para mais uma sequencia de artigos do Azure-To!

### **Sobre Azure Load Balancer**

O Azure Load Balancer é um serviço de balanceamento de carga de rede oferecido pela Microsoft Azure. Ele ajuda a distribuir o tráfego de entrada entre várias máquinas virtuais (VMs) ou instâncias de máquinas físicas em uma mesma rede, garantindo assim que os recursos sejam utilizados de maneira eficiente e que as aplicações permaneçam disponíveis e responsivas.

Principais características do Azure Load Balancer:

- **Balanceamento de Carga**: O serviço distribui o tráfego de entrada de forma equitativa entre as VMs ou instâncias de máquinas físicas, evitando sobrecargas em um único servidor.
- **Redundância**: O Load Balancer oferece alta disponibilidade, garantindo que, se um dos balanceadores falhar, outro assumirá suas funções, garantindo a continuidade do serviço.
- **Protocolos**: Ele suporta o balanceamento de carga para diferentes protocolos, como TCP, UDP, HTTP e HTTPS.
- **Balanceamento de carga interno e externo**: O Azure Load Balancer pode ser usado tanto para distribuir o tráfego entre máquinas dentro de uma rede (balanceamento de carga interno) quanto para disponibilizar aplicações para o público externo (balanceamento de carga externo).
- **Portas personalizadas**: É possível configurar o serviço para equilibrar o tráfego em portas específicas, o que é útil para diferentes tipos de aplicações e serviços.
- **Backend Pools**: As VMs ou instâncias físicas são agrupadas em "pools" de backend, permitindo o gerenciamento conjunto do tráfego direcionado a esse grupo de recursos.
- **Health Probes**: O Azure Load Balancer realiza verificações de integridade das VMs ou instâncias físicas para garantir que apenas servidores saudáveis recebam tráfego.
- **Session Persistence**: Para algumas aplicações que requerem que uma mesma sessão seja direcionada sempre para a mesma VM, o serviço suporta "session persistence" (persistência de sessão).

### **Objetivo**

Neste artigo, vamos criar um balanceador de carga interno.

As etapas para criar um balanceador de carga interno são muito semelhantes para criar um balanceador de carga público. A principal diferença é que, com um balanceador de carga público, o front-end é acessado por meio de um endereço IP público e você testa a conectividade de um host localizado fora de sua rede virtual; considerando que, com um balanceador de carga interno, o front-end é um endereço IP privado dentro de sua rede virtual e você testa a conectividade de um host dentro da mesma rede.

O diagrama abaixo ilustra o ambiente que você implantará neste exercício.

![](/assets/img/71/lb01.png){: "width=60%" }

### **1.1 Criando a Rede Virtual**

Para iniciar nosso laboratório, precisamos criar nossa VNET e a sub-rede que iremos utilizar em nosso back-end.

Na página inicial do Portal do Azure, clique em ‘**Create a resource**’ em seguida na página Novo, na caixa de Pesquisa, digite **Virtual Network** e clique em + **Add, + Create, + New.**

![](/assets/img/71/lb02.png){: "width=60%" }

Forneça os seguintes detalhes básicos para a nova VNET.

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Virtual network name: **vnet-unicast-lb**
- Region: **(US) East US**

![](/assets/img/71/lb03.png){: "width=60%" }

Avance para a sessão **IP addresses** e fornece os seguintes detalhes:

**Back-end subnet:**

Address range: **10.1.0.0/16**
Subnet name: **snet-backend-01**
Address range: **10.1.0.0/24**

**Front-end subnet:**

Address range: **10.1.0.0/16**
Subnet name: **snet-backend-01**
Address range: **10.1.0.0/24**

![](/assets/img/71/lb04.png){: "width=60%" }

Volte na sessão Security e habilite a opção **Enable Azure Bastion**.

Azure Bastion host name: **unicastBastion**

![](/assets/img/71/lb05.png){: "width=60%" }

Veja que ele já cria automaticamente a subnet especial para o Azure Bastion.

![](/assets/img/71/lb06.png){: "width=60%" }

Estamos ativando essa opção para poder fazer o acesso a nossa VM de testes via **Azure Bastion** e testar nosso load balancer interno.

Selecione **Review + create**.

Revise suas configurações e clique em **Create**.

![](/assets/img/71/lb07.png){: "width=60%" }

### **2.1 Criando servidores de back-end**

Agora, vamos criar três VMs, que estarão no mesmo conjunto de disponibilidade, para o pool de back-end do balanceador de carga, o script abaixo adicionará as VMs ao pool de back-end e instalará o IIS nas três VMs para testar o balanceador de carga.

Basta fazer o download no seguinte repositório: <a href="https://github.com/asilvajunior/azure-script-tools/tree/main/Azure%20VM%20IIS%20(Load%20Balancer)%20" target="_blank"> Azure VM IIS (Load Balancer)</a>

Siga os seguintes passos:

No portal do Azure, abra uma sessão do **Cloud Shell com PowerShell**.

![](/assets/img/71/lb08.png){: "width=60%" }

Na barra de ferramentas do painel Cloud Shell, selecione o ícone **Upload/Download**  arquivos, no menu suspenso, selecione **Upload** e carregue os arquivos um por um.

Agora, basta executar o seguinte comando:

````powershell
$RGName = "rg-unicast-lb"

New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile 01_az_backend.json -TemplateParameterFile 02_az_backend.parameters_vm1.json
New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile 01_az_backend.json -TemplateParameterFile 03_az_backend.parameters_vm2.json
New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile 01_az_backend.json -TemplateParameterFile 04_az_backend.parameters_vm3.json
````

![](/assets/img/71/lb09.png){: "width=60%" }

>Observação : você será solicitado a fornecer uma senha de administrador.
{: .prompt-info }

Quando o deploy estiver concluído, acesse a página inicial do portal do Azure, selecione Máquinas Virtuais e verifique se ambas as máquinas virtuais foram criadas.

![](/assets/img/71/lb10.png){: "width=60%" }

>Pode levar de 5 a 10 minutos para criar essas três VMs. Você não precisa esperar até que este trabalho seja concluído, você já pode continuar com a próxima tarefa.
{: .prompt-info }

### **3.1. Criando o Load Balancer**



### **4.1 Criando os recursos do Load Balancer**



### **5.1 Criar máquina virtual para validação e testes**



### **6.1 Testando o Load Balancer**






Parabéns! Você configurou e testou um **Load Balancer** do Azure.

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

É isso galera, espero que gostem!

Forte Abraço!
