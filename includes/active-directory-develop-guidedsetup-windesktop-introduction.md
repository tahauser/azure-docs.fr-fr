# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Appeler l’API Microsoft Graph à partir d’une application de bureau Windows

Ce guide explique comment une application de bureau Windows .NET (XAML) native peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.

À la fin de ce guide, votre application pourra appeler une API protégée à l’aide de comptes personnels (y compris outlook.com et live.com), ainsi que de comptes professionnels et scolaires de n’importe quelle société ou organisation qui possède Azure Active Directory.  

> Ce guide requiert Visual Studio 2015 Update 3 ou Visual Studio 2017.  Ni l’un, ni l’autre ne sont installés sur votre ordinateur ? [Téléchargez gratuitement Visual Studio 2017](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de ce guide](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

L’exemple d’application créé dans le cadre de ce guide permet à une application de bureau Windows d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons à partir d’un point de terminaison Azure Active Directory v2. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestion de l’acquisition de jetons pour accéder à des API web protégées

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un jeton qui peut être utilisé pour interroger l’API Microsoft Graph ou une API web sécurisée par Microsoft Azure Active Directory v2.

Les API telles que Microsoft Graph requièrent un jeton d’accès pour autoriser l’accès à des ressources spécifiques, par exemple pour lire le profil d’un utilisateur, accéder au calendrier de l’utilisateur ou envoyer un courrier électronique. Votre application peut demander un jeton d’accès à l’aide de MSAL pour accéder à ces ressources en spécifiant les étendues d’API. Ce jeton d’accès est ensuite ajouté à l’en-tête d’autorisation HTTP pour chaque appel effectué sur la ressource protégée. 

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.


### <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise les packages NuGet suivants :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Bibliothèque d’authentification Microsoft (MSAL)|

