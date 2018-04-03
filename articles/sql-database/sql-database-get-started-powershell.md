---
title: 'Azure PowerShell : Créer une base de données SQL | Microsoft Docs'
description: Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et des bases de données dans le Portail Azure.
keywords: didacticiel sur la base de données sql, créer une base de données sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/17/2017
ms.author: carlrab
ms.openlocfilehash: 97a2bdeb275c00d298b6b25e31516984c5d9b62e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-azure-sql-database-using-powershell"></a>Créer une base de données SQL Azure unique à l’aide de PowerShell

PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide décrit comment utiliser PowerShell pour déployer une base de données SQL Azure dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) dans un [serveur logique Azure SQL Database](sql-database-features.md).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Ce didacticiel requiert le module Azure PowerShell version 4.0 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) et suivez les instructions à l’écran.

```powershell
Add-AzureRmAccount
```

## <a name="create-variables"></a>Créer des variables

Définissez des variables à utiliser dans les scripts de ce démarrage rapide.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Création d'un serveur logique

Créez un [serveur logique Azure SQL Database](sql-database-features.md) avec la commande [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). Un serveur logique contient un groupe de bases de données gérées en tant que groupe. L’exemple suivant illustre la création d’un serveur nommé de façon aléatoire dans votre groupe de ressources avec un identifiant d’administrateur nommé `ServerAdmin` et un mot de passe `ChangeYourAdminPassword1`. Remplacez les valeurs prédéfinies par ce que vous souhaitez.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurer une règle de pare-feu du serveur

Créez une [règle de pare-feu au niveau du serveur Azure SQL Database](sql-database-firewall-configure.md) avec la commande [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Une règle de pare-feu au niveau du serveur permet à une application externe, telle que SQL Server Management Studio ou l’utilitaire SQLCMD, de se connecter à une base de données SQL via le pare-feu du service SQL Database. Dans l’exemple suivant, le pare-feu n’est ouvert qu’à d’autres ressources Azure. Pour activer la connectivité externe, remplacez l’adresse IP par une adresse correspondant à votre environnement. Pour ouvrir toutes les adresses IP, utilisez 0.0.0.0 comme adresse IP de début et 255.255.255.255 comme adresse de fin.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Créer une base de données dans le serveur avec des exemples de données

Créez une base de données SQL avec un [niveau de performance S0](sql-database-service-tiers.md) sur le serveur avec la commande [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase). L’exemple suivant crée une base de données appelée `mySampleDatabase` et charge les exemples de données AdventureWorksLT dans cette base de données. Remplacez ces valeurs prédéfinies selon les besoins (les autres démarrages rapides de cette collection reposent sur les valeurs de ce démarrage rapide).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. 

> [!TIP]
> Si vous souhaitez continuer à utiliser d’autres démarrages rapides, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées par ce démarrage rapide dans le portail Azure.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous disposez d’une base de données, vous pouvez vous [connecter et interroger](sql-database-connect-query.md) à l’aide d’un de vos outils ou langages préférés. 
- Pour apprendre à concevoir votre première base de données, créer des tables et insérer des données, consultez un de ces didacticiels :
 - [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
  - [Concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md)

