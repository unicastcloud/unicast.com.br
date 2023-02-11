---
layout: post
title: "Configurando o WSL2 no Windows 10"
author: asilva
date: 2021-08-02 09:00:00 -0500
categories: [Linux, Artigos]
tags: [windows, wsl2, linux, debian, ubuntu]
---

Fala galera! Seis tão baum?

O Windows possui um recurso disponível em PCs rodando Windows 10 chamado **Windows Subsystem for Linux ou WSL**. O WSL é um recurso que permite instalar e executar um ambiente Linux completo no Windows 10.

![](/assets/img/11/wsl1.png){: .shadow style="max-width: 50%" }

Desta forma você pode executar um ambiente Linux diretamente no Windows sem modificações, software secundário ou configurações de dual-boot. 

### **1.1 Instalando o WSL2**

Para instalar o WSL é bastante simples, basta executar os comandos de powershell como administrador.

### Habilitar o recurso WSL2

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

**Habilitar a plataforma de máquina virtual**

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Após concluir a instalação, reinicie o computador.

### Defina o WSL2 como a versão padrão

```powershell
wsl --set-default-version 2
```

### **2.1 Instalando uma distribuição Linux**

Abra a **Microsoft Store** e escolha a distribuição desejada.

![](/assets/img/11/wsl2.jpg){: .shadow style="max-width: 90%" }

Para este artigo estou utilizando a versão do Ubuntu 20.04 TLS.

![](/assets/img/11/wsl3.jpg){: .shadow style="max-width: 90%" }

Após baixar e instalar, é preciso executar a distribuição, você irá receber uma tela de terminal para configurar o nome de usuário e senha. Feito isso já tem sua distribuição Linux pronta para uso.

![](/assets/img/11/wsl5.jpg){: .shadow style="max-width: 90%" }

![](/assets/img/11/wsl4.jpg){: .shadow style="max-width: 90%" }

Para melhorar a experiência de uso, recomendo utilizar o Windows Terminal, você pode encontra-lo na Microsoft Store.

![](/assets/img/11/wsl6.jpg){: .shadow style="max-width: 90%" }

No momento estou estudando bastante material de IaC, e este recurso é uma mão na roda!

Não esqueça de se juntar a nossa comunidade Azure Brasil no Discord.

Link para o servidor: <https://discord.com/invite/S6zFKGA7hg>

Espero que gostem!

Forte abraço.