---
title: "Configuration de l’acheminement (homologation) d’un circuit ExpressRoute - Resource Manager, PowerShell et Azure | Microsoft Docs"
description: "Cet article vous guide tout au long des étapes de création et d’approvisionnement de l’homologation privée, publique et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer des homologations pour votre circuit."
documentationcenter: na
services: expressroute
author: osamazia
manager: jonor
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/3/2018
ms.author: osamaz, jaredr80
ms.openlocfilehash: 26165748c88629bacdaed6f12a540302ea12513b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Créer et modifier l’homologation d’un circuit ExpressRoute à l’aide de PowerShell

Cet article explique comment créer et gérer la configuration du routage d’un circuit ExpressRoute dans le modèle de déploiement Resource Manager à l’aide de PowerShell. Vous pouvez également mettre à jour, supprimer et déprovisionner des homologations d’un circuit ExpressRoute, ainsi qu’en vérifier l’état. Si vous souhaitez utiliser une autre méthode pour votre circuit, sélectionnez un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [interface de ligne de commande Azure](howto-routing-cli.md)
> * [Vidéo - Homologation privée](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview). 
* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Pour que vous puissiez exécuter les applets de commande décrites dans cet article, le circuit ExpressRoute doit être dans un état approvisionné et activé.

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

> [!IMPORTANT]
> Nous n'annonçons pas d’homologations configurées par les fournisseurs de services via le portail de gestion des services. Nous travaillons sur la prochaine activation de cette fonctionnalité. Contactez votre fournisseur de services avant de configurer des homologations BGP.
> 
> 

