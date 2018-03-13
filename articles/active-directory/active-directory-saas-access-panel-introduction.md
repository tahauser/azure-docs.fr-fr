---
title: "Présentation du volet d’accès dans Azure Active Directory ? | Microsoft Docs"
description: "Découvrez comment utiliser les différentes versions du volet d’accès (navigateur web, application Android, iPhone et iPad) pour accéder aux applications SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c9069cb0b46ddc1155c64bd63a7fcd8a685abbad
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="what-is-the-access-panel"></a>Présentation du volet d’accès

Le volet d’accès est un portail basé sur le web. Si vous disposez d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD), vous pouvez utiliser le volet d’accès pour afficher et démarrer des applications basées sur le cloud auxquelles un administrateur Azure AD vous a accordé un accès. Vous pouvez également utiliser des fonctionnalités de gestion de groupes et d’applications libre-service par le biais du volet d’accès.

Le volet d’accès est séparé du portail Azure. Il ne nécessite pas de disposer d’un abonnement Azure.

![Volet d’accès][1] Grâce au volet d’accès, vous pouvez modifier certains de vos paramètres de profil et procéder comme suit :

- Modifier le mot de passe associé à un compte professionnel ou scolaire.

- Modifier les paramètres de réinitialisation de mot de passe.

- Modifier les paramètres de contact et de préférence liés à l’authentification multifacteur (pour les comptes qui ont été contraints de l’utiliser par un administrateur).

- Afficher les détails de compte tels que l’ID d’utilisateur, l’adresse e-mail de secours, les numéros de téléphone mobile et de bureau et les appareils.

- Afficher et démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD vous a accordé un accès. 

