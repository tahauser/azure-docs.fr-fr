---
title: "Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration - Azure | Microsoft Docs"
description: "Découvrez comment tenir et mettre à jour votre hiérarchie de groupes d’administration."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2018
ms.author: rithorn
ms.openlocfilehash: 975f572d9bd0f32825e6a618cd31bbc263885030
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="manage-your-resources-with-management-groups"></a>Gérer vos ressources avec des groupes d’administration 
Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Vous pouvez modifier, supprimer et gérer ces conteneurs pour pouvoir utiliser des hiérarchies avec [Azure Policy](../azure-policy/azure-policy-introduction.md) et les [contrôles d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-what-is.md). Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md).

La fonctionnalité de groupe d’administration est disponible dans une préversion publique. Pour commencer à utiliser des groupes d’administration, connectez-vous au [portail Azure](https://portal.azure.com) ou utilisez [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) ou l’[API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) pour gérer vos groupes d’administration.

Pour apporter des modifications à un groupe d’administration, vous devez avoir un rôle de propriétaire ou contributeur sur le groupe d’administration. Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-what-is.md).

## <a name="change-the-name-of-a-management-group"></a>Modifier le nom d’un groupe d’administration 
Vous pouvez modifier le nom du groupe d’administration en utilisant le portail, PowerShell ou Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Modifier le nom dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**.  
3. Sélectionnez le groupe d’administration à renommer. 
4. Sélectionnez l’option **Renommer le groupe** en haut de la page. 

    ![Renommer le groupe](media/management-groups/detail_action_small.png)
5. Lorsque le menu s’ouvre, entrez le nouveau nom à afficher.

    ![Renommer le groupe](media/management-groups/rename_context.png) 
4. Sélectionnez **Enregistrer**. 

### <a name="change-the-name-in-powershell"></a>Modifier le nom dans PowerShell

Pour mettre à jour le nom d’affichage, utilisez **Update-AzureRmManagementGroup**. Par exemple, pour remplacer le nom de groupe d’administration « Contoso IT » par « Groupe Contoso », exécutez la commande suivante : 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Modifier le nom dans Azure CLI

Pour Azure CLI, utilisez la commande update. 

```azure-cli
C:\> az account management-group update --group-name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Supprimer un groupe d’administration
Pour supprimer un groupe d’administration, les conditions suivantes doivent être remplies :
1. Le groupe d’administration ne contient pas de groupes d’administration enfants ni d’abonnements. 
    - Pour déplacer un abonnement en dehors d’un groupe d’administration, consultez [Déplacer un abonnement vers un autre groupe d’administration](#Move-subscriptions-in-the-hierarchy). 
    - Pour déplacer un groupe d’administration vers un autre groupe d’administration, consultez [Déplacer des groupes d’administration dans la hiérarchie](#Move-management-groups-in-the-hierarchy). 
2. Vous avez des autorisations en écriture sur le rôle de propriétaire ou contributeur du groupe d’administration. Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-what-is.md).  

### <a name="delete-in-the-portal"></a>Supprimer dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**.  
3. Sélectionnez le groupe d’administration à supprimer. 
    
    ![Supprimer un groupe](media/management-groups/delete.png)
4. Sélectionnez **Supprimer**. 
    - Si l’icône est désactivée, placez le curseur de la souris au-dessus d’elle pour en connaître la raison. 
5. Une fenêtre s’ouvre pour que vous confirmiez la suppression du groupe d’administration. 

    ![Supprimer un groupe](media/management-groups/delete_confirm.png) 
6. Sélectionnez **Oui**. 


### <a name="delete-in-powershell"></a>Supprimer dans PowerShell

Utilisez la commande **Remove-AzureRmManagementGroup** dans PowerShell pour supprimer des groupes d’administration. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Supprimer dans Azure CLI
Avec Azure CLI, utilisez la commande az account management-group delete. 

```azure-cli
C:\> az account management-group delete --group-name Contoso
```
---

## <a name="view-management-groups"></a>Afficher des groupes d’administration
Vous pouvez afficher tous les groupes pour lesquels vous avez un rôle RBAC direct ou hérité.  

### <a name="view-in-the-portal"></a>Afficher dans le portail
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**. 
3. La page de la hiérarchie des groupes d’administration se charge et présente tous les groupes auxquels vous avez accès. 
    ![Main](media/management-groups/main.png)
4. Sélectionnez un groupe d’administration particulier pour en afficher les détails.  

### <a name="view-in-powershell"></a>Afficher dans PowerShell
Utilisez la commande Get-AzureRmManagementGroup pour récupérer tous les groupes.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Pour consulter les détails d’un seul groupe d’administration, utilisez le paramètre -GroupName.

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Afficher dans Azure CLI
Utilisez la commande list pour récupérer tous les groupes.  

```azure-cli
az account management-group list
```
Pour consulter les détails d’un seul groupe d’administration, utilisez la commande show.

```azurepowershell-interactive
az account management-group show --group-name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Déplacer des abonnements dans la hiérarchie
L’une des raisons de créer un groupe d’administration est de regrouper des abonnements. Seuls les groupes d’administration et les abonnements peuvent être enfants d’un autre groupe d’administration. Un abonnement déplacé vers un groupe d’administration hérite de toutes les stratégies et de tous les accès utilisateur du groupe d’administration parent. 

