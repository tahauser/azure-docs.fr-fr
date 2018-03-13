---
title: "Applications web de gestion des identités de cloud et applications mobiles Azure AD B2C | Microsoft Docs"
description: "Développement d’applications accessibles aux consommateurs avec Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.custom: seohack1
ms.openlocfilehash: 7881793ebce10b4f9fbc9c534d9d6fc0ebb38f0b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C : concentrez-vous sur votre application, nous nous chargeons de l’inscription et de la connexion

Azure AD B2C est une solution de gestion des identités de cloud pour applications web et mobiles. Il s’agit d’un service global hautement disponible dont l’échelle s’adapte à des centaines de millions d’identités. Basé sur une plateforme sécurisée à l’échelle de l’entreprise, Azure AD B2C protège vos applications, votre entreprise et vos clients.

Avec une configuration minimale, Azure AD B2C permet à votre application d’authentifier :

* des **comptes de réseaux sociaux** (tels que Facebook, Google, LinkedIn et autres) ;
* des **comptes d’entreprise** (à l’aide de protocoles de standards ouverts, d’OpenID Connect ou de SAML) ;
* des **comptes locaux** (adresse e-mail et mot de passe ou nom d’utilisateur et mot de passe).

## <a name="get-started"></a>Prise en main

D’abord, obtenez votre propre client en suivant la procédure décrite dans [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md).

Puis, choisissez le scénario de développement de votre application :

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Applications mobiles et de bureau](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Applications mobiles et de bureau</center> | [Vue d’ensemble](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Vue d’ensemble](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.JS](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Applications à page unique](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Applications à page unique</center> | [Vue d’ensemble](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![API Web](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />API Web</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [Appeler une API web .NET](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Nouveautés

Consultez régulièrement cette page pour en savoir plus sur les futures modifications apportées à Azure Active Directory B2C. Nous communiquons également les mises à jour sur Tweeter via @AzureAD.

* En plus des « Stratégies prédéfinies » (disponibilité générale), la fonctionnalité [« Stratégies personnalisées »](active-directory-b2c-overview-custom.md) est désormais disponible en version préliminaire publique.  Les stratégies personnalisées sont conçues pour les professionnels de l’identité qui doivent contrôler la composition de leur expérience d’identité.
* La fonctionnalité [Jeton d’accès](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) est désormais disponible en version préliminaire publique.
* [La disponibilité générale des annuaires Azure AD B2C basés en Europe](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/) a été annoncée.
* Consultez notre bibliothèque croissante [d’exemples de code sur Github](https://github.com/Azure-Samples?q=b2c).

## <a name="how-to-articles"></a>Articles sur les procédures

Découvrez comment utiliser des fonctionnalités spécifiques d’Azure Active Directory B2C :

* Configurer des comptes [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) et [LinkedIn](active-directory-b2c-setup-li-app.md) à utiliser dans vos applications grand public.
* [Utiliser des attributs personnalisés pour recueillir des informations sur vos consommateurs](active-directory-b2c-reference-custom-attr.md).
* [Activer Azure Multi-Factor Authentication dans vos applications grand public](active-directory-b2c-reference-mfa.md).
* [Configurer une réinitialisation de mot de passe libre-service pour vos consommateurs](active-directory-b2c-reference-sspr.md).
* [Personnaliser l’aspect des pages d’inscription, de connexion et des autres pages accessibles aux consommateurs](active-directory-b2c-reference-ui-customization.md) prises en charge par Azure Active Directory B2C.
* [Utiliser l’API Azure Active Directory Graph pour créer, lire, mettre à jour et supprimer des consommateurs par programme](active-directory-b2c-devquickstarts-graph-dotnet.md) dans votre client Azure Active Directory B2C.

## <a name="next-steps"></a>Étapes suivantes

Les liens ci-dessous vous permettent d’explorer le service en profondeur :

* Consultez les [informations de tarification relatives à Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Consultez notre [exemples de code](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) pour Azure Active Directory B2C. 
* Pour obtenir de l’aide sur Stack Overflow, utilisez la balise [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Faites-nous part de vos commentaires à l’aide de [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c), votre avis nous intéresse !
* Consultez [l’article de référence sur les protocoles Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Consultez [l’article de référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Consultez les [Forums Aux Questions sur Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Dépôt de demandes de support pour Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

