---
layout: post
title: "Criando DevContainer Terraform para Azure no VS Code"
author: asilva
date: 2022-10-10 09:00:00 -0500
categories: [DevOps, Terraform]
tags: [terraform, devops, vscode, azcli, azure, iac, devcontainer, container, docker]
---

Fala galera! Seis tão baum?

Eu tenho usado DevContainers para desenvolver e executar códigos de Terraform no Azure a algum tempo, sei que pode soar estranho ter um container para desenvolvimento local, mas é uma ideia muito interessante.

Na minha máquina funciona!

Com certeza você já ouviu essa frase, e é por isso que faz todo o sentido montar um container de desenvolvimento padrão.

Os contêineres são apenas uma maneira de ter um conjunto de dependências que podem ser executadas em qualquer lugar. E é exatamente isso que queremos, uma máquina de desenvolvimento padrão que possa ser versionada, atualizada e facilmente compartilhada.

Para garantir a qualidade do código e fazer testes eficazes, é essencial no desenvolvimento de código ter consistência entre o ambiente de desenvolvimento local e o pipeline CI/CD que traz o código para produção.

## **O que são DevContainers?**

O Visual Studio Code tem um conceito chamado **Visual Studio Code Dev Containers**

A extensão **Visual Studio Code Dev Containers** permite que você use um **contêiner Docker** como um ambiente de desenvolvimento completo. Ele permite que você abra qualquer pasta dentro (ou montado em) um contêiner e aproveite os recursos do **Visual Studio Code**.

Para saber mais sobre devcontainer: <a href="https://code.visualstudio.com/docs/remote/containers" target="_blank">https://code.visualstudio.com/docs/remote/containers</a>   

O objetivo deste **devcontainer** é criar um contêiner VS Code com todas as ferramentas, bibliotecas e frameworks necessários para sua equipe, evitando perca de tempo na configuração do ambiente de desenvolvimento local.

Sensacional né? Mas e se este devcontainer estivesse remoto?

Então, necessariamente este conteiner não precisa estar funcionando na sua máquina, e podemos levar esta experiência à um próximo nível com o <a href="https://github.com/features/codespaces" target="_blank">GitHub Codespaces</a> mas fica para um próximo artigo! 

## **Pré-requisitos**

1. Docker Desktop (Windows)
2. VS Code
3. Visual Studio Code Dev Containers

Uma vez instalados, você também precisa instalar a extensão no VS Code. Para isso, abra o VS Code, clique no ícone de extensões, procure **"Dev Containers"** e clique no botão de instalação.

![](/assets/img/39/devcont01.png){: "width=60%" }

## **Construindo o Contêiner**

Para construir nosso container, você pode usar uma das duas opções:

1. <a href="https://code.visualstudio.com/docs/remote/create-dev-container" target="_blank">Escrever e criar uma definição do zero.</a>  
2. <a href="https://github.com/microsoft/vscode-dev-containers/tree/main/containers" target="_blank">Começar a partir de um modelo e editar conforme necessidade..</a>    

Eu prefiro a segunda opção 😊.

Vou deixar meu repositório caso queria utilizá-lo como modelo:

<a href="https://github.com/asilvajunior/devcontainer-terraform" target="_blank">Starter template for Terraform on Azure</a> 

Aproveita e de uma fortalecida ⭐ no repositório!

## **Clone o repositório e abra em um DevContainer**

Uma vez que o repositório tenha sido clonado, o VS Code solicitará a abertura do repositório em um contêiner, se isso não acontecer, ele pode ser acionado pelo comando (Ctrl+Shift+p) e selecionar o comando 'Dev Containers: Open Folder in Container'. Na primeira vez, VS Code automaticamente construirá e executará o contêiner localmente, anexando a interface do usuário IDE a ele.

![](/assets/img/39/devcont02.png){: "width=60%" }

Selecione o repositório que você acabou de clonar (aquele com diretório .devcontainer na raiz), isso fará com que o VS Code construa o contêiner.

![](/assets/img/39/devcont03.png){: "width=60%" }

Agora, com o conteiner funcional, você pode utilizá-lo e validar se as ferramentas e extensões foram instaladas corretamente.

![](/assets/img/39/devcont04.png){: "width=60%" }

É isso galerinha, espero que gostem.

Forte abraço a todos!

