---
title: Utiliser l’interface de ligne de commande Azure pour créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure | Microsoft Docs
description: Utilisez PowerShell pour créer une affectation de stratégie Azure pour identifier les ressources non conformes.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: f56f00aabbef2cfa86264d3e962af9a9c0bafa98
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant l’interface de ligne de commande Azure

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources. Ce démarrage rapide vous guide pas à pas dans le processus de création d’une attribution de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques managés. Elles sont *non conformes* à l’attribution de stratégie.

Azure CLI permet de créer et de gérer des ressources Azure à partir de la ligne de commande ou dans des scripts. Ce guide utilise l’interface de ligne de commande Azure pour créer une attribution de stratégie et identifier les ressources non conformes dans votre environnement Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ce démarrage rapide nécessite que vous exécutiez la version 2.0.4 minimum d’Azure CLI pour installer et utiliser l’interface de ligne de commande en local. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).



## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une attribution de stratégie et affectez la définition Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques managés). Cette définition de stratégie identifie les ressources qui ne sont pas conformes aux conditions définies dans la définition de stratégie.

Exécutez la commande suivante pour créer une attribution de stratégie :

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

La commande précédente utilise les informations suivantes :

- **Name** : nom d’affichage pour l’attribution de stratégie. Dans ce cas, nous allons utiliser *Audit Virtual Machines without Managed Disks Assignment* (Auditer des machines virtuelles sans attribution de disques gérés).
- **Policy** : ID de définition de la stratégie, que vous utilisez pour créer l’attribution. Dans ce cas, il s’agit de la définition de stratégie *Audit Virtual Machines without Managed Disks* (Auditer des machines virtuelles sans disques gérés). Pour obtenir l’ID de définition de stratégie, exécutez cette commande : `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Scope** : une étendue détermine les ressources ou le regroupement de ressources sur lequel l’attribution de stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Assurez-vous de remplacer &lt;scope&gt; par le nom de votre groupe de ressources.
- **Sku** : cette commande crée une attribution de stratégie de niveau standard. Le niveau standard vous permet de procéder à grande échelle à la gestion, à l’évaluation de la conformité et à la correction. Le niveau standard est actuellement gratuit. À l’avenir, le niveau standard deviendra payant. La date de la modification de tarification sera annoncée ultérieurement et plus d’informations vous seront alors données sur la [tarification Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Pour afficher les ressources qui ne sont pas conformes à cette nouvelle attribution, obtenez l’ID d’attribution de stratégie en exécutant les commandes suivantes :

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

Pour plus d’informations sur les ID d’attribution de stratégie, voir [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Exécutez ensuite la commande suivante pour obtenir les ID des ressources non conformes, intégrés dans un fichier JSON :

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Vos résultats doivent ressembler à l’exemple suivant :

```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}

```

Les résultats sont comparables à ce que vous devriez généralement voir sous **Ressources non conformes** dans la vue du portail Azure.

## <a name="clean-up-resources"></a>Supprimer des ressources

D’autres guides de cette collection sont basés sur ce démarrage rapide. Si vous prévoyez de continuer avec d’autres didacticiels, ne nettoyez pas les ressources créées dans ce démarrage rapide. Dans le cas contraire, supprimez l’attribution que vous avez créée en exécutant la commande suivante :

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie pour identifier les ressources non conformes de votre environnement Azure.

Pour plus d’informations sur l’attribution de stratégies et pour garantir que les ressources que vous créerez **à l’avenir** sont conformes, continuez avec le didacticiel pour :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./create-manage-policy.md)
