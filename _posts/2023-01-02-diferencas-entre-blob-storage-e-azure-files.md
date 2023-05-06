---
layout: post
title: "Diferenças entre Blob Storage e Azure Files"
author: asilva
date: 2023-01-02 09:00:00 -0500
categories: [Cloud, Azure]
tags: [azure, microsoft, storage, fileshare, blob]
---

Fala galera! Seis tão baum?

Primeiro artigo do ano, vamos que vamos que 2023 será um ano de muito conteúdo e compartilhamento!

![](/assets/img/45/storage01.gif){: "width=60%" }

Neste artigo, discutiremos sobre o Armazenamento do Azure e os diferentes tipos de serviços de armazenamento fornecidos pela plataforma. 

### **O que é o Serviço de Armazenamento do Azure**

O armazenamento do Microsoft Azure oferece uma variedade de opções para os usuários salvarem dados em nuvem. Para cada necessidade há uma opção diferente disponível nos serviços de Armazenamento do Azure. 

Neste artigo iremos abordar dois tipos diferentes de serviços de armazenamento do Azure: armazenamento de arquivos e armazenamento de blob. 

### **Serviços de armazenamento no Azure**

A Microsoft oferece várias opções para armazenar dados na nuvem. Cada opção tem seu propósito exclusivo de atender a diferentes necessidades de negócios. 

- **Azure Blob** - conhecido como blobs, é utilizado para armazenar dados binários/de texto, como fotos, vídeos, documentos, etc.
- **Azure Tables** - armazenamento NoSQL para dados estruturados sem esquema.
- **Azure Queue** - usado para armazenar mensagens que podem ser usadas para se comunicar com aplicativos.
- **Azure Files** - serviço de compartilhamento de arquivos gerenciado para nuvem ou local usando o protocolo SMB/NFS.
- **Azure Disks** - disco rígido virtual (VHD) de dois tipos: gerenciado e não gerenciado.

### **Azure Blob Storage**

A solução de armazenamento de objetos para a nuvem é o Armazenamento de Blobs do Azure. Objetos são dados não estruturados que não requerem nenhum modelo de dados específico. O Armazenamento de Blobs do Azure armazena os objetos utilizando um namespace simples para armazenar arquivos. Embora a convenção de nomenclatura usada pelos usuários possa fazer com que pareça uma estrutura de arquivo normal, isso não é verdade. No Azure Blob Storage, os objetos são armazenados no formato chave-valor, onde a chave é o nome do objeto armazenado e o valor são os dados do objeto.

Uma maneira de pensar no Armazenamento de Blobs do Azure são os metadados, seu armazenamento é configurado em diferentes componentes. Os usuários primeiro configuram a conta de armazenamento seguida pela criação do Blob Storage. O Blob Storage pode ter vários contêineres dentro dele e cada contêiner contém o objeto blob real. Além disso, os Blob Objects podem ser salvos de várias maneiras: Page Blobs, Block Bobs e Append Blobs. Um Blob de página pode armazenar um disco rígido virtual, que é acessado pelo sistema operacional. Por outro lado, um Block Blob é usado para armazenar dados binários ou textuais. Além disso, Append Blobs são usados ​​para armazenar logs que geralmente não requerem nenhuma edição. 

Por exemplo, os Blobs do Azure são muito úteis para os cenários em que os desenvolvedores de uma organização precisam acessar as IDEs sem baixá-las da Internet. Usando o Azure Blob Storage, a organização pode armazenar a ferramenta e compartilhar o link de acesso com sua equipe de desenvolvimento. Além disso, alguns cenários comuns nos quais os usuários podem utilizar o Armazenamento de Blobs do Azure são:

- Salvando arquivos para acesso distribuído.
- Streaming de dados de áudio ou vídeo.
- Armazenando logs em arquivos.
- Salvando dados para backup e recuperação.

### **Azure File Storage**

A solução de armazenamento de arquivos, como o nome indica, é o Azure File Storage. Ele deve ser montado ou implantado em qualquer sistema operacional antes da configuração. Além disso, os usuários estão familiarizados com esse tipo de armazenamento, pois ele pode ser relacionado a um sistema de arquivos regular nos sistemas operacionais Windows. A única diferença é que o Armazenamento de Arquivos do Azure é montado remotamente com capacidade de armazenamento ilimitada. 

Para montar os sistemas de armazenamento de arquivos do Azure, os usuários podem utilizar as máquinas virtuais ou suas máquinas locais. Além disso, ao mesmo tempo, máquinas diferentes podem montar vários compartilhamentos. Além disso, o Azure File Storage vem em duas versões distintas: Standard Storage e Premium Storage. A única diferença entre os dois é que o Premium Storage Services é executado em unidades de estado sólido (SSD), o que torna as operações de arquivo mais rápidas em comparação com a opção de armazenamento padrão. 

