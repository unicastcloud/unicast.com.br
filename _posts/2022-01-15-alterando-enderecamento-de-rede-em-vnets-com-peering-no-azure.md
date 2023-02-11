---
layout: post
title: "Alterando endereçamento de rede em VNETs com peering no Azure"
author: asilva
date: 2022-01-15 09:00:00 -0500
categories: [Azure, Network]
tags: [azure, microsoft, network, vnet, peering, hubandspoke]
---

Fala galera! Seis tão baum?

Quem trabalha com redes no modelo Hub/Spoke no Azure certamente já teve problemas em modificar o espaço de rede de uma VNET.

![](/assets/img/16/vnets1.png){: "width=60%" }

Isso acontece devido a configuração de peering entre as VNETs, de forma simples, não é possível modificar o espaço de rede sem remover o peering das VNETs.

![](/assets/img/16/vnets2.png){: "width=60%" }

Remover e criar o peering entre as VNETs não é algo complicado ou mesmo demorado, o maior problema é a perda de conectividade que o ambiente sofrerá durante o procedimento de remoção, mudança de endereçamento e criação do novo peering.

Recentemente, a Microsoft anunciou uma nova maneira de lidar com esse problema, alterando o espaço de endereço sem a necessidade de remover o peering.

### **1.1 Registrar o recurso**

A primeira coisa que devemos fazer é habilitar a nova feature.

Vá na sua assinatura e em seguida, **Settings > Preview features.**

![](/assets/img/16/vnets3.png){: "width=60%" }

Procure por **AllowUpdateAddressSpaceInPeeredVnets**, selecione e clique em Register.

![](/assets/img/16/vnets4.png){: "width=60%" }

### **2.1 Modificar o espaço de rede**

Agora já é possível modificar o novo espaço de rede para sua VNET sem precisar excluir o peering entre elas.

![](/assets/img/16/vnets5.png){: "width=60%" }

### **3.1 Sincronizar VNETs**

Após a modificação, é necessário sincronizar o novo endereçamento entre as VNETs com peering.

No portal do Azure, você verá uma mensagem como está nas configurações de peering da VNET.

![](/assets/img/16/vnets6.png){: "width=60%" }

Para fazer o sincronismo selecione a VNET e clique em Sync.

![](/assets/img/16/vnets7.png){: "width=60%" }

Pronto, agora você tem suas VNETs atualizadas sem qualquer downtime.

Durante o processo de configuração e a sincronização dos peerings, a rede continua funcionando sem qualquer perda de pacote ou interrupção.

Para mais detalhes sobre rede Hub/Spoke no Azure: <a href="https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=clil" target="_blank"> Hub-spoke network topology in Azure</a> 

Para mais detalhes sobre a nova atualização: <a href="https://azure.microsoft.com/en-us/blog/how-to-resize-azure-virtual-networks-that-are-peered-now-in-preview/#:~:text=To%20do%20this%20in%20PowerShell,the%20following%20feature%20flag%3A%20Microsoft." target="_blank"> How to re-size Azure virtual networks that are peered</a>

É isso galera, espero que gostem.

Forte abraço.

