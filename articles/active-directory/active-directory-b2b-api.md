---
title: "API et personnalisation d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
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
ms.date: 04/11/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: b40dc42c1dfc8910f9be9242fee3beeade92d193
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API et personnalisation d’Azure Active Directory B2B Collaboration

De nombreux clients nous ont indiqué qu’ils souhaitent personnaliser le processus d’invitation d’une manière qui convient mieux à leurs organisations. Avec notre API, cela est possible. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Fonctionnalités de l’API d’invitation
L’API offre les fonctionnalités suivantes :

1. Invitez un utilisateur externe avec *n’importe quelle* adresse e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personnalisez l’emplacement vers lequel vous souhaitez diriger vos utilisateurs une fois qu’ils ont accepté l’invitation.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Choisissez d’envoyer l’e-mail d’invitation standard par notre intermédiaire

    ```
    "sendInvitationMessage": true
    ```

  accompagné d’un message au destinataire que vous pouvez personnaliser

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Et choisissez de mettre en copie des personnes que vous souhaitez informer de votre invitation de ce collaborateur.

5. Ou personnalisez entièrement votre flux de travail d’invitation et d’accueil en choisissant de ne pas envoyer des notifications via Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  Dans ce cas, vous obtenez une URL d’échange de la part de l’API que vous pouvez incorporer dans un modèle d’e-mail, un message instantané ou toute autre méthode de distribution de votre choix.

6. Enfin, si vous êtes un administrateur, vous pouvez choisir d’inviter l’utilisateur en tant que membre.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modèle d’autorisation
L’API peut être exécutée dans les modes d’autorisation suivants :

### <a name="app--user-mode"></a>Mode Application + Utilisateur
Dans ce mode, toute personne utilisant l’API doit disposer des autorisations permettant de créer des invitations B2B.

### <a name="app-only-mode"></a>Mode Application uniquement
Dans le contexte Application uniquement, l’application a besoin de l’étendue User.Invite.All pour que l’invitation réussisse.

Pour plus d’informations, consultez : https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Il est désormais possible d’utiliser PowerShell pour ajouter et inviter facilement des utilisateurs externes au sein d’une organisation. Créez une invitation à l’aide de l’applet de commande suivante :

```
New-AzureADMSInvitation
```

Vous pouvez utiliser les options suivantes :

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Vous pouvez également extraire la référence d’API d’invitation dans [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
