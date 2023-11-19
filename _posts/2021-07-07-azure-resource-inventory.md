---
layout: post
title: "Azure Resource Inventory"
author: asilva
date: 2021-07-07 09:00:00 -0500
categories: [Cloud, Azure]
tags: [arquitetura, azure, microsoft, ferramentas, ari, resourceinventory]
---

Fala galera! Seis tão baum?

Se você ainda não precisou, em algum momento você irá precisar mover seus recursos do Azure para outro grupo de recursos ou assinaturas.

Se você já trabalha como arquiteto em cloud Azure ou na área de projetos, já deve estar familiarizado com essa demanda. É muito comum se deparar com esse tipo de projeto, seja ela por motivos de organização de ambiente ou mesmo gestão de faturamento.

A princípio parece uma tarefa muito simples, uma vez que você pretende mover recursos que já estão no Azure, mas tenha calma, nem tudo é simples como parece.

Sempre que se deparar com esse tipo de demanda, você precisa considerar que nem todos os recursos do Azure suportam movimentações/migrações.

Antes de iniciar seu projeto, sugiro que você verifique algumas etapas importantes, antes de mover um recurso no Azure.

**Lista de verificações antes de mover recursos:** <a href="https://docs.microsoft.com/pt-br/azure/azure-resource-manager/management/move-resource-group-and-subscription#checklist-before-moving-resources" target="_blank">Mover recursos para uma nova assinatura ou grupo de recursos - Azure Resource Manager | Microsoft Docs.</a>

Após essa verificação inicial, é importante que você conheça quais recursos estão habilitados a movimentação/migração.

**A lista completa de serviços pode ser conferida aqui:** <a href="https://docs.microsoft.com/pt-br/azure/azure-resource-manager/management/move-support-resources" target="_blank">Mover suporte de operação por tipo de recurso - Azure Resource Manager | Microsoft Docs.</a>

Após ter todas essas informações em mãos, o próximo passo e fazer uma avaliação do ambiente que será migrado.

Levantar todos os recursos, e fazer uma análise detalhada de quais recursos serão compatíveis de movimentação.

Neste artigo, eu vou mostrar como você pode mapear todos os recursos de sua assinatura de uma forma simples e bem intuitiva.

O **Azure Resource Inventory** é um poderoso script escrito em powershell para gerar relatórios em Excel de qualquer ambiente Azure.

**Link do projeto:** <a href="https://github.com/azureinventory/ARI" target="_blank">ARI: Azure Resource Inventory - It's a Powerful tool to create EXCEL inventory from Azure Resources with low effort (github.com)</a>

Para utilizar o script é muito simples.

Você pode rodar o script localmente via **Powershell** ou pelo portal utilizando o **Cloudshell**.

## **Pré-requisitos**

1. Install-Module ImportExcel
2. Az CLI
3. az extension add --name resource-graph

Após instalar os módulos, você já pode executar o script.

Assim que você rodar o script, ele abrirá o navegador para que você possa autenticar sua conta. Basta fazer o login que ele começar a coletar as informações necessárias.

![](/assets/img/07/ari1.png){: "width=60%" }

Assim que terminar a coleta, o script salva as informações em um arquivo Excel no **C:\AzureResourceInventory.**

Agora, é só verificar as informações e planejar seu projeto de movimentação/migração da melhor maneira possível.

![](/assets/img/07/ari2.png){: "width=60%" }

Espero que gostem.

Forte abraço!