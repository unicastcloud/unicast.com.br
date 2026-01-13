---
layout: post
title: "AWS IAM: Princípios de segurança e boas práticas para iniciantes"
author: asilva
date: 2025-09-07 17:00:00 -0300
categories: [Cloud, AWS]
tags: [aws, iam, segurança, boas-praticas]
---

Fala galera! Seis tão baum?

Se você está começando na AWS, cedo ou tarde vai se deparar com o **IAM (Identity and Access Management)**. Ele é o serviço que controla **quem pode acessar o quê** dentro da sua conta AWS. Parece simples, mas é aqui que muita gente escorrega: permissões mal configuradas podem abrir brechas sérias de segurança ou gerar custos inesperados.

![](/assets/img/115/iam-01.png){: "width=60%" }

Neste artigo vamos explorar:

- O que é o IAM e por que ele é tão importante
- Os principais componentes (usuários, grupos, roles, policies)
- Boas práticas para iniciantes
- Exemplos práticos em CLI e Console
- Checklist para aplicar no seu ambiente

## **O que é o IAM?**

O **IAM** é o serviço de gerenciamento de identidade e acesso da AWS. Ele permite:
- Criar **usuários** e **grupos**
- Definir **roles** para serviços e aplicações
- Escrever **policies** (documentos JSON) que descrevem permissões

![](/assets/img/115/iam-02.png){: "width=60%" }

## **Componentes principais**

### Usuários

Representam pessoas ou sistemas que precisam acessar recursos. Cada usuário pode ter credenciais (senha para console e/ou access keys para CLI/API).

### Grupos

Coleções de usuários que compartilham permissões. Exemplo: grupo “Developers” com acesso a S3 e CloudWatch.

### Access Keys

As **Access Keys** são credenciais de longo prazo compostas por um **Access Key ID** e uma **Secret Access Key**. Elas permitem que usuários ou aplicações acessem a AWS programaticamente via CLI, SDKs ou APIs.

**Quando usar:**
- Automações e scripts que precisam interagir com a AWS
- Aplicações que rodam fora da AWS
- Integração com ferramentas de terceiros

**Quando NÃO usar:**
- Para serviços rodando dentro da AWS (use IAM Roles)
- Nunca compartilhe ou exponha em código-fonte
- Evite criar access keys para a conta root

**Boas práticas:**
- Rotacione regularmente (a cada 90 dias)
- Desative keys não utilizadas
- Use AWS Secrets Manager para armazenar credenciais
- Monitore uso com CloudTrail

### MFA (Multi-Factor Authentication)

O MFA adiciona uma camada extra de segurança exigindo um segundo fator de autenticação além da senha. Pode ser:

- **MFA virtual**: Apps como Google Authenticator, Microsoft Authenticator ou Authy
- **MFA físico**: Dispositivos de hardware como YubiKey
- **SMS**: Menos recomendado por questões de segurança

**Por que é essencial:**
- Protege contra roubo de credenciais
- Obrigatório para conta root
- Recomendado para todos os usuários com acesso ao console
- Pode ser exigido via policies condicionais

### Roles

IAM Roles (Funções do IAM) na AWS são identidades que definem um conjunto de permissões para fazer solicitações a serviços da AWS, mas, ao contrário dos usuários do IAM, elas não estão associadas a uma pessoa ou credenciais de longo prazo (como senhas ou chaves de acesso permanentes). Em vez disso, as funções fornecem credenciais de segurança temporárias que são automaticamente gerenciadas pela AWS Security Token Service (STS). 

Como Funcionam: As IAM Roles são projetadas para serem assumidas por entidades autorizadas, que podem ser:

- **Serviços da AWS:** Uma instância EC2 que precisa acessar um bucket S3, uma função Lambda que precisa gravar logs no CloudWatch, etc..
- **Usuários em outra conta da AWS**: Para acesso seguro e delegado entre contas.
- **Usuários federados (fora da AWS)**: Usuários de um sistema de identidade corporativo que precisam de acesso temporário aos recursos da AWS. 

**O funcionamento baseia-se em:**

1. **Políticas de Permissões**: Um conjunto de políticas define o que a função pode e não pode fazer (ex: s3:PutObject, ec2:RunInstances).
2. **Política de Confiança (Trust Policy)**: Define quais entidades têm permissão para assumir a função.
3. **Assunção da Função**: A entidade autorizada "assume" a função e, em troca, recebe credenciais temporárias para realizar as ações permitidas. 

![](/assets/img/115/iam-03.png){: "width=60%" }

### Policies

As Políticas IAM (Identity and Access Management) da AWS são documentos no formato JSON que definem permissões, controlando quais ações uma entidade (usuário, grupo, ou função/role) pode realizar em quais recursos da AWS. Elas são a base do controle de acesso na AWS. 

