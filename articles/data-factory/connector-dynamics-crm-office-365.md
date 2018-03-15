---
title: "Copier des données depuis et vers Dynamics CRM ou Dynamics 365 à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier des données de Microsoft Dynamics CRM ou Microsoft Dynamics 365 vers des banques de données réceptrices prises en charge, ou comment copier des données de banques de données sources prises en charge vers Dynamics CRM ou Dynamics 365 à l’aide de l’activité de copie disponible dans un pipeline de banque de données."
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
ms.openlocfilehash: bcf80fe8f10ae8c81b5eea94137bd62558a6447a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-and-to-dynamics-365-or-dynamics-crm-by-using-azure-data-factory"></a>Copier des données depuis et vers Dynamics 365 ou Dynamics CRM à l’aide d’Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers Microsoft Dynamics 365 ou Microsoft Dynamics CRM. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est en disponibilité générale, consultez [Activité de copie dans la version 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de Dynamics 365 ou Dynamics CRM vers toute banque de données réceptrice prise en charge. Vous pouvez également copier des données de n’importe quel magasin de données source pris en charge vers Dynamics 365 ou Dynamics CRM. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs pour l’activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Ce connecteur Dynamics prend en charge les versions et les types d’authentification de Dynamics ci-dessous : (IFD est l’abréviation d’« Internet Facing Deployment ») (déploiement avec accès via Internet).

