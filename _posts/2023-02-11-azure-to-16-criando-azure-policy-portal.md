---
layout: post
title: "[Azure-To] #16 Criando Azure Policy [Portal]"
author: asilva
date: 2023-02-11 09:20 -0300
categories: [Cloud, Azure]
tags: [azure, microsoft, segurança, politicas, policy]
---

Fala galera! Seis tão baum?

Vamos para mais uma sequencia de artigos do Azure-To, então vamos nessa!

## **Sobre Azure Policy**

Se você é um administrador de nuvem, pode estar se perguntando como gerenciar o acesso, a conformidade e a segurança dos recursos em sua organização em nuvem. Azure Policy é uma solução que permite gerenciar e aplicar políticas em grande escala para controlar o acesso e o uso dos recursos do Azure. 

É uma ferramenta de governança para ajudá-lo a definir, aplicar e auditar políticas em sua organização, independentemente do tamanho ou complexidade. Neste post, vamos explorar o que é o Azure Policy, como ele funciona e como você pode usá-lo para manter sua infraestrutura em nuvem segura e em conformidade.

## **Objetivo**

Neste artigo, criaremos uma Política no Azure para restringir a implantação dos recursos do Azure em um local específico.

## **1.1 Criar uma atribuição de política**

Na página inicial do Portal do Azure, em **All services**, procure e selecione **Policy**, Na seção **Authoring**, clique em **Definitions**. Reserve um momento para revisar a lista de definições de políticas. 

![](/assets/img/58/policy1.png){: "width=60%" }

Por exemplo, no menu suspenso **Category**, selecione apenas **Compute**. Observe que **Allowed virtual machine size SKUs** permite que você especifique um conjunto de SKUs de máquinas virtuais que sua organização pode implantar.

![](/assets/img/58/policy2.png){: "width=60%" }

Retorne à página **Policy**, na seção **Authoring**, clique em **Assignments**. Uma atribuição é uma política que foi atribuída para ocorrer dentro de um escopo específico. Por exemplo, uma definição pode ser atribuída ao escopo da assinatura.

Clique em **Assign Policy** na parte superior da página **Policy - Assignments**.

Na página **Assign Policy**, mantenha o Escopo padrão.

- Scope: Use a assinatura padrão.
- Policy definition: Clique nas opções, e procure por **Allowed Locations**.
- Assignment name: **Regiões Liberadas**

![](/assets/img/58/policy3.png){: "width=60%" }

![](/assets/img/58/policy4.png){: "width=60%" }

Na guia Parameters, selecione **Brazil South**. 

![](/assets/img/58/policy5.png){: "width=60%" }

Clique em **Review+Create** e em **Create**.

![](/assets/img/58/policy6.png){: "width=60%" }

> Um escopo determina a quais recursos ou agrupamento de recursos a atribuição de política se aplica. Em nosso caso, poderíamos atribuir essa política a um grupo de recursos específico, mas optamos por atribuir a política no nível da assinatura. Esteja ciente de que os recursos podem ser excluídos com base na configuração do escopo. As exclusões são opcionais.
{: .prompt-info }

> A definição **Regiões Liberadas** especificará um local no qual todos os recursos devem ser implantados. Se um local diferente for escolhido, a implantação não será permitida. 
{: .prompt-info }

Para obter mais informações, consulte a página <a href="https://learn.microsoft.com/pt-br/azure/governance/policy/samples/" target="_blank">Exemplos do Azure Policy</a>

A atribuição **Regiões Liberadas** agora está listada no painel **Policy - Assignments** e está em vigor, aplicando a política no nível de escopo que especificamos **(nível de assinatura)**.

![](/assets/img/58/policy7.png){: "width=60%" }

## **2.1 Validando a política de localização permitida**

Nesta tarefa, validaremos a política **Regiões Liberadas** que criamos. 

Na página inicial do Portal do Azure, clique em '**Criar um recurso**' em seguida na página **Novo**, na caixa de Pesquisa, digite **Storage accounts** e clique em **+ Add, + Create, + New**.

![](/assets/img/58/policy8.png){: "width=60%" }

Configure a **storage accoun** (substitua xxxx no nome da conta de armazenamento por letras e dígitos de forma que o nome seja globalmente exclusivo). Deixe os padrões para todo o resto.

- Subscription: o nome da assinatura que você está usando neste laboratório
- Resource group: **rg-policy-lab**
- Storage account name: **stunilab001policy**
- Location: **(US) East US**

![](/assets/img/58/policy9.png){: "width=60%" }

Clique em **Review+Create** e, em seguida, clique em **Create**.

![](/assets/img/58/policy10.png){: "width=60%" }

Se tudo correr bem, você receberá o erro de **deployment failed** informando que o deploy do recurso não foi permitida pela política **Regiões Liberadas**.

![](/assets/img/58/policy11.png){: "width=60%" }

## **3.1 Excluir atribuição de política**

Excluiremos a atribuição de política para garantir que não sejamos bloqueados em nenhum trabalho futuro.

Na página inicial do Portal do Azure, procure e selecione **Policy**.

![](/assets/img/58/policy12.png){: "width=60%" }

> Na aba **Policy** você pode visualizar o estado de conformidade das várias políticas que atribuiu.
{: .prompt-tip }

> A política de regiões permitidas pode mostrar recursos não compatíveis. Nesse caso, esses são recursos criados antes da atribuição de política.
{: .prompt-warning }

Clique em **Regiões Liberadas**, irá abrir uma janela com a conformidade da política regiões liberadas.

![](/assets/img/58/policy13.png){: "width=60%" }

Clique em **Delete Assignment** no menu superior. Confirme que deseja excluir a atribuição de política clicando em **Yes**.

![](/assets/img/58/policy14.png){: "width=60%" }

## **3.2 Validando a criação de recursos sem a atribuição de política**

Volte ao item **2.1 Validando a política de localização permitida** e tente criar novamente a **storage accoun** para garantir que a política não esteja mais em vigor.

O resultado deve ser o deploy do recurso sem restrições.

![](/assets/img/58/policy15.png){: "width=60%" }

É isso galera, espero que gostem!

Forte Abraço!