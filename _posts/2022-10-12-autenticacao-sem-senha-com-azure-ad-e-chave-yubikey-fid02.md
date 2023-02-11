---
layout: post
title: "Autenticação sem senha com Azure AD e chave Yubikey (FIDO2)"
author: asilva
date: 2022-10-12 09:00:00 -0500
categories: [Azure, Identidade]
tags: [azuread, azure, activedirectory, yubikey, yubico, passwordless, fido2]
---

Fala galera! Seis tão baum?

Você já ouviu falar no termo **passwordless**?

O termo significa **autenticação sem senha**: é um método de autenticação no qual um usuário pode efetuar login em um sistema sem inserir uma senha. Nas implementações mais comuns, os usuários são solicitados a inserir um identificador público e, em seguida, concluir o processo fornecendo uma prova segura de identidade por meio de um dispositivo ou token registrado.

Muitas vezes a autenticação sem senha é confundida com a **autenticação multifator (MFA)** embora parecidos, o MFA é uma camada adicional de segurança sobre a autenticação baseada em senha, a autenticação sem senha geralmente usa apenas um fator altamente seguro para autenticar a identidade do usuário.

A autenticação **multifator** visa incrementar etapas de segurança, exigindo fatores adicionais de autenticação, para uma melhor proteção contra o acesso não autorizado e possíveis fraudes. 

A Microsoft possui suporte de autenticação sem senha no Azure AD, possibilitando aos usuários fazer login em suas contas utilizando biometria com o Windows Hello ou chaves físicas compatíveis com o padrão **FIDO (Fast Identity Online)**.

![](/assets/img/37/yubikey0.png){: .shadow style="max-width: 80%" }

Hoje iremos abordar a autenticação sem senha baseada em **FIDO2** com chaves da **Yubico**

### **O que é um YubiKey?**

A Yubikey é uma ferramenta de autenticação por hardware.

Basicamente, é um pendrive produzido pela empresa **Yubico**. Existem vários modelos disponíveis com conexão USB Tipo A, Tipo C, Lightning, formas e tamanhos diferentes e funções como NFC e Biometria.

![](/assets/img/37/yubikey1.png){: .shadow style="max-width: 50%" }

Vale a pena visitar o site do fabricante e conferir a diversidade de produtos: <a href="https://www.yubico.com/products/" target="_blank">https://www.yubico.com/products/</a>  

### **Como funciona um YubiKey?**

Como vimos o Yubikey é um dispositivo de segurança na camada de hardware.

![](/assets/img/37/yubikey2.png){: .shadow style="max-width: 80%" }

Ele suporta vários protocolos como:

- OTP
- OATH HOTP and TOPT
- FIDO U2F and FIDO2
- OpenPGP 3

Você pode usar sua chave para fazer login em contas compatíveis com **one-time password** ou um par de chaves pública/privada baseado em **FIDO2** gerado pelo dispositivo. Após digitar sua credencial, você precisa pressionar o botão em sua chave física, o toque do dedo libera uma pequena carga elétrica que ativa o aparelho e libera sua autenticação.

![](/assets/img/37/yubikey3.png){: .shadow style="max-width: 70%" }

A Yubico suporta diversos serviços e aplicativos, entre eles: Microsoft, AWS, Google, Twitter, Instagram, Facebook, GitHub entre tantos outros.

Consulte a lista de compatibilidade do YubiKey: <a href="https://www.yubico.com/br/works-with-yubikey/catalog/?series=3&sort=popular" target="_blank">https://www.yubico.com/br/works-with-yubikey/catalog/?series=3&sort=popular</a>  

### **Por que usar um YubiKey?**

Como qualquer dispositivo de tecnologia, o **Yubikey** tem seus prós e contras.

Até aqui, tenho certeza de que você está bem animado em adquirir uma **Yubikey**, mas estamos no Brasil e Brasil não é para amadores! Lá fora, o **Yubikey** é encontrando entre US$ 45 a US$ 70 dependendo do modelo. Aqui no Brasil começa em R$400,00 reais e subindo!

![](/assets/img/37/yubikey4.gif){: .shadow style="max-width: 70%" }

Calma, segura a informação, respira e vamos até o final do artigo!

As pessoas costumam reutilizar senhas entre diferentes serviços, e variantes da mesma senha são frequentemente usadas para contornar, os requisitos de complexidade de senha.

E este tipo de padrão é o mais explorado por invasores.

**Com uma chave YubiKey:**

- Configurar e utilizar é um processo simples. Você acessa o serviço, insere a chave, toca no botão e já foi!
- Elimina o tempo gasto aguardando a chegada de um e-mail ou SMS 2FA ou apenas pegando seu smartphone para inserir um código de autenticação manualmente. Obviamente, você ainda precisa configurar o **YubiKey** com a conta para começar.
- Um dispositivo de hardware torna a violação de 2FA incrivelmente difícil. O OTP exclusivo que o **YubiKey** gera é quase impossível de falsificar.
- Você não pode gravar nem transferir arquivos no seu **YubiKey**, o firmware não é acessível e ele não pode ser infectado por vírus.
- Pesa apenas 3g é a prova d'água e pode suportar submersão em água de até 1,5 m por até meia hora.
- É barato dadas as vantagens que oferece.

