---
title: PowerShell pour points de terminaison de service de réseau virtuel et règles dans SQL | Microsoft Docs
description: Fournit des scripts PowerShell pour créer et gérer des points de terminaison de service virtuel pour Azure SQL Database.
services: sql-database
author: MightyPen
manager: jhubbard
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 489d1044de49c63ac7e1423708cc0638355ab1b5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database"></a>Utiliser PowerShell pour créer un point de terminaison de service virtuel et une règle pour la base de données SQL Azure

Cet article présente et décrit un script PowerShell qui effectue les actions suivantes :

1. Crée un *point de terminaison de service virtuel* Microsoft Azure sur votre sous-réseau.
2. Ajoute le point de terminaison au pare-feu de votre serveur Azure SQL Database afin de créer une *règle de réseau virtuel*.

L’intérêt de créer une règle est expliqué dans la section [Points de terminaison de service virtuel pour Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Si vous devez uniquement évaluer ou ajouter le point de terminaison de service virtuel *nom de type* pour SQL Database à votre sous-réseau, vous pouvez passer directement à notre [script PowerShell plus direct](#a-verify-subnet-is-endpoint-ps-100).

#### <a name="major-cmdlets"></a>Principales applets de commande

Cet article se concentre sur l’applet de commande nommée **New-AzureRmSqlServerVirtualNetworkRule**, qui ajoute le point de terminaison de sous-réseau à la liste de contrôle d’accès (ACL) de votre serveur de base de données SQL Azure, créant ainsi une règle.

La liste suivante montre la séquence des autres *principales* applets de commande que vous devez exécuter pour préparer votre appel à **New-AzureRmSqlServerVirtualNetworkRule**. Dans cet article, ces appels se produisent dans le [script 3 « Règle de réseau virtuel »](#a-script-30) :

1. [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) : crée un a objet de sous-réseau.

2. [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) : crée votre réseau virtuel et lui attribue un sous-réseau.

3. [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): attribue un point de terminaison de service virtuel à votre sous-réseau.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): mises à jour persistantes apportées à votre réseau virtuel.

5. [New-AzureRmSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlservervirtualnetworkrule) : lorsque fois votre sous-réseau devient un point de terminaison, ajoute votre sous-réseau en tant que règle de réseau virtuel à la liste ACL de votre serveur de base de données SQL Azure.
    - Propose le paramètre **-IgnoreMissingVnetServiceEndpoint**, à compter du module AzureRM PowerShell version 5.1.1.

#### <a name="prerequisites-for-running-powershell"></a>Prérequis pour l’exécution de PowerShell

- Vous pouvez déjà vous connecter à Azure, par exemple via le [portail Azure][http-azure-portal-link-ref-477t].
- Vous pouvez déjà exécuter des scripts PowerShell.

#### <a name="one-script-divided-into-four-chunks"></a>Un script est divisé en quatre parties

Notre script PowerShell de démonstration est divisé en une séquence de scripts plus petits. La division facilite l’apprentissage et offre plus de souplesse. Les scripts doivent être exécutés dans la séquence indiquée. Si vous n’avez pas le temps d’exécuter les scripts maintenant, nos résultats de test réels sont affichés après le script 4.






<a name="a-script-10" />

## <a name="script-1-variables"></a>Script 1 : variables

Ce premier script PowerShell attribue des valeurs aux variables. Les scripts suivants dépendent de ces variables.

> [!IMPORTANT]
> Avant d’exécuter ce script, vous pouvez modifier les valeurs, si vous le souhaitez. Par exemple, si vous disposez déjà d’un groupe de ressources, vous pouvez remplacer le nom de votre groupe de ressources par la valeur attribuée.
>
>  Le nom de votre abonnement doit être modifié dans le script.

#### <a name="powershell-script-1-source-code"></a>Code source du script PowerShell 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Login-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```





<a name="a-script-20" />

## <a name="script-2-prerequisites"></a>Script 2 : prérequis

Ce script prépare le script suivant, où se déroule l’action du point de terminaison. Ce script crée pour vous les éléments suivants, mais uniquement s’ils n’existent pas déjà. Vous pouvez ignorer le script 2 si vous êtes sûr que ces éléments existent déjà :

- Groupe de ressources Azure
- Serveur de base de données SQL Azure

#### <a name="powershell-script-2-source-code"></a>Code source du script PowerShell 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzureRmSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzureRmSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```






<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3 : Créer un point de terminaison et une règle

Ce script crée un réseau virtuel avec un sous-réseau. Le script attribue ensuite le type de point de terminaison **Microsoft.Sql** à votre sous-réseau. Enfin, le script ajoute votre sous-réseau à la liste de contrôle d’accès (ACL) de votre serveur de base de données SQL, créant ainsi une règle.

#### <a name="powershell-script-3-source-code"></a>Code source du script PowerShell 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Script 4 : Nettoyage

Ce dernier script supprime les ressources que les scripts précédents ont créées pour la démonstration. Toutefois, le script vous demande de confirmer la suppression des éléments suivants :

- Serveur de base de données SQL Azure
- Groupe de ressources Azure

Une fois le script 1 terminé, vous pouvez exécuter le script 4 à tout moment.

#### <a name="powershell-script-4-source-code"></a>Code source du script PowerShell 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```






<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>Résultats réels des scripts 1 à 4

Les résultats de notre série de tests vont maintenant s’afficher dans un format abrégé. Ces résultats peuvent être utiles si vous ne souhaitez pas réellement exécuter maintenant les scripts PowerShell.

```
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Completed script 2, the "Prerequisites".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations      
----------------- -------       ---------      
Succeeded         Microsoft.Sql {westcentralus}
                                               
Verify that the rule is in the SQL DB ACL.
                                               
Completed script 3, the "Virtual-Network-Rule".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

C’est la fin de notre principal script PowerShell.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Vérifiez que votre sous-réseau est un point de terminaison

Vous disposez peut-être d’un sous-réseau auquel le nom de type **Microsoft.Sql** a déjà été attribué, ce qui signifie qu’il est déjà un point de terminaison de service virtuel. Vous pouvez utiliser le [portail Azure][http-azure-portal-link-ref-477t] pour créer une règle de réseau virtuel à partir du point de terminaison.

Ou, vous n’êtes peut-être pas certain que votre sous-réseau comporte un nom de type **Microsoft.Sql**. Vous pouvez exécuter le script PowerShell suivant pour effectuer ces actions :

1. Assurez-vous que votre sous-réseau comporte le nom de type **Microsoft.Sql**.
2. Attribuez éventuellement le nom de type s’il est absent.
    - Le script vous invite à *confirmer* que le nom de type est absent.

#### <a name="phases-of-the-script"></a>Phases du script

Voici les phases du script PowerShell :

1. Connectez-vous à votre compte Azure, nécessaire une seule fois par session PS.  Attribuez les variables.
2. Recherchez votre réseau virtuel, puis votre sous-réseau.
3. Votre sous-réseau est-il étiqueté comme type de serveur de point de terminaison **Microsoft.Sql** ?
4. Ajoutez un point de terminaison de service virtuel avec le nom de type **Microsoft.Sql** sur votre sous-réseau.

> [!IMPORTANT]
> Avant d’exécuter ce script, vous devez modifier les valeurs affectées aux variables $, au début du script.

#### <a name="direct-powershell-source-code"></a>Code de source PowerShell direct

Ce script PowerShell ne met rien à jour, sauf si vous répondez Oui lorsque PowerShell vous demande confirmation. Le script peut ajouter le nom de type **Microsoft.Sql** à votre sous-réseau. Mais le script essaie d’ajouter ce nom de type uniquement si votre sous-réseau n’en dispose pas.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Login-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>Résultats réels

Le bloc suivant affiche nos commentaires réels (avec de légères modifications).

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```




<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

