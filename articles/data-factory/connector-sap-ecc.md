---
title: "Copier des données de SAP ECC à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de SAP ECC vers des magasins de données récepteurs pris en charge."
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
ms.date: 02/27/2018
ms.author: jingwang
ms.openlocfilehash: efca2c129a1c7b8aca6b879d6d1311c6be157be1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Copier des données de SAP ECC à l’aide d’Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de SAP ECC (SAP Enterprise Central Component). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de SAP ECC vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SAP ECC prend en charge ce qui suit :

- Copie de données à partir de SAP ECC sur SAP NetWeaver version 7.0 et ultérieures 
- Copie de données à partir de tout objet exposé par les services SAP ECC OData (par exemple, vues/tables SAP, BAPI, extracteurs de données, etc.), ou données/IDOC envoyés à SAP PI et pouvant être reçues en tant que OData par le biais d’adaptateurs relatifs
- Copie de données en utilisant une authentification de base.

## <a name="prerequisites"></a>Prérequis

En règle générale, SAP ECC expose des entités par le biais de services OData via la passerelle SAP. Pour utiliser ce connecteur SAP ECC, vous devez :

- **Configurer la passerelle SAP**. Sur les serveurs avec une version de SAP NetWeaver supérieure à la version 7.4, la passerelle SAP est déjà installée. Sinon, vous devez installer la passerelle incorporée ou le hub de passerelle avant d’exposer des données SAP ECC par le biais des services OData. Découvrez comment configurer la passerelle SAP dans le [guide d’installation](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Activer et configurer le service SAP OData**. Vous pouvez activer les services OData par le biais de TCODE SICF en quelques secondes. Vous pouvez également configurer les objets qui doivent être exposés. Voici un exemple d’[instructions pas à pas](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Prise en main

Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, consultez le [didacticiel sur l’activité de copie](quickstart-create-data-factory-dot-net.md).

Les sections suivantes fournissent des détails sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur SAP ECC.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP ECC sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **SapEcc** | OUI |
| url | URL du service OData SAP ECC. | OUI |
| username | Nom d’utilisateur utilisé pour se connecter à SAP ECC. | Non  |
| password | Mot de passe en texte clair utilisé pour se connecter à SAP ECC. | Non  |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données SAP ECC.

Pour copier des données de SAP ECC, définissez **SapEccResource** comme propriété de type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| chemin d’accès | Chemin de l’entité OData SAP ECC. | OUI |

**Exemple**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC en tant que source

Pour copier des données de SAP ECC, définissez **SapEccSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source d’activité de copie doit être définie sur **SapEccSource**. | OUI |
| query | Options de requête OData pour filtrer les données. Exemple : "$select=Name,Description&$top=10".<br/><br/>Le connecteur SAP ECC copie les données à partir de l’URL combinée : (URL spécifiée dans le service lié)/(chemin spécifié dans le jeu de données)?(requête spécifiée dans la source de copie d’activité). Voir [Composants d’URL d’OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | OUI |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>Mappage de type de données pour SAP ECC

Lors de la copie de données de SAP ECC, les mappages suivants sont utilisés entre les types de données OData pour les données SAP ECC et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données OData | Type de données intermédiaires de Data Factory |
|:--- |:--- |:--- |
| Edm.Binary | Chaîne |
| Edm.Boolean | Bool |
| Edm.Byte | Chaîne |
| Edm.DateTime | Datetime |
| Edm.Decimal | Décimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Chaîne |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Chaîne |
| Edm.Time | intervalle de temps |
| Edm.DateTimeOffset | DatetimeOffset |

> [!NOTE]
> Les types de données complexes ne sont pas pris en charge actuellement.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
