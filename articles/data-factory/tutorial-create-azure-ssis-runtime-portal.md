---
title: "Approvisionner un runtime d’intégration Azure SSIS à l’aide du portail | Microsoft Docs"
description: "Cet article explique comment créer un runtime d’intégration Azure-SSIS à l’aide du portail Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Approvisionner un runtime d’intégration Azure SSIS en utilisant l’interface utilisateur de Data Factory
Ce didacticiel décrit les différentes étapes d’utilisation du portail Azure pour approvisionner un runtime d’intégration (IR) Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure. Pour obtenir des informations conceptuelles sur le runtime d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Dans ce didacticiel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer et démarrer un runtime d’intégration Azure-SSIS

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>configuration requise
- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer. 
- **Serveur de base de données SQL Azure**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Azure Data Factory crée la base de données du catalogue SSIS (SSISDB) sur ce serveur de base de données. Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’exécution dans SSISDB sans dépasser les régions Azure. 
    - Vérifiez que le paramètre « **Autoriser l’accès aux services Azure** » est activé pour votre serveur de base de données. Pour en savoir plus, consultez [Sécuriser votre base de données SQL Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Pour activer ce paramètre à l’aide de PowerShell, consultez [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Vérifiez que votre serveur Azure SQL Database ne dispose pas d’un catalogue SSIS (base de données SSIDB). L’approvisionnement d’Azure-SSIS IR ne prend pas en charge l’utilisation d’un catalogue SSIS existant.
 
## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Connectez-vous au [portail Azure](https://portal.azure.com/).    
2. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Sur la page **Nouvelle fabrique de données**, saisissez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomMyAzureSsisDataFactory), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
      Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge pour la création de fabriques de données sont affichés dans la liste.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Cliquez sur **Créer et surveiller** pour lancer l’interface utilisateur (IU) de Data Factory dans un onglet séparé. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure SSIS

1. Dans la page de prise en main, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Sur la page **Paramètres généraux** de **Configuration du runtime d’intégration**, procédez comme suit : 

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Spécifiez le **nom** du runtime d’intégration.
    2. Sélectionnez l’**emplacement** du runtime d’intégration. Seuls les emplacements pris en charge sont affichés.
    3. Sélectionnez la **taille du nœud** devant être configuré avec le runtime SSIS.
    4. Spécifiez le **nombre de nœuds** dans le cluster.
    5. Cliquez sur **Suivant**. 
1. Dans les **Paramètres SQL**, suivez les étapes suivantes : 

    ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Spécifiez l’**abonnement** Azure comprenant le serveur SQL Azure. 
    2. Sélectionnez votre serveur SQL Azure comme **point de terminaison de serveur de base de données du catalogue**.
    3. Saisissez le nom d’**administrateur**.
    4. Saisissez le **mot de passe** d’administrateur.  
    5. Sélectionnez le **niveau de service** pour la base de données SSISDB. La valeur par défaut est De base.
    6. Cliquez sur **Suivant**. 
1.  Sur la page **Paramètres avancés**, sélectionnez une valeur pour **Exécutions parallèles maximales par nœud**.   

    ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Cette étape est **facultative**. Si vous disposez d’un réseau virtuel classique (VNet) auquel vous souhaitez joindre le runtime d’intégration, sélectionnez l’option **Sélectionner un réseau virtuel auquel joindre le runtime d’intégration Azure-SSIS et autoriser les services Azure à configurer les paramètres/autorisations du réseau virtuel** et procédez comme suit : 

    ![Paramètres avancés avec un réseau virtuel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Spécifiez l’**abonnement** comprenant un réseau virtuel classique. 
    2. Sélectionnez le **réseau virtuel**. <br/>
    4. Sélectionnez le **sous-réseau**.<br/> 
1. Cliquez sur **Terminer** pour démarrer la création du runtime d’intégration Azure-SSIS. 

    > [!IMPORTANT]
    > - Ce processus prend environ 20 minutes
    > - Le service Data Factory se connecte à votre base de données SQL Azure pour préparer la base de données du catalogue SSIS (SSISDB). Le script configure les autorisations et les paramètres pour votre réseau virtuel, s’il est spécifié, et relie la nouvelle instance du runtime d’intégration Azure-SSIS au réseau virtuel.
    > - Si vous approvisionnez une instance de SQL Database pour héberger SSISDB, les composants Azure Feature Pack pour SSIS et Access Redistributable sont également installés. Ces composants fournissent la connectivité aux fichiers Excel et Access et à diverses sources de données Azure, en plus des sources de données prises en charge par les composants intégrés. Vous ne pouvez pas installer de composants tiers pour SSIS à l’heure actuelle (notamment des composants tiers de Microsoft, tels que les composants Oracle et Teradata par Attunity et les composants SAP BI).

7. Dans la fenêtre **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Cliquez sur **Actualiser** pour actualiser l’état. 

    ![État de la création](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Utilisez les liens sous la colonne **Actions** pour surveiller, arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

    ![Runtime d’intégration Azure SSIS - actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Cliquez sur le lien **Surveiller** sous **Actions**.  

    ![Runtime d’intégration Azure SSIS - détails](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. S’il existe une **erreur** associée au runtime d’intégration Asure SSIS, vous voyez le nombre d’erreurs sur cette page et le lien pour afficher des détails sur l’erreur. Par exemple, si le catalogue SSIS existe déjà sur le serveur de base de données, vous voyez une erreur indiquant l’existence de la base de données SSISDB.  
11. Cliquez sur **Runtimes d’intégration** en haut pour retourner sur la page précédente et afficher tous les runtimes d’intégration associés à la fabrique de données.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtimes d’intégration Azure SSIS sur le portail

1. Dans l’interface utilisateur de Azure Data Factory, basculez vers l’onglet **Modifier**, cliquez sur **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory. 
    ![Afficher les runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Cliquez sur **Nouveau** pour créer un nouveau runtime d’intégration Azure SSIS. 

    ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Pour créer un runtime d’intégration Azure-SSIS, cliquez sur **Nouveau** comme indiqué sur l’image. 
3. Dans la fenêtre de configuration de runtime d’intégration, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**.

    ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consultez la section [Approvisionner un runtime d’intégration Azure SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

 
## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur SQL Azure qui héberge le catalogue SSIS (SSISDB). Le nom du serveur SQL Azure est au format : `<servername>.database.windows.net` (pour la base de données SQL Azure). 

Consultez les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller un package SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter au catalogue SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planifier l’exécution des packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>étapes suivantes
Dans ce didacticiel, vous avez appris à : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer et démarrer un runtime d’intégration Azure-SSIS

Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
>[Copier des données dans le cloud](tutorial-copy-data-portal.md)
