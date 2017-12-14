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
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d40f67c354e29f3a58c9d0f1967e0154b5b998d0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-the-access-panel"></a>Présentation du volet d’accès

Le volet d’accès est un portail basé sur le web. Il permet à un utilisateur qui dispose d’un compte professionnel ou scolaire dans Azure Active Directory d’afficher et de démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Vous pouvez également utiliser des fonctionnalités de gestion de groupes et d’applications libre-service par le biais du volet d’accès.

Le volet d’accès est distinct du portail Azure et n’exige pas un abonnement Azure.

![Volet d'accès][1]

Le volet d’accès vous permet de modifier certains paramètres de profil, notamment :

- Changer le mot de passe associé à un compte professionnel ou scolaire

- Modifier les paramètres de réinitialisation de mot de passe

- Modifier les paramètres de contact et de préférence liés à l’authentification multifacteur (pour les comptes qui ont été contraints de l’utiliser par un administrateur)

- Afficher les détails de compte tels que l’ID d’utilisateur, l’adresse e-mail de secours, les numéros de téléphone mobile et de bureau et les appareils

- Afficher et démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès Pour plus d’informations sur le volet d’accès du point de vue de l’utilisateur, consultez Utilisation du volet d’accès. 

- Gérer les groupes en libre-service. Plus précisément, l’administrateur peut créer et gérer des groupes de sécurité et demander l’appartenance à des groupes de sécurité dans Azure AD. Pour plus d’informations, consultez [Gestion de groupe libre-service pour les utilisateurs dans Azure AD](active-directory-accessmanagement-self-service-group-management.md) et [Gérer vos groupes](active-directory-manage-groups.md).




## <a name="accessing-the-access-panel"></a>Accès au volet d’accès

Vous pouvez accéder au volet d’accès en visitant l’URL suivante dans un navigateur web : `http://myapps.microsoft.com`

Si vous avez configuré la personnalisation de votre page de connexion, vous pouvez charger cette personnalisation en ajoutant le domaine de votre organisation à la fin de l’URL : `http://myapps.microsoft.com/<your domain>.com`

Dans ce cas, vous pouvez utiliser n’importe quel nom de domaine actif ou vérifié ayant été configuré dans votre portail Azure.

![Nom de domaine Jouets Wingtip][2]  

Vous devez distribuer l’URL à tous les utilisateurs qui se connecteront aux applications intégrées à Azure AD.

## <a name="authentication"></a>Authentification

Pour accéder au volet d’accès, vous devez être authentifié via un compte professionnel ou scolaire dans Azure AD. Vous pouvez être authentifié dans Azure AD directement. En guise d’alternative, si une organisation a configuré la fédération à l’aide des services Active Directory Federation Services (AD FS) ou d’autres technologies, vous pouvez être authentifié par Windows Server Active Directory.

Si vous disposez d’un abonnement Azure ou Office 365 et que vous utilisez le portail Azure ou une application Office 365, vous pouvez voir la liste des applications sans avoir à vous reconnecter. Si vous n’êtes pas authentifié, vous êtes invité à vous connecter à l’aide du nom d’utilisateur et du mot de passe correspondant à votre compte dans Azure AD. Si votre organisation a configuré la fédération, la saisie du nom d’utilisateur suffit.

Une fois authentifié, vous pouvez interagir avec les applications que l’administrateur a intégrées à l’annuaire. Pour découvrir comment intégrer des applications à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Configuration requise du navigateur web

Au minimum, le volet d’accès nécessite un navigateur prenant en charge JavaScript et dans lequel CSS est activée. Pour que vous soyez connecté aux applications via l’authentification unique (SSO) avec mot de passe, l’extension du volet d’accès doit être installée dans votre navigateur. Cette extension est téléchargée automatiquement quand vous sélectionnez une application configurée pour l’authentification unique (SSO) avec mot de passe.

L’extension du volet d’accès est actuellement disponible pour les navigateurs Internet Explorer 8 et ultérieur, Edge, Chrome et Firefox.

## <a name="mobile-app-support"></a>Prise en charge des applications mobiles

L’équipe Azure Active Directory publie l’application mobile My Apps. Quand vous installez cette application, vous pouvez vous connecter aux applications SSO avec mot de passe sur des appareils iOS et Android.

> [!NOTE]
> Vous pouvez vous connecter aux applications qui prennent en charge la fédération avec Azure AD (notamment Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 et plus de 70 autres) sur presque n’importe quel navigateur web sur n’importe quel appareil sans nécessiter de plug-in ou d’application mobile. Le reste de l’[expérience de volet d’accès](https://myapps.microsoft.com/) ne nécessite pas non plus l’utilisation de l’application mobile My Apps sur un appareil mobile.
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



## <a name="managed-browser-for-my-apps"></a>Managed Browser pour My apps

My apps est également intégré à Intune Managed Browser. Intune Managed Browser pour les appareils iOS et Android joue un rôle clé pour garantir la sécurisation des données sur les appareils mobiles. Il vous permet d’afficher et de parcourir des pages web pouvant contenir des informations d’entreprise, et fournit une expérience de navigation web sécurisée.  
Vous bénéficiez d’un accès rapide à My Apps dans votre page d’accueil Managed Browser et dans vos favoris, ce qui vous permet d’accéder aux applications souhaitées en moins de clics.

Il est disponible dans l’[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) et le [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Managed Browser pour My apps][5]    





## <a name="tips-for-testing-the-user-experience"></a>Conseils pour tester l’expérience utilisateur

Si vous êtes administrateur Azure et que vous êtes connecté au portail Azure à l’aide d’un compte dans l’annuaire, vous êtes automatiquement connecté au volet d’accès sous votre compte actuel. Dans ce cas, vous pourrez voir toutes les applications qui vous ont été affectées.

**Pour tester sous un compte d’utilisateur *différent* :**

1. Cliquez sur le menu utilisateur dans le coin supérieur droit du portail Azure ou du volet d’accès, puis sélectionnez **Déconnexion**. 
2. Accédez au [volet d’accès](http://myapps.microsoft.com).
3. Dans la page de connexion, tapez le nom d’utilisateur et le mot de passe du compte dans l’annuaire que vous souhaitez tester.


## <a name="starting-applications"></a>Démarrage des applications

Plusieurs types d’applications peuvent apparaître dans le volet d’accès.

### <a name="office-365-applications"></a>Applications Office 365

Si votre organisation utilise des applications Office 365 et que vous disposez d’une licence pour celles-ci, les applications Office 365 apparaissent dans votre volet d’accès.

Quand vous cliquez sur la vignette d’une application Office 365, vous êtes redirigé vers l’application et connecté automatiquement.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applications Microsoft et tierces configurées avec l’authentification unique (SSO) basée sur la fédération

Votre administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec Azure AD**. Vous ne voyez ces applications que si votre administrateur vous a explicitement accordé un accès aux applications.

Quand vous cliquez sur la vignette de l’une de ces applications, vous êtes redirigé vers l’application et connecté automatiquement.

### <a name="password-based-sso-without-identity-provisioning"></a>Authentification unique avec mot de passe sans approvisionnement d’identité

Votre administrateur peut ajouter des applications à la section Active Directory du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec mot de passe**. Tous les utilisateurs de l’annuaire peuvent voir toutes les applications qui ont été configurées dans ce mode.

La première fois que vous cliquez sur la vignette de l’une de ces applications, vous êtes invité à installer le plug-in d’authentification unique (SSO) avec mot de passe pour Internet Explorer ou Chrome. L’installation peut nécessiter le redémarrage de votre navigateur web. Quand vous êtes renvoyé au volet d’accès et que vous recliquez sur la vignette de l’application, vous êtes invité à fournir un nom d’utilisateur et un mot de passe pour l’application. Une fois que vous avez entré le nom d’utilisateur et le mot de passe, ces informations d’identification sont stockées de manière sécurisée dans Azure AD et liées à votre compte dans Azure AD.

La prochaine fois que vous cliquez sur la vignette de l’application, vous êtes connecté automatiquement à l’application.  
Vous ne serez pas obligé de retaper vos informations d’identification ou d’installer le plug-in d’authentification unique (SSO) avec mot de passe.

Si vos informations d’identification ont changé dans l’application tierce cible, vous devez également mettre à jour vos informations d’identification stockées dans Azure AD. 

**Pour mettre à jour les informations d’identification**

1. Sélectionnez l’icône sur la vignette de l’application.
2. Sélectionnez **mettre à jour les informations d’identification** pour retaper le nom d’utilisateur et le mot de passe de l’application.


### <a name="password-based-sso-with-identity-provisioning"></a>Authentification unique avec mot de passe avec approvisionnement d’identité

Vous pouvez ajouter des applications à la section **Active Directory** du portail Azure avec le mode d’authentification unique (SSO) défini sur **Authentification unique avec mot de passe**, ainsi que l’approvisionnement d’identité.

La première fois que vous cliquez sur la vignette de l’une de ces applications, vous êtes invité à installer le **plug-in d’authentification unique (SSO) avec mot de passe pour Internet Explorer ou Chrome**. L’installation peut nécessiter le redémarrage de votre navigateur web.  
Quand vous revenez au volet d’accès et que vous recliquez sur la vignette de l’application, vous êtes connecté automatiquement à l’application.

Certaines applications peuvent exiger que vous changiez votre mot de passe lors de la première connexion. Si vos informations d’identification ont changé dans l’application tierce cible, vous devez également mettre à jour les informations d’identification stockées dans Azure AD. 

**Pour mettre à jour les informations d’identification**

1. Sélectionnez l’icône sur la vignette de l’application.
2. Sélectionnez **mettre à jour les informations d’identification** pour retaper le nom d’utilisateur et le mot de passe de l’application.


### <a name="application-with-existing-sso-solutions"></a>Application avec solutions d’authentification unique (SSO) existantes

Pour configurer l’authentification unique (SSO) pour une application, le portail Azure propose une troisième option appelée **authentification unique existante**. Cette option permet à votre administrateur de créer un lien vers une application et de le placer dans le volet d’accès pour les utilisateurs sélectionnés.

Par exemple, si une application est configurée pour authentifier les utilisateurs avec les services AD FS 2.0, votre administrateur peut utiliser l’option d’**authentification unique existante** pour créer un lien vers cette application dans le volet d’accès. Quand vous accédez au lien, vous êtes authentifié par le biais des services AD FS 2.0 ou de la solution d’authentification unique (SSO) existante fournie par l’application.


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une liste de toutes les rubriques liées à la gestion des applications, consultez l’[index des articles relatifs à la gestion des applications dans Azure Active Directory](active-directory-apps-index.md).
 
- Pour découvrir comment intégrer une application SaaS dans Azure AD, consultez la [liste des didacticiels sur l’intégration d’applications SaaS](active-directory-saas-tutorial-list.md).
 
- Pour en savoir plus sur la gestion des applications avec Azure AD, consultez l’[introduction à l’authentification unique et à la gestion de l’accès aux applications avec Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Pour plus d’informations sur l’approvisionnement des utilisateurs, consultez [Automatiser l’approvisionnement et l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
