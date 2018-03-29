---
title: Copier des données d’Impala à l’aide d’Azure Data Factory (version bêta) | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie pour copier des données d’Impala vers des magasins de données récepteurs pris en charge dans le cadre d’un pipeline de fabrique de données.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 22e1ddbd9f621a3ef270c4b769032e6456b54fc9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>Copier des données d’Impala à l’aide d’Azure Data Factory (version bêta)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’Impala. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est en disponibilité générale, consultez [Activité de copie dans la version 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en version bêta. Essayez-le et envoyez-nous vos commentaires. Ne l’utilisez pas dans des environnements de production.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’Impala vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

 Data Factory fournit un pilote intégré pour permettre la connectivité. Vous n’avez donc pas besoin d’installer manuellement un pilote pour utiliser ce connecteur.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Impala.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Impala.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **Impala**. | OUI |
| host | Adresse IP ou nom d’hôte du serveur Impala (c’est-à-dire, 192.168.222.160).  | OUI |
| port | Port TCP utilisé par le serveur Impala pour écouter les connexions clientes. Valeur par défaut : 21050.  | Non  |
| authenticationType | Type d’authentification à utiliser. <br/>Les valeurs autorisées sont **Anonymous**, **SASLUsername** et **UsernameAndPassword**. | OUI |
| username | Nom d’utilisateur utilisé pour accéder au serveur Impala. La valeur par défaut est Anonymous si vous utilisez SASLUsername.  | Non  |
| password | Mot de passe qui correspond au nom d’utilisateur si vous utilisez UsernameAndPassword. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| enableSsl | Indique si les connexions au serveur sont chiffrées à l’aide du protocole SSL. La valeur par défaut est **false**.  | Non  |
| trustedCertPath | Chemin complet du fichier .pem qui contient les certificats d’autorité de certification approuvés utilisés pour vérifier le serveur quand vous vous connectez via SSL. Cette propriété ne peut être définie que quand vous utilisez SSL sur le runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non  |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier PEM spécifié. La valeur par défaut est **false**.  | Non  |
| allowHostNameCNMismatch | Indique si le nom du certificat SSL émis par l’autorité de certification doit correspondre au nom d’hôte du serveur si vous vous connectez via SSL. La valeur par défaut est **false**.  | Non  |
| allowSelfSignedServerCert | Indique si les certificats auto-signés provenant du serveur sont autorisés ou non. La valeur par défaut est **false**.  | Non  |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données Impala.

Pour copier des données d’Impala, affectez la valeur **ImpalaObject** à la propriété type du jeu de données. Il n’y a aucune autre propriété propre au type dans cette sorte de jeu de données.

**Exemple**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par le type de source Impala.

### <a name="impala-as-a-source-type"></a>Impala en tant que type de source

Pour copier des données d’Impala, affectez la valeur **ImpalaSource** au type source de l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source de l’activité de copie doit être définie sur **ImpalaSource**. | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple `"SELECT * FROM MyTable"`. | OUI |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
