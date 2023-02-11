---
layout: post
title: "[Azure-To] #7 Redefinir senha de VMs pelo Portal da Azure"
author: asilva
date: 2021-03-28 06:54:00 -0500
categories: [Azure, Azure-To]
tags: [maquinavirtual, azure, microsoft, infraestrutura]
---

Fala galera! Seis tão baum?

Faz tempo que não apareço por aqui com dicas rápidas (Azure-To), mas a de hoje é simples, mas uma hora você vai acabar precisando =D.

Vamos ver como é possível redefinir a senha de administrador da VM via portal.

Esta opção cria uma extensão VM "VMAccess" para redefinir a conta de administrador.

Windows - Também redefinirá a configuração do serviço de desktop remoto

Linux - Redefinirá uma chave de credenciais/ssh dos usuários existentes, se não existir, criará um novo usuário com privilégios sudo e redefinirá a configuração SSH

### **1.1 Redefinindo senha de máquina virtual Window**

Dentro das configurações de sua máquina virtual, vá ao menu na laterla esquerda e procure por Support + troubleshooting em seguida Reset Password.

![](/assets/img/03/reset.png){: .shadow style="max-width: 90%" }

Basta ajustar as informações e fazer o update.

### **2.1 Redefinindo senha de máquina virtual Linux**

Dentro das configurações de sua máquina virtual, vá ao menu na laterla esquerda e procure por Support + troubleshooting em seguida Reset Password.

![](/assets/img/03/reset-2.png){: .shadow style="max-width: 90%" }

Basta ajustar as informações e fazer o update.

É isso galera, espero que gostem!

Forte Abraço