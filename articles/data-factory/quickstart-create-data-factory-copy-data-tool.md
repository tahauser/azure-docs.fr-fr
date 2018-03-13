---
title: "Copier des données à l’aide de l’outil Copier des données d’Azure | Microsoft Docs"
description: "Créez une fabrique de données Azure, puis utilisez l’outil Copier des données pour copier des données depuis un dossier dans un stockage d’objets Blob Azure vers un autre dossier."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: aa9cdba4f4e891d5321eb8af6349d8b141faee03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Utiliser l’outil Copier des données pour copier des données 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-copy-data-tool.md)

Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier des données pour créer un pipeline qui copie des données depuis un dossier dans un stockage d’objets Blob Azure vers un autre dossier. 

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](data-factory-introduction.md) avant de commencer ce guide de démarrage rapide. 
>
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service, qui est en disponibilité générale (GA), consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Données + Analytique**, puis **Data Factory**. 
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
   ![Page « Nouvelle fabrique de données »](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, **&lt;votrenom&gt;ADFTutorialDataFactory**), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
   ![Erreur quand le nom n’est pas disponible](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Pour **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste. 
   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
   Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Pour **Version**, sélectionnez **V2 (préversion)**.
5. Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données. 

   Seuls les emplacements pris en charge sont affichés dans la liste. Les magasins de données (tels que le Stockage Azure et Azure SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent se trouver dans d’autres emplacements/régions.

6. Sélectionnez **Épingler au tableau de bord**.     
7. Sélectionnez **Créer**.
8. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** : 

    ![Vignette « Déploiement de Data Factory »](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche. Sélectionnez la vignette **Créer et surveiller** pour démarrer l’application d’interface utilisateur (IU) d’Azure Data Factory dans un onglet séparé.
   
   ![Page d’accueil de la fabrique de données, avec la vignette « Créer et surveiller »](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Démarrer l’outil Copier des données

1. Sur la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données. 

   ![Vignette « Copier des données »](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Sur la page **Propriétés** de l’outil Copier des données, cliquez sur **Suivant**. Vous pouvez spécifier un nom pour le pipeline et sa description dans cette page. 

   ![Page « Propriétés »](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Sur la page **Magasin de données sources**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Suivant**.

   ![Page « Magasin de données sources »](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Sur la page **Spécifier le compte de stockage d’objets Blob Azure**, sélectionnez votre compte de stockage dans la liste **Nom de compte de stockage**, puis cliquez sur **Suivant**. 

   ![Page « Spécifier le compte de stockage Blob Azure »](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :

   a. Accédez au dossier **adftutorial/input**.

   b. Sélectionnez le fichier **emp.txt**.

   c. Sélectionnez **Choisir**. Vous pouvez double-cliquer sur **emp.txt** pour ignorer cette étape.

   d. Sélectionnez **Suivant**. 

   ![Page « Choisir le fichier ou le dossier d’entrée »](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Sur la page **Paramètres de format de fichier**, notez que l’outil détecte automatiquement les séparateurs de ligne et de colonne, puis cliquez sur **Suivant**. Vous pouvez également afficher un aperçu des données et un schéma des données d’entrée sur cette page. 

   ![Page « Paramètres de format de fichier »](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Dans la page **Magasin de données de destination**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Suivant**. 

   ![Page « Magasin de données de destination »](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Sur la page **Spécifier le compte de stockage d’objets Blob Azure**, sélectionnez votre compte de stockage d’objets Blob Azure et cliquez sur **Suivant**. 

   ![Page « Spécifier le compte de stockage Blob Azure »](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit : 

   a. Entrez **adftutorial/output** pour le chemin d’accès du dossier.

   b. Entrez **emp.txt** pour le nom de fichier.

   c. Sélectionnez **Suivant**. 

   ![Page « Choisir le fichier ou le dossier de sortie »](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Sur la page **Paramètres de format de fichier**, cliquez sur **Suivant**. 

    ![Page « Paramètres de format de fichier »](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Sur la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page « Paramètres »](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Dans la page **Résumé**, passez en revue tous les paramètres, puis cliquez sur **Suivant**. 

    ![Page « Résumé »](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Sur la page **Déploiement terminé**, sélectionnez **Surveiller** pour surveiller le pipeline que vous avez créé. 

    ![Page « Déploiement terminé »](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. L’application bascule vers l’onglet **Surveiller**. Vous voyez l’état du pipeline dans cet onglet. Sélectionnez **Actualiser** pour actualiser la liste. 
    
    ![Onglet de Surveillance des exécutions de pipelines, avec le bouton « Actualiser »](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Le pipeline n’a qu’une seule activité de type **Copier**. 

    ![Liste des exécutions d’activités](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Pour afficher les détails de l’opération de copie, cliquez sur le lien **Détails** (image en forme de lunettes) dans la colonne **Actions**. Pour plus d’informations sur les propriétés, consultez [Vue d’ensemble de l’activité de copie](copy-activity-overview.md). 

    ![Copier les détails de l’opération](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Vérifiez que le fichier **emp.txt** est créé dans le dossier **de sortie** du conteneur **adftutorial**. Si le dossier de sortie n’existe pas, le service Data Factory le crée automatiquement. 
18. Basculez vers l’onglet **Modifier** pour pouvoir modifier les services liés, les jeux de données et les pipelines. Pour apprendre à les modifier dans l’interface utilisateur de Data Factory, consultez [Créer une fabrique de données à l’aide du portail Azure](quickstart-create-data-factory-portal.md).

    ![Onglet Modifier](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios, consultez les [didacticiels](tutorial-copy-data-portal.md). 