- Gérer les groupes en libre-service. Les administrateurs peuvent créer et gérer des groupes de sécurité et demander l’appartenance à des groupes de sécurité dans Azure AD. Pour plus d’informations, consultez [Gestion de groupe libre-service pour les utilisateurs dans Azure AD](active-directory-accessmanagement-self-service-group-management.md) et [Gérer vos groupes](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>Accéder au volet d’accès

Vous pouvez accéder au volet d’accès en accédant à `http://myapps.microsoft.com`.

Si vous avez configuré la personnalisation de votre page de connexion, vous pouvez charger cette personnalisation en ajoutant le domaine de votre organisation à l’URL (par exemple, `http://myapps.microsoft.com/<your domain>.com`).

Vous pouvez utiliser n’importe quel nom de domaine actif ou vérifié ayant été configuré dans votre portail Azure, comme illustré ici :

![Nom de domaine Jouets Wingtip][2]  

Distribuez l’URL à tous les utilisateurs qui se connectent aux applications intégrées à Azure AD.

## <a name="authentication"></a>Authentification

Pour accéder au volet d’accès, vous devez être authentifié via un compte professionnel ou scolaire dans Azure AD. Vous pouvez être authentifié dans Azure AD directement. En guise d’alternative, si une organisation a configuré la fédération à l’aide des services Active Directory Federation Services (AD FS) ou d’autres technologies, vous pouvez être authentifié par Windows Server Active Directory.

Si vous disposez d’un abonnement Azure ou Office 365 et que vous utilisez le portail Azure ou une application Office 365, vous pouvez afficher la liste des applications sans avoir à vous reconnecter. Si vous n’êtes pas authentifié, vous êtes invité à vous connecter à l’aide du nom d’utilisateur et du mot de passe correspondant à votre compte dans Azure AD. Si votre organisation a configuré la fédération, la saisie du nom d’utilisateur suffit.

Une fois authentifié, vous pouvez interagir avec les applications que l’administrateur a intégrées à l’annuaire. Pour découvrir comment intégrer des applications à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Configuration requise du navigateur web

Au minimum, le volet d’accès nécessite un navigateur prenant en charge JavaScript et dans lequel CSS est activée. Pour se connecter aux applications via l’authentification unique (SSO) avec mot de passe, l’extension du volet d’accès doit être installée dans votre navigateur. Cette extension est téléchargée automatiquement quand vous sélectionnez une application configurée pour l’authentification unique (SSO) avec mot de passe.

L’extension du panneau d’accès est actuellement disponible pour :
- **Edge** : sur Windows 10 Édition anniversaire ou version ultérieure. 
- **Chrome** : sur Windows 7 ou version ultérieure, et sur Mac OS X ou version ultérieure.
- **Firefox 26.0 ou version ultérieure** : sur Windows XP SP2 ou version ultérieure, et sur Mac OS X 10.6 ou version ultérieure.
- **Internet Explorer 8, 9, 10, 11** : sur Windows 7 ou version ultérieure (prise en charge limitée).

## <a name="my-apps-secure-sign-in-extension"></a>Extension de connexion sécurisée à Mes applications
Pour vous connecter à l’authentification unique avec mot de passe, vous devez utiliser l’extension. Une fois que l’extension est installée, vous pouvez vous y connecter pour activer des fonctionnalités supplémentaires en sélectionnant **Connectez-vous pour commencer**. 

- Vous pouvez vous connecter à une application directement à l’aide de l’**URL de connexion** de l’application. Lorsque vous utilisez l’URL de l’application, l’extension détecte l’action et vous offre la possibilité de vous connecter à partir de l’extension.
- Vous pouvez lancer une des vos applications à partir du panneau d’accès à l’aide de la fonctionnalité de *recherche rapide* de l’extension. 
- L’extension vous présente les trois dernières applications que vous avez lancées dans la section **Récemment utilisé**.

> [!NOTE]
> Les fonctionnalités supplémentaires sont disponibles uniquement pour Edge, Chrome et Firefox.
>

Si vous utilisez une URL de Mes applications autre que `https://myapps.microsoft.com`, configurez votre URL par défaut en procédant comme suit :
1. *Sans être* connecté à l’extension, cliquez avec le bouton droit sur l’icône de l’extension.
2. Dans le menu, sélectionnez **My Apps URL** (URL de Mes applications).
3. Sélectionnez votre URL par défaut.
4. Sélectionnez l’icône de l’extension.
5. Sélectionnez **Connectez-vous pour commencer**.

## <a name="mobile-app-support"></a>Prise en charge des applications mobiles

L’équipe Azure Active Directory publie l’application mobile My Apps. Quand vous installez cette application, vous pouvez vous connecter aux applications SSO avec mot de passe sur des appareils iOS et Android.

> [!NOTE]
> Vous pouvez vous connecter aux applications qui prennent en charge la fédération avec Azure AD (notamment Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 et plus de 70 autres) sur presque n’importe quel navigateur web sur n’importe quel appareil sans nécessiter de plug-in ou d’application mobile. Pour l’utilisation sur un appareil mobile, le reste de l’[expérience de volet d’accès](https://myapps.microsoft.com/) ne nécessite pas non plus l’utilisation de l’application mobile My Apps.
>
>

### <a name="my-apps-for-android"></a>My Apps pour Android

My Apps pour Android est prise en charge sur n’importe quel appareil Android exécutant Android version 4.1 et ultérieures.  

Elle est disponible dans le [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![My Apps pour Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>My Apps pour iPhone et iPad

My Apps pour iOS est prise en charge sur n’importe quel iPhone ou iPad exécutant iOS version 7 et ultérieures.  

Elle est disponible dans l’[Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![My Apps pour iOS][4]    


## <a name="managed-browser-for-my-apps"></a>Managed Browser pour My Apps

My Apps est également intégrée à Intune Managed Browser. Intune Managed Browser pour les appareils iOS et Android joue un rôle clé pour garantir la sécurisation des données sur les appareils mobiles. Le navigateur vous permet d’afficher et de parcourir des pages web pouvant contenir des informations d’entreprise, et fournit une expérience de navigation web sécurisée.  

Vous disposez d’un accès rapide à My Apps dans votre page d’accueil Managed Browser et dans vos favoris, vous permettant ainsi d’accéder aux applications souhaitées en moins de clics.

Intune Managed Browser est disponible dans l’[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) et le [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Managed Browser pour My Apps][5]    


## <a name="tips-for-testing-the-user-experience"></a>Conseils pour tester l’expérience utilisateur

Si vous êtes administrateur Azure et que vous êtes connecté au portail Azure à l’aide d’un compte dans l’annuaire, vous êtes automatiquement connecté au volet d’accès sous votre compte actuel. Cette vue affiche toutes les applications qui vous sont assignées.

Pour tester dans un *autre* compte d’utilisateur, procédez comme suit :

1. En haut à droite du portail Azure ou du volet d’accès, sélectionnez **Déconnexion**. 
2. Accédez au [volet d’accès](http://myapps.microsoft.com).
3. Dans la page de connexion, tapez le nom d’utilisateur et le mot de passe du compte dans l’annuaire que vous souhaitez tester.


## <a name="starting-applications"></a>Démarrage des applications

Cette section décrit plusieurs types d’applications qui peuvent apparaître dans le volet d’accès.

### <a name="office-365-applications"></a>Applications Office 365

Si votre organisation utilise des applications Office 365 et que vous disposez d’une licence pour celles-ci, les applications Office 365 apparaissent dans votre volet d’accès.

Lorsque vous sélectionnez la vignette d’une application Office 365, vous êtes redirigé vers l’application et connecté automatiquement.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applications Microsoft et tierces configurées avec l’authentification unique (SSO) basée sur la fédération

Votre administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec Azure AD**. Vous ne voyez ces applications que si votre administrateur vous a explicitement accordé un accès à celles-ci.

Lorsque vous sélectionnez la vignette d’une application, vous êtes redirigé et connecté automatiquement à celle-ci.

### <a name="password-based-sso-without-identity-provisioning"></a>Authentification unique avec mot de passe sans approvisionnement d’identité

Votre administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec mot de passe**. Tous les utilisateurs de l’annuaire peuvent voir toutes les applications qui ont été configurées dans ce mode.

La première fois que vous sélectionnez une vignette d’application, vous êtes invité à installer le plug-in d’authentification unique (SSO) avec mot de passe pour Internet Explorer ou Chrome. L’installation peut nécessiter le redémarrage de votre navigateur web. Lorsque vous revenez au volet d’accès et que vous sélectionnez à nouveau la vignette de l’application, vous êtes invité à fournir un nom d’utilisateur et un mot de passe pour l’application. Une fois que vous avez entré le nom d’utilisateur et le mot de passe, les informations d’identification sont stockées de manière sécurisée dans Azure AD et liées à votre compte dans Azure AD.

La prochaine fois que vous sélectionnez la vignette de l’application, vous êtes connecté automatiquement à l’application.  

Vous ne serez pas obligé de retaper vos informations d’identification ou d’installer le plug-in d’authentification unique (SSO) avec mot de passe.

Si vos informations d’identification ont changé dans l’application tierce cible, vous devez également mettre à jour vos informations d’identification stockées dans Azure AD. 

Pour mettre à jour vos informations d’identification, procédez comme suit :

1. Sélectionnez l’icône sur la vignette de l’application.
2. Sélectionnez **mettre à jour les informations d’identification** pour retaper le nom d’utilisateur et le mot de passe de l’application.


### <a name="password-based-sso-with-identity-provisioning"></a>Authentification unique avec mot de passe avec approvisionnement d’identité

Votre administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec mot de passe**, ainsi que l’approvisionnement d’identité.

La première fois que vous sélectionnez une vignette d’application, vous êtes invité à installer le plug-in d’authentification unique (SSO) avec mot de passe pour Internet Explorer ou Chrome. L’installation peut nécessiter le redémarrage de votre navigateur web.  

Lorsque vous revenez au volet d’accès et que vous sélectionnez à nouveau la vignette de l’application, vous êtes connecté automatiquement à l’application.

Certaines applications peuvent exiger que vous changiez votre mot de passe lors de la première connexion. Si vos informations d’identification ont changé dans l’application tierce cible, vous devez également mettre à jour les informations d’identification stockées dans Azure AD. 

Pour mettre à jour vos informations d’identification, procédez comme suit :

1. Sélectionnez l’icône sur la vignette de l’application.
2. Sélectionnez **mettre à jour les informations d’identification** pour retaper le nom d’utilisateur et le mot de passe de l’application.


### <a name="application-with-existing-sso-solutions"></a>Application avec solutions d’authentification unique (SSO) existantes

Pour configurer l’authentification unique (SSO) pour une application, le portail Azure propose une troisième option appelée l’authentification unique existante. Cette option permet à votre administrateur de créer un lien vers une application et de le placer dans le volet d’accès pour les utilisateurs sélectionnés.

Par exemple, si une application est configurée pour authentifier les utilisateurs avec les services AD FS 2.0, votre administrateur peut utiliser l’option d’authentification unique existante pour créer un lien vers cette application dans le volet d’accès. Quand vous accédez au lien, vous êtes authentifié par le biais des services AD FS 2.0 ou de la solution d’authentification unique (SSO) existante fournie par l’application.


## <a name="next-steps"></a>Étapes suivantes

- Pour afficher une liste de toutes les rubriques liées à la gestion des applications, consultez l’[index des articles relatifs à la gestion des applications dans Azure Active Directory](active-directory-apps-index.md).
 
- Pour découvrir comment intégrer une application SaaS à Azure AD, consultez la [liste des didacticiels sur l’intégration d’applications SaaS](active-directory-saas-tutorial-list.md).
 
- Pour en savoir plus sur la gestion des applications avec Azure AD, consultez l’[introduction à l’authentification unique et à la gestion de l’accès aux applications avec Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Pour plus d’informations sur l’approvisionnement des utilisateurs, consultez [Automatiser l’approvisionnement et l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
