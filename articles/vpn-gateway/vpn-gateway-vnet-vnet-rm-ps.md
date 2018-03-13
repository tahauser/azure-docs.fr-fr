---
title: "Connecter un réseau virtuel Azure à un autre réseau virtuel à l’aide d’une connexion de réseau virtuel à réseau virtuel : PowerShell | Microsoft Docs"
description: "Connectez des réseaux virtuels avec une connexion de réseau virtuel à réseau virtuel et PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 8f2ecf4f10c5506b79adb033c67c41a4b1e17777
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Configurer une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide de PowerShell

Cet article vous explique comment connecter des réseaux virtuels avec une connexion de réseau virtuel à réseau virtuel. Les réseaux virtuels peuvent être situés dans des régions identiques ou différentes et appartenir à des abonnements identiques ou différents. Lors de la connexion de réseaux virtuels provenant de différents abonnements, les abonnements ne sont pas tenus d’être associés au même locataire Active Directory.

Les étapes mentionnées dans cet article s’appliquent au modèle de déploiement Resource Manager et utilisent PowerShell. Vous pouvez également créer cette configuration à l’aide d’un autre outil ou modèle de déploiement en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [interface de ligne de commande Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portail Azure (classique)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connexions entre différents modèles de déploiement - Portail Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connexions entre différents modèles de déploiement - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>À propos de la connexion de réseaux virtuels

Il existe plusieurs manières de se connecter à des réseaux virtuels. Les sections ci-dessous décrivent les différentes manières de se connecter à des réseaux virtuels.

### <a name="vnet-to-vnet"></a>Connexion entre deux réseaux virtuels

La configuration d’une connexion de réseau virtuel à réseau virtuel est un bon moyen de se connecter facilement à des réseaux virtuels. La connexion entre deux réseaux virtuels est semblable à la création d’une connexion IPsec de site à site dans un emplacement local.  Ces deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE et communiquent de la même façon. La différence entre ces types de connexion se trouve dans la configuration de la passerelle réseau locale. Lorsque vous créez une connexion de réseau virtuel à réseau virtuel, vous ne voyez pas l’espace d’adressage de la passerelle réseau locale. L’espace est créé et rempli automatiquement. Si vous mettez à jour l’espace d’adressage pour un réseau virtuel, l’autre réseau virtuel achemine automatiquement le trafic vers le nouvel espace d’adressage. La création d’une connexion de réseau virtuel à réseau virtuel est généralement plus rapide et plus simple que la création d’une connexion de site à site entre des réseaux virtuels.

### <a name="site-to-site-ipsec"></a>Site à site (IPsec)

Si vous travaillez avec une configuration réseau complexe, vous pouvez connecter vos réseaux virtuels à l’aide des étapes [site à site](vpn-gateway-create-site-to-site-rm-powershell.md), à la place des étapes de réseau virtuel à réseau virtuel. Lorsque vous utilisez les étapes de site à site, vous créez et configurez manuellement les passerelles réseau locales. La passerelle de réseau local pour chaque réseau virtuel traite l’autre réseau virtuel comme un site local. Ainsi, vous pourrez spécifier un espace d’adressage supplémentaire pour la passerelle réseau locale afin d’acheminer le trafic. Si l’espace d’adressage pour un réseau virtuel est modifié, vous devez mettre à jour la passerelle réseau locale correspondante pour le refléter. Elle n’est pas automatiquement mise à jour.

### <a name="vnet-peering"></a>Homologation de réseaux virtuels

Vous envisagerez probablement de connecter vos réseaux virtuels à l’aide de VNet Peering. VNet Peering n’utilise pas une passerelle VPN et possède d’autres contraintes. En outre, la [tarification de VNet Peering](https://azure.microsoft.com/pricing/details/virtual-network) est différente de la [tarification de la passerelle VPN de réseau virtuel à réseau virtuel](https://azure.microsoft.com/pricing/details/vpn-gateway). Pour plus d’informations, consultez l’article [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Pourquoi créer une connexion de réseau virtuel à réseau virtuel ?

Vous pouvez décider de connecter des réseaux virtuels à l’aide d’une connexion de réseau virtuel à réseau virtuel pour les raisons suivantes :

* **Géo-redondance et présence géographique dans plusieurs régions**

  * Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
  * Avec Traffic Manager et l’équilibreur de charge Azure, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.
* **Applications multiniveaux régionales avec une limite d’isolement ou administrative**

  * Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés pour des besoins d’isolement ou d’administration.

Vous pouvez combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels.

## <a name="steps"></a>Quelles étapes de réseau virtuel à réseau virtuel dois-je utiliser ?

Cet article inclut deux ensembles d’étapes distincts. L’un des ensembles est destiné aux [réseaux virtuels qui se trouvent dans le même abonnement](#samesub), et l’autre aux [réseaux virtuels qui se trouvent dans des abonnements différents](#difsub).
La différence essentielle entre ces deux ensembles est que vous devez utiliser des sessions PowerShell distinctes quand vous configurez les connexions pour des réseaux virtuels qui se trouvent dans des abonnements différents. 

Pour cet exercice, vous pouvez combiner des configurations ou choisir simplement celle que vous voulez utiliser. Toutes les configurations utilisent la connexion de réseau virtuel à réseau virtuel. Le trafic circule entre les réseaux virtuels connectés directement entre eux. Dans cet exercice, le trafic du réseau TestVNet4 n’est pas acheminé jusqu’au réseau TestVNet5.

* [Réseaux virtuels qui se trouvent dans le même abonnement](#samesub) : les étapes de cette configuration utilisent les réseaux TestVNet1 et TestVNet4.

  ![Diagramme v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Réseaux virtuels qui se trouvent dans des abonnements différents](#difsub) : les étapes de cette configuration utilisent les réseaux TestVNet1 et TestVNet5.

  ![Diagramme v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>Connexion de réseaux virtuels situés dans le même abonnement

### <a name="before-you-begin"></a>Avant de commencer

Au préalable, vous devez installer la dernière version des applets de commande PowerShell Azure Resource Manager, au moins la version 4.0 ou ultérieure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

### <a name="Step1"></a>Étape 1 : planifier vos plages d’adresses IP

Dans les étapes suivantes, vous allez créer deux réseaux virtuels avec leurs sous-réseaux de passerelle respectifs et leur configuration. Vous allez ensuite créer une connexion VPN entre les deux réseaux virtuels. Il est important de planifier les plages d’adresses IP pour votre configuration réseau. N’oubliez pas que vous devez vous assurer qu’aucune plage de réseaux virtuels ou de réseaux locaux ne se chevauche. Dans ces exemples, nous n’incluons pas de serveur DNS. Si vous souhaitez une résolution de noms pour vos réseaux virtuels, consultez la page [Résolution de noms](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Nous utilisons les valeurs suivantes dans les exemples :

**Valeurs pour TestVNet1 :**

* Nom du réseau virtuel : TestVNet1
* Groupe de ressources : TestRG1
* Emplacement : Est des États-Unis
* TestVNet1 : 10.11.0.0/16 et 10.12.0.0/16
* FrontEnd : 10.11.0.0/24
* BackEnd : 10.12.0.0/24
* GatewaySubnet : 10.12.255.0/27
* GatewayName : VNet1GW
* Adresse IP publique : VNet1GWIP
* Type de VPN : RouteBased
* Connection(1to4) : VNet1toVNet4
* Connection(1to5) : VNet1toVNet5 (pour les réseaux virtuels se trouvant dans des abonnements différents)
* Type de connexion : VNet2VNet

**Valeurs pour TestVNet4 :**

* Nom du réseau virtuel : TestVNet4
* TestVNet2 : 10.41.0.0/16 et 10.42.0.0/16
* Serveur frontal : 10.41.0.0/24
* Serveur principal : 10.42.0.0/24
* Sous-réseau de passerelle : 10.42.255.0/27
* Groupe de ressources : TestRG4
* Emplacement : États-Unis de l’Ouest
* Nom de la passerelle : VNet4GW
* Adresse IP publique : VNet4GWIP
* Type de VPN : RouteBased
* Connexion : VNet4toVNet1
* Type de connexion : VNet2VNet


### <a name="Step2"></a>Étape 2 : créez et configurez TestVNet1

1. Déclarez vos variables. Cet exemple déclare les variables avec les valeurs de cet exercice. Dans la plupart des cas, vous devez remplacer les valeurs par les vôtres. Cependant, vous pouvez utiliser ces variables si vous exécutez la procédure pour vous familiariser avec ce type de configuration. Si besoin, modifiez les variables, puis copiez et collez-les dans la console PowerShell.

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```

2. Se connecter à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

  ```powershell
  Login-AzureRmAccount
  ```

  Vérifiez les abonnements associés au compte.

  ```powershell
  Get-AzureRmSubscription
  ```

  Spécifiez l’abonnement à utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```
3. Créez un groupe de ressources.

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. Créez les configurations de sous-réseau pour TestVNet1. Cet exemple crée un réseau virtuel nommé TestVNet1 et trois sous-réseaux nommés GatewaySubnet, FrontEnd et Backend. Lorsque vous remplacez les valeurs, pensez à toujours nommer votre sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la création de votre passerelle échoue.

  L’exemple suivant utilise les variables définies précédemment. Dans cet exemple, le sous-réseau de passerelle utilise /27. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau plus vaste qui inclut un plus grand nombre d’adresses en sélectionnant au moins /28 ou /27. Cela permettra à un nombre suffisant d’adresses de s’adapter à de possibles configurations supplémentaires possibles que vous êtes susceptible de souhaiter par la suite.

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. Créez TestVNet1.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. Demandez l’allocation d’une adresse IP publique à la passerelle que vous allez créer pour votre réseau virtuel. Notez que la valeur AllocationMethod est dynamique. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est allouée à votre passerelle de façon dynamique. 

  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. Créez la configuration de la passerelle. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple pour créer la configuration de votre passerelle.

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. Créez la passerelle pour TestVNet1. Dans cette étape, vous créez la passerelle de réseau virtuel de votre TestVNet1. Les configurations de réseau virtuel à réseau virtuel requièrent un VPN de type RouteBased. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>Étape 3 : créez et configurez TestVNet4

Une fois que vous avez configuré TestVNet1, créez TestVNet4. Suivez les étapes ci-dessous, en remplaçant les valeurs par les vôtres si nécessaire. Cette étape peut être réalisée dans la même session PowerShell, car elle se trouve dans le même abonnement.

1. Déclarez vos variables. Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
2. Créez un groupe de ressources.

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. Créez les configurations de sous-réseau pour TestVNet4.

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. Créez TestVNet4.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. Demandez une adresse IP publique

  ```powershell
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. Créez la configuration de la passerelle.

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. Créer la passerelle TestVNet4. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-4---create-the-connections"></a>Étape 4 : créez les connexions

1. Obtenez les passerelles de réseau virtuel. Si les deux passerelles appartiennent au même abonnement, comme dans l’exemple, vous pouvez effectuer cette étape dans la même session PowerShell.

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. Créez la connexion de TestVNet1 à TestVNet4. Lors de cette étape, vous créez la connexion de TestVNet1 à TestVNet4. Une clé partagée est référencée dans les exemples. Vous pouvez utiliser vos propres valeurs pour cette clé partagée. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. Créez la connexion de TestVNet4 à TestVNet1. Cette étape est similaire à celle présentée ci-dessus, sauf que vous créez la connexion de TestVNet4 à TestVNet1. Vérifiez que les clés partagées correspondent. Après quelques minutes, la connexion est établie.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. Vérifiez votre connexion. Consultez la section [Vérification de votre connexion](#verify).

## <a name="difsub"></a>Connexion de réseaux virtuels situés dans différents abonnements

Dans ce scénario, vous connectez TestVNet1 et TestVNet5. TestVNet1 et TestVNet5 se trouvent dans des abonnements différents. Les abonnements ne sont pas tenus d’être associés au même locataire Active Directory. La différence entre ce processus et le précédent réside dans le fait qu’une partie des étapes de configuration doit être exécutée dans une session PowerShell distincte dans le contexte d’un deuxième abonnement. notamment lorsque les deux abonnements appartiennent à différentes organisations.

### <a name="step-5---create-and-configure-testvnet1"></a>Étape 5 : créez et configurez TestVNet1

Vous devez terminer les étapes [1](#Step1) et [2](#Step2) de la section précédente pour créer et configurer TestVNet1 et la passerelle VPN pour TestVNet1. Pour cette configuration, vous n’êtes pas obligé de créer TestVNet4 de la section précédente. Même si vous le créez, ce dernier n’entrera pas en conflit avec ces étapes. Une fois les étapes 1 et 2 effectuées, passez à l’étape 6 pour créer TestVNet5. 

### <a name="step-6---verify-the-ip-address-ranges"></a>Étape 6 : vérifiez les plages d’adresses IP

Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel TestVNet5 ne chevauche aucune de vos plages de réseaux virtuels ou de passerelles de réseau locales. Dans cet exemple, les réseaux virtuels peuvent appartenir à différentes organisations. Pour cet exercice, vous pouvez utiliser les valeurs suivantes pour TestVNet5 :

**Valeurs pour TestVNet5 :**

* Nom du réseau virtuel : TestVNet5
* Groupe de ressources : TestRG5
* Emplacement : Japon de l’Est
* TestVNet5 : 10.51.0.0/16 et 10.52.0.0/16
* Serveur frontal : 10.51.0.0/24
* Serveur principal : 10.52.0.0/24
* Sous-réseau de passerelle : 10.52.255.0.0/27
* Nom de la passerelle : VNet5GW
* Adresse IP publique : VNet5GWIP
* Type de VPN : RouteBased
* Connexion : VNet5toVNet1
* Type de connexion : VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Étape 7 : créez et configurez TestVNet5

Cette étape doit être effectuée dans le cadre du nouvel abonnement. Cette partie peut être effectuée par l’administrateur dans une organisation différente qui possède l’abonnement.

1. Déclarez vos variables. Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. Connectez-vous à l’abonnement 5. Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

  ```powershell
  Login-AzureRmAccount
  ```

  Vérifiez les abonnements associés au compte.

  ```powershell
  Get-AzureRmSubscription
  ```

  Spécifiez l’abonnement à utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. Créez un groupe de ressources.

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. Créez les configurations de sous-réseau pour TestVNet5.

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. Créez TestVNet5.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. Demandez une adresse IP publique

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. Créez la configuration de la passerelle.

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. Créez la passerelle TestVNet5.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-8---create-the-connections"></a>Étape 8 : créez les connexions

Dans cet exemple, étant donné que les passerelles se trouvent dans différents abonnements, nous avons divisé cette étape en deux sessions PowerShell notées [Abonnement 1] et [Abonnement 5].

1. **[Abonnement 1]** Obtenez la passerelle de réseau virtuel pour Abonnement 1. Se connecter et se connecter à Abonnement 1 avant d’exécuter l’exemple suivant :

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```

  Copiez la sortie des éléments suivants et envoyez-la à l’administrateur d’Abonnement 5 par message électronique ou une autre méthode.

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```

  Ces deux éléments ont des valeurs similaires à l’exemple de sortie suivant :

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[Abonnement 5]** Obtenez la passerelle de réseau virtuel pour Abonnement 5. Se connecter et se connecter à Abonnement 5 avant d’exécuter l’exemple suivant :

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```

  Copiez la sortie des éléments suivants et envoyez-la à l’administrateur d’Abonnement 1 par message électronique ou une autre méthode.

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```

  Ces deux éléments ont des valeurs similaires à l’exemple de sortie suivant :

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[Abonnement 1]** Créez la connexion TestVNet1 à TestVNet5. Dans cette étape, vous créez la connexion de TestVNet1 à TestVNet5. La différence réside dans le fait que $vnet5gw ne peut pas être obtenu directement, car il se trouve dans un abonnement différent. Vous devez créer un objet PowerShell avec les valeurs communiquées par Abonnement 1 dans les étapes précédentes. Utilisez l’exemple ci-dessous. Remplacez le nom, l’ID et la clé partagée par vos propres valeurs. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.

  Se connecter à Abonnement 1 avant d’exécuter l’exemple suivant :

  ```powershell
  $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[Abonnement 5]** Créez la connexion TestVNet5 à TestVNet1. Cette étape est similaire à celle présentée ci-dessus, sauf que vous créez la connexion de TestVNet5 à TestVNet1. La procédure de création d’un objet PowerShell basé sur les valeurs obtenues à partir d’Abonnement 1 s’applique également ici. Dans cette étape, vérifiez que les clés partagées correspondent.

  Se connecter à Abonnement 5 avant d’exécuter l’exemple suivant :

  ```powershell
  $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  $Connection51 = "VNet5toVNet1"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>Vérification d’une connexion

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>Forum Aux Questions sur l’interconnexion de réseaux virtuels

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .
* Pour plus d’informations sur le protocole BGP, consultez les articles [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [Comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).