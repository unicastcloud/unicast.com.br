---
layout: post
title: "Protegendo recursos com Azure Locks"
author: asilva
date: 2023-01-04 09:00:00 -0300
categories: [Azure, Infraestrutura]
tags: [azure, microsoft, resources, locks]
---

Fala galera! Seis tão baum?

Neste artigo, discutiremos sobre como proteger, previnir e impedir a exclusão de recursos ou alterações inesperadas usando locks. 

Uma preocupação comum com os recursos provisionados no Azure é a facilidade com que eles podem ser excluídos. Um administrador descuidado pode acidentalmente apagar meses de trabalho com alguns cliques errados. Mas fique calmo, o Azure Locks pode ajudar aqui. O Azure Locks permiti que as organizações coloquem uma estrutura predefinida que impede a exclusão acidental de recursos em seu ambiente do Azure.

### **O que é o Azure Locks?**

O Azure Locks fornece uma maneira de bloquear os recursos do Azure para evitar a exclusão ou alteração de um recurso. Esses bloqueios ficam fora da hierarquia de controles de acesso baseados em função (RBAC) e, quando aplicados, impõem restrições ao recurso para todos os usuários. Eles são muito úteis quando você tem um recurso importante em sua assinatura que os usuários não devem poder excluir ou alterar e podem ajudar a evitar alterações ou exclusões acidentais e maliciosas.

Existem dois tipos de bloqueios de recursos que podem ser aplicados:

- **CanNotDelete** : isso impede que alguém exclua um recurso enquanto o bloqueio estiver em vigor. No entanto, ele pode sofrer alterações.
- **ReadOnly** : como o nome sugere, torna o recurso somente leitura, portanto, nenhuma alteração pode ser feita e ele não pode ser excluído.

>Somente as funções Proprietário e Administrador de acesso do usuário podem criar ou excluir bloqueios de gerenciamento.
{: .prompt-info }

Os bloqueios podem ser aplicados a assinaturas, grupos de recursos ou recursos individuais, conforme necessário. Quando você bloqueia uma assinatura, todos os recursos dessa assinatura (incluindo os adicionados posteriormente) herdam o mesmo bloqueio. Uma vez aplicados, esses bloqueios afetam todos os usuários, independentemente de suas funções. Se for necessário excluir ou alterar um recurso com um bloqueio em vigor, o bloqueio precisará ser removido antes que isso ocorra.

### **Configurando Azure Locks em seus recursos**

1. Localize o recurso que deseja bloquear e selecione-o. Na tela principal, clique no ícone **“Locks”**
2. Clique em **add**.
3. Dê um nome e uma descrição ao bloqueio e selecione o tipo, **delete** ou **ready only**.
4. Clique em **OK** para salvar o bloqueio: O recurso agora está protegido.
5. Para remover o bloqueio, basta voltar à mesma interface, selecionar o bloqueio e, em seguida, excluir.

![](/assets/img/46/locks01.png){: .shadow style="max-width: 70%" }

É isso galera, espero que gostem!

Forte Abraço!