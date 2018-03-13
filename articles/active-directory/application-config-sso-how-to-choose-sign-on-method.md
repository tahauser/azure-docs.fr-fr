---
title: "Déterminer quelle méthode d’authentification unique utiliser | Microsoft Docs"
description: "Découvrez les modes d’authentification unique pris en charge par Azure AD et comment choisir celui adapté à l’application qui vous intéresse."
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
ms.openlocfilehash: 4363f308f9bfa685b0f4e946cdd97d34b6ece197
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Déterminer quelle méthode d’authentification unique utiliser

Cet article présente les modes d’authentification unique pris en charge par Azure AD et vous montre comment choisir celui adapté à l’application qui vous intéresse.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modes d’authentification unique et d’approvisionnement pris en charge par des types d’applications spécifiques

Le tableau ci-après décrit les différents modes d’authentification unique et d’approvisionnement pris en charge par chacun des types d’applications précédents. Vous pouvez utiliser ce tableau pour mieux comprendre quelle application vous devez ajouter pour répondre à un objectif précis.

  ![Tableau des types d’applications](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Comment choisir un mode d’authentification unique

Vous trouverez ci-après les modes **d’authentification unique** pris en charge pour les applications Azure AD.

-   **Authentification unique Azure AD désactivée** : choisissez le **mode d’authentification unique** Authentification unique Azure AD désactivée si vous n’êtes pas encore prêt à intégrer cette application à l’authentification unique avec Azure AD, ou si vous êtes uniquement en train de la tester.

-   **Authentification liée** : choisissez le **mode d’authentification unique**[Authentification liée](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si vous disposez d’une application qui est déjà connectée à une solution d’authentification unique, ou si vous voulez publier un lien simple dans le [panneau d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) de vos utilisateurs ou dans le [lanceur d’applications Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none).

-   **Authentification par mot de passe** : choisissez le **mode d’authentification unique**[Authentification par mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si votre application affiche un champ de nom d’utilisateur et de mot de passe HTML et que vous souhaitez stocker ce nom d’utilisateur et ce mot de passe en toute sécurité pour vous reconnecter ultérieurement à l’application.

-   **Authentification basée sur SAML** : choisissez le mode d’authentification unique [Authentification basée sur SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si votre application prend en charge les protocoles SAML ou OpenID Connect, ou que vous souhaitez être en mesure de mapper les utilisateurs sur des rôles d’application spécifiques en fonction de règles que vous définissez dans vos demandes SAML. (***Remarque :** cette option n’est pas disponible quand le proxy d’application est configuré pour une application.)*

-   **Authentification basée sur l’en-tête** : choisissez le mode d’authentification unique [Authentification basée sur l’en-tête](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) si vous disposez d’une application utilisant PingAccess qui prend en charge l’authentification basée sur l’en-tête HTTP et pour laquelle vous souhaitez utiliser l’authentification unique. (***Remarque :** cette option est uniquement disponible quand le proxy d’application et PingAccess sont configurés pour une application.)*

-   **Authentification Windows intégrée** : choisissez le mode d’authentification unique [Authentification Windows intégrée](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) lorsque vous exposez une application WIA locale pour laquelle vous souhaitez utiliser l’authentification unique. (***Remarque :** cette option est uniquement disponible quand le proxy d’application est configuré pour une application.)*

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modes d’authentification unique pour les applications personnalisées

Les applications que vous avez développées de manière personnalisée par le biais de la fonction [d’application personnalisée](#_Custom-Developed_Applications) prennent également en charge des modes d’authentification unique supplémentaires non répertoriés précédemment, comprenant :

-   Authentification basée sur [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

-   Authentification basée sur [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

-   Authentification basée sur [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

-   Authentification basée sur [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Consultez le [guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) pour en savoir plus sur la création d’une application personnalisée qui prend en charge ces modes d’authentification unique.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Comment définir le mode d’authentification unique d’une application

Pour définir le mode **d’authentification unique** d’une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)

