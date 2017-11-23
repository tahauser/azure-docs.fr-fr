---
title: "Copie de données d’Amazon Simple Storage Service à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory pour copier des données d’Amazon Simple Storage Service (S3) vers des banques de données réceptrices prises en charge."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 2bfc59931bbbb08ad1cd0328cf624b7350f92ec0
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copie de données d’Amazon Simple Storage Service à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 - Préversion](connector-amazon-simple-storage-service.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Stockage Blob Azure. Il s’appuie sur l’article [présentant une vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Amazon S3 dans V1](v1/data-factory-amazon-simple-storage-service-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de toute banque de données source prise en charge vers Azure Data Lake Store, ou d’Azure Data Lake Store vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus spécifiquement, ce connecteur Amazon S3 prend en charge la copie de fichiers en l'état ou l’analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="required-permissions"></a>Autorisations requises

Pour copier des données à partir d’Amazon S3, assurez-vous que vous disposez des autorisations suivantes :

- `s3:GetObject` et `s3:GetObjectVersion` pour les opérations d’objet Amazon S3 ;
- `s3:ListBucket` pour les opérations de compartiment Amazon S3. Si vous utilisez l’Assistant de copie de Data Factory, l’élément `s3:ListAllMyBuckets` est également requis.

Pour obtenir la liste complète des autorisations Amazon S3, consultez l’article [Spécification des autorisations d’une stratégie](http://docs.aws.amazon.com/amazons3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie. 

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques d’Amazon S3.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Amazon S3 prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AmazonS3**. | Oui |
| accessKeyId | ID de la clé d’accès secrète. |Oui |
| secretAccessKey | La clé d’accès secrète elle-même. Marquez ce champ comme SecureString. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

>[!NOTE]
>Ce connecteur nécessite des clés d’accès pour le compte IAM pour copier des données à partir d’Amazon S3. Les [informations d’identification de sécurité temporaires](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) ne sont pas prises en charge.
>

Voici un exemple :

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Amazon S3.

Pour copier des données d’Amazon S3, affectez la valeur **AmazonS3Object** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AmazonS3Object** |Oui |
| bucketName | Le nom de compartiment S3. |Oui |
| key | La clé d’objet S3. S’applique uniquement lorsque le préfixe n’est pas spécifié. |Non |
| prefix | Préfixe de la clé d’objet S3. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la clé n’est pas spécifiée. |Non |
| version | La version de l’objet S3 si le contrôle de version S3 est activé. |Non |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers d’un format spécifique, les types de formats de fichier pris en charge sont les suivants : **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non |

> [!NOTE]
> **bucketName + key** spécifient l’emplacement de l’objet S3 où le compartiment est le conteneur racine pour les objets S3 et la clé est le chemin d’accès complet à l’objet S3.

**Exemple : utilisation de préfixe**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Exemple : utilisation de clé et version (facultative)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Data Lake en tant que source et récepteur.

### <a name="amazon-s3-as-source"></a>Amazon S3 en tant que source

Pour copier des données d’Amazon S3, définissez **FileSystemSource** (qui inclut Amazon S3) comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **FileSystemSource** |Oui |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).