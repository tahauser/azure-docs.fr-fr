---
title: "Charger des téraoctets de données dans SQL Data Warehouse | Microsoft Docs"
description: "Montre comment 1 To de données peut être chargé dans Azure SQL Data Warehouse en moins de 15 minutes avec Azure Data Factory"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b931de564417ab98207321d7798613b187e411f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Charger 1 To dans Azure SQL Data Warehouse en moins de 15 minutes avec Azure Data Factory
> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service Data Factory, qui est en préversion, consultez [Copier des données depuis/vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) est une base de données de mise à l’échelle basée sur le cloud qui prend en charge le traitement de grands volumes de données relationnelles et non relationnelles.  Reposant sur une architecture MPP (massively parallel processing), SQL Data Warehouse est optimisée pour les charges de travail d’entrepôt de données d’entreprise.  Elle offre l’élasticité du cloud avec la flexibilité de mettre à l’échelle le stockage et d’exécuter le calcul indépendamment.

La prise en main d’Azure SQL Data Warehouse est désormais plus facile à l’aide **d’Azure Data Factory**.  Azure Data Factory est un service d’intégration de données cloud entièrement géré, qui peut être utilisé pour remplir une base de données SQL Data Warehouse avec les données de votre système existant. Cela vous permet de gagner un temps précieux lors de l’évaluation de SQL Data Warehouse et de la création de vos solutions d’analyse. Voici les principaux avantages liés au chargement de données dans Azure SQL Data Warehouse à l’aide d’Azure Data Factory :

* **Facilité de configuration** : assistant intuitif en 5 étapes sans script nécessaire.
* **Prise en charge étendue du magasin de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et sur le cloud.
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute et la présence globale du service garantit que vos données ne quittent jamais les limites géographiques
* **Performances sans précédent à l’aide de PolyBase** : l’utilisation de Polybase est le moyen le plus efficace pour déplacer des données dans Azure SQL Data Warehouse. À l’aide de la fonction blob intermédiaire, vous pouvez obtenir des vitesses de charge élevées pour tous les types de magasins de données en plus du Stockage Blob Azure, pris en charge par Polybase par défaut.

Cet article vous montre comment utiliser l’Assistant Copie de Data Factory pour charger 1 To de données depuis le Stockage Blob Azure dans Azure SQL Data Warehouse en moins de 15 minutes, à un débit de 1,2 Go/s minimum.

Cet article fournit des instructions détaillées pour déplacer les données dans Azure SQL Data Warehouse à l’aide de l’Assistant Copie.

