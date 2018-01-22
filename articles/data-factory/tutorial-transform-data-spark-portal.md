---
title: "Transformer des données à l’aide de Spark dans Azure Data Factory | Microsoft Docs"
description: "Ce didacticiel fournit des instructions détaillées sur la transformation des données à l’aide d’une activité Spark dans Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: 8bd9382ed5a855368533c6bf2305682861c109c0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformer des données dans le cloud à l’aide d’une activité Spark dans Azure Data Factory
Dans ce didacticiel, vous utilisez le portail Azure pour créer un pipeline Azure Data Factory qui transforme des données à l’aide d’une activité Spark et d’un service lié HDInsight à la demande. Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données. 
> * Créer un pipeline avec une activité Spark
> * Déclencher une exécution du pipeline
> * Surveiller l’exécution du pipeline.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis
* **Compte Stockage Azure**. Vous créez un script Python et un fichier d’entrée, puis vous les téléchargez vers le stockage Azure. La sortie du programme Spark est stockée dans ce compte de stockage. Le cluster Spark sur demande utilise le même compte de stockage comme stockage principal.  
* **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Télécharger un script Python dans votre compte de stockage d’objets Blob
1. Créez un fichier Python nommé **WordCount_Spark.py** avec le contenu suivant : 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Remplacez **&lt;storageAccountName&gt;** par le nom de votre compte de stockage Azure. Puis enregistrez le fichier. 
3. Dans votre stockage Blob Azure, créez un conteneur nommé **adftutorial** s’il n’existe pas. 
4. Créez un dossier nommé **spark**.
5. Créer un sous-dossier nommé **script** sous le dossier **spark**. 
6. Téléchargez le fichier **WordCount_Spark.py** dans le sous-dossier **script**. 


