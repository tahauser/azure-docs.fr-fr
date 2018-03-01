---
title: "Gestion du Contrôle d’accès en fonction du rôle avec Azure PowerShell | Microsoft Docs"
description: "Comment gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell, notamment le référencement des rôles, l’attribution de rôles et la suppression d’attributions de rôle."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5e7e01502a173cb93216c77dd43f61094fd88148
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Gestion du Contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)

Le contrôle d’accès en fonction du rôle (RBAC) vous permet de définir l’accès des utilisateurs, des groupes et des principaux de service en attribuant des rôles dans une étendue déterminée. Cet article explique comment gérer l’accès à l’aide d’Azure PowerShell.

Pour pouvoir utiliser PowerShell afin de gérer le contrôle d’accès en fonction du rôle (RBAC), vous devez disposer des composants suivants :

* Azure PowerShell, version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
* Applets de commande Azure Resource Manager. Installez les [applets de commande Azure Resource Manager](/powershell/azure/overview) dans PowerShell.

## <a name="list-roles"></a>Répertorier les rôles
### <a name="list-all-available-roles"></a>Répertorier tous les rôles disponibles
Pour répertorier les rôles RBAC pouvant être affectés et inspecter les opérations auxquelles ils accordent l’accès, utilisez `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Répertorier les actions d'un rôle
Pour répertorier les actions pour un rôle spécifique, utilisez `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - Get-AzureRmRoleDefinition pour un rôle spécifique - capture d’écran](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Voir quel utilisateur a des autorisations d’accès
Pour répertorier les attributions d’accès RBAC, utilisez `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Répertorier les attributions de rôle dans une étendue spécifique
Vous pouvez voir toutes les attributions d’accès pour un abonnement, un groupe de ressources ou une ressource. Par exemple, pour voir toutes les attributions actives pour un groupe de ressources, utilisez `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment pour un groupe de ressource - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Répertorier les rôles attribués à un utilisateur
Pour répertorier tous les rôles attribués à un utilisateur spécifié et aux rôles affectés aux groupes auxquels appartient l’utilisateur, utilisez `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment pour un utilisateur - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Répertorier les affectations classiques des rôles Administrateur de service et Coadministrateur
Pour répertorier les affectations d'accès de liste pour les administrateurs d'abonnement et les coadministrateurs classiques, utilisez :

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Accorder l'accès
### <a name="search-for-object-ids"></a>Rechercher des ID d’objet
Pour attribuer un rôle, vous devez identifier l’objet (utilisateur, groupe ou application) et l’étendue.

Si vous ne connaissez pas l’ID d’abonnement, vous pouvez le trouver dans le panneau **Abonnements** du portail Azure. Pour découvrir comment rechercher l'ID d'abonnement, consultez [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) sur MSDN.

Pour obtenir l’ID d’objet pour un groupe Azure AD, utilisez :

    Get-AzureRmADGroup -SearchString <group name in quotes>

Pour obtenir l’ID d’objet pour un principal du service Azure AD ou une application, utilisez :

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Affectation d'un rôle à une application pour l'abonnement
Pour accorder l'accès à une application pour l'abonnement, utilisez :

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Affectation d'un rôle à un utilisateur pour le groupe de ressources
Pour accorder l'accès à un utilisateur pour le groupe de ressources, utilisez :

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Affectation d'un rôle à un utilisateur pour des ressources
Pour accorder l'accès à un groupe au niveau des ressources, utilisez :

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Suppression d'accès
Pour supprimer l'accès pour des utilisateurs, des groupes et des applications, utilisez :

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé
Pour créer un rôle personnalisé, utilisez la commande ```New-AzureRmRoleDefinition``` . Il existe deux méthodes pour structurer le rôle, avec PSRoleDefinitionObject ou avec un modèle JSON. 

## <a name="get-actions-for-a-resource-provider"></a>Actions Get pour un fournisseur de ressources
Si vous créez des rôles personnalisés de A à Z, il est important de connaître toutes les opérations possibles auprès des fournisseurs de ressources.
Utilisez la commande ```Get-AzureRMProviderOperation``` pour obtenir ces informations.
Par exemple, si vous souhaitez vérifier toutes les opérations disponibles pour la machine virtuelle, utilisez cette commande :

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Création d’un rôle avec PSRoleDefinitionObject
Lorsque vous utilisez PowerShell pour créer un rôle personnalisé, vous pouvez commencer de zéro ou utiliser un des [rôles intégrés](role-based-access-built-in-roles.md) comme point de départ. L’exemple de cette section commence par un rôle intégré, puis le personnalise avec des privilèges supplémentaires. Modifiez les attributs et ajoutez les propriétés *Actions*, *notActions*, ou les *étendues* de votre choix, puis enregistrez les modifications sous un nouveau rôle.

L’exemple suivant commence par le rôle *Collaborateur de machine virtuelle* et l’utilise pour créer un rôle personnalisé appelé *Opérateur de machine virtuelle*. Le nouveau rôle accorde l’accès à toutes les opérations des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network*, ainsi que l’accès pour démarrer, redémarrer et surveiller des machines virtuelles. Le rôle personnalisé peut être utilisé dans deux abonnements.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Création d’un rôle avec le modèle JSON
Un modèle JSON peut servir de définition source pour le rôle personnalisé. L’exemple suivant crée un rôle personnalisé qui autorise l’accès en lecture au stockage et aux ressources de calcul, ainsi que l’accès au support, et ajoute ce rôle à deux abonnements. Créez un fichier `C:\CustomRoles\customrole1.json` avec l’exemple de contenu suivant. L’ID doit être défini sur `null` lors de la création du rôle, étant donné qu’un nouvel ID est automatiquement généré. 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Pour ajouter le rôle aux abonnements, exécutez la commande PowerShell suivante :
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Modifier un rôle personnalisé
Comme pour la création d’un rôle personnalisé, vous pouvez modifier un rôle personnalisé existant à l’aide de PSRoleDefinitionObject ou d’un modèle JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Modification d’un rôle avec PSRoleDefinitionObject
Pour modifier un rôle personnalisé, utilisez d’abord la commande `Get-AzureRmRoleDefinition` pour récupérer la définition de rôle. Apportez ensuite les modifications souhaitées à la définition de rôle. Enfin, utilisez la commande `Set-AzureRmRoleDefinition` pour enregistrer la définition de rôle modifiée.

L’exemple suivant ajoute l’opération `Microsoft.Insights/diagnosticSettings/*` au rôle personnalisé *Opérateur de machine virtuelle* .

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

L’exemple suivant ajoute un abonnement Azure aux étendues attribuables du rôle personnalisé *Opérateur de machine virtuelle* .

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Modification d’un rôle avec le modèle JSON
À l’aide du modèle JSON précédent, vous pouvez facilement modifier un rôle personnalisé existant pour ajouter ou supprimer des actions. Mettez à jour le modèle JSON et ajoutez l’action de lecture pour la mise en réseau, comme l’indique l’exemple suivant. Les définitions figurant dans le modèle ne sont pas cumulativement appliquées à une définition existante, ce qui veut dire que le rôle s’affiche exactement comme vous le spécifiez dans le modèle. Vous devez aussi mettre à jour le champ ID avec l’ID du rôle. Si vous n’êtes pas certain de cette valeur, vous pouvez utiliser l’applet de commande `Get-AzureRmRoleDefinition` pour obtenir ces informations.

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Pour mettre à jour le rôle existant, exécutez la commande PowerShell suivante :
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé
Pour supprimer un rôle personnalisé, utilisez la commande `Remove-AzureRmRoleDefinition` .

L’exemple suivant supprime le rôle personnalisé *Opérateur de machine virtuelle* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Répertorier les rôles personnalisés
Pour répertorier les rôles pouvant être affectés dans une étendue, utilisez la commande `Get-AzureRmRoleDefinition` .

L’exemple suivant répertorie tous les rôles pouvant être affectés dans l’abonnement sélectionné.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Dans l’exemple suivant, le rôle personnalisé *Opérateur de machine virtuelle* n’est pas disponible dans l’abonnement *Production4*, car cet abonnement ne figure pas dans l’élément **AssignableScopes** du rôle.

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Voir aussi
* [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

