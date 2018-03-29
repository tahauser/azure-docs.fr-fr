---
title: Utilisation d’Azure PowerShell avec Azure Storage | Microsoft Docs
description: Apprenez à utiliser des applets de commande Azure PowerShell pour le stockage Azure.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: rogarana
ms.openlocfilehash: ae1c6628492de208f7c11e0b4e9d3133774ae184
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="using-azure-powershell-with-azure-storage"></a>Utilisation d'Azure PowerShell avec Azure Storage

Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Pour le stockage Azure, ces applets de commande se répartissent en deux catégories : le plan de contrôle et le plan de données. Les applets de commande de plan de contrôle sont utilisées pour gérer le compte de stockage, c’est-à-dire pour créer des comptes de stockage, définir des propriétés, supprimer des comptes de stockage, modifier les clés d’accès et ainsi de suite. Les applets de commande de plan de données sont utilisées pour gérer les données stockées *dans* le compte de stockage. Par exemple, le téléchargement d’objets blob, la création de partages de fichiers et l’ajout de messages à une file d’attente.

Cet article couvre les opérations courantes utilisant les applets de commande de plan de gestion pour gérer les comptes de stockage. Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Répertorier les comptes de stockage
> * Obtenir une référence à un compte de stockage existant
> * Créez un compte de stockage. 
> * Définir les propriétés du compte de stockage
> * Récupérer et régénérer les clés d’accès
> * Protéger l’accès à votre compte de stockage 
> * Activer l’analyse du stockage

Cet article fournit des liens vers plusieurs autres articles de PowerShell pour le stockage, notamment sur l’activation et l’accès à Storage Analytics, l’utilisation des applets de commande de plan de données et l’accès aux clouds indépendants Azure tels que le cloud de Chine, le cloud allemand et le cloud Azure Government.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Cet exercice requiert le module Azure PowerShell version 4.4 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Dans cet exercice, vous pouvez saisir les commandes dans une fenêtre PowerShell normale, ou utiliser [l’Environnement d'écriture de scripts intégré de Windows PowerShell (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) et saisir les commandes dans un éditeur, puis tester une ou plusieurs commandes à la fois en parcourant les exemples. Vous pouvez mettre en surbrillance les lignes que vous souhaitez exécuter et cliquer sur Exécuter la sélection pour exécuter uniquement ces commandes.

Pour plus d’informations sur les comptes de stockage, consultez la page [Introduction au stockage](storage-introduction.md) et [À propos des comptes de stockage Azure](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Répertorier les comptes de stockage de l’abonnement

Exécutez l’applet de commande [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) pour récupérer la liste des comptes de stockage dans l’abonnement actuel. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obtenir une référence à un compte de stockage

Vous avez ensuite besoin d’une référence à un compte de stockage. Vous pouvez créer un nouveau compte de stockage ou obtenir une référence à un compte de stockage existant. La section suivante illustre les deux méthodes. 

### <a name="use-an-existing-storage-account"></a>Utiliser un compte de stockage existant 

Pour récupérer un compte de stockage existant, vous avez besoin du nom du groupe de ressources et du nom du compte de stockage. Définissez les variables pour ces deux champs, puis utilisez l’applet de commande [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount). 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Vous avez maintenant $storageAccount, qui pointe vers un compte de stockage existant.

### <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Le script suivant montre comment créer un compte de stockage à usage général à l’aide de [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Après avoir créé le compte, récupérez son contexte, qui peut être utilisé dans les commandes suivantes au lieu de spécifier l’authentification à chaque appel.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

Le script utilise les applets de commande PowerShell suivantes : 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) -- récupère une liste des emplacements valides. L’exemple utilise `eastus` pour l’emplacement.

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) -- crée un nouveau groupe de ressources. Un groupe de ressources est un conteneur logique dans lequel vos ressources Azure sont déployées et gérées. Nous avons appelé le nôtre `teststoragerg`. 

*   [Nouveau-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) -- crée le compte de stockage réel. L’exemple utilise `testpshstorage`.

Le nom de référence (SKU) indique le type de réplication pour le compte de stockage, comme LRS (stockage localement redondant). Pour en savoir plus, consultez [Réplication de Stockage Azure](storage-redundancy.md).

