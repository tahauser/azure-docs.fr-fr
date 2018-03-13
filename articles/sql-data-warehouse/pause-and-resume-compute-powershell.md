---
title: "Démarrage rapide : Suspendre et reprendre le calcul dans Azure SQL Data Warehouse - PowerShell | Microsoft Docs"
description: "Découvrez les tâches PowerShell qui interrompent le calcul d’un entrepôt Azure SQL Data Warehouse afin de réduire les coûts. Reprenez le calcul quand vous êtes prêt à utiliser l’entrepôt de données."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/25/2018
ms.author: barbkess
ms.openlocfilehash: b1f5c10fe294b44a9853f16e1866b77cf74a1479
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-powershell"></a>Démarrage rapide : Suspendre et reprendre le calcul pour un entrepôt Azure SQL Data Warehouse dans PowerShell
Utilisez PowerShell pour interrompre le calcul d’un entrepôt Azure SQL Data Warehouse afin de réduire les coûts. [Reprenez le calcul](sql-data-warehouse-manage-compute-overview.md) quand vous êtes prêt à utiliser l’entrepôt de données.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Ce didacticiel nécessite le module Azure PowerShell version 5.1.1 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour connaître la version dont vous disposez. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps.md). 

## <a name="before-you-begin"></a>Avant de commencer

Ce guide de démarrage rapide part du principe que vous disposez déjà d’un entrepôt de données SQL que vous pouvez suspendre et reprendre. Si vous devez en créer un, vous pouvez utiliser la section [Créer et connecter - Portail](create-data-warehouse-portal.md) pour créer un entrepôt de données nommé **mySampleDataWarehouse**. 

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

4. Notez le nom de l’entrepôt de données, utilisé comme nom de base de données. Notez également le nom du serveur et le groupe de ressources. Vous 
5.  les utiliserez dans les commandes de suspension et de reprise.
6. Si votre serveur est foo.database.windows.net, utilisez uniquement la première partie comme nom du serveur dans les applets de commande PowerShell. Dans l’image précédente, le nom complet est newserver-20171113.database.windows.net. Supprimez le suffixe et utilisez **newserver-20171113** comme nom de serveur dans l’applet de commande PowerShell.

## <a name="pause-compute"></a>Suspension du calcul
Pour réduire les coûts, vous pouvez interrompre et reprendre des ressources de calcul à la demande. Par exemple, si vous n’utilisez pas la base de données pendant la nuit et les week-ends, vous pouvez la suspendre à ces moments et la reprendre pendant la journée. Aucune ressource de calcul ne vous sera facturée tant que la base de données restera suspendue. Le stockage, en revanche, continue à occasionner des frais. 

Pour interrompre une base de données, utilisez l’applet de commande [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase.md). L’exemple suivant suspend un entrepôt de données nommé **mySampleDataWarehouse** hébergé sur un serveur nommé **newserver-20171113**. Le serveur est un groupe de ressources Azure nommé **myResourceGroup**.


```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Il redirige ensuite l’objet récupéré vers [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase). Les résultats sont stockés dans l'objet resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Reprise du calcul
Pour démarrer une base de données, utilisez l’applet de commande [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase). L’exemple suivant démarre une base de données nommée mySampleDataWarehouse hébergée sur un serveur nommé newserver-20171113. Le serveur est un groupe de ressources Azure nommé myResourceGroup.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Il redirige ensuite l’objet vers [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase.md) et stocke les résultats dans $resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous êtes facturé pour les Data Warehouse Units et les données stockées dans votre entrepôt de données. Ces ressources de calcul et de stockage sont facturées séparément. 

- Si vous souhaitez conserver les données dans le stockage, suspendez le calcul.
- Si vous voulez éviter des frais ultérieurs, vous pouvez supprimer l’entrepôt de données. 

Suivez ces étapes pour nettoyer les ressources selon vos besoins.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur votre entrepôt de données.

    ![Supprimer des ressources](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Pour suspendre le calcul, cliquez sur le bouton **Suspendre**. Quand l’entrepôt de données est suspendu, un bouton **Démarrer** est visible.  Pour reprendre le calcul, cliquez sur **Démarrer**.

2. Pour supprimer l’entrepôt de données afin de ne pas être facturé pour le calcul ou le stockage, cliquez sur **Supprimer**.

3. Pour supprimer le serveur SQL que vous avez créé, cliquez sur **mynewserver-20171113.database.windows.net**, puis sur **Supprimer**.  N’oubliez pas que la suppression du serveur supprime également toutes les bases de données qui lui sont attribuées.

4. Pour supprimer le groupe de ressources, cliquez sur **myResourceGroup**, puis sur **Supprimer le groupe de ressources**.


## <a name="next-steps"></a>Étapes suivantes
Vous venez de suspendre et de reprendre le calcul pour votre entrepôt de données. Pour en savoir plus sur Azure SQL Data Warehouse, continuez avec le didacticiel de chargement des données.

> [!div class="nextstepaction"]
>[Charger des données dans un entrepôt SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)