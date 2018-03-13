---
title: "Guide d’attribution de licences pour Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration ne nécessite pas de licences Azure AD payées, mais vous pouvez également obtenir des fonctionnalités payantes pour les utilisateurs invités B2B."
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
ms.date: 08/09/2017
ms.author: twooley
ms.reviewer: sasubram
ms.custom: it-pro
ms.openlocfilehash: d4a739e5cb4dc4a6ccafac683fe913592f985ce2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guide d’attribution de licences pour Azure Active Directory B2B Collaboration

Vous pouvez utiliser les fonctionnalités de collaboration B2B Azure AD pour inviter des utilisateurs invités dans votre client Azure AD et les aider à accéder aux services Azure AD et autres ressources de votre entreprise. Si vous souhaitez fournir l’accès à des fonctionnalités Azure AD payantes, vous devez accorder aux utilisateurs invités de la collaboration B2B des licences Azure AD appropriées. 

Plus précisément :
* Les fonctionnalités Azure AD Free sont disponibles pour les utilisateurs invités sans licence supplémentaire.
* Si vous souhaitez fournir l’accès aux fonctionnalités Azure AD payantes aux utilisateurs B2B, vous devez disposer de suffisamment de licences pour prendre en charge les utilisateurs invités B2B.
* Un client invitant avec une licence Azure AD payante dispose de droits d’utilisation de la collaboration B2B pour cinq utilisateurs invités B2B supplémentaires, invités eux-mêmes par le client.
* C’est le client propriétaire du locataire invitant qui doit déterminer combien d’utilisateurs B2B Collaboration ont besoin de fonctionnalités Azure AD payantes. En fonction des fonctionnalités Azure AD payantes que vous souhaitez mettre à disposition de vos utilisateurs invités, vous devez avoir suffisamment de licences Azure AD payantes pour couvrir les utilisateurs de la collaboration B2B avec le même rapport 5:1.

Un utilisateur invité de la collaboration B2B est ajouté en tant qu’utilisateur d’une société partenaire, et pas un employé de votre organisation ou un employé d’une autre entreprise dans votre conglomérat. Un utilisateur invité B2B peut se connecter avec des informations d’identification externes ou détenues par votre organisation, comme décrit dans cet article. 

En d’autres termes, les licences B2B ne sont pas définies en fonction du mode d’authentification utilisé par l’utilisateur mais en fonction de la relation de l’utilisateur à votre organisation. Si ces utilisateurs ne sont pas des partenaires, ils sont traités différemment selon les conditions de licence. Ils ne sont pas considérés comme utilisateur de la collaboration B2B aux fins de licences, même si leur UserType est marqué comme « Invité ». Ils doivent disposer normalement d’une licence et une licence par utilisateur. Ces utilisateurs sont les suivants :
* vos employés
* le personnel connecté à l’aide d’identités externes
* un employé d’une autre entreprise dans votre conglomérat


## <a name="licensing-examples"></a>Exemples d’attribution de licences
- Un client souhaite inviter 100 utilisateurs B2B Collaboration à son locataire Azure AD. Le client affecte la gestion de l’accès et l’approvisionnement pour tous les utilisateurs, mais 50 utilisateurs nécessitent également l’authentification multifacteur et l’accès conditionnel. Ce client doit acheter 10 licences De base Azure AD et 10 licences Premium P1 Azure AD pour répondre correctement aux besoins de ces utilisateurs B2B. Si le client envisage d’utiliser les fonctionnalités de protection de l’identité pour les utilisateurs B2B, il doit avoir des licences Premium P2 Azure AD pour couvrir les utilisateurs invités en respectant le même ratio de 5 à 1.
- Un client a 10 employés qui disposent tous d’une licence Premium P1 Azure AD. Il veut désormais inviter 60 utilisateurs B2B, qui nécessitent tous l’authentification multifacteur. Selon la règle d’attribution de licences de 5 à 1, le client doit disposer d’au moins 12 licences Premium P1 Azure AD pour couvrir les 60 utilisateurs B2B Collaboration. Puisqu’il dispose déjà de 10 licences Premium P1 pour ses 10 employés, il peut inviter 50 utilisateurs B2B avec des fonctionnalités de niveau Premium P1 telles que l’authentification multifacteur. Dans cet exemple, il doit acheter deux licences Premium P1 supplémentaires afin de couvrir les 10 autres utilisateurs B2B Collaboration.

> [!NOTE]
> Il n’existe encore aucun moyen d’attribuer directement des licences aux utilisateurs B2B pour activer ces droits d’utilisateur B2B Collaboration.

C’est le client propriétaire du locataire invitant qui doit déterminer combien d’utilisateurs B2B Collaboration ont besoin de fonctionnalités Azure AD payantes. En fonction des fonctionnalités Azure AD payantes que vous souhaitez mettre à disposition de vos utilisateurs invités, vous devez avoir suffisamment de licences Azure AD payées pour couvrir les utilisateurs B2B Collaboration avec le ratio de 5 à 1. 

## <a name="additional-licensing-details"></a>Informations supplémentaires sur l’attribution de licences
- Il est inutile d’attribuer des licences aux comptes d’utilisateur B2B. D’après le ratio de 5 à 1, l’accord des licences est calculé et signalé automatiquement.
- S’il n’existe aucune licence Azure AD payée dans le locataire, chaque utilisateur invité obtient les droits de l’édition Azure AD Free.
- Si un utilisateur B2B Collaboration dispose déjà d’une licence Azure AD payée délivrée par son organisation, il n’utilise pas l’une des licences B2B Collaboration du locataire à l’origine de l’invitation.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Discussion approfondie : quelles sont les considérations relatives aux licences lorsque nous ajoutons des utilisateurs à partir d’un conglomérat en tant que « membres » à l’aide de vos API ?
Un utilisateur invité B2B est invité à partir d’une organisation partenaire pour travailler avec l’organisation hôte. En règle générale, aucun autre cas n’est considéré comme B2B, même s’il utilise des fonctionnalités B2B. Nous allons examiner deux cas en particulier :

1. Si un hôte invite un employé à l’aide d’une adresse client
  * Ce scénario n’est pas conforme à nos stratégies de gestion des licences et n’est actuellement pas recommandé.

2. Si une organisation hôte ajoute un utilisateur à partir d’un autre conglomérat
  1. Dans ce cas, l’utilisateur est invité à l’aide des API B2B, mais il ne s’agit généralement pas de B2B. Dans l’idéal, ces organisations doivent inviter les utilisateurs des autres organisations en tant que membres (notre API le permet). Dans ce cas, les licences doivent être assignées à ces membres pour leur permettre d’accéder aux ressources de l’organisation à l’origine de l’invitation.

  2. Certaines organisations utilisent une autre stratégie et ajoutent les utilisateurs de l’autre organisation comme « Invité ». Il existe deux cas :
      * Le conglomérat utilise déjà Azure AD et les utilisateurs invités sont concédés sous une licence de l’autre organisation : dans ce cas, les utilisateurs invités n’ont pas besoin de suivre la formule 1 à 5 présentée précédemment dans ce document. 

      * Le conglomérat n’utilise pas Azure AD ou ne dispose pas des licences adéquates : dans ce cas, suivez la formule 1 à 5 présentée précédemment dans ce document.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
