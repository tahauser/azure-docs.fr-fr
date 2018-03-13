---
title: FAQ sur Azure Active Directory B2B Collaboration | Microsoft Docs
description: "Trouvez les réponses aux questions les plus fréquentes sur Azure Active Directory B2B Collaboration."
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
ms.date: 01/08/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 21511b37eba2cd2e87d8720f3e9cfe453b50d88c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>FAQ sur Azure Active Directory B2B Collaboration

Cette foire aux questions (FAQ) sur la collaboration interentreprises (B2B) Azure Active Directory (Azure AD) est régulièrement complétée par de nouvelles rubriques.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Peut-on personnaliser sa page de connexion afin qu’elle soit plus intuitive pour les utilisateurs invités B2B Collaboration ?
Absolument ! Consultez notre [billet de blog sur cette fonctionnalité](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Pour plus d’informations sur la personnalisation de la page de connexion d’une organisation, consultez la page [Ajouter la personnalisation de l’entreprise aux pages de connexion et du volet d’accès](customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Les utilisateurs de B2B Collaboration peuvent-ils accéder à SharePoint Online et OneDrive ?
Oui. La fonctionnalité de recherche d’utilisateurs invités existants avec le sélecteur de personnes dans SharePoint Online est cependant **désactivée** par défaut. Pour activer l’option de recherche d’utilisateurs invités, définissez **ShowPeoplePickerSuggestionsForGuestUsers** sur **Activé**. Vous pouvez activer ce paramètre au niveau du client ou bien de la collection de sites. Vous pouvez modifier ce paramètre à l’aide des applets de commande Set-SPOTenant et Set-SPOSite. Grâce à ces applets de commande, les membres peuvent rechercher parmi tous les utilisateurs invités existants du répertoire. Les modifications apportées à l’étendue du client n’affectent pas les sites SharePoint Online déjà configurés.

### <a name="is-the-csv-upload-feature-still-supported"></a>La fonctionnalité de chargement CSV est-elle toujours prise en charge ?
Oui. Pour plus d’informations sur la fonctionnalité de chargement de fichiers .csv, consultez [cet exemple PowerShell](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Comment puis-je personnaliser mes e-mails d’invitation ?
Vous pouvez personnaliser pratiquement tous les éléments du processus de l’inviteur à l’aide des [API d’invitation B2B](active-directory-b2b-api.md).

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>Un utilisateur externe peut-il quitter une organisation après y avoir été invité ?
L’administrateur de l’organisation à l’origine de l’invitation peut supprimer un utilisateur invité B2B Collaboration de son répertoire, mais l’utilisateur invité ne peut pas quitter le répertoire de l’organisation à l’origine de l’invitation par lui-même. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Les utilisateurs invités peuvent-ils réinitialiser leur méthode d’authentification multifacteur ?
Oui. Les utilisateurs invités peuvent réinitialiser leur méthode d’authentification multifacteur de la même manière que les utilisateurs normaux.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Quelle est l’organisation responsable des licences de l’authentification multifacteur ?
L’organisation à l’origine de l’invitation effectue l’authentification multifacteur. Elle doit s’assurer que l’organisation dispose d’un nombre suffisant de licences pour ses utilisateurs B2B qui utilisent l’authentification multifacteur.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Que se passe-t-il si une organisation partenaire a déjà configuré l’authentification multifacteur ? Pouvons-nous faire confiance à son authentification multifacteur et ne pas utiliser notre propre authentification multifacteur ?
Cette fonctionnalité est planifiée pour une version ultérieure : vous pourrez alors sélectionner des partenaires à exclure de votre authentification multifacteur (celle de l’organisation à l’origine de l’invitation).

### <a name="how-can-i-use-delayed-invitations"></a>Comment utiliser les invitations différées ?
Une organisation peut souhaiter ajouter des utilisateurs B2B Collaboration et les approvisionner pour les applications au fil des besoins, avant d'envoyer des invitations. Vous pouvez utiliser l’API d’invitation B2B Collaboration pour personnaliser le workflow d’intégration.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Peut-on faire d’un utilisateur invité un administrateur limité ?
Absolument. Pour plus d’informations, consultez [Ajouter des utilisateurs invités à un rôle](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B Collaboration permet-il aux utilisateurs B2B d’accéder au Portail Azure ?
À moins qu’un rôle Administrateur limité ou Administrateur général ne leur soit assigné, les utilisateurs B2B Collaboration n’ont pas besoin d’accéder au portail Azure. Toutefois, les utilisateurs B2B Collaboration qui ont le rôle Administrateur limité ou Administrateur général peuvent accéder au portail. En outre, si un utilisateur invité qui ne possède aucun de ces rôles Administrateur accède au portail, il peut être en mesure d’accéder à certaines parties de l’expérience. Le rôle utilisateur invité possède quelques autorisations dans le répertoire.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Puis-je bloquer l’accès au portail Azure pour les utilisateurs invités ?
Oui. Lorsque vous configurez cette stratégie, faites attention à ne pas bloquer accidentellement l’accès aux membres et aux administrateurs.
Pour bloquer l’accès d’un utilisateur invité au [portail Azure](https://portal.azure.com), utilisez une stratégie d’accès conditionnel dans l’API Modèle de déploiement Azure Classic :
1. Modifiez le groupe **Tous les utilisateurs** pour qu’il contienne uniquement les membres.
  ![capture d’écran - modifier le groupe](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Créez un groupe dynamique contenant des utilisateurs invités.
  ![capture d’écran - créer un groupe](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configurez une stratégie d’accès conditionnel pour empêcher les utilisateurs invités d’accéder au portail, comme le montre la vidéo suivante :
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B Collaboration prend-il en charge l’authentification multifacteur et les comptes de messagerie grand public ?
Oui. Les comptes de messagerie grand public et l’authentification multifacteur sont tous deux pris en charge par Azure AD B2B Collaboration.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Prévoyez-vous de prendre en charge la réinitialisation de mot de passe pour les utilisateurs Azure AD B2B Collaboration ?
Oui. Voici les informations importantes concernant la réinitialisation de mot de passe en libre-service (SSPR) pour un utilisateur B2B invité à partir d’une organisation partenaire :
 
* La réinitialisation de mot de passe en libre-service ne s’effectue que dans le locataire d’identité de l’utilisateur B2B.
* Si le locataire d’identité est un compte Microsoft, le mécanisme de réinitialisation de mot de passe en libre-service du compte Microsoft est utilisé.
* Si le locataire d’identité est un locataire « viral » ou juste-à-temps (JIT), un e-mail de réinitialisation de mot de passe est envoyé.
* Concernant les autres locataires, le processus de réinitialisation standard est suivi pour les utilisateurs B2B. Comme pour la réinitialisation de mot de passe libre-service des utilisateurs B2B membres, la location est bloquée dans le contexte de la ressource. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>La réinitialisation du mot de passe est-elle disponible pour les utilisateurs invités dans un locataire « viral » ou juste-à-temps (JIT) et qui ont accepté des invitations par le biais d’une adresse e-mail scolaire ou professionnelle, mais qui n’avaient pas de compte Azure AD préexistant ?
Oui. Un e-mail de réinitialisation de mot de passe peut être envoyé pour permettre à un utilisateur de réinitialiser son mot de passe dans la location JIT.

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics CRM fournit-il un support en ligne pour Azure AD B2B Collaboration ?
Actuellement, Microsoft Dynamics CRM ne fournit pas de support en ligne pour Azure AD B2B Collaboration. Toutefois, nous prévoyons de le prendre en charge à l’avenir.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Quelle est la durée de vie d’un mot de passe initial pour un utilisateur B2B Collaboration nouvellement créé ?
Azure AD dispose d’un ensemble fixe d'exigences en matière de nombre de caractères, de force du mot de passe et de verrouillage de compte qui s’appliquent à tous les comptes d’utilisateur cloud Azure AD. Les comptes d’utilisateur cloud sont des comptes qui ne sont pas fédérés avec un autre fournisseur d’identité, comme 
* Compte Microsoft
* Facebook
* Services de fédération Active Directory (AD FS)
* Un autre locataire cloud (pour B2B Collaboration)

Pour les comptes fédérés, la stratégie de mot de passe dépend de la stratégie qui s’applique dans la location locale et des paramètres de compte Microsoft de l’utilisateur.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Une organisation peut avoir différentes expériences dans ses applications pour les utilisateurs clients et les utilisateurs invités. Une assistance standard est-elle disponible pour cela ? La présence de la revendication de fournisseur d’identité représente-t-elle le bon modèle ?
 Un utilisateur invité peut utiliser n’importe quel fournisseur d’identité pour s’authentifier. Pour plus d’informations, consultez la page [Propriétés d’un utilisateur B2B Collaboration](active-directory-b2b-user-properties.md). Utilisez la propriété **UserType** pour déterminer l’expérience utilisateur. La revendication **UserType** n’est pour le moment pas incluse dans le jeton. Les applications doivent utiliser l’API Graph pour interroger le répertoire de l’utilisateur et obtenir le UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Où puis-je trouver une communauté B2B Collaboration pour partager des solutions et envoyer des idées ?
Nous sommes constamment à l’écoute de vos commentaires afin d’améliorer B2B Collaboration. Nous vous invitons à partager vos scénarios utilisateur, vos meilleures pratiques et ce que vous appréciez par rapport à Azure AD B2B Collaboration. Participez à la discussion dans la [Communauté Microsoft Tech](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Nous vous invitons également à soumettre vos idées et à voter pour les prochaines fonctionnalités sur la page [Idées B2B Collaboration](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Peut-on envoyer une invitation utilisée automatiquement, afin que l’utilisateur soit simplement « prêt » ? L’utilisateur doit-il toujours cliquer sur l’URL d’utilisation ?
Les invitations envoyées par un utilisateur de l’organisation à l’origine de l’invitation, qui est également un membre de l’organisation partenaire, ne nécessitent pas d’utilisation par l’utilisateur B2B.

Nous vous recommandons d’inviter un utilisateur de l’organisation partenaire à rejoindre l’organisation à l’origine de l’invitation. [Ajoutez cet utilisateur au rôle Inviteur d’invités dans l’organisation de ressources](active-directory-b2b-add-guest-to-role.md). Cet utilisateur peut inviter d’autres utilisateurs dans l’organisation partenaire à l’aide de l’interface utilisateur de connexion, de scripts PowerShell ou d’API. Les utilisateurs B2B Collaboration de cette organisation ne sont alors pas obligés d’échanger leurs invitations.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Comment fonctionne la collaboration B2B lorsque le partenaire invité utilise la fédération pour ajouter sa propre authentification locale ?
Si le partenaire a un client Azure AD qui est fédéré à l’infrastructure d’authentification locale, l’authentification unique (SSO) locale s’effectue automatiquement. Si le partenaire n’a pas de client Azure AD, un compte Azure AD est créé pour les nouveaux utilisateurs. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Je pensais qu’Azure AD B2B n’acceptait pas les adresses de messagerie gmail.com et outlook.com, et que B2C était utilisé pour ces types de comptes ?
Nous supprimons les différences entre B2B et B2C (des entreprises aux particuliers) Collaboration en ce qui concerne la prise en charge des identités. L’identité utilisée n’est pas un bon critère de choix entre B2B et B2C. Pour plus d’informations sur le choix de l’option de collaboration, consultez la page [Comparer B2C et B2B Collaboration dans Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quelles applications et services prennent en charge les utilisateurs invités Azure B2B ?
Toutes les applications intégrées à Azure AD prennent en charge les utilisateurs invités Azure B2B. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Pouvons-nous forcer l’authentification multifacteur pour les utilisateurs invités B2B si nos partenaires n’ont pas l’authentification multifacteur ?
Oui. Pour plus d’informations, consultez la page [Accès conditionnel pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>Dans SharePoint, il est possible de définir une liste « allow (autoriser) » ou « deny (refuser) » pour les utilisateurs externes. Peut-on faire cela dans Azure ?
Oui. Azure AD B2B Collaboration prend en charge les listes autorisées et refusées. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Quelles licences faut-il avoir pour utiliser Azure AD B2B ?
Pour plus d’informations sur les licences dont votre organisation a besoin pour utiliser Azure AD B2B, consultez la page [Aide sur les licences Azure Active Directory B2B Collaboration](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure AD ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure AD B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [API et personnalisation d’Azure AD B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure AD](active-directory-apps-index.md)
