---
title: "Verrouiller les ressources Azure pour empêcher les modifications | Microsoft Docs"
description: "Empêchez les utilisateurs de mettre à jour ou de supprimer des ressources Azure critiques en appliquant un verrou à tous les utilisateurs et rôles."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: d7b091f4a437781547610624007ac1d7f22fed61
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Verrouiller les ressources pour empêcher les modifications inattendues 
En tant qu’administrateur, vous pouvez avoir besoin de verrouiller un abonnement, une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier de manière accidentelle des ressources critiques. Vous pouvez définir le niveau de verrouillage sur **CanNotDelete** ou **ReadOnly**. 

* **CanNotDelete** signifie que les utilisateurs autorisés peuvent lire et modifier une ressource, mais pas la supprimer. 
* **ReadOnly** signifie que les utilisateurs autorisés peuvent lire une ressource, mais pas la supprimer ni la mettre à jour. Appliquer ce verrou revient à limiter à tous les utilisateurs autorisés les autorisations accordées par le rôle **Lecteur**. 

## <a name="how-locks-are-applied"></a>Application des verrous

Lorsque vous appliquez un verrou à une étendue parente, toutes les ressources de cette étendue héritent du même verrou. Même les ressources que vous ajoutez par la suite héritent du verrou du parent. Le verrou le plus restrictif de l’héritage est prioritaire.

Contrairement au contrôle d'accès basé sur les rôles, vous utilisez des verrous de gestion pour appliquer une restriction à tous les utilisateurs et rôles. Pour en savoir plus sur la définition des autorisations pour les utilisateurs et les rôles, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

Les verrous Resource Manager s'appliquent uniquement aux opérations qui se produisent dans le plan de gestion, c'est-à-dire les opérations envoyées à `https://management.azure.com`. Les verrous ne limitent pas la manière dont les ressources exécutent leurs propres fonctions. Les modifications des ressources sont limitées, mais pas les opérations sur les ressources. Par exemple, un verrou ReadOnly sur une base de données SQL vous empêche de supprimer ou de modifier cette base de données, mais il ne vous empêche pas de créer, mettre à jour ou supprimer les données qu'elle contient. Les transactions de données sont autorisées car ces opérations ne sont pas envoyées à `https://management.azure.com`.

L’application de **ReadOnly** peut produire des résultats inattendus, car certaines opérations qui ressemblent à des opérations de lecture nécessitent en fait des actions supplémentaires. Par exemple, le placement d’un verrou **ReadOnly** sur un compte de stockage empêche tous les utilisateurs de répertorier les clés. L’opération de listage de clés est gérée via une demande POST, car les clés retournées sont disponibles pour les opérations d’écriture. Autre exemple : le placement d’un verrou **ReadOnly** sur une ressource App Service empêche l’Explorateur de serveurs Visual Studio d’afficher les fichiers de la ressource, car cette interaction requiert un accès en écriture.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Personnes autorisées à créer ou supprimer des verrous dans votre organisation
Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à des actions `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions.

## <a name="portal"></a>Portail
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modèle
L’exemple suivant représente un modèle créant un verrou sur un compte de stockage. Le compte de stockage auquel est appliqué le verrou est fourni en tant que paramètre. Le nom du verrou résulte de la concaténation du nom de la ressource avec **/Microsoft.Authorization/** et le nom du verrou, en l’occurrence **myLock**.

Le type fourni est spécifique au type de ressource. Pour le stockage, définissez le type suivant : « Microsoft.Storage/storageaccounts/providers/locks ».

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="powershell"></a>PowerShell
Vous pouvez verrouiller des ressources déployées avec Azure PowerShell en utilisant la commande [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock).

Pour verrouiller une ressource, indiquez le nom de la ressource, son type de ressource et son nom de groupe de ressources.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite `
  -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Pour verrouiller un groupe de ressources : indiquez le nom du groupe de ressources.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete `
  -ResourceGroupName exampleresourcegroup
```

Pour obtenir des informations sur un verrou, utilisez [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Pour obtenir tous les verrous de votre abonnement, utilisez :

```powershell
Get-AzureRmResourceLock
```

Pour obtenir tous les verrous d’une ressource, utilisez :

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Pour obtenir tous les verrous d’un groupe de ressources, utilisez :

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Azure PowerShell fournit d’autres commandes d’utilisation des verrous, comme [Set-AzureRmResourceLock](/powershell/module/azurerm.resources/set-azurermresourcelock), pour mettre à jour un verrou et [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) pour supprimer un verrou.

## <a name="azure-cli"></a>Interface de ligne de commande Azure

Verrouillez les ressources déployées avec Azure CLI à l’aide de la commande [az lock create](/cli/azure/lock#create).

Pour verrouiller une ressource, indiquez le nom de la ressource, son type de ressource et son nom de groupe de ressources.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete \
  --resource-group exampleresourcegroup --resource-name examplesite \
  --resource-type Microsoft.Web/sites
```

Pour verrouiller un groupe de ressources : indiquez le nom du groupe de ressources.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete \
  --resource-group exampleresourcegroup
```

Pour obtenir des informations sur un verrou, utilisez la commande [az lock list](/cli/azure/lock#list). Pour obtenir tous les verrous de votre abonnement, utilisez :

```azurecli
az lock list
```

Pour obtenir tous les verrous d’une ressource, utilisez :

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite \
  --namespace Microsoft.Web --resource-type sites --parent ""
```

Pour obtenir tous les verrous d’un groupe de ressources, utilisez :

```azurecli
az lock list --resource-group exampleresourcegroup
```

Azure CLI fournit d’autres commandes d’utilisation des verrous, telles que [az lock update](/cli/azure/lock#update) pour mettre à jour un verrou, et [az lock delete](/cli/azure/lock#delete) pour supprimer un verrou.

## <a name="rest-api"></a>API REST
Vous pouvez verrouiller des ressources déployées à l’aide de l’ [API REST pour les verrous de gestion](https://docs.microsoft.com/rest/api/resources/managementlocks). L’API REST vous permet de créer et de supprimer des verrous, et de récupérer des informations relatives aux verrous existants.

Pour créer un verrou, exécutez :

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Le verrou peut être appliqué à un abonnement, à un groupe de ressources ou à une ressource. Le nom du verrou est personnalisable. Pour la version de l’API, utilisez **2015-01-01**.

Dans la demande, incluez un objet JSON spécifiant les propriétés du verrou.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’utilisation de verrous sur des ressources, consultez [Lock Down Your Azure Resources](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Pour en savoir plus sur l’organisation logique de vos ressources, consultez [Organisation des ressources à l’aide de balises](resource-group-using-tags.md)
* Pour changer le groupe de ressources où se trouve une ressource, consultez [Déplacer des ressources vers un nouveau groupe de ressources](resource-group-move-resources.md)
* Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Policy ?](../azure-policy/azure-policy-introduction.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).

