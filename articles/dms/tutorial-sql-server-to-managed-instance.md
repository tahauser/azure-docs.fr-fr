---
title: Utiliser Azure Database Migration Service pour migrer SQL Server vers Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment migrer une instance locale de SQL Server vers Azure SQL Database Managed Instance à l’aide d’Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 8abf3bae3a2274ed5514a5c621675b4c9ec27ae2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Migrer SQL Server vers Azure SQL Database Managed Instance
Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance SQL Server locale vers Azure SQL Database. Dans ce tutoriel, vous allez migrer la base de données **Adventureworks2012** d’une instance locale de SQL Server vers Azure SQL Database à l’aide d’Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Découvrez les topologies de réseau pour les migrations Azure SQL DB Managed Instance à l’aide d’Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Configurez [l’accès au moteur de base de données source dans votre Pare-feu Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre Pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source, par défaut le port TCP 1433.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Si vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration, ainsi qu’aux fichiers via le port SMB 445.
- Créez une instance Azure SQL Database Managed Instance en suivant les indications de l’article [Créer une instance Azure SQL Database Managed Instance dans le portail Azure](https://aka.ms/sqldbmi).
- Vérifiez que les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance appartiennent à des membres du rôle serveur sysadmin.
- Créez un partage réseau qu’Azure Database Migration Service peut utiliser pour sauvegarder la base de données source.
- Vérifiez que le compte de service qui exécute l’instance source de SQL Server dispose de privilèges d’écriture pour le partage réseau que vous avez créé.
- Notez l’utilisateur Windows (et son mot de passe) qui dispose d’un contrôle total sur le partage réseau que vous avez créé précédemment. Azure Database Migration Service emprunte l’identité de l’utilisateur pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure pour l’opération de restauration.
- Créez un conteneur d’objets blob et récupérez son URI SAS en suivant les étapes de l’article [Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Veillez à sélectionner toutes les autorisations (lecture, écriture, suppression, liste) dans la fenêtre de la stratégie lors de la création de l’URI SAS. Azure Database Migration Service peut ainsi accéder à votre conteneur de compte de stockage afin de charger les fichiers de sauvegarde utilisés pour migrer les bases de données sur Azure SQL Database Managed Instance.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1.  Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.
![Afficher les abonnements du portail](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.
![Afficher les fournisseurs de ressources](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.
![Inscrire un fournisseur de ressources](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance

1.  Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez **Azure Database Migration Service**, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

     ![Place de marché Azure](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  Dans l’écran **Azure Database Migration Service (version préliminaire)**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  Dans l’écran **Azure Database Migration Service**, spécifiez un nom pour le service, l’abonnement, le groupe de ressources, le réseau virtuel et le niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing). *Azure Database Migration Service est actuellement disponible en préversion. Son utilisation ne vous sera pas facturée.*

    **Réseau :** Sélectionnez un réseau virtuel existant ou créez-en un pour qu’Azure Database Migration Service puisse accéder à l’instance source de SQL Server et à l’instance cible d’Azure SQL Database Managed Instance. [Découvrez les topologies de réseau pour les migrations Azure SQL DB Managed Instance à l’aide d’Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez [Créer un réseau virtuel comprenant des sous-réseaux à l’aide du portail Azure](https://aka.ms/DMSVnet).

    ![Créer un service DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Sélectionnez **Créer** pour créer le service.


## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, puis ouvrez-le.

1.  Sélectionnez **+ Nouveau projet de migration**.

1.  Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Enfin, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database Managed Instance**.

    ![Créer un projet DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Sélectionnez **Créer** pour créer le projet.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1.  Dans l’écran **Détails de la source**, spécifiez les détails de connexion de SQL Server source.

    ![Détails de la source](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Sélectionnez **Enregistrer**, puis sélectionnez la base de données **AdventureWorks2012** pour la migration.

    ![Sélectionner les bases de données sources](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1.  Sélectionnez **Enregistrer** puis, dans l’écran **Détails de la cible**, spécifiez les détails de connexion de la cible, à savoir l’instance préprovisionnée d’Azure SQL Database Managed Instance vers laquelle la base de données **AdventureWorks2012** doit être migrée.

    ![Sélectionner la cible](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Sélectionnez **Enregistrer**.

1.  Dans l’écran **Récapitulatif du projet**, vérifiez les détails associés au projet de migration.

## <a name="run-the-migration"></a>Exécuter la migration

1.  Sélectionnez le projet récemment enregistré, sélectionnez **+ Nouvelle activité**, puis sélectionnez **Exécuter la migration**.

    ![Créer une activité](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Lorsque vous y êtes invité, entrez les informations d’identification des serveurs source et cible, puis sélectionnez **Enregistrer**.

1.  Dans l’écran **Mapper aux bases de données cibles**, sélectionnez les bases de données sources que vous souhaitez migrer.

    ![Sélectionner les bases de données sources](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Sélectionnez **Enregistrer**, puis, dans l’écran **Configurer les paramètres de migration**, fournissez les informations suivantes :

    | | |
    |--------|---------|
    |**Emplacement de sauvegarde du serveur** | Partage réseau local qu’Azure Database Migration Service peut utiliser pour sauvegarder la base de données source. Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges d’écriture pour ce partage réseau. |
    |**Nom d'utilisateur** | Nom d’utilisateur Windows dont Azure Database Migration Service emprunte l’identité pour charger les fichiers de sauvegarde vers le conteneur de stockage Azure pour l’opération de restauration. |
    |**Mot de passe** | Mot de passe de l’utilisateur ci-dessous. |
    |**URI SAS du stockage** | URI SAS qui permet à Azure Database Migration Service d’accéder au conteneur de compte de stockage dans lequel le service charge les fichiers de sauvegarde et qui est utilisé pour migrer les bases de données sur Azure SQL Database Managed Instance. [Découvrez comment obtenir l’URI SAS du conteneur d’objets blob](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Configurer les paramètres de migration](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Sélectionnez **Enregistrer** dans l’écran Résumé de la migration. Ensuite, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration.

    ![Résumé de la migration](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Surveiller la migration

1.  Sélectionnez l’activité de migration pour consulter l’état de l’activité.

1.  Une fois la migration terminée, vérifiez les bases de données cibles présentes sur l’instance cible d’Azure SQL Database Managed Instance.

    ![Surveiller la migration](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

