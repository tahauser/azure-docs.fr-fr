---
title: "Copier des données de Google BigQuery à l’aide d’Azure Data Factory (version bêta) | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline de fabrique de données pour copier des données de Google BigQuery vers des banques de données réceptrices prises en charge."
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
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Copier des données de Google BigQuery à l’aide d’Azure Data Factory (version bêta)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Google BigQuery. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez [Activité de copie dans la version 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en version bêta. Essayez-le et envoyez-nous vos commentaires. Ne l’utilisez pas dans des environnements de production.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données depuis Google BigQuery vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

 Data Factory fournit un pilote intégré pour permettre la connectivité. Vous n’avez donc pas besoin d’installer manuellement un pilote pour utiliser ce connecteur.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Google BigQuery.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Google BigQuery sont les suivantes.

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **GoogleBigQuery**. | OUI |
| project | L’ID du projet BigQuery par défaut sur lequel exécuter la requête.  | OUI |
| additionalProjects | Liste séparée par des virgules des ID de projets BigQuery publics accessibles.  | Non  |
| requestGoogleDriveScope | Pour demander l’accès à Google Drive. Autoriser l’accès à Google Drive active la prise en charge des tables fédérées qui combinent les données BigQuery avec les données issues de Google Drive. La valeur par défaut est **false**.  | Non  |
| authenticationType | Mécanisme d’authentification OAuth 2.0 utilisé pour l’authentification. ServiceAuthentication ne peut être utilisé que sur un runtime d’intégration auto-hébergé. <br/>Les valeurs autorisées sont **ServiceAuthentication** et **UserAuthentication**. | OUI |
| refreshToken | Jeton d’actualisation obtenu de Google servant à autoriser l’accès à BigQuery pour UserAuthentication. Vous pouvez marquer ce champ en tant que SecureString pour le stocker en toute sécurité dans Data Factory. Vous pouvez également stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie l’y récupérer quand vous effectuez une copie de données. Pour plus d’informations, consultez [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| email | ID d’e-mail du compte de service utilisé pour ServiceAuthentication. Il ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non  |
| keyFilePath | Chemin complet du fichier de clé .p12 utilisé pour authentifier l’adresse e-mail du compte de service. Il ne peut être utilisé que sur un runtime d’intégration auto-hébergé.  | Non  |
| trustedCertPath | Chemin complet du fichier .pem qui contient les certificats d’autorité de certification approuvés utilisés pour vérifier le serveur quand vous vous connectez via SSL. Cette propriété ne peut être définie que quand vous utilisez SSL sur le runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non  |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier .pem spécifié. La valeur par défaut est **false**.  | Non  |

**Exemple :**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Google BigQuery.

Pour copier des données à partir de Google BigQuery, définissez la propriété type du jeu de données sur **GoogleBigQueryObject**. Il n’y a aucune autre propriété propre au type dans cette sorte de jeu de données.

**Exemple**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par le type de source Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource en tant que type de source

Pour copier des données à partir de Google BigQuery, définissez le type de source sur **GoogleBigQuerySource** dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes.

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **GoogleBigQuerySource**. | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple `"SELECT * FROM MyTable"`. | OUI |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
