---
title: "Démarrage rapide : Monter en puissance le calcul dans Azure SQL Data Warehouse - PowerShell | Microsoft Docs"
description: "Les tâches PowerShell permettent de monter en puissance les ressources de calcul en ajustant les unités de l’entrepôt de données."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Démarrage rapide : Mettre à l’échelle le calcul dans Azure SQL Data Warehouse avec PowerShell

Mettez à l’échelle le calcul dans Azure SQL Data Warehouse avec PowerShell. [Augmentez le calcul](sql-data-warehouse-manage-compute-overview.md) pour améliorer les performances, ou réduisez-le pour diminuer les coûts. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Ce didacticiel nécessite le module Azure PowerShell version 5.1.1 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour connaître la version dont vous disposez. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps.md). 

## <a name="before-you-begin"></a>Avant de commencer

Ce guide de démarrage rapide part du principe que vous disposez déjà d’un entrepôt de données SQL que vous pouvez mettre à l’échelle. Si vous devez en créer un, utilisez la section [Créer et connecter - Portail](create-data-warehouse-portal.md) pour créer un entrepôt de données nommé **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) et suivez les instructions à l’écran.

```powershell
Add-AzureRmAccount
```

Pour voir quel abonnement vous utilisez, exécutez [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Si vous devez utiliser un abonnement autre que l’abonnement par défaut, exécutez [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Rechercher des informations sur l’entrepôt de données

Recherchez le nom de la base de données, le nom du serveur et le groupe de ressources de l’entrepôt de données que vous souhaitez suspendre et reprendre. 

Suivez ces étapes pour rechercher des informations sur l’emplacement de votre entrepôt de données.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Cliquez sur **Bases de données SQL** dans la page de gauche du portail Azure.
3. Sélectionnez **mySampleDataWarehouse** dans la page **Bases de données SQL**. L’entrepôt de données s’ouvre. 

    ![Nom du serveur et groupe de ressources](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Notez le nom de l’entrepôt de données qui sera utilisé comme nom de base de données. Notez également le nom du serveur et le groupe de ressources. Vous les utiliserez dans les commandes de suspension et de reprise.
5. Si votre serveur est foo.database.windows.net, utilisez uniquement la première partie comme nom du serveur dans les applets de commande PowerShell. Dans l’image précédente, le nom complet est newserver-20171113.database.windows.net. Nous utilisons **newserver-20171113** comme nom de serveur dans l’applet de commande PowerShell.

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul

Dans SQL Data Warehouse, vous pouvez augmenter ou réduire les ressources de calcul en ajustant les unités Data Warehouse Unit. Le guide [Créer et connecter – Portail](create-data-warehouse-portal.md) a permis de créer **mySampleDataWarehouse** et de l’initialiser avec 400 DWU. Les étapes suivantes ajustent les DWU pour **mySampleDataWarehouse**.

Pour modifier les unités de l’entrepôt de données (DWU), utilisez l’applet de commande PowerShell [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase). L’exemple suivant définit les unités de l’entrepôt de données sur DW300 pour la base de données **mySampleDataWarehouse** qui est hébergée dans le groupe de ressources **myResourceGroup** sur le serveur  **mynewserver-20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>Vérifier l’état de la base de données

Pour afficher l’état actuel de l’entrepôt de données, utilisez l’applet de commande PowerShell [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase). Il obtient l’état de la base de données **mySampleDataWarehouse** dans le groupe de ressources **myResourceGroup** et le serveur **mynewserver-20171113.database.windows.net**.

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

Ce qui donnera quelque chose comme ceci :

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Vous pouvez ensuite vérifier **l’état** de la base de données. Dans ce cas, vous pouvez voir que la base de données est en ligne.  Lorsque vous exécutez cette commande, vous devriez recevoir une valeur d’état En ligne, Suspension, Reprise, Mise à l’échelle ou Suspendu.

## <a name="next-steps"></a>Étapes suivantes
Vous savez maintenant mettre à l’échelle le calcul pour votre entrepôt de données. Pour en savoir plus sur Azure SQL Data Warehouse, continuez avec le didacticiel de chargement des données.

> [!div class="nextstepaction"]
>[Charger des données dans un entrepôt SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
