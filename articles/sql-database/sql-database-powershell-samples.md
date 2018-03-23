---
title: Exemples de scripts Azure PowerShell pour SQL Database | Microsoft Docs
description: Exemples de scripts Azure PowerShell vous permettant de créer et de gérer des serveurs, des pools élastiques, des bases de données et des pare-feu Azure SQL Database.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: tysonn
tags: azure-service-management
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 06/23/2017
ms.author: carlrab
ms.openlocfilehash: 63be3858bf159195acb99821a17447b7f05d7917
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemples Azure PowerShell pour Azure SQL Database

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure SQL Database.

| |  |
|---|---|
|**Créer une base de données unique et un pool élastique**||
| [Créer une base de données unique et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell crée une base de données SQL Azure unique et configure une règle de pare-feu au niveau du serveur. |
| [Créer des pools élastiques et déplacer les bases de données regroupées](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell crée des pools élastiques Azure SQL Database, déplace des bases de données mises en pool et modifie les niveaux de performances.|
|**Configurer la géoréplication et le basculement**||
| [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure la géoréplication active pour une base de données SQL Azure unique et la fait basculer vers le réplica secondaire. |
| [Configurer et basculer une base de données regroupée à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure la géoréplication active pour une base de données SQL Azure dans un pool élastique SQL et la fait basculer vers le réplica secondaire. |
| [Configurer et basculer un groupe de basculement pour une base de données unique (préversion)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell configure un groupe de basculement pour une instance de serveur de base de données SQL Azure, ajoute une base de données au groupe de basculement et le bascule vers le serveur secondaire |
|**Mettre à l’échelle une base de données unique et un pool élastique**||
| [Mettre à l’échelle une base de données unique](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell surveille les mesures de performances d’une base de données SQL Azure, l’adapte à un niveau de performances supérieur et crée une règle d’alerte sur l’une des mesures de performances. |
| [Mettre à l’échelle un pool élastique](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell surveille les mesures de performances d’un pool élastique Azure SQL Database, l’adapte à un niveau de performances supérieur et crée une règle d’alerte sur l’une des mesures de performances.  |
| **Audit et détection des menaces** |
| [Configurer l’audit et la détection des menaces](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell configure les stratégies d’audit et de détection des menaces pour une base de données SQL Azure. |
| **Restaurer, copier et importer une base de données**||
| [Restaurer une base de données](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell restaure une base de données SQL Azure à partir d’une sauvegarde géoredondante et restaure une base de données SQL Azure supprimée dans la dernière sauvegarde. |
| [Copier une base de données sur un nouveau serveur](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell crée une copie d’une base de données SQL Azure existante sur un nouveau serveur SQL Azure. |
| [Importer une base de données à partir d’un fichier bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell importe une base de données vers un serveur SQL Azure à partir d’un fichier bacpac. |
| **Synchroniser des données entre des bases de données**||
| [Synchroniser des données entre des bases de données SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell configure la synchronisation des données entre plusieurs bases de données Azure SQL. |
| [Synchroniser des données entre une base de données SQL et un serveur SQL local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell configure la synchronisation des données entre une base de données Azure SQL et une base de données SQL Server locale. |
| [Mettre à jour le schéma de synchronisation SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ce script PowerShell ajoute ou supprime des éléments du schéma de synchronisation Data Sync. |
|||