### <a name="upload-the-input-file"></a>Télécharger le fichier d’entrée
1. Créez un fichier nommé **minecraftstory.txt** avec du texte. Le programme Spark compte le nombre de mots dans ce texte. 
2. Créez un sous-dossier nommé `inputfiles` dans le dossier `spark`. 
3. Téléchargez le fichier `minecraftstory.txt` dans le sous-dossier `inputfiles`. 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page de nouvelle fabrique de données](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Erreur : nom indisponible](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
      Certaines étapes de ce guide de démarrage rapide supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup** . Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. Cliquez sur le titre **Créer et surveiller** pour lancer l’application de l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-linked-services"></a>Créez des services liés
Vous créez deux services liés dans cette section : 
    
- Un **service lié au stockage Azure** relie un compte de stockage Azure à la fabrique de données. Ce stockage est utilisé par le cluster HDInsight à la demande. Il contient également le script Spark à exécuter. 
- Un **service lié HDInsight à la demande**. Azure Data Factory crée automatiquement un cluster HDInsight, exécute le programme Spark, puis supprime le cluster HDInsight à la fin de la période d’inactivité préconfigurée. 

### <a name="create-an-azure-storage-linked-service"></a>Créer un service lié Stockage Azure

1. Sur la page **Prise en main**, basculez vers l’onglet **Modifier** dans le volet gauche, comme illustré dans l’image suivante : 

    ![Création d’une vignette de pipeline](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Cliquez sur **Connexions** au bas de la fenêtre, puis cliquez sur **+ Nouveau**. 

    ![Bouton Nouvelle connexion](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. Dans la fenêtre **Nouveau service lié**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Continuer**. 

    ![Sélectionner Stockage Blob Azure](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Sélectionnez votre **Nom de compte de stockage Azure**, puis cliquez sur **Enregistrer**. 

    ![Spécifier un compte de stockage Blob](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Créer un service lié HDInsight à la demande

1. Cliquez de nouveau sur le bouton **+ Nouveau** pour créer un nouveau service lié. 
2. Dans la fenêtre **Nouveau service lié**, sélectionnez **Azure HDInsight**, puis cliquez sur **Continuer**. 

    ![Sélectionner Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureHDInsightLinkedService** comme **nom**.
    2. Vérifiez que **HDInsight à la demande** est le **Type** sélectionné.
    3. Sélectionnez **AzureStorage1** comme **Service lié Stockage Azure**. Vous avez déjà créé ce service lié. Si vous avez utilisé un nom différent, spécifiez le nom correct pour ce champ. 
    4. Sélectionnez **spark** comme **Type de Cluster**.
    5. Entrez l’**ID du principal de service** ayant l’autorisation de créer un cluster HDInsight. Ce principal de service doit être membre du rôle Contributeur de l’abonnement ou du groupe de ressources dans lequel le cluster est créé. Pour plus de détails, reportez-vous à l’article relatif à la [création de l’application Azure Active Directory et du principal du service à l’aide du portail](../azure-resource-manager/resource-group-create-service-principal-portal.md).
    6. Saisissez la **clé du principal de service**. 
    7. Sélectionnez le même groupe de ressources que celui utilisé lors de la création de la fabrique de données pour le **groupe de ressources**. Le cluster Spark est créé dans ce groupe de ressources. 
    8. Développer **le type de système d’exploitation**.
    9. Entrez un **nom** pour l’**utilisateur** du cluster. 
    10. Entrez le **mot de passe** correspondant à l’utilisateur. 
    11. Cliquez sur **Enregistrer**. 

        ![Paramètres du service lié HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight présente une limite relative au nombre total de cœurs que vous pouvez utiliser dans chaque région Azure prise en charge. Pour le service lié HDInsight à la demande, le cluster HDInsight sera créé au même emplacement de stockage Azure que celui utilisé comme stockage principal. Vérifiez que vous disposez des quotas de cœurs suffisants pour pouvoir créer le cluster avec succès. Pour plus d’informations, reportez-vous à l’article [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka et bien plus encore](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Créer un pipeline

2. Cliquez sur le bouton + (plus), puis cliquez sur **Pipeline** dans le menu.

    ![Menu Nouveau pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. Dans la boîte à outils **Activités**, développez **HDInsight**, et glissez-déposez l’activité **Spark** depuis la boîte à outils **Activités** vers la surface du concepteur de pipeline. 

    ![Glisser-déposer une activité Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. Dans les propriétés de la fenêtre d’activité **Spark** en bas, procédez comme suit : 

    1. Basculez vers l’onglet **Cluster HDI**.
    2. Sélectionnez **AzureHDInsightLinkedService** créé à l’étape précédente. 
        
    ![Spécifier un service lié HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Basculez vers l’onglet **Script/Jar**, et procédez comme suit : 

    1. Sélectionnez **AzureStorage1** comme **Service lié au travail**.
    2. Cliquez sur **Parcourir le stockage**. 
    3. Accédez au dossier **adftutorial/spark/script**, sélectionnez **WordCount_Spark.py**, puis cliquez sur **Terminer**.      

    ![Spécifier le script Spark](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. Pour valider le pipeline, cliquez sur le bouton **Valider** dans la barre d’outils. Cliquez sur le bouton **flèche droite** (>>) pour fermer la fenêtre de validation. 
    
    ![Bouton de validation](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Cliquez sur **Publier**. L’interface utilisateur de Data Factory publie des entités (services liés et pipelines) sur le service Azure Data Factory. 

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline
Cliquez sur **Déclencher** dans la barre d’outils, cliquez sur **Déclencher maintenant**. 

![Déclencher maintenant](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Basculez vers l’onglet **Surveiller**. Vérifiez qu’un pipeline est exécuté. La création d’un cluster Spark prend 20 minutes environ. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Cliquez régulièrement sur **Actualiser** pour vérifier l’état de l’exécution des pipelines. 

    ![État de l’exécution de pipelines](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. Pour afficher des exécutions d’activité associées à l’exécution de pipelines, cliquez sur l’action **Voir les exécutions d’activités** dans la colonne Actions. Vous pouvez basculer vers la vue des exécutions de pipelines en cliquant sur le lien **Pipelines** en haut.

    ![Vue des exécutions d’activités](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Vérifier la sortie
Vérifiez que le fichier de sortie est créé dans le dossier spark/otuputfiles/wordcount du conteneur adftutorial. 

![Vérifier la sortie](./media/tutorial-transform-data-spark-portal/verity-output.png)

Le fichier doit contenir chaque mot du fichier texte entrée et le nombre d’apparitions du mot dans le fichier. Par exemple :  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>étapes suivantes
Le pipeline dans cet exemple transforme les données à l’aide de l’activité Spark et un service lié HDInsight de la demande. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données. 
> * Créer un pipeline avec une activité Spark
> * Déclencher une exécution du pipeline
> * Surveiller l’exécution du pipeline.

Passez au didacticiel suivant pour découvrir comment transformer des données en exécutant un script Hive sur un cluster Azure HDInsight qui se trouve dans un réseau virtuel. 

> [!div class="nextstepaction"]
> [Didacticiel : transformer des données à l’aide de Hive dans un réseau virtuel Azure](tutorial-transform-data-hive-virtual-network-portal.md).





