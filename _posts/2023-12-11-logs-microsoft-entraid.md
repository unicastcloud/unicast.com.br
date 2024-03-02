---
layout: post
title: "Logs de Entrada e Auditoria no Microsoft Entra ID [Portal]"
author: [rpaliosa]
date: 2023-12-11 00:01 -0300
categories: [Cloud, Azure]
tags: [Azure, Azure Active directory, Microsoft Entra, Logs Entrada, Logs Auditoria, Identidade]
---

## Saudações Pessoal!!!

Em um passado não muito distante em que a "Operação Lava Jato" tinha grande destaque nos meios de comunicação brasileiro, lemos e ouvimos muito a frase **"Seguindo o rastro do dinheiro é possível encontrar o  criminoso!!!"**. 

Dentro do nosso planeta tecnológico, também temos o privilégio de possuirmos frases de impacto, como algo parecido com **"Analise os Logs e encontre respostas"**, ou **"Sem Logs, Sem Culpados rs rs rs"** ou também **"Quem Não gera Logs, Não arruma confusão rs rs"**. O Fato é que 
Simmmm, os Logs possuem alta relevância e por este motivo precisam ser muito bem projetados para que ajudem na análise, diagnóstico, segurança, auditoria e melhoria dos ambientes em que puder ser aplicado.

