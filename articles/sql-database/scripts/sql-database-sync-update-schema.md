---
title: "Exemple PowerShell : Mettre à jour le schéma de synchronisation SQL Data Sync | Microsoft Docs"
description: "Exemple de script Azure PowerShell pour mettre à jour le schéma de synchronisation pour SQL Data Sync"
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: 66bf084f585b86979e6521321daf466c571de10c
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Utiliser PowerShell pour mettre à jour le schéma de synchronisation dans un groupe de synchronisation existant

Cet exemple PowerShell met à jour le schéma de synchronisation dans un groupe de synchronisation existant. Lorsque vous synchronisez plusieurs tables, ce script vous permet de mettre à jour efficacement le schéma de synchronisation.

Cet exemple illustre l’utilisation du script **UpdateSyncSchema**, qui est disponible sur GitHub en tant que [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync (Préversion)](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Conditions préalables

Cet exemple requiert le module Azure PowerShell version 4.2 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
 
Exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="examples"></a>Exemples

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exemple 1 : Ajouter toutes les tables au schéma de synchronisation

L’exemple suivant actualise le schéma de base de données et ajoute toutes les tables valides de la base de données Hub dans le schéma de synchronisation.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exemple 2 : Ajouter et supprimer des tables et des colonnes

L’exemple suivant ajoute `[dbo].[Table1]` et `[dbo].[Table2].[Column1]` au schéma de synchronisation et supprime `[dbo].[Table3]`.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Paramètres de script

Le script **UpdateSyncSchema** contient les paramètres suivants :

| Paramètre | Notes |
|---|---|
| $SubscriptionId | Abonnement dans lequel le groupe de synchronisation est créé. |
| $ResourceGroupName | Groupe de ressources dans lequel le groupe de synchronisation est créé.|
| $ServerName | Nom de serveur de la base de données Hub.|
| $DatabaseName | Nom de la base de données Hub. |
| $SyncGroupName | Nom du groupe de synchronisation. |
| $MemberName | Spécifiez le nom du membre si vous voulez charger le schéma de base de données du membre de synchronisation au lieu de la base de données Hub. Si vous voulez charger le schéma de base de données à partir du Hub, laissez ce paramètre vide. |
| $TimeoutInSeconds | Délai d’attente pendant l’actualisation du schéma de base de données par le script. La valeur par défaut est de 900 secondes. |
| $RefreshDatabaseSchema | Spécifiez si le script doit actualiser le schéma de base de données. Si votre schéma de base de données a changé par rapport à la configuration précédente (par exemple, si vous avez ajouté une nouvelle table ou la nouvelle colonne), vous devez actualiser le schéma avant de reconfigurer il. La valeur par défaut est false. |
| $AddAllTables | Si cette valeur est true, toutes les tables et colonnes valides sont ajoutées au schéma de synchronisation. Les valeurs de $TablesAndColumnsToAdd et $TablesAndColumnsToRemove sont ignorées. |
| $TablesAndColumnsToAdd | Spécifiez les tables ou les colonnes à ajouter au schéma de synchronisation. Chaque nom de table ou de colonne doit être entièrement délimité avec le nom du schéma. Par exemple : `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Plusieurs noms de table ou de colonne peuvent être spécifiés et séparés par des virgules (,). |
| $TablesAndColumnsToRemove | Spécifiez les tables ou les colonnes à supprimer du schéma de synchronisation. Chaque nom de table ou de colonne doit être entièrement délimité avec le nom du schéma. Par exemple : `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Plusieurs noms de table ou de colonne peuvent être spécifiés et séparés par des virgules (,). |
|||

## <a name="script-explanation"></a>Explication du script

Le script **UpdateSyncSchema** utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Renvoie des informations sur un groupe de synchronisation. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Met à jour un groupe de synchronisation. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Renvoie des informations sur un membre de synchronisation. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Renvoie des informations sur un schéma de synchronisation. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Met à jour un schéma de synchronisation. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation d’Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell sur la page [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).

Pour plus d’informations sur SQL Data Sync, consultez :

-   [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](../sql-database-sync-data.md)
-   [Configurer Azure SQL Data Sync](../sql-database-get-started-sql-data-sync.md)
-   [Bonnes pratiques pour Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
-   [Surveiller Azure SQL Data Sync avec OMS Log Analytics](../sql-database-sync-monitor-oms.md)
-   [Résoudre les problèmes liés à Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md)

-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](sql-database-sync-data-between-sql-databases.md)
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](sql-database-sync-data-between-azure-onprem.md)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](../sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
