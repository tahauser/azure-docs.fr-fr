---
title: "Copier des données depuis/vers Stockage Table Azure à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Stockage Table Azure (ou) ou de Stockage Table Azure vers des banques de données réceptrices prises en charge."
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
ms.date: 11/07/2017
ms.author: jingwang
ms.openlocfilehash: a80a947f5dc6176aaa6334a10eabf1a2b4be5847
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Copier des données depuis/vers Stockage Table Azure à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-table-connector.md)
> * [Version 2 - Préversion](connector-azure-table-storage.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Stockage Table Azure. Il s’appuie sur l’article [présentant une vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Stockage Table Azure dans V1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de toute banque de données source prise en charge vers le Stockage Table Azure, ou depuis Stockage Table Azure vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Table Azure prend en charge la copie de données à l’aide des authentifications par **clé de compte** et par **SAP de service** (signature d'accès partagé).

## <a name="get-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques de Stockage Table Azure.

## <a name="linked-service-properties"></a>Propriétés du service lié

### <a name="using-account-key"></a>Utilisation d’une clé de compte

Vous pouvez créer un service lié Stockage Azure à l’aide de la clé de compte qui fournit à la fabrique de données un accès global au Stockage Azure. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur : **AzureStorage** |Oui |
| connectionString | Spécifier les informations requises pour la connexion au stockage Azure pour la propriété connectionString. Marquez ce champ comme SecureString. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Utilisation d’une authentification par SAP de service

Vous pouvez également créer un service lié Stockage Azure en utilisant une signature d'accès partagé (SAP) qui fournit à la fabrique de données un accès restreint/limité dans le temps à toutes les ressources ou à des ressources spécifiques dans le stockage.

Une signature d’accès partagé (SAP) fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations, sans partager les clés d’accès de votre compte. La SAP est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d'accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié. Pour plus d’informations sur SAP, consultez [Signatures d’accès partagé : présentation du modèle SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory prend maintenant uniquement en charge la **signature d’accès partagé SAS**, et pas la signature d’accès partagé du compte. Pour plus d’informations sur ces deux types et leur construction, consultez [Types de signatures d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) . L’URL de SAP qu’il est possible de générer à partir du portail Azure ou de l’Explorateur de stockage est une signature d’accès partagé de compte qui n’est pas prise en charge.
>

Pour utiliser l’authentification par SAP de service, les propriétés suivantes sont prises en charge :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur : **AzureStorage** |Oui |
| sasUri | Spécifiez l’URI de signature d’accès partagé des ressources Stockage Azure, telles qu’un objet blob, un conteneur ou une table. Marquez ce champ comme SecureString. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Quand vous créez un **URI SAP**, prenez en compte les points suivants :

- Définissez des **autorisations** de lecture/écriture appropriées sur les objets en fonction de l’utilisation du service lié (lecture, écriture, lecture/écriture) dans votre fabrique de données.
- Définissez le paramètre **Heure d’expiration** correctement. Assurez-vous que l’accès aux objets Azure Storage n’expire pas pendant la période active du pipeline.
- L’URI doit être créé au niveau table approprié en fonction des besoins.

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données de l’objet Table Azure.

Pour copier des données depuis/vers un objet Table Azure, définissez la propriété type du jeu de données sur **AzureTable** . Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzureTable**  |Oui |
| TableName |Nom de la table dans l'instance de base de données Table Azure à laquelle le service lié fait référence. |Oui |

**Exemple :**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma par Data Factory

Pour les magasins de données sans schéma comme Azure Table, le service Data Factory déduit le schéma de l’une des manières suivantes :

1. Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory respecte cette structure en tant que schéma. Dans ce cas, si une ligne ne contient pas de valeur pour une colonne, une valeur null est fournie pour celle-ci.
2. Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes ne sont pas incluses dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure pratique consiste à définir la structure des données à l’aide de la propriété **structure** .

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Table Azure en tant que source et récepteur.

### <a name="azure-table-as-source"></a>Table Azure en tant que source

Pour copier des données de Table Azure, définissez **AzureTableSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **AzureTableSource** |Oui |
| AzureTableSourceQuery |La requête de table Azure personnalisée permet de lire les données. Consultez les exemples dans la section suivante. |Non |
| azureTableSourceIgnoreTableNotFound |Indiquer si l'exception de la table n'existe pas.<br/>Valeurs autorisées : **True** et **False** (par défaut). |Non |

### <a name="azuretablesourcequery-examples"></a>Exemples azureTableSourceQuery

Si la colonne de table Azure est de type datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Si la colonne de table Azure est de type chaîne :

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Si vous utilisez le paramètre de pipeline, effectuez un cast de la valeur datetime au format approprié en fonction des exemples ci-dessus.

### <a name="azure-table-as-sink"></a>Table Azure en tant que récepteur

Pour copier des données vers la Table Azure, définissez le type de récepteur sur **AzureTableSink** dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type de la source d’activité de copie doit être définie sur **AzureTableSink**. |Oui |
| azureTableDefaultPartitionKeyValue |Valeur de clé de partition par défaut qui peut être utilisée par le récepteur. |Non |
| azureTablePartitionKeyName |Spécifiez le nom de la colonne dont les valeurs sont utilisées comme clés de partition. Si aucune valeur n'est spécifiée, « AzureTableDefaultPartitionKeyValue » est utilisée comme clé de partition. |Non |
| azureTableRowKeyName |Spécifiez le nom de la colonne dont les valeurs sont utilisées comme clé de ligne. Si aucune valeur n'est spécifiée, un GUID est utilisé pour chaque ligne. |Non |
| azureTableInsertType |Le mode d’insertion des données dans une table Azure. Cette propriété détermine le remplacement ou la fusion des valeurs des lignes existantes dans la table de sortie avec des clés de partition et de ligne correspondantes. <br/><br/>Valeurs autorisées : **merge** (par défaut), et **replace**. <br/><br> Ce paramètre s’applique au niveau de la ligne, non au niveau de la table, et aucune option ne supprime des lignes de la table de sortie qui n’existent pas dans l’entrée. Consultez [Insertion ou fusion d’entité](https://msdn.microsoft.com/library/azure/hh452241.aspx) et [Insertion ou remplacement d’entité](https://msdn.microsoft.com/library/azure/hh452242.aspx) pour en savoir plus sur le fonctionnement de ces paramètres (fusion et remplacement). |Non |
| writeBatchSize |Insère des données dans la table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte.<br/>Valeurs autorisées : integer (nombre de lignes) |Non (valeur par défaut : 10 000) |
| writeBatchTimeout |Insère des données dans la table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte.<br/>Valeurs autorisées : timespan. Exemple : « 00:20:00 » (20 minutes) |Non (la valeur par défaut est 90 s - délai d’expiration par défaut du client de stockage) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mappez une colonne source à une colonne de destination à l’aide de la propriété « translator » pour pouvoir utiliser la colonne de destination comme azureTablePartitionKeyName.

Dans l’exemple suivant, la colonne source DivisionID est mappée sur la colonne de destination DivisionID.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

« DivisionID » est spécifié en tant que clé de partition.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mappage de type de données pour Table Azure

Lors de la copie de données de Table Azure, les mappages suivants sont utilisés entre les types de données Table Azure et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

Pendant le déplacement de données à partir de et vers Table Azure, les [mappages suivants définis par le service de Table Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) sont utilisés à partir des types OData Table Azure vers le type .NET et vice versa.

| Type de données de Table Azure | Type de données intermédiaires de Data Factory | Détails |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Tableau d’octets jusqu’à 64 Ko. |
| Edm.Boolean |valeur booléenne |Valeur booléenne. |
| Edm.DateTime |DateTime |Valeur de 64 bits exprimée en temps universel coordonné (UTC). La plage DateHeure prise en charge commence à partir de 12:00 minuit, le 1er janvier 1601 apr. J.C. (NOTRE ÈRE), UTC. La plage se termine le 31 décembre 9999. |
| Edm.Double |double |Valeur à virgule flottante de 64 bits. |
| Edm.Guid |Guid |Identificateur global unique de 128 bits. |
| Edm.Int32 |Int32 |Nombre entier 32 bits. |
| Edm.Int64 |Int64 |Nombre entier 64 bits. |
| Edm.String |String |Valeur encodée en UTF-16. Les valeurs de chaîne peuvent aller jusqu’à 64 Ko. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).