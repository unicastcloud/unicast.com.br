---
layout: post
title: "Cluster Kubernetes Raspberry PI com Kubeadm e Containerd"
author: asilva
date: 2022-09-02 09:00:00 -0500
categories: [DevOps, Kubernetes]
tags: [kubernetes, devops, network, raspberrypi, k8s, cluster, homelab]
---

Fala galera! Seis tão baum?

Atualmente estou estudando para o exame **Certified Kubernetes Administrator (CKA)** e como se trata de um exame 100% prático, a única maneira de estar pronto é fazendo bastante laboratório.

Bom, **Kubernetes** e **Azure Kubernetes Services (AKS)** não são atividades do meu dia a dia, pelo menos não era. Como alguns de vocês sabem, eu fiquei um ano trabalhando como Azure Team Leader e agora estou desempenhando uma nova função como DevOps Tech Lead.

![](/assets/img/34/k8spi4-01.gif){: "width=60%" }

É uma volta para área técnica, pero no mucho, ainda tenho bastante demanda operacional e administrativa, mas agora não tenho as demandas relacionadas a gestão de time e pessoas.

Com isso, tenho maior liberdade para trabalhar com arquitetura e automação, além de contribuir de forma mais ativa com o desenvolvimento do time técnico.

História contada, vamos ao que interessa!

Existem muitos métodos de instalação do Kubernetes, e para não fugir do tópico principal, vou citar os mais relevantes.

- **Minikube**: <a href="https://minikube.sigs.k8s.io" target="_blank"> https://minikube.sigs.k8s.io
- **Kind**: <a href="https://kind.sigs.k8s.io" target="_blank"> https://kind.sigs.k8s.io
- **K3s**: <a href="https://k3s.io" target="_blank"> https://k3s.io
- **K3d**: <a href="https://github.com/rancher/k3d" target="_blank"> https://github.com/rancher/k3d
- **Microk8s**: <a href="https://microk8s.io" target="_blank"> https://microk8s.io

Em tempo, faça uma pesquisa e avalie qual será a melhor opção a você.

Para a prova, o cenário ideal é estar o mais próximo possível de um cluster **Self-Hosting/Bare Metal**. É necessário ter controle total do seu cluster para poder estudar todos os conceitos.

E é por isso que fazer o deploy de um cluster gerenciado como o **Azure Kubernetes Service (AKS)** não faz sentido, o Azure gerencia o cluster aliviando quase toda a sobrecarga de administração, configuração e execução do Kubernetes, principalmente o plano de controle. 

Neste artigo iremos utilizar a instalação manual, no modelo cluster mínimo viável com **kubeadm**.

O **kubeadm** é uma ferramenta utilizada para implantar um cluster de forma manual. Ele é usado para inicializar componentes do Kubernetes. Antes de inicializar o cluster, algumas ações devem ser feitas manualmente.

**Mais informações em:** <a href="https://kubernetes.io/docs/reference/setup-tools/kubeadm" target="_blank"> https://kubernetes.io/docs/reference/setup-tools/kubeadm

Na data deste artigo, a **Linux Foundation** está cobrando a versão **v1.24** do Kubernetes, e é por este motivo que vamos usar o **Containerd** como **Container Runtime Interface (CRI)**.

O Kubernetes removeu o **Docker** como **Container Runtime Interface (CRI)** na versão **v1.23**. E a principal mudança na versão **v1.24** do Kubernetes é a remoção do **Dockershim**.

>O Docker como um runtime subjacente está sendo preterido em favor de runtimes que usam a Container Runtime Interface (CRI) criada para Kubernetes. As imagens produzidas pelo Docker continuarão a funcionar em seu cluster com todos os tempos de execução, como sempre.
{: .prompt-info }

![](/assets/img/34/k8spi4-02.gif){: "width=60%" }

**Então, bora colocar esse cluster para funcionar!!!**

Se você quiser saber mais detalhes sobre meu cluster, confira os artigos da sessão **Homelab**!

### **Objetivo**

Instalar e configurar Cluster Kubernetes Raspberry PI com Kubeadm e Containerd.

### **Premissas**

Pra simplificar o artigo, não vou me atentar na instalação do sistema operacional, você pode utilizar o **Raspberry Pi imager** e selecionar a distribuição mais adequada.

Vou utilizar o sistema **Ubuntu Server 20.04.5 LTS (64-bit)**.

Após a instalação do sistema operacional, lembre-se de fazer as configurações básicas de seus hosts:

- Hostname
    - k8s-master
    - k8s-node01
    - k8s-node02
- IP estático
- DNS
- Acesso SSH
- Updates

### **Antes de começar**

- Uma máquina com sistema operacional Linux compatível. O projeto Kubernetes provê instruções para distribuições Linux baseadas em Debian e Red Hat, bem como para distribuições sem um gerenciador de pacotes.
- 2 GB ou mais de RAM por máquina (menos que isso deixará pouca memória para as suas aplicações).
- 2 CPUs ou mais.
- Conexão de rede entre todas as máquinas no cluster. Seja essa pública ou privada.
- Nome da máquina na rede, endereço MAC e producy_uuid únicos para cada nó. Mais detalhes podem ser lidos aqui.
- Portas específicas abertas nas suas máquinas. Você poderá ler quais são aqui.
- **Swap desabilitado**. Você precisa desabilitar a funcionalidade de swap para que o kubelet funcione de forma correta

