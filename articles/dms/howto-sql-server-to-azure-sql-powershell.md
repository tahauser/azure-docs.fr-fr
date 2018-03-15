---
title: "Utiliser le module Azure Database Migration Service dans Microsoft Azure PowerShell pour migrer l’instance SQL Server locale vers Azure SQL DB | Microsoft Docs"
description: "Apprenez à migrer l’instance SQL Server vers SQL Azure à l’aide d’Azure PowerShell."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 8569bf65d04f677a45935284dc61d68879014c10
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrer l’instance SQL Server locale vers la base de données SQL Azure à l’aide d’Azure PowerShell
Dans cet article, vous allez migrer la base de données **Adventureworks2012** restaurée vers une instance locale de SQL Server 2016 ou une version ultérieure ou Microsoft Azure SQL Database, à l’aide de Microsoft Azure PowerShell. Vous pouvez migrer des bases de données à partir d’une instance SQL Server locale vers Microsoft Azure SQL Database, à l’aide du module `AzureRM.DataMigration`, dans Microsoft Azure PowerShell.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Créez un groupe de ressources.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration dans une instance Azure Database Migration Service.
> * Exécuter la migration.

## <a name="prerequisites"></a>Prérequis
Pour effectuer cette procédure, vous avez besoin de :

- [SQL Server 2016 ou version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads) (toute édition)
- Le protocole TCP/IP est désactivé par défaut avec l’installation de SQL Server Express. Activez-le en suivant les [instructions de cet article](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Pour configurer votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Une instance Azure SQL Database. Vous pouvez créer une instance Azure SQL Database en suivant les indications de l’article [Création d’une base de données SQL Azure à l’aide du portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Assistant Migration des données](https://www.microsoft.com/download/details.aspx?id=53595) version 3.3 ou ultérieure.
- Azure Database Migration Service nécessite un réseau virtuel créé à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux à l’aide de la fonction [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- L’évaluation de la base de données locale et de la migration de schéma à l’aide de Microsoft Data Migration Assistant, comme indiqué dans l’article relatif à [l’évaluation de la migration de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
- Téléchargez et installez le module AzureRM.DataMigration sur PowerShell Gallery avec la [cmdlet Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
- Les informations d’identification utilisées pour se connecter à une instance SQL Server source doivent être associées à l’autorisation [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Les informations d’identification utilisées pour se connecter à une instance Azure SQL DB cible doivent être associées à l’autorisation CONTROL DATABASE concernant les bases de données Azure SQL Database cibles.
- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Connexion à un abonnement Microsoft Azure
Suivez les instructions de l’article [Se connecter avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) pour vous connecter à votre abonnement Azure à l’aide de PowerShell.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources pour pouvoir créer une machine virtuelle.

Créez un groupe de ressources à l’aide de la commande [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1). 

L’exemple suivant permet de créer un groupe de ressources nommé *myResourceGroup* dans la région *Est des États-Unis*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Créer une instance Azure Database Migration Service 
Vous pouvez créer une instance Azure Database Migration Service à l’aide de l’applet de commande `New-AzureRmDataMigrationService`. Cette cmdlet attend les paramètres requis suivants :
- *Nom du groupe de ressources Azure*. Vous pouvez utiliser la commande [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) pour créer le groupe de ressources Azure indiqué précédemment et indiquez son nom en tant que paramètre.
- *Nom du service*. Chaîne qui correspond au nom de service unique à donner à l’instance Azure Database Migration Service. 
- *Emplacement*. Spécifie l’emplacement du service. Indiquez un emplacement de centre de données Azure, tels que l’Ouest des États-Unis or le Sud-Est asiatique.
- *Référence SKU*. Ce paramètre correspond au nom de référence SKU DMS. Les noms de référence SKU actuellement pris en charge sont *Basic_1vCore*, *Basic_2vCores* et *GeneralPurpose_4vCores*.
- *Identificateur de sous-réseau virtuel*. Créez un sous-réseau avec la cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1). 

L’exemple suivant crée un service nommé *MyDMS* dans le groupe de ressources *MyDMSResourceGroup*, qui se trouve dans la région *Est des États-Unis* à l’aide d’un réseau virtuel appelé *MySubnet* et d’un sous-réseau appelé *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Créer un projet de migration
Une fois l’instance Azure Database Migration Service créée, générez un projet de migration. Un projet Azure Database Migration Service requiert des informations de connexion pour les instances source et cible, ainsi que la liste des bases de données que vous souhaitez migrer dans le cadre de ce projet.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Créer un objet d’informations de connexion de base de données pour les connexions source et cible
Vous pouvez créer un objet d’informations de connexion de base de données à l’aide de la cmdlet `New-AzureRmDmsConnInfo`. Cette cmdlet attend les paramètres suivants :
- *ServerType*. Type de connexion de base de données demandé, par exemple, MySQL, Oracle ou MySQL. Utilisez SQL pour SQL Server et SQL Azure.
- *DataSource*. Nom ou adresse IP du serveur SQL Azure ou de l’instance SQL.
- *AuthType*. Type d’authentification pour la connexion, qui peut être SqlAuthentication ou WindowsAuthentication.
- Le paramètre *TrustServerCertificate* définit une valeur qui indique si le canal est chiffré tout en ignorant l’analyse de la chaîne de certificat pour valider l’approbation. La valeur doit être « true » ou « false ».

L’exemple suivant crée l’objet d’informations de connexion pour l’instance SQL Server source, appelée MySQLSourceServer, avec l’authentification SQL. 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

L’exemple suivant illustre la création des informations de connexion du serveur de base de données SQL Azure appelé MySQLAzureTarget, à l’aide de l’authentification sql.

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fournir des bases de données pour le projet de migration
Créez une liste d’objets `AzureRmDataMigrationDatabaseInfo` spécifiant les bases de données dans le cadre du projet Azure Database Migration qui peut être fourni en tant que paramètre de création du projet. La cmdlet `New-AzureRmDataMigrationDatabaseInfo` peut être utilisée pour créer AzureRmDataMigrationDatabaseInfo. 

L’exemple suivant crée le projet `AzureRmDataMigrationDatabaseInfo` pour la base de données AdventureWorks2016 et l’ajoute à la liste qui doit être fournie en tant que paramètre de création du projet.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Créer un objet de projet
Enfin, vous pouvez créer le projet Azure Database Migration appelé *MyDMSProject*, situé dans la région *Est des États-Unis*, à l’aide du paramètre `New-AzureRmDataMigrationProject`, et en ajoutant les connexions source et cible créées précédemment et la liste des bases de données à migrer.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfos $dbList
```

## <a name="create-and-start-a-migration-task"></a>Créer et démarrer une tâche de migration
Enfin, créez et démarrez une tâche Azure Database Migration. La tâche Azure Database Migration nécessite des informations de connexion pour les systèmes source et cible, ainsi que la liste des tables de base de données à migrer, en plus des informations déjà fournies avec le projet créé, en tant que condition préalable. 

### <a name="create-credential-parameters-for-source-and-target"></a>Créer des paramètres d’informations d’identification pour la source et la cible
Vous pouvez créer des informations d’identification de sécurité de la connexion en tant qu’objet [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

L’exemple suivant illustre la création d’objets *PSCredential* pour les connexions source et cible, en fournissant des mots de passe en tant que variables de chaîne *$sourcePassword* et *$targetPassword*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Créer un mappage de table et sélectionner des paramètres source et cible pour la migration
Le mappage des tables depuis des tables source et cible à migrer est un autre paramètre nécessaire à la migration. Créez un dictionnaire des tables qui assure le mappage entre les tables source et cible pour la migration. L’exemple suivant illustre le mappage entre les tables source et cible du schéma des ressources humaines pour la base de données AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

L’étape suivante consiste à sélectionner les bases de données source et cible et à fournir un mappage de table pour effectuer la migration en tant que paramètre, à l’aide de la cmdlet `New-AzureRmDmsSqlServerSqlDbSelectedDB`, comme indiqué dans l’exemple suivant :

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Créer et démarrer une tâche de migration

Utilisez la cmdlet `New-AzureRmDataMigrationTask` pour créer et démarrer une tâche de migration. Cette cmdlet attend les paramètres suivants :
- *TaskType*. Le type de tâche de migration à créer pour SQL Server pour le type de migration SQL Azure *MigrateSqlServerSqlDb* est attendu. 
- *Resource Group Name*. Nom du groupe de ressources Azure dans lequel créer la tâche.
- *ServiceName*. Instance Azure Database Migration Service dans laquelle créer la tâche.
- *ProjectName*. Nom du projet Azure Database Migration Service dans lequel créer la tâche. 
- *TaskName*. Nom de la tâche à créer. 
- *Source Connection*. Objet AzureRmDmsConnInfo représentant la connexion source.
- *Target Connection*. Objet AzureRmDmsConnInfo représentant la connexion cible.
- *SourceCred*. Objet [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pour la connexion au serveur source.
- *TargetCred*. Objet [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pour la connexion au serveur cible.
- *SelectedDatabase*. Objet AzureRmDmsSqlServerSqlDbSelectedDB qui représente le mappage entre les bases de données source et cible.

L’exemple suivant crée et démarre une tâche de migration nommée myDMSTask :

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Surveiller la migration
Vous pouvez surveiller la tâche de migration en cours d’exécution en interrogeant la propriété d’état de la tâche, comme indiqué dans l’exemple suivant :

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Étapes suivantes
- Lisez l’aide à la migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.