---
layout: post
title: "Utilizando Infracost para estimar custos no Azure com Terraform"
author: asilva
date: 2022-10-18 09:00:00 -0500
categories: [DevOps, Terraform]
tags: [Terraform, devops, vscode, azcli, azure, iac, infracost]
---

Fala galera! Seis tão baum?

Mais uma vez a cadência de artigos no site caiu, mas novamente e graças a Deus, por bons motivos!

Tem muita coisa para compartilhar com vocês, mas vou manter o padrão de qualidade que já estou acostumado, assim posso garantir que estarei trazendo o melhor material a vocês.

Entre elas estão:

* Minha nomeação para o prêmio Microsoft MVP na categoria Azure.
* Programa de Mentoria de Carreiras 2.0 do Canal da Cloud.
* Palestra no TDC Future em Porto Alegre.
* Início dos vídeos no canal do Youtube.

E muito mais, fiquem ligados que vem muita coisa por aí, 2023 então...

Fora tudo isso, ainda estou trabalhando em um grande projeto de infraestrutura como código (IaC) no Azure com Terraform. 

Nas últimas semanas, estou bastante envolvido com estes temas e resolvi trazer este artigo a vocês.

Hoje vamos falar sobre a ferramenta *Infracost* para estimar custos dos recursos que serão implantados no ambiente Azure utilizando o Terraform.

**TL;DR**

Um pouco de contexto:

### **O que é Terraform?**

O Terraform é uma das principais ferramentas de IaC utilizadas no mundo. É uma ferramenta de código aberto criada pela **HashiCorp** e utiliza uma linguagem de configuração declarativa, conhecida como HashiCorp Configuration Language ou HCL.

Com o Terraform é possível criar infraestruturas no Azure, AWS, Google Cloud entre outros providers, utilizando apenas códigos. 

Esse tipo de automação torna o processo de administração da infra mais confiável, seguro e controlado, pois todas as alterações no código ficam registradas, facilitando a rastreabilidade do conteúdo e proporcionando uma maior velocidade no processo de entrega de uma nova infra ou de manutenção, eliminando a necessidade de executar processos manuais, os quais levariam mais tempo para ser executados e com risco de falha humana. 

![](/assets/img/40/iac01.png){: "width=60%" }

Link do Terraform: <a href="https://www.terraform.io/" target="_blank">HashiCorp Terraform</a>  

### **O que é Infracost?**

![](/assets/img/40/iac02.png){: "width=60%" }

Já o Infracost, é uma ferramenta auxiliar que mostra estimativas de custo para projetos de infraestrutura como código, como o Terraform. Isso ajuda DevOps, SRE e desenvolvedores a ver rapidamente uma previsão de custos antes de efetuar a implantação."

Link do projeto: <a href="https://github.com/infracost/infracost" target="_blank">GitHub Infracost</a>  

### **Por que utilizar?**

Infraestrutura como código (IaC) é utilizada para implementar, manter e facilitar a evolução de ambientes em nuvem. Como qualquer outro tipo de linguagem, ela deve seguir as mesmas boas práticas e passar por etapas de revisão de código, e porque não, revisão de custos? 

Infraestrutura em nuvem tem um custo, e a visibilidade de seus gastos ao utiliza-la é extremamente importante. Ao utilizar IaC o custo nem sempre é destacado na fase de implementação e se você não estiver de olho nos gastos, os custos podem sair mais alto do que o planejado.

### 1.1 **Instalando o Infracost**

Você pode instalar o Infracost em diversos sistemas operacionais, e independente da sua escolha, são bem semelhantes e fácies de instalar.

Vou utilizar como exemplo a instalação manual para Windows.

Baixe e descompacte aversão mais recente do Infracost: <a href="https://github.com/infracost/infracost/releases/latest/download/infracost-windows-amd64.zip" target="_blank">Download</a>  

Movimente o arquivo executável para uma pasta, exemplo: C:\Infracost. No meu caso, utilizei a minha pasta C:\HashiCorp, pois já deixo o executável do Terraform por lá.

![](/assets/img/40/iac03.png){: "width=60%" }

Agora, basta criar uma variável de ambiente para o executável. Você pode fazer isso da seguinte forma:

1. Vá para Control Panel -> System -> System settings -> Environment Variables.
2. Role para baixo nas variáveis do sistema até encontrar PATH
3. Clique em editar e altere de acordo.
4. Certifique-se de incluir um ponto e vírgula no final do anterior, pois esse é o delimitador, ou seja, c:\path; c:\path2
5. Inicie um novo console para que as configurações entrem em vigor.