Focando agora no Planeta Microsoft, o seu gerenciador de identidade moderno para Azure e 365, antes chamado de **Azure Active Directory** e agora batizado como **Microsoft Entra** <a href="https://entra.microsoft.com" target="_blank"> **[https://entra.microsoft.com]**</a>, disponibiliza até este presente momento em sua área de **Monitoramento e Integridade**, 3 opções de Logs de altíssima relevância, **"Logs de Entrada, Logs de Auditoria e Logs de Provisionamento"**.  

![](/assets/img/78/01.png){: "width=60%" }

A seguir trago um overview com pontos que considero relevântes, masssss é somente a pontinha do Iceberg. Este é um tema que faz parte da jornada de certificação SC-300 - Microsoft Identity and Access Administrator e na minha opnião, merece um estudo muito mais profundo.

## 1 - [Sign Logs] Logs de Entrada 

Os logs de entrada fornecidos pelo Microsoft Entra ID auxilia a responder a perguntas como:

- Quantos utilizadores iniciaram sessão numa determinada aplicação esta semana.
- Quantas tentativas de início de sessão falhadas ocorreram nas últimas 24 horas.
- Os utilizadores iniciam sessão a partir de browsers, App, e qual Sistema Operacional.
- Quais recursos do Azure estão sendo acessados por identidades gerenciadas e entidades de serviço.
- A identidade (Utilizador) que realizou o início de sessão.
- O cliente (Aplicativo) usado para o login.
- O destino (Recurso) acessado pela identidade.

Atualmente é possível visualizar 4 tipos de logs de entrada:

- Login de usuário interativo
- Entradas de usuário não interativas
- Entradas na entidade de serviço
- Entradas de identidade gerenciadas

![](/assets/img/78/02.png){: "width=60%" }

>**Observação:** Os logs do Microsoft Entra não podem ser alterados ou excluídos, mas há um período padrão para a   retenção dos dados, conforme tabela a seguir. A <a href="https://learn.microsoft.com/pt-br/entra/identity/monitoring-health/reference-reports-data-retention?source=recommendations" target="_blank"> **Doc oficial** </a> apresenta soluções para períodos de retenção maiores do que o período padrão!!!
{: .prompt-warning }

![](/assets/img/78/03.png){: "width=60%" }

### **1.1 - Login de usuário interativo** 

Os logins interativos são realizados por um usuário e mostram entre outros resultados:
- Localização
- Status do Acesso Condicional

### **1.2 - Entradas de usuário não interativas**

Os logins não interativos são feitos em nome de um usuário. Essas entradas geralmente foram executadas por um aplicativo cliente ou componentes do sistema operacional em nome de um usuário.

### **1.3 - Entradas na entidade de serviço**

 Entradas na entidade de serviço não envolvem um utilizador, pois são entradas por qualquer conta de não usuário, como aplicativos ou entidades de serviço (exceto o login de identidade gerenciada).

 Nessas entradas, o aplicativo ou serviço fornece sua própria credencial, como um certificado ou segredo de aplicativo para autenticar ou acessar recursos.

### **1.4 - Entradas de identidade gerenciadas**

Estas são entradas que foram executadas por recursos Azure para simplificar o gerenciamento de credenciais. Uma VM com credenciais gerenciadas usa o Microsoft Entra ID para obter um Token de Acesso.


## 2 - [Audit Logs] Logs de Auditoria 

Logs de auditoria mostra um relatório abrangente sobre cada evento registrado no Microsoft Entra ID, fornecendo respostas para perguntas relacionadas a usuários, grupos e aplicativos, como:

**Usuários:**
- Quais tipos de alterações foram aplicadas recentemente aos usuários
- Quantos usuários foram alterados
- Quantas senhas foram alteradas

**Grupos:**
- Quais grupos foram adicionados recentemente
- Se proprietários do grupo foram alterados
- Quais licenças foram atribuídas a um grupo ou a um usuário

**Aplicativos:**
- Quais aplicativos foram adicionados, atualizados ou removidos
- Se uma entidade de serviço para um aplicativo foi alterada
- Se nomes de aplicativos foram alterados

A exibição padrão do Log de auditoria mostra:

- Data e hora da ocorrência
- O serviço que registrou a ocorrência
- A categoria e o nome da atividade (o que)
- O status da atividade (sucesso ou falha)
- Destino
- Iniciador/ator de uma atividade (quem)

![](/assets/img/78/04.png){: "width=60%" }

## 3 - Logs de provisionamento

É possível usar as informações capturadas nos logs de provisionamento para ajudar a solucionar problemas com um usuário provisionado.

>**Observação:** Para exibir os logs de provisionamento, é necessário licenciamento Microsoft Entra ID P1 ou P2!!!
{: .prompt-warning }

Aqui estão algumas dicas e considerações para analisar os logs de provisionamento:

- O portal do Azure armazena dados de provisionamento relatados por 30 dias se você tiver uma edição premium e 7 dias se tiver uma edição gratuita. Você pode rotear os logs de provisionamento para os logs do Azure Monitor para retenção além de 30 dias.

- Você pode usar o atributo change ID como identificador exclusivo, o que pode ser útil quando você está interagindo com o suporte ao produto, por exemplo.

- Você pode ver eventos ignorados para usuários que não estão no escopo. Esse comportamento é esperado, especialmente quando o escopo de sincronização é definido para todos os usuários e grupos. O serviço avalia todos os objetos no locatário, mesmo aqueles que estão fora do escopo.

- Os logs de provisionamento não mostram importações de função (se aplica à AWS, Salesforce e Zendesk). Você pode encontrar os logs para importações de função nos logs de auditoria.

## 4 - Logs de atividades do Microsoft 365

Agora é hora de adicionar algumas doses de adrenalina no cardápio.
Embora os logs de atividades do Microsoft 365 e do Microsoft Entra compartilhem muitos recursos de diretório, **somente o Centro de administração do Microsoft 365 oferece uma exibição completa dos logs de atividades do Microsoft 365**, portanto, se você é um profissional que surfa nesta praia aproveite para encarar grandes ondas com a A <a href="https://learn.microsoft.com/pt-br/microsoft-365/admin/admin-overview/admin-center-overview?view=o365-worldwide" target="_blank"> **Doc oficial**</a>!!!

## 5 - Next Stop

Bem meu caro leitor e leitora, como mencionei lá no início este artigo, isto aqui é somente a entrada, pra você que gostou do assunto deixo a seguir o prato principal com direito a sobremesa. 

Tkssss pela leitura e até breve!!! 🍻🚀 

- <a href="https://learn.microsoft.com/pt-br/training/modules/monitor-maintain-azure-active-directory/" target="_blank"> **Monitorar e Manter o Microsoft Entra ID**</a>

- <a href="https://learn.microsoft.com/pt-br/entra/identity/monitoring-health/howto-access-activity-logs" target="_blank"> **Como acessar logs de atividades no Microsoft Entra ID**</a>

- <a href="https://learn.microsoft.com/pt-br/entra/identity/monitoring-health/howto-archive-logs-to-storage-account" target="_blank"> **Arquivar os logs de atividade em uma conta de armazenamento do Azure**</a>

- <a href="https://www.youtube.com/watch?v=YaAThOU1Px0" target="_blank"> **Exportar Logs para Azure Monitor e analisar com consultas KQL**</a>
