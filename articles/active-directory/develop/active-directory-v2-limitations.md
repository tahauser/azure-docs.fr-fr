---
title: "Azure Active Directory v2.0 : limites et restrictions du point de terminaison | Microsoft Docs"
description: "Une liste des limitations et restrictions associées au point de terminaison v2.0 Azure AD."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a81f505c189da31edb91d1b522d9f3140f821cb4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="should-i-use-the-v20-endpoint"></a>Dois-je utiliser le point de terminaison v2.0 ?
Quand vous créez des applications qui s’intègrent dans Azure Active Directory, vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 répondent à vos besoins. Le point de terminaison d’origine d’Azure Active Directory est toujours intégralement pris en charge. À certains égards, il est plus riche en fonctionnalités que le point de terminaison v2.0. Toutefois, le point de terminaison v2.0 [présente des avantages significatifs](active-directory-v2-compare.md) pour les développeurs.

Voici notre recommandation simplifiée pour les développeurs à ce stade :

* Si vous devez prendre en charge des comptes personnels Microsoft dans votre application, utilisez le point de terminaison v2.0. Mais avant cela, tenez compte des limitations abordées dans cet article.
* Si votre application ne doit prendre en charge que des comptes professionnels et scolaires Microsoft, n’utilisez pas le point de terminaison v2.0. Dans ce cas, consultez notre [guide de développement Azure AD](active-directory-developers-guide.md).

Au fil du temps, le point de terminaison v2.0 se développera et les restrictions répertoriées ici seront éliminées. Ainsi, vous n’aurez qu’à utiliser le point de terminaison v2.0. En attendant, cet article vous aide à déterminer si le point de terminaison v2.0 répond à vos besoins. Nous continuerons à mettre à jour cet article pour refléter l’état actuel du point de terminaison v2.0. Consultez-le régulièrement pour réévaluer vos besoins par rapport aux fonctionnalités de la version 2.0.

Si vous disposez d’une application associée à Azure AD qui ne recourt pas au point de terminaison v2.0, il n’est pas nécessaire de repartir de zéro. À l’avenir, nous vous fournirons un moyen d’utiliser vos applications Azure AD existantes avec le point de terminaison v2.0.

