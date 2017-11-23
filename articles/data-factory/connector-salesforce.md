---
title: "Copier des données dans Salesforce à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier des données de Salesforce vers des banques de données réceptrices prises en charge et comment copier des données de banques de données sources prises en charge vers Salesforce, à l’aide de l’activité de copie disponible dans le pipeline Azure Data Factory."
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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 017d03b76bd19a0b3a1e19c22233c61be9067d0d
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-fromto-salesforce-using-azure-data-factory"></a>Copier des données dans Salesforce à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-salesforce-connector.md)
> * [Version 2 - Préversion](connector-salesforce.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Salesforce et vers Salesforce. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Salesforce dans V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de Salesforce vers toute banque de données réceptrice prise en charge, ainsi que copier des données de toute banque de données source prise en charge vers Salesforce. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Ce connecteur Salesforce prend en charge :

- Les éditions suivantes de Salesforce : **Developer Edition, Professional Edition, Enterprise Edition et Unlimited Edition**.
- La copie de données vers et depuis des **domaines de production, des domaines sandbox et des domaines personnalisés** Salesforce.

## <a name="prerequisites"></a>Composants requis

* L’autorisation de l’API doit être activée dans Salesforce. Consultez l’article [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites des requêtes Salesforce

Salesforce prend en charge un nombre limité de requêtes d’API totales et de requêtes d’API simultanées. Notez les points suivants :

- Si le nombre de requêtes simultanées dépasse la limite autorisée, les nouvelles requêtes sont bloquées avec un risque de défaillances aléatoires.
- Si le nombre total de requêtes dépasse la limite autorisée, le compte Salesforce est bloqué pendant 24 heures.

Vous pouvez également recevoir l’erreur « REQUEST_LIMIT_EXCEEDED » dans les deux scénarios. Pour plus de détails, consultez la section « API Request Limits » (Limites de requête d’API) du document [Salesforce Developer Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limites des développeurs Salesforce).

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Salesforce.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Salesforce sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type |La propriété de type doit être définie sur **Salesforce**. |Oui |
| environmentUrl | Spécifiez l’URL de l’instance Salesforce. <br> - La valeur par défaut est `"https://login.salesforce.com"`. <br> - Pour copier des données du bac à sable, spécifiez `"https://test.salesforce.com"`. <br> - Pour copier les données du domaine personnalisé, spécifiez, par exemple, `"https://[domain].my.salesforce.com"`. |Non |
| username |Spécifiez un nom d’utilisateur pour le compte d’utilisateur. |Oui |
| password |Spécifiez le mot de passe du compte d’utilisateur.<br/><br/>Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie du fichier de définition d’application en tirer (pull) les données lors de la copie des données. Pour plus d’informations, consultez [Stocker les informations d’identification dans le coffre de clés](store-credentials-in-key-vault.md). |Oui |
| securityToken |Spécifiez le jeton de sécurité du compte d’utilisateur. Consultez l’article [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Obtenir un jeton de sécurité) pour obtenir des instructions sur la réinitialisation et l’obtention d’un jeton de sécurité. Pour en savoir plus sur les jetons de sécurité, consultez l’article [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sécurité et API).<br/><br/>Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le jeton de sécurité dans Azure Key Vault et laisser l’activité de copie du fichier de définition d’application en tirer (pull) les données lors de la copie des données. Pour plus d’informations, consultez [Stocker les informations d’identification dans le coffre de clés](store-credentials-in-key-vault.md). |Oui |
| connectVia | [Integration Runtime](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. À défaut de spécification, le runtime Azure Integration Runtime par défaut est utilisé. | Non pour Source, Oui pour Récepteur |

>[!IMPORTANT]
>Pour copier des données dans Salesforce, vous devez [créer un runtime Azure IR](create-azure-integration-runtime.md#create-azure-ir) explicitement à un emplacement proche de Salesforce et l’associer au service lié, comme dans l’exemple suivant.

**Exemple : stockage des informations d’identification dans le fichier de définition d’application**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage des informations d’identification dans Azure Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Salesforce.

Pour copier des données vers et depuis Salesforce, définissez la propriété de type du jeu de données sur **SalesforceObject**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **SalesforceObject**.  | Oui |
| objectApiName | Nom d’objet Salesforce duquel extraire des données. | Non pour Source, Oui pour Récepteur |

> [!IMPORTANT]
> La partie « __c » du nom de l’API est requise pour tout objet personnalisé.

![Connexion Salesforce - Data Factory - Nom de l’API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemple :**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Pour permettre la compatibilité descendante, l’ancien jeu de données de type « RelationalTable » continue de fonctionner lorsque vous copiez des données à partir de Salesforce. Toutefois, il vous est proposé de passer au nouveau type « SalesforceObject ».

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **RelationalTable** | Oui |
| TableName | Nom de la table dans Salesforce. | Non (si « query » dans la source de l’activité est spécifié) |

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Salesforce.

### <a name="salesforce-as-source"></a>Salesforce en tant que source

Pour copier des données à partir de Salesforce, définissez le type de source sur **SalesforceSource** dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type de la source d’activité de copie doit être définie sur **SalesforceSource** | Oui |
| query |Utilise la requête personnalisée pour lire des données. Vous pouvez utiliser une requête SQL-92 ou [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Par exemple : `select * from MyTable__c`. | Non (si « tableName » est spécifié dans dataset) |

> [!IMPORTANT]
> La partie « __c » du nom de l’API est requise pour tout objet personnalisé.

![Connexion Salesforce - Data Factory - Nom de l’API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Pour la compatibilité descendante, l’ancienne source de copie de type « RelationalSource » continue de fonctionner lorsque vous copiez des données à partir de Salesforce. Toutefois, il vous est proposé de passer au nouveau type « SalesforceSource ».

### <a name="salesforce-as-sink"></a>Salesforce en tant que récepteur

Pour copier des données vers Salesforce, définissez le type de récepteur sur **SalesforceSink** dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type du récepteur d’activité de copie doit être définie sur **SalesforceSink** | Oui |
| writeBehavior | Comportement d’écriture de l’opération.<br/>Les valeurs autorisées sont **Insert** et **Upsert**. | Non (la valeur par défaut est un point Insert) |
| externalIdFieldName | Nom du champ ID externe pour l’opération d’upsert. Le champ spécifié doit être défini en tant que « Champ ID externe » dans l’objet Salesforce. En outre, les données d’entrée qu’il contient ne peuvent pas comprendre de valeurs Null. | Oui, pour « Upsert » |
| writeBatchSize | Nombre de lignes de données écrites dans Salesforce pour chaque lot. | Non (valeur par défaut : 5 000) |
| ignoreNullValues | Indique si les valeurs Null des données d’entrée doivent être ignorées pendant l’opération d’écriture.<br/>Les valeurs autorisées sont **True** et **False**.<br>- **true** : ne modifie pas les données de l’objet de destination lors d’une opération Upsert/Update, et insère la valeur définie par défaut lors d’une opération Insert.<br/>- **false** : attribue la valeur NULL aux données de l’objet de destination lors d’une opération Upsert/Update, et insère la valeur NULL lors d’une opération Insert. | Non (valeur par défaut : false) |

### <a name="example-salesforce-sink-in-copy-activity"></a>Exemple : récepteur Salesforce dans l’activité de copie

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Conseils pour les requêtes

### <a name="retrieving-data-from-salesforce-report"></a>Récupération de données à partir d’un rapport Salesforce

Vous pouvez récupérer des données à partir de rapports Salesforce en spécifiant la requête en tant que `{call "<report name>"}`. Exemple : `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Récupération d’enregistrements supprimés dans la Corbeille Salesforce

Pour interroger les enregistrements supprimés de manière réversible dans la Corbeille Salesforce, vous pouvez spécifier **« IsDeleted = 1 »** dans votre requête. Par exemple,

* Pour interroger uniquement les enregistrements supprimés, spécifiez « select * from MyTable__c **where IsDeleted= 1** »
* Pour interroger tous les enregistrements, notamment ceux existants et supprimés, spécifiez « select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1** »

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Récupération de données à l’aide de la clause where sur la colonne DateTime

Lorsque vous spécifiez une requête SOQL ou SQL, faites attention à la différence de format DateTime. Par exemple :

* **Exemple SOQL** : `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemple SQL** : `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Mappage de type de données pour Salesforce

Lors de la copie de données de Salesforce, les mappages suivants sont utilisés entre les types de données Salesforce et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Salesforce | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| Numérotation automatique |String |
| Case à cocher |Boolean |
| Devise |Double |
| Date |DateTime |
| Date/Heure |DateTime |
| Email |String |
| ID |String |
| Relation de recherche |String |
| Liste déroulante à sélection multiple |String |
| Number |Double |
| Pourcentage |Double |
| Téléphone |String |
| Liste déroulante |String |
| Texte |String |
| Zone de texte |String |
| Zone de texte (long) |String |
| Zone de texte (enrichi) |String |
| Texte (chiffré) |String |
| URL |String |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).