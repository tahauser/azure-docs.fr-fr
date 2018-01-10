---
title: "Copier des données de/vers SAP Cloud for Customer avec Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Data Factory pour copier des données de SAP Cloud for Customer vers des magasins de données récepteurs pris en charge (ou) de magasins de données sources pris en charge vers SAP Cloud for Customer."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ad3bc7ba38f5ea20586031bdcc3ae44f03f9da0b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copier des données de SAP Cloud for Customer (C4C) avec Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de/vers SAP Cloud for Customer (C4C). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de SAP Cloud for Customer vers n’importe quel magasin de données récepteur pris en charge, ou de n’importe quel magasin de données source pris en charge vers SAP Cloud for Customer. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur permet à Azure Data Factory de copier des données de/vers SAP Cloud for Customer, notamment les solutions SAP Cloud for Sales, SAP Cloud for Service et SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Prise en main

Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes donnent des précisions sur les propriétés utilisées pour définir des entités Data Factory propres au connecteur SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié SAP Cloud for Customer :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SapCloudForCustomer**. | Oui |
| url | URL de l’instance SAP C4C OData. | Oui |
| username | Indiquez le nom d'utilisateur à utiliser pour se connecter à SAP C4C. | Oui |
| password | Indiquez le mot de passe du compte d’utilisateur défini pour username. Marquez ce champ comme SecureString. | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour Source, Oui pour Récepteur |

>[!IMPORTANT]
>Pour copier des données dans SAP Cloud for Customer, vous devez [créer explicitement un runtime Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) à un emplacement proche de SAP Cloud for Customer et l’associer au service lié, comme dans l’exemple suivant :

**Exemple :**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données SAP Cloud for Customer.

Pour copier des données de SAP Cloud for Customer, affectez la valeur **SapCloudForCustomerResource** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **SapCloudForCustomerResource**. |Oui |
| path | Indiquez le chemin d’accès de l’entité SAP C4C OData. |Oui |

**Exemple :**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par la source SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C comme source

Pour copier des données de SAP Cloud for Customer, affectez la valeur **SapCloudForCustomerSource** au type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SapCloudForCustomerSource**.  | Oui |
| query | Indiquez la requête OData personnalisée permettant de lire les données. | Non |

Exemple de requête permettant d’obtenir des données pour un jour en particulier : `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SAPC4CSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C comme récepteur

Pour copier des données vers SAP Cloud for Customer, affectez la valeur **SapCloudForCustomerSink** au type récepteur de l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SapCloudForCustomerSink**.  | Oui |
| writeBehavior | Comportement d’écriture de l’opération. Valeurs possibles : « Insert », « Update ». | Non. Valeur par défaut : « Insert ». |
| writeBatchSize | Taille de lot de l’opération d’écriture. La taille de lot offrant les meilleures performances peut être différente selon les tables et les serveurs. | Non. Valeur par défaut : 10. |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mappage des types de données pour SAP Cloud for Customer

Lors de la copie de données de SAP Cloud for Customer, les mappages suivants sont utilisés pour passer des types de données SAP Cloud for Customer aux types de données intermédiaires Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données SAP C4C OData | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Décimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | intervalle de temps |
| Edm.DateTimeOffset | Datetimeoffset |


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