> [!IMPORTANT]
> Le nom de votre compte de stockage doit être unique dans Azure et être en minuscules. Pour connaître les conventions et restrictions relatives aux attributions de noms, consultez la page [Affectation de noms et références aux conteneurs, objets BLOB et métadonnées](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Vous disposez maintenant d’un compte de stockage et d’une référence à celui-ci. 

## <a name="manage-the-storage-account"></a>Gérer le compte de stockage

Maintenant que vous avez une référence à un compte de stockage ou un compte de stockage existant, la section suivante présente certaines des commandes que vous pouvez utiliser pour gérer votre compte de stockage.

### <a name="storage-account-properties"></a>Propriétés du compte de stockage

Pour modifier les paramètres pour un compte de stockage, utilisez [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Si vous ne pouvez pas modifier l’emplacement d’un compte de stockage, ou le groupe de ressources dans lequel il réside, vous pouvez modifier la plupart des autres propriétés. La liste suivante décrit certaines des propriétés que vous pouvez modifier à l’aide de PowerShell.

* Le **domaine personnalisé** affecté au compte de stockage.

* Les **balises** affectées au compte de stockage. Les balises sont souvent utilisées pour catégoriser les ressources à des fins de facturation.

* Le nom de référence (**SKU**) est le paramètre de réplication pour le compte de stockage, comme LRS (stockage localement redondant). Par exemple, vous pouvez passer de Standard\_LRS à Standard\_GRS ou Standard\_RAGRS. Notez que vous ne pouvez pas passer du stockage Standard\_ZRS ou Premium\_LRS à d’autres références (SKU), ou modifier d’autres références sur celles-ci.

* Le **niveau d’accès** pour les comptes de stockage d’objets blob. La valeur du niveau d’accès est définie sur **chaud** ou **froid**, et vous permet de réduire les coûts en sélectionnant le niveau d’accès qui s’aligne avec l’utilisation du compte de stockage. Pour plus d’informations, consultez [Niveaux de stockage chauds et froid et d’archivage](../blobs/storage-blob-storage-tiers.md).

* Autorisez uniquement le trafic HTTPS. 

### <a name="manage-the-access-keys"></a>Gérer les clés d’accès

Un compte Azure Storage est fourni avec deux clés de compte. Pour récupérer les clés, utilisez [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Cet exemple récupère la première clé. Pour récupérer l’autre, utilisez `Value[1]` au lieu de `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Pour régénérer la clé, utilisez [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Pour régénérer l’autre clé, utilisez `key2` comme nom de clé à la place de `key1`.

Régénérez un de vos clés, puis récupérez-la à nouveau pour voir la nouvelle valeur.

> [!NOTE] 
> Vous devez planifier soigneusement la régénération de la clé pour un compte de stockage de production. La régénération d’une ou des deux clés invalidera l’accès pour n’importe quelle application utilisant la clé qui a été régénérée. Pour plus d’informations, consultez [Regénérer les clés d’accès au stockage](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Suppression d'un compte de stockage 

Pour supprimer un compte de stockage, utilisez [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Lorsque vous supprimez un compte de stockage, toutes les ressources stockées dans le compte sont également supprimées. Si vous supprimez accidentellement un compte, appelez le Support immédiatement et ouvrez un ticket pour restaurer le compte de stockage. La récupération de vos données n’est pas garantie, mais elle fonctionne parfois. Ne créez pas un compte de stockage portant le même nom que l’ancien tant que le ticket de support n’a pas été résolu. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Protéger votre compte de stockage à l’aide de réseaux virtuels et de pare-feu

Par défaut, tous les comptes de stockage sont accessibles par n’importe quel réseau ayant accès à Internet. Toutefois, vous pouvez configurer des règles de réseau pour autoriser uniquement les applications de réseaux virtuels spécifiques à accéder à un compte de stockage. Pour plus d’informations, consultez [Configurer Pare-feu et réseaux virtuels dans Stockage Azure](storage-network-security.md). 

L’article explique comment gérer ces paramètres à l’aide des applets de commande PowerShell suivantes :
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Utiliser l’analyse du stockage  

[Azure Storage Analytics](storage-analytics.md) comprend [les métriques d’analyse de stockage](/rest/api/storageservices/about-storage-analytics-metrics) et [la journalisation des analyses de stockage](/rest/api/storageservices/about-storage-analytics-logging). 

Les **métriques d’analyse de stockage** servent à collecter des mesures pour vos comptes de stockage Azure que vous pouvez utiliser pour surveiller l’intégrité d’un compte de stockage. Les métriques peuvent être activées pour les objets Blob, les fichiers, les tables et les files d’attente.

La **journalisation de l’analyse du stockage** se produit côté serveur. Elle vous permet d’enregistrer les détails des demandes ayant réussi et des demandes ayant échoué dans votre compte de stockage. Ces journaux permettent d'afficher les détails des opérations de lecture, d'écriture et de suppression sur vos tables, vos files d'attente et vos objets blob, ainsi que la raison de l'échec de certaines demandes. La journalisation n’est pas disponible pour les fichiers Azure.

Vous pouvez configurer la surveillance en utilisant le [portail Azure](https://portal.azure.com) ou PowerShell, ou par programmation en utilisant la bibliothèque cliente de stockage. 

> [!NOTE]
> Vous pouvez activer l’analyse par minute à l’aide de PowerShell. Cette fonctionnalité n’est pas disponible dans le portail.
>

* Pour savoir comment activer et afficher les données de métriques de stockage à l’aide de PowerShell, consultez [Activation des données de métriques de visualisation et de métriques pour le stockage Azure](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Pour savoir comment activer et récupérer des données de journalisation du stockage à l’aide de PowerShell, consultez [Comment activer Storage Logging avec PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) et [Recherche des données de journal Storage Logging](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Pour plus d’informations sur l’utilisation de Storage Metrics et de la journalisation du stockage pour résoudre les problèmes de stockage, consultez la page [Surveiller, diagnostiquer et résoudre les problèmes liés à Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Gérer les données dans le compte de stockage

Maintenant que vous savez comment gérer votre compte de stockage avec PowerShell, consultez les articles suivants pour découvrir comment accéder aux objets de données dans le compte de stockage.

* [Comment gérer les objets Blob avec PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Comment gérer les fichiers avec PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Comment gérer les files d’attente avec PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Effectuer des opérations sur Stockage Table Azure avec PowerShell](../../cosmos-db/table-storage-how-to-use-powershell.md)

L’API Table Azure Cosmos DB fournit des fonctionnalités premium pour le stockage des tables, telles que la distribution globale clé en main, les opérations de lecture et d’écriture à faible latence, l’indexation secondaire automatique et un débit dédié. 

* Pour plus d’informations, consultez [API Table Azure Cosmos DB](../../cosmos-db/table-introduction.md). 
* Pour découvrir comment utiliser PowerShell pour effectuer des opérations d’API Table Azure Cosmos DB, consultez [Effectuer des opérations d’API Table Azure Cosmos DB avec PowerShell](../../cosmos-db/table-powershell.md).

## <a name="independent-cloud-deployments-of-azure"></a>Déploiements de cloud indépendant d’Azure

La plupart des gens utilisent le cloud public Azure pour leur déploiement Azure global. Pour des raisons de souveraineté, entre autres, il existe également des déploiements indépendants de Microsoft Azure. Ces déploiements indépendants sont appelés « environnements ». Voici les environnements disponibles :

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Cloud Azure de Chine géré par 21Vianet en Chine](http://www.windowsazure.cn/)
* [Cloud Azure allemand](../../germany/germany-welcome.md)

Pour découvrir comment accéder à ces clouds et à leur stockage avec PowerShell, consultez [Gestion du stockage dans les clouds indépendants d’Azure avec PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez créé un groupe de ressources et un compte de stockage pour cet exercice, vous pouvez supprimer toutes les ressources que vous avez créées en supprimant le groupe de ressources. Cette opération supprime également toutes les ressources contenues dans le groupe. Dans le cas présent, le compte de stockage créé et le groupe de ressources sont supprimés.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Étapes suivantes

Cet article couvre les opérations courantes utilisant les applets de commande de plan de gestion pour gérer les comptes de stockage. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Répertorier les comptes de stockage
> * Obtenir une référence à un compte de stockage existant
> * Créez un compte de stockage. 
> * Définir les propriétés du compte de stockage
> * Récupérer et régénérer les clés d’accès
> * Protéger l’accès à votre compte de stockage 
> * Activer l’analyse du stockage

Cet article fournit également des liens vers plusieurs autres articles, notamment sur la gestion des objets de données, l’activation de Storage Analytics et l’accès aux clouds indépendants Azure tels que le cloud de Chine, le cloud allemand et le cloud Azure Government. Voici d’autres articles connexes et ressources de référence :

* [Applets de commande PowerShell pour le plan de contrôle du stockage Azure](/powershell/module/AzureRM.Storage/)
* [Applets de commande PowerShell pour le plan de données du stockage Azure](/powershell/module/azure.storage/)
* [Référence Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)
