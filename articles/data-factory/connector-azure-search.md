---
title: "Copier des données vers l’index de recherche à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour envoyer ou copier des données vers un index de recherche Azure."
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
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: 2b6219dc509b1af8f196f056b489a31fa331acaf
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Copier des données vers un index de recherche Azure à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-search-connector.md)
> * [Version 2 - Préversion](connector-azure-search.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers un index de recherche Azure. Il s’appuie sur l’article [présentant une vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Recherche Azure dans V1](v1/data-factory-azure-search-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de toute banque de données source prise en charge dans l’index de recherche Azure. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Recherche Azure.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Recherche Azure sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **AzureSearch** | Oui |
| URL | URL du service Recherche Azure. | Oui |
| key | Clé d’administration du service Recherche Azure. Marquez ce champ comme SecureString. | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

> [!IMPORTANT]
> Lors de la copie de données d’une banque de données cloud vers l’index de recherche Azure dans un service lié Azure Search, vous devez référencer un runtime d’intégration Azure avec une région explicite dans connactVia. Définissez la région comme celle dans laquelle votre Recherche Azure réside. En savoir plus dans [Integration Runtime Azure](concepts-integration-runtime.md#azure-integration-runtime).

**Exemple :**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données de Recherche Azure.

Pour copier des données vers Recherche Azure, affectez la valeur **RelationalTable** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzureSearchIndex** | Oui |
| indexName | Nom de l’index Recherche Azure. Data Factory ne crée pas l’index. L’index doit exister dans Recherche Azure. | Oui |

**Exemple :**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Recherche Azure.

### <a name="azure-search-as-sink"></a>Azure Search en tant que récepteur

Pour copier des données vers Recherche Azure, définissez **AzureSearchIndexSink** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **AzureSearchIndexSink** | Oui |
| writeBehavior | Indique s’il convient de procéder à une fusion ou à un remplacement lorsqu’un document existe déjà dans l’index. Voir la [propriété WriteBehavior](#writebehavior-property).<br/><br/>Valeurs autorisées : **Merge** (par défaut) et **Upload**. | Non |
| writeBatchSize | Charge des données dans l’index Recherche Azure lorsque la taille du tampon atteint writeBatchSize. Pour plus d’informations, voir la [propriété WriteBatchSize](#writebatchsize-property).<br/><br/>Valeurs autorisées : entier de 1 à 1000 ; la valeur par défaut est 1000. | Non |

### <a name="writebehavior-property"></a>Propriété WriteBehavior

AzureSearchSink effectue une opération d’upsert lors de l’écriture des données. En d’autres termes, lorsque vous écrivez un document, si la clé du document existe déjà dans l’index Recherche Azure, Recherche Azure met à jour le document existant au lieu de lever une exception de conflit.

AzureSearchSink fournit les deux comportements upsert suivants (en utilisant le Kit de développement logiciel (SDK) AzureSearch) :

- **Fusion** : combine toutes les colonnes du nouveau document avec celles du document existant. Pour les colonnes ayant une valeur Null dans le nouveau document, la valeur de celles du document existant est conservée.
- **Chargement** : le nouveau document remplace l’ancien. Pour les colonnes qui ne sont pas spécifiées dans le nouveau document, la valeur est définie sur Null qu’il existe ou non une valeur autre que Null dans le document existant.

**Fusion** est le comportement par défaut.

### <a name="writebatchsize-property"></a>Propriété WriteBatchSize

Le service Recherche Azure prend en charge l’écriture de documents sous forme d’un lot. Un lot peut contenir 1 à 1 000 actions. Une action gère un document pour effectuer l’opération de chargement/fusion.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Prise en charge des types de données

Le tableau suivant indique si un type de données Recherche Azure est pris en charge ou non.

| Type de données Recherche Azure | Pris en charge dans le récepteur de l’index Recherche Azure |
| ---------------------- | ------------------------------ |
| String | O |
| Int32 | O |
| Int64 | O |
| Double | O |
| Booléen | O |
| DataTimeOffset | O |
| Tableau de chaînes | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
