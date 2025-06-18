---
layout: post
title: "Primeiros passos com AWS CLI: Instalando e configurando"
author: asilva
date: 2025-06-11 08:00:00 -0500
categories: [Cloud, AWS]
tags: [aws, cli, infraestrutura, cloud]
---

Fala galera! Seis tão baum?

Como comentei no último post, essa nova fase profissional me levou a mergulhar de cabeça na **AWS**. E quando você começa a explorar de verdade o ecossistema da Amazon, uma das ferramentas que rapidamente se torna indispensável é o **AWS CLI**.

Pode parecer simples — e até é, depois que você entende o fluxo — mas no começo, até coisas pequenas como **“como instalo?”**, **“onde coloco as credenciais?”**, ou **“como alterno entre contas?”** podem travar seu ritmo de aprendizado.

Por isso, decidi registrar aqui um mini tutorial com tudo o que eu gostaria de ter lido quando fui instalar e configurar o CLI da AWS no meu Mac. Se você também está migrando do Azure, ou apenas começando na AWS, esse post pode te poupar um bom tempo.

## **O que é o AWS CLI?**

O **AWS Command Line Interface (CLI)** é uma ferramenta que permite gerenciar recursos da AWS diretamente do terminal. Com ele, você pode criar buckets no S3, iniciar instâncias EC2, consultar serviços, entre milhares de outras ações — tudo por linha de comando.

## **Instalando o AWS CLI**

**Para usuários de macOS (via Homebrew)**

Se você usa Mac e já tem o Homebrew instalado, instalar o AWS CLI é tranquilo:

```bash
brew update
brew install awscli
```

Verifique a instalação:

```bash
aws --version
```

**Para usuários Linux**

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**Para usuários Windows**

Baixe o instalador .msi no site da AWS: <a href="https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html" target="_blank">Installing or updating to the latest version of the AWS CLI </a>

## **Configurando o AWS CLI**

Depois da instalação, configure suas credenciais:

```bash
aws configure
```
Você vai inserir:

- Access Key ID 
- Secret Access Key
- Região padrão (ex: us-east-1)
- Formato de saída (json, table, text)

## **Usando múltiplas contas com perfis**

Se você trabalha com mais de uma conta da AWS (pessoal, empresa, clientes), recomendo fortemente usar perfis nomeados:

Para ambientes diferentes (pessoal, cliente, empresa), use perfis:

```bash
aws configure --profile empresa
```

Depois, para usar:

```bash
aws s3 ls --profile empresa
``` 

Você também pode definir um profile padrão via variável de ambiente:

```bash
export AWS_PROFILE=empresa
```

## **Entendendo os arquivos credentials e config**

O AWS CLI salva suas configurações e credenciais nestes arquivos:

- `~/.aws/credentials`: guarda chaves de acesso
- `~/.aws/config`: guarda preferências de perfil, como região e formato

Exemplo de `~/.aws/credentials`:

```bash
[default]
aws_access_key_id = AKIAxxx
aws_secret_access_key = xxx

[empresa]
aws_access_key_id = AKIAyyy
aws_secret_access_key = yyy
```

Exemplo de `~/.aws/config`:

```bash
[default]
region = us-east-1
output = json

[profile empresa]
region = us-west-2
output = json
```

## **Verificando quem é você na AWS**

Quando você já configurou várias contas e quer verificar qual identidade está sendo usada no momento, execute:

```bash
aws sts get-caller-identity
```

Saída esperada:

```bash
{
  "UserId": "AIDAxxxxxxxxxxxx",
  "Account": "123456789012",
  "Arn": "arn:aws:iam::123456789012:user/seu-usuario"
}
```

Se estiver usando um profile específico:

```bash
aws sts get-caller-identity --profile empresa
```

## **Habilitando autocomplete para AWS CLI**

**No Zsh**

Adicione ao `~/.zshrc`:

```bash
complete -C '/opt/homebrew/bin/aws_completer' aws
```

Recarregue:

```bash
source ~/.zshrc
```

**No Bash**

Adicione ao `~/.bash_profile` ou `~/.bashrc`:

```bash
complete -C "$(which aws_completer)" aws
```

Recarregue:

```bash
source ~/.bashrc
```

## **Boas práticas com perfis e chaves**

- Evite deixar chaves expostas no código (mesmo em .env)
- Para projetos em equipe, prefira usar roles IAM com permissões mínimas
- Se possível, utilize AWS SSO (Single Sign-On) para controle centralizado
- Sempre que possível, rote suas chaves periodicamente

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender um pouco sobre AWS! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte abraço a todos!
