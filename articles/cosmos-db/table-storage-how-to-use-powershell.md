---
title: "Effectuer des opérations sur Stockage Table Azure avec PowerShell | Microsoft Docs"
description: "Effectuer des opérations sur Stockage Table Azure avec PowerShell"
services: cosmos-db
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: 15a4ed2370598cb98565c48b4563bee3a4445827
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Effectuer des opérations sur Stockage Table Azure avec Azure PowerShell 

>[!NOTE]
>L’API Table Azure Cosmos DB fournit des fonctionnalités premium pour le stockage des tables, telles que la distribution globale clé en main, les opérations de lecture et d’écriture à faible latence, l’indexation secondaire automatique et un débit dédié. Bien que les commandes PowerShell dans cet article fonctionnent généralement pour l’API Table Azure Cosmos DB et Stockage Table Azure, cet article ne concerne que Stockage Table Azure. Si vous utilisez l’API Table Azure Cosmos DB, consultez [Opérations de l’API Table Azure Cosmos DB avec Azure PowerShell](table-powershell.md).
>

Stockage Table Azure est un magasin de données NoSQL que vous pouvez utiliser pour stocker et interroger de grands ensembles de données non relationnelles structurées. Les principaux composants du service sont les tables, les entités et les propriétés. une table est une collection d’entités. Une entité est un ensemble de propriétés. Chaque entité peut avoir jusqu’à 252 propriétés, qui sont toutes des paires nom-valeur. Cet article suppose que vous êtes déjà familiarisé avec les concepts du service Stockage Table Azure. Pour plus d’informations, consultez [Présentation du modèle de données du service de Table](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) et [Prise en main d’Azure Table Storage à l’aide de .NET](table-storage-how-to-use-dotnet.md).

Cet article sur les procédures décrit les opérations courantes liées à Stockage Table Azure. Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Création d’une table
> * Récupération d’une table
> * Ajout d’entités de table
> * Interrogation d’une table
> * Suppression d’entités de table
> * Suppression d’une table

Cet article sur les procédures vous montre comment créer un compte de stockage dans un nouveau groupe de ressources afin que vous puissiez le supprimer facilement quand vous avez terminé. Si vous préférez, vous pouvez utiliser un compte de stockage existant.

Ces exemples nécessitent le module Azure PowerShell version 4.4.0 ou ultérieure. Dans une fenêtre PowerShell, exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si aucune information n’est affichée ou que vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Une fois Azure PowerShell installé ou mis à jour, vous devez installer le module **AzureRmStorageTable**, qui contient les commandes de gestion des entités. Pour installer ce module, exécutez PowerShell en tant qu’administrateur et utilisez la commande **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Récupérer la liste des régions

Si vous ne savez pas quelle région utiliser, listez celles qui sont disponibles. Dans la liste, trouvez celle que vous souhaitez utiliser. Ces exemples utilisent **eastus**. Stockez cette valeur dans la variable **location** pour une utilisation ultérieure.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Stockez le nom du groupe de ressources dans une variable pour une utilisation ultérieure. Dans cet exemple, un groupe de ressources nommé *pshtablesrg* est créé dans la région *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Créer un compte de stockage

Créez un compte de stockage standard à usage général avec un stockage localement redondant à l’aide de [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser. Si un compte de stockage est utilisé, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Créer une table

Pour créer une table, utilisez l’applet de commande [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable). Dans cet exemple, la table est appelée `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Récupérer une liste de tables dans le compte de stockage

Récupérez la liste de tables dans le compte de stockage à l’aide de [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Récupérer une référence à une table spécifique

Pour effectuer des opérations sur une table, vous avez besoin d’une référence à cette table. Obtenir une référence à l’aide de [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Suppression d’une table

Pour supprimer une table, utilisez [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Cette applet de commande supprime la table, données comprises.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez créé un groupe de ressources et un compte de stockage au début de cet article sur les procédures, vous pouvez supprimer toutes les ressources que vous avez créées dans cet exercice en supprimant le groupe de ressources. Cette commande supprime toutes les ressources contenues dans le groupe de ressources, ainsi que le groupe lui-même.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Cet article sur les procédures vous a présenté des opérations Stockage Table Azure courantes réalisables avec PowerShell. Vous avez notamment appris à effectuer les tâches suivantes : 

> [!div class="checklist"]
> * Création d’une table
> * Récupération d’une table
> * Ajout d’entités de table
> * Interrogation d’une table
> * Suppression d’entités de table
> * Suppression d’une table

Pour plus d’informations, consultez les articles suivants :

* [Applets de commande PowerShell - Stockage](/powershell/module/azurerm.storage#storage)

* [Working with Azure Storage Tables from PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) (Utilisation de Stockage Table Azure à partir de PowerShell)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.