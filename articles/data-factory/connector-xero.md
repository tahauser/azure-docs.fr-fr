---
title: "Copier des données de Xero à l’aide d’Azure Data Factory (version bêta) | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de Xero vers des banques de données réceptrices prises en charge."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 9236198338fc66697942463e350a0f9732ea217b
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Copier des données de Xero à l’aide d’Azure Data Factory (version bêta)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de Xero. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en version bêta. Essayez-le et envoyez-nous vos commentaires. Ne l’utilisez pas dans des environnements de production.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données de Xero vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Xero.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Xero sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **Xero** | Oui |
| host | Point de terminaison du serveur Xero (autrement dit, api.xero.com).  | Oui |
| consumerKey | Clé de consommateur associée à l’application Xero. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans Data Factory, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie en tirer (pull) les données durant la copie. Pour plus d’informations, consultez [Stocker des informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| privateKey | Clé privée provenant du fichier .pem qui a été généré pour votre application privée Xero. Inclut tout le texte du fichier .pem, y compris les fins de ligne Unix (\n). Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans Data Factory, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie en tirer (pull) les données durant la copie. Pour plus d’informations, consultez [Stocker des informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non  |
| useHostVerification | Indique si le nom d’hôte est requis dans le certificat de serveur et doit correspondre au nom d’hôte du serveur lors de la connexion via le protocole SSL. La valeur par défaut est true.  | Non  |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non  |

**Exemple :**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Xero.

Pour copier des données de Xero, affectez la valeur **XeroObject** à la propriété de type du jeu de données. Il n’y a aucune autre propriété propre au type dans cette sorte de jeu de données.

**Exemple**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Xero.

### <a name="xerosource-as-source"></a>XeroSource en tant que source

Pour copier des données de Xero, définissez le type de source dans l’activité de copie sur **XeroSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source d’activité de copie doit être définie sur **XeroSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM Contacts"`. | Oui |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>étapes suivantes
Consultez les [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir la liste des banques de données prises en charge par l’activité de copie.
