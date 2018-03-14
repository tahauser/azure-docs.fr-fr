---
title: "Utiliser PowerShell pour gérer les ressources Azure Event Hubs | Microsoft Docs"
description: "Utiliser le module PowerShell pour créer et gérer Event Hubs"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: de86b8241166d4e0bd03beb22550464457e3db5e
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Utiliser PowerShell pour gérer des ressources Event Hubs

Microsoft Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion des services Azure. Cet article explique comment utiliser le [module PowerShell du Gestionnaire de ressources Event Hubs](/powershell/module/azurerm.eventhub) pour approvisionner et gérer les entités Event Hubs (espaces de noms, concentrateurs d’événements individuels et groupes de consommateurs) à l’aide d’une console Azure PowerShell locale ou d’un script.

Vous pouvez également gérer les ressources Event Hubs avec des modèles Azure Resource Manager. Pour plus d’informations, consultez l’article [Créer un espace de noms Event Hubs avec un concentrateur d’événements et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Prérequis

Avant de débuter, vous avez besoin des éléments suivants :

* Un abonnement Azure. Pour plus d’informations sur la façon de se procurer un abonnement, consultez les [options d’achat][purchase options], les [offres spéciales membres][member offers] ou découvrez comment créer un [compte gratuit][free account].
* Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la page [Bien démarrer avec les applets de commande Azure PowerShell](/powershell/azure/get-started-azureps).
* Des connaissances générales sur les scripts PowerShell, les packages NuGet et .NET Framework.

## <a name="get-started"></a>Prise en main

La première étape consiste à utiliser PowerShell pour vous connecter à votre compte Azure et à votre abonnement Azure. Suivez les instructions décrites dans [Prise en main des applets de commande Azure PowerShell](/powershell/azure/get-started-azureps) pour vous connecter à votre compte Azure, puis récupérez les ressources de votre abonnement Azure et accédez à celles-ci.

## <a name="provision-an-event-hubs-namespace"></a>Approvisionner un espace de noms Event Hubs

Lorsque vous travaillez avec des espaces de noms Event Hubs, vous pouvez utiliser les applets de commande [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) et [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace).

Cet exemple crée quelques variables locales dans le script ; `$Namespace` et `$Location`.

* `$Namespace` est le nom de l’espace de noms Event Hubs que nous allons utiliser.
* `$Location` identifie le centre de données dans lequel nous allons approvisionner l'espace de noms.
* `$CurrentNamespace` stocke l'espace de noms de référence récupéré (ou créé).

Dans un script réel, les variables `$Namespace` et `$Location` peuvent être transmises en tant que paramètres.

Cette partie du script effectue les opérations suivantes :

1. Il tente de récupérer un espace de noms Event Hubs portant le nom spécifié.
2. S'il trouve l'espace de noms recherché, il signale qu'il l'a trouvé.
3. S'il ne trouve pas l'espace de noms recherché, il le crée, puis il récupère le nouvel espace de noms.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Création d'un concentrateur d'événements

Pour créer un concentrateur d’événements, vérifiez l’espace de noms à l’aide du script décrit dans la section précédente. Ensuite, utilisez l’applet de commande [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) pour créer le concentrateur d’événements :

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Créer un groupe de consommateurs

Pour créer un groupe de consommateurs au sein d’un concentrateur d’événements, vérifiez l’espace de noms et l’instance Event Hub à l’aide des scripts de la section précédente. Ensuite, utilisez l’applet de commande [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) pour créer le groupe de consommateurs dans le concentrateur d’événements. Par exemple : 

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Définir les métadonnées utilisateur

Après avoir exécuté les scripts des sections précédentes, vous pourrez utiliser l’applet de commande [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) pour mettre à jour les propriétés d’un groupe de consommateurs, comme dans l’exemple suivant :

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Supprimer un concentrateur d’événements

Pour supprimer les concentrateur d’événements que vous avez créés, vous pouvez utiliser les applets de commande `Remove-*`, comme dans l’exemple suivant :

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>étapes suivantes

- Consultez la documentation complète du module PowerShell du Gestionnaire de ressources Event Hubs [ici](/powershell/module/azurerm.eventhub). Cette page liste toutes les applets de commande disponibles.
- Pour plus d’informations sur les modèles Azure Resource Manager, consultez l’article [Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informations sur les [bibliothèques de gestion .NET Event Hubs](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
