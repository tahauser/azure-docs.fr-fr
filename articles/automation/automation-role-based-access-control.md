---
title: "Contrôle d’accès en fonction du rôle dans Azure Automation | Microsoft Docs"
description: "Le contrôle d’accès en fonction du rôle (RBAC) permet de gérer les accès des ressources Azure. Cet article décrit la configuration de RBAC dans Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "rbac automation, contrôle d’accès en fonction du rôle, azure rbac"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: magoedte;sngun
ms.openlocfilehash: 59ec4000645583b8cd35f7e8e475b95ea5499232
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="role-based-access-control-in-azure-automation"></a>Contrôle d’accès en fonction du rôle dans Azure Automation
## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle
Le contrôle d’accès en fonction du rôle (RBAC) permet de gérer les accès des ressources Azure. Avec [RBAC](../active-directory/role-based-access-control-configure.md), vous pouvez répartir les tâches au sein de votre équipe et accorder aux utilisateurs, groupes et applications uniquement les accès nécessaires pour accomplir leur travail. L’accès en fonction du rôle peut être accordé aux utilisateurs à l’aide du portail Azure, des outils en ligne de commande Azure ou des API de gestion Azure.

## <a name="rbac-in-automation-accounts"></a>RBAC dans les comptes Automation
Dans Azure Automation, l’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue de compte Automation. Les rôles intégrés pris en charge par un compte Automation sont présentés ci-après :  

| **Rôle** | **Description** |
|:--- |:--- |
| Propriétaire |Le rôle Propriétaire autorise l’accès à l’ensemble des ressources et actions d’un compte Automation, y compris l’accès aux autres utilisateurs, groupes et applications pour gérer le compte Automation. |
| Collaborateur |Le rôle Collaborateur vous permet de gérer tous les éléments excepté la modification des autorisations d’accès des autres utilisateurs à un compte Automation. |
| Lecteur |Le rôle Lecteur vous permet d’afficher toutes les ressources d’un compte Automation, mais vous ne pouvez pas y apporter de modifications. |
| Opérateur Automation |Le rôle Opérateur Automation vous permet d’effectuer des tâches opérationnelles ; vous pouvez notamment démarrer, arrêter, suspendre, reprendre et planifier des tâches. Ce rôle est utile si vous souhaitez protéger vos ressources de compte Automation telles que les ressources d’informations d’identification et les Runbooks et empêcher leur affichage ou leur modification, tout en autorisant les membres de votre organisation à exécuter ces Runbooks. |
| Administrateur de l'accès utilisateur |Le rôle Administrateur de l’accès utilisateur vous permet de gérer l’accès utilisateur aux comptes Azure Automation. |

> [!NOTE]
> Vous ne pouvez pas accorder de droits d’accès à un ou plusieurs Runbooks spécifiques, mais uniquement aux ressources et actions du compte Automation.  
> 
> 

Dans cet article, nous vous guidons dans la configuration de RBAC dans Azure Automation. Mais tout d’abord, examinons un peu plus attentivement les autorisations individuelles accordées aux rôles Collaborateur, Lecteur, Opérateur Automation et Administrateur de l’accès utilisateur afin d’avoir une bonne compréhension avant d’accorder à quiconque des droits d’accès au compte Automation.  Dans le cas contraire, les conséquences risquent d’être inattendues ou indésirables.     

## <a name="contributor-role-permissions"></a>Autorisations du rôle Collaborateur
Le tableau suivant présente les actions spécifiques qui peuvent être effectuées par le rôle Collaborateur dans Automation :

| **Type de ressource** | **Lire** | **Écrire** | **Supprimer** | **Autres actions** |
|:--- |:--- |:--- |:--- |:--- |
| Compte Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Ressource de certificat Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Ressource de connexion Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Ressource de type de connexion Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Ressource d'identification Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Ressource de planification Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Ressource variable Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Configuration de l’état souhaité Automation | | | |![État vert](media/automation-role-based-access-control/green-checkmark.png) |
| Type de ressource du Runbook Worker hybride |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Travail Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |![État vert](media/automation-role-based-access-control/green-checkmark.png) |
| Flux de travail Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Planification du travail Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Module Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |
| Runbook Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |
| Brouillon de Runbook Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | |![État vert](media/automation-role-based-access-control/green-checkmark.png) |
| Travail de test de brouillon de Runbook Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |![État vert](media/automation-role-based-access-control/green-checkmark.png) |
| Webhook Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) |

