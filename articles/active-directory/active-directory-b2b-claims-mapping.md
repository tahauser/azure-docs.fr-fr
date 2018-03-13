---
title: "Mappage des revendications d’utilisateur B2B Collaboration dans Azure Active Directory | Microsoft Docs"
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
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 3fe75e96c153a7cbcad638d606d0fbc248bd983a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mappage des revendications d’utilisateur B2B Collaboration dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge la personnalisation des revendications émises dans le jeton SAML pour les utilisateurs B2B Collaboration. Lorsqu’un utilisateur s’authentifie auprès de l’application, Azure AD émet un jeton SAML à l’application qui contient des informations (ou des revendications) sur l’utilisateur qui l’identifient de façon unique. Par défaut, ceci inclut le nom d’utilisateur, l’adresse e-mail, le prénom et nom de l’utilisateur. Vous pouvez afficher ou modifier les revendications envoyées à l’application dans le jeton SAML sous l’onglet Attributs.

Il existe deux raisons pour lesquelles vous devrez peut-être modifier les revendications émises dans le jeton SAML.

1. L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication

2. Votre application nécessite que la revendication NameIdentifier soit différente du nom d’utilisateur principal stocké dans Azure Active Directory.

  ![Afficher les revendications dans le jeton SAML](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Pour plus d’informations sur la façon d’ajouter et de modifier les revendications, consultez cet article sur la personnalisation des revendications, [Personnalisation des revendications émises dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory](develop/active-directory-saml-claims-customization.md). Pour les utilisateurs B2B Collaboration, le mappage entre locataires de NameID et UPN est empêché pour des raisons de sécurité.


## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)
