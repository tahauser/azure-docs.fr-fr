---
title: "Configuration de l’acheminement (homologation) d’un circuit ExpressRoute - Azure Classic | Microsoft Docs"
description: "Cet article vous guide tout au long des étapes de création et d’approvisionnement de l’homologation privée, publique et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer des homologations pour votre circuit."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: d6c675d314e96cd0caab7e5eb5c60e3b1458e4a0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Créer et modifier l’homologation pour un circuit ExpressRoute (Classic)
> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [interface de ligne de commande Azure](howto-routing-cli.md)
> * [Vidéo - Homologation privée](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

Cet article vous guide tout au long des étapes de création et de gestion de la configuration du routage d'un circuit ExpressRoute à l'aide de PowerShell et du modèle de déploiement classique. Les étapes ci-dessous vous montreront également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement des homologations d'un circuit ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Prérequis de configuration
* Vous devez installer la dernière version des applets de commande PowerShell Azure Service Management (SM). Pour en savoir plus, voir [Prise en main des applets de commande PowerShell](/powershell/azure/overview).  
* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être dans un état approvisionné et activé pour être en mesure d'exécuter les applets de commande décrites ci-dessous.

> [!IMPORTANT]
> Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement IPVPN, à l’image de MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.
> 
> 

Vous pouvez configurer une, deux ou les trois homologations (privée Azure, publique Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Connectez-vous à votre compte Azure et sélectionnez un abonnement
1. Ouvrez la console PowerShell avec des droits élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

        Login-AzureRmAccount

2. Vérifiez les abonnements associés au compte.

        Get-AzureRmSubscription

3. Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Utilisez l’applet de commande suivante pour ajouter votre abonnement Azure à PowerShell pour le modèle de déploiement classique.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Homologation privée Azure
Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d'homologation privée Azure pour un circuit ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privée Azure
1. **Importez le module PowerShell pour ExpressRoute.**
   
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell. La version peut varier.    
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Créez un circuit ExpressRoute.**
   
    Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l'homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous. 
3. **Vérifiez que le circuit ExpressRoute est approvisionné.**
   
    Vous devez tout d'abord vérifier que le circuit ExpressRoute est approvisionné et activé. Reportez-vous à l’exemple ci-dessous.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Assurez-vous que le circuit affiche l’état Provisioned (approvisionné) et Enabled (activé). Si ce n'est pas le cas, contactez votre fournisseur de connectivité afin que votre circuit affiche l’état requis.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configurez l'homologation privée Azure pour le circuit.**
   
    Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :
   
   * Un sous-réseau /30 pour le lien principal. Ce sous-réseau ne doit faire partie d’aucun espace d'adressage réservé aux réseaux virtuels.
   * Un sous-réseau /30 pour le lien secondaire. Ce sous-réseau ne doit faire partie d’aucun espace d'adressage réservé aux réseaux virtuels.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation. Veillez à ne pas utiliser le numéro 65515.
   * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative**.
     
    Vous pouvez exécuter l’applet de commande suivante pour configurer l’homologation privée Azure pour votre circuit.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
     
    Vous pouvez utiliser l'applet de commande ci-dessous si vous choisissez d'utiliser un hachage MD5.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Pour afficher les détails d’une homologation privée Azure
Vous pouvez obtenir les détails de la configuration à l'aide de l'applet de commande suivante

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Pour mettre à jour la configuration d'homologation privée Azure
Vous pouvez mettre à jour toute partie de la configuration à l'aide de l’applet de commande suivante. Dans l'exemple ci-dessous, l'ID VLAN du circuit est mis à jour de 100 à 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Pour supprimer une homologation privée Azure
Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante.

> [!WARNING]
> Vous devez vous assurer que tous les réseaux virtuels sont dissociés du circuit ExpressRoute avant d'exécuter cette applet de commande. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Homologation publique Azure
Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d'homologation publique Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation publique Azure
1. **Importez le module PowerShell pour ExpressRoute.**
   
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell. La version peut varier.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Création d’un circuit ExpressRoute**
   
    Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l'homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous.
3. **Vérification de l’approvisionnement du circuit ExpressRoute**
   
    Vous devez tout d'abord vérifier que le circuit ExpressRoute est approvisionné et activé. Reportez-vous à l’exemple ci-dessous.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Assurez-vous que le circuit affiche l’état Provisioned (approvisionné) et Enabled (activé). Si ce n'est pas le cas, contactez votre fournisseur de connectivité afin que votre circuit affiche l’état requis.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configuration de l'homologation publique Azure pour le circuit**
   
    Assurez-vous de disposer des informations suivantes avant de continuer.
   
   * Un sous-réseau /30 pour le lien principal. Ce doit être un préfixe IPv4 public valide.
   * Un sous-réseau /30 pour le lien secondaire. Ce doit être un préfixe IPv4 public valide.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative**.
     
    Vous pouvez exécuter l’applet de commande suivante pour configurer l’homologation publique Azure pour votre circuit
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Vous pouvez utiliser l'applet de commande ci-dessous si vous choisissez d'utiliser un hachage MD5
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Pour afficher les détails d’une homologation publique Azure
Vous pouvez obtenir les détails de la configuration à l'aide de l'applet de commande suivante

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Pour mettre à jour la configuration d'homologation publique Azure
Vous pouvez mettre à jour toute partie de la configuration à l'aide de l’applet de commande suivante

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

L'ID VLAN du circuit est mis à jour de 200 à 600 dans l’exemple ci-dessus.

### <a name="to-delete-azure-public-peering"></a>Pour supprimer une homologation publique Azure
Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Homologation Microsoft
Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d'homologation Microsoft pour un circuit ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Pour créer une homologation Microsoft
1. **Importez le module PowerShell pour ExpressRoute.**
   
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell. La version peut varier.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Création d’un circuit ExpressRoute**
   
    Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l'homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous.
3. **Vérification de l’approvisionnement du circuit ExpressRoute**
   
    Vous devez tout d'abord vérifier que le circuit ExpressRoute est approvisionné et activé.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Assurez-vous que le circuit affiche l’état Provisioned (approvisionné) et Enabled (activé). Si ce n'est pas le cas, contactez votre fournisseur de connectivité afin que votre circuit affiche l’état requis.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configuration de l'homologation Microsoft pour le circuit**
   
    Assurez-vous de disposer des informations suivantes avant de poursuivre.
   
   * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
   * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * Préfixes publiés : vous devez fournir une liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Vous pouvez envoyer une liste séparée par des virgules si vous prévoyez d'envoyer un jeu de préfixes. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
   * ASN client : si vous publiez des préfixes non enregistrés dans le numéro AS d’homologation, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés. **Cette étape est facultative**.
   * Nom du registre de routage : vous pouvez spécifier les registres RIR/IRR par rapport auxquels le numéro AS et les préfixes sont enregistrés.
   * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative.**
     
    Vous pouvez exécuter l'applet de commande suivante afin de configurer l’homologation Microsoft pour votre circuit
     
        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Pour afficher les détails de l’homologation Microsoft
Vous pouvez obtenir les détails de la configuration à l'aide de l'applet de commande suivante.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Pour mettre à jour la configuration d’homologation Microsoft
Vous pouvez mettre à jour toute partie de la configuration à l'aide de l’applet de commande suivante.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Pour supprimer une homologation Microsoft
Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Étapes suivantes
Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Pour plus d'informations sur les workflows, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).

