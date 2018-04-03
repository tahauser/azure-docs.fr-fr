---
title: Azure Active Directory pour les développeurs | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la connexion des comptes professionnels et scolaires Microsoft à l’aide de Azure Active Directory.
services: active-directory
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 8d70f36c5e434a26fce4d6b4bd1ddefc22234ab5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory pour les développeurs
Azure Active Directory (Azure AD) est un service d’identité de cloud qui permet aux développeurs de créer des applications qui connectent en toute sécurité les utilisateurs disposant d’un compte Microsoft professionnel ou scolaire. Azure AD prend en charge les développeurs qui créent des applications à locataire unique et métiers, ainsi que les développeurs qui souhaitent développer des applications multi-locataires. En plus de la connexion basique, Azure AD permet également aux applications d’appeler des API Microsoft comme [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) et des API personnalisées reposant sur la plateforme Azure AD.  Cette documentation explique comment ajouter une prise en charge Azure AD à votre application par le biais de protocoles standard du secteur, comme OAuth 2.0 et OpenID Connect. 

> [!NOTE]
> La plupart du contenu de cette page se concentre sur le point de terminaison Azure AD v1, qui prend uniquement en charge les comptes Microsoft professionnels ou scolaires. Si vous voulez vous connecter à des comptes Microsoft consommateurs ou personnels, consultez plus d’informations sur le [point de terminaison Azure AD v2.0](active-directory-appmodel-v2-overview.md). Le point de terminaison Azure AD v2.0 offre une expérience de développement unifiée pour les applications devant connecter des utilisateurs avec des comptes Azure AD (professionnels et scolaires) et des comptes Microsoft personnels. 

| | |
| --- | --- |
|[Principes fondamentaux de l’authentification](active-directory-authentication-scenarios.md) | Introduction à l’authentification avec Azure AD. |
|[Types d’applications](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Vue d’ensemble des scénarios d’authentification pris en charge par Azure AD. |                                
                                                                              
## <a name="get-started"></a>Prise en main
Les indications de configuration ci-dessous vous guident pas à pas dans les différentes étapes de création d’une application sur votre plateforme préférée à l’aide du Kit de développement logiciel (SDK) de la bibliothèque d’authentification Azure Active Directory (ADAL). Si vous recherchez des informations à l’aide de la bibliothèque d’authentification Microsoft (MSAL), consultez la documentation sur le [point de terminaison Azure AD v2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Applications de bureau et mobiles](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Applications de bureau et mobiles</center> | [Vue d’ensemble](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Applications Web](./media/active-directory-developers-guide/Web_app.png)<br />Applications Web</center> | [Vue d’ensemble](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![Applications à page unique](./media/active-directory-developers-guide/SPA.png)<br />Applications à page unique</center> | [Vue d’ensemble](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![API Web](./media/active-directory-developers-guide/Web_API.png)<br />API Web</center> | [Vue d’ensemble](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.JS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![De service à service](./media/active-directory-developers-guide/Service_App.png)<br />De service à service</center> | [Vue d’ensemble](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>Procédures
Les indications ci-dessous vous guident pas à pas dans l’exécution des tâches les plus courantes dans Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Inscription de l’application](active-directory-integrating-applications.md)           | Comment inscrire une application dans Azure AD. |
|[Applications multilocataires](active-directory-devhowto-multi-tenant-overview.md)    | Comment connecter n’importe quel compte professionnel Microsoft. |
|[Protocoles OAuth et OpenID Connect](active-directory-protocols-openid-connect-code.md)| Comment connecter des utilisateurs et appeler des API web en utilisant les protocoles d’authentification de Microsoft. |
|[Guides supplémentaires](active-directory-developers-guide-index.md#guides)        |  Une liste des guides disponibles pour Azure AD.   |

## <a name="reference-topics"></a>Rubriques de référence
Les articles suivants fournissent des informations détaillées sur les API, les messages de protocole et les termes utilisés dans Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliothèques d’authentification](active-directory-authentication-libraries.md)   | Vue d’ensemble des bibliothèques et des kits de développement logiciel fournis par Azure AD. |
| [Exemples de code](active-directory-code-samples.md)                                  | Une liste de tous les exemples de code Azure AD. |
| [Glossaire](active-directory-dev-glossary.md)                                      | Terminologie et définitions des termes utilisés dans cette documentation. |
| [Rubriques de référence supplémentaires](active-directory-developers-guide-index.md#reference)| Une liste des rubriques de référence disponibles pour Azure AD.   |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
