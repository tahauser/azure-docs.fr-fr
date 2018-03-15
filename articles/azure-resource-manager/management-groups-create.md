---
title: "Créer des groupes d’administration pour organiser les ressources Azure | Microsoft Docs"
description: "Découvrez comment créer des groupes d’administration Azure pour gérer plusieurs ressources."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: ae91ad29b867ad4ab00831ee40102bcec2fc890c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Créer des groupes d’administration pour la gestion et l’organisation des ressources
Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Créez ces conteneurs pour construire une hiérarchie efficace utilisable avec [Azure Policy](../azure-policy/azure-policy-introduction.md) et les [contrôles d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-what-is.md). Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md). 

La fonctionnalité de groupe d’administration est disponible dans une préversion publique. Pour commencer à utiliser des groupes d’administration, connectez-vous au [portail Azure](https://portal.azure.com) ou utilisez [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) ou l’[API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) pour créer des groupes d’administration.   

La création du premier groupe d’administration dans l’annuaire peut nécessiter jusqu’à 15 minutes. En effet, des processus s’exécutent la première fois pour configurer le service des groupes d’administration dans Azure pour votre annuaire. Vous recevez une notification quand le processus est terminé.  

## <a name="how-to-create-a-management-group"></a>Comment créer un groupe d’administration
Vous pouvez créer un groupe d’administration en utilisant le portail, PowerShell ou Azure CLI.

### <a name="create-in-portal"></a>Créer dans le portail

1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**.
3. Dans la page principale, sélectionnez **Nouveau groupe d’administration**. 

    ![Créer un groupe](media/management-groups/create_main.png) 
4.  Renseignez le champ ID du groupe d’administration. 
    - L’**ID du groupe d’administration** est l’identificateur unique de l’annuaire utilisé pour envoyer des commandes sur ce groupe d’administration. Cet identificateur n’est pas modifiable après sa création car il est utilisé dans tout le système Azure pour identifier ce groupe. 
    - Le champ du nom d’affichage correspond au nom qui s’affiche dans le portail Azure. Un nom d’affichage distinct est un champ facultatif lors de la création du groupe d’administration. Il peut être modifié à tout moment.  

    ![Créer](media/management-groups/create_context_menu.png)  
5.  Sélectionnez **Enregistrer**.


### <a name="create-in-powershell"></a>Créer dans PowerShell
Dans PowerShell, utilisez les applets de commande Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** est un identificateur unique créé. Cet ID est utilisé par d’autres commandes pour faire référence à ce groupe et il ne peut pas être modifié ultérieurement.

Si vous voulez afficher un autre nom pour le groupe d’administration dans le portail Azure, ajoutez le paramètre **DisplayName** avec la chaîne. Par exemple, pour créer un groupe d’administration dont l’identificateur GroupName est Contoso et le nom d’affichage « Contoso Group », utilisez l’applet de commande suivante : 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Utilisez le paramètre **ParentId** pour que ce groupe d’administration soit créé sous un autre groupe d’administration.  

### <a name="create-in-azure-cli"></a>Créer dans Azure CLI
Dans Azure CLI, utilisez la commande az account management-group create. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>étapes suivantes 
Pour en savoir plus sur les groupes d’administration, consultez : 
- [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](management-groups-manage.md)
- [Installer le module Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Passer en revue les spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installer l’extension Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
