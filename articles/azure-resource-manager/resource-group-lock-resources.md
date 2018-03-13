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
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 6832bd6dfb136b944a752ae61da74465a01c80a4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Verrouiller les ressources pour empêcher les modifications inattendues 

En tant qu’administrateur, vous pouvez avoir besoin de verrouiller un abonnement, une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier de manière accidentelle des ressources critiques. Vous pouvez définir le niveau de verrouillage sur **CanNotDelete** ou **ReadOnly**. Dans le portail, les verrous sont appelés **Supprimer** et **Lecture seule** respectivement.

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
L’exemple suivant représente un modèle créant un verrou sur un compte de stockage.plan App Service et un verrou sur le site web. Le type de ressource du verrou est le type de ressource de la ressource à verrouiller et **/providers/locks**. Le nom du verrou résulte de la concaténation du nom de la ressource avec **/Microsoft.Authorization/** et le nom du verrou.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Vous pouvez verrouiller des ressources déployées avec Azure PowerShell en utilisant la commande [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock).

Pour verrouiller une ressource, indiquez le nom de la ressource, son type de ressource et son nom de groupe de ressources.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Pour verrouiller un groupe de ressources : indiquez le nom du groupe de ressources.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Pour obtenir des informations sur un verrou, utilisez [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Pour obtenir tous les verrous de votre abonnement, utilisez :

```powershell
Get-AzureRmResourceLock
```

Pour obtenir tous les verrous d’une ressource, utilisez :

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Pour obtenir tous les verrous d’un groupe de ressources, utilisez :

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Pour supprimer un verrou, utilisez :

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Verrouillez les ressources déployées avec Azure CLI à l’aide de la commande [az lock create](/cli/azure/lock#az_lock_create).

Pour verrouiller une ressource, indiquez le nom de la ressource, son type de ressource et son nom de groupe de ressources.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Pour verrouiller un groupe de ressources : indiquez le nom du groupe de ressources.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Pour obtenir des informations sur un verrou, utilisez la commande [az lock list](/cli/azure/lock#az_lock_list). Pour obtenir tous les verrous de votre abonnement, utilisez :

```azurecli
az lock list
```

Pour obtenir tous les verrous d’une ressource, utilisez :

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Pour obtenir tous les verrous d’un groupe de ressources, utilisez :

```azurecli
az lock list --resource-group exampleresourcegroup
```

Pour supprimer un verrou, utilisez :

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>de l’API REST
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
* Pour en savoir plus sur l’organisation logique de vos ressources, consultez [Organisation des ressources à l’aide de balises](resource-group-using-tags.md)
* Pour changer le groupe de ressources où se trouve une ressource, consultez [Déplacer des ressources vers un nouveau groupe de ressources](resource-group-move-resources.md)
* Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Policy ?](../azure-policy/azure-policy-introduction.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).

