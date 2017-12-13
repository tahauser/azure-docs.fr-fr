---
title: "Exemples PowerShell pour gérer des groupes dans Azure Active Directory | Documents Microsoft"
description: "Cette page fournit des exemples PowerShell pour vous aider à gérer vos groupes dans Azure Active Directory"
keywords: Azure AD, Azure Active Directory, PowerShell, Groupes, Gestion des groupes
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: rodejo
ms.openlocfilehash: 2e59528df6a66979c3fc2f596e3e94c1f51f0111
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Applets de commande Azure Active Directory version 2 pour la gestion de groupe
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Cet article contient des exemples expliquant comment utiliser PowerShell pour gérer vos groupes dans Azure Active Directory (Azure AD).  Il fournit également des informations sur la configuration à l’aide du module Azure AD PowerShell. Vous devez tout d’abord [télécharger le module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installer le module Azure AD PowerShell
Pour installer le module PowerShell Azure AD, utilisez les commandes suivantes :

    PS C:\Windows\system32> install-module azuread

Pour vérifier que le module a été installé, utilisez la commande suivante :

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Vous pouvez désormais utiliser les applets de commande dans le module. Pour obtenir une description complète des applets de commande du module Azure AD, consultez la documentation de référence en ligne pour [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Se connecter à l’annuaire
Avant de pouvoir gérer des groupes à l’aide des applets de commande Azure AD PowerShell, vous devez connecter votre session PowerShell à l’annuaire que vous voulez gérer. Utilisez la commande suivante :

    PS C:\Windows\system32> Connect-AzureAD

L’applet de commande vous demande de fournir les informations d’identification à utiliser pour accéder à votre annuaire. Dans cet exemple, nous utilisons karen@drumkit.onmicrosoft.com pour accéder à l’annuaire de démonstration. L’applet de commande retourne une confirmation pour indiquer que la session a été correctement connectée à votre annuaire :

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

À présent, vous pouvez commencer à utiliser les applets de commande Azure AD pour gérer les groupes de votre annuaire.

## <a name="retrieve-groups"></a>Récupérer des groupes
Pour récupérer des groupes existants à partir de votre annuaire, utilisez l’applet de commande Get-AzureADGroups. 

Pour récupérer tous les groupes dans l’annuaire, utilisez l’applet de commande sans paramètres :

    PS C:\Windows\system32> get-azureadgroup

L’applet de commande retourne tous les groupes de l’annuaire connecté.

Vous pouvez utiliser le paramètre -objectID pour récupérer un groupe spécifique pour lequel vous spécifiez le paramètre objectID du groupe :

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

L’applet de commande renvoie à présent l’objectID correspond à la valeur du paramètre que vous avez saisi :

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Vous pouvez rechercher un groupe spécifique en utilisant le paramètre -filter. Ce paramètre utilise une clause de filtre ODATA et renvoie tous les groupes qui correspondent au filtre, comme dans l’exemple suivant :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Les applets de commande Azure AD PowerShell mettent en œuvre la norme de requête OData. Pour plus d’informations, consultez **$filter** dans [options de requête système OData à l’aide du point de terminaison OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Créer des groupes
Pour créer un nouveau groupe dans votre annuaire, utilisez l’applet de commande New-AzureADGroup. Cette applet de commande crée un nouveau groupe de sécurité appelé « Marketing » :

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Mettre à jour des groupes
Pour mettre à jour un groupe existant, utilisez l’applet de commande Set-AzureADGroup. Dans cet exemple, nous modifions la propriété DisplayName du groupe « Administrateurs Intune ». Tout d’abord, nous recherchons le groupe à l’aide de l’applet de commande Get-AzureADGroup et filtrons à l’aide de l’attribut DisplayName :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Ensuite, nous attribuons à la propriété Description la nouvelle valeur « Administrateurs d’appareil Intune » :

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

À présent, si nous recherchons à nouveau le groupe, nous constatons que la propriété Description est mise à jour pour refléter la nouvelle valeur :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="delete-groups"></a>Supprimer des groupes
Pour supprimer des groupes de votre annuaire, utilisez l’applet de commande Remove-AzureADGroup comme suit :

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Gérer l’appartenance au groupe 
### <a name="add-members"></a>Ajouter des membres
Pour ajouter de nouveaux membres à un groupe, utilisez l’applet de commande Add-AzureADGroupMember. Cette commande ajoute un membre au groupe Administrateurs Intune que nous avons utilisé dans l’exemple précédent :

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Le paramètre -ObjectId est l’ObjectID du groupe auquel nous souhaitons ajouter un membre et le paramètre -RefObjectId est l’ObjectID de l’utilisateur que nous souhaitons ajouter au groupe en tant que membre.

### <a name="get-members"></a>Obtenir les membres
Pour obtenir les membres existants d’un groupe, utilisez l’applet de commande Get-AzureADGroupMember, comme dans cet exemple :

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Supprimer des membres
Pour supprimer le membre, que nous avons ajouté précédemment au groupe, utilisez l’applet de commande Remove-AzureADGroupMember, comme illustré ici :

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Vérifier les membres
Pour vérifier l’appartenance d’un utilisateur à un groupe, utilisez l’applet de commande Select-AzureADGroupIdsUserIsMemberOf. Cette applet de commande utilise comme paramètres l’ObjectId de l’utilisateur pour lequel vérifier l’appartenance au groupe et une liste de groupes pour lesquels vérifier les appartenances. La liste des groupes doit être spécifiée sous la forme d’une variable complexe de type « Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck », donc nous devons tout d’abord créer une variable de ce type :

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Ensuite, nous fournissons des valeurs pour les groupIds à vérifier dans l’attribut « GroupIds » de cette variable complexe :

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

À présent, pour vérifier les appartenances aux groupes d’un utilisateur avec l’ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea sur les groupes dans $g, nous devons utiliser :

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


La valeur retournée est une liste de groupes dont cet utilisateur est membre. Vous pouvez également appliquer cette méthode pour vérifier l’appartenance aux Contacts, Groupes ou Principaux du service pour une liste donnée de groupes, à l’aide de Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf ou Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Désactiver la création d’un groupe par vos utilisateurs
Vous pouvez empêcher des utilisateurs non-administrateurs de créer des groupes de sécurité. Le comportement par défaut dans Microsoft Online Directory Services (MSODS) consiste à permettre aux utilisateurs non-administrateurs de créer des groupes, que la gestion de groupes en libre-service soit ou non également activée. Le paramétrage de la gestion de groupes en libre-service contrôle ce comportement uniquement dans le panneau d’accès Mes applications. 

Pour désactiver la création d’un groupe pour les utilisateurs non-administrateurs :

1. Vérifiez que les utilisateurs non-administrateurs sont autorisés à créer des groupes :
   
  ````
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ````
  
2. Si la commande retourne `UsersPermissionToCreateGroupsEnabled : True`, les utilisateurs non-administrateurs peuvent créer des groupes. Pour désactiver cette fonctionnalité :
  
  ```` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ````
  
## <a name="manage-owners-of-groups"></a>Gérer les propriétaires de groupes
Pour ajouter des propriétaires à un groupe, utilisez l’applet de commande Add-AzureADGroupOwner :

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Le paramètre -ObjectId est l’ObjectID du groupe auquel nous souhaitons ajouter un propriétaire et le paramètre -RefObjectId est l’ObjectID de l’utilisateur que nous souhaitons ajouter au groupe en tant que propriétaire.

Pour récupérer les propriétaires d’un groupe, utilisez l’applet de commande Get-AzureADGroupOwner :

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

L’applet de commande renvoie la liste des propriétaires du groupe spécifié :

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Si vous souhaitez supprimer un propriétaire d’un groupe, utilisez l’applet de commande Remove-AzureADGroupOwner :

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Alias réservés 
Quand un groupe est créé, certain points de terminaison autorisent l’utilisateur final à spécifier un mailNickname ou alias à utiliser dans l’adresse e-mail du groupe. Les groupes avec les alias de messagerie hautement privilégiés suivants peuvent uniquement être créés par un administrateur général Azure AD. 
  
* abuse 
* admin 
* administrator 
* hostmaster 
* majordomo 
* postmaster 
* root 
* secure 
* security 
* ssl-admin 
* webmaster 

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez plus d’informations sur Azure Active Directory PowerShell dans la page dédiée aux [applets de commande Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
