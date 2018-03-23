---
title: Architecture de connectivité Azure SQL Database | Microsoft Docs
description: Ce document décrit l’architecture de connectivité Azure SQLDB dans et en dehors d’Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: 98784b2d1ede5354c965e483b34b5fcb323394aa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architecture de connectivité Azure SQL Database 

Cet article explique l’architecture de connectivité Azure SQL Database et comment les différents composants fonctionnent pour diriger le trafic vers votre instance Azure SQL Database. Ces composants de connectivité Azure SQL Database permettent de diriger le trafic réseau vers la base de données Azure avec des clients se connectant à partir d’Azure et d’autres se connectant en dehors d’Azure. Cet article comporte également des exemples de script permettant de modifier la façon dont la connectivité se produit et des considérations liées à la modification des paramètres de connectivité par défaut. 

## <a name="connectivity-architecture"></a>Architecture de connectivité

Le diagramme suivant donne une vue d’ensemble détaillée de l’architecture de connectivité Azure SQL Database.

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/architecture-overview.png)


Les étapes suivantes décrivent la manière dont une connexion est établie vers une base de données SQL Azure via l’équilibreur de charge logiciel et la passerelle Azure SQL Database.

- Les clients dans Azure ou en dehors d’Azure se connectent à l’équilibreur de charge logiciel, qui détient une adresse IP publique et écoute le port 1433.
- L’équilibreur de charge logiciel dirige le trafic vers la passerelle Azure SQL Database.
- La passerelle redirige le trafic vers l’intergiciel de proxy approprié.
- Ce dernier redirige le trafic vers la base de données SQL Azure appropriée.

> [!IMPORTANT]
> Chacun de ces composants dispose d’une protection contre les attaques par déni de service distribué (DDoS) intégrée au niveau du réseau et de la couche d’application.
>

## <a name="connectivity-from-within-azure"></a>Connectivité dans Azure

Si vous vous connectez à partir d’Azure, vos connexions ont une stratégie de connexion par **redirection** par défaut. Une stratégie de **redirection** signifie qu’une fois les connexions établies après la session TCP vers la base de données SQL Azure, la session du client est redirigée vers l’intergiciel de proxy en modifiant l’adresse (en la faisant passer de celle de la passerelle de base de données SQL Azure à celle de l’intergiciel de proxy). Ainsi, tous les paquets suivants flux se dirigent directement vers l’intergiciel de proxy en ignorant la passerelle Azure SQL Database. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectivité en dehors d’Azure

Si vous vous connectez en dehors d’Azure, vos connexions disposent d’une stratégie de connexion de **proxy** par défaut. Une stratégie de **Proxy** signifie que la session TCP est établie via la passerelle Azure SQL Database et que tous les paquets suivants transitent via la passerelle. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresses IP de la passerelle Azure SQL Database

Pour vous connecter à une base de données SQL Azure à partir de ressources locales, vous devez autoriser le trafic réseau sortant vers la passerelle Azure SQL Database pour votre région Azure. Les connexions transitent uniquement via la passerelle lors de la connexion en mode Proxy, qui est le mode par défaut lors de la connexion à partir de ressources locales.

Le tableau suivant répertorie les adresses IP principales et secondaires de la passerelle Azure SQL Database pour toutes les régions de données. Pour certaines régions, il existe deux adresses IP. Dans ces régions, l’adresse IP principale est l’adresse IP actuelle de la passerelle et la deuxième adresse IP est une adresse IP de basculement. L’adresse de basculement est l’adresse vers laquelle nous pouvons déplacer votre serveur pour maintenir la haute disponibilité du service. Pour ces régions, nous vous conseillons d’autoriser le trafic sortant vers les deux adresses IP. La deuxième adresse IP est détenue par Microsoft et n’écoute pas les services tant qu’elle n’est pas activée par Azure SQL Database de manière à accepter les connexions.

