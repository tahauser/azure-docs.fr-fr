---
title: Inscription d’application - Azure Active Directory B2C
description: Inscription de votre application auprès d’Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: davidmu
ms.openlocfilehash: 0d3c351ebe70a963db0453538108ae9b2fefef86
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C : inscription de votre application

Ce démarrage rapide vous aide à inscrire une application dans un client B2C Microsoft Azure Active Directory (Azure AD) en quelques minutes. Lorsque vous avez terminé, votre application est inscrite comme utilisable dans le client Azure AD B2C.

## <a name="prerequisites"></a>Prérequis


Pour générer une application acceptant l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un client Azure Active Directory B2C. Obtenez votre propre client en suivant la procédure décrite dans [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md).

Les applications créées dans le portail Azure doivent être gérées à partir du même emplacement. Si vous modifiez des applications Azure AD B2C à l’aide de PowerShell ou d’un autre portail, elles ne sont plus prises en charge et ne fonctionnent pas avec Azure AD B2C. Consultez les détails dans la section [Applications ayant généré une erreur](#faulted-apps). 

Cet article utilise des exemples qui vous aideront à démarrer avec nos exemples. Les articles suivants fournissent plus d’informations sur ces exemples.

## <a name="navigate-to-b2c-settings"></a>Accéder aux paramètres B2C

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général du client B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Choisissez les étapes suivantes en fonction de votre type d’application

* [Inscrire une application web](#register-a-web-app)
* [Inscrire une API web](#register-a-web-api)
* [Inscrire une application mobile ou native](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Inscrire une application web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Créer une clé secrète client d’application web

Si votre application web appelle une API web sécurisée par Azure AD B2C, procédez comme suit :
   1. Créez une clé secrète d’application en accédant au panneau **Clés** et en cliquant sur le bouton **Générer la clé**. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.
   2. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
> 

[Passer aux **étapes suivantes**](#next-steps)

### <a name="register-a-web-api"></a>Inscrire une API web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Cliquez sur **Étendues publiées** pour ajouter des étendues si nécessaire. Par défaut, l’étendue « user_impersonation » est définie. L’étendue user_impersonation permet à d’autres applications d’accéder à cette API pour le compte de l’utilisateur connecté. Si vous le souhaitez, l’étendue user_impersonation peut être supprimée.

[Passer aux **étapes suivantes**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Inscrire une application mobile/native

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Passer aux **étapes suivantes**](#next-steps)

## <a name="limitations"></a>Limites

### <a name="choosing-a-web-app-or-api-reply-url"></a>Choix d’une URL de réponse d’API ou d’application web

Pour l’instant, les applications qui sont inscrites auprès d’Azure AD B2C sont limitées à un ensemble restreint de valeurs d’URL de réponse. L’URL de réponse pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URL de réponse doivent partager un même domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web comportant l’une des URL de réponse suivantes :

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URL de réponse existante au nom DNS de l’URL de réponse que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :

* Le nom DNS complet de la nouvelle URL de réponse ne correspond pas au nom DNS de l’URL de réponse existante.
* Le nom DNS complet de la nouvelle URL de réponse n’est pas un sous-domaine de l’URL de réponse existante.

Par exemple, si l’application a cette URL de réponse :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous voulez disposer d’une application avec login-east.contoso.com et login-west.contoso.com comme URL de réponse, vous devez ajouter ces URL de réponse dans l’ordre suivant :

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Vous pouvez ajouter les deux derniers car il s’agit de sous-domaines de la première URL de réponse, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Choix d’un URI de redirection d’application native

Il existe deux points importants à prendre en considération lors du choix d’un URI de redirection pour les applications mobiles/natives :

* **Unique** : le schéma de l’URI de redirection doit être unique pour chaque application. Dans l’exemple (com.onmicrosoft.contoso.appname://redirect/path), com.onmicrosoft.contoso.appname correspond au schéma. Nous vous recommandons de suivre ce modèle. Si deux applications partagent le même schéma, l’utilisateur voit une boîte de dialogue « Choix d’une application ». Si l’utilisateur effectue un choix incorrect, la connexion échoue.
* **Complet** : l’URI de redirection doit comporter un schéma et un chemin d’accès. Le chemin d’accès doit contenir au moins une barre oblique après le domaine (par exemple, //contoso/ fonctionne, tandis que //contoso échoue).

Vérifiez que l’URI de redirection ne comporte aucun caractère spécial, tel que des traits de soulignement.

### <a name="faulted-apps"></a>Applications ayant généré une erreur

Les applications B2C ne doivent PAS être modifiées :

* Sur les autres portails de gestion des applications tels que le [Portail d’inscription des applications](https://apps.dev.microsoft.com/)
* À l’aide de l’API Graph ou de PowerShell

Si vous modifiez l’application Azure AD B2C comme décrit et tentez de la modifier de nouveau dans les fonctionnalités d’Azure AD B2C sur le portail Azure, l’application génère une erreur et n’est plus utilisable avec Azure AD B2C. Vous devez supprimer l’application, puis la recréer.

Pour supprimer l’application, accédez au [Portail d’inscription des applications](https://apps.dev.microsoft.com/), puis supprimez l’application à cet emplacement. Pour que l’application soit visible, vous devez en être le propriétaire (et non simplement un administrateur du locataire).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez une application inscrite auprès d’Azure AD B2C, vous pouvez suivre l’un des [didacticiels de démarrage rapide](active-directory-b2c-overview.md#get-started) afin de devenir opérationnel.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET avec inscription, connexion et réinitialisation de mot de passe](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
