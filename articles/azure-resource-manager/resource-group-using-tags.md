---
title: Organisation logique des ressources Azure à l’aide de balises | Microsoft Docs
description: Indique comment appliquer des balises afin d’organiser des ressources Azure dédiées à la facturation et à la gestion.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: c677b80ba6c274cfa9936378110b63e583a102b6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Organisation des ressources Azure à l’aide de balises

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

## <a name="powershell"></a>PowerShell

Les exemples de cet article nécessitent la version 3.0 ou ultérieure d’Azure PowerShell. Si vous n’avez pas la version 3.0, vous devez [mettre à jour votre version](/powershell/azureps-cmdlets-docs/) à l’aide de PowerShell Gallery ou de Web Platform Installer.

Pour afficher les balises existantes pour un *groupe de ressources*, utilisez :

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Le script retourne les informations au format suivant :

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Pour afficher les balises existantes pour une *ressource dont l’ID de ressource est spécifié*, utilisez :

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Alternativement, pour afficher les balises existantes pour une *ressource dont le nom et le groupe de ressources sont spécifiés*, utilisez :

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Pour obtenir *les groupes de ressources contenant une balise spécifique*, utilisez :

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Pour obtenir *les ressources contenant une balise spécifique*, utilisez :

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Chaque fois que vous appliquez des balises à une ressource ou un groupe de ressources, vous remplacez les balises existantes de cette ressource ou de ce groupe de ressources. Par conséquent, vous devez utiliser une approche différente selon que la ressource ou le groupe de ressources a des balises existantes.

Pour ajouter des balises à un *groupe de ressources ne contenant pas de balises existantes*, utilisez :

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Pour ajouter des balises à un *groupe de ressources avec des balises existantes*, récupérez les balises existantes, ajoutez la nouvelle balise et réappliquez les balises :

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Pour ajouter des balises à une *ressource ne contenant pas de balises existantes*, utilisez :

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Pour ajouter des balises à une *ressource avec des balises existantes*, utilisez :

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Pour appliquer toutes les balises d’un groupe de ressources à ses ressources *sans conserver les balises existantes*, utilisez le script suivant :

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Pour appliquer toutes les balises d’un groupe de ressources à ses ressources et *conserver les balises existantes sur les ressources qui ne sont pas des doublons*, utilisez le script suivant :

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Pour supprimer toutes les balises, utilisez une table de hachage vide :

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```


## <a name="azure-cli"></a>Azure CLI

Pour afficher les balises existantes pour un *groupe de ressources*, utilisez :

```azurecli
az group show -n examplegroup --query tags
```

Le script retourne les informations au format suivant :

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Alternativement, pour afficher les balises existantes pour une *ressource dont le nom, le type et le groupe sont spécifiés*, utilisez :

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Durant un bouclage sur une collection de ressources, vous préférez peut-être afficher la ressource par ID de ressource. Un exemple complet est présenté plus loin dans cet article. Pour afficher les balises existantes pour une *ressource dont l’ID de ressource est spécifié*, utilisez :

```azurecli
az resource show --id <resource-id> --query tags
```

Pour obtenir des groupes de ressources contenant une balise spécifique, utilisez `az group list` :

```azurecli
az group list --tag Dept=IT
```

Pour obtenir toutes les ressources contenant une balise et une valeur spécifiques, utilisez `az resource list` :

```azurecli
az resource list --tag Dept=Finance
```

Chaque fois que vous appliquez des balises à une ressource ou un groupe de ressources, vous remplacez les balises existantes de cette ressource ou de ce groupe de ressources. Par conséquent, vous devez utiliser une approche différente selon que la ressource ou le groupe de ressources a des balises existantes.

Pour ajouter des balises à un *groupe de ressources ne contenant pas de balises existantes*, utilisez :

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Pour ajouter des balises à une *ressource ne contenant pas de balises existantes*, utilisez :

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Pour ajouter des balises à une ressource qui comporte déjà les balises, récupérez les balises existantes et reformatez cette valeur, puis réappliquez les balises nouvelles et existantes : 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Pour appliquer toutes les balises d’un groupe de ressources à ses ressources *sans conserver les balises existantes*, utilisez le script suivant :

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Pour appliquer toutes les balises d’un groupe de ressources à ses ressources *en conservant les balises existantes*, utilisez le script suivant :

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Modèles

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portail

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>de l’API REST

Le portail Azure et PowerShell utilisent tous deux [l’API REST du Gestionnaire de ressources](https://docs.microsoft.com/rest/api/resources/) en arrière-plan. Si vous avez besoin d’intégrer le balisage dans un autre environnement, vous pouvez récupérer des balises en utilisant **GET** sur l’ID de ressource et mettre à jour le jeu de balises en utilisant un appel **PATCH**.

## <a name="tags-and-billing"></a>Balises et facturation

Vous pouvez utiliser des balises pour regrouper vos données de facturation. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais des [API Resource Usage et RateCard](../billing/billing-usage-rate-card-overview.md) ou du fichier de valeurs séparées par des virgules (CSV). Téléchargez le fichier d’utilisation depuis le [portail des comptes Azure](https://account.windowsazure.com/) ou depuis le [portail EA](https://ea.azure.com). Pour plus d’informations sur l’accès par programme aux informations de facturation, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne **Balises** . Pour plus d’informations, consultez [Comprendre votre facture Microsoft Azure](../billing/billing-understand-your-bill.md).

![Voir les balises dans la facturation](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. La stratégie que vous définissez peut exiger que toutes les ressources aient une valeur pour une balise en particulier. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Policy ?](../azure-policy/azure-policy-introduction.md).
* Pour plus d’informations sur l’utilisation d’Azure PowerShell lors du déploiement de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).
* Si vous n’avez jamais utilisé l’interface CLI Azure pour le déploiement de ressources, consultez [Utiliser l’interface CLI Azure pour Mac, Linux et Windows avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Pour plus d’informations sur l’utilisation du portail, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](resource-group-portal.md).  
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
