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
ms.openlocfilehash: eec6eeb3419c5f5f4c8d22398051f7cf057ac980
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Charger des données dans Azure SQL Data Warehouse à l’aide d’Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) est une base de données de mise à l’échelle basée sur le cloud qui prend en charge le traitement de grands volumes de données relationnelles et non relationnelles. SQL Data Warehouse repose sur une architecture MPP (massively parallel processing) optimisée pour les charges de travail d’entrepôt de données d’entreprise. Elle offre l’élasticité du cloud avec la flexibilité de mettre à l’échelle le stockage et d’exécuter le calcul indépendamment.

La prise en main d’Azure SQL Data Warehouse est désormais plus facile lorsque vous utilisez Azure Data Factory. Azure Data Factory est un service informatique d’intégration de données intégralement géré. Vous pouvez utiliser le service pour remplir une base de données SQL Data Warehouse avec les données de votre système existant et gagner du temps lors de la création de vos solutions d’analyse.

Azure Data Factory offre les avantages suivants pour le chargement des données dans Azure SQL Data Warehouse :

* **Facilité de configuration** : assistant intuitif en 5 étapes sans script nécessaire.
* **Prise en charge étendue du magasin de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et sur le cloud. Pour une liste détaillée, consultez le tableau des [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sécurité et conformité** : les données sont transférées sur HTTPS ou ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Performances sans précédent à l’aide de PolyBase** : PolyBase est le moyen le plus efficace de déplacer des données dans Azure SQL Data Warehouse. Utilisez la fonction blob intermédiaire pour atteindre des vitesses de charge élevées pour tous les types de magasins de données, y compris le stockage Blob Azure et Data Lake Store. (Polybase prend en charge le stockage Blob Azure et Azure Data Lake Store par défaut.) Pour plus d’informations, consultez [Performances de l’activité de copie](copy-activity-performance.md).

Cet article explique comment utiliser l’outil de copie de données Data Factory pour _charger des données d’Azure SQL Database dans Azure SQL Data Warehouse_. Vous pouvez procéder de même pour copier des données à partir d’autres types de magasins de données.

> [!NOTE]
> Pour plus d’informations, consultez [Copier des données depuis/vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Activité de copie dans Azure Data Factory version 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Azure SQL Data Warehouse : l’entrepôt de données conserve les données copiées à partir de la base de données SQL. Si vous n’avez pas d’entrepôt de données Azure SQL Data Warehouse, consultez les instructions dans [Créer un entrepôt de données SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Base de données SQL Azure : ce didacticiel copie les données à partir d’une base de données SQL avec l’exemple de données Adventure Works LT. Vous pouvez créer une base de données SQL en suivant les instructions dans [Création d’une base de données SQL Azure](../sql-database/sql-database-get-started-portal.md). 
* Compte de stockage Azure : le stockage Azure est utilisé comme objet blob _intermédiaire_ dans l’opération de copie en bloc. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu sur la gauche, sélectionnez **Nouveau** > **Données + Analytique** > **Data Factory** : 
   
   ![Créer une fabrique de données](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante :
      
   ![Page Nouvelle fabrique de données](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nom** : entrez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données \"LoadSQLDWDemo\" n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, consultez [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement** : sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version** : sélectionnez **V2 (préversion)**.
    * **Emplacement** : sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. Ces magasins de données incluent Azure Data Lake Store, le stockage Azure, la base de données SQL Azure, etc.

3. Sélectionnez **Créer**.
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante :
   
   ![Page d'accueil Data Factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Sélectionnez la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Chargement de données dans Azure SQL Data Warehouse

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données :

   ![Vignette de l’outil Copier les données](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Dans la page **Propriétés**, spécifiez **CopyFromSQLToSQLDW** dans le champ **Nom de tâche**, puis cliquez sur **Suivant** :

    ![Page Propriétés](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Dans la page **Magasin de données sources**, sélectionnez **Azure SQL Database**, puis cliquez sur **Suivant** :

    ![Page Magasin de données sources](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Sur la page **Spécifier la base de données SQL Azure**, procédez comme suit : 
   1. Sélectionnez votre serveur SQL Azure comme **nom de serveur**.
   2. Sélectionnez votre base de données SQL Azure pour le **nom de la base de données**.
   3. Spécifiez le nom de l’utilisateur comme **nom d’utilisateur**.
   4. Spécifiez le mot de passe de l’utilisateur comme **mot de passe**.
   5. Sélectionnez **Suivant**.
   
   ![Spécifier la base de données SQL Azure](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Dans la page **Sélectionner les tables à partir desquelles copier les données ou utiliser une requête personnalisée**, entrez **SalesLT** pour filtrer les tables. Activez la case **(Sélectionner tout)** pour utiliser toutes les tables pour la copie, puis cliquez sur **Suivant** : 

    ![Sélectionner les tables source](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Dans la page **Magasin de données de destination**, sélectionnez **Azure SQL Data Warehouse**, puis cliquez sur **Suivant** :

    ![Page Magasin de données de destination](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Sur la page **Spécifier la base de données Azure SQL Data Warehouse**, procédez comme suit : 

   1. Sélectionnez votre serveur SQL Azure comme **nom de serveur**.
   2. Sélectionnez votre base de données Azure SQL Data Warehouse sous le **nom de la base de données**.
   3. Spécifiez le nom de l’utilisateur comme **nom d’utilisateur**.
   4. Spécifiez le mot de passe de l’utilisateur comme **mot de passe**.
   5. Sélectionnez **Suivant**.
   
   ![Spécification de la base de données Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Dans la page **Mappage de table**, passez en revue le contenu, puis cliquez sur **Suivant**. Un mappage de table intelligent s’affiche. Les tables source sont mappées sur les tables de destination en fonction des noms de tables. Si une table source n’existe pas dans la destination, Azure Data Factory crée une table de destination par défaut qui porte le même nom. Vous pouvez également mapper une table source sur une table de destination existante. 

   > [!NOTE]
   > La création automatique de table pour le récepteur SQL Data Warehouse s’applique quand SQL Server ou Azure SQL Database est la source. Si vous copiez des données à partir d’un autre magasin de données source, vous devez précréer le schéma dans le récepteur Azure SQL Data Warehouse avant d’exécuter la copie des données.

   ![Page Mappage de table](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Dans la page **Mappage de table**, passez en revue le contenu, puis cliquez sur **Suivant**. Le mappage de table intelligent repose sur le nom de colonne. Lorsque vous autorisez Data Factory à créer automatiquement les tables, la conversion du type de données peut se produire en cas d’incompatibilités entre les magasins source et de destination. Si la conversion du type de données n’est pas prise en charge entre la colonne de destination source et de destination, un message d’erreur s’affiche en regard de la table correspondante.

    ![Page Mappage de schéma](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Dans la page **Paramètres**, sélectionnez le compte de stockage Azure dans la liste déroulante **Nom du compte de stockage**. Le compte est utilisé pour les données intermédiaires avant leur chargement dans SQL Data Warehouse à l’aide de PolyBase. Une fois la copie terminée, les données temporaires du stockage Azure sont nettoyées automatiquement. Sous **Paramètres avancés**, désélectionnez l’option **Utiliser l’option de type par défaut** :

    ![Page Paramètres](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant** :

    ![Page de résumé](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Dans la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche) :

    ![Page Déploiement](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline : 

    ![Surveiller des exécutions de pipelines](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il existe 10 activités de copie dans le pipeline et chacune copie une table de données. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** en haut. Sélectionnez **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Pour effectuer un suivi de l’exécution de chaque activité de copie, cliquez sur le lien **Détails** sous **Actions** dans la page d’analyse des activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées :

    ![Détails du suivi de l'exécution des activités](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure SQL Data Warehouse : 

> [!div class="nextstepaction"]
>[Connecteur Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)