---
title: "Étapes suivantes de la gestion des accès à l’aide de groupes | Microsoft Docs"
description: "Procédures avancées concernant la gestion des groupes de sécurité et l’utilisation de ces groupes pour gérer l’accès à une ressource."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: 177e31b8b8aff90cd91135d02eb3a2d8c85e2c33
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="managing-owners-for-a-group"></a>Gestion des propriétaires d’un groupe
Une fois que le propriétaire d'une ressource a attribué l'accès à une ressource à un groupe Azure AD, l'appartenance au groupe est gérée par le propriétaire du groupe. Le propriétaire de la ressource délègue effectivement au propriétaire du groupe l’autorisation d’affecter des utilisateurs aux ressources.

## <a name="add-an-owner-to-a-group"></a>Ajouter un propriétaire à un groupe

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes**.
2. Sélectionnez **Tous les groupes**, puis ouvrez le groupe auquel vous voulez ajouter des propriétaires.
3. Sélectionnez **Ajouter des propriétaires**.
4. Sur la page **Ajouter des propriétaires**, sélectionnez l’utilisateur à ajouter en tant que propriétaire de ce groupe, puis vérifiez que son nom a été ajouté au volet **Sélectionné**.

## <a name="remove-an-owner-from-a-group"></a>Supprimer un propriétaire d’un groupe

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes**.
2. Sélectionnez **Tous les groupes**, puis ouvrez le groupe duquel vous voulez supprimer des propriétaires.
3. Sélectionnez l’onglet **Propriétaires** .
4. Sélectionnez le propriétaire que vous souhaitez supprimer de ce groupe, puis choisissez **Supprimer**.

## <a name="additional-information"></a>Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
