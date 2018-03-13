---
title: "Copier des données à partir de Teradata à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez le connecteur Teradata du service Data Factory, qui vous permet de copier des données d’une base de données Teradata vers des magasins de données pris en charge par Data Factory en tant que récepteurs."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 6af955b456a00fa90a9e49701fef6318e51bbd4b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copier des données à partir de Teradata à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-onprem-teradata-connector.md)
> * [Version 2 - Préversion](connector-teradata.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une base de données Teradata. Il s’appuie sur l’article de [vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Teradata dans V1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données Teradata vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Teradata prend en charge :

- Teradata **version 12 et ultérieures**
- Copie des données avec l’authentification **De base** ou **Windows**.

## <a name="prerequisites"></a>configuration requise

Pour utiliser ce connecteur Teradata, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer le [Fournisseur de données .NET pour Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) version 14 ou ultérieure sur l’ordinateur Integration Runtime.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Teradata.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Teradata :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **Teradata**. | OUI |
| server | Nom du serveur Teradata. | OUI |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données Teradata.<br/>Les valeurs autorisées sont **De base** et **Windows**. | OUI |
| username | Spécifiez le nom d’utilisateur associé à la connexion à la base de données Teradata. | OUI |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |OUI |

**Exemple :**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit une liste des propriétés prises en charge par le jeu de données Teradata.

Pour copier des données à partir de Teradata, affectez la valeur **RelationalTable** à la propriété de type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **RelationalTable** | OUI |
| TableName | Nom de la table dans la base de données Teradata. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple :**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source Teradata.

### <a name="teradata-as-source"></a>Teradata en tant que source

Pour copier des données à partir de Teradata, définissez **RelationalSource** comme type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **RelationalSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Mappage de type de données pour Teradata

Lors de la copie des données à partir de Teradata, les mappages suivants sont utilisés entre les types de données Teradata et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Teradata | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Chaîne |
| Clob |Chaîne |
| Date |Datetime |
| Décimal |Décimal |
| Double |Double |
| Graphic |Chaîne |
| Entier  |Int32 |
| Interval Day |intervalle de temps |
| Interval Day To Hour |intervalle de temps |
| Interval Day To Minute |intervalle de temps |
| Interval Day To Second |intervalle de temps |
| Interval Hour |intervalle de temps |
| Interval Hour To Minute |intervalle de temps |
| Interval Hour To Second |intervalle de temps |
| Interval Minute |intervalle de temps |
| Interval Minute To Second |intervalle de temps |
| Interval Month |Chaîne |
| Interval Second |intervalle de temps |
| Interval Year |Chaîne |
| Interval Year To Month |Chaîne |
| Number |Double |
| Period(Date) |Chaîne |
| Period(Time) |Chaîne |
| Period(Time With Time Zone) |Chaîne |
| Period(Timestamp) |Chaîne |
| Period(Timestamp With Time Zone) |Chaîne |
| SmallInt |Int16 |
| Temps |intervalle de temps |
| Time With Time Zone |Chaîne |
| Timestamp |Datetime |
| Timestamp With Time Zone |DatetimeOffset |
| VarByte |Byte[] |
| VarChar |Chaîne |
| VarGraphic |Chaîne |
| xml |Chaîne |


## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).