---
title: Utiliser Azure Database Migration Service pour migrer SQL Server vers Azure SQL Database | Microsoft Docs
description: "Découvrez comment migrer SQL Server local vers Azure SQL à l’aide d’Azure Database Migration Service."
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 8dc8b4db80d5e319fad0b681924ab5a8e5642b2e
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Migrer SQL Server vers Azure SQL Database
Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance SQL Server locale vers Azure SQL Database. Dans ce didacticiel, vous allez migrer la base de données **Adventureworks2012** restaurée dans une instance locale de SQL Server 2016 (ou une version ultérieure) vers Azure SQL Database à l’aide d’Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Évaluer votre base de données locale à l’aide de l’Assistant Migration des données.
> * Migrer l’exemple de schéma à l’aide de l’Assistant Migration des données.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration en utilisant Azure Database Migration Service.
> * Exécuter la migration.
> * Surveiller la migration.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Téléchargez et installez [SQL Server 2016 ou version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads) (toute édition).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Créez une instance d’Azure SQL Database en suivant les indications de l’article [Création d’une base de données SQL Azure à l’aide du portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Téléchargez et installez [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou version ultérieure.
- Créez un réseau virtuel pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Assurez-vous que les règles de groupe de sécurité Réseau virtuel Microsoft Azure ne bloquent pas les ports de communication 443, 53, 9354, 445 et 12000. Pour plus d’informations sur le filtrage de groupe de sécurité Réseau virtuel Microsoft Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au SQL Server source.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
- Créez une [règle de pare-feu](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) de niveau serveur pour le serveur Azure SQL Database afin de permettre à Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Assurez-vous que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent des autorisations [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assurez-vous que les informations d’identification utilisées pour se connecter à l’instance Azure SQL Database cible disposent des autorisations CONTROL DATABASE concernant les bases de données SQL Azure cibles.

## <a name="assess-your-on-premises-database"></a>Évaluer votre base de données locale
Avant de pouvoir migrer des données d’une instance SQL Server locale vers Azure SQL Database, vous devez évaluer les problèmes de blocage dans la base de données SQL Server qui peuvent empêcher la migration. Après avoir téléchargé et installé Data Migration Assistant v3.3 ou version ultérieure, suivez les étapes décrites dans l’article [Évaluation de la migration de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) pour évaluer la base de données locale. Un résumé de la procédure est présenté ci-dessous :
1.  Dans l’Assistant Migration des données, sélectionnez l’icône Nouveau (+), puis sélectionnez le type de projet **Évaluation**.
2.  Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**.
3.  Sélectionnez **Créer** pour créer le projet.

    Pendant l’évaluation de la migration de la base de données SQL Server source vers Azure SQL Database, vous pouvez choisir un ou les deux types de rapports d’évaluation suivants :
    - Vérifier la compatibilité de la base de données
    - Vérifier la parité de fonctionnalité

    Les deux types de rapports sont sélectionnés par défaut.
4.  Dans l’Assistant Migration des données, dans l’écran **Options**, sélectionnez **suivant**.
5.  Dans l’écran **Sélectionner les sources**, dans la boîte de dialogue **Se connecter à un serveur**, fournissez les détails de connexion à votre SQL Server, puis sélectionnez **Se connecter**.
6.  Dans la boîte de dialogue **Add sources (Ajouter des sources)**, sélectionnez **AdventureWorks2012** et **Ajouter**, puis **Start Assessment (Démarrer l’évaluation)**.

    Une fois l’évaluation terminée, les résultats s’affichent comme illustré dans le graphique suivant :

    ![Évaluer la migration des données](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Pour Azure SQL Database, les évaluations identifient les problèmes de blocage de migration et les problèmes de parité de fonctionnalité.

7.  Passez en revue les résultats d’évaluation en ce qui concerne les problèmes de blocage de migration et les problèmes de parité de fonctionnalité en sélectionnant les options spécifiques.
    - La catégorie de **parité de fonctionnalité SQL Server** fournit un ensemble complet de recommandations, d’approches alternatives disponibles dans Azure et de procédures d’atténuation pour vous aider à planifier les efforts de vos projets de migration.
    - La catégorie de **problèmes de compatibilité** indique les fonctionnalités partiellement ou non prises en charge reflétant les éventuels problèmes de compatibilité susceptibles de bloquer la migration des bases de données SQL Server locales vers Azure SQL Database. Des recommandations sont également fournies pour vous aider à résoudre ces problèmes.


## <a name="migrate-the-sample-schema"></a>Migrer l’exemple de schéma
Une fois que vous êtes à l’aise avec l’évaluation et que la base de données sélectionnée est un bon candidat pour la migration vers Azure SQL Database, utilisez Data Migration Assistant pour migrer le schéma vers Azure SQL Database.

> [!NOTE]
> Avant de créer un projet de migration dans Data Migration Assistant, vérifiez que vous avez bien approvisionné une base de données SQL Azure comme indiqué dans les conditions préalables. Dans ce didacticiel, le nom d’Azure SQL Database est censé être **AdventureWorksAzure**, mais vous pouvez lui attribuer un autre nom si vous le souhaitez.

Pour migrer le schéma **AdventureWorks2012** vers Azure SQL Database, procédez comme suit :

1.  Dans Data Migration Assistant, sélectionnez l’icône Nouveau (+), puis sous **Type de projet**, sélectionnez **Migration**.
3.  Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**.
4.  Sous **Étendue de la migration**, sélectionnez **Schéma uniquement**.

    Une fois les étapes précédentes effectuées, l’interface de l’Assistant Migration des données doit apparaître comme indiqué dans le graphique suivant :
    
    ![Créer un projet d’Assistant Migration des données](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Sélectionnez **Créer** pour créer le projet.
6.  Dans l’Assistant Migration des données, spécifiez les détails de connexion source de votre SQL Server, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorks2012**.

    ![Détails de connexion source de l’Assistant Migration des données](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Sélectionnez **Suivant**, sous **Se connecter au serveur cible**, spécifiez les détails de connexion cible de la base de données Azure SQL, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorksAzure** que vous avez pré-approvisionnée dans la base de données Azure SQL.

    ![Détails de connexion cible de l’Assistant Migration des données](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Sélectionnez **Suivant** pour accéder à l’écran **Sélectionner des objets** dans lequel vous pouvez spécifiez les objets de schéma dans la base de données **AdventureWorks2012** à déployer sur Azure SQL Database.

    Par défaut, tous les objets sont sélectionnés.

    ![Générer des scripts SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Sélectionnez **Générer un script SQL** pour créer les scripts SQL, puis recherchez d’éventuelles erreurs dans les scripts.

    ![Script de schéma](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Sélectionnez **Déployer le schéma** pour déployer sur Azure SQL Database puis, une fois le schéma déployé, recherchez d’éventuelles anomalies sur le serveur cible.

    ![Déployer le schéma](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration
1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.
 
   ![Afficher les abonnements au portail](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.
 
    ![Afficher les fournisseurs de ressources](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.
 
    ![S’inscrire auprès du fournisseur de ressources](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>Créer une instance
1.  Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  Dans l’écran **Azure Database Migration Service (version préliminaire)**, sélectionnez **Créer**.
 
    ![Créer une instance Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  Dans l’écran **Azure Database Migration Service**, spécifiez un nom pour le service, l’abonnement, un réseau virtuel et le niveau de tarification.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

     ![Configurer des paramètres d’instance Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration
Une fois le service créé, recherchez-le dans le portail Azure, puis créez un projet de migration.
1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.
 
      ![Rechercher toutes les instances Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure DMS que vous avez créée, puis sélectionnez l’instance.
 
     ![Rechercher votre instance Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Sélectionnez **+ Nouveau projet de migration**.
4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**.

    ![Créer un projet Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Sélectionnez **Créer** pour créer le projet.


## <a name="specify-source-details"></a>Spécifier les détails de la source
1. Dans l’écran **Détails de la source**, spécifiez les détails de connexion de SQL Server source.

    ![Sélectionner la source](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Sélectionnez **Enregistrer**, puis sélectionnez la base de données **AdventureWorks2012** pour la migration.

    ![Sélectionner la base de données source](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Spécifier les détails de la cible
1. Sélectionnez **Enregistrer** puis, dans l’écran **Détails de la cible**, spécifiez les détails de connexion de la cible, à savoir Azure SQL Database pré-approvisionnée sur laquelle le schéma **AdventureWorks2012**  a été déployé à l’aide de l’Assistant Migration des données.

    ![Sélectionner la cible](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Sélectionnez **Enregistrer** pour enregistrer le projet.
3. Dans l’écran **Résumé de la migration**, passez en revue et vérifiez les détails associés au projet de migration.

    ![Résumé DMS](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Sélectionnez **Enregistrer**.

## <a name="run-the-migration"></a>Exécuter la migration
1.  Sélectionnez le projet récemment enregistré, sélectionnez **+ Nouvelle activité**, puis sélectionnez **Exécuter la migration des données**.

    ![Nouvelle activité](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  Lorsque vous y êtes invité, entrez les informations d’identification des serveurs source et cible, puis sélectionnez **Enregistrer**.
3.  Dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure DMS sélectionne la base de données cible par défaut.

    ![Mapper aux bases de données cibles](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Sélectionnez **Enregistrer**, dans l’écran **Sélectionner des tables**, développez la liste des tables et passez en revue la liste des champs concernés.

    ![Sélectionner des tables](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Sélectionnez **Enregistrer**, dans l’écran **Résumé de la migration**, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration.

    Dans cet écran, vous pouvez également développer l’écran **Choisir l’option de validation**, que vous pouvez utiliser pour valider les éléments suivants de la base de données migrée :
    - Schéma
    - Cohérence des données
    - Exactitude de la requête et performances

    ![Choisir l’option de validation](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Sélectionnez **Enregistrer**, passez en revue le résumé pour vérifier que les détails de la source et de la cible correspondent à ce que vous avez spécifié précédemment.

    ![Résumé de la migration](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Sélectionnez **Exécuter la migration** pour démarrer l’activité de migration, puis sélectionnez **Actualiser** pour consulter l’état actuel.

    ![État de l’activité](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Surveiller la migration
1. Sélectionnez l’activité de migration pour consulter l’état de l’activité.
2. Vérifiez la base de données Azure SQL cible une fois la migration terminée.

    ![Completed](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
