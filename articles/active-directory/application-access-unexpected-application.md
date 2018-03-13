---
title: "Application inattendue dans ma liste d’applications | Microsoft Docs"
description: "Guide pratique pour afficher toutes les applications dans votre client et comprendre comment les applications apparaissent dans votre liste Toutes les applications sous Applications d’entreprise"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ef04e557aca69e5a15bb38d816ca9f9b9ffaf502
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="unexpected-application-in-my-applications-list"></a>Application inattendue dans ma liste d’applications

Cet article vous aide à comprendre comment les applications apparaissent dans votre liste **Toutes les applications** sous **Applications d’entreprise**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Guide pratique pour afficher toutes les applications dans votre client

Pour afficher toutes les applications dans votre client, vous devez utiliser le contrôle **Filtrer** pour afficher **Toutes les applications** sous la liste **Toutes les applications**. Procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

6.  Cliquez sur le contrôle **Filtrer** en haut de la **liste de toutes les applications**.

7.  Dans le volet **Filtrer**, définissez l’option **Afficher** sur **Toutes les applications**.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Pourquoi une application spécifique apparaît-elle dans ma liste de toutes les applications ?

Lors du filtrage avec **Toutes les applications**, la **liste** **Toutes les applications** affiche chaque objet Principal de service dans votre client. Les objets Principal de service peuvent apparaître dans cette liste de différentes façons :

1.  Lorsque vous ajoutez une application à partir de la galerie d’applications, y compris :

   1. **Applications de la galerie Azure AD** : applications pré-intégrées pour l’authentification unique avec Azure AD.

   2. **Applications du proxy d’application** : applications s’exécutant dans votre environnement local, pour lesquelles vous souhaitez utiliser l’authentification unique en externe.

   3. **Applications personnalisées** : applications que votre organisation souhaite développer sur la plateforme de développement d’applications Azure AD, mais qui n’existent peut-être pas encore.

   4. **Applications hors galerie** : créez vos propres applications ! Tous les liens web et toutes les applications disposant d’un champ de nom d’utilisateur et de mot de passe prennent en charge les protocoles SAML ou OpenID Connect, ou prennent en charge SCIM pour l’intégration à l’authentification unique avec Azure AD.

2.  Lors de l’inscription ou de l’ouverture d’une session, une application <sup>tierce</sup> intégrée à Azure Active Directory. Exemple : [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Lors de l’inscription ou de l’ajout d’une licence à un utilisateur ou à un groupe pour une application principale, par exemple [Microsoft Office 365](http://products.office.com/).

4.  Lorsque vous ajoutez une inscription pour une nouvelle application en créant une application personnalisée à l’aide du [Registre d’application](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).

5.  Lorsque vous ajoutez une inscription pour une nouvelle application en créant une application personnalisée à l’aide du [Portail d’inscription des applications V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal).

6.  Lorsque vous ajoutez une application que vous développez à l’aide des [méthodes d’authentification ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) ou des [Services connectés](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) de Visual Studio.

7.  Lorsque vous créez un objet Principal de service à l’aide du [module Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0).

8.  Lorsque vous [donnez votre consentement à une application](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) en tant qu’administrateur concernant l’utilisation des données de votre locataire.

9.  Lorsqu’un [utilisateur donne son consentement à une application](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) concernant l’utilisation des données de votre locataire.

10. Lorsque vous activez certains services qui stockent des données dans votre client. Un exemple correspond à l’opération de réinitialisation du mot de passe, qui est modélisée en tant que principal de service pour stocker de façon sécurisée votre stratégie de réinitialisation du mot de passe.

Pour plus d’informations sur la façon dont les applications sont ajoutées à votre annuaire, lisez [Comment et pourquoi les applications sont ajoutées à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Je souhaite supprimer l’attribution d’une application à un utilisateur ou groupe spécifique

Pour supprimer l’attribution d’une application à un utilisateur ou groupe, suivez les étapes répertoriées dans l’article [Supprimer l’attribution d’une application à un utilisateur ou groupe à partir d’une application d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Je souhaite désactiver tous les accès à une application pour chaque utilisateur

Pour désactiver toutes les connexions des utilisateurs à une application, suivez les étapes répertoriées dans l’article [Désactiver les connexions des utilisateurs à une application d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Je veux complètement supprimer une passerelle d’application

Pour **supprimer une application**, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez n’apparaît pas, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application que vous voulez supprimer.

7.  Une fois l’application chargée, cliquez sur l’icône **Supprimer** dans le volet supérieur **Vue d’ensemble** de l’application.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Je veux désactiver toutes les futures opérations de consentement de l’utilisateur pour n’importe quelle application

La désactivation du consentement de l’utilisateur pour votre annuaire entier empêche les utilisateurs finaux de donner leur consentement pour n’importe quelle application. Les administrateurs peuvent toujours donner leur consentement au nom de l’utilisateur. Pour plus d’informations sur le consentement de l’application et sur les conditions pour donner ou refuser ce consentement, consultez la section [Comprendre le consentement de l’utilisateur et de l’administrateur](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Pour **désactiver toutes les futures opérations de consentement de l’utilisateur dans l’ensemble de votre annuaire**, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Paramètres utilisateur**.

6.  Désactivez toutes les futures opérations de consentement de l’utilisateur en définissant l’option **Les utilisateurs peuvent autoriser les applications à accéder à leurs données** sur **Non**, puis cliquez sur le bouton **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
[Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md)
