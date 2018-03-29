---
title: Copier des données depuis/vers Azure Cosmos DB à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Azure Cosmos DB (ou) de Cosmos DB vers des banques de données réceptrices prises en charge.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 01de1020cd485b3cf91e5d5bc2e175df2bcad111
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Copier des données depuis/vers Azure Cosmos DB à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-documentdb-connector.md)
> * [Version 2 - Préversion](connector-azure-cosmos-db.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de/vers Azure Cosmos DB (API SQL). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Azure Cosmos DB dans V1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’Azure Cosmos DB vers toute banque de données réceptrice prise en charge, ou copier des données de toute banque de données source prise en charge vers Azure Cosmos DB. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure Cosmos DB prend en charge ce qui suit :

- [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) Cosmos DB.
- Importation/exportation de documents JSON en l'état, ou copie de données depuis/vers un jeu de données tabulaire, par exemple, une base de données SQL, des fichiers CSV, etc.

Pour copier des documents en l’état à partir/à destination de fichiers JSON ou d’une autre collection Cosmos DB, voir [Importer/Exporter des documents JSON](#importexport-json-documents).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir des entités Data Factory spécifiques d’Azure Cosmos DB.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure Cosmos DB sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **CosmosDb**. | OUI |
| connectionString |Spécifiez les informations requises pour se connecter à la base de données Azure Cosmos DB. Notez que vous devez spécifier des informations de base de données dans la chaîne de connexion comme dans l’exemple ci-dessous. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Cosmos DB.

Pour copier des données depuis/vers Azure Cosmos DB, définissez la propriété type du jeu de données sur **DocumentDbCollection**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **DocumentDbCollection** |OUI |
| collectionName |Nom de la collection de documents Cosmos DB. |OUI |

**Exemple :**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma par Data Factory

Pour les banques de données sans schéma telles qu’Azure Cosmos DB, l’activité de copie déduit le schéma de l’une des manières suivantes. Par conséquent, sauf si vous souhaitez [importer/exporter des documents JSON en l'état](#importexport-json-documents), la meilleure pratique consiste à spécifier la structure des données dans la section **structure**.

1. Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory respecte cette structure en tant que schéma. Dans ce cas, si une ligne ne contient pas de valeur pour une colonne, une valeur null est fournie pour celle-ci.
2. Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes ne seront pas incluses dans le résultat de l’opération de copie.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Cosmos DB en tant que source et récepteur.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB en tant que source

Pour copier des données d’Azure Cosmos DB, définissez le type de source dans l’activité de copie sur **DocumentDbCollectionSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **DocumentDbCollectionSource** |OUI |
| query |Spécifiez la requête Cosmos DB pour lire les données.<br/><br/>Exemple : `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Non  <br/><br/>Si non spécifié, l’instruction SQL exécutée : `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractère spécial pour indiquer que le document est imbriqué et comment aplatir jeu de résultats.<br/><br/>Par exemple, si une requête Azure Cosmos DB retourne un résultat imbriqué `"Name": {"First": "John"}`, l’activité de copie identifie le nom de colonne en tant que « Nom.Prénom » avec la valeur « John » lorsque le séparateur (nestedSeparator) est un point. |Non (la valeur par défaut est un point `.`) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB en tant que récepteur

Pour copier des données dans Azure Cosmos DB, définissez le type de récepteur dans l’activité de copie sur **DocumentDbCollectionSink**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **DocumentDbCollectionSink**. |OUI |
| nestingSeparator |Caractère spécial dans le nom de colonne source pour indiquer que le document imbriqué est nécessaire. <br/><br/>Par exemple, `Name.First` dans la structure du jeu de données de sortie génère la structure JSON suivante dans le document Cosmos DB :`"Name": {"First": "[value maps to this column from source]"}` lorsque le séparateur nestedSeparator est un point. |Non (la valeur par défaut est un point `.`) |
| writeBatchTimeout |Temps d'attente pour que l'opération soit terminée avant d'expirer.<br/><br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importation/exportation de documents JSON

À l’aide de ce connecteur Cosmos DB, vous pouvez facilement :

* Importer des documents JSON de différentes sources dans Cosmos DB, notamment Stockage Blob Azure, Azure Data Lake Store ou d’autres banques de données basées sur des fichiers prises en charge par Azure Data Factory.
* Exporter des documents JSON d’une collection Cosmos DB vers différentes banques basées sur des fichiers.
* Copier des documents entre deux collections Cosmos DB en l’état.

Pour obtenir une copie de ce type, indépendante du schéma :

- Dans les jeux de données Cosmos DB, ne spécifiez pas la section « structure ». Et, dans la source ou le récepteur Cosmos DB de l’activité de copie, ne spécifiez pas de propriété « nestingSeparator ».
- Lors de l’importation à partir de/exportation à destination de fichiers JSON, dans le jeu de données du magasin de fichiers correspondant, spécifiez le type de format comme « JsonFormat » et la configuration « filePattern » correctement ( pour plus de détails, voir [format JSON](supported-file-formats-and-compression-codecs.md#json-format)), ne spécifiez pas la section « structure », et ignorez les paramètres de format rest.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
