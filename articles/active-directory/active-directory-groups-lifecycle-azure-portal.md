---
title: Expiration des groupes Office 365 dans Azure Active Directory | Microsoft Docs
description: "Comment configurer l’expiration des groupes Office 365 dans Azure Active Directory (préversion)"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: f9d79746dcf307cf434ee78d9b1514f5886d9fb6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Configurer l’expiration des groupes Office 365 (préversion)

Vous pouvez désormais gérer le cycle de vie des groupes Office 365 en définissant des fonctionnalités d’expiration pour ces derniers. Vous pouvez définir l’expiration uniquement pour les groupes Office 365 dans Azure Active Directory (Azure AD). Une fois que vous avez défini l’expiration d’un groupe :
-   Les propriétaires du groupe sont invités à renouveler le groupe à l’approche de l’expiration
-   Les groupes non renouvelés sont supprimés
-   Les groupes Office 365 qui sont supprimés peuvent être restaurés dans les 30 jours qui suivent par les propriétaires des groupes ou l’administrateur

> [!NOTE]
> Définir l’expiration des groupes Office 365 nécessite une licence Azure AD Premium pour tous les membres des groupes auxquels les paramètres d’expiration sont appliqués.

Pour plus d’informations sur le téléchargement et l’installation des applets de commande Azure AD PowerShell, consultez [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Rôles et autorisations
Voici les rôles que vous pouvez configurer et utiliser à l’expiration de groupes Office 365 dans Azure AD.

Rôle | Autorisations
-------- | --------
Administrateur général<br>Administrateur de compte utilisateur | Peut créer, lire, mettre à jour ou supprimer les paramètres de stratégie d’expiration de groupes Office 365
Utilisateur | Peut renouveler un groupe Office 365 dont il est propriétaire<br>Peut restaurer un groupe Office 365 dont il est propriétaire

Pour plus d’informations sur les autorisations nécessaires pour restaurer un groupe supprimé, consultez [Restaurer un groupe Office 365 supprimé](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Définir l’expiration d’un groupe

1. Ouvrez le [centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte qui est un administrateur général dans votre locataire Azure AD.

2. Sélectionnez **Utilisateurs et groupes**.

3. Sélectionnez **Paramètres de groupe**, puis **Expiration** pour ouvrir les paramètres d’expiration.
  
  ![Panneau Expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Dans le panneau **Expiration**, vous pouvez :

  * Définir la durée de vie du groupe en jours. Vous pouvez sélectionner l’une des valeurs prédéfinies ou une valeur personnalisée (31 jours ou plus). 
  * Spécifier une adresse de messagerie à laquelle les notifications de renouvellement et d’expiration doivent être envoyées lorsqu’un groupe n’a pas de propriétaire. 
  * Sélectionnez les groupes Office 365 qui expirent. Vous pouvez activer l’expiration pour **tous** les groupes Office 365, vous pouvez choisir d’activer les groupes Office 365 **sélectionnés** uniquement, ou vous pouvez sélectionner **Aucun** pour désactiver l’expiration de tous les groupes.
  * Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.


Les notifications par e-mail comme celle-ci sont envoyées aux propriétaires de groupes Office 365 30 jours, 15 jours et 1 jour avant l’expiration du groupe.

![Notification par e-mail de l’expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Le propriétaire du groupe peut ensuite sélectionner **Renew group** (Renouveler le groupe) pour renouveler le groupe Office 365, ou vous pouvez sélectionner **Accéder au groupe** pour voir les membres et d’autres détails sur le groupe.

Quand un groupe expire, il est supprimé un jour après la date d’expiration. Une notification par e-mail comme celle-ci est envoyée aux propriétaires de groupes Office 365 pour les informer de l’expiration et de la suppression qui en découle de leur groupe Office 365.

![Notification par e-mail de la suppression du groupe](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Le groupe peut être restauré en sélectionnant **Restore group** (Restaurer le groupe) ou à l’aide des cmdlets PowerShell, comme décrit dans [Restaurer un groupe Office 365 supprimé dans Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Si le groupe que vous restaurez contient des documents, des sites SharePoint ou d’autres objets persistants, la restauration du groupe et de son contenu peut nécessiter jusqu’à 24 heures.

> [!NOTE]
> * Quand vous définissez l’expiration pour la première fois, les groupes qui sont plus anciens que l’intervalle d’expiration bénéficient d’un délai de 30 jours avant expiration. La première notification par e-mail est envoyée dans la journée qui suit. 
>   Par exemple, le groupe A a été créé il y a 400 jours et l’intervalle d’expiration est défini sur 180 jours. Lorsque vous appliquez les paramètres d’expiration, il reste 30 jours au groupe A avant qu’il ne soit supprimé, sauf si le propriétaire le renouvelle.
> * Lorsqu’un groupe dynamique est supprimé et restauré, il est considéré comme un groupe nouveau, complété conformément à la règle. Ce processus peut prendre jusqu’à 24 heures.

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes Azure AD.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
