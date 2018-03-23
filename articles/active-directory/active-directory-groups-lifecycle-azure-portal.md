---
title: Expiration des groupes Office 365 dans Azure Active Directory | Microsoft Docs
description: Comment configurer l’expiration des groupes Office 365 dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 95593eaacd73316ab527ffda8f977fbf0eb15558
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurer la stratégie d’expiration pour les groupes Office 365

Vous pouvez désormais gérer le cycle de vie des groupes Office 365 en définissant une stratégie d’expiration pour ces derniers. Vous pouvez définir la stratégie d’expiration uniquement pour les groupes Office 365 d’Azure Active Directory (Azure AD). 

Une fois que vous avez défini l’expiration d’un groupe :
-   Les propriétaires du groupe sont invités à renouveler le groupe à l’approche de l’expiration
-   Les groupes non renouvelés sont supprimés
-   Les groupes Office 365 qui sont supprimés peuvent être restaurés dans les 30 jours qui suivent par les propriétaires des groupes ou l’administrateur

> [!NOTE]
> La configuration et l’utilisation de la stratégie d’expiration pour les groupes Office 365 nécessitent que vous disposiez de licences Azure AD Premium pour tous les membres des groupes auxquels la stratégie d’expiration est appliquée.

Pour plus d’informations sur le téléchargement et l’installation des applets de commande Azure AD PowerShell, consultez [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Rôles et autorisations
Voici les rôles pour lesquels vous pouvez configurer et utiliser l’expiration de groupes Office 365 dans Azure AD.

Rôle | Autorisations
-------- | --------
Administrateur général ou Administrateur de compte d’utilisateur | Peut créer, lire, mettre à jour ou supprimer les paramètres de stratégie d’expiration de groupes Office 365<br>Peut renouveler n’importe quel groupe Office 365
Utilisateur | Peut renouveler un groupe Office 365 dont il est propriétaire<br>Peut restaurer un groupe Office 365 dont il est propriétaire<br>Peut lire les paramètres de stratégie d’expiration

Pour plus d’informations sur les autorisations nécessaires pour restaurer un groupe supprimé, consultez [Restaurer un groupe Office 365 supprimé dans Azure Active Directory](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Définir l’expiration d’un groupe

1. Ouvrez le [centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte qui est un administrateur général dans votre locataire Azure AD.

2. Sélectionnez **Groupes**, puis **Expiration**, pour ouvrir les paramètres d’expiration.
  
  ![Panneau Expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Dans le panneau **Expiration**, vous pouvez :

  * Définir la durée de vie du groupe en jours. Vous pouvez sélectionner l’une des valeurs prédéfinies ou une valeur personnalisée (31 jours ou plus). 
  * Spécifier une adresse de messagerie à laquelle les notifications de renouvellement et d’expiration doivent être envoyées lorsqu’un groupe n’a pas de propriétaire. 
  * Sélectionnez les groupes Office 365 qui expirent. Vous pouvez activer l’expiration pour **tous** les groupes Office 365, vous pouvez choisir d’activer les groupes Office 365 **sélectionnés** uniquement, ou vous pouvez sélectionner **Aucun** pour désactiver l’expiration de tous les groupes.
  * Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.


Les notifications par e-mail comme celle-ci sont envoyées aux propriétaires de groupes Office 365 30 jours, 15 jours et 1 jour avant l’expiration du groupe.

![Notification par e-mail de l’expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Dans l’e-mail de notification **Renouveler le groupe**, les propriétaires de groupes peuvent directement accéder à la page d’informations du groupe dans le volet d’accès. Dans cette page, les utilisateurs peuvent obtenir plus d’informations sur le groupe, comme sa description, la date de son dernier renouvellement, sa date d’expiration, ainsi que la possibilité de son renouvellement. La page d’informations du groupe comprend également des liens vers les ressources de groupe Office 365, pour que le propriétaire du groupe puisse voir facilement le contenu et les activités dans son groupe.

Quand un groupe expire, il est supprimé un jour après la date d’expiration. Une notification par e-mail comme celle-ci est envoyée aux propriétaires de groupes Office 365 pour les informer de l’expiration et de la suppression qui en découle de leur groupe Office 365.

![Notification par e-mail de la suppression du groupe](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Le groupe peut être restauré dans les 30 jours suivant sa suppression. Pour cela, vous pouvez sélectionner **Restaurer le groupe** ou utiliser les cmdlets PowerShell, comme décrit dans [Restaurer un groupe Office 365 supprimé dans Azure Active Directory](active-directory-groups-restore-azure-portal.md).
    
Si le groupe que vous restaurez contient des documents, des sites SharePoint ou d’autres objets persistants, la restauration du groupe et de son contenu peut nécessiter jusqu’à 24 heures.

> [!NOTE]
> * Quand vous définissez l’expiration pour la première fois, les groupes qui sont plus anciens que l’intervalle d’expiration bénéficient d’un délai de 30 jours avant expiration. La première notification par e-mail est envoyée dans la journée qui suit. 
>   Par exemple, le groupe A a été créé il y a 400 jours et l’intervalle d’expiration est défini sur 180 jours. Lorsque vous appliquez les paramètres d’expiration, il reste 30 jours au groupe A avant qu’il ne soit supprimé, sauf si le propriétaire le renouvelle.
> * Une seule stratégie d’expiration peut être configurée pour les groupes Office 365 sur un même locataire.
> * Lorsqu’un groupe dynamique est supprimé et restauré, il est considéré comme un groupe nouveau, complété conformément à la règle. Ce processus peut prendre jusqu’à 24 heures.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Fonctionnement de l’expiration des groupes Office 365 avec une boîte aux lettres en conservation légale
Quand un groupe expire et est supprimé, les données du groupe issues des applications comme Planner, Sites ou Teams sont supprimées définitivement au bout de 30 jours, ce qui n’est pas le cas de la boîte aux lettres de groupe en conservation légale. L’administrateur peut utiliser les applets de commande Exchange pour restaurer la boîte aux lettres afin d’en extraire les données. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Fonctionnement de l’expiration des groupes Office 365 avec la stratégie de conservation
La stratégie de conservation est configurée dans le Centre de conformité et de sécurité. Si vous avez configuré une stratégie de conservation pour des groupes Office 365, lorsqu’un groupe expire et est supprimé, les conversations de groupe situées dans la boîte aux lettres de groupe et les fichiers situés dans le site de groupe sont conservés dans le conteneur de conservation pendant le nombre de jours défini dans la stratégie de conservation. Les utilisateurs ne verront pas le groupe et son contenu après son expiration. Toutefois, ils peuvent récupérer les données de site et de boîte aux lettres via découverte électronique.

## <a name="powershell-examples"></a>Exemples PowerShell
Voici des exemples d’utilisation des applets de commande PowerShell permettant de configurer les paramètres d’expiration des groupes Office 365 de votre locataire :

1. Installez le module de la préversion de PowerShell v2.0 (2.0.0.137) et connectez-vous à l’invite de PowerShell :
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. Configurez les paramètres d’expiration New-AzureADMSGroupLifecyclePolicy : cette applet de commande définit la durée de vie de tous les groupes Office 365 du locataire sur 365 jours. Les notifications de renouvellement pour les groupes Office 365 sans propriétaires sont envoyées à « emailaddress@contoso.com ».
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. Récupérez la stratégie existante Get-AzureADMSGroupLifecyclePolicy : cette applet de commande récupère les paramètres d’expiration des groupes Office 365 qui ont été configurés. Dans cet exemple, vous pouvez voir ce qui suit :
  * L’ID de la stratégie 
  * La durée de vie de tous les groupes Office 365 du locataire est définie sur 365 jours
  * Les notifications de renouvellement pour les groupes Office 365 sans propriétaires sont envoyées à « emailaddress@contoso.com ».
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. Mettez à jour la stratégie existante Set-AzureADMSGroupLifecyclePolicy : cette applet de commande est utilisée pour mettre à jour une stratégie existante. Dans l’exemple ci-dessous, la durée de vie du groupe dans la stratégie existante est passée de 365 jours à 180 jours. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Ajoutez des groupes à la stratégie Add-AzureADMSLifecyclePolicyGroup : cette applet de commande ajoute un groupe à la stratégie de cycle de vie. Par exemple : 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Supprimez la stratégie existante AzureADMSGroupLifecyclePolicy : cette applet de commande supprime les paramètres d’expiration des groupes Office 365, mais nécessite l’ID de la stratégie. Cela va désactiver l’expiration des groupes Office 365. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
Les applets de commande suivantes peuvent être utilisées pour configurer la stratégie plus en détail. Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes Azure AD.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
