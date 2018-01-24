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
ms.date: 12/14/2017
ms.author: jingwang
ms.openlocfilehash: 145c2bc0556010389e78e523fde6fd4b9063f930
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Stocker des informations d’identification dans Azure Key Vault

Vous pouvez stocker les informations d’identification des magasins de données dans un coffre de clés [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory récupère les informations d’identification lors de l’exécution d’une activité qui utilise le magasin de données.

Actuellement, le [connecteur Dynamics](connector-dynamics-crm-office-365.md), le [connecteur Salesforce](connector-salesforce.md) et quelques nouveaux connecteurs prennent en charge cette fonctionnalité. D’autres sont à prévoir. Vous pouvez consulter la rubrique de chaque connecteur pour plus d’informations. Une note s’affichera dans la description des champs de mot de passe qui prennent en charge cette fonctionnalité : « *Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans le fichier de définition d’application, ou stocker le mot de passe dans Azure Key Vault et laisser l’activité de copie en tirer (pull) les données lors de la copie. Pour plus d’informations, consultez la page Stocker des informations d’identification dans Key Vault.* »

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>configuration requise

Cette fonctionnalité repose sur l’identité de service de la fabrique de données. Découvrez comment cela fonctionne dans [Identité du service de fabrique de données](data-factory-service-identity.md) et vérifiez que votre fabrique de données est bien associée à une identité de service.

## <a name="steps"></a>Étapes

Pour référencer des informations d’identification stockées dans Azure Key Vault, vous devez :

1. [Récupérez l’identité de service de la fabrique de données](data-factory-service-identity.md#retrieve-service-identity) en copiant la valeur « ID d’application de l’identité du service » générée en même temps que votre fabrique.
2. Autoriser l’identité du service à accéder à votre coffre de clés Azure Key Vault. Dans votre coffre de clés -> Contrôle d’accès -> Ajouter -> recherchez l’ID d’application de l’identité du service pour lui attribuer au minimum une autorisation de **Lecture**. Cela permet à la fabrique désignée d’accéder au secret du coffre de clés.
3. Créer un service lié pointant vers votre coffre de clés Azure Key Vault. Reportez-vous à la section [Service lié Azure Key Vault](#azure-key-vault-linked-service).
4. Créer un service lié de magasin de données, dans lequel référencer le secret correspondant qui est stocké dans le coffre de clés. Reportez-vous à la section [Référencer les informations d’identification stockées dans le coffre de clés](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Service lié Azure Key Vault

Les propriétés suivantes sont prises en charge pour le service lié Azure Key Vault :

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureKeyVault**. | OUI |
| baseUrl | Spécifiez l’URL d’Azure Key Vault. | OUI |

**Exemple :**

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

| Propriété | DESCRIPTION | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du champ doit être définie sur **AzureKeyVaultSecret**. | OUI |
| secretName | Nom du secret dans le coffre de clés Azure. | OUI |
| secretVersion | Version du secret dans le coffre de clés Azure.<br/>Si elle n’est pas spécifiée, la version la plus récente du secret est utilisée.<br/>Si elle est spécifiée, elle utilise la version spécifiée.| Non  |
| store | Fait référence au service lié Azure Key Vault que vous utilisez pour stocker les informations d’identification. | OUI |

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

## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).