Nunca é demais lembrar: Minimamente você vai precisar de:

- Três (ou mais) Raspberry Pi 4s (de preferência os modelos de 4 GB de RAM)
- Sistema operacional ARM64 em todos os Raspberry Pis

### **1.1 Pré-requisitos (Master e Worker)**

Atualizando o sistema:

```bash
sudo apt update
sudo apt upgrade
```

Desabilitar **SWAP**

```bash
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a
```
Forwarding IPv4 Iptables

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

### **1.2 Instalação do containerd (Master Node e Worker Node)**

Os pacotes do **containerd.io** são distribuídos pelo Docker (não pelo projeto containerd).

```bash
sudo apt install containerd
```

### **1.3 Configurando o systemd driver cgroup (Master Node e Worker Node)**

Para usar o systemd driver cgroup **/etc/containerd/config.toml** com **runc**, defina:

```bash
sudo mkdir -p /etc/containerd
```

```bash
sudo su
containerd config default > /etc/containerd/config.toml
exit
```

Reinicie o containerd

```bash
sudo systemctl restart containerd
```

### **2.1 Instalação do kubeadm, kubelet e kubectl (Somente Master Node)**

Atualize o índice de pacotes **apt** e instale os pacotes necessários para utilizar o repositório **apt** do Kubernetes:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
Faça o download da chave de assinatura pública da Google Cloud:

```bash
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

Adicione o repositório apt do **Kubernetes**:

```bash
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Atualize o índice de pacotes **apt**, instale o kubelet, o kubeadm e o kubectl, e fixe suas versões:

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### **2.2 Configurando o memory cgroup (Master Node e Worker Node)**

```bash
cgroup="$(head -n1 /boot/firmware/cmdline.txt) cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1 swapaccount=1"
```

```bash
echo $cgroup | sudo tee /boot/firmware/cmdline.txt
```

Reinicie o Raspbery Pi

```bash
sudo reboot
```

### **2.3 Pull de imagens do Kubernetes (Somente Master Node)**

Os objetos que compõem o cluster também são contêineres, vamos baixar as imagens necessárias para o Kubernetes.

```bash
sudo kubeadm config images pull
```

### **2.4 Criar e iniciar o cluster (Somente Master Node)**

Vamos utilizar o **kubeadm** para criar e configurar nosso cluster. Vamos aproveitar e passar nosso **CIDR** de rede que utilizaremos em nossos PODs.

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 
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

kubeadm join 172.16.0.200:6443 --token zrvqje.ken51fpgndcrpyu1 \
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
![](/assets/img/34/k8spi4-03.png){: "width=60%" }

Veja que nosso cluster ainda está com status de **NotReady** isso acontece pois ainda não temos um controlador de rede para nosso cluster.

### **2.5 Instalando Addons de rede (Somente Master Node)**

O Kubernetes tem uma lista de add-ons disponíveis para instalação, fique à vontade para escolher a que melhor se adapta a suas necessidades.

**Mais informações em:** <a href="https://kubernetes.io/docs/concepts/cluster-administration/addons/" target="_blank"> Kubernetes Addons

No meu caso, vou utilizar o **Flannel** ele é um provider de rede de camada 3 simples e de fácil configuração.

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

Após a instalação, podemos verificar se tudo está ok.

```bash
kubectl get pods -A
```

![](/assets/img/34/k8spi4-04.png){: "width=60%" }

Agora, podemos verificar o status de nosso cluster novamente:

```bash
kubectl get nodes
```

Veja que agora nosso cluster está com o status de **Ready**, isso conclui que estamos com nosso cluster operacional.

![](/assets/img/34/k8spi4-05.png){: "width=60%" }

### **3.1 Configurando Worker Nodes**

Basicamente, você precisa seguir o artigo novamente e fazer a instalação dos componentes do Kubernetes nos workers node.

Basta se atentar para as TAGs no em cada tópico **(Somente Master Node)** e **(Master Node e Worker Node)**.

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
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

Adicione o repositório apt do **Kubernetes**:

```bash
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Atualize o índice de pacotes **apt**, instale o kubelet, o kubeadm, e fixe suas versões:

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm
sudo apt-mark hold kubelet kubeadm
```

### **3.1 Adicionando Worker Node ao cluster**

Agora, basta adicionar nosso Worker Node ao nosso cluster Kubernetes.

Para fazer o join é necessário ter o Token gerado na configuração do nosso Master Node (control-plane).

Caso não tenha o token, volte ao **Master Node** e digite o seguinte comando:

```bash
kubeadm token create --print-join-command
```
![](/assets/img/34/k8spi4-06.png){: "width=60%" }

Feito o join você terá a saída abaixo:

![](/assets/img/34/k8spi4-07.png){: "width=60%" }

Volte ao Master Node e verifique se o Worker Node está ativo no cluster.

```bash
kubectl get nodes 
```

![](/assets/img/34/k8spi4-08.png){: "width=60%" }

Bazinga! Temos um cluster Kubernetes funcional, agora basta repetir os processos e continuar adicionando os próximos workers.

Bom, é isso, agora é brincar e estudar muito! 

Logo, trago mais atualizações do projeto e estudos!

Forte abraço a todos!