| Versions de Dynamics | Types d’authentification | Exemples de services liés |
|:--- |:--- |:--- |
| Dynamics 365 (en ligne) <br> Dynamics CRM en ligne | office365 | [Dynamics en ligne + authentification Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local avec IFD <br> Dynamics CRM 2016 local avec IFD <br> Dynamics CRM 2015 local avec IFD | IFD | [Dynamics local avec IFD + authentification IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Plus spécifiquement pour Dynamics 365, les types d’applications suivants sont pris en charge :

- Dynamics 365 pour les ventes
- Dynamics 365 pour le service client
- Dynamics 365 pour le service après-vente
- Dynamics 365 pour l’automatisation de service de projet
- Dynamics 365 pour le marketing

Les autres types d’applications, par exemple opérations et finances, talent, etc., ne sont pas pris en charge.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de Dynamics.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Dynamics sont les suivantes :

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 et Dynamics CRM en ligne

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Dynamics**. | OUI |
| deploymentType | Type de déploiement de l’instance Dynamics. Il doit être **« en ligne »** pour Dynamics en ligne. | OUI |
| organizationName | Nom d’organisation de l’instance Dynamics. | Non, à spécifier quand il y a plusieurs instances Dynamics associées à l’utilisateur |
| authenticationType | Type d’authentification pour se connecter à un serveur Dynamics. Spécifiez **« Office365 »** pour Dynamics en ligne. | OUI |
| username | Indiquez le nom d'utilisateur à utiliser pour se connecter à Dynamics. | OUI |
| password | Indiquez le mot de passe du compte d’utilisateur défini pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour la source, oui pour le récepteur si le service lié à la source n’a pas de runtime d’intégration |

>[!IMPORTANT]
>Lorsque vous copiez des données dans Salesforce, le runtime d’intégration Azure par défaut ne peut pas être utilisé pour exécuter la copie. En d’autres termes, si votre service lié à la source n’a pas de runtime d’intégration spécifié, [créez un Runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) de manière explicite, avec un emplacement près de votre instance Dynamics. Associez-le dans le service lié Dynamics comme dans l’exemple suivant.

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 et Dynamics CRM locaux avec IFD

*Les propriétés supplémentaires comparables à celles de Dynamics en ligne sont « hostName » et « port ».*

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Dynamics**. | OUI |
| deploymentType | Type de déploiement de l’instance Dynamics. Cela doit être **« OnPremisesWithIfd »** pour Dynamics local avec IFD.| OUI |
| hostName | Nom d’hôte du serveur Dynamics local. | OUI |
| port | Port du serveur Dynamics local. | Non, la valeur par défaut est 443 |
| organizationName | Nom d’organisation de l’instance Dynamics. | OUI |
| authenticationType | Type d’authentification pour se connecter au serveur Dynamics. Spécifiez **« Ifd »** pour Dynamics local avec IFD. | OUI |
| username | Indiquez le nom d'utilisateur à utiliser pour se connecter à Dynamics. | OUI |
| password | Indiquez le mot de passe du compte d’utilisateur défini pour le nom d’utilisateur. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie en tirer (pull) les données lors de la copie. Pour plus d’informations, consultez la page [Stocker des informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour Source, Oui pour Récepteur |

>[!IMPORTANT]
>Pour copier des données dans Dynamics, [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) de manière explicite, avec l’emplacement près de votre instance Dynamics. Associez-le dans le service lié comme dans l’exemple suivant.

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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Dynamics.

Pour copier des données depuis et vers Dynamics, définissez la propriété de type du jeu de données sur **DynamicsEntity**. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du jeu de données doit être définie sur **DynamicsEntity**. |OUI |
| entityName | Nom logique de l’entité à récupérer. | Non pour la source (si « query » est spécifié dans la source de l’activité) ; Oui pour le récepteur |

> [!IMPORTANT]
>- Lorsque vous copiez des données à partir de Dynamics, la section « structure » est requise dans le jeu de données Dynamics. Il définit le nom de colonne et le type de données pour les données Dynamics que vous souhaitez copier. Pour en savoir plus, consultez [Dataset structure](concepts-datasets-linked-services.md#dataset-structure) (Structure du jeu de données) et [Mappage de type de données pour Dynamics](#data-type-mapping-for-dynamics).
>- Lorsque vous copiez des données vers Dynamics, la section « structure » est facultative dans le jeu de données Dynamics. Les colonnes à copier sont déterminées par le schéma de données source. Si votre source est un fichier CSV sans en-tête, spécifiez la « structure » dans le jeu de données d’entrée avec le nom de colonne et le type de données. Ils sont mappés aux champs dans le fichier CSV un par un dans l’ordre.

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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par les types de source et de récepteur Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics en tant que type de source

Pour copier des données de Dynamics, définissez le type de source dans l’activité de copie sur **DynamicsSource**. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source de l’activité de copie doit être définie sur **DynamicsSource**. | OUI |
| query | FetchXML est un langage de requête propriétaire qui est utilisé dans Dynamics (en ligne et local). Consultez l’exemple qui suit. Pour en savoir plus, consultez [Build queries with FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx) (Générer des requêtes avec FeachXML). | Non (si « entityName » est spécifié dans le jeu de données) |

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics comme type de récepteur

Pour copier des données vers Dynamics, définissez le type de récepteur dans l’activité de copie sur **DynamicsSink**. Les propriétés suivantes sont prises en charge dans la section **récepteur** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **DynamicsSink**. | OUI |
| writeBehavior | Comportement d’écriture de l’opération.<br/>La valeur autorisée est **« Upsert »**. | OUI |
| writeBatchSize | Nombre de lignes de données écrites dans Dynamics pour chaque lot. | Non (valeur par défaut : 10) |
| ignoreNullValues | Indique si les valeurs Null des données d’entrée (à l’exception des champs clés) doivent être ignorées pendant une opération d’écriture.<br/>Les valeurs autorisées sont **true** et **false**.<br>- **True** : ne pas modifier des données dans l’objet de destination lorsque vous effectuez une opération upsert/une opération de mise à jour. Insérer une valeur définie par défaut lorsque vous effectuez une opération insert.<br/>- **False** : ne pas modifier des données dans l’objet de destination lorsque vous effectuez une opération upsert/une opération de mise à jour. Insérer une valeur NULL lorsque vous effectuez une opération insert. | Non (valeur par défaut : false) |

>[!NOTE]
>La valeur par défaut du récepteur writeBatchSize et de l’activité de copie [parallelCopies](copy-activity-performance.md#parallel-copy) pour le récepteur Dynamics est de 10. Par conséquent, 100 enregistrements sont soumis simultanément à Dynamics.

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

Lorsque vous copiez de données de Dynamics, les mappages suivants sont utilisés entre les types de données Dynamics et les types de données intermédiaires Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

Configurez le type de données Data Factory correspondant dans la structure du jeu de données en fonction du type de données Dynamics de votre source à l’aide des tables de mappage suivantes :

| Type de données Dynamics | Type de données intermédiaires d’Azure Data Factory | Prise en charge en tant que source | Prise en charge en tant que récepteur |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booléen | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Décimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Chaîne | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | |
| AttributeType.ManagedProperty | Booléen | ✓ | |
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

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
