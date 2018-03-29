---
title: Copier des données de DB2 à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de DB2 vers des banques de données réceptrices prises en charge.
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
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: 7713e1b6a74fd099206804133d2dc8140fe83a8d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copier des données de DB2 à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-onprem-db2-connector.md)
> * [Version 2 - Préversion](connector-db2.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’une base de données DB2. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur DB2 dans V1](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données DB2 vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

En particulier, ce connecteur DB2 prend en charge les plateformes et versions IBM DB2 suivantes avec les versions 9, 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture) :

* IBM DB2 pour z/OS 11.1
* IBM DB2 pour z/OS 10.1
* IBM DB2 pour i 7.2
* IBM DB2 pour i 7.1
* IBM DB2 pour LUW 11
* IBM DB2 pour LUW 10.5
* IBM DB2 pour LUW 10.1

> [!TIP]
> Si vous recevez le message d’erreur « Le package correspondant à une requête d’exécution d’instruction SQL est introuvable. SQLSTATE = 51002 SQLCODE =-805 », la raison en est qu’un package nécessaire n’est pas créé pour un utilisateur normal sur un tel système d’exploitation. Suivez les instructions appropriées pour votre type de serveur DB2 :
> - DB2 pour i (AS400) : demandez à un utilisateur chevronné de créer une collection pour l’utilisateur de connexion avant d’utiliser une activité de copie. Commande : `create collection <username>`
> - DB2 pour z/OS ou LUW : utilisez un compte doté de privilèges élevés (utilisateur chevronné ou administrateur disposant d’autorités de package et d’autorisations BIND, BINDADD, GRANT EXECUTE TO PUBLIC) pour exécuter une fois l’activité de copie. Le package nécessaire est ensuite créé automatiquement au cours de la copie. Ensuite, vous pouvez revenir au mode utilisateur normal pour vos séries de copie suivantes.

## <a name="prerequisites"></a>Prérequis


Pour copier des données depuis/vers une base de données DB2 qui n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour en savoir plus sur les runtimes d’intégration auto-hébergés, voir l’article [Runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md). Le runtime d’intégration fournit un pilote DB2 intégré. Ainsi, vous n’avez pas besoin d’installer manuellement un pilote lors de la copie des données de DB2.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur DB2.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié DB2 sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Db2** | OUI |
| server |Nom du serveur DB2. |OUI |
| database |Nom de la base de données DB2. |OUI |
| authenticationType |Type d'authentification utilisé pour se connecter à la base de données DB2.<br/>Valeur autorisée : **De base**. |OUI |
| username |Spécifiez le nom d’utilisateur pour la connexion à la base de données DB2. |OUI |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
            "database": "<dbname>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données DB2.

Pour copier des données de DB2, affectez la valeur **RelationalTable** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **RelationalTable** | OUI |
| TableName | Nom de la table dans la base de données DB2. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source DB2.

### <a name="db2-as-source"></a>DB2 en tant que source

Pour copier des données de DB2, définissez **RelationalSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **RelationalSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Mappage de type de données pour DB2

Lors de la copie de données de DB2, les mappages suivants sont utilisés entre les types de données DB2 et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de base de données DB2 | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Chaîne |
| Clob |Chaîne |
| Date |DateTime |
| DB2DynArray |Chaîne |
| DbClob |Chaîne |
| Décimal |Décimal |
| DecimalFloat |Décimal |
| Double |Double |
| Float |Double |
| Graphic |Chaîne |
| Entier  |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Chaîne |
| LongVarGraphic |Chaîne |
| Chiffre |Décimal |
| Real |Single |
| SmallInt |Int16 |
| Temps |intervalle de temps |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |Chaîne |
| VarGraphic |Chaîne |
| xml |Byte[] |


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).