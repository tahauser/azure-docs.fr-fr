---
title: "Charger des données dans Azure Data Lake Store à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Utiliser Azure Data Factory pour copier des données dans Azure Data Lake Store"
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
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Charger des données dans Azure Data Lake Store à l’aide d’Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) est un référentiel d'entreprise à très grande échelle pour les charges de travail d'analyse du Big Data. Azure Data Lake vous permet de capturer les données de toute taille, de tout type et à toute vitesse d'ingestion dans un emplacement unique en vue d'une analyse opérationnelle et exploratoire.

Azure Data Factory est un service d’intégration de données cloud entièrement géré, qui peut être utilisé pour renseigner Lake à l’aide des données de votre système existant. Cela vous permet de gagner un temps précieux durant la création de vos solutions d’analyse. Voici les principaux avantages liés au chargement de données dans Azure Data Lake Store à l’aide d’Azure Data Factory :

* **Facilité de configuration** : assistant intuitif en 5 étapes sans script nécessaire.
* **Prise en charge étendue des magasins de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et sur le cloud (pour connaître la liste détaillée, consultez le [tableau des magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats)).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute et la présence globale du service garantit que vos données ne quittent jamais les limites géographiques
* **Hautes performances** : vitesse de chargement des données dans Azure Data Lake Store pouvant atteindre 1 Gbit/s. Pour plus d’informations, consultez le [Guide sur les performances de l’activité de copie](copy-activity-performance.md).

Cet article explique comment utiliser l’outil de copie de données Data Factory pour **charger des données d’Amazon S3 dans Azure Data Lake Store**. Vous pouvez procéder de même pour copier des données à partir d’autres types de magasins de données.

> [!NOTE]
>  Pour des informations générales sur les fonctionnalités de Data Factory permettant de copier des données de/vers Azure Data Lake Store, consultez l’article [Copier des données depuis/vers Azure Data Lake Store à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md).
>
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>configuration requise

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Azure Data Lake Store**. Si vous n’avez pas de compte Data Lake Store, consultez l’article [Créer un compte Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) pour savoir comment en créer un.
* **Amazon S3**. Cet article explique comment copier des données à partir d’Amazon S3. Vous pouvez utiliser d’autres magasins de données en procédant de la même façon.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Cliquez sur **NOUVEAU** dans le menu de gauche, puis sur **Données et analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs indiquées dans la capture d’écran suivante : 
      
     ![Page Nouvelle fabrique de données](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **Nom.** Entrez un nom unique global pour la fabrique de données. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
            `Data factory name "LoadADLSDemo" is not available`

    * **Abonnement.** Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
    * **Groupe de ressources.** Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version.** Sélectionnez **V2 (préversion)**.
    * **Emplacement.** Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (Azure Data Lake Store, Stockage Azure, Azure SQL Database, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements/régions.

3. Cliquez sur **Créer**.
4. Après la création, accédez à votre fabrique de données, où la page **Data Factory** s’affiche comme sur l’image. Cliquez sur la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé. 
   
   ![Page d'accueil Data Factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Charger des données dans Azure Data Lake Store

1. Sur la page de prise en main, cliquez sur la vignette **Copier des données** pour lancer l’outil Copier les données. 

   ![Vignette de l’outil Copier les données](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Sur la page **Propriétés** de l’outil Copier les données, spécifiez **CopyFromAmazonS3ToADLS** comme **nom de tâche**, puis cliquez sur **Suivant**. 

    ![Outil Copier les données - Page Propriétés](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Sur la page **Magasin de données sources**, sélectionnez **Amazon S3**, puis cliquez sur **Suivant**.

    ![Page Magasin de données sources](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Sur la page **Spécifier la connexion Amazon S3**, procédez comme suit : 
    1. Spécifiez l’**ID de clé d’accès**.
    2. Spécifiez la **clé d’accès secrète**.
    3. Cliquez sur **Suivant**. 

        ![Spécification du compte Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Sur la page **Choisir le fichier ou le dossier d’entrée**, accédez au dossier ou au fichier que vous voulez copier, sélectionnez-le et cliquez sur **Choisir**, puis sur **Suivant**. 

    ![Choisir le fichier ou le dossier d’entrée](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Sur la page **Magasin de données de destination**, sélectionnez **Azure Data Lake Store**, puis cliquez sur **Suivant**. 

    ![Page Magasin de données de destination](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Sur cette page, choisissez le comportement de copie en sélectionnant les options de **copie récursive des fichiers** et de **copie binaire** (copie des fichiers en l’état). Cliquez sur **Suivant**.

    ![Spécification du dossier de sortie](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Sur la page **Spécifier la connexion Data Lake Store**, procédez comme suit : 

    1. Sélectionnez votre base de données Data Lake Store sous **Nom du compte Data Lake Store**.
    2. Spécifiez les informations du principal de service, notamment **Locataire**, **ID de principal de service** et **Clé de principal de service**.
    3. Cliquez sur **Suivant**. 

    > [!IMPORTANT]
    > Dans cette procédure pas à pas, vous utilisez un **principal de service** pour authentifier Data Lake Store. Suivez les instructions indiquées [ici](connector-azure-data-lake-store.md#using-service-principal-authentication) et veillez à accorder l’autorisation appropriée au principal de service dans Azure Data Lake Store.

    ![Spécification du compte Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. Sur la page **Choisir le fichier ou le dossier de sortie**, spécifiez **copyfroms3**, puis cliquez sur **Suivant**. 

    ![Spécification du dossier de sortie](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. Dans la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Dans la page **Résumé**, passez en revue les paramètres, puis cliquez sur **Suivant**.

    ![Page de résumé](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Dans la page **Déploiement**, cliquez sur **Surveiller** pour surveiller le pipeline (tâche).

    ![Page Déploiement](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline. 

    ![Surveiller des exécutions de pipelines](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Pour afficher des exécutions d’activité associées à l’exécution de pipelines, cliquez sur le lien **Voir les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline, vous ne voyez donc qu’une seule entrée. Pour revenir à l’affichage des exécutions du pipeline, cliquez sur le lien **Pipelines** en haut. Cliquez sur **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Vous pouvez effectuer un suivi avancé de l’exécution de chaque activité de copie en accédant à la page d’analyse des activités et en cliquant sur le lien **Détails** sous **Actions**. Parmi les informations répertoriées figurent le volume de données copiées de la source vers le récepteur, le débit, les étapes effectuées avec la durée correspondante, et les configurations utilisées.

    ![Détails du suivi de l'exécution des activités](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Vérifiez que les données sont copiées dans votre base de données Azure Data Lake Store. 

    ![Vérification de la sortie de Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure Data Lake Store : 

> [!div class="nextstepaction"]
>[Connecteur Azure Data Lake Store](connector-azure-data-lake-store.md)