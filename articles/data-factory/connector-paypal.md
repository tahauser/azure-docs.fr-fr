---
title: "Copier des données de PayPal avec Azure Data Factory (version bêta) | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie pour copier des données de PayPal vers des magasins de données récepteurs pris en charge dans le cadre d’un pipeline Azure Data Factory."
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
ms.openlocfilehash: 7a30cca215dad6ca71a31bd1f775b85dd2f8ea00
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-paypal-using-azure-data-factory-beta"></a>Copier des données de PayPal avec Azure Data Factory (version bêta)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de PayPal. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en version bêta. Essayez-le et envoyez-nous vos commentaires. Ne l’utilisez pas dans des environnements de production.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de PayPal vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes donnent des précisions sur les propriétés utilisées pour définir des entités Data Factory propres au connecteur PayPal.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié PayPal :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **PayPal**. | Oui |
| host | URL de l’instance PayPal (c’est-à-dire api.sandbox.paypal.com).  | Oui |
| clientId | ID client associé à l’application PayPal.  | Oui |
| clientSecret | Clé secrète client associée à l’application PayPal. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie en tirer (pull) les données lors de la copie. Pour plus d’informations, consultez la page [Stocker des informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non  |
| useHostVerification | Indique si le nom d’hôte du certificat du serveur doit correspondre à celui du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non  |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non  |

**Exemple :**

```json
{
    "name": "PayPalLinkedService",
    "properties": {
        "type": "PayPal",
        "typeProperties": {
            "host" : "api.sandbox.paypal.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données PayPal.

Pour copier des données de PayPal, affectez la valeur **PayPalObject** à la propriété type du jeu de données. Il n’y a aucune autre propriété propre au type dans cette sorte de jeu de données.

**Exemple**

```json
{
    "name": "PayPalDataset",
    "properties": {
        "type": "PayPalObject",
        "linkedServiceName": {
            "referenceName": "<PayPal linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par la source PayPal.

### <a name="paypalsource-as-source"></a>PayPalSource comme source

Pour copier des données de PayPal, affectez la valeur **PayPalSource** au type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source de l’activité de copie doit être définie sur **PayPalSource**. | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM Payment_Experience"`. | Oui |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromPayPal",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PayPal input dataset name>",
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
                "type": "PayPalSource",
                "query": "SELECT * FROM Payment_Experience"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