Exemplo de policy mínima para listar buckets no S3:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListAllMyBuckets"],
      "Resource": "*"
    }
  ]
}
```

![](/assets/img/115/iam-04.webp){: "width=60%" }

### AWS Managed Policies vs Customer Managed Policies

Existem três tipos principais de policies:

**1. AWS Managed Policies**
- Criadas e mantidas pela AWS
- Cobrem casos de uso comuns
- Atualizadas automaticamente pela AWS
- Exemplos: `AmazonS3ReadOnlyAccess`, `PowerUserAccess`, `ReadOnlyAccess`
- **Quando usar**: Para necessidades padrão e começar rapidamente

**2. Customer Managed Policies**
- Criadas e gerenciadas por você
- Permitem controle granular específico para suas necessidades
- Você controla as atualizações
- Reutilizáveis em múltiplos usuários, grupos e roles
- **Quando usar**: Quando AWS Managed Policies são muito permissivas ou não atendem seu caso de uso

**3. Inline Policies**
- Anexadas diretamente a um único usuário, grupo ou role
- Não são reutilizáveis
- Deletadas junto com a entidade
- **Quando usar**: Para permissões muito específicas que não devem ser compartilhadas

**Recomendação**: Use AWS Managed Policies sempre que possível, crie Customer Managed Policies para casos específicos, e reserve Inline Policies apenas para exceções.

## **Políticas baseadas em identidade e em recursos**

As políticas baseadas em identidade são anexadas a uma identidade (usuário, grupo ou perfil) do IAM para controlar suas permissões, enquanto as políticas baseadas em recursos são anexadas a um recurso (como um bucket S3) para controlar o acesso a ele. 

### Políticas baseadas em identidade

- O que são: Documentos de permissão em formato JSON que são anexados a um usuário, grupo de usuários ou perfil do IAM.
- O que controlam: Quais ações uma identidade pode realizar, em quais recursos e sob quais condições.
- Exemplo: Uma política que permite que um usuário veja apenas permissões para suas próprias credenciais de acesso.
- Quando usar: Para definir as permissões de um usuário ou um role de forma granular. 

### Políticas baseadas em recursos

- O que são: Políticas que são anexadas diretamente a um recurso, como um bucket do Amazon S3 ou uma fila do Amazon SQS.
- O que controlam: Quais entidades (usuários, grupos, roles) podem acessar o recurso e quais ações elas podem realizar nele.
- Exemplo: Uma política de bucket que permite que um determinado grupo de usuários do IAM leia arquivos de um bucket específico.
- Quando usar: Para restringir o acesso a um recurso específico, independentemente de quem está tentando acessá-lo. 

## **Princípio do menor privilégio e Boas práticas**

O princípio do menor privilégio na AWS é uma prática de segurança fundamental que concede a usuários, funções e serviços apenas as permissões estritamente necessárias para realizar suas tarefas. O objetivo é minimizar o impacto de uma conta comprometida, limitando o acesso a recursos, restringindo as ações possíveis e, assim, protegendo contra acessos não autorizados e vazamentos de dados. 


- **Atribua permissões por função**: Crie usuários e funções com base em suas responsabilidades. Por exemplo, um usuário que monitora recursos no CloudWatch não precisa de permissões de administrador.
- **Evite o uso de contas administrativas**: Nunca utilize a conta root para tarefas diárias. Crie usuários individuais com privilégios específicos, em vez de conceder acesso administrativo a todos.
- **Use ferramentas de automação**: O AWS Access Analyzer pode analisar logs do CloudTrail para gerar políticas que definem as permissões exatas que uma role ou conta precisa, automatizando o processo de criação de políticas de menor privilégio.
- **Crie políticas dinâmicas**: O menor privilégio não é um estado estático, mas sim um processo contínuo. Revisite e atualize as políticas de permissão regularmente para se ajustar às mudanças nas necessidades do negócio e nas atualizações de serviços da AWS.
- **Use contas de serviço separadas**: Para automação, como a que o Terraform usa para provisionar recursos, crie contas ou roles com permissões limitadas apenas para as tarefas de infraestrutura necessárias.
- **Limite o acesso por contexto**: Além de restringir ações, é possível refinar o acesso usando chaves de condição, tags e limitando permissões a regiões específicas ou ambientes, como por meio de ferramentas como o AWS Secrets Manager ou Amazon Verified Permissions. 

## **Exemplos práticos**

### Criando um usuário com AWS CLI

Criando usuário:

```bash
aws iam create-user --user-name dev-user
aws iam create-access-key --user-name dev-user
```

Anexando policy de acesso ao S3:

```bash
aws iam attach-user-policy \
  --user-name dev-user \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

### Criando um usuário pelo Console AWS

