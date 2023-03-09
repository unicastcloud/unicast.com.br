---
layout: post
title: "[Azure-To] #18 Automatizando Exclusão de Resource Group com Tags [Portal]"
author: [rpaliosa]
date: 2023-03-01 09:00 -0300
categories: [Azure, Azure-To]
tags: [azure, microsoft,management, tags, resourcegroup, cloudshell, azurecli, az104]
---

### Saudações Pessoal!!!

No cardápio de hoje vamos servir uma **Automação para Exclusão de Vários Grupos de Recursos do Azure através de Tags**.

### **Sobre Azure Tags**

**TAGS ou Marcas**, tem o propósito de **Identificar Recursos no ambiente Cloud** como se fosse uma "**etiqueta, post it, crachá**" ou qualquer outra coisa que você associe com identificação" e também não é uma exclusividade do Azure, **TAGS** estão presentes na AWS e GCP!!!

Pra quem trilhou a **AZ 900**, a **TAG** tem uma abordagem voltada para auxiliar no **Gerenciamento de Custos**, pois ao gerar relatórios de custo e fatura de cobrança, é possível verificar através da **"Etiqueta"** quem ou qual setor ou projeto fez o consumo. 

Masssssssss..... a **TAG** vai muito além de ajudar a galera da Gestão Financeira, ela também permite que **Automações** que evitam tarefas repetitivas sejam executas.

O que apresento a seguir também é uma pequena demonstração do conceito da **Infraestrutura como Código ou IaaC**, cujo objetivo é eliminar tarefas repetitivas aumentando a agilidade no trabalho.

### **Objetivo**

O objetivo deste artigo é **Utilizar TAG para excluir vários Grupos de Recursos com apenas 1 linha de Comando do Azure CLI**.

>**Observação:** Para quem pretende se aprofundar na Infraestrutura Cloud, seja Azure, AWS ou GCP, IaaC é um prato obrigatório e a entrada deve começar pelo **Azure CLI** 
<a href="https://learn.microsoft.com/pt-br/cli/azure/get-started-with-azure-cli" target="_blank">https://learn.microsoft.com/pt-br/cli/azure/get-started-with-azure-cli</a> ou pelo **Azure PowerShell** <a href="https://learn.microsoft.com/pt-br/powershell/azure/get-started-azureps?view=azps-9.4.0" target="_blank">https://learn.microsoft.com/pt-br/powershell/azure/get-started-azureps?view=azps-9.4.0</a>

{: .prompt-warning }

### **1. Criar Grupo de Recursos e vincular 1 VNET a cada um**

1.1 - Crie um Grupo de Recursos na Região Brazil e vincule uma Virtual Network (VNET) padrão, **Sem se preocupar com Range de IP e Subnet!!!**  

![](/assets/img/61/azure-to-18-tag1.png){: "width=60%" }
---
![](/assets/img/61/azure-to-18-tag2.png){: "width=60%" }
---
![](/assets/img/61/azure-to-18-tag3.png){: "width=60%" }


1.2 - Seguindo os passos anteriores, crie outro Grupo de Recursos na Região EAST US e outro em France Central, também vinculando a cada um deles uma VNET Padrão.

1.3 - No final destas etapas, o cenário deve estar conforme abaixo:

![](/assets/img/61/azure-to-18-tag4.png){: "width=60%" }
---
![](/assets/img/61/azure-to-18-tag5.png){: "width=60%" }
---
![](/assets/img/61/azure-to-18-tag6.png){: "width=60%" }
---

1.4 - Retorne ao Painel de Administração do Grupo de Recursos.

![](/assets/img/61/azure-to-18-tag7.png){: "width=60%" }

### **2. Inserir TAG aos Grupos de Recursos que serão Excluídos**

Ok, agora é o momento em que a **TAG** entra em ação!<br>
Vamos **Identificar** estes 3 Grupos de Recursos para que sejam **excluídos simultaneamente e sem esforço repetitivo.**

