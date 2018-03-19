---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 21216d19fb8a37d3e9e02e410d39b2a999d3935e
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
Pour ajouter deux balises à un groupe de ressources, utilisez la commande [Set-AzureRmResourceGroup](/powershell/module/azurerm.resources/set-azurermresourcegroup) :

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Supposons que vous souhaitez ajouter une troisième balise. Chaque fois que vous appliquez des balises à une ressource ou un groupe de ressources, vous remplacez les balises existantes de cette ressource ou de ce groupe de ressources. Pour ajouter une balise sans perdre les balises existantes, vous devez récupérer les balises existantes, ajouter une balise et réappliquer la collection de balises :

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags += @{Project="Documentation"}

# Reapply the updated set of tags 
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

Les ressources n’héritent pas des balises du groupe de ressources. Votre groupe de ressources possède actuellement trois balises, mais les ressources n’ont pas de balises. Pour appliquer toutes les balises d’un groupe de ressources à ses ressources et conserver les balises existantes sur les ressources qui ne sont pas des doublons, utilisez le script suivant :

```azurepowershell-interactive
# Get the resource group
$group = Get-AzureRmResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = $group | Find-AzureRmResource

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags

        # Loop through each tag from the resource group
        foreach ($key in $group.Tags.Keys)
        {
            # Check if the resource already has a tag for the key from the resource group. If so, remove it from the resource
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }

        # Add the tags from the resource group to the resource tags
        $resourcetags += $group.Tags

        # Reapply the updated tags to the resource 
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Autrement, vous pouvez appliquer des balises du groupe de ressources aux ressources sans conserver les balises existantes :

```azurepowershell-interactive
# Get the resource group
$g = Get-AzureRmResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Pour combiner plusieurs valeurs dans une balise unique, utilisez une chaîne JSON.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Pour supprimer toutes les balises, vous utilisez une table de hachage vide.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
