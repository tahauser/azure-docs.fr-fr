---
title: Vue d’ensemble des conditions préalables pour l’utilisation d’Azure Database Migration Service | Microsoft Docs
description: Découvrez une vue d’ensemble des conditions préalables pour l’utilisation d’Azure Database Migration Service pour effectuer des migrations de bases de données.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 883e71c871f3d1f279aa4adc2c0cec7c610333ba
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Vue d’ensemble des conditions préalables pour l’utilisation d’Azure Database Migration Service
Il existe plusieurs conditions préalables requises pour vous garantir qu’Azure Database Migration Service s’exécute correctement lors de la migration de bases de données. Certaines des conditions préalables s’appliquent à tous les scénarios (paires source-cible) pris en charge par le service, tandis que d’autres sont propres à un scénario spécifique.

Les conditions préalables relatives à l’utilisation d’Azure Database Migration Service sont répertoriées dans les sections suivantes.

## <a name="prerequisites-common-across-migration-scenarios"></a>Conditions préalables communes dans l’ensemble des scénarios de migration
Les conditions préalables associées à Azure Database Migration Service communes à tous les scénarios de migration pris en charge incluent le besoin de :
- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Conditions préalables pour la migration de SQL Server vers Azure SQL Database 
En plus des conditions préalables d’Azure Database Migration Service qui sont communes à tous les scénarios de migration, il existe également des conditions préalables s’appliquant spécifiquement à un scénario ou un autre.

Lorsque vous utilisez Azure Database Migration Service pour exécuter SQL Server sur les migrations Azure SQL Database, outre les conditions préalables qui sont communes à tous les scénarios de migration, veillez à respecter les conditions préalables supplémentaires suivantes :

- Créez une instance d’Azure SQL Database en suivant les indications de l’article [Création d’une base de données SQL Azure à l’aide du portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Téléchargez et installez [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou version ultérieure.
- Créez une [règle de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de niveau serveur pour le serveur Azure SQL Database afin de permettre à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Assurez-vous que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent des autorisations [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assurez-vous que les informations d’identification utilisées pour se connecter à l’instance Azure SQL Database cible disposent des autorisations CONTROL DATABASE concernant les bases de données SQL Azure cibles.

   > [!NOTE]
   > Pour obtenir une liste complète des conditions préalables requises pour utiliser Azure Database Migration Service afin d’effectuer des migrations à partir de SQL Server vers Azure SQL Database, consultez le didacticiel [Migrer SQL Server vers Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Prérequis de la migration de SQL Server vers Azure SQL Database Managed Instance
- Créez une instance Azure SQL Database Managed Instance en suivant les indications de l’article [Créer une instance Azure SQL Database Managed Instance dans le portail Azure](https://aka.ms/sqldbmi).
- Ouvrez vos pare-feu afin d’autoriser le trafic SMB via le port 445 pour la plage d’adresses IP ou le sous-réseau Azure Database Migration Service.
- Vérifiez que les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance appartiennent à des membres du rôle serveur sysadmin.
- Créez un partage réseau qu’Azure Database Migration Service peut utiliser pour sauvegarder la base de données source.
- Vérifiez que le compte de service qui exécute l’instance source de SQL Server dispose de privilèges d’écriture pour le partage réseau que vous avez créé.
- Notez l’utilisateur Windows (et son mot de passe) qui dispose d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration.
- Créez un conteneur d’objets blob et récupérez son URI SAP en suivant les étapes de l’article [Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).  Veillez à sélectionner toutes les autorisations (lecture, écriture, suppression, liste) dans la fenêtre de la stratégie lorsque vous créez l’URI SAP.

   > [!NOTE]
   > Pour obtenir la liste complète des prérequis de l’utilisation d’Azure Database Migration Service afin d’effectuer des migrations à partir de SQL Server vers Azure SQL Database Managed Instance, consultez le tutoriel [Migrer SQL Server vers Azure SQL Database Managed Instance](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Étapes suivantes
Pour une présentation d’Azure Database Migration Service et de la mise à disponibilité régionale pour la préversion publique, consultez l’article [Qu’est-ce qu’Azure Database Migration Service Preview ?](dms-overview.md). 