Vous pouvez configurer une, deux ou les trois homologations (privée Azure, publique Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation. Pour plus d’informations sur les domaines de routage et les homologations, consultez [Domaines de routage ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Homologation Microsoft

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation Microsoft pour un circuit ExpressRoute.

> [!IMPORTANT]
> L’homologation Microsoft des circuits ExpressRoute qui ont été configurés avant le 1er août 2017 entraînera la publication de tous les préfixes de service via l’homologation Microsoft, même si aucun filtre d’itinéraire n’est défini. L’homologation Microsoft des circuits ExpressRoute qui sont configurés le 1er août 2017 ou après n’entraînera la publication d’aucun préfixe tant qu’un filtre de routage n’aura pas été attaché au circuit. Pour plus d’informations, consultez [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurer un filtre d’itinéraire pour l’homologation Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Pour créer une homologation Microsoft

[!INCLUDE [Premium](../../includes/expressroute-mspeering-premium-include.md)]

1. Importez le module PowerShell pour ExpressRoute.

  Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Importez tous les modules AzureRM.* dans la plage de version sémantique connue.

  ```powershell
  Import-AzureRM
  ```

  Vous pouvez également importer un seul module sélectionné dans la plage de version sémantique connue.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.

  Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation Microsoft pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit. 

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  La réponse ressemble à ce qui suit :

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
  ProvisioningState                : Succeeded
  Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
  ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Configurez l’homologation Microsoft pour le circuit. Assurez-vous de disposer des informations suivantes avant de poursuivre.

  * Un sous-réseau /30 ou /126 pour le lien principal. Il doit s’agir d’un préfixe IPv4 ou IPv6 public valide vous appartenant et enregistré dans un registre RIR / IRR.
  * Un sous-réseau /30 ou /126 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 ou IPv6 public valide vous appartenant et enregistré dans un registre RIR / IRR.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
  * Préfixes publiés : vous devez fournir une liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Si vous prévoyez d’envoyer un jeu de préfixes, vous pouvez envoyer une liste séparée par des virgules. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR. Les sessions IPv4 BGP nécessitent des préfixes publiés IPv4 et les sessions IPv6 BGP nécessitent des préfixes publiés IPv6. 
  * Nom du registre de routage : vous pouvez spécifier les registres RIR/IRR par rapport auxquels le numéro AS et les préfixes sont enregistrés.
  * Facultatif :
    * ASN client : si vous publiez des préfixes non enregistrés dans le numéro AS d’homologation, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés.
    * Un hachage MD5 si vous choisissez d’en utiliser un.

  Utilisez l’exemple suivant afin de configurer l’homologation Microsoft pour votre circuit :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>Pour obtenir des détails sur l’homologation Microsoft

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Pour mettre à jour la configuration d’homologation Microsoft

Vous pouvez mettre à jour toute partie de la configuration à l’aide de l’exemple suivant :

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Pour supprimer une homologation Microsoft

Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante :

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Homologation privée Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation privée Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privée Azure

1. Importez le module PowerShell pour ExpressRoute.

  Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importez tous les modules AzureRM.* dans la plage de version sémantique connue.

  ```powershell
  Import-AzureRM
  ```

  Vous pouvez également importer un seul module sélectionné dans la plage de version sémantique connue.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.

  Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  La réponse ressemble à ce qui suit :

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
  ProvisioningState                : Succeeded
  Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
  ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Configurez l'homologation privée Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

  * Un sous-réseau /30 pour le lien principal. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels.
  * Un sous-réseau /30 pour le lien secondaire. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation. Veillez à ne pas utiliser le numéro 65515.
  * Facultatif :
    * Un hachage MD5 si vous choisissez d’en utiliser un.

  Utilisez l’exemple suivant pour configurer l’homologation privée Azure pour votre circuit :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Si vous choisissez d’utiliser un hachage MD5, utilisez l’exemple suivant :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
  > 
  >

### <a name="getprivate"></a>Pour obtenir les détails d’une homologation privée Azure

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Pour mettre à jour la configuration d’homologation privée Azure

Vous pouvez mettre à jour toute partie de la configuration à l’aide de l’exemple suivant : Dans cet exemple, l’ID VLAN du circuit est mis à jour de 100 à 500.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Pour supprimer une homologation privée Azure

Vous pouvez supprimer votre configuration d’homologation en exécutant l’exemple suivant :

> [!WARNING]
> Vous devez vous assurer que tous les réseaux virtuels sont dissociés du circuit ExpressRoute avant d’exécuter cet exemple. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Homologation publique Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation publique Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation publique Azure

1. Importez le module PowerShell pour ExpressRoute.

  Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Importez tous les modules AzureRM.* dans la plage de version sémantique connue.

  ```powershell
  Import-AzureRM
  ```

  Vous pouvez également importer un seul module sélectionné dans la plage de version sémantique connue.

  ```powershell
  Import-Module AzureRM.Network
```

  Connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.

  Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation publique Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  La réponse ressemble à ce qui suit :

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
  ProvisioningState                : Succeeded
  Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
  ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Configurez l’homologation publique Azure pour le circuit. Assurez-vous que vous disposez des informations suivantes avant de continuer.

  * Un sous-réseau /30 pour le lien principal. Ce doit être un préfixe IPv4 public valide.
  * Un sous-réseau /30 pour le lien secondaire. Ce doit être un préfixe IPv4 public valide.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
  * Facultatif :
    * Un hachage MD5 si vous choisissez d’en utiliser un.

  Exécutez l’exemple suivant pour configurer l’homologation publique Azure pour votre circuit :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Si vous choisissez d’utiliser un hachage MD5, utilisez l’exemple suivant :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
  > 
  >

### <a name="getpublic"></a>Pour obtenir les détails d’une homologation publique Azure

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante :

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Pour mettre à jour la configuration d’homologation publique Azure

Vous pouvez mettre à jour toute partie de la configuration à l’aide de l’exemple suivant : Dans cet exemple, l’ID VLAN du circuit est mis à jour de 200 à 600.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Pour supprimer une homologation publique Azure

Vous pouvez supprimer votre configuration d’homologation en exécutant l’exemple suivant :

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).
