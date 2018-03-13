---
title: "Comment choisir le type d’application à utiliser lors de l’ajout d’une application | Microsoft Docs"
description: "Connaître les types d’applications pris en charge que vous pouvez intégrer à Azure AD et leurs options de configuration connexes"
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
ms.openlocfilehash: e4a5ee41349a97493636327e3bda25cbd507867c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>Comment choisir le type d’application à utiliser lors de l’ajout d’une application

Cet article vous aide à comprendre les quatre principaux types d’applications que vous pouvez intégrer à Azure AD :

* Ce qui est pris en charge par chacun d’eux
* Pourquoi choisir telle ou telle application
* Comment configurer les principales propriétés de cette application, par exemple la façon dont les utilisateurs sont **configurés** ou la technologie d’**authentification unique** à utiliser.

## <a name="supported-application-types-in-azure-ad"></a>Types d’applications pris en charge dans Azure AD

Azure AD prend en charge quatre types d’applications principaux que vous pouvez ajouter à l’aide de la fonction **Ajouter** qui se trouve sous **Applications d’entreprise**. Il s’agit des actions suivantes :

-   **Applications de la galerie Azure AD** : applications pré-intégrées pour l’authentification unique avec Azure AD.

-   **Applications du proxy d’application** : applications s’exécutant dans votre environnement local, pour lesquelles vous souhaitez utiliser l’authentification unique en externe.

-   **Applications personnalisées** : applications que votre organisation souhaite développer sur la plateforme de développement d’applications Azure AD, mais qui n’existent pas encore.

-   **Applications hors galerie** : créez vos propres applications ! Tous les liens web et toutes les applications disposant d’un champ de nom d’utilisateur et de mot de passe prennent en charge les protocoles SAML ou OpenID Connect, ou prennent en charge SCIM pour l’intégration à l’authentification unique avec Azure AD.

## <a name="features-and-capabilities-supported-by-all-the-preceding-application-types"></a>Fonctionnalités et capacités prises en charge par tous les types d’applications précédents

Les fonctionnalités ci-après sont prises en charge par les quatre types d’applications précédents dans Azure AD :

-   **Démarrage rapide** : commencez rapidement à utiliser une application en suivant des [étapes de déploiement simple](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started).

-   **Gestion des propriétés générales** : obtenez un [lien direct ciblé](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) vers une application, [personnalisez la marque](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) d’une application, ou [désactivez l’application](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) pour tous les utilisateurs.

-   **Gestion des utilisateurs et des groupes** : [affectez](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) des utilisateurs et des groupes à une application ou [supprimez](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)-les, et attribuez éventuellement les rôles d’application spécifiques auxquels ces utilisateurs et groupes ont accès.

-   **Accès aux applications en libre-service** : autorisez vos utilisateurs à demander un [accès à une application en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) à partir de leurs panneaux d’accès aux applications, soit en ajoutant directement une application, soit en [rejoignant un groupe pour lequel le libre-service est activé](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), éventuellement en exigeant une approbation d’entreprise au cours du processus.

-   **Journaux de connexion** : affichez [toutes les connexions à une application](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), ou à toutes vos applications.

-   **Journaux d’audit** : consultez [des journaux d’audit détaillés sur les modifications apportées à une application](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), ou à toutes vos applications.

-   **Accès conditionnel et basé sur les risques** : définissez de puissantes [règles d’accès basées sur des conditions](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), appliquées lorsque les utilisateurs tentent de se connecter à une application spécifique.

-   **Affichage des autorisations** : visualisez les [autorisations OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) auxquelles une application a accès dans votre répertoire, à partir d’un emplacement unique.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modes d’authentification unique et d’approvisionnement pris en charge par des types d’applications spécifiques

