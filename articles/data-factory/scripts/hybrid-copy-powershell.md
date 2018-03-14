---
title: "Script PowerShell : copier des données en local dans Azure à l’aide de Data Factory | Microsoft Docs"
description: "Ce script PowerShell copie des données depuis une base de données SQL Server locale vers un stockage Blob Azure."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: bf84603c587b7bee5d0f69355ff9c1375ed7e60c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Utiliser PowerShell pour créer un pipeline de fabrique de données afin de copier des données en local dans Azure

Cet exemple de script PowerShell crée dans Azure Data Factory un pipeline qui copie les données d’une base de données SQL Server locale vers un stockage Blob Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis

- **SQL Server**. Dans cet exemple, vous allez utiliser une base de données SQL Server locale comme magasin de données **source**.
- **Compte Stockage Azure**. Dans cet exemple, le stockage Blob Azure est utilisé comme magasin de données de **destination/réception**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
- **Runtime d’intégration auto-hébergé**. Téléchargez le fichier MSI à partir du [centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=39717) et exécutez-le pour installer un runtime d’intégration auto-hébergé sur votre ordinateur.  

### <a name="create-sample-database-in-sql-server"></a>Créer un exemple de base de données dans SQL Server
1. Dans la base de données SQL Server locale, créez une table nommée **emp** en utilisant le script SQL suivant : 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Insérez des exemples de données dans la table :

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Exemple de script

> [!IMPORTANT]
> Ce script crée des fichiers JSON qui définissent des entités Data Factory (service lié, jeu de données et pipeline) sur votre disque dur dans le dossier c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Après avoir exécuté l’exemple de script, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées :

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Pour supprimer la fabrique de données du groupe de ressources, exécutez la commande suivante : 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Créer une fabrique de données. |
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | Chiffre les informations d’identification dans un service lié et génère une nouvelle définition de service lié à l’aide des informations d’identification chiffrées. 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Crée un service lié dans la fabrique de données. Un service lié rattache une banque de données ou une ressource de calcul à une fabrique de données. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Crée un jeu de données dans la fabrique de données. Un jeu de données représente les entrées/sorties pour une activité dans un pipeline. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Crée un pipeline dans la fabrique de données. Un pipeline contient une ou plusieurs activités effectuant une opération donnée. Dans ce pipeline, l’activité de copie a pour effet de copier les données d’un emplacement vers un autre dans le Stockage Blob Azure. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Crée une exécution pour le pipeline. En d’autres termes, exécute le pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtient les détails de l’exécution de l’activité dans le pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Factory sont à votre disposition dans [Exemples PowerShell pour Azure Data Factory](../samples-powershell.md).