Pour déplacer l’abonnement, vous devez avoir deux autorisations : 
- Rôle de « propriétaire » sur l’abonnement enfant.
- Rôle de « propriétaire » ou « contributeur » sur le nouveau groupe d’administration parent. 
- Rôle de « propriétaire » ou « contributeur » sur l’ancien groupe d’administration parent.
Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-what-is.md). 

### <a name="move-subscriptions-in-the-portal"></a>Déplacer des abonnements dans le portail

**Ajouter un abonnement existant à un groupe d’administration**
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**. 
3. Sélectionnez le groupe d’administration que vous envisagez d’utiliser comme parent.      
5. En haut de la page, sélectionnez **Ajouter existant**. 
6. Dans le menu ouvert, sélectionnez le **type de ressource** de l’élément que vous essayez de déplacer, autrement dit **Abonnement**.
7. Sélectionnez l’abonnement dans la liste portant le bon ID. 

    ![Enfants](media/management-groups/add_context_2.png)
8. Sélectionnez « Enregistrer ».

**Supprimer un abonnement d’un groupe d’administration**
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**. 
3. Sélectionnez le groupe d’administration qui est le parent actuel.  
4. Dans la liste, sélectionnez les points de suspension situés en fin de la ligne de l’abonnement à déplacer.

    ![Déplacer](media/management-groups/move_small.png)
5. Sélectionnez **Déplacer**.
6. Dans le menu qui s’ouvre, sélectionnez le **groupe d’administration parent**.

    ![Déplacer](media/management-groups/move_small_context.png)
7. Sélectionnez **Enregistrer**.

### <a name="move-subscriptions-in-powershell"></a>Déplacer des abonnements dans PowerShell
Pour déplacer un abonnement dans PowerShell, utilisez la commande Add-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
Add-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Pour supprimer le lien entre un abonnement et un groupe d’administration, utilisez la commande Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Déplacer des abonnements dans Azure CLI
Pour déplacer un abonnement dans CLI, utilisez la commande add. 

```azure-cli
C:\> az account management-group add --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Pour supprimer l’abonnement du groupe d’administration, utilisez la commande subscription remove.  

```azure-cli
C:\> az account management-group remove --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Déplacer des groupes d’administration dans la hiérarchie  
Lorsque vous déplacez un groupe d’administration parent, toutes les ressources enfants qui incluent des groupes d’administration, des abonnements, des groupes de ressources et des ressources se déplacent avec le parent.   

### <a name="move-management-groups-in-the-portal"></a>Déplacer des groupes d’administration dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**. 
3. Sélectionnez le groupe d’administration que vous envisagez d’utiliser comme parent.      
5. En haut de la page, sélectionnez **Ajouter existant**.
6. Dans le menu ouvert, sélectionnez le **type de ressource** de l’élément que vous essayez de déplacer, autrement dit **Groupe d’administration**.
7. Sélectionnez le groupe d’administration portant le bon ID et le bon nom.

    ![déplacer](media/management-groups/add_context.png)
8. Sélectionnez **Enregistrer**.

### <a name="move-management-groups-in-powershell"></a>Déplacer des groupes d’administration dans PowerShell
Utilisez la commande Update-AzureRmManagementGroup dans PowerShell pour déplacer un groupe d’administration sous un autre groupe.  

```powershell
C:\> Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Déplacer des groupes d’administration dans Azure CLI
Utilisez la commande update pour déplacer un groupe d’administration avec Azure CLI. 

```azure-cli
C:/> az account management-group udpate --group-name Contoso --parent-id "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>Étapes suivantes 
Pour en savoir plus sur les groupes d’administration, consultez : 
- [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md)
- [Créer des groupes d’administration pour organiser les ressources Azure](management-groups-create.md)
- [Installer le module Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Passer en revue les spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installer l’extension Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)