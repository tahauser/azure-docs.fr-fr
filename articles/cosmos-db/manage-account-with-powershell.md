---
title: "Automatisation Azure Cosmos DB - Gestion avec Powershell | Microsoft Docs"
description: "Utilisez Azure Powershell pour gérer vos comptes Azure Cosmos DB."
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: d2436ad639c53360f4d1afde99d668285b606aa9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Créer un compte Azure Cosmos DB à l’aide de PowerShell

Le guide suivant décrit les commandes permettant d’automatiser la gestion de vos comptes de base de données Azure Cosmos DB à l’aide d’Azure Powershell. Il inclut également des commandes dédiées à la gestion des clés de comptes et des priorités de basculement dans des [comptes de base de données multi-régions][scaling-globally]. La mise à jour de votre compte de base de données vous permet de modifier les stratégies de cohérence et d’ajouter/de supprimer des régions. Pour une gestion multiplateforme de votre compte de base de données Azure Cosmos DB, vous pouvez utiliser l’interface [Azure CLI](cli-samples.md), [l’API REST Resource Provider][rp-rest-api] ou le [portail Azure](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Mise en route

Suivez les instructions de la page [Installation et configuration d’Azure PowerShell][powershell-install-configure] pour procéder à l’installation et vous connecter à votre compte Azure Resource Manager dans PowerShell.

### <a name="notes"></a>Notes

* Si vous voulez exécuter les commandes suivantes sans demander la confirmation de l’utilisateur, ajoutez l’indicateur `-Force` à la commande.
* L’ensemble des commandes suivantes sont synchrones.

## <a id="create-documentdb-account-powershell"></a> Créer un compte Azure Cosmos DB

Cette commande vous permet de créer un compte de base de données Azure Cosmos DB. Configurez votre nouveau compte de base de données pour une seule ou [plusieurs régions][scaling-globally] avec une [stratégie de cohérence](consistency-levels.md) déterminée.

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Le nom d’emplacement de la région d’écriture du compte de base de données. Cet emplacement doit présenter une valeur de priorité de basculement de 0. Il doit y avoir exactement une région d’écriture par compte de base de données.
* `<read-region-location>` Le nom d’emplacement de la région de lecture du compte de base de données. L’emplacement doit présenter une valeur de priorité de basculement supérieure à 0. Un compte de base de données peut présenter plusieurs régions de lecture.
* `<ip-range-filter>` Spécifie le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour les ajouter à la liste d’adresses IP clientes autorisées pour un compte de base de données particulier. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Pour plus d’informations, consultez [Prise en charge du pare-feu dans Azure Cosmos DB](firewall-support.md).
* `<default-consistency-level>` Niveau de cohérence par défaut du compte Azure Cosmos DB. Pour plus d’informations, consultez [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente la durée (en secondes) d’obsolescence tolérée. Les valeurs acceptables sont comprises entre 1 et 100.
* `<max-staleness-prefix>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente le nombre de requêtes d’obsolescence tolérées. Les valeurs acceptables sont 1-2, 147, 483 et 647.
* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<resource-group-location>` Emplacement du groupe de ressources Azure auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB à créer. Cette valeur accepte uniquement les lettres minuscules, les nombres, le caractère « - », et doit présenter entre 3 et 50 caractères.

Exemple : 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Notes
* L’exemple précédent crée un compte de base de données avec deux régions. Il est également possible de créer un compte de base de données avec l’une des régions (désignée comme la région d’écriture, elle présente une valeur de priorité de basculement de 0) ou plus de deux régions. Pour plus d’informations, consultez la section des [comptes de base de données multi-régions][scaling-globally].
* Les emplacements doivent correspondre à des régions dans lesquelles Azure Cosmos DB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Mettre à jour un compte de base de données Azure Cosmos DB

Cette commande vous permet de mettre à jour les propriétés de votre compte de base de données Azure Cosmos DB. Les informations mises à jour sont notamment la stratégie de cohérence et les emplacements hébergeant le compte de base de données.

> [!NOTE]
> Cette commande, qui vous permet d’ajouter ou de supprimer des régions, ne prend aucunement en charge la modification des priorités de basculement. Pour modifier les priorités de basculement, consultez la section [ci-dessous](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Le nom d’emplacement de la région d’écriture du compte de base de données. Cet emplacement doit présenter une valeur de priorité de basculement de 0. Il doit y avoir exactement une région d’écriture par compte de base de données.
* `<read-region-location>` Le nom d’emplacement de la région de lecture du compte de base de données. L’emplacement doit présenter une valeur de priorité de basculement supérieure à 0. Un compte de base de données peut présenter plusieurs régions de lecture.
* `<default-consistency-level>` Niveau de cohérence par défaut du compte Azure Cosmos DB. Pour plus d’informations, consultez [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Spécifie le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour les ajouter à la liste d’adresses IP clientes autorisées pour un compte de base de données particulier. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Pour plus d’informations, consultez [Prise en charge du pare-feu dans Azure Cosmos DB](firewall-support.md).
* `<max-interval>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente la durée (en secondes) d’obsolescence tolérée. Les valeurs acceptables sont comprises entre 1 et 100.
* `<max-staleness-prefix>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente le nombre de requêtes d’obsolescence tolérées. Les valeurs acceptables sont 1-2, 147, 483 et 647.
* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<resource-group-location>` Emplacement du groupe de ressources Azure auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB à mettre à jour.

Exemple : 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Supprimer un compte de base de données Azure Cosmos DB

Cette commande vous permet de supprimer un compte existant de base de données Azure Cosmos DB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB à supprimer.

Exemple :

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Obtenir les propriétés d’un compte de base de données Azure Cosmos DB

Cette commande vous permet d’obtenir les propriétés d’un compte existant de base de données Azure Cosmos DB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB.

Exemple :

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Mettre à jour les balises d’un compte de base de données Azure Cosmos DB

L’exemple suivant vous explique comment définir les [balises des ressources Azure][azure-resource-tags] pour votre compte de base de données Azure Cosmos DB.

> [!NOTE]
> Cette commande peut être combinée aux commandes de création ou de mise à jour, via l’ajout de l’indicateur `-Tags` avec le paramètre correspondant.

Exemple :

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Liste des clés de comptes

Lorsque vous créez un compte Azure Cosmos DB, le service génère deux clés d’accès maître qui peuvent être utilisées pour l’authentification lors de l’accès au compte Azure Cosmos DB. En fournissant deux clés d’accès, Azure Cosmos DB vous permet de régénérer les clés sans interruption de votre compte Azure Cosmos DB. Des clés en lecture seule pour l’authentification des opérations en lecture seule sont également disponibles. Il existe deux clés en lecture et écriture (primaire et secondaire) et deux clés en lecture seule (primaire et secondaire).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB.

Exemple :

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Répertorier les chaînes de connexion

Pour les comptes MongoDB, vous pouvez récupérer la chaîne de connexion pour connecter votre application MongoDB au compte de base de données à l’aide de la commande suivante.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB.

Exemple :

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Régénération de la clé de compte

Vous devez modifier périodiquement les clés d'accès à votre compte Azure Cosmos DB pour garantir la sécurité des connexions. Deux clés d’accès vous sont affectées afin de vous permettre de conserver des connexions au compte Azure Cosmos DB à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB.
* `<key-kind>` L’un des quatre types de clés : ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] que vous souhaitez régénérer.

Exemple :

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modifier la priorité de basculement d’un compte de base de données Azure Cosmos DB

Pour les comptes de base de données multirégions, vous pouvez modifier la priorité de basculement des différentes régions dans lesquelles existe le compte de base de données Azure Cosmos DB. Pour plus d’informations sur le basculement dans votre compte de base de données Azure Cosmos DB, consultez la section [Azure Cosmos DB, un service de base de données mondialement distribué sur Azure][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Le nom d’emplacement de la région d’écriture du compte de base de données. Cet emplacement doit présenter une valeur de priorité de basculement de 0. Il doit y avoir exactement une région d’écriture par compte de base de données.
* `<read-region-location>` Le nom d’emplacement de la région de lecture du compte de base de données. L’emplacement doit présenter une valeur de priorité de basculement supérieure à 0. Un compte de base de données peut présenter plusieurs régions de lecture.
* `<resource-group-name>` Nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données Azure Cosmos DB.
* `<database-account-name>` Nom du compte de base de données Azure Cosmos DB.

Exemple :

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Étapes suivantes

* Pour vous connecter avec .NET, consultez la page [Connect and query with .NET (Se connecter et effectuer des requêtes avec .NET)](create-sql-api-dotnet.md).
* Pour vous connecter avec Node.js, consultez [Connect and query with Node.js and a MongoDB app (Se connecter et exécuter des requêtes avec Node.js et une application MongoDB)](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/