Le tableau ci-après décrit les différents modes d’authentification unique et d’approvisionnement pris en charge par chacun des types d’applications précédents. Vous pouvez utiliser ce tableau pour mieux comprendre quelle application vous devez ajouter pour répondre à un objectif précis.

  ![Tableau des types d’applications](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Comment choisir un mode d’authentification unique

Vous trouverez ci-après les modes **d’authentification unique** pris en charge pour les applications Azure AD.

-   **Authentification unique Azure AD désactivée** : choisissez le **mode d’authentification unique** Authentification unique Azure AD désactivée si vous n’êtes pas encore prêt à intégrer cette application à l’authentification unique avec Azure AD, ou si vous êtes uniquement en train de la tester.

-   **Authentification liée** : choisissez le **mode d’authentification unique**[Authentification liée](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si vous disposez d’une application qui est déjà connectée à une solution d’authentification unique, ou si vous voulez publier un lien simple dans le [panneau d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) de vos utilisateurs ou dans le [lanceur d’applications Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none).

-   **Authentification par mot de passe** : choisissez le **mode d’authentification unique**[Authentification par mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si votre application affiche un champ de nom d’utilisateur et de mot de passe HTML et que vous souhaitez stocker ce nom d’utilisateur et ce mot de passe en toute sécurité pour vous reconnecter ultérieurement à l’application.

-   **SAML-based Sign-on** (Authentification SAML) : choisissez le mode d’authentification unique [SAML-based Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) (Authentification SAML) si votre application prend en charge les protocoles SAML ou OpenID Connect, ou si vous souhaitez être en mesure de mapper les utilisateurs à des rôles d’application spécifiques en fonction de règles que vous définissez dans vos demandes SAML *.

   >[!NOTE]
   >Cette option n’est pas disponible lorsque le proxy d’application est configuré pour une application.
   >
   >

-   **Authentification basée sur l’en-tête** : choisissez le mode d’authentification unique [Authentification basée sur l’en-tête](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) si vous disposez d’une application utilisant PingAccess qui prend en charge l’authentification basée sur un en-tête HTTP et pour laquelle vous souhaitez utiliser l’authentification unique. 

   >[!NOTE]
   >Cette option est uniquement disponible si le proxy d’application et PingAccess sont configurés pour une application.
   >
   >

-   **Authentification Windows intégrée** : choisissez le mode d’authentification unique [Authentification Windows intégrée](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) lorsque vous exposez une application WIA locale pour laquelle vous souhaitez utiliser l’authentification unique. 

   >[!NOTE]
   >Cette option est uniquement disponible lorsque le proxy d’application est configuré pour une application.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modes d’authentification unique pour les applications personnalisées

Les applications que vous avez développées de manière personnalisée par le biais de la fonction [d’application personnalisée](#_Custom-Developed_Applications) prennent également en charge des modes d’authentification unique supplémentaires non répertoriés précédemment, comprenant :

-   Authentification basée sur [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

-   Authentification basée sur [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

-   Authentification basée sur [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

-   Authentification basée sur [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Pour plus d’informations sur la procédure de création d’une application personnalisée qui prend en charge ces modes d’authentification unique, consultez le [Guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

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

## <a name="how-to-choose-a-provisioning-mode"></a>Comment choisir un mode d’approvisionnement

-   **Approvisionnement manuel** : choisissez le mode d’approvisionnement [Manuel](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) si vous avez des comptes existants, ou si vous souhaitez gérer les comptes pour cette application hors d’Azure AD.

-   **Approvisionnement automatique** : choisissez le **mode d’approvisionnement**[automatique](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) si vous souhaitez activer l’approvisionnement automatique basé sur les API et/ou l’annulation d’approvisionnement des comptes utilisateur pour cette application. 

   >[!NOTE]
   >Cette option est disponible uniquement pour les applications de la catégorie **Applications à la une** de la [galerie d’applications Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery).
   >
   >

-   **SCIM-based Automatic Provisioning** (Approvisionnement automatique SCIM) : utilisez l’[approvisionnement automatique SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) si votre application prend en charge le protocole SCIM pour détecter les modifications apportées aux utilisateurs et aux groupes, qui sont alors automatiquement appliquées à toutes les applications intégrées à Azure AD. 

   >[!NOTE]
   >Cette option n’est pas répertoriée comme un mode d’approvisionnement spécifique, mais est activée par défaut pour toutes les applications intégrées à Azure AD.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Comment définir le mode d’approvisionnement d’une application

Pour définir le mode **d’approvisionnement** d’une application, procédez comme suit :

Pour définir le mode **d’authentification unique** d’une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez n’apparaît pas, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’approvisionnement.

7.  Une fois l’application chargée, cliquez sur **Approvisionnement** dans le menu de navigation de gauche de l’application.

## <a name="next-steps"></a>Étapes suivantes
[Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md)
