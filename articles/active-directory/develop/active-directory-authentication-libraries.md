---
title: "Bibliothèques d’authentification Azure Active Directory | Microsoft Docs"
description: "La bibliothèque d'authentification Azure AD (ADAL) permet aux développeurs d’applications clientes d’authentifier facilement les utilisateurs sur Active Directory (AD) en local ou sur le cloud, puis d'obtenir des jetons d'accès pour sécuriser les appels d'API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: f017e3d323b98660fdee902770652b3165e70e5e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliothèques d’authentification d’Azure Active Directory
La bibliothèque d’authentification Azure Active Directory (ADAL) permet aux développeurs d’applications d’authentifier les utilisateurs dans une instance locale d’Active Directory (AD) ou dans le cloud, puis d’obtenir des jetons pour sécuriser les appels d’API. La bibliothèque ADAL facilite l’authentification pour les développeurs grâce à des fonctionnalités comme :
 - Un cache de jeton configurable qui stocke les jetons d’accès et les jetons d’actualisation
 - L’actualisation automatique des jetons lorsqu’un jeton d’accès expire et qu’un jeton d’actualisation est disponible
 - La prise en charge des appels de méthode asynchrones
 - Et bien plus

> [!NOTE]
> Vous recherchez les bibliothèques Azure AD v2.0 (MSAL) ? Consultez le [guide des bibliothèques MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries). 
> 
> 

### <a name="client-libraries"></a>Bibliothèques clientes

| Plateforme | Bibliothèque | Télécharger | Code source | Exemple | Référence
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS et Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Application de bureau](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Référence](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| .NET Client, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Application de bureau](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Application à page unique](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Application iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Référence](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[Référentiel Central](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Application Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Applications web Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>Bibliothèques serveur 

| Plateforme | Bibliothèque | Télécharger | Code source | Exemple | Référence
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pour Azure AD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Application MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pour OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Application web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN pour WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Application Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensions de protocole d’identité pour .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Gestionnaire JWT pour .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Bibliothèques clientes v2.0 (MSAL)

Le [point de terminaison Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) combine Azure AD et des comptes Microsoft derrière un seul point de terminaison. Pour accéder à ce point de terminaison, les développeurs peuvent utiliser les [bibliothèques MSAL en préversion prises en charge pour la production](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) au lieu de la bibliothèque ADAL.

| Plateforme | Bibliothèque | Télécharger | Code source | Exemple | Référence
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS et Android |MSAL pour .NET (préversion) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Application de bureau](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Référence](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL pour JavaScript (préversion) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Application à page unique](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Référence](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL pour iOS (préversion) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Application iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Référence](https://azuread.github.io/docs/objc/) |
| Android |MSAL pour Android (préversion) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Application Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Référence](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Scénarios

Voici trois scénarios courants dans lesquels ADAL peut être utilisé pour authentifier un utilisateur qui accède à une ressource distante :  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Authentification des utilisateurs d’une application cliente native exécutée sur un appareil 

Dans ce scénario, un développeur dispose d’une application cliente WPF, qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le locataire Azure AD qu’utilise l’API web. Par conséquent, il peut utiliser la bibliothèque ADAL pour faciliter l'authentification avec Azure AD, soit en déléguant complètement l'expérience d'authentification à ADAL, soit en gérant de manière explicite les informations d'identification de l'utilisateur. La bibliothèque ADAL facilite l’authentification de l'utilisateur, l’obtention d’un jeton d'accès et d’un jeton d'actualisation d'Azure AD, puis l’utilisation du jeton d'accès pour effectuer des requêtes à l'API web.

Pour voir un exemple de code qui illustre ce scénario en utilisant l’authentification auprès d’Azure AD, consultez la section [Native Client WPF Application to Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Authentification d’une application cliente confidentielle exécutée sur un serveur web

Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. Il dispose d’un abonnement Azure, sait comment appeler le service en aval et connaît le locataire Azure AD qu’utilise l’API web. Par conséquent, il peut utiliser la bibliothèque ADAL pour faciliter l'authentification avec Azure AD en gérant de manière explicite les informations d'identification de l'application. La bibliothèque ADAL facilite la récupération d’un jeton d'Azure AD à l'aide d'informations d'identification client de l'application, puis l’utilisation de ce jeton pour faire des requêtes à l'API web. La bibliothèque ADAL gère également la durée de vie du jeton d'accès en le mettant en cache et en le renouvelant si nécessaire. Pour voir un exemple de code qui illustre ce scénario, consultez [Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Authentification d’une application cliente confidentielle exécutée sur un serveur, pour le compte d’un utilisateur 

Dans ce scénario, un développeur a une application fonctionnant sur un serveur qui doit accéder à une ressource distante sécurisée par Azure AD, par exemple une API web. La demande doit également être effectuée pour le compte d’un utilisateur d’Azure AD. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le locataire Azure AD qu’utilise le service. Une fois l'utilisateur authentifié sur l'application web, l'application peut obtenir d’Azure AD un code d'autorisation pour l'utilisateur. L'application web peut ensuite utiliser ADAL pour obtenir d’Azure AD un jeton d'accès et un jeton d’actualisation pour le compte de l'utilisateur en utilisant le code d'autorisation et les informations d’identification client associés à l'application. Une fois l'application web en possession du jeton d'accès, elle peut appeler l'API web jusqu’à expiration du jeton. Lorsque le jeton expire, l'application web peut utiliser la bibliothèque ADAL pour obtenir un nouveau jeton d'accès en utilisant le jeton d’actualisation reçu précédemment. Pour voir un exemple de code qui illustre ce scénario, consultez [Native client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Voir aussi

- [Le guide du développeur Azure Active Directory](active-directory-developers-guide.md)
- [Scénarios d’authentification pour Azure Active Directory](active-directory-authentication-scenarios.md)
- [Exemples de code Azure Active Directory](active-directory-code-samples.md)