![](/assets/img/40/iac04.png){: "width=60%" }

Feito isso, já podemos validar a versão de nossa ferramenta:

```powershell
infracost --version
```

![](/assets/img/40/iac05.png){: "width=60%" }

Caso você utilize outro sistema operacional, você pode seguir os passos neste link: <a href="https://www.infracost.io/docs/" target="_blank">Get started</a>  

### 2.1 **Instalando a extensão Infracost no VS Code**

Inicie o seu VS Code vá para **"Extensions"**, depois disso, instale a seguinte extensão:

![](/assets/img/40/iac06.png){: "width=60%" }

Clique em **install**.

### 3.1 **Autenticando a chave de API do Infracost**

Agora, precisamos gerar uma chave API, execute o comando abaixo.

```powershell
infracost register
```

![](/assets/img/40/iac07.png){: "width=60%" }

Clique na URL e confirme a autenticação clicando em **"Login":**

![](/assets/img/40/iac08.png){: "width=60%" }

### 4.1 **Estimando custos de infra no Azure (Terminal)**

Agora, já podemos testar a funcionalidade do Infracost em nosso código de Terraform.

Você precisa de:

* Um projeto de terraform, código da infraestrutura desejada para implantação.
* Terraform instalado.

Para estimar o custo de seu projeto, execute o seguinte comando:

```powershell
infracost breakdown --path . 
```

![](/assets/img/40/iac09.png){: "width=60%" }

O Infracost consegue calcular sem configurações extras os custos mensais dos recursos a serem implantados.

Você também pode usar a flag *--show-skipped* para verificar os recursos que são gratuitos.

```powershell
infracost breakdown --show-skipped --path . 
```

![](/assets/img/40/iac10.png){: "width=60%" }

### 4.2 **Estimando custos de infra no Azure (Extensão VSCode)**

Com a extensão do Infracost instalada em seu VS Code, você pode verificar a estimativa de custo em tempo real em seu código.

Funciona com blocos de `recursos` e `módulos`. 

![](/assets/img/40/iac11.gif){: "width=60%" }

### 5.1 **Refinando a estimativa de custos**

O Terraform atualiza, exclui ou cria recursos e salva o estado da infraestrutura em um arquivo chamado **tfstate**. Quando você implanta uma nova versão, o Terraform é capaz de detectar o que precisa ser atualizado, removido e criado após suas alterações. O Infracost depende desse arquivo de estado para mostrar o impacto no custo da alteração atual do plano.

O Infracost possui várias flags para customizar sua estimativa de custo, uma delas é o comando `configure set currency` que pode ser utilizado para modificar a moeda atual.

Podemos por exemplo, mudar nossa estimativa para Real brasileiro (BRL)

Basta utilizar o comando abaixo:

```powershell
infracost configure set currency BRL
```

Basta rodar a estimativa novamente e verificar o resultado em reais.

![](/assets/img/40/iac12.png){: "width=60%" }

Além disso, você pode alimentar o Infracost com um arquivo `yaml` personalizado, e refinar ainda mais a estimativa de custo:

Isso pode ser interessante para personalizar a estimativa de custo de acordo com o contrato e o markup utilizado por seus clientes.

De forma padrão, o Infracost irá estimar os custos de acordo com a calculadora pública do Azure, e este valor reflete bem um cenário PAYG, mas para clientes que possuem contratos CSP e EA ela pode gerar diferenças consideráveis de valores.

Caso tenha necessidade de um arquivo customizado, visite a documentação para verificar os parâmetros disponíveis para uso: <a href="https://www.infracost.io/docs/features/config_file/" target="_blank">config File</a>  

### 5.1 **Gerando relatórios da estimativa de custos no Azure**

O Infracost possibilita gerar relatórios para compartilhar a estimativa de custos em diferentes formatos **(HTML, JSON, etc.)** :

Use o comando abaixo para gerar o relatório.

```powershell
infracost breakdown --path . --format html > report.html
```

![](/assets/img/40/iac13.png){: "width=60%" }

### **Outras possibilidades**

Você pode integrar o Infracost com muitas soluções de **CI/CD** como, GitHub Action, Azure Pipelines entre outros, desta forma ao ser criado um Pull Request (PR), ele fará um comentário com o custo da infra atual, e o futuro, como no exemplo que peguei no site deles abaixo!

![](/assets/img/40/iac14.png){: "width=60%" }

Espero que gostem!

Forte abraço.



