---
title: "Copier des données à partir de Sybase à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier des données de Sybase vers des magasins de données récepteurs pris en charge à l’aide d’une activité de copie dans un pipeline Azure Data Factory."
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
ms.openlocfilehash: f0fe8f65ff2d6a3029e44b51c004404e336e0129
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Copier des données à partir de Sybase à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-onprem-sybase-connector.md)
> * [Version 2 - Préversion](connector-sybase.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une base de données Sybase. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Sybase dans V1](v1/data-factory-onprem-sybase-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données Sybase vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Sybase prend en charge :

- SAP Sybase SQL Anywhere (ASA) **version 16 et versions ultérieures** ; IQ et ASE ne sont pas pris en charge.
- Copie des données avec l’authentification **De base** ou **Windows**.

## <a name="prerequisites"></a>configuration requise

Pour utiliser ce connecteur Sybase, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer le [fournisseur de données pour Sybase iAnywhere.Data.SQLAnywhere](http://go.microsoft.com/fwlink/?linkid=324846) 16 ou version ultérieure sur l’ordinateur Runtime d’intégration.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Sybase.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Sybase :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **Sybase**. | OUI |
| server | Nom du serveur Sybase. |OUI |
| database | Nom de la base de données Sybase. |OUI |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données Sybase.<br/>Les valeurs autorisées sont **De base** et **Windows**. |OUI |
| username | Spécifiez le nom d’utilisateur associé à la connexion à la base de données Sybase. |OUI |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |OUI |

**Exemple :**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit une liste des propriétés prises en charge par le jeu de données Sybase.

Pour copier des données à partir de Sybase, affectez la valeur **RelationalTable** à la propriété de type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **RelationalTable** | OUI |
| TableName | Nom de la table dans la base de données Sybase. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source Sybase.

### <a name="sybase-as-source"></a>Sybase en tant que source

Pour copier des données à partir de Sybase, définissez **RelationalSource** comme type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **RelationalSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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

## <a name="data-type-mapping-for-sybase"></a>Mappage de type de données pour Sybase

Lors de la copie des données à partir de Sybase, les mappages suivants sont utilisés entre les types de données Sybase et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma source et le type de données au récepteur, consultez [Mappages de types de données et de schémas](copy-activity-schema-and-type-mapping.md).

Sybase prend en charge les types T-SQL. Pour obtenir une table de mappage entre les types SQL et les types de données intermédiaires d’Azure Data Factory, consultez la section [Connecteur Azure SQL Database - Mappage de type de données](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).