---
layout: post
title: "Acessar computadores ingressados no Azure AD via RDP"
author: asilva
date: 2021-08-06 09:00:00 -0500
categories: [Cloud, Azure]
tags: [windows, azuread, azure, activedirectory, rdp]
---

Fala galera! Seis tão baum?

Caso você tenha computadores ingressados no Azure Active Directory (Azure AD) e tenha dificuldades para acessa-los remotamente via RDP, confira a dica abaixo para resolver o problema.

## **1.1 Ingressando seu computador no Azure Active Directory**

Você pode ingressar seus computadores ao AzureAD com os seguintes passos:

1. Todas as Configurações,
2. Contas, 
3. Acesso ao trabalho ou escola,
4. Conectar e digite seu nome de usuário do AzureAD,
5. Entrar neste dispositivo para o Azure Active Directory,
6. Verifique se seu dispositivo está ingressado no AzureAD com o comando **dsregcmd /status.**

![](/assets/img/12/rdp1.jpg){: "width=60%" }

![](/assets/img/12/rdp2.jpg){: "width=60%" }

![](/assets/img/12/rdp3.jpg){: "width=60%" }

![](/assets/img/12/rdp4.jpg){: "width=60%" }

Você também pode conferir no portal do Azure.

![](/assets/img/12/rdp5.jpg){: "width=60%" }

## **1.2 Alterando as configurações de acesso remoto**

Agora vamos definir as permissões do RDP para permitir conexões remotas a este computador.

Permita conexões remotas a este computador e remova a caixa de seleção de permitir conexões apenas de computadores executando remote desktop com autenticação de nível de rede ativada.

![](/assets/img/12/rdp6.jpg){: "width=60%" }

> Estamos em um ambiente de laboratório, em produção você não deve habilitar o RDP desta forma.
{: .prompt-danger }

## **2.1 Criar arquivo de configuração RDP personalizado**

Ao tentar logar com seu usuário de AzureAD, você terá o seguinte erro:

![](/assets/img/12/rdp7.jpg){: "width=60%" }

Para resolver este erro, iremos personalizar nosso arquivo de configuração do RDP.

Abra o cliente do **Remote Desktop Connection** e em seguida clique para mostrar mais opções.

Insira o IP ou FQDN da máquina que deseja acessar, clique em salvar como.

![](/assets/img/12/rdp8.jpg){: "width=60%" }

Abra o arquivo de configuração salvo utilizando o bloco de notas e insira os seguintes comandos no final do arquivo, e em seguida salve as modificações.

![](/assets/img/12/rdp9.jpg){: "width=60%" }

```
enablecredsspsupport:i:0
authentication level:i:2
```

## **2.2 Acessando a máquina via RDP personalizado**

Abra o arquivo de RDP personalizado e faça o acesso ao ambiente.

```
AzureAD\username@domain.com
```
![](/assets/img/12/rpd10.jpg){: "width=60%" }

![](/assets/img/12/rdp11.jpg){: "width=60%" }

É isso galera.

Não esqueça de se juntar a nossa comunidade Azure Brasil no Discord.

Link para o servidor: <https://discord.com/invite/S6zFKGA7hg>

Espero que gostem!

Forte abraço.