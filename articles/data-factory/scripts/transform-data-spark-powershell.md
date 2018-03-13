---
title: "Script PowerShell - Transformer des données dans le cloud à l’aide de Data Factory | Microsoft Docs"
description: "Ce script PowerShell transforme les données dans le cloud en exécutant le programme Spark sur un cluster Azure HDInsight Spark."
services: data-factory
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: f83d9d2e862f909d6eaa0c02ecac745909aab83a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Script PowerShell - Transformer des données dans le cloud à l’aide d’Azure Data Factory

Cet exemple de script PowerShell crée un pipeline qui transforme les données dans le cloud en exécutant le programme Spark sur un cluster Azure HDInsight Spark. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>configuration requise
* **Compte Stockage Azure**. Créez un script Python et un fichier d’entrée, puis téléchargez-les sur le stockage Azure. La sortie du programme Spark est stockée dans ce compte de stockage. Le cluster Spark sur demande utilise le même compte de stockage comme stockage principal.  

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
2. Créez un sous-dossier nommé `inputfiles` dans le dossier `spark` du conteneur blob. 
3. Téléchargez le fichier `minecraftstory.txt` dans le sous-dossier `inputfiles`. 

## <a name="sample-script"></a>Exemple de script
> [!IMPORTANT]
> Ce script crée des fichiers JSON qui définissent des entités Data Factory (service lié, jeu de données et pipeline) sur votre disque dur dans le dossier c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Après avoir exécuté l’exemple de script, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées :

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Pour supprimer la fabrique de données du groupe de ressources, exécutez la commande suivante : 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | Créer une fabrique de données. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | Crée un service lié dans la fabrique de données. Un service lié rattache un magasin de données ou une ressource de calcul à une fabrique de données. |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | Crée un pipeline dans la fabrique de données. Un pipeline contient une ou plusieurs activités effectuant une opération donnée. Dans ce pipeline, une activité spark transforme les données en exécutant un programme sur un cluster Azure HDInsight Spark. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | Crée une exécution pour le pipeline. En d’autres termes, exécute le pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtient les détails de l’exécution de l’activité dans le pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Factory sont à votre disposition dans [Exemples PowerShell pour Azure Data Factory](../samples-powershell.md).