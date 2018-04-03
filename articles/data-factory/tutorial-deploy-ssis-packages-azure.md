---
title: Déployer des packages SSIS sur Azure | Microsoft Docs
description: Cet article explique comment déployer des packages SSIS dans Azure et créer un runtime d’intégration Azure-SSIS en utilisant Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 01/29/2018
ms.author: douglasl
ms.openlocfilehash: aca9f822bf3fd3b26e554240a4fee2474b89143d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Déployer des packages SQL Server Integration Services sur Azure
Ce didacticiel décrit les différentes étapes d’utilisation du portail Azure pour approvisionner un runtime d’intégration (IR) Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools ou SQL Server Management Studio pour déployer des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure. Pour obtenir des informations conceptuelles sur les runtimes d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale (GA), consultez la [documentation relative à Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 
- **Serveur de base de données SQL Azure**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Azure Data Factory crée le catalogue SSIS (base de données SSISDB) sur ce serveur de base de données. Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’exécution dans la base de données SSISDB sans dépasser les régions Azure. 
- Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Pour en savoir plus, consultez [Sécuriser votre base de données SQL Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Pour activer ce paramètre à l’aide de PowerShell, consultez [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
- Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
- Vérifiez que votre serveur Azure SQL Database ne dispose pas d’un catalogue SSIS (base de données SSISDB). L’approvisionnement d’un runtime d’intégration SSIS Azure ne prend pas en charge l’utilisation d’un catalogue SSIS existant.

> [!NOTE]
> - Vous pouvez créer une fabrique de données de version 2 dans les régions suivantes : Est des États-Unis, Est des États-Unis 2, Asie du Sud-Est et Europe de l’Ouest. 
> - Vous pouvez créer un runtime d’intégration Azure-SSIS dans les régions suivantes : Est des États-Unis, Est des États-Unis 2, Centre des États-Unis, Europe du Nord, Europe de l’Ouest et Est de l’Australie. 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Connectez-vous au [Portail Azure](https://portal.azure.com/).    
3. Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Données + Analytique**, puis **Data Factory**. 
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Sur la page **Nouvelle fabrique de données**, saisissez **MyAzureSsisDataFactory** sous **Nom**. 
      
   ![Page « Nouvelle fabrique de données »](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, **&lt;votrenom&gt;MyAzureSsisDataFactory**), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour en savoir plus sur les règles d’affectation des noms d’artefacts Data Factory.
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. Pour **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous voulez créer la fabrique de données. 
5. Pour **Groupe de ressources**, effectuez l’une des opérations suivantes :
     
   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste. 
   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
   Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
6. Pour **Version**, sélectionnez **V2 (préversion)**.
7. Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge pour la création de fabriques de données sont affichés dans la liste.
8. Sélectionnez **Épingler au tableau de bord**.     
9. Sélectionnez **Créer**.
10. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** : 

   ![Vignette « Déploiement de Data Factory »](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Une fois la création terminée, la page **Data Factory** s’affiche.
   
   ![Page d’accueil de la fabrique de données](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Sélectionnez **Créer et surveiller** pour ouvrir l’interface utilisateur de Data Factory dans un onglet séparé. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure-SSIS

1. Dans la page de **prise en main**, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Sur la page **Paramètres généraux** de **Configuration du runtime d’intégration**, procédez comme suit : 

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Pour **Nom**, indiquez le nom du runtime d’intégration.

   b. Pour **Emplacement**, sélectionnez l’emplacement du runtime d’intégration. Seuls les emplacements pris en charge sont affichés.

   c. Pour **Taille du nœud**, sélectionnez la taille du nœud devant être configuré avec le runtime SSIS.

   d. Pour **Nombre de nœuds**, indiquez le nombre de nœuds du cluster.
   
   e. Sélectionnez **Suivant**. 
3. Sur la page **Paramètres SQL**, procédez comme suit : 

   ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Pour **Abonnement**, indiquez l’abonnement Azure comprenant le serveur de base de données Azure.

   b. Pour **Point de terminaison de serveur de base de données du catalogue**, sélectionnez votre serveur de base de données Azure.

   c. Pour **Nom d’utilisateur de l’administrateur**, entrez le nom d’utilisateur de l’administrateur.

   d. Pour **Mot de passe d’administrateur**, entrez le mot de passe de l’administrateur.

   e. Pour **Niveau de serveur de base de données de catalogue**, sélectionnez le niveau de service pour la base de données SSISDB. La valeur par défaut est **De base**.

   f. Sélectionnez **Suivant**. 
4. Sur la page **Paramètres avancés**, sélectionnez une valeur pour **Exécutions parallèles maximales par nœud**.   

   ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Cette étape est *facultative*. Si vous disposez d’un réseau virtuel (créé à l’aide du modèle de déploiement classique ou d’Azure Resource Manager) auquel vous souhaitez joindre le runtime d’intégration, cochez la case **Sélectionner un réseau virtuel auquel joindre le runtime d’intégration Azure-SSIS et autoriser les services Azure à configurer les paramètres/autorisations du réseau virtuel**. Effectuez ensuite les tâches suivantes : 

   ![Paramètres avancés avec un réseau virtuel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. Pour **Abonnement**, indiquez l’abonnement comprenant le réseau virtuel.

   b. Pour **Nom du réseau virtuel**, sélectionnez le nom du réseau virtuel.

   c. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau du réseau virtuel. 
6. Sélectionnez **Terminer** pour démarrer la création du runtime d’intégration Azure-SSIS. 

   > [!IMPORTANT]
   > Ce processus prend environ 20 minutes.
   >
   > Le service Data Factory se connecte à votre base de données SQL Azure pour préparer le catalogue SSIS (base de données SSISDB). Le script configure également les autorisations et les paramètres de votre réseau virtuel, s’il est spécifié. Il joint également la nouvelle instance du runtime d’intégration Azure-SSIS sur le réseau virtuel.
   > 
   > Si vous approvisionnez l’instance d’un runtime d’intégration Azure-SSIS, les composants Azure Feature Pack pour SSIS et Access Redistributable sont également installés. Ces composants fournissent la connectivité aux fichiers Excel et Access et à diverses sources de données Azure, en plus des sources de données prises en charge par les composants intégrés. Vous ne pouvez pas installer de composants tiers pour SSIS à l’heure actuelle. Cette restriction inclut les composants tiers de Microsoft, tels que les composants Oracle et Teradata par Attunity et les composants BI SAP.

7. Dans l’onglet **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Sélectionnez **Actualiser** pour actualiser l’état. 

   ![État de la création avec bouton Actualiser](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

   ![Liens de la colonne Actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Créer un runtime d’intégration Azure-SSIS

1. Dans l’interface utilisateur d’Azure Data Factory, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory. 
   ![Sélections pour l’affichage des runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Sélectionnez **Nouveau** pour créer un runtime d’intégration Azure-SSIS. 

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. Dans la **fenêtre de configuration de runtime d’intégration**, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**.

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consultez la section [Approvisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

 
## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools ou SQL Server Management Studio pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur de base de données Azure qui héberge le catalogue SSIS (base de données SSISDB). Le nom du serveur de base de données Azure est au format `<servername>.database.windows.net` (pour Azure SQL Database). 

Consultez les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller un package SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter au catalogue SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planifier l’exécution des packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.

Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
> [Copier des données dans le cloud](tutorial-copy-data-portal.md)
