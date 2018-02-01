---
title: "Charger des données dans Azure SQL Data Warehouse à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Utiliser Azure Data Factory pour copier des données dans Azure SQL Data Warehouse"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Charger des données dans Azure SQL Data Warehouse à l’aide d’Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) est une base de données de mise à l’échelle basée sur le cloud qui prend en charge le traitement de grands volumes de données relationnelles et non relationnelles.  Reposant sur une architecture MPP (massively parallel processing), SQL Data Warehouse est optimisée pour les charges de travail d’entrepôt de données d’entreprise.  Elle offre l’élasticité du cloud avec la flexibilité de mettre à l’échelle le stockage et d’exécuter le calcul indépendamment.

La prise en main d’Azure SQL Data Warehouse est désormais plus facile à l’aide **d’Azure Data Factory**.  Azure Data Factory est un service d’intégration de données cloud entièrement géré, qui peut être utilisé pour remplir une base de données SQL Data Warehouse avec les données de votre système existant. Cela vous permet de gagner un temps précieux lors de l’évaluation de SQL Data Warehouse et de la création de vos solutions d’analyse. Voici les principaux avantages liés au chargement de données dans Azure SQL Data Warehouse à l’aide d’Azure Data Factory :

* **Facilité de configuration** : assistant intuitif en 5 étapes sans script nécessaire.
* **Prise en charge étendue des magasins de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et sur le cloud (pour connaître la liste détaillée, consultez le [tableau des magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats)).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute et la présence globale du service garantit que vos données ne quittent jamais les limites géographiques
* **Performances sans précédent à l’aide de PolyBase** : l’utilisation de PolyBase est le moyen le plus efficace de déplacer des données dans Azure SQL Data Warehouse. À l’aide de la fonction blob intermédiaire, vous pouvez obtenir des vitesses de charge élevées pour tous les types de magasins de données en plus du stockage Blob Azure et de Data Lake Store, pris en charge par PolyBase par défaut. Pour plus d’informations, consultez le [Guide sur les performances de l’activité de copie](copy-activity-performance.md).

Cet article explique comment utiliser l’outil de copie de données Data Factory pour **charger des données d’Azure SQL Database dans Azure SQL Data Warehouse**. Vous pouvez procéder de même pour copier des données à partir d’autres types de magasins de données.

