---
layout: post
title: "Update Cluster Kubernetes Raspberry PI"
author: asilva
date: 2025-03-05 09:00:00 -0500
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, network, raspberrypi, k8s, cluster, homelab]
---

Fala galera! Seis tão baum?

Resolvi atualizar este artigo porque estou estudando para a certificação **Certified Kubernetes Security Specialist (CKS)** e vou precisar usar mais meu cluster. Então, decidi não apenas atualizar o **Kubernetes**, mas também o **sistema operacional dos Raspberry Pis**. Dessa forma, trago aqui uma versão revisada e melhorada deste artigo.

Na primeira versão do artigo, eu estava utilizando o **Flannel** como **CNI**. No entanto, como a ideia agora é estudar para a **CKS**, preciso de um **CNI** com suporte a **Network Policies**. Por isso, nesta atualização, vamos utilizar o **Calico**.

Além disso, o **Ubuntu 20.04 (que era o sistema utilizado anteriormente)** terá seu fim do suporte **standard** em **31 de maio de 2025**. Dessa forma, vamos utilizar uma versão mais atualizada de sistema operacional:** Raspberry Pi OS Lite (64-bit)**. Ele é basicamente um port do **Debian Bookworm**, que é minha distribuição preferida. Sempre utilizei **Debian** nos meus projetos ao longo da vida, então faz sentido tê-lo aqui.

Para o **Kubernetes**, vamos utilizar a versão **1.32**.

Outro ponto interessante é que estou revitalizando meu **homelab**. Nas próximas semanas, pretendo começar a montar um **NAS** com Raspberry Pi. A ideia é que ele seja minha mídia de backup para arquivos pessoais e também sirva de volume para o cluster Kubernetes.

O plano é utilizar um **Raspberry Pi 5** com um** HAT SATA e 4 HDDs**. Acho que vai ficar bem legal! Com isso, devo aposentar o **Raspberry Pi 3** que hoje roda meu **DNS Pi-hole** e movê-lo para dentro do cluster Kubernetes ou talvez para meu **Proxmox** (o que provavelmente será o destino final dele), mas ainda não tenho certeza. Aguarde cenas dos próximos capítulos.

Agora, vamos às atualizações técnicas!

## **Objetivo**

Instalar e configurar um Cluster Kubernetes Raspberry Pi com **Kubeadm**, **Containerd** e **Calico**.

## **Premissas**

Pra simplificar o artigo, não vou me atentar na instalação do sistema operacional, você pode utilizar o **Raspberry Pi imager** e selecionar a distribuição mais adequada.

O sistema operacional utilizado será o **Raspberry Pi OS Lite (64-bit).**

Após a instalação do sistema operacional, faça as configurações básicas:

- Hostname
    - k8s-master
    - k8s-node01
    - k8s-node02
- IP estático
- DNS
- Acesso SSH
- Updates

## **Antes de começar**

- Uma máquina com sistema operacional Linux compatível. O projeto Kubernetes provê instruções para distribuições Linux baseadas em Debian e Red Hat, bem como para distribuições sem um gerenciador de pacotes.
- 2 GB ou mais de RAM por máquina (menos que isso deixará pouca memória para as suas aplicações).
- 2 CPUs ou mais.
- Conexão de rede entre todas as máquinas no cluster. Seja essa pública ou privada.
- Nome da máquina na rede, endereço MAC e producy_uuid únicos para cada nó. 
- Portas específicas abertas nas suas máquinas. 
- **Swap desabilitado**. Você precisa desabilitar a funcionalidade de swap para que o kubelet funcione de forma correta

Nunca é demais lembrar: Minimamente você vai precisar de:

- Três (ou mais) Raspberry Pi 4s (de preferência os modelos de 4 GB de RAM)
- Sistema operacional ARM64 em todos os Raspberry Pis

## **1.1 Pré-requisitos (Master e Worker)**

Atualizando o sistema:

```bash
sudo apt update
sudo apt upgrade
```

Desabilitar **SWAP**

```bash
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
sudo rm -f /var/swap
swapon --show
```
Configurando Forwarding IPv4 Iptables

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

```bash
sudo sysctl --system
```

## **1.2 Instalação do containerd (Master Node e Worker Node)**

Os pacotes do **containerd.io** são distribuídos pelo Docker (não pelo projeto containerd).

```bash
sudo apt install containerd
```

## **1.3 Configurando o systemd driver cgroup (Master Node e Worker Node)**

Para usar o systemd driver cgroup **/etc/containerd/config.toml** com **runc**, defina:

```bash
sudo mkdir -p /etc/containerd
```

```bash
sudo su
containerd config default > /etc/containerd/config.toml
```

Modifique o **SystemdCgroup** para `true`

```bash
vim /etc/containerd/config.toml
```

```bash
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
   SystemdCgroup = true
```

Reinicie o containerd

```bash
systemctl enable containerd
systemctl restart containerd
exit
```

## **2.1 Instalação do kubeadm, kubelet e kubectl (Somente Master Node)**

Atualize o índice de pacotes **apt** e instale os pacotes necessários para utilizar o repositório **apt** do Kubernetes:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```
Faça o download da chave de assinatura pública da Google Cloud:

```bash
sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Adicione o repositório apt do **Kubernetes**:

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Atualize o índice de pacotes **apt**, instale o kubelet, o kubeadm e o kubectl, e fixe suas versões:

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## **2.2 Configurando o memory cgroup (Master Node e Worker Node)**

