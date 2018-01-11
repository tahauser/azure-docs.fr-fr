
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Appeler l’API Microsoft Graph à partir d’une application iOS

Ce guide vous explique comment une application iOS native (Swift) peut appeler les API qui requièrent des jetons d’accès provenant du point de terminaison Microsoft Azure Active Directory (Azure AD) v2.0. Le guide explique comment obtenir des jetons d’accès et les utiliser pour appeler l’API Microsoft Graph ainsi que d’autres API.

Lorsque vous aurez effectué les exercices de ce guide, votre application pourra appeler l’API protégée d’une entreprise ou d’une organisation qui dispose d’Azure AD. Votre application peut appeler des API protégées à l’aide de comptes personnels comme outlook.com, live.com et autres, et de comptes professionnels ou scolaires.

## <a name="prerequisites"></a>Composants requis
- XCode version 8.x est requis pour l’exemple à créer dans ce guide. Vous pouvez télécharger XCode sur le [site web d’iTunes ](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode Download URL").
- Vous aurez besoin du gestionnaire de dépendances [Carthage](https://github.com/Carthage/Carthage) pour la gestion des packages.

## <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de ce guide](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

L’exemple d’application créé dans le cadre de ce guide permet à une application iOS d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons provenant d’un point de terminaison Azure AD v2.0. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’**autorisation**. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Gérer l’acquisition de jetons pour accéder à des API web protégées

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un jeton. Le jeton est utilisé pour interroger l’API Microsoft Graph ou une API web sécurisée par le point de terminaison Azure AD v2.0.

Avec les API comme Microsoft Graph, vous avez besoin d’un jeton d’accès pour autoriser l’accès à des ressources spécifiques. Les jetons sont nécessaires pour lire le profil d’un utilisateur, accéder au calendrier d’un utilisateur, envoyer un e-mail, etc. Votre application peut demander un jeton d’accès à l’aide de la bibliothèque MSAL en spécifiant les étendues de l’API. Le jeton d’accès est ajouté à l’en-tête d’**autorisation** HTTP pour chaque appel effectué sur la ressource protégée.

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.


## <a name="libraries"></a>Bibliothèques

Ce guide utilise la bibliothèque suivante :

|Bibliothèque|Description|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Préversion de la bibliothèque d’authentification Microsoft pour iOS|

