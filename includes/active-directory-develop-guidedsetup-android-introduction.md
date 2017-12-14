
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Appeler l’API Microsoft Graph à partir d’une application Android

Ce guide explique comment une application Android native peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.

À la fin de ce guide, votre application pourra appeler une API protégée à l’aide de comptes personnels (y compris outlook.com et live.com), ainsi que de comptes professionnels et scolaires de n’importe quelle société ou organisation qui possède Azure Active Directory.  

### <a name="how-this-sample-works"></a>Fonctionnement de cet exemple
![Fonctionnement de cet exemple](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

L’exemple créé par ce guide est basé sur un scénario dans lequel une application Android est utilisée pour interroger une API web qui accepte des jetons provenant du point de terminaison Azure Active Directory v2, dans ce cas l’API Microsoft Graph. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).

### <a name="pre-requisites"></a>Conditions préalables
* Cette installation guidée se concentre sur Android Studio, mais n’importe quel autre environnement de développement d’application Android peut être utilisé. 
* Le Kit de développement logiciel (SDK) Android 21 ou version ultérieure est requis (Kit de développement logiciel (SDK) 25 recommandé).
* Google Chrome ou un navigateur web utilisant les onglets personnalisés est requis pour cette version de Microsoft Authentication Library (MSAL) pour Android.

> Remarque : Google Chrome n’est pas inclus dans l’émulateur Visual Studio pour Android. Nous vous recommandons de tester ce code sur un émulateur avec l’API 25 ou sur une image avec l’API 21 ou version ultérieure disposant de Google Chrome.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Gérer l’acquisition de jetons pour accéder à une API web protégée

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un jeton qui peut être utilisé pour interroger l’API Microsoft Graph ou une API web sécurisée par Microsoft Azure Active Directory v2.

Les API telles que Microsoft Graph requièrent un jeton d’accès pour autoriser l’accès à des ressources spécifiques, par exemple pour lire le profil d’un utilisateur, accéder au calendrier de l’utilisateur ou envoyer un courrier électronique. Votre application peut demander un jeton d’accès à l’aide de MSAL pour accéder à ces ressources en spécifiant les étendues d’API. Ce jeton d’accès est ensuite ajouté à l’en-tête d’autorisation HTTP pour chaque appel effectué sur la ressource protégée. 

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.

### <a name="libraries"></a>Bibliothèques

Ce guide utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Bibliothèque d’authentification Microsoft (MSAL)|
