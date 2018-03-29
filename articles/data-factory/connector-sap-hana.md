---
title: Copier des données de SAP HANA à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de SAP HANA vers des banques de données réceptrices prises en charge.
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
ms.openlocfilehash: 96d9da90114ae392c3a9e1c22d8e993fd787ada8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copier des données de SAP HANA à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-sap-hana-connector.md)
> * [Version 2 - Préversion](connector-sap-hana.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’une base de données SAP HANA. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur SAP HANA dans V1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données SAP HANA vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SAP HANA prend en charge ce qui suit :

- Copie de données de toute version de base de données SAP HANA.
- Copie de données de **modèles d’informations HANA** (tels que les vues Analyse et Calcul) et de **tables Ligne/Colonne** à l’aide de requêtes SQL.
- Copie de données en utilisant une authentification **De base** ou **Windows**.

> [!NOTE]
> Pour copier des données **vers** une banque de données SAP HANA, utilisez le connecteur ODBC générique. Pour plus de détails, voir [Récepteur SAP HANA](connector-odbc.md#sap-hana-sink). Notez que les services liés pour les connecteurs SAP HANA et ODBC sont de types différents et qu’ils ne peuvent donc pas être réutilisées.

## <a name="prerequisites"></a>Prérequis


Pour utiliser ce connecteur SAP HANA, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer le pilote ODBC de SAP HANA sur l’ordinateur exécutant le runtime d’intégration. Vous pouvez télécharger le pilote ODBC SAP HANA à partir du [Centre de téléchargement de logiciels SAP](https://support.sap.com/swdc). Faites une recherche avec le mot-clé **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des détails sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur SAP HANA.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP HANA sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **SapHana** | OUI |
| server | Le nom du serveur sur lequel réside l’instance SAP HANA. Si votre serveur utilise un port personnalisé, spécifiez `server:port`. | OUI |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données SAP HANA.<br/>Valeurs autorisées : **De base** et **Windows** | OUI |
| userName | Nom de l’utilisateur ayant accès au serveur SAP. | OUI |
| password | Mot de passe pour l’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |OUI |

**Exemple :**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données SAP HANA.

Pour copier des données de SAP HANA, affectez la valeur **RelationalTable** à la propriété type du jeu de données. Aucune propriété propre à un type n’est prise en charge pour le type de jeu de données SAP HANA RelationalTable.

**Exemple :**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA en tant que source

Pour copier des données de SAP HANA, définissez **RelationalSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **RelationalSource** | OUI |
| query | Spécifie la requête SQL pour lire les données de l’instance SAP HANA. | OUI |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mappage de type de données pour SAP HANA

Lors de la copie de données de SAP HANA, les mappages suivants sont utilisés entre les types de données SAP HANA et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données SAP HANA | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| ALPHANUM | Chaîne |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | Datetime |
| DÉCIMAL | Décimal |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | Chaîne |
| REAL | Single |
| SECONDDATE | Datetime |
| SMALLINT | Int16 |
| TEMPS | intervalle de temps |
| TIMESTAMP | Datetime |
| TINYINT | Byte |
| VARCHAR | Chaîne |

## <a name="known-limitations"></a>Limites connues

Il existe quelques limitations connues lors de la copie des données à partir de SAP HANA :

- Les chaînes NVARCHAR sont tronquées à une longueur maximale de 4 000 caractères Unicode
- SMALLDECIMAL n’est pas pris en charge
- VARBINARY n’est pas pris en charge
- Les dates valides sont celles comprises entre 1899/12/30 et 9999/12/31


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).