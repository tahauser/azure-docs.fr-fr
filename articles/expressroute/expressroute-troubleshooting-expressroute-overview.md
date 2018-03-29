---
title: 'Vérification de la connectivité : Guide de dépannage Azure ExpressRoute | Microsoft Docs'
description: Cette page fournit des instructions sur le dépannage et la validation de la connectivité de bout en bout d’un circuit ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 5d01f2e402e4b793274761703ec3ca1ea3ff8164
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="verifying-expressroute-connectivity"></a>Vérification de la connectivité ExpressRoute
ExpressRoute, qui étend un réseau local dans le cloud Microsoft par le biais d’une connexion privée qui est facilitée par un fournisseur de connectivité, implique les trois zones de réseau distinctes suivantes :

-   Réseau de client
-   Réseau de fournisseur
-   Centre de données Microsoft

L’objectif de ce document est d’aider l’utilisateur à identifier où (ou même si) un problème de connectivité existe et dans quelle zone, afin de demander l’aide d’une équipe appropriée pour le résoudre. Si le support technique de Microsoft est nécessaire pour résoudre un problème, ouvrez un ticket de support auprès du [support Microsoft][Support].

> [!IMPORTANT]
> Ce document a pour but de vous aider à diagnostiquer et à résoudre des problèmes simples. Il n’a pas pour objet de remplacer le support de Microsoft. Si vous ne parvenez pas à résoudre le problème en suivant les conseils donnés, ouvrez un ticket de support auprès du [support Microsoft][Support].
>
>

## <a name="overview"></a>Vue d'ensemble
Le diagramme suivant illustre la connectivité logique d’un réseau de client vers un réseau Microsoft à l’aide d’ExpressRoute.
[![1]][1]

Dans le diagramme précédent, les nombres indiquent les points clés de réseau. Les points de réseau sont souvent référencés dans cet article par le numéro qui leur est associé.

Selon le modèle de connectivité ExpressRoute (colocalisation Cloud Exchange, connexion Ethernet point à point ou universelle (IPVPN)), les points de réseau 3 et 4 peuvent être commutateurs (appareils de couche 2). Les points clés de réseau illustrés sont les suivants :

1.  Appareil de calcul du client (par exemple, un serveur ou un PC)
2.  CE : routeurs de périphérie du client 
3.  PE (collaborant avec des CE) : routeurs / commutateurs de périphérie du fournisseur qui collaborent avec des routeurs de périphérie du client. Appelé PE-CE dans ce document.
4.  PE (collaborant avec des MSEE) : routeurs / commutateurs de périphérie du fournisseur qui collaborent avec des MSEE. Appelé PE-MSEE dans ce document.
5.  MSEE : routeurs ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Passerelle de réseau virtuel (VNet)
7.  Appareil de calcul sur le réseau virtuel Azure

Si les modèles de connectivité Colocalisation Cloud Exchange ou Connexion Ethernet point à point sont utilisés, le routeur de périphérie du client (2) établit l’homologation BGP avec des MSEE (5). Les points de réseau 3 et 4 existent toujours, mais sont quelque peu transparents en tant qu'appareils de couche 2.

Si le modèle de connectivité universelle (IPVPN) est utilisé, les PE (collaborant avec des MSEE) (4) établissent une homologation BGP avec des MSEE (5). Les itinéraires sont ensuite propagés sur le réseau du client par le biais du réseau de fournisseur de service IPVPN.

>[!NOTE]
>Pour la haute disponibilité ExpressRoute, Microsoft nécessite une paire de sessions BGP redondante entre des MSEE (5) et des PE-MSEE (4). Une paire redondante de chemins d’accès au réseau est également recommandée entre le réseau du client et les PE-CE. Cependant, dans le modèle de connexion universelle (IPVPN), un seul appareil CE (2) peut-être connecté à un ou plusieurs PE (3).
>
>

