---
title: "Gestion de l’authentification unique pour les applications d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Gérer les paramètres d’authentification unique pour les applications d’entreprise au sein de votre organisation à partir de la galerie d’applications Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: markvi
ms.reviewer: asmalser
ms.openlocfilehash: bb9c2e1fdf392e234e6c72e0728ab04d2fd88a81
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Gestion de l’authentification unique pour les applications d’entreprise

Cet article explique comment utiliser le [portail Azure](https://portal.azure.com) pour gérer les paramètres d’authentification uniques pour les applications d’entreprise. Les applications d’entreprise sont des applications qui sont déployées et utilisées au sein de votre organisation. Cet article s’applique particulièrement aux applications qui ont été ajoutées à partir de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Recherche de vos applications dans le portail
Vous pouvez afficher et gérer toutes les applications d’entreprise qui sont configurées pour l’authentification unique dans le portail Azure. Les applications figurent dans la section **Tous les services** &gt; **Applications d’entreprise** du portail. 

![Panneau Applications d’entreprise][1]

Sélectionnez **toutes les applications** pour afficher une liste de toutes les applications qui ont été configurées. La sélection d’une application affiche les ressources pour cette application, où vous pouvez consulter des rapports sur cette application et gérer un large éventail de paramètres.

Pour gérer les paramètres d’authentification unique, sélectionnez **Authentification unique**.

![Panneau de ressources d’application][2]

## <a name="single-sign-on-modes"></a>Modes d’authentification unique
L’**Authentification unique** commence par un menu **Mode**, qui permet de configurer le mode d’authentification. Les options disponibles incluent :

* **Authentification basée sur SAML** : cette option est disponible si l’application prend en charge l’authentification unique fédérée complète avec Azure Active Directory à l’aide des protocoles de connexion SAML 2.0, WS-Federation ou OpenID.
* **Authentification par mot de passe** : cette option est disponible si Azure AD prend en charge le remplissage de formulaire de mot de passe pour cette application.
* **Authentification liée** : précédemment appelée « Authentification unique existante », cette option permet aux administrateurs de placer un lien vers cette application dans le panneau d’accès Azure AD ou le lanceur d’applications Office 365 de leurs utilisateurs.

Pour plus d’informations sur ces modes, voir [Fonctionnement de l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Authentification basée sur SAML
L’option **Authentification basée sur SAML** est divisée en quatre sections :

### <a name="domains-and-urls"></a>Domains and URLs (Domaines et URL)
C’est là que toutes les informations concernant le domaine et les URL de l’application doivent être ajoutées à votre annuaire Azure AD. Toutes les entrées requises pour que l’authentification unique fonctionne sont affichées directement dans l’écran, tandis que toutes les entrées facultatives peuvent être affichées en sélectionnant la case à cocher **Show advanced URL settings** (Afficher les paramètres d’URL avancés). La liste complète des entrées prises en charge inclut :

* **URL d’authentification** : adresse où l’utilisateur se connecte à l’application. Si l’application est configurée pour effectuer une authentification unique lancée par le fournisseur de services, quand un utilisateur accède à cette URL, le fournisseur de services redirige vers Azure AD pour authentifier et connecter l’utilisateur. 
  * Si ce champ est renseigné, Azure AD utilise l’URL pour démarrer l’application à partir d’Office 365 et du panneau d’accès Azure AD.
  * Si ce champ est omis, Azure AD effectue plutôt une authentification lancée par le fournisseur d’identité quand l’application est démarrée à partir d’Office 365, du panneau d’accès Azure AD ou de l’URL de connexion unique d’Azure AD.
* **Identificateur** : cet URI doit identifier de façon unique l’application pour laquelle l’authentification unique est configurée. Il s’agit de la valeur qu’Azure AD renvoie à l’application en tant que paramètre Audience du jeton SAML. L’application doit la valider. Cette valeur apparaît également en tant qu’ID d’entité dans les métadonnées SAML fournies par l’application.
* **URL de réponse** : l’URL de réponse est là où l’application attend le jeton SAML. Elle est aussi appelée URL ACS (Assertion Consumer Service). Une fois ces entrées effectuées, cliquez sur Suivant pour passer à l’écran suivant. Cet écran fournit des informations sur ce qui doit être configuré dans l'application pour lui permettre d'accepter un jeton SAML à partir d'Azure AD.
* **Relay State** (État du relais) : l’état du relais est un paramètre facultatif permettant d’indiquer à l’application où rediriger l’utilisateur une fois l’authentification terminée. En général, la valeur est une URL valide au niveau de l’application. Cependant, certaines applications utilisent ce champ différemment (voir la documentation relative à l’authentification unique de l’application pour plus d’informations). La possibilité de définir l’état du relais est une nouvelle fonctionnalité disponible uniquement dans le nouveau portail Azure.

### <a name="user-attributes"></a>Attributs utilisateur
C’est là que les administrateurs peuvent afficher et modifier les attributs envoyés dans le jeton SAML émis par Azure AD pour l’application chaque fois qu’un utilisateur se connecte.

Le seul attribut modifiable pris en charge est l’attribut **Identificateur d’utilisateur**. La valeur de cet attribut correspond au champ d’Azure AD qui identifie de façon unique chaque utilisateur au sein de l’application. Par exemple, si l’application a été déployée en utilisant l’adresse de messagerie comme nom d’utilisateur et identificateur unique, la valeur est définie sur le champ « user.mail » d’Azure AD.

### <a name="saml-signing-certificate"></a>Certificat de signature SAML
Cette section affiche les détails du certificat utilisé par Azure AD pour signer les jetons SAML émis pour l’application chaque fois que l’utilisateur s’authentifie. Elle permet d’inspecter les propriétés du certificat actuel, notamment sa date d’expiration.

### <a name="application-configuration"></a>Configuration de l’application
La dernière section fournit la documentation ou les contrôles requis pour configurer l’application de manière à utiliser Azure Active Directory comme fournisseur d’identité.

Le menu volant **Configurer l’application** propose de nouvelles instructions incorporées concises pour la configuration de l’application. Il s’agit d’une autre nouvelle fonctionnalité disponible uniquement dans le nouveau portail Azure.

> [!NOTE]
> Pour obtenir un exemple complet de documentation incorporée, voir l’application Salesforce.com. De la documentation est ajoutée en continu pour d’autres applications.
> 
> 

![Documents incorporés][3]

## <a name="password-based-sign-on"></a>Authentification par mot de passe
Si l’application le prend en charge, la sélection du mode d’authentification unique par mot de passe et du bouton **Enregistrer** configure instantanément ce type d’authentification pour l’application. Pour plus d’informations sur le déploiement de l’authentification unique par mot de passe, voir [Fonctionnement de l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Authentification par mot de passe][4]

## <a name="linked-sign-on"></a>Authentification liée
Si l’application le prend en charge, la sélection du mode d’authentification unique lié vous permet d’entrer l’URL de redirection à utiliser par le panneau d’accès Azure AD ou Office 365 lorsqu’un utilisateur clique sur cette application. Pour plus d’informations sur l’authentification unique liée (précédemment appelée Authentification unique existante), voir [Fonctionnement de l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Authentification liée][5]

##<a name="feedback"></a>Commentaires

Nous espérons que vous appréciez votre expérience d’Azure AD améliorée. Continuez à nous faire part de vos commentaires. Publiez vos commentaires et vos idées d’amélioration dans la section du **portail Admin** de notre [forum de commentaires](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nous sommes très heureux de créer chaque jour des nouveautés et de pouvoir bénéficier de vos commentaires pour concevoir et définir de nouvelles créations.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