O Armazenamento de Arquivos do Azure destina-se ao manuseio interno de arquivos montando-o na VM ou máquina local. Alguns cenários comuns nos quais os usuários podem utilizar o Armazenamento de Arquivos do Azure são:

- Substituindo servidores de arquivos locais por servidores de arquivos baseados em nuvem.
- Simplificando o acesso à arquivos salvos na nuvem.

### **Qual a diferença entre Blob e File Storage?**

Simplificando, o Azure Blob Storage é um armazenamento de objetos usado para armazenar grandes quantidades de dados não estruturados, enquanto o Azure File Storage é um sistema de arquivos distribuído totalmente gerenciado com base no protocolo SMB.

### **Escalabilidade e Segurança**

É importante conhecer a cota e os limites do Armazenamento do Azure para escolher a opção correta. O nível mais alto de representação para capacidade no Armazenamento de Blobs do Azure é **Containers**, enquanto para Arquivos é **Compartilhamento**.

| **Recursos do Blob Storage**                                               | **Alvo**                               |
|----------------------------------------------------------------------------|:--------------------------------------:|
| Tamanho máximo de contêiner de <br /> Blob único	                         | 500 TB                                 | 
| Número máximo de blocos em um bloco <br /> Blob ou anexar Blob             | 50.000 blocos                          | 
| Tamanho máximo de um bloco <br /> em um bloco Blob	                     | 100 MB                                 | 
| Tamanho máximo de um bloco Blob		                                     | 50.000 X 100 MB <br /> (aprox. 4,75 TB)| 
| Tamanho máximo de um bloco em <br /> um Blob de acréscimo	                 | 4 MB                                   | 
| Tamanho máximo de um Blob de acréscimo		                             | 50.000 x 4 MB <br /> (aprox. 195 GB)   | 
| Tamanho máximo de um Blob de página		                                 | 8 TB                                   | 
| Número máximo de políticas de <br /> acesso armazenadas por contêiner Blob | 5                                      | 


| **Recursos do File Storage**                                                             | **Alvo**                 |
|------------------------------------------------------------------------------------------|:------------------------:|
| Tamanho máximo de um <br /> compartilhamento de arquivo	                               | 5 TB                     | 
| Tamanho máximo de um arquivo <br /> em um compartilhamento de arquivo                    | 1 TB                     | 
| Número máximo de arquivos em <br /> um compartilhamento de arquivo	                   | Sem limite               | 
| Máximo de IOPS por compartilhamento		                                               | 1000 IOPS                | 
| Número máximo de políticas de acesso <br />  armazenadas por compartilhamento de arquivo | 5                        | 
| Taxa máxima de solicitação por <br />	conta de armazenamento	                           | 20.000 solicitações por segundo <br />  para arquivos de qualquer tamanho válido | 
| Taxa de transferência de destino para <br /> compartilhamento de arquivo único	       | Até 60 MB por segundo    | 
| Máximo de identificadores abertos por arquivo                                            | 2000                     | 
| Número máximo de instantâneos <br /> cde compartilhamento                | 200 instantâneos <br /> compartilhados   | 

Quando o aplicativo grava/lê um novo Blob/Arquivo, eles são criptografados usando o algoritmo AES (Advanced Encryption Standard) de 256 bits. Se chamado por meio da API REST, os Blobs e Files do Azure são compatíveis com a habilitação de Transferência obrigatória segura. 

>Tanto os Blobs do Azure quanto os Arquivos do Azure precisam de Assinatura de Acesso Compartilhado (SAS) para obter acesso delegado a Blobs e Arquivos. Além da autorização, ambos são compatíveis com o Azure AD e o token de acesso compartilhado.
{: .prompt-tip }

Os Blobs permitem obter criptografia pela classe **BlobEncryptionPolicy** com Azure Key Vault. Os arquivos do Azure usam criptografia interna no protocolo SMB 3.0. Além disso, os Blobs e Arquivos do Azure oferecem suporte à **CORS** (Compartilhamento de recursos de origem cruzada). O CORS permite que você descreva a lista de permissões para solicitação de cabeçalho HTTP.

No que diz respeito à segurança da rede, você tem mais controle do tráfego de rede de entrada para os Blobs e Files do Azure. Pois permite apenas um intervalo de IP especificado e redes virtuais para acessá-lo. 

É isso galera, espero que gostem!

Forte Abraço!