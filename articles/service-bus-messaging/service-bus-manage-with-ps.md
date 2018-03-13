---
title: "Utiliser PowerShell pour gérer les ressources Azure Service Bus | Microsoft Docs"
description: "Utiliser le module PowerShell pour créer et gérer les ressources Service Bus"
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4fbc6e18565ec14a3ccb4499b24804f681026023
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Utiliser PowerShell pour gérer les ressources Service Bus

Microsoft Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion des services Azure. Cet article explique comment utiliser le [module PowerShell du Gestionnaire de ressources Service Bus](/powershell/module/azurerm.servicebus) pour approvisionner et gérer les entités Service Bus (espaces de noms, files d’attente, rubriques et abonnements) à l’aide d’une console Azure PowerShell locale ou d’un script.

Vous pouvez également gérer les entités Service Bus à l’aide de modèles Azure Resource Manager. Pour plus d’informations, consultez l’article [Création de ressources Service Bus à l’aide de modèles Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que les prérequis suivants sont respectés :

* Un abonnement Azure. Pour plus d’informations sur la façon de se procurer un abonnement, consultez les [options d’achat][purchase options], les [offres spéciales membres][member offers] ou découvrez comment créer un [compte gratuit][free account].
* Un ordinateur sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la page [Bien démarrer avec les applets de commande Azure PowerShell](/powershell/azure/get-started-azureps).
* Des connaissances générales sur les scripts PowerShell, les packages NuGet et .NET Framework.

## <a name="get-started"></a>Prise en main

La première étape consiste à utiliser PowerShell pour vous connecter à votre compte Azure et à votre abonnement Azure. Suivez les instructions décrites dans [Prise en main des applets de commande Azure PowerShell](/powershell/azure/get-started-azureps) pour vous connecter à votre compte Azure et récupérer les ressources de votre abonnement Azure ainsi qu’accéder à celles-ci.

## <a name="provision-a-service-bus-namespace"></a>Approvisionner un espace de noms Service Bus

Lorsque vous travaillez avec des espaces de noms Service Bus, vous pouvez utiliser les applets de commande [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) et [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace).

Cet exemple crée quelques variables locales dans le script ; `$Namespace` et `$Location`.

* `$Namespace` est le nom de l’espace de noms Service Bus que nous allons utiliser.
* `$Location` identifie le centre de données dans lequel nous approvisionnons l’espace de noms.
* `$CurrentNamespace` stocke l'espace de noms de référence récupéré (ou créé).

Dans un script réel, les variables `$Namespace` et `$Location` peuvent être transmises en tant que paramètres.

Cette partie du script effectue les opérations suivantes :

1. Elle tente de récupérer un espace de noms Service Bus portant le nom spécifié.
2. S'il trouve l'espace de noms recherché, il signale qu'il l'a trouvé.
3. S'il ne trouve pas l'espace de noms recherché, il le crée, puis il récupère le nouvel espace de noms.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Créer une règle d’autorisation d’espace de noms

L’exemple suivant montre comment gérer les règles d’autorisation d’espace de noms à l’aide des applets de commande [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule) et [applets de commande Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Créer une file d’attente

Pour créer une file d’attente ou une rubrique, vérifiez l’espace de noms à l’aide du script décrit dans la section précédente. Ensuite, créez la file d’attente :

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modifier les propriétés de la file d’attente

Après l’exécution du script de la manière décrite dans la section précédente, vous pouvez utiliser l’applet de commande [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) pour mettre à jour les propriétés d’une file d’attente, comme dans l’exemple suivant :

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Approvisionne d'autres entités Service Bus

Vous pouvez utiliser le [module PowerShell de Service Bus](/powershell/module/azurerm.servicebus) pour approvisionner d’autres entités, telles que des rubriques et abonnements. La syntaxe de ces applets de commande est similaire à celle des applets de commande de création de file d’attente décrites dans la section précédente.

## <a name="next-steps"></a>étapes suivantes

- Consultez la documentation complète du module PowerShell du Gestionnaire de ressources Service Bus [ici](/powershell/module/azurerm.servicebus). Cette page répertorie toutes les applets de commande disponibles.
- Pour plus d’informations sur l’utilisation des modèles Azure Resource Manager, consultez l’article [Création de ressources Service Bus à l’aide de modèles Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informations sur les [bibliothèques de gestion Service Bus .NET](service-bus-management-libraries.md).

D’autres manières de gérer les entités Service Bus sont décrites dans les billets de blog suivants :

* [Comment créer des files d'attente, des rubriques et des abonnements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Comment créer un espace de noms et un concentrateur d'événements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Scripts PowerShell pour Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
