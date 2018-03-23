---
title: Application de connexion Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article explique comment connecter votre application à Azure SQL Database Managed Instance.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f02311026e3f28d4cf41dfe9b155f928885ae938
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Connecter votre application à Azure SQL Database Managed Instance

Aujourd’hui, plusieurs choix s’offrent à vous pour déterminer comment et où héberger votre application. 
 
Vous pouvez choisir le cloud en utilisant soit Azure App Service, soit certaines options intégrées de réseau virtuel Azure comme l’environnement Azure App Service, une machine virtuelle ou un groupe de machines virtuelles identiques. Vous pouvez également adopter une approche du cloud hybride pour conserver vos applications localement. 
 
Quel que soit le choix effectué, vous pouvez le connecter à Managed Instance (préversion).  

![haute disponibilité](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Connecter une application à l’intérieur du même réseau virtuel 

Ce scénario est le plus simple. Des machines virtuelles à l’intérieur du réseau virtuel peuvent se connecter entre elles directement même si elles sont dans des sous-réseaux différents. Cela signifie que pour connecter une application dans un environnement Azure Application ou une machine virtuelle, il vous suffit de définir correctement la chaîne de connexion.  
 
Si vous ne parvenez pas à établir la connexion, vérifiez si un groupe de sécurité réseau est défini sur le sous-réseau de l’application. Le cas échéant, vous devez ouvrir une connexion sortante sur le port SQL 1 433 ainsi que la plage des ports 11 000 à 12 000 à des fins de redirection. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Connecter une application à l’intérieur d’un autre réseau virtuel 

Ce scénario est un peu plus complexe, car Managed Instance a une adresse IP privée dans son propre réseau virtuel. Pour établir la connexion, une application a besoin d’accéder au réseau virtuel dans lequel Managed Instance est déployé. Ainsi, vous devez d’abord établir une connexion entre l’application et le réseau virtuel Managed Instance. Les réseaux virtuels ne doivent pas nécessairement être dans le même abonnement pour que ce scénario fonctionne. 
 
Il existe deux options pour connecter des réseaux virtuels : 
- [Homologation de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md) 
- Passerelle VPN de réseau virtuel à réseau virtuel ([portail Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
L’option d’homologation est préférable car elle utilise le réseau principal de Microsoft, donc du point de vue de la connectivité, il n’y a pas de différence notable de latence entre les machines virtuelles dans le réseau virtuel homologué et dans le même réseau virtuel. L’homologation de réseaux virtuels se limite aux réseaux de la même région, bien que l’homologation entre régions soit possible en préversion dans certaines régions.  
 
> [!IMPORTANT]
> Les homologations de réseaux virtuels créées entre les régions peuvent ne pas avoir le même niveau de disponibilité et de fiabilité que les homologations d’une version publique. Les homologations de réseaux virtuels peuvent avoir des fonctionnalités limitées et ne pas être disponibles dans toutes les régions Azure. Pour connaître les notifications les plus récentes sur la disponibilité et l’état de cette fonctionnalité, consultez la page relative aux mises à jour du  [réseau virtuel Azure](https://azure.microsoft.com/updates/?product=virtual-network). 

## <a name="connect-an-on-premises-application"></a>Connecter une application locale 

Managed Instance est uniquement accessible par le biais d’une adresse IP privée. Afin d’y accéder localement, vous devez établir une connexion de site à site entre l’application et le réseau virtuel Managed Instance. 
 
Vous avez deux options pour la connexion locale à un réseau virtuel Azure : 
- Connexion VPN de site à site ([portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- Connexion [ExpressRoute](../expressroute/expressroute-introduction.md)  
 
Si vous avez établi une connexion locale à Azure et que vous ne parvenez pas à établir une connexion à Managed Instance, vérifiez si votre pare-feu dispose d’une connexion sortante ouverte sur le port SQL 1 433 et la plage de ports 11 000 à 12 000 à des fins de redirection. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Connecter une application hébergée Azure App Service 

Managed Instance est uniquement accessible par le biais d’une adresse IP privée, donc pour y accéder à partir d’Azure App Service, vous devez d’abord établir une connexion entre l’application et le réseau virtuel Managed Instance. Consultez [Intégrer une application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Pour résoudre les problèmes, consultez [Résolution des problèmes liés aux réseaux virtuels et aux applications](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Si aucune connexion ne peut être établie, essayez de [synchroniser la configuration de la mise en réseau](sql-database-managed-instance-sync-network-configuration.md). 
 
L’intégration d’Azure App Service à un réseau homologué avec un réseau virtuel Managed Instance constitue un cas spécial de connexion entre Azure App Service et Managed Instance. Ce cas nécessite la configuration suivante : 

- Le réseau virtuel Managed Instance ne doit PAS avoir de passerelle.  
- Le réseau virtuel Managed Instance doit utiliser des passerelles distantes. 
- Le réseau virtuel homologué doit autoriser le transit par passerelle. 
 
Ce scénario est illustré dans le diagramme suivant :

![homologation d’applications intégrées](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Connecter une application dans la box de développeur 

Managed Instance est uniquement accessible par le biais d’une adresse IP privée, donc pour y accéder à partir de votre box de développeur, vous devez d’abord établir une connexion entre cette dernière et le réseau virtuel Managed Instance.  
 
Configurez une connexion point à site à un réseau virtuel à l’aide des articles sur l’authentification par certificat Azure native ([portail Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)).  

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’option Managed Instance, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md).
- Pour un tutoriel, consultez [Créer une option Managed Instance](sql-database-managed-instance-tutorial-portal.md).
