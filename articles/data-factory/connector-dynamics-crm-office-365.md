---
title: "Copier des données vers et depuis Dynamics CRM et 365 à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier des données de Dynamics CRM et 365 vers des banques de données réceptrices prises en charge et comment copier des données de banques de données sources prises en charge vers Dynamics CRM et 365, à l’aide de l’activité de copie disponible dans le pipeline Azure Data Factory."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 91de03f3472244341f4cf086bc8a2f56f7d2e487
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copier des données vers et depuis Dynamics CRM et 365 à l’aide d’Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis Dynamics 365 et Dynamics CRM. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de Dynamics 365/Dynamics CRM vers toute banque de données réceptrice prise en charge, et de toute banque de données source prise en charge vers Dynamics 365/Dynamics CRM. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Ce connecteur Dynamics prend en charge les versions et les types d’authentification de Dynamics ci-dessous (*IFD, pour Internet Facing Deployment*) :

| Versions de Dynamics | Types d’authentification | Exemples de services liés |
|:--- |:--- |:--- |
| Dynamics 365 (en ligne) <br> Dynamics CRM (en ligne) | office365 | [Dynamics en ligne + authentification Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local avec IFD <br> Dynamics CRM 2016 local avec IFD <br> Dynamics CRM 2015 local avec IFD | IFD | [Dynamics local avec IFD + authentification IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Plus spécifiquement pour Dynamics 365, les types d’applications suivants sont pris en charge :

- Dynamics 365 pour les ventes
- Dynamics 365 pour le service client
- Dynamics 365 pour le service après-vente
- Dynamics 365 pour l’automatisation de service de projet
- Dynamics 365 pour le marketing

> [!NOTE]
> Pour utiliser le connecteur Dynamics, stockez votre mot de passe dans Azure Key Vault et laissez l’activité de copie le récupérer au moment de l’exécution d’une copie de données. Découvrez comment configurer dans la section relative aux [propriétés de service lié](#linked-service-properties).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de Dynamics.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Dynamics sont les suivantes :

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 et Dynamics CRM en ligne

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Dynamics**. | Oui |
| deploymentType | Type de déploiement de l’instance Dynamics. Doit être **« Online »** pour Dynamics en ligne. | Oui |
| organizationName | Nom d’organisation de l’instance Dynamics. | Non, à spécifier quand il y a plusieurs instances Dynamics associées à l’utilisateur. |
| authenticationType | Type d’authentification pour se connecter au serveur Dynamics. Spécifiez **« Office365 »** pour Dynamics en ligne. | Oui |
| username | Spécifiez le nom d’utilisateur pour la connexion à Dynamics. | Oui |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Vous devez placer le mot de passe dans Azure Key Vault et le configurer en tant que « AzureKeyVaultSecret ». Pour plus d’informations, consultez [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour la source, oui pour le récepteur si le service lié à la source ne dispose pas du runtime d’intégration. |

>[!IMPORTANT]
>Pendant la copie de données **dans** Dynamics, le runtime d’intégration Azure par défaut ne peut pas être utilisé pour exécuter la copie. En d’autres termes, si aucun runtime d’intégration n’est spécifié pour votre service lié à la source, vous devez [créer un runtime Azure IR](create-azure-integration-runtime.md#create-azure-ir) explicitement à un emplacement proche de Dynamics et l’associer au service lié Dynamics, comme dans l’exemple suivant.

**Exemple : Dynamics en ligne utilisant l’authentification Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 et Dynamics CRM locaux avec IFD

*Les propriétés supplémentaires en comparaison de Dynamics en ligne sont « hostName » et « port ».*

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Dynamics**. | Oui |
| deploymentType | Type de déploiement de l’instance Dynamics. Doit être **« OnPremisesWithIfd »** pour Dynamics local avec IFD.| Oui |
| **hostName** | Nom d’hôte du serveur Dynamics local. | Oui |
| **port** | Port du serveur de Dynamics local. | Non, la valeur par défaut est 443 |
| organizationName | Nom d’organisation de l’instance Dynamics. | Oui |
| authenticationType | Type d’authentification pour se connecter au serveur Dynamics. Spécifiez **« Ifd »** pour Dynamics local avec IFD. | Oui |
| username | Spécifiez le nom d’utilisateur pour la connexion à Dynamics. | Oui |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Notez que vous devez placer le mot de passe dans Azure Key Vault et le configurer en tant que « AzureKeyVaultSecret ». Pour plus d’informations, consultez [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour Source, Oui pour Récepteur |

>[!IMPORTANT]
>Pour copier des données dans Dynamics, vous devez [créer un runtime Azure IR](create-azure-integration-runtime.md#create-azure-ir) explicitement à un emplacement proche de Dynamics et l’associer au service lié, comme dans l’exemple suivant.

**Exemple : Dynamics local avec IFD utilisant l’authentification IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Dynamics.

Pour copier des données vers/depuis Dynamics, affectez la valeur **DynamicsEntity** à la propriété de type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **DynamicsEntity** |Oui |
| entityName | Nom logique de l’entité à récupérer. | Non pour la source (si « query » est spécifié dans la source de l’activité) ; Oui pour le récepteur |

> [!IMPORTANT]
>- **Lorsque vous copiez des données à partir de Dynamics, la section « structure » est obligatoire** dans le jeu de données Dynamics, car elle définit le nom de colonne et le type de données pour les données Dynamics que vous voulez copier. Pour en savoir plus, découvrez la [structure du jeu de données](concepts-datasets-linked-services.md#dataset-structure) et le [mappage de type de données pour Dynamics](#data-type-mapping-for-dynamics).
>- **Lorsque vous copiez des données vers Dynamics, la section « structure » est facultative** dans le jeu de données Dynamics. Les colonnes à copier sont déterminées par le schéma de données source. Si votre source est un fichier CSV sans en-tête, spécifiez la « structure » dans le jeu de données d’entrée avec le nom de colonne et le type de données correspondant aux champs du fichier CSV, l’un après l’autre, dans l’ordre.

**Exemple :**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Dynamics.

### <a name="dynamics-as-source"></a>Dynamics en tant que source

Pour copier des données de Dynamics, définissez le type de source dans l’activité de copie sur **DynamicsSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source de l’activité de copie doit être définie sur **DynamicsSource**.  | Oui |
| query  | FetchXML est un langage de requête propriétaire qui est utilisé dans Microsoft Dynamics (en ligne et local). Voyez l’exemple ci-dessous et apprenez-en davantage en lisant [Générer des requêtes avec FetchXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Non (si « entityName » est spécifié dans le jeu de données)  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
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
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exemple de requête FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-sink"></a>Dynamics en tant que récepteur

Pour copier des données vers Dynamics, définissez le type de récepteur dans l’activité de copie sur **DynamicsSink**. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **DynamicsSink**.  | Oui |
| writeBehavior | Comportement d’écriture de l’opération.<br/>La valeur autorisée est **Upsert**. | Oui |
| writeBatchSize | Nombre de lignes de données écrites dans Dynamics pour chaque lot. | Non (valeur par défaut : 10) |
| ignoreNullValues | Indique si les valeurs Null des données d’entrée (à l’exception des champs clés) doivent être ignorées pendant l’opération d’écriture.<br/>Les valeurs autorisées sont **True** et **False**.<br>- true : ne modifie pas les données de l’objet de destination lors d’une opération Upsert/Update, et insère la valeur définie par défaut lors d’une opération Insert.<br/>false : attribue la valeur NULL aux données de l’objet de destination lors d’une opération Upsert/Update, et insère la valeur NULL lors d’une opération Insert.  | Non (valeur par défaut : false) |

>[!NOTE]
>La valeur par défaut du récepteur writeBatchSize et de l’activité de copie [parallelCopies](copy-activity-performance.md#parallel-copy) pour le récepteur Dynamics sont toutes les deux égales à 10, ce qui signifie que 100 enregistrements sont envoyés à Dynamics simultanément.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mappage de type de données pour Dynamics

Lors de la copie de données de Dynamics, les mappages suivants sont utilisés entre les types de données Dynamics et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

Configurez le type de données Data Factory correspondant dans la structure du jeu de données en fonction du type de données Dynamics de votre source à l’aide de la table de mappage ci-dessous :

| Type de données Dynamics | Type de données intermédiaires de Data Factory | Prise en charge en tant que source | Prise en charge en tant que récepteur |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booléen | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Décimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Chaîne | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | Booléen | ✓ |  |
| AttributeType.Memo | Chaîne | ✓ | ✓ |
| AttributeType.Money | Décimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Chaîne | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Les types de données Dynamics AttributeType.CalendarRules et AttributeType.PartyList ne sont pas pris en charge.

## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).