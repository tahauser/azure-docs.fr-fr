---
title: "Résolution des problèmes d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Solutions pour les problèmes courants liés à Azure Active Directory B2B Collaboration"
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
ms.date: 05/25/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 588e154d35fda539ac6ee8803ed96e6cd9a3d1df
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Résolution des problèmes d’Azure Active Directory B2B Collaboration

Voici des solutions pour les problèmes courants liés à Azure Active Directory (Azure AD) B2B Collaboration.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>J’ai ajouté un utilisateur externe, mais je ne le vois pas dans mon carnet d’adresses global ou dans le sélecteur de personnes

Dans les cas où les utilisateurs externes ne sont pas renseignés dans la liste, la réplication de l’objet peut prendre quelques minutes.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Un utilisateur invité B2B ne s’affiche pas dans le sélecteur de personnes SharePoint Online/OneDrive 
 
La fonctionnalité de recherche d’utilisateurs invités existants dans le sélecteur de personnes SharePoint Online (SPO) est désactivée par défaut pour correspondre au comportement hérité.

Vous pouvez activer cette fonctionnalité à l’aide du paramètre ShowPeoplePickerSuggestionsForGuestUsers au niveau du client et de la collection du site. Vous pouvez définir cette fonctionnalité à l’aide des applets de commande Set-SPOTenant et Set-SPOSite qui permettent aux membres de rechercher tous les utilisateurs invités existants dans le répertoire. Les modifications apportées à la portée du client n’affectent pas les sites SPO déjà configurés.

## <a name="invitations-have-been-disabled-for-directory"></a>Des invitations ont été désactivées pour le répertoire

Si un message vous indique que vous n’êtes pas autorisé à inviter des utilisateurs, vérifiez que votre compte d’utilisateur est autorisé à inviter des utilisateurs externes sous Paramètres utilisateur :

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Si vous avez récemment modifié ces paramètres ou affecté le rôle d’inviteur d’invités à un utilisateur, vous devrez peut-être attendre 15 à 60 minutes avant que les modifications ne prennent effet.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>L’utilisateur que j’ai invité reçoit une erreur au cours de l’utilisation de l'invitation

Les erreurs courantes sont les suivantes :

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>L’administrateur de l’invité n’autorise pas la création d’utilisateurs EmailVerified dans leur client

Si vous invitez des utilisateurs dont l’organisation utilise un Azure Active Directory dans lequel le compte d’utilisateur spécifique n’existe pas (par exemple, l’utilisateur n’existe pas dans Azure AD contoso.com). L’administrateur de contoso.com peut avoir mis en place une stratégie empêchant la création d'utilisateurs. L’utilisateur doit contacter son administrateur pour déterminer si les utilisateurs externes sont autorisés. L’administrateur de l’utilisateur externe devra peut-être autoriser les utilisateurs vérifiés par e-mail dans son domaine (consultez cet [article](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) sur l’autorisation d’utilisateurs vérifiés par e-mail).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>L'utilisateur externe n’existe pas déjà dans un domaine fédéré

Si vous utilisez l’authentification par fédération et si l’utilisateur n’existe pas déjà dans Azure Active Directory, l’utilisateur ne peut pas être invité.

Pour résoudre ce problème, administrateur de l’utilisateur externe doit synchroniser le compte d’utilisateur sur Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Comment synchroniser « \# », qui n’est normalement pas un caractère valide, avec Azure AD ?

« \# » est un caractère réservé dans les UPN pour les utilisateurs Azure AD B2B Collaboration ou externes, car le compte invité user@contoso.com devient user_contoso.com#EXT@fabrikam.onmicrosoft.com. Par conséquent, il est impossible pour \# dans les UPN en local de se connecter au portail Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Je reçois une erreur lors de l’ajout d'utilisateurs externes à un groupe synchronisé

Des utilisateurs externes peuvent être ajoutés uniquement à des groupes « affectés » ou « de sécurité » et non à des groupes contrôlés localement.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mon utilisateur externe n’a pas reçu d'e-mail à utiliser

L’invité doit contacter son fournisseur de services Internet ou contrôler son filtre de courriers indésirables pour s’assurer que l’adresse suivante est autorisée : Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Je remarque que le message personnalisé n’est parfois pas inclus dans les messages d’invitation

Par respect des lois sur la confidentialité, nos API n’incluent pas de messages personnalisés dans les invitations par e-mail lorsque :

- L’inviteur n’a pas d’adresse e-mail dans le locataire à l’origine de l’invitation
- Un principal AppService envoie l’invitation

Si ce scénario est important pour vous, vous pouvez supprimer l’API qui envoie l’invitation par e-mail, et envoyer cette dernière au moyen d’un mécanisme de messagerie de votre choix. Consultez un conseiller juridique dans votre organisation pour vous assurer que tout e-mail que vous envoyez est également conforme aux lois relatives à la vie privée.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