## <a name="restrictions-on-app-types"></a>Restrictions concernant les types d’applications
Actuellement, les types d’applications suivants ne sont pas pris en charge par le point de terminaison v2.0. Pour obtenir une description des types d’applications pris en charge, consultez [Types d’applications pour le point de terminaison Azure Active Directory v2.0](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>API Web autonome
Vous pouvez utiliser le point de terminaison v2.0 pour [générer une API web sécurisée avec OAuth 2.0](active-directory-v2-flows.md#web-apis). Toutefois, cette API web peut recevoir uniquement les jetons d’une application ayant le même ID d’application. Vous ne pouvez pas accéder à une API web à partir d’un client qui a un ID d’application différent. Ce client ne pourra pas demander ou obtenir d’autorisation d’accès à votre API web.

Pour voir comment créer une API web qui accepte des jetons d’un client ayant un ID d’application identique, consultez les exemples d’API web de point de terminaison v2.0 de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application
À l’heure actuelle, pour chaque application que vous souhaitez intégrer au point de terminaison v2.0, vous devez créer une inscription d’application dans le nouveau [portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Les applications de compte Microsoft ou Azure AD existantes ne sont pas compatibles avec le point de terminaison v2.0. Les applications qui sont inscrites dans un portail autre que le portail d’inscription des applications ne sont pas compatibles avec le point de terminaison v2.0. À l’avenir, nous prévoyons de fournir un moyen d’utiliser des applications existantes en tant qu’applications v2.0. Cependant, il n’existe actuellement aucun chemin de migration permettant à une application existante de fonctionner avec le point de terminaison v2.0.

De plus, les inscriptions d’applications que vous créez dans le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ont les caractéristiques suivantes :

* Seuls deux secrets d’application sont autorisés par ID d’application.
* Une application inscrite par un utilisateur dans un compte Microsoft personnel ne peut être affichée et gérée que par un compte de développeur. Elle ne peut pas être partagée entre plusieurs développeurs.  Si vous souhaitez partager votre inscription d’application avec plusieurs développeurs, vous pouvez créer l’application en vous connectant au portail d’inscription avec un compte Azure AD.
* Il existe plusieurs restrictions quant au format de l’URI de redirection autorisé. Pour plus d’informations sur les URI de redirection, consultez la section suivante.

## <a name="restrictions-on-redirect-uris"></a>Restrictions concernant les URI de redirection
Actuellement, les applications inscrites dans le portail d’inscription des applications sont limitées à un jeu restreint de valeurs d’URI de redirection. L’URI de redirection pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URI de redirection doivent partager un seul domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web contenant l’un de ces URI de redirection :

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URI de redirection existante au nom DNS de l’URI de redirection que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :  

* Le nom DNS complet de la nouvelle URI de redirection ne correspond pas au nom DNS de l’URI de redirection existante.
* Le nom DNS complet de la nouvelle URI de redirection n’est pas un sous-domaine de l’URI de redirection existante.

Par exemple, si l’application a cet URI de redirection :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous souhaitez disposer d’une application avec login-east.contoso.com et login-west.contoso.com en tant qu’URI de redirection, vous devez ajouter ces URI de redirection dans l’ordre suivant :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Vous pouvez ajouter les deux derniers car il s’agit de sous-domaines du premier URI de redirection, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Notez également qu’une même application ne peut avoir que 20 URL de réponse.

Pour savoir comment inscrire une application dans le portail d’inscription des applications, consultez [Inscription d’une application avec le point de terminaison v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restrictions concernant les services et API
Actuellement, le point de terminaison v2.0 prend en charge la connexion de toute application inscrite dans le portail d’inscription des applications et figurant dans la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md). Toutefois, ces applications peuvent obtenir des jetons d’accès OAuth 2.0 pour un ensemble très limité de ressources. Le point de terminaison v2.0 délivre des jetons d’accès uniquement pour :

* L’application qui a demandé le jeton. Une application peut obtenir un jeton d’accès pour son propre compte, si l’application logique est composée de plusieurs composants ou niveaux. Pour voir ce scénario en action, consultez nos didacticiels [Prise en main](active-directory-appmodel-v2-overview.md#getting-started) .
* La messagerie Outlook, le calendrier et les API REST de Contacts, qui se trouvent à l’adresse https://outlook.office.com. Pour savoir comment écrire une application qui accède à ces API, consultez les didacticiels de [Prise en main d’Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
* Les API Microsoft Graph. Vous pouvez en savoir plus sur [Microsoft Graph](https://graph.microsoft.io) et les données qui vous sont accessibles.

Aucun autre service n’est actuellement pris en charge. Davantage de services Microsoft Online seront ajoutés prochainement, en plus de la prise en charge de vos propres services et API web personnalisés.

## <a name="restrictions-on-libraries-and-sdks"></a>Restrictions concernant les bibliothèques et les SDK
Actuellement, la prise en charge des bibliothèques pour le point de terminaison v2.0 est limitée. Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

* Si vous générez une application web, vous pouvez en toute sécurité utiliser le middleware Microsoft mis à la disposition générale côté serveur pour vous connecter et procéder à la validation des jetons. Vous recourrez notamment au middleware OWIN Open ID Connect pour ASP.NET et au plug-in Node.js Passport. Pour obtenir des exemples de code qui utilisent le middleware Microsoft, consultez la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).
* Si vous créez une application de bureau ou mobile, vous pouvez utiliser l’une de nos bibliothèques d’authentification Microsoft (MSAL).  Bien qu’il s’agisse de versions préliminaires, ces bibliothèques sont utilisables dans des applications de production. Pour en savoir plus sur les versions préliminaires et les bibliothèques disponibles, consultez notre [référence des bibliothèques d’authentification](active-directory-v2-libraries.md).
* Vous pouvez intégrer les plateformes non couvertes par les bibliothèques Microsoft, dans le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans le code de votre application. Les protocoles v2.0 OpenID Connect et OAuth [sont explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
* Pour finir, vous pouvez utiliser les bibliothèques open source Open ID Connect et OAuth pour procéder à l’intégration avec le point de terminaison v2.0. Le protocole v2.0 devrait être compatible avec de nombreuses bibliothèques de protocole open source sans modification majeure. La disponibilité de ces types de bibliothèques varie en fonction de la langue et de la plateforme. Les sites web [Open ID Connect](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) contiennent une liste à jour des implémentations les plus courantes. Pour plus d’informations, consultez [Azure Active Directory v2.0 et bibliothèques d’authentification](active-directory-v2-libraries.md), ainsi que la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison v2.0.

## <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles
Le point de terminaison 2.0 ne prend pas en charge SAML ou WS-Federation, mais uniquement Open ID Connect et OAuth 2.0.  Certaines fonctionnalités des protocoles OAuth n’ont pas été intégrées dans le point de terminaison v2.0. Ces fonctionnalités ne sont *pas disponibles* actuellement dans le point de terminaison v2.0 :

* Les jetons d’ID délivrés par le point de terminaison v2.0 ne contiennent pas de revendication `email` pour l’utilisateur, même si vous obtenez l’autorisation de l’utilisateur de consulter sa messagerie.
* Le point de terminaison OpenID Connect UserInfo n’est pas implémenté sur le point de terminaison v2.0. Toutefois, toutes les données de profil utilisateur que vous êtes susceptible de recevoir sur ce point de terminaison sont disponibles sur le point de terminaison Microsoft Graph `/me` .
* Le point de terminaison v2.0 ne prend pas en charge l’émission de revendications de rôle ou de groupe dans les jetons d’ID.
* Le point de terminaison v2.0 ne prend pas en charge l’[octroi des informations de mot de passe du propriétaire de la ressource OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3).

De plus, il ne prend en charge aucun protocole SAML ou WS-Federation.

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le point de terminaison v2.0, consultez notre page de [référence sur les protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrictions concernant les comptes professionnels et scolaires
Si vous avez utilisé la bibliothèque ADAL (Active Directory Authentication Library) dans des applications Windows, vous avez peut-être tiré parti de l’authentification intégrée Windows, qui utilise l’octroi d’assertions SAML (Security Assertion Markup Language). Avec cet octroi, les utilisateurs de locataires Azure AD fédérés peuvent s’authentifier en mode silencieux auprès de leur instance d’Active Directory locale sans entrer leurs informations d’identification. À l’heure actuelle, l’octroi d’assertion SAML n’est pas pris en charge sur le point de terminaison v2.0.