2.1 - Selecione os **Grupos de Recursos Brazil, East US e France**.

![](/assets/img/61/azure-to-18-tag8.png){: "width=60%" }

2.2 - Clique em **Assign Tags**.

![](/assets/img/61/azure-to-18-tag9.png){: "width=60%" }

2.3 - No campo **Name** digite **excluir-tudo** e clique em **Save** .

![](/assets/img/61/azure-to-18-tag10.png){: "width=60%" }

2.4 - Clique no ícone do **Cloud Shell** para acesso a **Console de Linha do Comando** e caso seja exibido o alerta **"Você não tem nenhum armazenamento montado"**, clique em **Criar Armazenamento**. <br>
Isto ocorre porque o Cloud Shell cria um **Storage Account** na primeira vez que o recurso é acionado!!!

![](/assets/img/61/azure-to-18-tag11.png){: "width=60%" }
---
![](/assets/img/61/azure-to-18-tag12.png){: "width=60%" }

2.5 - Se esta é a sua 1ª vez no Cloud Shell, Welcome!!!<br>
Será neste ambiente que a **"brincadeira"** vai acontecer rs rs. Por Padrão o Shell carrega o ambiente de **PowerShell**, mas vamos **brincar** no ambiente **Bash (Linux)**.

![](/assets/img/61/azure-to-18-tag13.png){: "width=60%" }
---
![](/assets/img/61/azure-to-18-tag14.png){: "width=60%" }

### **3. Executando a Automação**

3.1 - O 1º passo é listar de forma resumida os **Grupos de Recursos**  que possuem a **TAG** **"excluir-tudo"** através do comando <br>
```az group list --tag=excluir-tudo -otable```

>**Atenção:** Copie e cole ou digite o comando acima exatamente como está!!!
{: .prompt-warning }

O **Parâmetro -otable** faz a exibição na tela de forma resumida!!!

![](/assets/img/61/azure-to-18-tag15.png){: "width=60%" }


3.2 - Fogooooo!!!!

>**Atenção:** O próximo passo vai excluir **DEFINITIVAMENTE**  os Grupos de Recursos com a **TAG "excluir-tudo**. Mesmo que seja em ambiente de teste, cuidado para não **Taguear** o Grupo de Recurso errado!!! 
{: .prompt-warning }

Execute o comando abaixo, **Aguarde alguns minutos** e veja a **mágica** acontecer. 
*Quanto mais recursos vinculados ao Grupo de Recursos, maior será o tempo de espera!!!*

```
az group list --tag=excluir-tudo --query [].name -o tsv | xargs -otl az group delete --no-wait  -n
```

![](/assets/img/61/azure-to-18-tag16.png){: "width=60%" }

3.3 - Confirme com **y** para **excluir**.

![](/assets/img/61/azure-to-18-tag17.png){: "width=60%" }

>**Observação:** O Comando acima procura **(--query)** quais os **Grupos de Recursos** possuem a **TAG "excluir-tudo"**, armazena em uma variável e associado a parâmetros exigidos pelo **az group delete** completa a exclusão total!!!
{: .prompt-warning }

3.4 - O Processo de exclusão deste cenário deve demorar +/- 3 minutos. <br>
Digite novamente o comando 
```az group list --tag=excluir-tudo -otable``` para confirmar que tudo foi excluído e também confirme no Portal de Administração do Grupo de Recurso do Azure!!!.

![](/assets/img/61/azure-to-18-tag18.png){: "width=60%" }
---

Bommmmm, o artigo termina por aqui, massss deixo como recomendação um Hands On da **TFTEC** onde o **Raphael Andrade** apresenta no seu Canal do <a href="https://www.youtube.com/watch?v=HkW5zpGx40w" target="_blank">Youtube</a>  a como **Criar uma Automação Start / Stop de Máquinas Virtuais** também com o auxílio de **TAGS** 
 
https://www.youtube.com/watch?v=HkW5zpGx40w

Tkssss pela leitura e Até breve!!! 🍻🚀 