## <a name="reader-role-permissions"></a>Autorisations du rôle Lecteur
Le tableau suivant présente les actions spécifiques qui peuvent être effectuées par le rôle Lecteur dans Automation :

| **Type de ressource** | **Lire** | **Écrire** | **Supprimer** | **Autres actions** |
|:--- |:--- |:--- |:--- |:--- |
| Administrateur d’abonnements classiques |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Verrou de gestion |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Autorisation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Opérations de fournisseur |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Affectation de rôle |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Définition de rôle |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="automation-operator-role-permissions"></a>Autorisations du rôle Opérateur Automation
Le tableau suivant présente les actions spécifiques qui peuvent être effectuées par le rôle Opérateur Automation dans Automation :

| **Type de ressource** | **Lire** | **Écrire** | **Supprimer** | **Autres actions** |
|:--- |:--- |:--- |:--- |:--- |
| Compte Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource de certificat Automation | | | | |
| Ressource de connexion Automation | | | | |
| Ressource de type de connexion Azure Automation | | | | |
| Ressource d'identification Automation | | | | |
| Ressource de planification Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | |
| Ressource variable Automation | | | | |
| Configuration de l’état souhaité Automation | | | | |
| Type de ressource du Runbook Worker hybride | | | | |
| Travail Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | |![État vert](media/automation-role-based-access-control/green-checkmark.png) |
| Flux de travail Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Planification du travail Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | |
| Module Automation | | | | |
| Runbook Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Brouillon de Runbook Automation | | | | |
| Travail de test de brouillon de Runbook Automation | | | | |
| Webhook Automation | | | | |

