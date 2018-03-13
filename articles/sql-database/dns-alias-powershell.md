---
title: PowerShell pour les alias DNS sur Azure SQL | Microsoft Docs
description: Les cmdlets PowerShell, notamment New-AzureRMSqlServerDNSAlias, permettent de rediriger les nouvelles connexions clientes vers un autre serveur Azure SQL Database, sans toucher aux configurations clientes.
keywords: "base de données sql dns"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: 
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;amagarwa;maboja
ms.author: dmalik
ms.openlocfilehash: ec638d7b48b443cda5755e3077c6304b0c5ad78e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell pour les alias DNS sur Azure SQL Database

Cet article comporte un script PowerShell montrant une manière de gérer un alias DNS pour Azure SQL Database. Le script exécute les applets de commande suivantes, qui effectuent les actions ci-dessous :


Voici les cmdlets utilisées dans l’exemple de code :
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : crée un alias DNS dans le système de service Azure SQL Database. L’alias fait référence au serveur 1 Azure SQL Database.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : récupère et liste tous les alias DNS affectés au serveur 1 SQL DB.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : modifie le nom du serveur auquel l’alias est configuré pour faire référence, en passant du serveur 1 au serveur 2 SQL DB.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1) : supprime l’alias DNS du serveur 2 SQL DB, en utilisant le nom de l’alias.


Les cmdlets PowerShell précédentes ont été ajoutées au module **AzureRm.Sql** à partir de la version 5.1.1.




#### <a name="dns-alias-in-connection-string"></a>Alias DNS dans la chaîne de connexion

Pour connecter un serveur Azure SQL Database en particulier, un client tel que SQL Server Management Studio (SSMS) peut fournir le nom d’alias DNS au lieu du vrai nom du serveur. Dans l’exemple de chaîne de serveur suivant, l’alias *any-unique-alias-name* remplace le premier nœud délimité par un point dans la chaîne de serveur à quatre nœuds :
- Exemple de chaîne de serveur : `any-unique-alias-name.database.windows.net`.



## <a name="prerequisites"></a>configuration requise

Pour exécuter le script PowerShell de démonstration proposé dans cet article, les prérequis suivants s’appliquent :

- Un compte et un abonnement Azure. Pour profiter d’un essai gratuit, cliquez sur [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].

- Le module Azure PowerShell avec la cmdlet **New-AzureRMSqlServerDNSAlias**.
    - Pour l’installer ou le mettre à niveau, consultez la section [Installer le module Azure PowerShell][install-azurerm-ps-84p].
    - Exécutez `Get-Module -ListAvailable AzureRM;` dans powershell\_ise.exe pour trouver la version.

- Deux serveurs Azure SQL Database.

## <a name="code-example"></a>Exemple de code

L’exemple de code PowerShell suivant commence par affecter des valeurs littérales à plusieurs variables. Pour exécuter le code, vous devez tout d’abord remplacer toutes les valeurs des espaces réservés par les valeurs réelles de votre système. Vous pouvez aussi étudier tout simplement le code. La sortie du code dans la console est également donnée.


```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Login-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>Sortie réelle de l’exemple PowerShell dans la console

La sortie de la console suivante a été copiée et collée à partir d’une exécution réelle.


```
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.
 
[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount : 

 
[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName    
----------------- ----------         ------------------    
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>> 
```

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez une explication complète de la fonctionnalité d’alias DNS pour SQL Database dans la section [Alias DNS pour Azure SQL Database][dns-alias-overview-37v].



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

