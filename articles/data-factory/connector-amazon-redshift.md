---
title: Copier des données d’Amazon Redshift à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données d’Amazon Redshift vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 7dc4af39a659a9c3c910c835265886b79ad5cf62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copier des données d’Amazon Redshift à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-amazon-redshift-connector.md)
> * [Version 2 - Préversion](connector-amazon-redshift.md)


Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’Amazon Redshift. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Amazon Redshift dans V1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données de Amazon Redshift dans tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Amazon Redshift prend en charge la récupération de données à partir de Redshift en utilisant une requête ou le support UNLOAD intégré de Redshift.

> [!TIP]
> Pour obtenir de meilleures performances lors de la copie de grandes quantités de données à partir de Redshift, utilisez le mécanisme Redshift intégré UNLOAD via Amazon S3. Consultez la section [Utiliser UNLOAD pour copier des données à partir d’Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) pour plus d’informations.

## <a name="prerequisites"></a>Prérequis


* Si vous copiez des données vers une banque de données locale à l’aide du [runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md), accordez au runtime d’intégration (en utilisant l’adresse IP de l’ordinateur) l’accès au cluster Amazon Redshift. Pour davantage d’instructions, consultez la rubrique [Authorize access to the cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autoriser l’accès au cluster).
* Si vous copiez des données vers une banque de données Azure, consultez la page [Plages IP des centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) pour connaître les plages d’adresses IP de calcul et SQL utilisées par les centres de données Azure.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Amazon Redshift.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Amazon Redshift sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **AmazonRedshift** | OUI |
| server |Nom d’hôte ou adresse IP du serveur Amazon Redshift. |OUI |
| port |Le numéro du port TCP utilisé par le serveur Amazon Redshift pour écouter les connexions clientes. |Non, la valeur par défaut est 5439 |
| database |Nom de la base de données Amazon Redshift. |OUI |
| username |Nom d’utilisateur ayant accès à la base de données. |OUI |
| password |Mot de passe du compte d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Amazon Redshift.

Pour copier des données d’Amazon Redshift, affectez la valeur **RelationalTable** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **RelationalTable** | OUI |
| TableName | Nom de la table dans Amazon Redshift. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Amazon Redshift en tant que source.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift en tant que source

Pour copier des données d’Amazon Redshift, définissez **AmazonRedshiftSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **AmazonRedshiftSource** | OUI |
| query |Utilise la requête personnalisée pour lire des données. |Chaîne de requête SQL. Par exemple : select * from MyTable. |Non (si « tableName » est spécifié dans dataset) |
| redshiftUnloadSettings | Groupe de propriétés lors de l’utilisation du mécanisme UNLOAD d’Amazon Redshift. | Non  |
| s3LinkedServiceName | Fait référence à un service Amazon S3 à utiliser comme magasin temporaire en spécifiant un nom de service lié de type « AmazonS3 ». | Oui, en cas d’utilisation de UNLOAD |
| bucketName | Indiquez le compartiment S3 pour stocker les données intermédiaires. S’il n’est pas spécifié, le service Data Factory le génère automatiquement.  | Oui, en cas d’utilisation de UNLOAD |

**Exemple : source Amazon Redshift dans une activité de copie utilisant UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Apprenez-en davantage sur l’utilisation de UNLOAD pour copier efficacement des données d’Amazon Redshift en lisant la section suivante.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Utiliser UNLOAD pour copier des données à partir d’Amazon Redshift

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) est un mécanisme fourni par Amazon Redshift qui peut décharger les résultats d’une requête dans un ou plusieurs fichiers sur Amazon Simple Storage Service (Amazon S3). C’est le moyen recommandé par Amazon pour copier un jeu de données volumineux à partir de Redshift.

**Exemple : copier des données d’Amazon Redshift vers Azure SQL Data Warehouse à l’aide de UNLOAD, d’une copie intermédiaire et de PolyBase**

Dans cet exemple de cas d’utilisation, l’activité de copie décharge les données d’Amazon Redshift sur Amazon S3, comme configuré dans « redshiftUnloadSettings », puis copie les données d’Amazon S3 dans un objet blob Azure, comme spécifié dans « stagingSettings », et enfin utilise PolyBase pour charger des données dans SQL Data Warehouse. Tous les formats intermédiaires sont gérés correctement par l’activité de copie.

![Flux de travail de copie de Redshift vers SQL DW](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mappage de type de données pour Amazon Redshift

Lors de la copie de données à partir d’Amazon Redshift, les mappages suivants sont utilisés entre les types de données Amazon Redshift et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données d’Amazon Redshift | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |Chaîne |
| CHAR |Chaîne |
| DATE |Datetime |
| DÉCIMAL |Décimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXTE |Chaîne |
| TIMESTAMP |Datetime |
| VARCHAR |Chaîne |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