> [!NOTE]
>  Pour des informations générales sur les fonctionnalités de Data Factory permettant de déplacer des données vers et depuis Azure SQL Data Warehouse, consultez [Déplacer des données vers et depuis Azure SQL Data Warehouse à l’aide d’Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md).
>
> Vous pouvez également créer des pipelines à l’aide du portail Azure, de Visual Studio, de PowerShell, etc. Consultez le [Didacticiel : copie de données d’Azure Blob Storage vers une base de données SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir une brève procédure pas à pas de l’utilisation de l’activité de copie dans Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Prérequis
* Stockage Blob Azure : cette expérience utilise le Stockage Blob Azure (GRS) pour stocker un jeu de données de test TPC-H.  Si vous ne possédez pas de compte de stockage Azure, découvrez [comment créer un compte de stockage](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Données [TPC-H](http://www.tpc.org/tpch/) : nous allons utiliser TPC-H comme jeu de données de test.  Pour ce faire, vous devez utiliser `dbgen` dans le kit d’outils TPC-H, qui vous permet de générer le jeu de données.  Vous pouvez télécharger le code source pour `dbgen` depuis [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) et le compiler vous-même, ou vous pouvez télécharger le fichier binaire compilé à partir de [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Exécutez dbgen.exe avec les commandes suivantes pour générer le fichier plat de 1 To pour la table `lineitem` répartie entre 10 fichiers :

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    À présent, copiez les fichiers générés vers Azure Blob.  Consultez [Déplacer des données vers et depuis un système de fichiers local à l’aide d’Azure Data Factory](data-factory-onprem-file-system-connector.md) pour savoir comment procéder à l’aide d’ADF Copy.    
* Azure SQL Data Warehouse : cette expérience charge des données dans Azure SQL Data Warehouse créé avec 6 000 DWU

    Consultez [Créer un Azure SQL Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) pour obtenir des instructions détaillées sur la création d’une base de données SQL Data Warehouse.  Pour obtenir les meilleures performances possibles de charge dans SQL Data Warehouse à l’aide de Polybase, nous choisissons le nombre maximal d’unités de DWU (Data Warehouse Units) autorisé dans le paramètre Performance, qui est de 6 000 DWU.

  > [!NOTE]
  > Lors du chargement à partir d’Azure Blob, les performances de chargement des données sont directement proportionnelles au nombre de DWU configuré dans le SQL Data Warehouse :
  >
  > Le chargement de 1 To dans 1 000 DWU SQL Data Warehouse prend 87 minutes (débit d’environ 200 Mo/s). Le chargement de 1 To dans 2000 DWU SQL Data Warehouse prend 46 minutes (débit d’environ 380 Mo/s). Le chargement de 1 To dans 6 000 DWU SQL Data Warehouse prend 14 minutes (débit d’environ 1,2 Go/s).
  >
  >

    Pour créer un SQL Data Warehouse avec 6 000 DWU, déplacez le curseur Performance complètement à droite :

    ![Curseur Performance](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Pour une base de données existante qui n’est pas configurée avec 6 000 DWU, vous pouvez la mettre à l’échelle à l’aide du portail Azure.  Accédez à la base de données dans le portail Azure. Il existe un bouton **Mise à l’échelle** situé dans le panneau **Présentation** illustré dans l’image suivante :

    ![Bouton Mise à l’échelle](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Cliquez sur le bouton **Mise à l’échelle** pour ouvrir le panneau suivant, déplacez le curseur sur la valeur maximale, puis cliquez sur le bouton **Enregistrer**.

    ![Boîte de dialogue de mise à l’échelle](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Cette expérience charge des données dans Azure SQL Data Warehouse à l’aide de la classe de ressources `xlargerc`.

    Pour obtenir le meilleur débit possible, la copie doit être effectuée à l’aide d’un utilisateur SQL Data Warehouse appartenant à la classe de ressources `xlargerc`.  Découvrez comment procéder en consultant [Exemple de modification d’une classe de ressources utilisateur](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Créez le schéma de la table de destination dans la base de données Azure SQL Data Warehouse en exécutant l’instruction DDL suivante :

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
Une fois les étapes requises terminées, nous sommes désormais prêts à configurer l’activité de copie à l’aide de l’Assistant Copie.

## <a name="launch-copy-wizard"></a>Lancer l’Assistant Copie
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Créer une ressource** en haut à gauche, cliquez sur **Intelligence + analyse**, puis sur **Data Factory**.
3. Dans le volet **Nouvelle fabrique de données** :

   1. Entrez **LoadIntoSQLDWDataFactory** pour le **nom**.
       Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomLoadIntoSQLDWDataFactory), puis tentez de la recréer : **Le nom de la fabrique de données « LoadIntoSQLDWDataFactory » n'est pas disponible**. Consultez la rubrique [Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.  
   2. Sélectionnez votre **abonnement**Azure.
   3. Pour Groupe de ressources, effectuez l’une des opérations suivantes :
      1. Sélectionnez **Utiliser l’existant** pour sélectionner un groupe de ressources existant.
      2. Sélectionnez **Créer un nouveau** pour entrer un nom pour un groupe de ressources.
   4. Sélectionnez un **emplacement** pour la fabrique de données.
   5. Sélectionnez la case à cocher **Épingler au tableau de bord** en bas du panneau.  
   6. Cliquez sur **Créer**.
4. Une fois la création terminée, le panneau **Data Factory** s’affiche comme sur l’image suivante :

   ![Page d'accueil Data Factory](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Dans la page d’accueil Fabrique de données, cliquez sur la vignette **Copier les données** pour lancer l’**Assistant de copie**.

   > [!NOTE]
   > Si vous voyez que le navigateur web est bloqué au niveau « Autorisation... », désactivez/décochez l’option **Block third party cookies and site data** (Bloquer les cookies et les données de site tiers) (ou) laissez cette option activée et créez une exception pour **login.microsoftonline.com**, puis essayez de relancer l’Assistant.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Étape 1 : Configurer la planification du chargement de données
La première étape consiste à configurer la planification du chargement de données.  

Dans la page **Propriétés** :

1. Entrez **CopyFromBlobToAzureSqlDataWarehouse** comme **Nom de la tâche**
2. Sélectionnez l’option **Exécuter une fois**.   
3. Cliquez sur **Suivant**.  

    ![Assistant Copie - Page Propriétés](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Étape 2 : Configurer la source
Cette section décrit les étapes pour configurer la source : Azure Blob contenant les fichiers d’éléments de ligne TPC-H 1 To.

1. Sélectionnez **Stockage Blob Azure** comme magasin de données et cliquez sur **Suivant**.

    ![Assistant Copie - Page Sélectionner la source](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Renseignez les informations de connexion pour le compte Stockage Blob Azure, puis cliquez sur **Suivant**.

    ![Assistant Copie - Informations de connexion à la source](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Choisissez le **dossier** contenant les fichiers d’éléments de ligne TPC-H et cliquez sur **Suivant**.

    ![Assistant Copie - Sélectionner le dossier d’entrée](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Lorsque vous cliquez sur **Suivant**, les paramètres de format de fichier sont détectés automatiquement.  Vérifiez que le délimiteur de colonne est « | » au lieu de la virgule par défaut « , ».  Cliquez sur **Suivant** une fois que vous avez affiché un aperçu des données.

    ![Assistant Copie - Paramètres de format de fichier](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Étape 3 : Configurer la destination
Cette section vous montre comment configurer la destination : la table `lineitem` dans la base de données Azure SQL Data Warehouse.

1. Choisissez **Azure SQL Data Warehouse** comme magasin de destination et cliquez sur **Suivant**.

    ![Assistant Copie - Sélectionner le magasin de données de destination](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Renseignez les informations de connexion pour Azure SQL Data Warehouse.  Veillez à spécifier l’utilisateur qui est membre du rôle `xlargerc` (voir les **conditions préalables** pour obtenir des instructions détaillées), puis cliquez sur **Suivant**.

    ![Assistant Copie - Informations de connexion à la destination](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Choisissez la table de destination et cliquez sur **Suivant**.

    ![Assistant Copie - Page Mappage de table](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Dans la page Mappage de schéma, laissez l’option « Appliquer le mappage de colonnes » décochée et cliquez sur **Suivant**.

## <a name="step-4-performance-settings"></a>Étape 4 : Paramètres de performance

La case **Autoriser Polybase** est cochée par défaut.  Cliquez sur **Suivant**.

![Assistant Copie - Page Mappage de schéma](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Étape 5 : Déployer et surveiller les résultats du chargement
1. Cliquez sur le bouton **Terminer** pour déployer.

    ![Assistant Copie - Page Résumé](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Une fois le déploiement terminé, cliquez sur `Click here to monitor copy pipeline` pour surveiller la progression de l’exécution de la copie. Sélectionner le pipeline de copie que vous avez créé dans la liste **Fenêtres d’activité**.

    ![Assistant Copie - Page Résumé](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Vous pouvez afficher les détails de l’exécution de la copie dans **l’Explorateur de fenêtres d’activité** dans le panneau de droite, notamment le volume de données lu à partir de la source et écrit dans la destination, la durée et le débit moyen de l’exécution.

    Comme vous pouvez le voir dans la capture d’écran suivante, la copie de 1 To à partir du Stockage Blob Azure vers SQL Data Warehouse a pris 14 minutes, avec un débit de 1,22 Gbits/s !

    ![Assistant Copie - Boîte de dialogue Succès](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Meilleures pratiques
Voici quelques meilleures pratiques pour l’exécution de votre base de données Azure SQL Data Warehouse :

* Utilisez une classe de ressources supérieure lors du chargement dans un INDEX COLUMNSTORE EN CLUSTER.
* Pour des jonctions plus efficaces, envisagez d’utiliser la distribution par hachage en fonction d’une colonne sélectionnée au lieu de la distribution par tourniquet (round robin) par défaut.
* Pour des vitesses de chargement plus rapides, envisagez d’utiliser des tas pour les données temporaires.
* Créez des statistiques une fois le chargement Azure SQL Data Warehouse terminé.

Pour plus d’informations, consultez [Meilleures pratiques pour Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

## <a name="next-steps"></a>étapes suivantes
* [Assistant Copie de Data Factory](data-factory-copy-wizard.md) : cet article fournit des détails sur l’Assistant Copie.
* [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) : cet article contient le guide sur le réglage et les mesures de performances de référence.
