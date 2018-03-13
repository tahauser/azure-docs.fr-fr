---
title: "Vue d’ensemble des conditions préalables pour l’utilisation d’Azure Database Migration Service | Microsoft Docs"
description: "Découvrez une vue d’ensemble des conditions préalables pour l’utilisation d’Azure Database Migration Service pour effectuer des migrations de bases de données."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Vue d’ensemble des conditions préalables pour l’utilisation d’Azure Database Migration Service
Il existe plusieurs conditions préalables requises pour vous garantir qu’Azure Database Migration Service s’exécute correctement lors de la migration de bases de données. Certaines des conditions préalables s’appliquent à tous les scénarios (paires source-cible) pris en charge par le service, tandis que d’autres sont propres à un scénario spécifique.

Les conditions préalables relatives à l’utilisation d’Azure Database Migration Service sont répertoriées dans les sections suivantes.

## <a name="prerequisites-common-across-migration-scenarios"></a>Conditions préalables communes dans l’ensemble des scénarios de migration
Les conditions préalables associées à Azure Database Migration Service communes à tous les scénarios de migration pris en charge incluent le besoin de :
- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Conditions préalables pour la migration de SQL Server vers Azure SQL Database 
En plus des conditions préalables d’Azure Database Migration Service qui sont communes à tous les scénarios de migration, il existe également des conditions préalables s’appliquant spécifiquement à un scénario ou un autre.

Lorsque vous utilisez Azure Database Migration Service pour exécuter SQL Server sur les migrations Azure SQL Database, outre les conditions préalables qui sont communes à tous les scénarios de migration, veillez à respecter les conditions préalables supplémentaires suivantes :
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Créez une instance d’Azure SQL Database en suivant les indications de l’article [Création d’une base de données SQL Azure à l’aide du portail Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal).
- Téléchargez et installez [Data Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 ou version ultérieure.
- Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder aux bases de données sources.
- Créez une [règle de pare-feu](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) de niveau serveur pour le serveur Azure SQL Database afin de permettre à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Assurez-vous que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent des autorisations [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assurez-vous que les informations d’identification utilisées pour se connecter à l’instance Azure SQL Database cible disposent des autorisations CONTROL DATABASE concernant les bases de données SQL Azure cibles.

   > [!NOTE]
   > Pour obtenir une liste complète des conditions préalables requises pour utiliser Azure Database Migration Service afin d’effectuer des migrations à partir de SQL Server vers Azure SQL Database, consultez le didacticiel [Migrer SQL Server vers Azure SQL Database](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="next-steps"></a>Étapes suivantes
Pour une présentation d’Azure Database Migration Service et de la mise à disponibilité régionale pour la préversion publique, consultez l’article [Qu’est-ce qu’Azure Database Migration Service Preview ?](dms-overview.md). 