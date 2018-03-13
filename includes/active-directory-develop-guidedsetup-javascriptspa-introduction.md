# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Appeler l’API Microsoft Graph à partir d’une application à page unique (SPA) JavaScript

Ce guide explique comment une application à page unique JavaScript peut permettre l’identification de comptes personnels, scolaires et professionnels, peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.

### <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de l’exemple d’application de ce guide](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

L’exemple d’application créé dans le cadre de ce guide permet à une application SPA JavaScript d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons à partir d’un point de terminaison Azure Active Directory v2. Pour ce scénario, une fois l’utilisateur authentifié, un jeton d’accès est demandé et ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliothèques

Ce guide utilise la bibliothèque suivante :

|Bibliothèque|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Bibliothèque d’authentification Microsoft pour JavaScript Preview|

> [!NOTE]
> *msal.js* cible le *point de terminaison Azure Active Directory v2*, ce qui permet aux comptes personnels, scolaires et professionnels de se connecter et d’acquérir des jetons. Le *point de terminaison Azure Active Directory v2* a [certaines limitations](..\articles\active-directory\develop\active-directory-v2-limitations.md). Si vous êtes uniquement intéressé par les comptes scolaires et professionnels, utilisez *adal.js* et le *point de terminaison v1*. Pour comprendre les différences entre les points de terminaison v1 et v2, lisez la [comparaison v1-v2](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
