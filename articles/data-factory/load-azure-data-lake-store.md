---
title: Charger des données dans Azure Data Lake Store à l’aide d’Azure Data Factory | Microsoft Docs
description: Utiliser Azure Data Factory pour copier des données dans Azure Data Lake Store
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: bf0d607d63a68a222a1d44d9cb05253497d12591
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Charger des données dans Azure Data Lake Store à l’aide d’Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) est un référentiel d'entreprise à très grande échelle pour les charges de travail d'analyse du Big Data. Azure Data Lake vous permet de capturer des données de toute taille, de tout type et dont les vitesses de réception sont variées. Les données sont capturées à un emplacement unique, à des fins d’analytique opérationnelle et exploratoire.

Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez utiliser le service pour remplir le lac de données avec les données de votre système existant et gagner du temps lors de la création de vos solutions d’analyse.

Azure Data Factory offre les avantages suivants lors du chargement des données dans Azure Data Lake Store :

* **Facilité de configuration** : assistant intuitif en 5 étapes. Aucun script nécessaire.
* **Prise en charge étendue du magasin de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et informatiques. Pour une liste détaillée, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Hautes performances** : la vitesse de chargement des données dans Azure Data Lake Store peut atteindre 1 Gbit/s. Pour en savoir plus, voir [Performances de l’activité de copie](copy-activity-performance.md).

Cet article explique comment utiliser l’outil de copie de données Data Factory pour _charger les données d’Amazon S3 dans Azure Data Lake Store_. Vous pouvez procéder de même pour copier des données à partir d’autres types de banques de données.

> [!NOTE]
> Pour en savoir plus, voir [Copier des données depuis/vers Azure Data Lake Store à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md).
>
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez la section [Activité de copie dans Azure Data Factory version 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Azure Data Lake Store : si vous n’avez pas de compte Data Lake Store, suivez les instructions de l’article [Créer un compte Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3 : cet article explique comment copier des données à partir d’Amazon S3. Vous pouvez utiliser d’autres magasins de données en procédant de la même façon.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Nouveau** > **Données + Analytique** > **Data Factory** :
   
   ![Créer une fabrique de données](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante : 
      
   ![Page Nouvelle fabrique de données](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nom** : saisissez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données \"LoadADLSDemo\" n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement** : sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : sélectionnez un groupe de ressources existant dans la liste déroulante, ou sélectionnez l’option **Créer** et indiquez le nom d’un groupe de ressources. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version** : sélectionnez **V2 (préversion)**.
    * **Emplacement** : sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. Ces magasins de données incluent Azure Data Lake Store, le Stockage Azure, Microsoft Azure SQL Database, etc.

3. Sélectionnez **Créer**.
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante : 
   
   ![Page d'accueil Data Factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Sélectionnez la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé.

## <a name="load-data-into-azure-data-lake-store"></a>Charger des données dans Azure Data Lake Store

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier les données** pour démarrer l’outil Copier les données : 

   ![Vignette de l’outil Copier les données](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Dans la page **Propriétés**, spécifiez **CopyFromAmazonS3ToADLS** dans le champ **Nom de tâche**, puis cliquez sur **Suivant** :

    ![Page Propriétés](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Sur la page **Magasin de données sources**, sélectionnez **Amazon S3**, puis cliquez sur **Suivant** :

    ![Page Magasin de données sources](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Sur la page **Spécifier la connexion Amazon S3**, procédez comme suit : 
   1. Spécifiez la valeur du champ **ID de clé d’accès**.
   2. Spécifiez la valeur **Clé d’accès secrète**.
   3. Sélectionnez **Suivant**.
   
   ![Spécification du compte Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Sur la page de **sélection du fichier ou dossier d’entrée**, accédez au dossier et au fichier sur lesquels effectuer la copie. Sélectionnez le dossier ou le fichier ; cliquez sur **Choisir**, puis sur **Suivant** :

    ![Choisir le fichier ou le dossier d’entrée](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Sur la page **Magasin de données de destination**, sélectionnez **Azure Data Lake Store**, puis choisissez **Suivant** :

    ![Page Magasin de données de destination](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Choisissez le comportement de copie en sélectionnant les options de **copie récursive des fichiers** et de **copie binaire** (copie des fichiers en l’état). Sélectionnez **Suivant** :

    ![Spécification du dossier de sortie](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Sur la page **Spécifier la connexion Data Lake Store**, procédez comme suit : 

   1. Sélectionnez votre base de données Data Lake Store sous **Nom du compte Data Lake Store**.
   2. Spécifiez les informations du principal de service, à savoir **Locataire**, **ID de principal de service** et **Clé de principal de service**.
   3. Sélectionnez **Suivant**.
   
   > [!IMPORTANT]
   > Dans cette procédure pas à pas, vous utilisez un _principal de service_ pour authentifier Data Lake Store. Veillez à accorder au principal de service les autorisations appropriées dans Azure Data Lake Store, en suivant [ces instructions](connector-azure-data-lake-store.md#using-service-principal-authentication).
   
   ![Spécification du compte Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Dans la page de **sélection du fichier ou dossier de sortie**, saisissez **copyfroms3** dans le champ du nom du dossier de sortie, puis sélectionnez **Suivant** : 

    ![Spécification du dossier de sortie](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Sur la page **Paramètres**, cliquez sur **Suivant** :

    ![Page Paramètres](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant** :

    ![Page de résumé](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Dans la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche) :

    ![Page Déploiement](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution de l’activité et de réexécuter le pipeline :

    ![Surveiller des exécutions de pipelines](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Pour surveiller l’exécution de chaque activité de copie, cliquez sur le lien **Détails** sous **Actions** dans la page de surveillance des activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées :

    ![Détails du suivi de l'exécution des activités](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Vérifiez que les données sont copiées dans votre base de données Azure Data Lake Store : 

    ![Vérification de la sortie de Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure Data Lake Store : 

> [!div class="nextstepaction"]
>[Connecteur Azure Data Lake Store](connector-azure-data-lake-store.md)