Mas eu sei, seu coração ainda está machucado com os valores praticados aqui no Brasil, você deve estar se perguntando: e se eu perder meu YubiKey? precisaria ter pelo menos duas chaves!

![](/assets/img/37/yubikey5.webp){: .shadow style="max-width: 40%" }

De fato, isso é um problema, e o mundo ideal seria ter 2 chaves. Mas isso é com qualquer coisa que seja importante a você, é o famoso: quem tem 2 tem 1.

Mas pensando no cenário em que você tenha apenas uma chave, perder sua **YubiKey** não motivo para pânico. A maioria dos serviços com 2FA permite que você crie códigos de backup ou use uma opção de autenticação de dois fatores secundária para acessar sua conta.

Perder sua **YubiKey** não será o fim do mundo. Mas você precisa ter uma segunda opção segura além da sua chave física, caso contrário complica. 

- Perder a chave.
- Não fazer o dever de casa e configurar múltiplos fatores de autenticação além de uma chave física.

Errar é humano, persistir no erro é burrice, se você comete uma dupla falta como está, precisa voltar alguns passos atrás! ;)

Veja, uma chave física não é um **Santo Graal** é somente mais uma camada de segurança para se proteger, como tudo na vida, você precisa ter outras camadas de segurança, a conexão de todas essas iniciativas é que aumenta seu nível de confiabilidade.

Segurança é assim, quanto mais engessado, mais seguro, quanto mais flexível, mais inseguro.

Na minha opinião, depois de configurado, a **YubiKey** é a melhor maneira de gerenciar a segurança de suas credenciais.

### **Configurando sua Yubikey com Azure AD**

Como mencionei no início do artigo, a **Yubico** suporta diversos serviços e aplicativos, em nosso exemplo, vamos configurar nosso **Azure Active Directory.**

O primeiro passo é ativar o método de autenticação **FIDO2** em seu **AAD**.

Navegue até seu **Azure Active Directory** > **Security** > **Authentication methods**, selecione **FIDO2 security key** habilite o recurso e clique em **Save**.

![](/assets/img/37/yubikey6.png){: .shadow style="max-width: 70%" }

![](/assets/img/37/yubikey7.png){: .shadow style="max-width: 70%" }

Feito isso já podemos configurar o usuário para logar com a **YubiKey**.

- Navegue até <a href="https://myprofile.microsoft.com" target="_blank">https://myprofile.microsoft.com</a>   
- Se ainda não estiver logado, faça o login.
- Navegue até o menu Segurança e em seguida Opções de segurança adicionais.
- Clique em Adicionar um novo modo de entrada ou de verificação e selecione usar uma chave de segurança.

![](/assets/img/37/yubikey8.png){: .shadow style="max-width: 70%" }

- Tenha sua **YubiKey** em mãos e clique em próximo.

![](/assets/img/37/yubikey9.png){: .shadow style="max-width: 70%" }

- Em seguida, insira sua chave.

![](/assets/img/37/yubikey10.png){: .shadow style="max-width: 70%" }

- Toque na sua **YubiKey** para confirmar o processo.

![](/assets/img/37/yubikey11.png){: .shadow style="max-width: 70%" }

- Dê um nome para sua chave de segurança e clique em próximo.

![](/assets/img/37/yubikey12.png){: .shadow style="max-width: 70%" }

![](/assets/img/37/yubikey13.png){: .shadow style="max-width: 70%" }

- Agora você já deve visualizar sua chave no painel de segurança.

![](/assets/img/37/yubikey14.png){: .shadow style="max-width: 70%" }

Acesse o **portal.azure.com** selecione **Sign-in options** e em seguida **Sign in with a security key** toque em sua **YubiKey** e seu login será efetuado com sucesso.

![](/assets/img/37/yubikey15.png){: .shadow style="max-width: 70%" }

![](/assets/img/37/yubikey16.png){: .shadow style="max-width: 70%" }

![](/assets/img/37/yubikey17.png){: .shadow style="max-width: 70%" }

>Por um padrão de segurança da Microsoft, mesmo habilitando seu login via chave física, será necessário adicionar o seu PIN como camada adicional de segurança.
{: .prompt-info }

### **Vale a pena?**

Na minha opinião, sim! No mundo globalizado em que vivemos, o patrimônio mais importante são nossos dados! 

Não adianta saber onde o cofre está, é necessário ter a informação para abri-lo! E por este motivo, vale o investimento.

Talvez você possa fazer um exercício: Caso você tenha suas credenciais comprometidas, quanto elas valem? O prejuízo intelectual e financeiro será maior que o investimento em uma chave de segurança?

Dadas as vantagens que um **YubiKey** oferece, não é um gasto relevante.

Segurança nunca é demais e a autenticação de dois fatores faz uma enorme diferença na sua segurança pessoal e corporativa.

Em pleno 2022, as senhas simplesmente não são suficientes...

Espero que gostem!

Forte abraço.