Les étapes suivantes permettent de valider un circuit ExpressRoute (avec le point de réseau indiqué par le nombre associé) :
1. [Validation de l'approvisionnement et l’état du circuit (5)](#validate-circuit-provisioning-and-state)
2. [Valider qu'au moins une homologation ExpressRoute est configurée (5)](#validate-peering-configuration)
3. [Validation de l'ARP entre Microsoft et le fournisseur de service (lien entre 4 et 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Valider les BGP et les itinéraires sur les MSEE (BGP entre 4 et 5 et 5 et 6 si un réseau virtuel est connecté)](#validate-bgp-and-routes-on-the-msee)
5. [Vérifier les statistiques de trafic (trafic passant par 5)](#check-the-traffic-statistics)

Des validations et des vérifications complémentaires seront ajoutées prochainement, vérifiez tous les mois !

## <a name="validate-circuit-provisioning-and-state"></a>Validation de l'approvisionnement et l’état du circuit
Quel que soit le modèle de connectivité, un circuit ExpressRoute doit être créé et une clé de service générée pour l’approvisionnement du circuit. L’approvisionnement d’un circuit ExpressRoute établit une connexion de couche 2 redondante entre les PE-MSEE (4) et les MSEE (5). Pour plus d’informations sur la manière de créer, de modifier, d'approvisionner et de vérifier un circuit ExpressRoute, consultez l’article [Création et modification d’un circuit ExpressRoute][CreateCircuit].

>[!TIP]
>Une clé de service identifie un circuit ExpressRoute de façon unique. Cette clé est exigée pour la plupart des commandes powershell mentionnées dans ce document. Si vous avez également besoin d'assistance de la part de Microsoft ou d’un partenaire ExpressRoute pour résoudre un problème lié à ExpressRoute, entrez la clé de service afin d’identifier facilement le circuit.
>
>

### <a name="verification-via-the-azure-portal"></a>Vérification par le biais du portail Azure
Dans le portail Azure, l’état d’un circuit ExpressRoute peut être vérifié en sélectionnant ![2][2] dans la barre de menu de gauche, puis en sélectionnant le circuit ExpressRoute. Sélectionner un circuit ExpressRoute répertorié sous « Toutes les ressources » ouvre le panneau de circuit ExpressRoute. Dans la section ![3][3] du panneau, les informations essentielles d'ExpressRoute sont indiquées comme illustré dans la capture d’écran suivante :

![4][4]    

Dans ExpressRoute Essentials, *l'état du circuit* indique l’état du circuit côté Microsoft. *L'état du fournisseur* indique si le circuit a été *approvisionné/non approvisionné* côté fournisseur de service. 

Pour qu'un circuit ExpressRoute soit opérationnel, *l'état du Circuit* doit être *activé* et *l'état du fournisseur* doit être *approvisionné*.

>[!NOTE]
>Si *l'état du circuit* est désactivé, contactez le [support Microsoft][Support]. Si *l'état du fournisseur* n'est pas approvisionné, contactez votre fournisseur de services.
>
>

### <a name="verification-via-powershell"></a>Vérification par le biais de PowerShell
Pour répertorier tous les circuits ExpressRoute dans un groupe de ressources, utilisez la commande suivante :

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Vous pouvez obtenir le nom de votre groupe de ressources par le biais d’Azure. Consultez la section précédente de ce document et notez que le nom de groupe de ressources est répertorié dans l'exemple de capture d’écran.
>
>

Pour sélectionner un circuit ExpressRoute spécifique dans un groupe de ressources, utilisez la commande suivante :

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Voici un exemple de réponse :

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Pour vérifier si un circuit ExpressRoute est opérationnel, portez une attention particulière aux champs suivants :

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Si *CircuitProvisioningState* est désactivé, contactez le [support Microsoft][Support]. Si *ServiceProviderProvisioningState* n'est pas approvisionné, contactez votre fournisseur de services.
>
>

### <a name="verification-via-powershell-classic"></a>Vérification par le biais de PowerShell (classique)
Pour répertorier tous les circuits ExpressRoute dans un abonnement, utilisez la commande suivante :

    Get-AzureDedicatedCircuit

Pour sélectionner un circuit ExpressRoute spécifique, utilisez la commande suivante :

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Voici un exemple de réponse :

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Pour vérifier si un circuit ExpressRoute est opérationnel, portez une attention particulière aux champs suivants : ServiceProviderProvisioningState : Provisioned Status                           : Enabled

>[!NOTE]
>Si *l'état* est désactivé, contactez le [support Microsoft][Support]. Si *ServiceProviderProvisioningState* n'est pas approvisionné, contactez votre fournisseur de services.
>
>

## <a name="validate-peering-configuration"></a>Validation de la configuration de l’homologation
Une fois que le fournisseur de services a terminé l'approvisionnement du circuit ExpressRoute, une configuration de routage peut être créée à travers le circuit ExpressRoute entre des MSEE-PR (4) et des MSEE (5). Chaque circuit ExpressRoute peut avoir un, deux ou trois contextes de routage activés : l'homologation privée Azure (trafic vers des réseaux virtuels privés dans Azure), l'homologation publique Azure (le trafic vers des adresses IP publiques dans Azure) et l'homologation Microsoft (Office 365 et Dynamics 365). Pour plus d’informations sur la création et la modification de la configuration de routage, consultez l’article [Créer et modifier le routage le routage pour un circuit ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Vérification par le biais du portail Azure

>[!NOTE]
>Si la couche 3 est fournie par le fournisseur de services et que les homologations sont vides dans le portail, actualisez la configuration du circuit à l’aide du bouton Actualiser du portail. Cette opération appliquera la configuration de routage appropriée à votre circuit. 
>
>

Dans le portail Azure, l’état d’un circuit ExpressRoute peut être vérifié en sélectionnant ![2][2] dans la barre de menu de gauche, puis en sélectionnant le circuit ExpressRoute. Sélectionner un circuit ExpressRoute répertorié sous « Toutes les ressources » permet d'ouvrir le panneau de circuit ExpressRoute. Dans la section ![3][3] du panneau, les informations essentielles d'ExpressRoute sont indiquées comme illustré dans la capture d’écran suivante :

![5][5]

Dans l’exemple précédent, nous avons noté que le contexte de routage Homologation privée Azure est activé, tandis que les contextes de routage Homologation publique Azure et Microsoft ne sont pas activés. Un contexte d’homologation correctement activé doit également répertorier les sous-réseaux de point à point principaux et secondaires (nécessaires pour BGP). Les sous-réseaux /30 sont utilisés pour l’adresse IP d’interface des MSEE et PE-MSEE. 

>[!NOTE]
>Si aucune homologation n’est activée, vérifiez si les sous-réseaux principaux et secondaires attribués correspondent à la configuration sur les PE-MSEE. Dans le cas contraire, pour modifier la configuration sur les routeurs MSEE, reportez-vous à la rubrique [Créer et modifier le routage le routage pour un circuit ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Vérification par le biais de PowerShell
Pour obtenir les détails sur la configuration de l'homologation privée Azure, utilisez les commandes suivantes :

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Voici un exemple de réponse pour une homologation privée correctement configurée :

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Un contexte d’homologation correctement activé répertorie les préfixes d’adresses principales et secondaires. Les sous-réseaux /30 sont utilisés pour l’adresse IP d’interface des MSEE et PE-MSEE.

Pour obtenir les détails sur la configuration de l'homologation publique Azure, utilisez les commandes suivantes :

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Pour obtenir les détails sur la configuration de l'homologation Microsoft, utilisez les commandes suivantes :

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Si aucune homologation n’est configurée, un message d’erreur est généré. Voici un exemple de réponse si les homologations mentionnées (l'homologation publique Azure dans cet exemple) ne sont pas configurées dans le circuit :

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Si aucune homologation n’est activée, vérifiez si les sous-réseaux principaux et secondaires attribués correspondent à la configuration sur le PE-MSEE lié. Vérifiez également si les *VlanId*, *AzureASN* et *PeerASN* sont utilisés sur des MSEE et si ces valeurs correspondent à celles utilisées sur le PE-MSEE lié. Si le hachage MD5 est choisi, la clé partagée doit être la même sur la paire MSEE et PE-MSEE. Pour modifier la configuration sur les routeurs MSEE, reportez-vous à la rubrique [Créer et modifier le routage le routage pour un circuit ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Vérification par le biais de PowerShell (classique)
Pour obtenir les détails sur la configuration de l'homologation privée Azure, utilisez la commande suivante :

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Voici un exemple de réponse pour une homologation privée correctement configurée :

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Un contexte d’homologation correctement activé répertorie les sous-réseaux d'homologation principaux et secondaires. Les sous-réseaux /30 sont utilisés pour l’adresse IP d’interface des MSEE et PE-MSEE.

Pour obtenir les détails sur la configuration de l'homologation publique Azure, utilisez les commandes suivantes :

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Pour obtenir les détails sur la configuration de l'homologation Microsoft, utilisez les commandes suivantes :

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Si les homologations de couche 3 ont été définies par le fournisseur de services, la définition d'homologations ExpressRoute par le biais du portail ou de PowerShell remplace les paramètres du fournisseur de service. La réinitialisation des paramètres d’homologation côté fournisseur nécessite le support du fournisseur de services. Modifiez uniquement les homologations ExpressRoute si vous êtes certain que le fournisseur de services fournit uniquement des services de couche 2 !
>
>

<p/>
>[!NOTE]
>Si une homologation n’est pas activée, vérifiez si les sous-réseaux d’homologation principaux et secondaires attribués correspondent à la configuration sur le PE-MSEE lié. Vérifiez également si les *VlanId*, *AzureAsn* et *PeerAsn* sont utilisés sur des MSEE et si ces valeurs correspondent à celles utilisées sur le PE-MSEE lié. Pour modifier la configuration sur les routeurs MSEE, reportez-vous à la rubrique [Créer et modifier le routage le routage pour un circuit ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validation de l'ARP entre Microsoft et le fournisseur de service
Cette section utilise les commandes PowerShell (classiques). Si vous avez utilisé des commandes PowerShell Azure Resource Manager, vérifiez que vous avez un accès administrateur/coadministrateur à l’abonnement. Pour le dépannage à l’aide des commandes Azure Resource Manager, veuillez vous reporter au document [Obtention de tables ARP dans le modèle de déploiement Resource Manager][ARP].

>[!NOTE]
>Vous pouvez utiliser le portail Azure et les commandes Azure Resource Manager PowerShell pour obtenir l’ARP. Si vous rencontrez des erreurs avec les commandes PowerShell Azure Resource Manager, les commandes PowerShell classiques peuvent fonctionner comme commandes PowerShell classiques et avec des circuits ExpressRoute Azure Resource Manager.
>
>

Pour obtenir la table ARP depuis le routeur MSEE principal pour l’homologation privée, utilisez la commande suivante :

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Voici un exemple de réponse pour la commande si le scénario réussit :

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

De même, vous pouvez vérifier la table ARP à partir des MSEE dans le chemin d'accès *principal*/*secondaire* pour les homologations *privée*/*publique*/*Microsoft*.

L’exemple suivant montre la réponse de la commande pour une homologation inexistante.

    ARP Info:
       
>[!NOTE]
>Si la table ARP ne comprend pas les adresses IP des interfaces mappées sur des adresses MAC, passez en revue les informations suivantes :
>1. Si la première adresse IP du sous-réseau /30 affecté pour la liaison entre les MSEE-PR et MSEE est utilisée dans l’interface de MSEE-PR. Azure utilise toujours la deuxième adresse IP pour les MSEE.
>2. Vérifiez si les balises VLAN du client (C-Tag) et du service (S-Tag) correspondent à la paire MSEE-PR et MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validation de BGP et des itinéraires sur les MSEE
Cette section utilise les commandes PowerShell (classiques). Si vous avez utilisé des commandes PowerShell Azure Resource Manager, vérifiez que vous avez un accès administrateur/coadministrateur à l’abonnement.

>[!NOTE]
>Vous pouvez utiliser le portail Azure et les commandes Azure Resource Manager PowerShell pour obtenir des informations BGP. Si vous rencontrez des erreurs avec les commandes PowerShell Azure Resource Manager, les commandes PowerShell classiques peuvent fonctionner comme commandes PowerShell classiques et avec des circuits ExpressRoute Azure Resource Manager.
>
>

Pour obtenir le résumé de la table de routage (voisin BGP) d’un contexte de routage spécifique, utilisez la commande suivante :

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Voici un exemple de réponse :

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Comme indiqué dans l’exemple précédent, la commande est utile pour déterminer pour la durée de mise en place du contexte de routage. Il indique également le nombre de préfixes d’itinéraire annoncés par le routeur d'homologation.

>[!NOTE]
>Si l’état est Activé ou Inactif, vérifiez si les sous-réseaux d’homologation principaux et secondaires attribués correspondent à la configuration sur le PE-MSEE lié. Vérifiez également si les *VlanId*, *AzureAsn* et *PeerAsn* sont utilisés sur des MSEE et si ces valeurs correspondent à celles utilisées sur le PE-MSEE lié. Si le hachage MD5 est choisi, la clé partagée doit être la même sur la paire MSEE et PE-MSEE. Pour modifier la configuration sur les routeurs MSEE, reportez-vous à la rubrique [Créer et modifier le routage le routage pour un circuit ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Si certaines destinations ne sont pas accessibles par le biais d’une homologation particulière, vérifiez la table d’itinéraires des MSEE appartenant au contexte d’homologation spécifique. Si aucun préfixe correspondant (éventuellement NATed IP) n'est présent dans la table de routage, vérifiez s’il existe des pare-feu/NSG/ACL sur le chemin d’accès et si elles autorisent le trafic.
>
>

Pour obtenir la table de routage complète à partir du MSEE sur le chemin d’accès *principal* pour le contexte de routage *privé* spécifique, utilisez la commande suivante :

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Voici un exemple de sortie réussie de la commande :

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

De même, vous pouvez vérifier la table de routage à partir des MSEE dans le chemin d'accès *principal*/*secondaire* pour le contexte d'homologation *privé*/*public*/*Microsoft*.

L’exemple suivant montre la réponse de la commande pour une homologation inexistante :

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Vérification des statistiques de trafic
Pour obtenir les statistiques combinées du trafic de chemin d’accès primaire et secondaire (octets en entrée et en sortie) d’un contexte d’homologation, utilisez la commande suivante :

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Voici un exemple de sortie de la commande :

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Voici un exemple de sortie de la commande pour une homologation inexistante :

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations ou d'aide, consultez les liens suivants :

- [Support Microsoft][Support]
- [Création et modification d’un circuit ExpressRoute][CreateCircuit]
- [Créer et modifier le routage le routage pour un circuit ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Connectivité logique ExpressRoute"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Icône Toutes les ressources"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Icône Vue d’ensemble"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Capture d’écran : exemple pour ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Capture d’écran : exemple pour ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






