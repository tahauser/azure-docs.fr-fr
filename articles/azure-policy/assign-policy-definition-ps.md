---
title: Démarrage rapide - Utiliser PowerShell pour créer une attribution de stratégie pour identifier les ressources non conformes dans votre environnement Azure | Microsoft Docs
description: Dans ce démarrage rapide, vous utiliserez PowerShell pour créer une attribution de stratégie Azure pour identifier les ressources non conformes.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 45c5ccd0f891a5592eee7400de108c5097f75286
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Démarrage rapide : Créer une attribution de stratégie pour identifier les ressources non conformes à l’aide du module AzureRM PowerShell

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources. Dans ce démarrage rapide, vous créerez une attribution de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés. Lorsque vous aurez terminé, vous identifierez les machines virtuelles qui ne sont *pas conformes* à l’attribution de stratégie.

Le module AzureRM PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans des scripts. Ce guide explique comment utiliser AzureRM pour créer une attribution de stratégie. La stratégie identifie les ressources non conformes dans votre environnement Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


- Avant de commencer, assurez-vous que la dernière version de PowerShell est installée. Consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) pour plus de détails.
- Mettez à jour votre module AzureRM PowerShell vers la dernière version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une affectation de stratégie et attribuons la définition *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*. Cette définition de stratégie identifie les ressources qui ne sont pas conformes aux conditions définies dans la définition de stratégie.

Exécutez la commande suivante pour créer une nouvelle attribution de stratégie :

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

Les commandes précédentes utilisent les informations suivantes :

- **Name** : nom d’affichage pour l’attribution de stratégie. Dans ce cas, nous allons utiliser *Audit Virtual Machines without Managed Disks Assignment* (Auditer des machines virtuelles sans attribution de disques managés).
- **Definition** : définition de la stratégie, que vous utilisez pour créer l’attribution. Dans ce cas, il s’agit de la définition de stratégie *Audit Virtual Machines without Managed Disks* (Auditer des machines virtuelles sans disques managés).
- **Scope** : une étendue détermine les ressources ou le regroupement de ressources sur lequel l’attribution de stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Assurez-vous de remplacer &lt;scope&gt; par le nom de votre groupe de ressources.
- **Sku** : cette commande crée une attribution de stratégie de niveau Standard. Le niveau Standard vous permet de procéder à grande échelle à la gestion, à l’évaluation de la conformité et à la correction. Pour plus de détails concernant les niveaux tarifaires, consultez [Tarification Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Utilisez les informations suivantes pour identifier les ressources qui ne sont pas conformes à l’attribution de stratégie que vous avez créée. Exécutez les commandes suivantes :

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

Pour plus d’informations sur les ID d’attribution de stratégie, voir [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Exécutez ensuite la commande suivante pour obtenir les ID des ressources non conformes, intégrés dans un fichier JSON :

```powershell
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

Les guides suivants de cette collection sont basés sur ce démarrage rapide. Si vous prévoyez de continuer avec d’autres didacticiels, ne nettoyez pas les ressources créées dans ce démarrage rapide. Dans le cas contraire, vous pouvez supprimer l’attribution que vous avez créée en exécutant cette commande :

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie pour identifier les ressources non conformes de votre environnement Azure.

Pour plus d’informations sur l’attribution de stratégies et garantir que les ressources que vous créerez **à l’avenir** sont conformes, continuez avec le didacticiel suivant :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./create-manage-policy.md)
