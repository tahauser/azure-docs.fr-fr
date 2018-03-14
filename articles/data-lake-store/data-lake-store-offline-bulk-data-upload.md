---
title: "Charger de grandes quantités de données sur Data Lake Store à l’aide de méthodes hors connexion | Microsoft Docs"
description: "Utiliser l’outil AdlCopy pour copier les données d’objets blob Stockage Azure vers Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 8dd20d0cf7f202b5d5fdeffb5848235e73eb9349
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Utiliser le service Azure Import/Export pour copier les données dans Data Lake Store hors connexion
Dans cet article, vous allez découvrir comment copier de grands jeux de données (> 200 Go) dans un Azure Data Lake Store à l’aide de méthodes de copie hors connexion, comme le [service Azure Import/Export](../storage/common/storage-import-export-service.md). Plus précisément, la taille du fichier utilisé comme exemple dans cet article est de 339 420 860 416 octets ou environ 319 Go sur disque. Appelons ce fichier 319GB.tsv.

Le service Azure Import/Export vous aide à transférer de façon plus sécurisée des volumes importants de données vers Stockage Blob Azure en expédiant des disques durs vers un centre de données Azure.

## <a name="prerequisites"></a>Prérequis
Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte de stockage Azure**.
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Préparation des données

Avant d’utiliser le service Import/Export, scindez le fichier de données à transférer **en copies de moins de 200 Go**. L’outil d’importation ne fonctionne pas avec des fichiers de plus de 200 Go. Dans ce didacticiel, nous fractionnons le fichier en blocs de 100 Go. Pour ce faire, utilisez [Cygwin](https://cygwin.com/install.html). Cygwin prend en charge les commandes Linux. Dans ce cas, utilisez la commande suivante :

    split -b 100m 319GB.tsv

L’opération split crée des fichiers portant les noms suivants.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Préparer les disques avec les données
Suivez les instructions relatives à [l’utilisation du Service Azure Import/Export](../storage/common/storage-import-export-service.md) (sous la section **Préparation des lecteurs**) pour préparer vos disques durs. Voici la séquence générale :

1. Procurez-vous un disque dur utilisable pour le service Import/Export Azure.
2. Identifiez un compte de stockage Azure où les données seront copiées une fois le disque expédié vers le centre de données Azure.
3. Utilisez [l’outil Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), qui est un utilitaire de ligne de commande. Voici un exemple d’extrait de code illustrant l’utilisation de l’outil.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Pour obtenir d’autres exemples d’extraits, consultez [Using the Azure Import/Export service](../storage/common/storage-import-export-service.md) (Utilisation du service Azure Import/Export).
4. La commande précédente crée un fichier journal à l’emplacement spécifié. Utilisez ce fichier journal pour créer un travail d’importation à partir du [portail Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Créer une tâche d’importation
Vous pouvez maintenant créer un travail d’importation en suivant les instructions relatives à [l’utilisation du Service Azure Import/Export](../storage/common/storage-import-export-service.md) (sous la section **Création de la tâche d’importation**). Pour ce travail d’importation, entre autres détails, indiquez le fichier journal créé durant la préparation des lecteurs de disque.

## <a name="physically-ship-the-disks"></a>Expédier les disques physiquement
Vous pouvez désormais expédier physiquement les disques vers un centre de données Azure. Les données y seront copiées dans les objets blob Stockage Azure que vous avez fournis durant la création du travail d’importation. En outre, pendant la création du travail, si vous avez choisi d’indiquer les informations de suivi plus tard, vous pouvez maintenant revenir à votre travail d’importation et mettre à jour le numéro de suivi.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copier des données d’objets blob Stockage Azure vers Azure Data Lake Store
Une fois que l’état du travail d’importation indique que celui-ci est terminé, vous pouvez vérifier si les données sont disponibles dans les objets blob Stockage Azure que vous aviez spécifiés. Vous pouvez ensuite utiliser différentes méthodes pour déplacer ces données depuis les objets blob vers Azure Data Lake Store. Pour connaître toutes les options disponibles pour le chargement de données, consultez [Réception de données dans Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Dans cette section, nous vous fournissons les définitions JSON grâce auxquelles vous pouvez créer un pipeline Azure Data Factory en vue de copier les données. Vous pouvez utiliser ces définitions JSON à partir du [portail Azure](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md) ou de [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Service lié source (objet blob Stockage Azure)
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Service lié cible (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Jeu de données d’entrée
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Jeu de données de sortie
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (activité de copie)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Pour plus d’informations, consultez [Move data from Azure Storage blob to Azure Data Lake Store using Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) (Déplacer des données à partir d’un objet blob Stockage Azure vers Azure Data Lake Store à l’aide d’Azure Data Factory).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstruire les fichiers de données dans Azure Data Lake Store
Nous avons commencé avec un fichier de 319 Go, que nous avons scindé en fichiers de plus petite taille afin de pouvoir le transférer à l’aide du service Azure Import/Export. Les données se trouvant désormais dans Azure Data Lake Store, nous pouvons reconstruire le fichier à sa taille d’origine. Pour ce faire, vous pouvez utiliser l’applet de commande Azure PowerShell suivante.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
