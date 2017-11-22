---
title: "Effectuer des opérations d’API Table Azure Cosmos DB avec PowerShell | Microsoft Docs"
description: "Guide pratique pour effectuer des opérations d’API Table Azure Cosmos DB avec PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d0f835db8a9fbe3833a9c7931ad1d8b4a778f016
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Effectuer des opérations d’API Table Azure Cosmos DB avec Azure PowerShell 

>[!NOTE]
>L’API Table Azure Cosmos DB fournit des fonctionnalités premium pour le stockage des tables, telles que la distribution globale clé en main, les opérations de lecture et d’écriture à faible latence, l’indexation secondaire automatique et un débit dédié. Bien que les commandes PowerShell dans cet article fonctionnent généralement pour l’API Table Azure Cosmos DB et Stockage Table Azure, cet article ne concerne que l’API Table Azure Cosmos DB. Si vous utilisez Stockage Table Azure, consultez [Effectuer des opérations sur Stockage Table Azure avec Azure PowerShell](table-storage-how-to-use-powershell.md).
>

L’API Table Azure Cosmos DB vous permet de stocker et d’interroger des jeux de données non relationnelles structurées volumineux. Les principaux composants du service sont les tables, les entités et les propriétés. une table est une collection d’entités. Une entité est un ensemble de propriétés. Chaque entité peut avoir jusqu’à 252 propriétés, qui sont toutes des paires nom-valeur. Cet article suppose que vous êtes déjà familiarisé avec les concepts de l’API Table Azure Cosmos DB. Pour plus d’informations, consultez [Présentation de l’API Table Azure Cosmos DB](table-introduction.md) et [Créer une application .NET à l’aide de l’API Table](create-table-dotnet.md).

Cet article sur les procédures décrit les opérations courantes liées à l’API Table. Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Création d’une table
> * Récupération d’une table
> * Ajout d’entités de table
> * Interrogation d’une table
> * Suppression d’entités de table

## <a name="prerequisites"></a>Composants requis

Ces exemples nécessitent le module Azure PowerShell version 4.4.0 ou ultérieure. Dans une fenêtre PowerShell, exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si aucune information n’est affichée ou que vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Une fois Azure PowerShell installé ou mis à jour, vous devez installer le module **AzureRmStorageTable**, qui contient les commandes de gestion des entités. Pour installer ce module, exécutez PowerShell en tant qu’administrateur et utilisez la commande **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

Puis, installez les assemblys Azure Cosmos DB en local pour pouvoir utiliser ces cmdlets PowerShell. Pour obtenir des instructions sur la procédure à suivre, consultez [Azure RM Storage Tables PowerShell module for Cosmos DB Tables](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/) (Module PowerShell Azure RM Storage Tables pour les tables de Cosmos DB).

Pour effectuer les exercices suivants, vous avez besoin d’un compte de base de données Azure Cosmos DB. Si vous n’en avez pas déjà un, créez un compte Azure Cosmos DB à l’aide du [portail Azure](https://portal.azure.com). Pour créer un compte de base de données, consultez [Azure Cosmos DB : Créer un compte de base de données](create-table-dotnet.md#create-a-database-account).

Récupérez le nom du compte de base de données et le groupe de ressource à partir du portail ; vous devrez insérer ces valeurs dans votre script pour accéder aux tables. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Créer une table ou référencer une table

Pour créer une table ou pour obtenir une référence à une table, utilisez **Get-AzureStorageTableTable**. Si vous appelez cette applet de commande avec le nom d’une table qui n’existe pas, elle crée une table portant ce nom et retourne une référence à la nouvelle table. Si la table existe, elle retourne une référence à la table existante.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Vous ne pouvez pas répertorier les tables dans le compte Azure Cosmos DB à l’aide de PowerShell, mais vous pouvez vous connecter au portail et consulter la table. À présent, nous allons voir comment gérer les entités dans la table.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Suppression d’une table 

PowerShell ne prend pas en charge la suppression de tables d’Azure Cosmos DB. Pour supprimer une table, accédez au [portail Azure](https://azure.portal.com), recherchez le compte Azure Cosmos DB que vous utilisez, puis recherchez la table et supprimez-la. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez créé un groupe de ressources, puis un compte Azure Cosmos DB dans ce groupe, vous pouvez supprimer toutes les ressources que vous avez créées dans cet exercice en supprimant le groupe de ressources. Cette commande supprime toutes les ressources contenues dans le groupe de ressources, ainsi que le groupe lui-même.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Cet article sur les procédures vous a présenté des opérations de l’API Table courantes réalisables avec PowerShell. Vous avez notamment appris à effectuer les tâches suivantes : 

> [!div class="checklist"]
> * Création d’une table
> * Récupération d’une table
> * Ajout d’entités de table
> * Interrogation d’une table
> * Suppression d’entités de table

Pour plus d’informations, consultez les articles suivants :

* [Working with Azure Storage Tables from PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) (Utilisation de Stockage Table Azure à partir de PowerShell)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.