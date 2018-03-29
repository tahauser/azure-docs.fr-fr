---
title: Copier des données de MongoDB à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de MongoDB vers des banques de données réceptrices prises en charge.
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
ms.date: 03/07/2018
ms.author: jingwang
ms.openlocfilehash: 3c1e5dbf60c247399b620a437da92a166990087e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Déplacer des données de MongoDB à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-on-premises-mongodb-connector.md)
> * [Version 2 - Préversion](connector-mongodb.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’une base de données MongoDB. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur MongoDB dans V1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données MongoDB vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur MongoDB prend en charge :

- MongoDB **versions 2.4, 2.6, 3.0 et 3.2**.
- Copie de données en utilisant une authentification **De base** ou **Anonyme**.

## <a name="prerequisites"></a>Prérequis


Pour copier des données depuis/vers une base de données MongoDB qui n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour plus d’informations, voir l’article [Runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md). Le runtime d’intégration fournit un pilote MongoDB intégré, ce qui évite d’avoir à en installer un manuellement lors de la copie des données issues de MongoDB.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur MongoDB.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié MongoDB sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type |La propriété type doit être définie sur **MongoDb** |OUI |
| server |Nom d’hôte ou adresse IP du serveur MongoDB. |OUI |
| port |Le port TCP utilisé par le serveur MongoDB pour écouter les connexions clientes. |Non (valeur par défaut est 27017) |
| databaseName |Nom de la base de données MongoDB à laquelle vous souhaitez accéder. |OUI |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données MongoDB.<br/>Valeurs autorisées : **De base** et **Anonyme**. |OUI |
| username |Compte d’utilisateur pour accéder à MongoDB. |Oui (si l’authentification de base est utilisée). |
| password |Mot de passe pour l’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui (si l’authentification de base est utilisée). |
| authSource |Nom de la base de données MongoDB que vous souhaitez utiliser pour vérifier vos informations d’identification pour l’authentification. |Non. Par défaut, l’authentification de base utilise le compte d’administrateur et la base de données spécifiés à l’aide de la propriété databaseName. |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données MongoDB.

Pour copier des données depuis MongoDB, affectez la valeur **MongoDbCollection** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **MongoDbCollection** | OUI |
| collectionName |Nom de la collection dans la base de données MongoDB. |OUI |

**Exemple :**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }

```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source MongoDB.

### <a name="mongodb-as-source"></a>MongoDB en tant que source

Pour copier des données de MongoDB, définissez le type de source dans l’activité de copie sur **MongoDbSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **MongoDbSource** | OUI |
| query |Utiliser la requête SQL-92 personnalisée pour lire les données. Par exemple : select * from MyTable. |Non (si « collectionName » est spécifié dans le jeu de données) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Lorsque vous spécifiez une requête SQL, soyez attentif au format DateTime. Par exemple : `SELECT * FROM Account WHERE LastModifiedDate >= {{ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')}'}} AND LastModifiedDate < {{ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}'}}`

## <a name="schema-by-data-factory"></a>Schéma par Data Factory

Le service Azure Data Factory déduit le schéma à partir d’une collection MongoDB à l’aide des **100 derniers documents** dans la collection. Si ces 100 documents ne contiennent pas de schéma complet, certaines colonnes peuvent être ignorées lors de l’opération de copie.

## <a name="data-type-mapping-for-mongodb"></a>Mappage de type de données pour MongoDB

Lors de la copie de données de MongoDB, les mappages suivants sont utilisés entre les types de données MongoDB et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données MongoDB | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| Binary |Byte[] |
| Booléen |Booléen |
| Date |Datetime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Chaîne |
| Chaîne |Chaîne |
| UUID |Guid |
| Object |Renormalisé en colonnes aplaties avec « _ » comme séparateur imbriqué |

> [!NOTE]
> Pour en savoir plus sur la prise en charge des tableaux à l’aide de tables virtuelles, reportez-vous à la section [Prise en charge des types complexes à l’aide de tables virtuelles](#support-for-complex-types-using-virtual-tables).
>
> Actuellement, les types de données MongoDB suivants ne sont pas pris en charge : DBPointer, JavaScript, clé max./min., expression régulière, symbole, horodatage, non définie.

## <a name="support-for-complex-types-using-virtual-tables"></a>Prise en charge des types complexes à l’aide de tables virtuelles

Azure Data Factory utilise un pilote ODBC intégré pour assurer la connexion à votre base de données MongoDB et copier des données à partir de cette dernière. Pour les types complexes tels que des tableaux ou des objets avec des types différents entre les documents, le pilote normalise de nouveau les données dans les tables virtuelles correspondantes. En particulier, si une table contient de telles colonnes, le pilote génère les tables virtuelles suivantes :

* Une **table de base**, qui contient les mêmes données que la table réelle, à l’exception des colonnes de type complexe. La table de base utilise le même nom que la table réelle qu’elle représente.
* Une **table virtuelle** pour chaque colonne de type complexe, qui étend les données imbriquées. Le nom des tables virtuelles est composé du nom de la table réelle, d’un séparateur « _ » et du nom du tableau ou de l’objet.

Les tables virtuelles font référence aux données présentées dans la table réelle, de manière à permettre au pilote d’accéder aux données dénormalisées. Vous pouvez accéder au contenu des tableaux MongoDB en interrogeant et en joignant les tables virtuelles.

### <a name="example"></a>Exemples

Par exemple, « ExampleTable » est ici une table MongoDB qui dispose d’une colonne avec un tableau d’objets dans chaque cellule (Factures) et d’une colonne avec un tableau de types scalaires (Évaluations).

| _id | Nom du client | Factures | Niveau de service | Évaluations |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |Gold |[1,2] |

Le pilote génère plusieurs tables virtuelles pour représenter cette table. La première table virtuelle est la table de base nommée « ExampleTable » présentée dans l’exemple. La table de base contient toutes les données de la table d’origine, mais les données dans les tableaux ont été omises et sont développées dans les tables virtuelles.

| _id | Nom du client | Niveau de service |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

Les tables suivantes montrent les tables virtuelles qui représentent les tableaux d’origine dans l’exemple. Ces tables contiennent les éléments suivants :

* Une référence à la colonne de clé primaire d’origine correspondant à la ligne du tableau d’origine (via la colonne _id)
* Une indication de la position des données dans le tableau d’origine
* Les données développées pour chaque élément du tableau

**Table « ExampleTable_Invoices » :**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Remise |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |grille-pain |456 |0.2 |
| 1111 |1 |124 |four |1235 |0.2 |
| 2222 |0 |135 |réfrigérateur |12543 |0.0 |

**Table « ExampleTable_Ratings » :**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5. |
| 1111 |1 |6. |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).