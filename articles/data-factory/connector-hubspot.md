---
title: "Copier des données de HubSpot à l’aide d’Azure Data Factory (version bêta) | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de HubSpot vers des banques de données réceptrices prises en charge."
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
ms.openlocfilehash: 6fbc4644ca7d42f40024739b48c12968e67c7fee
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-hubspot-using-azure-data-factory-beta"></a>Copier des données de HubSpot à l’aide d’Azure Data Factory (version bêta)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis HubSpot. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en version bêta. Essayez-le et envoyez-nous vos commentaires. Ne l’utilisez pas dans des environnements de production.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données depuis HubSpot vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur HubSpot.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié HubSpot sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **HubSpot** | Oui |
| clientId | L’ID client associé à votre application Hubspot.  | Oui |
| clientSecret | La clé secrète client associée à votre application Hubspot. Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie effectuer l’extraction des données lors de la copie. Pour plus d’informations, consultez [Stocker les informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| accessToken | Le jeton d’accès obtenu lors de l’authentification initiale de votre intégration OAuth. Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie effectuer l’extraction des données lors de la copie. Pour plus d’informations, consultez [Stocker les informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| refreshToken | Le jeton d’actualisation obtenu lors de l’authentification initiale de votre intégration OAuth. Vous pouvez choisir de marquer ce champ comme un SecureString pour le stocker de manière sécurisée dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie effectuer l’extraction des données lors de la copie. Pour plus d’informations, consultez [Stocker les informations d’identification dans Key Vault](store-credentials-in-key-vault.md). | Oui |
| useEncryptedEndpoints | Spécifie si les points de terminaison de source de données sont chiffrés à l’aide de HTTPS. La valeur par défaut est true.  | Non |
| useHostVerification | Spécifie si le nom d’hôte dans le certificat de serveur doit correspondre au nom d’hôte du serveur lors de la connexion via le protocole SSL. La valeur par défaut est true.  | Non |
| usePeerVerification | Spécifie s’il faut vérifier l’identité du serveur lors de la connexion via le protocole SSL. La valeur par défaut est true.  | Non |

**Exemple :**

```json
{
    "name": "HubspotLinkedService",
    "properties": {
        "type": "Hubspot",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données HubSpot.

Pour copier des données depuis HubSpot, définissez la propriété type du jeu de données sur **HubspotObject**. Il n’y a aucune autre propriété de type dans cette sorte de jeu de données.

**Exemple**

```json
{
    "name": "HubspotDataset",
    "properties": {
        "type": "HubspotObject",
        "linkedServiceName": {
            "referenceName": "<Hubspot linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source HubSpot.

### <a name="hubspotsource-as-source"></a>HubspotSource en tant que source

Pour copier des données depuis HubSpot, définissez le type de source dans l’activité de copie sur **HubspotSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur **HubspotSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM Companies where Company_Id = xxx"`. | Oui |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromHubspot",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hubspot input dataset name>",
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
                "type": "HubspotSource",
                "query": "SELECT * FROM Companies where Company_Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