```bash
sudo vi /boot/firmware/cmdline.txt
```

Adicione ao final:

```bash
cgroup_enable=cpuset cgroup_enable=memory
```

Reinicie o Raspbery Pi

```bash
sudo reboot
```

## **2.3 Pull de imagens do Kubernetes (Somente Master Node)**

Os objetos que compõem o cluster também são contêineres, vamos baixar as imagens necessárias para o Kubernetes.

```bash
sudo kubeadm config images pull
```

## **2.4 Criar e iniciar o cluster (Somente Master Node)**

Vamos utilizar o **kubeadm** para criar e configurar nosso cluster. Vamos aproveitar e passar nosso **CIDR** de rede que utilizaremos em nossos PODs.

```bash
sudo kubeadm init --control-plane-endpoint=$HOSTNAME
```

Se tudo correr bem, teremos uma saída como esta:

```bash
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join k8s-master:6443 --token zrvqje.ken51fpgndcrpyu1 \
    --discovery-token-ca-cert-hash
sha256:715dceaaa6bf95bff97e1503a8e385d5d79ebb4d3ff1a5388090f9b370f99718
```

Agora, vamos criar um usuário regular de acordo com as informações acima:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Desta forma já podemos verificar o status de nosso cluster:

```bash
kubectl get nodes
```

```bash
asilva@lk8s-master-001:~$ kubectl get nodes
NAME              STATUS      ROLES           AGE     VERSION
lk8s-master-001   NotReady    control-plane   3d4h    v1.32.3
```

Veja que nosso cluster ainda está com status de **NotReady** isso acontece pois ainda não temos um controlador de rede para nosso cluster.

## **2.5 Instalando Addons de rede (Somente Master Node)**

O Kubernetes tem uma lista de add-ons disponíveis para instalação, fique à vontade para escolher a que melhor se adapta a suas necessidades.

**Mais informações em:** <a href="https://kubernetes.io/docs/concepts/cluster-administration/addons/" target="_blank"> Kubernetes Addons

Agora, vou utilizar o **Calico** que é um **CNI** com suporte a **Network Policies**.

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.0/manifests/calico.yaml
```

Após a instalação, podemos verificar se tudo está ok.

```bash
kubectl get pods -A
```

![](/assets/img/107/k8spi01.png){: "width=60%" }

Agora, podemos verificar o status de nosso cluster novamente:

```bash
kubectl get nodes
```

Veja que agora nosso cluster está com o status de **Ready**, isso conclui que estamos com nosso cluster operacional.

```bash
asilva@lk8s-master-001:~$ kubectl get nodes
NAME              STATUS   ROLES           AGE     VERSION
lk8s-master-001   Ready    control-plane   3d4h    v1.32.3
```

## **3.1 Configurando Worker Nodes**

Basicamente, você precisa seguir o artigo novamente e fazer a instalação dos componentes do Kubernetes nos workers node.

Basta se atentar para as **TAGs** no em cada tópico **(Somente Master Node)** e **(Master Node e Worker Node)**.

Faça a instalação e configuração dos tópicos **(Master Node e Worker Node)** e siga com a instalação do **kubeadm** e **kubelet**.

>Em nosso **Worker Node**, só precisamos do **Kubeadm** e **Kubelet**. O **kubeclt** só é necessário no **Master Node**, onde vamos gerenciar nosso cluster.
{: .prompt-warning }

Atualize o índice de pacotes **apt** e instale os pacotes necessários para utilizar o repositório **apt** do Kubernetes:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
Faça o download da chave de assinatura pública da Google Cloud:

```bash
sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Adicione o repositório apt do **Kubernetes**:

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Atualize o índice de pacotes **apt**, instale o kubelet, o kubeadm, e fixe suas versões:

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm
sudo apt-mark hold kubelet kubeadm
```

## **3.1 Adicionando Worker Node ao cluster**

Agora, basta adicionar nosso Worker Node ao nosso cluster Kubernetes.

Para fazer o join é necessário ter o Token gerado na configuração do nosso Master Node (control-plane).

Caso não tenha o token, volte ao **Master Node** e digite o seguinte comando:

```bash
kubeadm token create --print-join-command
```

![](/assets/img/107/k8spi02.png){: "width=60%" }

Feito o join você terá a saída abaixo:

![](/assets/img/107/k8spi03.png){: "width=60%" }

>Caso você esteja enfrentando dificuldades para estabelecer comunicação com o Master Node, provavelmente o comando de join está apontando para o nome qualificado do nó mestre. Você pode resolver isso de várias formas: Adicionando no arquivo hosts os nomes e IPs dos seus dispositivos ou editando o comando de join para substituir o nome do Master Node pelo seu IP, seguido da porta correspondente. 
{: .prompt-info }

Volte ao Master Node e verifique se o Worker Node está ativo no cluster.

```bash
kubectl get nodes 
```

![](/assets/img/107/k8spi04.png){: "width=60%" }

Pronto! Agora temos um C**luster Kubernetes** atualizado com **Calico** rodando no **Raspberry Pi OS Lite (64-bit).**

Nos próximos passos, vou continuar aprimorando o homelab e explorar como configurar um **NAS** utilizando um **Raspberry Pi 5**!

É isso, galera! Se você gostou do artigo, comenta ou mande pra galera que também quer aprender mais sobre HomeLabs e Kubernetes! 

Se você tiver alguma dúvida ou comentário, sinta-se à vontade para compartilhá-los conosco na seção de comentários abaixo!

Forte Abraço!
