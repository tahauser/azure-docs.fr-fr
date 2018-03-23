---
title: Copie d’une base de données SQL Azure | Documents Microsoft
description: Créez une copie cohérente au niveau transactionnel d’une base de données Azure SQL Database existante sur le même serveur ou sur un autre serveur.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 06/15/2017
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: c4a3836cfd0bbbb8d26a42af2980deab5f9d7681
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="copy-an-azure-sql-database"></a>Copie d'une base de données SQL Azure

Azure SQL Database fournit plusieurs méthodes pour créer une copie cohérente au niveau transactionnel de la base de données SQL Azure existante sur le même serveur ou sur un autre serveur. Vous pouvez copier une base de données SQL à l’aide du portail Azure, de PowerShell ou de T-SQL. 

## <a name="overview"></a>Vue d'ensemble

La copie de la base de données est une capture instantanée de la base de données source au moment de la demande de la copie. Vous pouvez sélectionner le même serveur ou un autre serveur, son niveau de service et le niveau de performances, ou un niveau de performance différent dans le même niveau de service (édition). Une fois la copie terminée, elle devient une base de données indépendante et entièrement fonctionnelle. À ce stade, vous pouvez la mettre à niveau ou la rétrograder vers n’importe quelle édition. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.  

## <a name="logins-in-the-database-copy"></a>Connexions dans la copie de la base de données

Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.  

Lorsque vous copiez une base de données vers un autre serveur logique, le principal de sécurité sur le nouveau serveur devient le propriétaire de la base de données sur la nouvelle base de données. Si vous utilisez des [utilisateurs de base de données à relation contenant-contenu](sql-database-manage-logins.md) pour accéder aux données, vérifiez que les bases de données primaire et secondaire ont toujours les mêmes informations d’identification utilisateur afin de pouvoir y accéder immédiatement avec les mêmes informations d’identification, une fois la copie terminée. 

Si vous utilisez [Azure Active Directory](../active-directory/active-directory-whatis.md), vous n’avez plus du tout besoin de gérer les informations d’identification dans la copie. Toutefois, lorsque vous copiez la base de données sur un nouveau serveur, l’accès par connexion peut ne pas fonctionner, car ces connexions n’existent pas sur le nouveau serveur. Pour en savoir plus sur la gestion des connexions durant la copie d’une base de données vers un autre serveur logique, consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md). 

Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données, peut se connecter à la nouvelle base de données. Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copier une base de données à l’aide du portail Azure

Pour copier une base de données à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Copier**. 

   ![Copie de base de données](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Copier une base de données à l’aide de PowerShell

Pour copier une base de données à l’aide de PowerShell, utilisez l’applet de commande [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy). 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Pour un exemple complet de script, consultez [Copier une base de données sur un nouveau serveur](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Copier une base de données à l’aide de Transact-SQL

Connectez-vous à la base de données maître à l’aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Pour que la copie de la base de données réussisse, les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager. Pour plus d’informations sur les connexions et la connexion au serveur, consultez la page [Gérer les connexions](sql-database-manage-logins.md).

Démarrez la copie de la base de données source avec l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'exécution de cette instruction initie le processus de copie de la base de données. Étant donné que la copie d'une base de données est un processus asynchrone, l'instruction CREATE DATABASE est retournée avant la fin de la copie de la base de données.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copie d'une base de données SQL sur le même serveur
Connectez-vous à la base de données maître à l’aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Pour que la copie de la base de données réussisse, les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager.

Cette commande copie Database1 dans une nouvelle base de données nommée Database2 sur le même serveur. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copie d'une base de données SQL vers un autre serveur

Connectez-vous à la base de données maître du serveur de destination, le serveur SQL Database dans lequel la nouvelle base de données doit être créée. Utilisez une connexion qui a les mêmes nom et mot de passe que le propriétaire de la base de données source sur le serveur SQL Database source. La connexion sur le serveur de destination doit également être membre du rôle dbmanager ou être la connexion principale niveau serveur.

Cette commande copie Database1 sur server1 dans une nouvelle base de données nommée Database2 sur server2. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Contrôle de la progression de l’opération de copie

Contrôlez le processus de copie en interrogeant les vues sys.databases et sys.dm_database_copies. Pendant que la copie est en cours, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **COPYING**.

* Si la copie échoue, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **SUSPECT**. Exécutez l'instruction DROP sur la nouvelle base de données et réessayez ultérieurement.
* Si la copie réussit, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **ONLINE**. La copie est terminée et la nouvelle base de données est une base de données normale, qui peut être modifiée indépendamment de la base de données source.

> [!NOTE]
> Si vous décidez d’annuler la copie pendant qu’elle est en cours, exécutez l’instruction [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) sur la nouvelle base de données. Vous pouvez également exécuter l'instruction DROP DATABASE sur la base de données source pour annuler le processus de copie.
> 

## <a name="resolve-logins"></a>Résolution des connexions

Une fois que la nouvelle base de données est en ligne sur le serveur de destination, utilisez l'instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) pour remapper les utilisateurs de la nouvelle base de données avec des connexions sur le serveur de destination. Pour résoudre les problèmes d’utilisateurs orphelins, consultez [Dépannage des utilisateurs orphelins](https://msdn.microsoft.com/library/ms175475.aspx). Vous pouvez également consulter [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).

Tous les utilisateurs de la nouvelle base de données conservent les autorisations qu’ils avaient dans la base de données source. L'utilisateur qui a initié la copie de la base de données devient le propriétaire de celle-ci et reçoit un nouvel identificateur de sécurité (SID). Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données, peut se connecter à la nouvelle base de données.

Pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données sur un autre serveur logique, consultez [Configurer et gérer la sécurité Azure SQL Database pour la géo-restauration ou le basculement](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md) et [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).
* Pour exporter une base de données, consultez [Exporter la base de données vers un fichier BACPAC](sql-database-export.md).