> [!NOTE]
>  Pour des informations générales sur les fonctionnalités de Data Factory permettant de copier des données de/vers Azure SQL Data Warehouse, consultez l’article [Copier des données depuis/vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>configuration requise

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Azure SQL Data Warehouse**. Cet entrepôt de données conserve les données copiées à partir de SQL Database. Si vous n’avez pas d’entrepôt de données Azure SQL Data Warehouse, consultez l’article [Créer un entrepôt de données SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) pour la procédure à suivre.
* **Base de données SQL Azure**. Ce didacticiel explique comment copier une base de données Azure SQL Database avec l’exemple de données Adventure Works LT. Pour cela, vous pouvez en créer une en suivant les instructions de l’article [Créer une base de données SQL Azure](../sql-database/sql-database-get-started-portal.md). 
* **Compte Stockage Azure**. Le compte Stockage Azure est utilisé comme objet blob **intermédiaire** dans l’opération de copie en bloc. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Cliquez sur **NOUVEAU** dans le menu de gauche, puis sur **Données et analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs indiquées dans la capture d’écran suivante :
      
     ![Page Nouvelle fabrique de données](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **Nom.** Entrez un nom unique global pour la fabrique de données. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **Abonnement.** Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
    * **Groupe de ressources.** Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version.** Sélectionnez **V2 (préversion)**.
    * **Emplacement.** Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements/régions. 

3. Cliquez sur **Créer**.
4. Après la création, accédez à votre fabrique de données, où la page **Data Factory** s’affiche comme sur l’image. Cliquez sur la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé.
   
   ![Page d'accueil Data Factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Chargement de données dans Azure SQL Data Warehouse

1. Sur la page de prise en main, cliquez sur la vignette **Copier des données** pour lancer l’outil Copier les données. 

   ![Vignette de l’outil Copier les données](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Sur la page **Propriétés** de l’outil Copier les données, spécifiez **CopyFromSQLToSQLDW** comme **nom de tâche**, puis cliquez sur **Suivant**. 

    ![Outil Copier les données - Page Propriétés](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Sur la page **Magasin de données sources**, sélectionnez **Azure SQL Database**, puis cliquez sur **Suivant**.

    ![Page Magasin de données sources](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Sur la page **Spécifier la base de données SQL Azure**, procédez comme suit : 
    1. Sélectionnez votre serveur SQL Azure comme **nom de serveur**.
    2. Sélectionnez votre base de données SQL Azure pour le **nom de la base de données**.
    3. Spécifiez le nom de l’utilisateur comme **nom d’utilisateur**.
    4. Spécifiez le mot de passe de l’utilisateur comme **mot de passe**.
    5. Cliquez sur **Suivant**. 

        ![Spécifier la base de données SQL Azure](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Sur la page **Sélectionner les tables à partir desquelles copier les données ou utiliser une requête personnalisée**, filtrez les tables en spécifiant **SalesLT** dans la zone d’entrée, cochez la case **(Sélectionner tout)** pour sélectionner toutes les tables, puis cliquez sur **Suivant**. 

    ![Choisir le fichier ou le dossier d’entrée](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Sur la page **Magasin de données de destination**, sélectionnez **Azure SQL Data Warehouse**, puis cliquez sur **Suivant**. 

    ![Page Magasin de données de destination](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Sur la page **Spécifier la base de données Azure SQL Data Warehouse**, procédez comme suit : 

    1. Sélectionnez votre serveur SQL Azure comme **nom de serveur**.
    2. Sélectionnez votre base de données Azure SQL Data Warehouse sous le **nom de la base de données**.
    3. Spécifiez le nom de l’utilisateur comme **nom d’utilisateur**.
    4. Spécifiez le mot de passe de l’utilisateur comme **mot de passe**.
    5. Cliquez sur **Suivant**. 

        ![Spécification de la base de données Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Sur la page **Mappage de table**, vérifiez les données et cliquez sur **Suivant**. Un mappage de table intelligent mettant en correspondance la source avec des tables de destination en fonction des noms de la table, s’affiche. Si la table n’existe pas dans la destination, Azure Data Factory en crée une par défaut qui porte le même nom. Vous pouvez également choisir d’effectuer un mappage vers une table existante. 

    > [!NOTE]
    > La création automatique de table pour le récepteur SQL Data Warehouse s’applique quand SQL Server ou Azure SQL Database est la source. Si vous copiez des données à partir d’un autre magasin de données sources, vous devez précréer le schéma dans le récepteur Azure SQL Data Warehouse avant de copier les données.

    ![Page Mappage de table](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Sur la page **Mappage de schéma**, vérifiez les données et cliquez sur **Suivant**. Le mappage intelligent repose sur le nom de colonne. Si vous choisissez de laisser la fabrique de données créer automatiquement les tables, une conversion appropriée du type de données peut se produire si nécessaire pour corriger l’incompatibilité entre les magasins source et de destination. Si la conversion du type de données n’est pas prise en charge entre la colonne de destination source et de destination, un message d’erreur s’affiche le long de la table correspondante.

    ![Page Mappage de schéma](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Sur la page **Paramètres**, sélectionnez un stockage Azure dans la liste déroulante **Nom du compte de stockage**. Celui-ci est utilisé pour les données intermédiaires avant leur chargement dans SQL Data Warehouse à l’aide de PolyBase. Une fois la copie terminée, les données temporaires du stockage seront nettoyées automatiquement. 

    Sous « Paramètres avancés », décochez la case « Utiliser l’option de type par défaut ».

    ![Page Paramètres](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Dans la page **Résumé**, passez en revue les paramètres, puis cliquez sur **Suivant**.

    ![Page de résumé](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Dans la page **Déploiement**, cliquez sur **Surveiller** pour surveiller le pipeline (tâche).

    ![Page Déploiement](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline. 

    ![Surveiller des exécutions de pipelines](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Pour afficher des exécutions d’activité associées à l’exécution de pipelines, cliquez sur le lien **Voir les exécutions d’activités** dans la colonne **Actions**. Il existe 10 activités de copie dans le pipeline. Chacune d’entre elles copie une table de données. Pour revenir à l’affichage des exécutions du pipeline, cliquez sur le lien **Pipelines** en haut. Cliquez sur **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Vous pouvez effectuer un suivi avancé de l’exécution de chaque activité de copie en accédant à la page d’analyse des activités et en cliquant sur le lien **Détails** sous **Actions**. Parmi les informations répertoriées figurent le volume de données copiées de la source vers le récepteur, le débit, les étapes effectuées avec la durée correspondante, et les configurations utilisées.

    ![Détails du suivi de l'exécution des activités](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure SQL Data Warehouse : 

> [!div class="nextstepaction"]
>[Connecteur Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)