| Nom de la région | Adresse IP principale | Adresse IP secondaire |
| --- | --- |--- |
| Est de l’Australie | 191.238.66.109 | 13.75.149.87 |
| Sud-Est de l’Australie | 191.239.192.109 | 13.73.109.251 |
| Sud du Brésil | 104.41.11.5 | |
| Centre du Canada | 40.85.224.249 | |
| Est du Canada | 40.86.226.166 | |
| Centre des États-Unis | 23.99.160.139 | 13.67.215.62 |
| Est de l'Asie | 191.234.2.139 | 52.175.33.150 |
| Est des États-Unis 1 | 191.238.6.43 | 40.121.158.30 |
| Est des États-Unis 2 | 191.239.224.107 | 40.79.84.180 * |
| Inde-Centre | 104.211.96.159  | |
| Sud de l’Inde | 104.211.224.146  | |
| Inde-Ouest | 104.211.160.80 | |
| Est du Japon | 191.237.240.43 | 13.78.61.196 |
| Ouest du Japon | 191.238.68.11 | 104.214.148.156 |
| Centre de la Corée | 52.231.32.42 | |
| Corée du Sud | 52.231.200.86 |  |
| Centre-Nord des États-Unis | 23.98.55.75 | 23.96.178.199 |
| Europe du Nord | 191.235.193.75 | 40.113.93.91 |
| Centre-Sud des États-Unis | 23.98.162.75 | 13.66.62.124 |
| Asie du Sud-Est | 23.100.117.95 | 104.43.15.0 |
| Nord du Royaume-Uni | 13.87.97.210 | |
| Sud du Royaume-Uni 1 | 51.140.184.11 | |
| Sud du Royaume-Uni 2 | 13.87.34.7 | |
| Ouest du Royaume-Uni | 51.141.8.11  | |
| Centre-Ouest des États-Unis | 13.78.145.25 | |
| Europe de l'Ouest | 191.237.232.75 | 40.68.37.158 |
| Ouest des États-Unis 1 | 23.99.34.75 | 104.42.238.205 |
| Ouest des États-Unis 2 | 13.66.226.202  | |
||||

\***REMARQUE :** *Est des États-Unis 2* a également l’adresse IP tertiaire `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Modifier la stratégie de connexion Azure SQL Database

Pour modifier la stratégie de connexion Azure SQL Database d’un serveur Azure SQL Database, utilisez l’[API REST](https://msdn.microsoft.com/library/azure/mt604439.aspx).

- Si votre stratégie de connexion est définie sur **Proxy**, tous les paquets réseau transitent via la passerelle Azure SQL Database. Pour ce paramètre, vous devez autoriser le trafic sortant uniquement vers l’IP de la passerelle Azure SQL Database. L’utilisation d’un paramètre de **Proxy** offre plus de latence qu’un paramètre de **redirection**.
- Si votre stratégie de connexion repose sur le paramètre de **redirection**, tous les paquets réseau se dirigent directement vers le proxy de l’intergiciel. Pour ce paramètre, vous devez autoriser le trafic sortant vers plusieurs adresses IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script permettant de modifier les paramètres de connexion via PowerShell

> [!IMPORTANT]
> Ce script nécessite le [module Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Le script PowerShell suivant montre comment modifier la stratégie de connexion.

```powershell
Add-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Script permettant de modifier les paramètres de connexion via Azure CLI 2.0

> [!IMPORTANT]
> Ce script nécessite [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Le script CLI suivant montre comment modifier la stratégie de connexion.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la façon de modifier la stratégie de connexion Azure SQL Database d’un serveur Azure SQL Database, consultez [Create or Update Server Connection Policy using the REST API](https://msdn.microsoft.com/library/azure/mt604439.aspx) (Créer ou mettre à jour la stratégie de connexion au serveur à l’aide de l’API REST).
- Pour plus d’informations sur le comportement de connexion d’Azure SQL Database pour les clients qui utilisent ADO.NET version 4.5 ou ultérieure, consultez [Ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Pour en savoir plus sur la vue d’ensemble du développement d’applications générales, consultez [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md).
