---
title: Partage externe dans Office 365 et Azure Active Directory B2B Collaboration | Microsoft Docs
description: "référence de mappage des revendications pour Azure Active Directory B2B Collaboration"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/24/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 8486c7518e2cabb1d17a8f38442b7b5a77fddb01
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Partage externe dans Office 365 et Azure Active Directory B2B Collaboration

Le partage externe dans Office 365 (OneDrive, SharePoint Online, groupes unifiés, etc.) et Azure Active Directory (Azure AD) B2B Collaboration revient techniquement au même. Tous les partages externes (à l’exception de OneDrive/SharePoint Online), notamment les invités dans les groupes Office 365, utilisent déjà les API d’invitation Azure AD B2B Collaboration pour le partage.

OneDrive/SharePoint Online possède un gestionnaire d’invitation distinct. La prise en charge par OneDrive/SharePoint Online du partage externe a démarré avant qu’Azure AD ne propose sa prise en charge. Au fil du temps, plusieurs fonctionnalités ont été ajoutées au partage externe OneDrive/SharePoint Online, ce qui a attiré de des millions d’utilisateurs exploitant le modèle de partage intégré. Cependant, il existe quelques différences subtiles entre le fonctionnement du partage externe OneDrive/SharePoint Online et le fonctionnement d’Azure AD B2B Collaboration :

- OneDrive/SharePoint Online ajoute les utilisateurs au répertoire une fois qu’ils ont utilisé leurs invitations. Par conséquent, avant qu’il utilise son invitation, l’utilisateur n’apparaît pas dans le portail Azure AD. Si un autre site invite un utilisateur dans l’intervalle, une nouvelle invitation est générée. En revanche, lorsque vous utilisez Azure AD B2B Collaboration, les utilisateurs sont ajoutés immédiatement sur invitation afin qu’ils apparaissent partout.

- L’expérience d’utilisation de l’invitation dans OneDrive/SharePoint Online est différente de celle d’Azure AD B2B Collaboration. Une fois l’invitation utilisée, les expériences sont similaires.

- Les utilisateurs invités par Azure AD B2B Collaboration peuvent être sélectionnés à partir des boîtes de dialogue de partage de OneDrive/SharePoint Online. Les utilisateurs invités par OneDrive/SharePoint Online apparaissent également dans Azure AD une fois qu’ils ont utilisé leur invitation.

- Pour gérer le partage externe dans OneDrive/SharePoint Online avec Azure AD B2B Collaboration, définissez le paramètre OneDrive/SharePoint Online de partage externe sur **Autoriser le partage uniquement avec des utilisateurs externes figurant déjà dans le répertoire**. Les utilisateurs peuvent accéder à des sites partagés de manière externe et choisir parmi les collaborateurs externes ajoutés par l’administrateur. L’administrateur peut ajouter les collaborateurs externes par le biais des API d’invitation B2B Collaboration.

![Paramètre OneDrive/SharePoint Online de partage externe](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)
