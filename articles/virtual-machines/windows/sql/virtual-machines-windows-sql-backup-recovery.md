---
title: "Sauvegarde et restauration pour SQL Server | Microsoft Docs"
description: "Décrit les considérations relatives à la sauvegarde et à la restauration des bases de données SQL Server s’exécutant sur des machines virtuelles Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 16fef048e7c795f3d21fbc4185f6ba31bbc885fb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure
## <a name="overview"></a>Vue d'ensemble
Stockage Azure conserve 3 copies de chaque disque de machine virtuelle Azure pour garantir la protection contre la perte de données ou l’altération physique des données. Par conséquent, contrairement aux configurations locales, vous n’avez pas besoin de vous en occuper. Vous devez cependant toujours sauvegarder vos bases de données SQL Server pour les protéger des erreurs des applications ou des utilisateurs (par exemple l’insertion de données incorrectes ou la suppression d’une table), et pour pouvoir restaurer à un point dans le temps.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Pour SQL Server s’exécutant sur des machines virtuelles Azure, vous pouvez utiliser les techniques de sauvegarde et restauration en mode natif en utilisant des disques attachés pour la destination des fichiers de sauvegarde. Néanmoins, le nombre de disques que vous pouvez attacher à une machine virtuelle Azure est limité et basé sur la [taille de la machine virtuelle](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Il convient également de tenir compte du traitement de la gestion des disques.

Depuis SQL Server 2014, vous pouvez sauvegarder et restaurer du contenu dans le stockage d’objets blob Microsoft Azure. SQL Server 2016 apporte également des améliorations pour cette option. Par ailleurs, pour les fichiers de base de données stockés dans le stockage d’objets blob Microsoft Azure, SQL Server 2016 propose une option pour effectuer des sauvegardes quasi instantanées et des restaurations rapides à l’aide d’instantanés Azure. Cet article fournit une vue d’ensemble de ces options. Pour plus d’informations, consultez [Sauvegarde et restauration SQL Server avec le service Stockage Blob Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

> [!NOTE]
> Pour obtenir une présentation des options permettant de sauvegarder des bases de données très volumineuses, voir [Multi-Terabyte SQL Server Database Backup Strategies for Azure Virtual Machines](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx)(en anglais).
> 
> 

Les sections ci-dessous contiennent des informations propres aux différentes versions de SQL Server prises en charge sur une machine virtuelle Azure.

## <a name="sql-server-virtual-machines"></a>Machines virtuelles SQL Server
Lorsque votre instance SQL Server est en cours d’exécution sur une machine virtuelle Azure, vos fichiers de base de données se trouvent déjà sur les disques de données dans Azure. Ces disques se situent dans le stockage d’objets blob Azure. Par conséquent, les raisons de sauvegarder votre base de données et l’approche que vous adoptez diffèrent légèrement. Examinons le code suivant. 

* Vous n’êtes plus tenu d’effectuer des sauvegardes de base de données pour assurer la protection contre les défaillances matérielles ou de média, car Microsoft Azure propose cette protection dans le cadre du service Microsoft Azure.
* Vous devez cependant effectuer des sauvegardes de base de données pour garantir une protection contre les erreurs d’utilisateur, ou à des fins d’archivage ou d’administration, ou pour des besoins réglementaires.
* Vous pouvez stocker le fichier de sauvegarde directement dans Azure. Pour plus d’informations, consultez les sections suivantes relatives aux différentes versions de SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016
Microsoft SQL Server 2016 prend en charge les fonctionnalités de [sauvegarde et de restauration d’objets blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) disponibles dans SQL Server 2014. Elle inclut également les améliorations suivantes :

| Améliorations en 2016 | Détails |
| --- | --- |
| **Entrelacement** |Lors de la sauvegarde dans le stockage d’objets blob Microsoft Azure, SQL Server 2016 prend en charge la sauvegarde dans plusieurs objets blob, ce qui permet de sauvegarder des bases de données volumineuses allant jusqu’à 12,8 To. |
| **Sauvegarde instantanée** |Avec l’utilisation d’instantanés Azure, la fonctionnalité Sauvegarde instantanée de fichier SQL Server fournit des sauvegardes quasi instantanées et des restaurations rapides des fichiers de base de données stockés à l’aide du service de stockage d’objets blob Azure. Cette fonctionnalité vous permet de simplifier vos stratégies de sauvegarde et de restauration. La sauvegarde instantanée de fichier prend également en charge la limite de restauration dans le temps. Pour plus d'informations, consultez [File-Snapshot Backups for Database Files in Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx)(en anglais). |
| **Planification de sauvegarde gérée** |La sauvegarde de SQL Server gérée dans Azure prend désormais en charge des planifications personnalisées. Pour plus d’informations, voir [SQL Server Managed Backup to Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)(en anglais). |

Pour suivre un didacticiel sur les fonctionnalités de SQL Server 2016 en cas d’utilisation du stockage d’objets blob Azure, voir [Tutorial: Using the Microsoft Azure Blob storage service with SQL Server 2016 databases](https://msdn.microsoft.com/library/dn466438.aspx)(en anglais).

## <a name="sql-server-2014"></a>SQL Server 2014
SQL Server 2014 inclut les améliorations suivantes :

1. **Sauvegarde et restauration dans Azure**:
   
   * *sauvegarde de SQL Server vers une URL* est désormais prise en charge dans SQL Server Management Studio. L’option de sauvegarde vers Azure est désormais disponible lors de l’utilisation d’une tâche de sauvegarde ou de restauration, ou de l’Assistant Plan de maintenance dans SQL Server Management Studio. Pour plus d’informations, voir [SQL Server Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)(en anglais).
   * *sauvegarde de SQL Server gérée dans Azure* dispose d’une nouvelle fonctionnalité qui permet d’automatiser la gestion des sauvegardes. Cela est particulièrement utile pour automatiser la gestion des sauvegardes pour les instances SQL Server 2014 s’exécutant sur une machine virtuelle Azure. Pour plus d’informations, voir [SQL Server Managed Backup to Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)(en anglais).
   * La *Sauvegarde automatisée* fournit une automatisation supplémentaire pour activer automatiquement la *sauvegarde de SQL Server gérée dans Azure* dans l’ensemble des bases de données nouvelles et existantes d’une machine virtuelle SQL Server dans Azure. Pour plus d’informations, voir [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).
   * Pour une vue d’ensemble de toutes les options de sauvegarde SQL Server 2014 dans Azure, voir [SQL Server Backup and Restore with Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)(en anglais).
2. **Chiffrement**: SQL Server 2014 prend en charge le chiffrement des données lors de la création d’une sauvegarde. Il gère plusieurs algorithmes de chiffrement ainsi que l’utilisation d’un certificat ou d’une clé asymétrique. Pour plus d’informations, voir [Chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012
Pour plus d'informations sur la sauvegarde et la restauration dans SQL Server 2012, consultez [Backup and Restore of SQL Server Databases (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)(en anglais).

Depuis la mise à jour cumulative 2 de SQL Server 2012 SP1, vous pouvez sauvegarder dans le service de stockage d’objets blob Azure et restaurer à partir de ce service. Cette amélioration permet de sauvegarder des bases de données SQL Server sur un serveur SQL Server s’exécutant sur une machine virtuelle Azure ou une instance locale. Pour plus d’informations, voir [SQL Server Backup and Restore with Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)(en anglais).

Voici certains avantages liés à l’utilisation du service de stockage d’objets blob Azure : possibilité de contourner la limite de 16 disques attachés, facilité de gestion, disponibilité directe du fichier de sauvegarde sur une autre instance SQL Server s’exécutant sur une machine virtuelle Azure ou sur des instances locales à des fins de migration ou de récupération d’urgence. Pour obtenir la liste complète des avantages liés à l’utilisation d’un service de stockage d’objets blob Azure pour les sauvegardes SQL Server, voir la section *Avantages (Benefits)* de la page [SQL Server Backup and Restore with Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)(en anglais).

Pour prendre connaissance des recommandations et des informations de dépannage, voir [Backup and Restore Best Practices (Azure Blob Storage Service)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)(en anglais).

## <a name="sql-server-2008"></a>SQL Server 2008
Pour la sauvegarde et la restauration de SQL Server dans SQL Server 2008 R2, voir [Sauvegarde et restauration de bases de données dans SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Pour la sauvegarde et la restauration de SQL Server dans SQL Server 2008, voir [Sauvegarde et restauration de bases de données dans SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Étapes suivantes
Si vous planifiez le déploiement de SQL Server dans une machine virtuelle Azure, des conseils de configuration sont disponibles dans le didacticiel suivant : [Configuration d’une machine virtuelle SQL Server sur Azure avec Azure Resource Manager](virtual-machines-windows-portal-sql-server-provision.md).

Même si la sauvegarde et la restauration permettent de migrer vos données, il existe des chemins de migration de données potentiellement plus simples vers SQL Server sur une machine virtuelle Azure. Pour une description complète des options de migration et des recommandations connexes, voir [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

Passez en revue les autres [ressources liées à l’exécution de SQL Server dans des machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