Pour plus de détails, la section sur les [actions de l’opérateur Automation](../active-directory/role-based-access-built-in-roles.md#automation-operator) répertorie les actions prises en charge par le rôle Opérateur Automation dans le compte Automation et dans ses ressources.

## <a name="user-access-administrator-role-permissions"></a>Autorisations du rôle Administrateur de l’accès utilisateur
Le tableau suivant présente les actions spécifiques qui peuvent être effectuées par le rôle Administrateur de l’accès utilisateur dans Automation :

| **Type de ressource** | **Lire** | **Écrire** | **Supprimer** | **Autres actions** |
|:--- |:--- |:--- |:--- |:--- |
| Compte Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource de certificat Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource de connexion Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource de type de connexion Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource d'identification Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource de planification Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ressource variable Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Configuration de l’état souhaité Automation | | | | |
| Type de ressource du Runbook Worker hybride |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Travail Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Flux de travail Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Planification du travail Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Module Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Runbook Azure Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Brouillon de Runbook Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Travail de test de brouillon de Runbook Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Webhook Automation |![État vert](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configurer RBAC pour votre compte Automation à l’aide du portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez votre compte Automation depuis la page Comptes Automation.  
2. Cliquez sur le contrôle **Utilisateurs** . Vous ouvrez ainsi la page **Utilisateurs**. Celle-ci vous permet d’ajouter de nouveaux utilisateurs, groupes et applications pour gérer votre compte Automation et d’afficher les rôles existants qui peuvent être configurés pour le compte Automation.  
   
   ![Bouton Accéder](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> **Administrateurs des abonnements** existe déjà en tant qu’utilisateur par défaut. Le groupe Active Directory Administrateurs des abonnements inclut les administrateurs et les coadministrateurs de services fédérés pour votre abonnement Azure. L’administrateur de services fédérés est le propriétaire de votre abonnement Azure et de ses ressources, et dispose du rôle de propriétaire hérité pour les comptes Automation. En d’autres termes, l’accès est **hérité** pour les **administrateurs et coadministrateurs de services fédérés** d’un abonnement et il est **affecté** pour tous les autres utilisateurs. Cliquez sur **Administrateurs des abonnements** pour afficher plus de détails sur leurs autorisations.  
> 
> 

### <a name="add-a-new-user-and-assign-a-role"></a>Ajouter un nouvel utilisateur et affecter un rôle
1. Dans la page Utilisateurs, cliquez sur **Ajouter** pour ouvrir la page **Ajouter un accès** dans laquelle vous pouvez ajouter un utilisateur, un groupe ou une application et lui affecter un rôle.  
   
   ![Ajouter un utilisateur](media/automation-role-based-access-control/automation-02-add-user.png)  
2. Dans la liste des rôles disponibles, sélectionnez un rôle. Dans le cadre de notre exemple, nous choisirons le rôle **Lecteur** , mais vous pouvez choisir l’un des rôles intégrés disponibles pris en charge par un compte Automation ou un rôle personnalisé que vous avez défini.  
   
   ![Sélectionner un rôle](media/automation-role-based-access-control/automation-03-select-role.png)  
3. Cliquez sur **Ajouter des utilisateurs** pour ouvrir la page **Ajouter des utilisateurs**. Si vous avez ajouté des utilisateurs, des groupes ou des applications pour gérer votre abonnement, ces utilisateurs sont répertoriés, et vous pouvez les sélectionner pour ajouter un accès. Si aucun utilisateur n’est répertorié ou si l’utilisateur que vous souhaitez ajouter n’est pas répertorié, cliquez sur **Inviter** pour ouvrir la page **Convier un invité**. Celle-ci vous permet d’inviter un utilisateur disposant d’une adresse de messagerie de compte Microsoft valide comme Outlook.com, OneDrive ou des identifiants Xbox Live. Une fois que vous avez entré l’adresse de messagerie de l’utilisateur, cliquez sur **Sélectionner** pour ajouter l’utilisateur, puis sur **OK**. 
   
   ![Ajouter des utilisateurs](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   L’utilisateur doit maintenant apparaître dans la page **Utilisateurs** avec le rôle **Lecteur** qui lui a été affecté.  
   
   ![Répertorier les utilisateurs](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   Vous pouvez également affecter un rôle à l’utilisateur à partir de la page **Rôles** . 
4. Dans la page Utilisateurs, cliquez sur **Rôles** pour ouvrir la page **Rôles**. Dans cette page, vous pouvez voir le nom du rôle, ainsi que le nombre d’utilisateurs et de groupes affectés à ce rôle.
   
    ![Affecter un rôle à partir de la page Utilisateurs](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > Le contrôle d’accès en fonction du rôle ne peut être défini qu’au niveau du compte Automation et pas au niveau d’une ressource située au-dessous de ce compte.
   > 
   > 
   
    Vous pouvez affecter plusieurs rôles à un utilisateur, à un groupe ou à une application. Par exemple, si nous ajoutons les rôles **Opérateur Automation** et **Lecteur** pour l’utilisateur, il pourra alors afficher toutes les ressources Automation et exécuter les tâches de Runbook. Vous pouvez développer la liste déroulante pour afficher la liste des rôles affectés à l’utilisateur.  
   
    ![Afficher plusieurs rôles](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### <a name="remove-a-user"></a>Supprimer un utilisateur
Vous pouvez supprimer l’autorisation d’accès d’un utilisateur qui ne gère pas le compte Automation ou qui ne travaille plus pour votre organisation. Pour supprimer un utilisateur, procédez comme suit : 

1. Dans la page **Utilisateurs**, sélectionnez l’affectation de rôle à supprimer.
2. Cliquez sur le bouton **Supprimer** dans le volet des détails de l’affectation.
3. Cliquez sur **Oui** pour confirmer la suppression. 
   
   ![Supprimer des utilisateurs](media/automation-role-based-access-control/automation-08-remove-users.png)  

## <a name="role-assigned-user"></a>Utilisateur affecté à un rôle
Lorsqu’un utilisateur affecté à un rôle se connecte à son compte Automation, il peut alors voir le compte du propriétaire répertorié dans la liste des **répertoires par défaut**. Pour afficher le compte Automation auquel il a été ajouté, il doit basculer le répertoire par défaut sur le répertoire par défaut de l’utilisateur.  

![Annuaire par défaut](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### <a name="user-experience-for-automation-operator-role"></a>Expérience utilisateur pour le rôle d’opérateur Automation
Lorsqu’un utilisateur, qui est affecté au rôle Opérateur Automation, consulte le compte Automation auquel il est affecté, il peut afficher uniquement la liste des Runbooks, ainsi que les tâches et les planifications de Runbook créées dans le compte Automation, mais il ne peut pas afficher leur définition. Il peut démarrer, arrêter, suspendre, reprendre ou planifier la tâche de Runbook. L’utilisateur n’a pas accès aux autres ressources Automation telles que les configurations, les groupes de Workers hybrides ou les nœuds DSC.  

![Pas d’accès aux ressources](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Si l’utilisateur clique sur le Runbook, les commandes permettant d’afficher la source ou de modifier le Runbook ne sont pas fournies, car le rôle d’opérateur Automation ne permet pas d’y accéder.  

![Pas d’accès pour modifier un Runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

L’utilisateur peut afficher et créer des planifications, mais il n’a pas accès à d’autres types de ressource.  

![Aucun accès aux ressources](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Cet utilisateur ne peut pas non plus accéder aux Webhooks associés à un Runbook.

![Pas d’accès aux Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configurer RBAC pour votre compte Automation à l’aide d’Azure PowerShell
L’accès en fonction du rôle peut également être configuré pour un compte Automation à l’aide des [applets de commande Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)ci-dessous.

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) répertorie tous les rôles RBAC qui sont disponibles dans Azure Active Directory. Vous pouvez utiliser cette commande avec la propriété **Name** pour dresser la liste de toutes les actions qui peuvent être effectuées par un rôle spécifique.  
    **Exemple :**  
    ![Obtenir la définition de rôle](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) répertorie les affectations de rôle RBAC d’Azure AD dans l’étendue spécifiée. Sans paramètres, cette commande renvoie toutes les affectations de rôle effectuées dans l’abonnement. Utilisez le paramètre **ExpandPrincipalGroups** pour répertorier les affectations d’accès de l’utilisateur spécifié et des groupes dont il est membre.  
    **Exemple :** utilisez la commande suivante pour dresser la liste de tous les utilisateurs et de leurs rôles dans un compte Automation.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obtenir l’affectation de rôle](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• Utilisez [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) pour affecter un accès à des utilisateurs, des groupes et des applications à une étendue particulière.  
    **Exemple :** utilisez la commande suivante pour affecter le rôle Opérateur Automation à un utilisateur dans l’étendue Compte Automation.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nouvelle affectation de rôle](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Utilisez [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) pour supprimer l’accès d’un utilisateur, groupe ou application spécifié dans une étendue particulière.  
    **Exemple :** utilisez la commande suivante pour supprimer l’utilisateur du rôle Opérateur Automation dans l’étendue Compte Automation.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Dans les exemples indiqués ci-dessus, remplacez **l’ID de connexion**, **l’ID d’abonnement**, le **nom du groupe de ressources** et le **nom du compte Automation** par les détails de votre compte. Choisissez **oui** lorsque vous êtes invité à confirmer avant de supprimer une affectation de rôle d’utilisateur.   

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les différentes façons de configurer RBAC pour Azure Automation, reportez-vous à l’article [Gestion du Contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Pour plus d’informations sur les différentes façons de démarrer un Runbook, consultez l’article [Démarrage d’un Runbook](automation-starting-a-runbook.md)
* Pour en savoir plus sur les différents types de Runbook, consultez l’article [Types de Runbook Azure Automation](automation-runbook-types.md)

