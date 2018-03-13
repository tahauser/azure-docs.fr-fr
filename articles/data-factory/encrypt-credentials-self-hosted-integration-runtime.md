---
title: "Chiffrer des informations d’identification dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment chiffrer et stocker des informations d’identification pour vos magasins de données locaux sur une machine avec runtime d’intégration auto-hébergé."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 0f42d971fcf21d0f719468a8c10ff637f2e523f9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Chiffrer des informations d’identification pour vos magasins de données locaux dans Azure Data Factory
Vous pouvez chiffrer et stocker des informations d’identification pour vos magasins de données locaux (services associés avec des informations sensibles) sur une machine avec runtime d’intégration auto-hébergé. 

Vous passez un fichier de définition JSON avec des informations d’identification à l’ <br/>applet de commande [**New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) pour générer un fichier de définition JSON de sortie avec les informations d’identification chiffrées. Ensuite, utilisez la définition JSON mise à jour pour créer les services liés.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

## <a name="author-sql-server-linked-service"></a>Rédiger un service lié SQL Server
Créez un fichier JSON nommé **StorageLinkedService.json** dans n’importe quel dossier avec le contenu suivant :  

Remplacez `<servername>`, `<databasename>`, `<username>`, et `<password>` par des valeurs pour votre serveur SQL Server avant d’enregistrer le fichier. Et remplacez `<integration runtime name>` par le nom de votre runtime d’intégration. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Chiffrer des informations d’identification
Pour chiffrer les données sensibles de la charge utile JSON sur un runtime d’intégration auto-hébergé local, exécutez **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** et passez la charge utile JSON. Cette applet de commande garantit le chiffrement des informations d’identification à l’aide de DPAPI et leur stockage local sur le nœud de runtime d’intégration auto-hébergé. La charge utile de sortie peut être redirigée vers un autre fichier JSON (dans ce cas, « encryptedLinkedService.json ») qui contient les informations d’identification chiffrées.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Utiliser le fichier JSON avec des informations d’identification chiffrées
À présent, utilisez le fichier JSON de sortie de la commande précédente contenant les informations d’identification chiffrées pour configurer le **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les considérations de sécurité pour le déplacement des données, consultez [Data movement security considerations](data-movement-security-considerations.md) (Considérations de sécurité pour le déplacement des données).

