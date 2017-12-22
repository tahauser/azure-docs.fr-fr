---
title: "Obtention de tables ARP - Resource Manager : guide de résolution des problèmes Azure ExpressRoute | Microsoft Docs"
description: "Cette page fournit des instructions sur l’obtention des tables ARP pour un circuit ExpressRoute"
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obtention de tables ARP dans le modèle de déploiement Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Classique](expressroute-troubleshooting-arp-classic.md)
> 
> 

Cet article vous guide tout au long des étapes d’apprentissage des tables ARP pour votre circuit ExpressRoute. 

> [!IMPORTANT]
> Ce document a pour objet de vous aider à diagnostiquer et résoudre les problèmes simples. Il n’a pas pour objet de remplacer le support de Microsoft. Si vous ne parvenez pas à résoudre le problème en suivant les conseils ci-dessous, ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protocole ARP (Address Resolution Protocol) et tables ARP
Le protocole ARP (Address Resolution Protocol) est un protocole de couche 2 défini dans [RFC 826](https://tools.ietf.org/html/rfc826). ARP est utilisé pour mapper l’adresse Ethernet (adresse MAC) avec une adresse IP.

La table ARP fournit un mappage de l’adresse ipv4 et de l’adresse MAC pour une homologation particulière. La table ARP d’une homologation de circuit ExpressRoute fournit les informations suivantes pour chaque interface (principale et secondaire)

1. Mappage de l’adresse IP de l’interface du routeur local sur l’adresse MAC
2. Mappage de l’adresse IP de l’interface du routeur ExpressRoute sur l’adresse MAC
3. Âge du mappage

Les tables ARP permettent de valider la configuration de la couche 2 et de résoudre les problèmes de connectivité de base de la couche 2. 

Exemple de table ARP : 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


La section suivante fournit des informations sur l’affichage des tables ARP vues par les routeurs de bordure ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Conditions préalables à l’apprentissage des tables ARP
Assurez-vous que vous disposez des éléments suivants avant de poursuivre

* Un circuit ExpressRoute valide configuré avec au moins une homologation. Le circuit doit être entièrement configuré par le fournisseur de connectivité. Vous (ou votre fournisseur de connectivité) devez avoir configuré au moins une des homologations (Azure privé, Azure public et Microsoft) sur ce circuit.
* Plages d’adresses IP utilisées pour configurer les homologations (Azure privé, Azure public et Microsoft). Passez en revue les exemples d’affectation d’adresses IP sur la [page de configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour comprendre de quelle manière les adresses IP sont mappées aux interfaces de votre côté et du côté d’ExpressRoute. Vous pouvez obtenir plus d’informations sur la configuration d’homologation en examinant la [page de configuration d’homologation ExpressRoute](expressroute-howto-routing-arm.md).
* Informations de votre équipe réseau/fournisseur de connectivité sur les adresses MAC des interfaces utilisées avec ces adresses IP.
* Vous devez disposer du dernier module PowerShell pour Azure (version 1.50 ou plus récente).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obtention des tables ARP pour votre circuit ExpressRoute
Cette section fournit des instructions sur la manière d’afficher les tables ARP par homologation à l’aide de PowerShell. Vous ou votre fournisseur de connectivité devez avoir configuré l’homologation avant de poursuivre. Chaque circuit a deux chemins d’accès (principal et secondaire). Vous pouvez contrôler indépendamment la table ARP de chaque chemin d’accès.

### <a name="arp-tables-for-azure-private-peering"></a>Tables ARP pour l’homologation privée Azure
L’applet de commande suivante fournit les tables ARP pour l’homologation privée Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tables ARP pour l’homologation publique Azure
L’applet de commande suivante fournit les tables ARP pour l’homologation publique Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tables ARP pour l’homologation Microsoft
L’applet de commande suivante fournit les tables ARP de l’homologation Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Un exemple de sortie est affiché ci-dessous pour l’un des chemins d’accès

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Utilisation de ces informations
La table ARP d’une homologation peut servir à valider la connectivité et la configuration de la couche 2. Cette section fournit une vue d’ensemble de l’aspect des tables ARP dans différents scénarios.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Table ARP lorsqu’un circuit est dans un état opérationnel (état attendu)
* La table ARP aura une entrée pour le côté local avec une adresse IP valide et une adresse MAC, ainsi qu’une entrée similaire pour le côté Microsoft. 
* Le dernier octet de l’adresse IP locale sera toujours un nombre impair.
* Le dernier octet de l’adresse IP Microsoft sera toujours un nombre pair.
* La même adresse MAC s’affichera côté Microsoft pour les trois homologations (principales/secondaires). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Table ARP en cas de problèmes côté fournisseur de connectivité/local
Si vous rencontrez des problèmes liés au site ou au fournisseur de connectivité, vous pouvez constater qu’une seule entrée apparaîtra dans la table ARP ou l’adresse MAC locale sera incomplète. Cette commande affiche le mappage entre l’adresse MAC et l’adresse IP utilisée côté Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

ou
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Ouvrez une demande de support avec votre fournisseur de connectivité pour déboguer ces problèmes. Si la table ARP ne comprend pas les adresses IP des interfaces mappées sur des adresses MAC, passez en revue les informations suivantes :
> 
> 1. Si la première adresse IP du sous-réseau /30 affecté pour la liaison entre les MSEE-PR et MSEE est utilisée dans l’interface de MSEE-PR. Azure utilise toujours la deuxième adresse IP pour les MSEE.
> 2. Vérifiez si les balises VLAN du client (C-Tag) et du service (S-Tag) correspondent à la paire MSEE-PR et MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Table ARP en cas de problèmes côté Microsoft
* Aucune table ARP ne s’affiche pour une homologation en cas de problèmes côté Microsoft. 
* Ouvrez un incident auprès du [support technique Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Spécifiez que vous avez un problème au niveau de la connectivité de couche 2. 

## <a name="next-steps"></a>Étapes suivantes
* Valider les configurations de couche 3 pour votre circuit ExpressRoute
  * Obtenir un récapitulatif d’itinéraires pour déterminer l’état des sessions BGP 
  * Obtenir une table d’itinéraires pour déterminer quels préfixes sont publiés sur ExpressRoute
* Valider le transfert des données en examinant les octets en entrée/sortie
* Ouvrez un ticket de support auprès du [support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez encore des problèmes.

