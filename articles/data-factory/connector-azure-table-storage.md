---
title: "Copier des données depuis et vers le stockage Table Azure à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers le stockage Table Azure ou du stockage Table Azure vers des banques de données réceptrices prises en charge."
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
ms.openlocfilehash: 41e2117e14f336d33f5d6f4e1f446e32a6886079
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copier des données depuis et vers le stockage Table Azure à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – Disponibilité générale](v1/data-factory-azure-table-connector.md)
> * [Version 2 - Préversion](connector-azure-table-storage.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis le stockage Table Azure. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 d’Azure Data Factory, qui est en disponibilité générale, consultez [Déplacer des données vers et depuis Azure Table à l’aide d’Azure Data Factory](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données depuis n’importe quelle banque de données source prise en charge vers le stockage Table. Vous pouvez également copier des données depuis le stockage Table vers n’importe quelle banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Table Azure prend en charge la copie de données à l’aide des authentifications par clé de compte et par signature d’accès partagé de service.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Azure Data Factory spécifiques au stockage Table.

## <a name="linked-service-properties"></a>Propriétés du service lié

### <a name="use-an-account-key"></a>Utiliser une clé de compte

Vous pouvez créer un service lié Stockage Azure à l’aide de la clé de compte. La fabrique de données dispose ainsi d’un accès global au stockage. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureStorage**. |OUI |
| connectionString | Spécifiez les informations requises pour la connexion au stockage pour la propriété connectionString. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre magasin de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

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

### <a name="use-service-shared-access-signature-authentication"></a>Utiliser l’authentification par signature d’accès partagé de service

Vous pouvez également créer un service lié de stockage à l’aide d’une signature d’accès partagé. Ainsi, la fabrique de données dispose d’un accès restreint ou limité dans le temps à tout ou partie des ressources dans le stockage.

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Vous pouvez l’utiliser pour octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations. Vous n’êtes pas obligé de partager vos clés d’accès de compte. La signature d’accès partagé est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d’accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié. Pour plus d’informations sur les signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Azure Data Factory prend désormais en charge les signatures d’accès partagé de service uniquement, et non les signatures d’accès partagé de compte. Pour plus d’informations sur ces deux types et leur construction, consultez [Types de signatures d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). L’URL de la signature d’accès partagé générée à partir du portail Azure ou de l’Explorateur Stockage Microsoft Azure est une signature d’accès partagé de compte, qui n’est donc pas prise en charge.

> [!TIP]
> Vous pouvez exécuter les commandes PowerShell suivantes pour générer une signature d’accès partagé de service pour votre compte de stockage. Remplacez les espaces réservés et octroyez l’autorisation nécessaire.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Pour utiliser l’authentification par signature d’accès partagé de service, les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureStorage**. |OUI |
| sasUri | Spécifiez l’URI de signature d’accès partagé des ressources de stockage, telles qu’un objet blob, un conteneur ou une table. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

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

Lorsque vous créez un URI de signature d’accès partagé, prenez en compte les points suivants :

- Définissez des autorisations de lecture/écriture appropriées sur les objets en fonction de l’utilisation du service lié (lecture, écriture, lecture/écriture) dans votre fabrique de données.
- Définissez le paramètre **Heure d’expiration** correctement. Assurez-vous que l’accès aux objets du stockage n’expire pas pendant la période active du pipeline.
- L’URI doit être créé au niveau table approprié en fonction des besoins.

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Table Azure.

Pour copier des données vers et depuis Table Azure, définissez la propriété de type du jeu de données sur **AzureTable** . Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du jeu de données doit être définie sur **AzureTable**. |OUI |
| TableName |Le nom de la table dans l’instance de base de données de stockage Table à laquelle le service lié fait référence. |OUI |

**Exemple :**

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

Pour les banques de données sans schéma comme Azure Table, Azure Data Factory déduit le schéma de l’une des manières suivantes :

* Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, Azure Data Factory respecte cette structure en tant que schéma. Dans ce cas, si une ligne ne contient pas de valeur pour une colonne, une valeur null est fournie pour celle-ci.
* Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes ne sont pas incluses dans le résultat de l’opération de copie.

Pour les sources de données sans schéma, la meilleure pratique consiste à définir la structure des données à l’aide de la propriété **structure**.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Table Azure.

### <a name="azure-table-as-a-source-type"></a>Table Azure en tant que type source

Pour copier des données de Table Azure, définissez **AzureTableSource** comme type de source dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source d’activité de copie doit être définie sur **AzureTableSource**. |OUI |
| AzureTableSourceQuery |Utilisez la requête de Table Azure personnalisée pour lire les données. Consultez les exemples dans la section suivante. |Non  |
| azureTableSourceIgnoreTableNotFound |Indique s’il faut autoriser l’exception de la table qui n’existe pas.<br/>Les valeurs autorisées sont **True** et **False** (par défaut). |Non  |

### <a name="azuretablesourcequery-examples"></a>Exemples azureTableSourceQuery

Si la colonne de Table Azure est de type datetime :

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Si la colonne de Table Azure est de type chaîne :

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Si vous utilisez le paramètre de pipeline, effectuez un cast de la valeur datetime au format approprié en fonction des exemples précédents.

### <a name="azure-table-as-a-sink-type"></a>Table Azure en tant que type récepteur

Pour copier des données vers la Table Azure, définissez le type de récepteur sur **AzureTableSink** dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **récepteur** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **AzureTableSink**. |OUI |
| azureTableDefaultPartitionKeyValue |La valeur de clé de partition par défaut qui peut être utilisée par le récepteur. |Non  |
| azureTablePartitionKeyName |Spécifiez le nom de la colonne dont les valeurs sont utilisées comme clés de partition. Si aucune valeur n'est spécifiée, « AzureTableDefaultPartitionKeyValue » est utilisée comme clé de partition. |Non  |
| azureTableRowKeyName |Spécifiez le nom de la colonne dont les valeurs sont utilisées comme clé de ligne. Si aucune valeur n'est spécifiée, un GUID est utilisé pour chaque ligne. |Non  |
| azureTableInsertType |Le mode d’insertion des données dans Table Azure. Cette propriété détermine le remplacement ou la fusion des valeurs des lignes existantes dans la table de sortie avec des clés de partition et de ligne correspondantes. <br/><br/>Les valeurs autorisées sont **fusionner** (par défaut), et **remplacer**. <br/><br> Ce paramètre s’applique au niveau ligne et non au niveau table. Ces options ne suppriment pas de lignes dans la table de sortie qui n’existent pas dans l’entrée. Consultez [Insert Or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Entité d’insertion ou de fusion) et [Insert Or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Entité d’insertion ou de remplacement) pour en savoir plus sur le fonctionnement des paramètres fusionner et remplacer. |Non  |
| writeBatchSize |Insère des données dans Table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte.<br/>Les valeurs autorisées sont des nombre entiers (nombre de lignes). |Non (valeur par défaut : 10 000) |
| writeBatchTimeout |Insère des données dans Table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte.<br/>Les valeurs autorisées sont des intervalles de temps. Par exemple : « 00:20:00 » (20 minutes). |Non (la valeur par défaut est 90 secondes, le délai d’expiration par défaut du client de stockage) |

**Exemple :**

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

Mappez une colonne source à une colonne de destination à l’aide de la propriété **« translator »** pour pouvoir utiliser la colonne de destination comme azureTablePartitionKeyName.

Dans l’exemple suivant, la colonne source DivisionID est mappée sur la colonne de destination DivisionID :

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

« DivisionID » est spécifié en tant que clé de partition.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mappage de type de données pour Table Azure

Lorsque vous copiez des données depuis et vers Table Azure, les mappages suivants sont utilisés entre les types de données Table Azure et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

Pendant le déplacement de données depuis et vers Table Azure, les [mappages suivants définis par Table Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) sont utilisés à partir des types OData Table Azure vers le type .NET et vice versa.

| Type de données de Table Azure | Type de données intermédiaires d’Azure Data Factory | Détails |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Tableau d’octets jusqu’à 64 Ko. |
| Edm.Boolean |bool |Valeur booléenne. |
| Edm.DateTime |Datetime |Valeur de 64 bits exprimée en temps universel coordonné (UTC). La plage DateHeure prise en charge commence à partir de minuit, le 1er janvier 1601 apr. J.C. (NOTRE ÈRE), UTC. La plage se termine le 31 décembre 9999. |
| Edm.Double |double |Valeur à virgule flottante de 64 bits. |
| Edm.Guid |Guid |Identificateur global unique de 128 bits. |
| Edm.Int32 |Int32 |Nombre entier 32 bits. |
| Edm.Int64 |Int64 |Nombre entier 64 bits. |
| Edm.String |Chaîne |Valeur encodée en UTF-16. Les valeurs de chaîne peuvent aller jusqu’à 64 Ko. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
