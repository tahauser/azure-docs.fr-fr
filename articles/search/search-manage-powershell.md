---
title: "Gérer Recherche Azure avec les scripts PowerShell | Microsoft Docs"
description: "Gérez votre service Azure Search avec des scripts PowerShell. Créez ou mettez à jour un service Azure Search et gérez les clés d’administration Azure Search"
services: search
documentationcenter: 
author: seansaleh
manager: mblythe
editor: 
tags: azure-resource-manager
ms.assetid: 9b3dc1f2-3619-4235-ba1f-d2d6f5c45dd5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: powershell
ms.date: 08/15/2016
ms.author: seasa
ms.openlocfilehash: aa51c846efef12461ec382274199bc049c42aaa3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gérer votre service Azure Search avec PowerShell
> [!div class="op_single_selector"]
> * [Portail](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Cette rubrique décrit les commandes PowerShell permettant d’exécuter la plupart des tâches de gestion pour les services Azure Search. Nous allons étudier la création d’un service de recherche, sa mise à l’échelle et la gestion de ses clés d’API.
Ces commandes reflètent les options de gestion disponibles dans l’ [API REST de gestion Azure Search](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Composants requis
* Vous devez disposer d’Azure PowerShell 1.0 ou versions ultérieures. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/overview).
* Vous devez être connecté à votre abonnement Azure dans PowerShell, comme décrit ci-dessous.

Tout d’abord, vous devez vous connecter à Azure avec cette commande :

    Login-AzureRmAccount

Spécifiez l’adresse de messagerie électronique et le mot de passe de votre compte Azure dans la boîte de dialogue de connexion à Microsoft Azure.

Vous pouvez également [vous connecter de manière non interactive avec un principal de service](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Si vous avez plusieurs abonnements Azure, vous devez sélectionner l’abonnement Azure à utiliser. Pour afficher une liste de vos abonnements en cours, exécutez la commande suivante.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pour spécifier l’abonnement, exécutez la commande suivante. Dans l’exemple suivant, le nom de l’abonnement est `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Commandes pour vous aider à démarrer
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre service est créé, vous pouvez passer aux étapes suivantes : créer un [index](search-what-is-an-index.md), [interroger un index](search-query-overview.md), puis créer et gérer vos propres applications de recherche utilisant Recherche Azure.

* [Création d’un index Azure Search dans le portail Azure](search-create-index-portal.md)
* [Interrogation d’un index Azure Search à l’aide de Search Explorer dans le Portail Azure](search-explorer.md)
* [Configuration d’un indexeur pour charger des données provenant d’autres services](search-indexer-overview.md)
* [Utilisation d'Azure Search dans .NET](search-howto-dotnet-sdk.md)
* [Analyse du trafic Azure Search](search-traffic-analytics.md)