1. Acesse o **IAM Console** em [console.aws.amazon.com/iam](https://console.aws.amazon.com/iam)
2. No menu lateral, clique em **Users** → **Add users**
3. Defina o nome do usuário (ex: `dev-user`)
4. Selecione o tipo de acesso:
   - **AWS Management Console access**: para acesso via navegador
   - **Programmatic access**: para CLI/API (gera access keys)
5. Clique em **Next: Permissions**
6. Escolha como atribuir permissões:
   - **Add user to group**: adiciona a um grupo existente
   - **Attach policies directly**: anexa policies diretamente
   - **Copy permissions**: copia de outro usuário
7. Selecione a policy (ex: `AmazonS3ReadOnlyAccess`)
8. Revise e clique em **Create user**
9. **Importante**: Salve as credenciais (download .csv) - elas aparecem apenas uma vez!

### Criando uma IAM Role para EC2 (CLI)

```bash
# 1. Criar o trust policy (permite EC2 assumir a role)
cat > trust-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

# 2. Criar a role
aws iam create-role \
  --role-name EC2-S3-ReadOnly-Role \
  --assume-role-policy-document file://trust-policy.json

# 3. Anexar policy à role
aws iam attach-role-policy \
  --role-name EC2-S3-ReadOnly-Role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

# 4. Criar instance profile (necessário para EC2)
aws iam create-instance-profile \
  --instance-profile-name EC2-S3-ReadOnly-Profile

# 5. Adicionar role ao instance profile
aws iam add-role-to-instance-profile \
  --instance-profile-name EC2-S3-ReadOnly-Profile \
  --role-name EC2-S3-ReadOnly-Role
```

### Criando uma IAM Role para EC2 (Console)

1. No **IAM Console**, clique em **Roles** → **Create role**
2. Selecione **AWS service** como tipo de trusted entity
3. Escolha **EC2** como use case
4. Clique em **Next**
5. Selecione as policies necessárias (ex: `AmazonS3ReadOnlyAccess`)
6. Clique em **Next**
7. Nomeie a role (ex: `EC2-S3-ReadOnly-Role`)
8. Adicione uma descrição e tags (opcional)
9. Clique em **Create role**
10. Para usar, ao criar uma instância EC2, selecione esta role em **IAM instance profile**

### Criando uma Policy customizada (CLI)

```bash
# Criar policy JSON
cat > custom-s3-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::meu-bucket-exemplo/*"
    },
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::meu-bucket-exemplo"
    }
  ]
}
EOF

# Criar a policy
aws iam create-policy \
  --policy-name Custom-S3-Bucket-Access \
  --policy-document file://custom-s3-policy.json

# Anexar a policy a um usuário
aws iam attach-user-policy \
  --user-name dev-user \
  --policy-arn arn:aws:iam::123456789012:policy/Custom-S3-Bucket-Access
```

### Criando uma Policy customizada (Console)

1. No **IAM Console**, clique em **Policies** → **Create policy**
2. Escolha entre:
   - **Visual editor**: interface gráfica
   - **JSON**: escrever diretamente o JSON
3. **Usando Visual editor**:
   - Selecione o serviço (ex: S3)
   - Escolha as ações (ex: `GetObject`, `PutObject`, `ListBucket`)
   - Defina os recursos (ARNs específicos ou `*`)
   - Adicione condições se necessário
4. Clique em **Next**
5. Nomeie a policy (ex: `Custom-S3-Bucket-Access`)
6. Adicione descrição
7. Clique em **Create policy**
8. Para usar, anexe a policy a usuários, grupos ou roles

## **Checklist rápido para iniciantes**

- Criar usuário admin separado do root
- Ativar MFA para todos os usuários
- Usar grupos para organizar permissões
- Aplicar princípio do menor privilégio
- Usar roles para serviços (EC2, Lambda, EKS)
- Rotacionar credenciais periodicamente
- Monitorar acessos com CloudTrail e Access Analyzer
- Remover access keys não utilizadas
- Revisar policies regularmente com Access Analyzer
- Usar AWS Managed Policies quando possível
- Testar policies com IAM Policy Simulator

## **Recursos e Referências**

Para aprofundar seus conhecimentos sobre IAM:

**Documentação Oficial:**
- [AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/){:target="_blank"}  
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html){:target="_blank"}  
- [IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html){:target="_blank"}  
- [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html){:target="_blank"}  

**Ferramentas úteis:**
- [IAM Policy Simulator](https://policysim.aws.amazon.com/){:target="_blank"} - Teste suas policies antes de aplicar
- [AWS Access Analyzer](https://aws.amazon.com/iam/access-analyzer/){:target="_blank"} - Analise e refine permissões
- [IAM Access Advisor](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_access-advisor.html){:target="_blank"} - Veja quais serviços estão sendo usados

**Segurança:**
- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/){:target="_blank"}  
- [MFA Setup Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html){:target="_blank"}  
- [Credential Report](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_getting-report.html){:target="_blank"}  

## **Conclusão**

O IAM é a espinha dorsal da segurança na AWS. Dominar seus conceitos desde o início evita dores de cabeça no futuro.  
Com usuários bem configurados, MFA ativo e roles corretas, você garante que sua infraestrutura na nuvem esteja protegida e organizada.

**Dica extra**: mantenha um padrão de nomenclatura para usuários, grupos e roles. Isso facilita auditoria e evita confusão em times grandes.

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender um pouco sobre AWS! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte abraço a todos!
