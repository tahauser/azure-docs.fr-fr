---
title: Expiration des groupes Office 365 dans Azure Active Directory | Microsoft Docs
description: "Comment configurer l’expiration des groupes Office 365 dans Azure Active Directory (préversion)"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: c2dd56bd34e5b7845298fab1f36e231113a2e28e
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Configurer l’expiration des groupes Office 365 (préversion)

Vous pouvez désormais gérer le cycle de vie des groupes Office 365 en définissant des fonctionnalités d’expiration pour ces derniers. Vous pouvez définir l’expiration uniquement pour les groupes Office 365 dans Azure Active Directory (Azure AD). Une fois que vous avez défini l’expiration d’un groupe :
-   Les propriétaires du groupe sont invités à renouveler le groupe à l’approche de l’expiration
-   Les groupes non renouvelés sont supprimés
-   Les groupes Office 365 qui sont supprimés peuvent être restaurés dans les 30 jours qui suivent par les propriétaires des groupes ou l’administrateur

> [!NOTE]
> Pour définir l’expiration des groupes Office 365, une licence Azure AD Premium ou une licence Azure AD Basic EDU doit être attribuée à tous les membres des groupes auxquels les paramètres d’expiration sont appliqués.
> 
> Si vous disposez d’une licence Azure AD Basic EDU et que vous configurez cette stratégie pour la première fois, utilisez les applets de commande Azure Active Directory PowerShell. Ensuite, vous pouvez mettre à jour les paramètres d’expiration à l’aide de PowerShell ou du portail Azure AD, avec un compte qui est administrateur des comptes d’utilisateur ou administrateur général dans votre locataire Azure AD.

Pour plus d’informations sur le téléchargement et l’installation des applets de commande Azure AD PowerShell, consultez [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="set-group-expiration"></a>Définir l’expiration d’un groupe

1. Ouvrez le [centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte qui est un administrateur général dans votre locataire Azure AD.

2. Ouvrez Azure AD et sélectionnez **Utilisateurs et groupes**.

3. Sélectionnez **Paramètres de groupe**, puis **Expiration** pour ouvrir les paramètres d’expiration.
  
  ![Panneau Expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Dans le panneau **Expiration**, vous pouvez :

  * Définir la durée de vie du groupe en jours. Vous pouvez sélectionner l’une des valeurs prédéfinies ou une valeur personnalisée (31 jours ou plus). 
  * Spécifier une adresse de messagerie à laquelle les notifications de renouvellement et d’expiration doivent être envoyées lorsqu’un groupe n’a pas de propriétaire. 
  * Sélectionnez les groupes Office 365 qui expirent. Vous pouvez activer l’expiration pour **tous** les groupes Office 365, vous pouvez sélectionner certains groupes Office 365, ou vous pouvez sélectionner **Aucun** pour désactiver l’expiration de tous les groupes.
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
