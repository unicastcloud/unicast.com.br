---
layout: post
title: "[Azure-To] #14 Configurar Azure Key Vault [Portal]"
author: asilva
date: 2023-01-31 09:00:00 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, keyvault, secrets]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

### **Sobre Azure Key Vault**

O Azure Key Vault é um serviço em nuvem da Microsoft que oferece um armazenamento seguro e centralizado para chaves criptográficas, senhas, certificados e outros segredos. Ele usa técnicas de criptografia avançadas para proteger esses dados e controla o acesso a eles por meio de autenticação e autorização rigorosas.

Uma das características importantes do Azure Key Vault é a sua integração com outros serviços do Azure, como o Azure Active Directory e o Azure Active Directory Domain Services, para gerenciar o acesso e as políticas de segurança. Ele também suporta a criação e o gerenciamento de chaves criptográficas geradas por hardware, como HSMs (Hardware Security Modules), que garantem uma proteção ainda mais robusta.

Outra característica importante do Azure Key Vault é a sua facilidade de uso e escalabilidade, permitindo que desenvolvedores e empresas gerenciem com facilidade seus segredos e chaves criptográficas, independentemente do tamanho ou complexidade do projeto. Além disso, o Azure Key Vault oferece recursos de auditoria, monitoramento e logging para ajudar a detectar e resolver possíveis ameaças à segurança.

### **Objetivo**

Neste artigo, criaremos um cofre de chaves do Azure e, em seguida, criaremos um segredo de senha dentro desse cofre de chaves, fornecendo uma senha armazenada com segurança e gerenciada centralmente para uso com aplicativos.

### **1.1 Criando o Azure Key Vault**

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo**, na caixa de Pesquisa, digite **Key vaults** e clique em **+ Add, + Create, + New**.

![](/assets/img/56/keyvaul1.png){: "width=60%" }

Configure o cofre de chaves (substitua xxxx no nome do cofre de chaves por letras e dígitos de modo que o nome seja globalmente exclusivo). Deixe os padrões para todo o resto.

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: selecione seu grupo de recursos ou crie um novo.
- Key vault name: **keyvaulttestxxx**
- Location: **East US**
- Pricing tier: **Standard**

![](/assets/img/56/keyvault2.png){: "width=60%" }

Clique em **Review+Create** e, em seguida, clique em **Create**. Depois que o novo cofre de chaves for provisionado, clique em **Go to resource**.

1. Clique na guia **Overview** do cofre de chaves e anote o URI do cofre. Os aplicativos que usam seu cofre por meio das APIs REST precisarão desse URI.

![](/assets/img/56/keyvault3.png){: "width=60%" }

2. Reserve um momento para navegar por algumas das outras opções de cofre de chaves. Em **Settings**, revise **Keys, Secrets, Certificates, Access Policies, Firewalls and Virtual Networks.**

>**Observação:** sua conta do Azure é a única autorizada a realizar operações neste novo cofre. Você pode modificar isso se desejar na seção Configurações e, em seguida, na seção Políticas de acesso.
{: .prompt-warning }

### **2.1 Adicionando secrets no Azure Key Vault**

1. Em **Settings**, clique em **Secrets** e, em seguida, clique em **+Generate/Import.**

![](/assets/img/56/keyvault4.png){: "width=60%" }

2. Configure o segredo. Deixe os outros valores em seus padrões. Observe que você pode definir uma data de ativação e expiração.

Upload options: **Manual**
Name: **UnilabSecret**
Value: ouniLAB007

![](/assets/img/56/keyvault5.png){: "width=60%" }

3. Clique em **Create.**
4. Assim que o segredo for criado com sucesso, clique no botão **UnilabSecret** e observe que ela tem o status **Enabled**.

![](/assets/img/56/keyvault6.png){: "width=60%" }

5. Selecione o segredo que você acabou de criar, observe o **Secret Identifier**. Este é o valor de url que agora você pode usar com aplicativos. Ele fornece uma senha gerenciada central e armazenada com segurança.

![](/assets/img/56/keyvault7.png){: "width=60%" }

6. Clique no botão **Show Secret Value**, para exibir a senha que você especificou anteriormente.

![](/assets/img/56/keyvault8.png){: "width=60%" }

Parabéns! Você criou um cofre de chaves do Azure e, em seguida, criou um segredo de senha nesse cofre de chaves, fornecendo uma senha armazenada com segurança e gerenciada centralmente para uso com aplicativos.

É isso galera, espero que gostem!

Forte Abraço!