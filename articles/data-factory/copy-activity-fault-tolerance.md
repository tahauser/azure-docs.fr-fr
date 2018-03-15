---
title: "Tolérance de panne de l’activité de copie dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment ajouter une tolérance de panne à l’activité de copie dans Azure Data Factory en ignorant les lignes incompatibles."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: b7ca3f6da104da16bd64db042a2a13f593a393b6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolérance de panne de l’activité de copie dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 : disponibilité générale](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Version 2 - Préversion](copy-activity-fault-tolerance.md)

Avec l’activité de copie dans Azure Data Factory, vous avez deux moyens de traiter les lignes incompatibles lors de la copie de données entre les magasins de données source et récepteur :

- Vous pouvez abandonner et laisser en échec l’activité de copie en cas de détection de données incompatibles (comportement par défaut).
- Vous pouvez continuer à copier toutes les données en ajoutant une tolérance de panne et en ignorant les lignes de données incompatibles. Vous avez également la possibilité de journaliser les lignes incompatibles dans le stockage Blob Azure ou Azure Data Lake Store. afin de pouvoir examiner la cause de l’échec dans le journal, corriger les données sur la source de données, puis effectuer une nouvelle tentative de copie.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez [Tolérance de panne de l’activité de copie dans V1](v1/data-factory-copy-activity-fault-tolerance.md).


 ## <a name="supported-scenarios"></a>Scénarios pris en charge
L’activité de copie offre la possibilité de détecter, d’ignorer et de journaliser les incompatibilités de données suivantes :

- **Incompatibilité entre le type de données sources et le type natif récepteur** 

    Exemple : copie de données d’un fichier CSV du stockage Blob dans une base de données SQL avec une définition de schéma contenant trois colonnes de type INT. Les lignes du fichier CSV qui contiennent des données numériques, telles que 123 456 789, sont correctement copiées dans le magasin récepteur. En revanche, les lignes qui contiennent des valeurs non numériques, telles que 123 456, abc, sont considérées comme incompatibles et ignorées.

- **Incompatibilité du nombre de colonnes entre la source et le récepteur**

    Exemple : copie de données d’un fichier CSV du stockage Blob Azure dans une base de données SQL avec une définition de schéma contenant six colonnes. Les lignes du fichier CSV qui contiennent six colonnes sont correctement copiées dans le magasin récepteur. Les lignes du fichier CSV qui contiennent plus ou moins de six colonnes sont considérées comme incompatibles et ignorées.

- **Violation de clé primaire durant l’écriture dans une base de données relationnelle**

    Exemple : copie de données depuis un serveur SQL dans une base de données SQL. Il existe une clé primaire définie dans la base de données SQL réceptrice, mais aucune clé primaire correspondante n’est définie dans le serveur SQL source. Les lignes en double qui peuvent exister dans la source ne sont pas copiées dans le récepteur. L’activité de copie ne copie que la première ligne des données sources dans le récepteur. Toutes les lignes sources suivantes contenant une valeur de clé primaire en double sont considérées comme incompatibles et ignorées.

>[!NOTE]
>Cette fonctionnalité ne s’applique pas quand l’activité de copie est configurée pour appeler un mécanisme de chargement de données externes, comme [Azure SQL Data Warehouse PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift). Pour charger des données dans SQL Data Warehouse avec PolyBase, utilisez la prise en charge native de la tolérance de panne de PolyBase en spécifiant « [polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink) » dans l’activité de copie.

## <a name="configuration"></a>Configuration
L’exemple suivant fournit une définition JSON pour configurer la manière d’ignorer les lignes incompatibles dans le cadre de l’activité de copie :

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Indique s’il faut ignorer ou non les lignes incompatibles durant la copie. | True<br/>False (valeur par défaut) | Non 
redirectIncompatibleRowSettings | Groupe de propriétés qui peuvent être spécifiées lorsque vous souhaitez journaliser les lignes incompatibles. | &nbsp; | Non 
linkedServiceName | Service lié de [Stockage Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) pour stocker le journal contenant les lignes ignorées. | Nom d’un service lié de type `AzureStorage` ou `AzureDataLakeStore` faisant référence à l’instance que vous souhaitez utiliser pour stocker le fichier journal. | Non 
chemin d’accès | Chemin d’accès du fichier journal contenant les lignes ignorées. | Spécifiez le chemin que vous souhaitez utiliser pour journaliser les données incompatibles. Si vous ne spécifiez pas le chemin d’accès, le service crée un conteneur à votre place. | Non 

## <a name="monitor-skipped-rows"></a>Effectuer le monitoring des lignes ignorées
Une fois l’activité de copie exécutée, vous pouvez voir le nombre de lignes ignorées dans la sortie de l’activité de copie :

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Si vous effectuez la configuration de manière à enregistrer les lignes incompatibles, vous pouvez trouver le fichier journal sous le chemin d’accès suivant : `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Les fichiers journaux peuvent être uniquement les fichiers csv. Les données d’origine ignorées seront enregistrées avec la virgule comme délimiteur de colonne si nécessaire. Nous ajoutons deux colonnes supplémentaires « ErrorCode » et « ErrorMessage » en plus des données sources d’origine dans le fichier journal, où vous pouvez voir la cause racine de l’incompatibilité. Les valeurs ErrorCode et ErrorMessage figurent entre guillemets doubles. 

Voici un exemple de contenu de fichier journal :

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Étapes suivantes
Voir les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Performances de l’activité de copie](copy-activity-performance.md)


