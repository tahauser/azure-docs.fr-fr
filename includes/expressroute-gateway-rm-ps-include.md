---
title: Fichier Include
description: Fichier Include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9eaab359d76afce61cfcc72e3fa125b08856e32
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
Les étapes de cette tâche utilisent un réseau virtuel basé sur les valeurs figurant dans la liste de référence de configuration suivante. Les noms et paramètres supplémentaires sont également présentés dans cette liste. Nous n’utilisons pas cette liste directement dans la procédure, mais nous ajoutons des variables en fonction des valeurs dans cette liste. Vous pouvez copier la liste et l’utiliser en tant que référence, en remplaçant les valeurs par les vôtres.

**Liste de référence de configuration**

* Nom du réseau virtuel : « TestVNet »
* Espace d’adressage du réseau virtuel : 192.168.0.0/16
* Groupe de ressources : « TestRG »
* Nom du sous-réseau 1 : « FrontEnd » 
* Espace d’adressage du sous-réseau 1 = "192.168.1.0/24"
* Nom de sous-réseau de passerelle : « GatewaySubnet » Vous devez toujours nommer un sous-réseau de passerelle *GatewaySubnet*.
* Espace d'adressage du sous-réseau de passerelle : « 192.168.200.0/26 »
* Région : « Est des États-Unis »
* Nom de la passerelle : « GW »
* Nom d’adresse IP de la passerelle : « GWIP »
* Nom de configuration IP de la passerelle : « gwipconf »
* Type : « ExpressRoute » Ce type est requis pour une configuration ExpressRoute.
* Nom d’adresse IP publique de passerelle = « gwpip »

## <a name="add-a-gateway"></a>Ajout d’une passerelle
1. Connectez-vous à votre abonnement Azure.

  ```powershell 
  Login-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Déclarez vos variables pour cet exercice. Veillez à modifier l’exemple pour refléter les paramètres que vous souhaitez utiliser.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Stockez l’objet de réseau virtuel en tant que variable.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Ajoutez un sous-réseau de passerelle à votre réseau virtuel. Le sous-réseau de passerelle doit être nommé « GatewaySubnet ». Vous devez créer un sous-réseau de passerelle défini sur /27 ou plus (/26, /25, etc.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Définissez la configuration.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Stockez le sous-réseau de passerelle en tant que variable.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Demandez une adresse IP publique L’adresse IP est demandée avant de créer la passerelle. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser, car elle est allouée de façon dynamique. Vous utiliserez cette adresse IP dans la section de configuration suivante. La méthode AllocationMethod doit être dynamique.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Créez la configuration de votre passerelle. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Dans cette étape, vous spécifiez la configuration qui sera utilisée lors de la création de la passerelle. Cette étape ne crée pas réellement l’objet de passerelle. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Créez la passerelle. Dans cette étape, le type **-GatewayType** est particulièrement important. Vous devez utiliser la valeur **ExpressRoute**. Après l’exécution de ces cmdlets, la création de la passerelle peut prendre 45 minutes ou plus.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Vérification de la création de la passerelle
Utilisez les commandes suivantes pour vérifier que la passerelle a été créée :

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Redimensionner une passerelle
Il existe un certain nombre de [Références (SKU) de passerelle](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence de passerelle à tout moment.

> [!IMPORTANT]
> Cette commande ne fonctionne pas pour la passerelle UltraPerformance. Pour modifier votre passerelle en passerelle UltraPerformance, commencez par supprimer la passerelle ExpressRoute, puis créez une passerelle UltraPerformance. Pour mettre à niveau votre passerelle à partir d’une passerelle UltraPerformance, commencez par supprimer la passerelle UltraPerformance, puis créez-en une.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Supprimer une passerelle
Pour supprimer une passerelle, utilisez la commande suivante :

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
