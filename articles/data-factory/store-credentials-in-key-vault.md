---
title: "Stocker des informations d’identification dans Azure Key Vault | Microsoft Docs"
description: "Découvrez comment stocker les informations d’identification des magasins de données utilisées par un coffre de clés Azure, qui peuvent être récupérées automatiquement par Azure Data Factory au moment de l’exécution."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: f7604e251bd62ec382ac9ace3de058e345abb863
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Stocker des informations d’identification dans Azure Key Vault

Vous pouvez stocker les informations d’identification des magasins de données dans un coffre de clés [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory récupère les informations d’identification lors de l’exécution d’une activité qui utilise le magasin de données. Seuls le [connecteur Dynamics](connector-dynamics-crm-office-365.md) et le [connecteur Salesforce](connector-salesforce.md) prennent en charge cette fonctionnalité.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Composants requis

Cette fonctionnalité repose sur l’identité de service de la fabrique de données. Découvrez comment cela fonctionne dans [Identité du service de fabrique de données](data-factory-service-identity.md) et vérifiez que votre fabrique de données est bien associée à une identité de service.

## <a name="steps"></a>Étapes

Pour référencer des informations d’identification stockées dans Azure Key Vault, vous devez :

1. [Récupérez l’identité de service de la fabrique de données](data-factory-service-identity.md#retrieve-service-identity) en copiant la valeur « ID d’application de l’identité du service » générée en même temps que votre fabrique.
2. Autoriser l’identité du service à accéder à votre coffre de clés Azure Key Vault. Dans votre coffre de clés -> Contrôle d’accès -> Ajouter -> recherchez l’ID d’application de l’identité du service pour lui attribuer au minimum une autorisation de **Lecture**. Cela permet à la fabrique désignée d’accéder au secret du coffre de clés.
3. Créer un service lié pointant vers votre coffre de clés Azure Key Vault. Reportez-vous à la section [Service lié Azure Key Vault](#azure-key-vault-linked-service).
4. Créer un service lié de magasin de données, dans lequel référencer le secret correspondant qui est stocké dans le coffre de clés. Reportez-vous à la section [Référencer les informations d’identification stockées dans le coffre de clés](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Service lié Azure Key Vault

Les propriétés suivantes sont prises en charge pour le service lié Azure Key Vault :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureKeyVault**. | Oui |
| baseUrl | Spécifiez l’URL d’Azure Key Vault. | Oui |

**Exemple :**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Référencer les informations d’identification stockées dans le coffre de clés

Les propriétés suivantes sont prises en charge lorsque vous configurez un champ dans le service lié qui référence un secret de coffre de clés :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type du champ doit être définie sur **AzureKeyVaultSecret**. | Oui |
| secretName | Nom du secret dans le coffre de clés Azure. | Oui |
| secretVersion | Version du secret dans le coffre de clés Azure.<br/>Si elle n’est pas spécifiée, la version la plus récente du secret est utilisée.<br/>Si elle est spécifiée, elle utilise la version spécifiée.| Non |
| store | Fait référence au service lié Azure Key Vault que vous utilisez pour stocker les informations d’identification. | Oui |

**Exemple : (voir la section « password »)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).