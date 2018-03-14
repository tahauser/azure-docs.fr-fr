---
title: "Guide du développeur pour l’accès conditionnel à Azure Active Directory"
description: "Guide du développeur et scénarios pour l’accès conditionnel à Azure AD"
services: active-directory
keywords: 
author: danieldobalian
manager: mtillman
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 8ad1ed1b8a178cb8906e6233e6bd78f50d01c50c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Guide du développeur pour l’accès conditionnel à Azure Active Directory

Azure Active Directory (AD) offre plusieurs moyens de sécuriser votre application et de protéger un service.  Une de ces fonctionnalités uniques est l’accès conditionnel.  L’accès conditionnel permet aux développeurs et aux clients d’entreprise de protéger les services dans une multitude de façons, notamment :

* Authentification multifacteur
* Autoriser uniquement les appareils inscrits sur Intune inscrit pour accéder aux services spécifiques
* Restriction des plages IP et emplacements utilisateur

Pour plus d’informations sur toutes les fonctionnalités de l’accès conditionnel, consultez [Accès conditionnel dans Azure Directory](../active-directory-conditional-access-azure-portal.md). 

Dans cet article, nous nous concentrons sur ce que l’accès conditionnel permet aux développeurs de générer des applications pour Azure AD.  Il suppose des connaissances [d’applications uniques](active-directory-integrating-applications.md) et [mutualisées](active-directory-devhowto-multi-tenant-overview.md) et [des modèles courants d’authentification](active-directory-authentication-scenarios.md).

Nous allons approfondir l’impact de l’accès aux ressources que vous ne contrôlez pas et qui peuvent avoir des stratégies d’accès conditionnelles appliquées.  En outre, nous explorons les implications de l’accès conditionnel dans les applications web et de flux « Pour le compte de » accédant à Microsoft Graph et appelant des API.

## <a name="how-does-conditional-access-impact-an-app"></a>Comment l’accès conditionnel impacte-t-il une application ?

### <a name="app-types-impacted"></a>Incidence sur les types d’application

Dans les scénarios les plus courants, l’accès conditionnel ne modifie pas le comportement d’une application ou nécessite des modifications de la part du développeur.  Uniquement dans certains cas, lorsqu’une application en mode silencieux ou indirectement demande un jeton pour un service, une application requiert des modifications du code pour gérer des « défis » d’accès conditionnel.  Cela peut être aussi simple que l’exécution d’une demande de connexion interactive. 

Plus précisément, les scénarios suivants requièrent un code pour gérer des « défis » d’accès conditionnel : 

* Applications qui accèdent à Microsoft Graph
* Applications effectuant le flux Pour le compte de
* Applications accédant à plusieurs services/ressources
* Applications à page unique en utilisant ADAL.js
* Les applications appelant une ressource

Les stratégies d’accès conditionnel peuvent être appliquées à l’application, mais peuvent également être appliquées à une API web à laquelle votre application a accès. Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Bien démarré avec l’accès conditionnel Azure Active Directory](../active-directory-conditional-access-azure-portal-get-started.md).

Selon le scénario, un client d’entreprise peut appliquer et supprimer des stratégies d’accès conditionnel à tout moment.  Afin que votre application continue à fonctionner correctement lorsqu’une nouvelle stratégie est appliquée, vous devez mettre en œuvre la gestion de « défi ». Les exemples suivants illustrent la gestion des défis. 

### <a name="conditional-access-examples"></a>Exemples d’accès conditionnel

Certains scénarios requièrent des modifications de code pour gérer l’accès conditionnel, tandis que d’autres travaillent tel quel.  Voici quelques scénarios utilisant l’accès conditionnel pour l’authentification multifacteur qui donne une idée de la différence.

* Vous générez une application iOS de client unique et appliquez une stratégie d’accès conditionnel.  L’application connecte un utilisateur et ne demande pas l’accès à une API.  Lorsque l’utilisateur se connecte, la stratégie est appelée automatiquement et l’utilisateur doit effectuer l’authentification multifacteur (MFA). 
* Vous générez une application web multi-locataire qui utilise Microsoft Graph pour accéder à Exchange, entre autres services.  Un client d’entreprise qui adopte cette application définit une stratégie sur Exchange.  Lorsque l’application web demande un jeton pour MS Graph, il ne sera pas demandé à l’application de se conformer à la stratégie.  L’utilisateur final est connecté avec des jetons valides. Lorsque l’application tente d’utiliser ce jeton avec Microsoft Graph pour accéder aux données Exchange, un « défi » de revendication est renvoyé à l’application web via l’en-tête ```WWW-Authenticate```.  L’application peut ensuite utiliser le ```claims``` dans une nouvelle demande, et l’utilisateur final sera invité à respecter les conditions. 
* Vous générez une application native qui utilise un service de niveau intermédiaire pour accéder à une API en aval.  Un client d’entreprise de la société utilisant cette application applique une stratégie à l’API en aval.  Lorsqu’un utilisateur final se connecte, l’application native demande l’accès au niveau intermédiaire et envoie le jeton.  Le niveau intermédiaire effectue le flux Pour le compte pour demander l’accès à l’API en aval.  À ce stade, un défi de « revendications » est présenté au niveau intermédiaire. Le niveau intermédiaire renvoie la demande à l’application native, qui doit se conformer à la stratégie d’accès conditionnel.

### <a name="complying-with-a-conditional-access-policy"></a>Conformité à une stratégie d’accès conditionnel

Pour différentes topologies d’applications, une stratégie d’accès conditionnel est évaluée lorsque la session est établie.  Étant donné qu’une stratégie d’accès conditionnel fonctionne sur la granularité des applications et des services, le point sur lequel elle est appelée dépend essentiellement du scénario que vous essayez d’accomplir.

Lorsque votre application tente d’accéder à un service avec une stratégie d’accès conditionnel, elle peut rencontrer un défi d’accès conditionnel.  Ce défi est encodé dans le paramètre `claims` qui est fourni dans une réponse d’Azure AD ou sur Microsoft Graph.  Voici un exemple de ce paramètre de défi : 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Les développeurs peuvent prendre ce défi et l’ajouter à une nouvelle demande à Azure AD.  Passer cet état invite l’utilisateur final à exécuter toute action nécessaire pour se conformer à la stratégie d’accès conditionnel. Les scénarios suivants expliquent les détails de l’erreur et le mode d’extraction du paramètre. 

## <a name="scenarios"></a>Scénarios

### <a name="prerequisites"></a>Prérequis

L’accès conditionnel Azure AD est une fonctionnalité inclue dans [Azure AD Premium](../active-directory-whatis.md#choose-an-edition).  Pour en savoir plus sur les conditions requises de licence, consultez le [Rapport d’utilisation sans licence](../active-directory-conditional-access-unlicensed-usage-report.md).  Les développeurs peuvent joindre le [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), qui inclut un abonnement gratuit à la Suite de mobilité d’entreprise y compris Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considérations pour des scénarios spécifiques

Les informations suivantes s’appliquent uniquement dans ces scénarios d’accès conditionnel :

* Applications qui accèdent à Microsoft Graph
* Applications effectuant le flux Pour le compte de
* Applications accédant à plusieurs services/ressources
* Applications à page unique en utilisant ADAL.js

Dans les sections suivantes, nous aborderons des scénarios communs plus complexes.  Le principe de fonctionnement central est que les stratégies d’accès conditionnel sont évaluées au moment où le jeton est demandé pour le service qui contient une stratégie d’accès conditionnel, sauf si elle est accessible via Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scénario : application ayant accès à Microsoft Graph

Dans ce scénario, nous abordons le cas où une application web demande l’accès à Microsoft Graph. Dans ce cas, la stratégie d’accès conditionnel peut être attribuée à SharePoint, Exchange ou à un autre service accessible en tant que charge de travail via Microsoft Graph.  Dans cet exemple, supposons qu’il existe une stratégie d’accès conditionnel sur Sharepoint Online.

![Applications ayant accès au diagramme de flux Microsoft Graph](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

L’application demande tout d’abord une autorisation à Microsoft Graph qui nécessite l’accès à une charge de travail en aval sans accès conditionnel.  La demande réussit sans appeler de stratégie et l’application reçoit les jetons pour Microsoft Graph.  À ce stade, l’application peut utiliser le jeton d’accès dans une demande du porteur pour le point de terminaison demandé. À présent, l’application doit accéder à un point de terminaison Sharepoint Online de Microsoft Graph, par exemple : `https://graph.microsoft.com/v1.0/me/mySite`

L’application a déjà un jeton valide pour Microsoft Graph, afin d’exécuter la nouvelle demande sans émettre de nouveau jeton. Cette demande échoue et un défi de revendications est émis depuis Microsoft Graph sous la forme d’un HTTP 403 Interdit avec un défi ```WWW-Authenticate```.
Voici un exemple de la réponse : 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

Le défi de revendications se trouve dans l’en-tête ```WWW-Authenticate``` qui peut être analysée pour extraire le paramètre des revendications pour la demande suivante.  Une fois qu’il est ajouté à la nouvelle demande, Azure AD sait évaluer la stratégie d’accès conditionnel pour se connecter à l’utilisateur et l’application est maintenant conforme à la stratégie d’accès conditionnel.  La répétition de la demande au point de terminaison Sharepoint Online réussit.

L’en-tête ```WWW-Authenticate``` possède une structure unique et il n’est pas sans importance de l’analyser afin d’extraire des valeurs.  Voici une courte méthode pour aider.

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

Pour plus d’exemples de code qui montrent comment gérer le défi de revendications, consultez [l’exemple de code Pour le compte de](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) pour .NET ADAL.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scénario : applications effectuant le flux Pour le compte de

Dans ce scénario, nous abordons le cas dans lequel une application native appelle une API/ service Web.  À son tour, ce service exécute [le flux « Pour le compte de »](active-directory-authentication-scenarios.md#application-types-and-scenarios) pour appeler un service en aval.  Dans notre cas, nous avons appliqué notre stratégie d’accès conditionnel pour le service en aval (API Web 2) et nous utilisons une application native plutôt qu’une application démon/serveur. 

![Application effectuant le diagramme de flux Pour le compte](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

La demande de jeton initiale pour l’API Web 1 n’affiche aucune invite pour l’utilisateur final pour l’authentification multifacteur car l’API Web 1 ne peut pas toujours atteindre l’API en aval.  Une fois que l’API Web 1 tente de demander un jeton flux Pour le compte de l’utilisateur pour l’API Web 2, la demande échoue car l’utilisateur ne s’est pas connecté avec l’authentification multifacteur.

Azure AD renvoie une réponse HTTP avec des données intéressantes : 

> [!NOTE]
> Dans cette instance, il s’agit d’une description d’erreur de l’authentification multifacteur, mais il existe une large gamme de `interaction_required` possible concernant l’accès conditionnel.  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Dans notre API Web 1, nous interceptons l’erreur `error=interaction_required` et renvoyons le défi `claims` à l’application de bureau.  À ce stade, l’application de bureau peut effectuer un nouvel appel `acquireToken()` et ajouter le défi `claims` comme un paramètre de chaîne de requêtes supplémentaire.  Cette nouvelle demande oblige l’utilisateur à effectuer une authentification multifacteur et à renvoyer ce nouveau jeton à l’API Web 1, puis exécuter le flux Pour le compte de.

Pour tester ce scénario, consultez notre [exemple de code .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Il indique comment repasser le défi de revendications à partir d’API Web 1 vers l’application native et construire une nouvelle demande à l’intérieur de l’application cliente. 

## <a name="scenario-app-accessing-multiple-services"></a>Scénario : applications accédant à plusieurs services

Dans ce scénario, nous abordons le cas dans lequel une application Web accède aux deux services, dont un d'entre eux est affecté à une stratégie d’accès conditionnel.  En fonction de votre logique d’application, il peut exister un chemin d’accès dans lequel votre application ne nécessite pas l’accès aux deux services Web.  Dans ce scénario, l’ordre dans lequel vous demandez un jeton joue un rôle important dans l’expérience de l’utilisateur final.

Supposons que nous disposons d’un service Web A et B et que le service Web B applique notre stratégie d’accès conditionnel.  Tandis que la demande initiale d’authentification interactive requiert le consentement pour les deux services, la stratégie d’accès conditionnel n’est pas requise dans tous les cas.  Si l’application demande un jeton pour le service Web B, la stratégie est appelée et les demandes ultérieures pour le service Web A réussissent également comme suit.

![Application accédant à un diagramme de flux multi-services](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Sinon, si l’application demande initialement un jeton pour le service Web A, l’utilisateur final n’appelle pas la stratégie d’accès conditionnel.  Cela permet au développeur d’applications de contrôler l’expérience de l’utilisateur final et de ne forcer la stratégie d’accès conditionnel à être appelée dans tous les cas. Cela se complique si l’application demande ensuite un jeton pour le service Web B. À ce stade, l’utilisateur final doit se conformer à la stratégie d’accès conditionnel.  Lorsque l’application tente de `acquireToken`, cela peut générer l’erreur suivante (illustrée dans le diagramme suivant) : 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Application accédant à plusieurs services demandant un nouveau jeton](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Si l’application utilise la bibliothèque ADAL, un échec d’acquisition du jeton est toujours retenté interactivement.  Lorsque cette demande interactive se produit, l’utilisateur final a la possibilité de se conformer à l’accès conditionnel.  Cela est vrai, sauf si la demande est un `AcquireTokenSilentAsync` ou `PromptBehavior.Never` et dans ce cas, l’application doit effectuer une demande interactive ```AcquireToken``` pour permettre à l’utilisation de fin de se conformer à la stratégie. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scénario : application à page unique (SPA) en utilisant ADAL.js

Dans ce scénario, nous abordons le cas où nous disposons d’une application à page unique (SPA), en utilisant ADAL.js pour appeler une API Web protégée par l’accès conditionnel.  Il s’agit d’une architecture simple mais avec des nuances qui doivent être prises en compte lors du développement autour de l’accès conditionnel.

Dans ADAL.js, il existe quelques fonctions qui obtiennent des jetons : `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, et `acquireTokenRedirect(…)`. 

* `login()`Obtient un jeton d’ID via une demande de connexion interactive, mais n’obtient pas les jetons d’accès pour n’importe quel service (y compris une API Web protégée par l’accès conditionnel).  
* `acquireToken(…)` peut ensuite être utilisé pour obtenir silencieusement un jeton d’accès c'est-à-dire qu’il n’affiche pas l’interface utilisateur dans tous les cas.  
* `acquireTokenPopup(…)` et `acquireTokenRedirect(…)` sont tous deux utilisés pour demander interactivement un jeton pour une ressource, ce qui signifie qu’ils affichent toujours l’interface utilisateur de connexion.

Lorsqu’une application a besoin d’un jeton d’accès pour appeler une API Web, elle tente une `acquireToken(…)`.  Si la session du jeton a expiré ou si nous devons nous conformer à une stratégie d’accès conditionnel, la fonction *acquireToken* échoue et l’application utilise `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Application à page unique en utilisant le diagramme de flux ADAL](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Examinons un exemple avec notre scénario d’accès conditionnel.  L’utilisateur final accède simplement au site et n’a pas de session.  Nous effectuons un appel `login()` et obtenons un jeton d’ID sans authentification multifacteur.  Puis l’utilisateur final appuie sur un bouton qui exige que l’application demande des données depuis une API Web.  L’application tente d’effectuer un appel `acquireToken()` mais échoue, étant donné que l’utilisateur n’a pas encore effectué l’authentification multifacteur et doit se conformer à la stratégie d’accès conditionnel.

Azure AD renvoie la réponse HTTP suivante : 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Notre application a besoin d’intercepter le `error=interaction_required`.  L’application peut alors utiliser `acquireTokenPopup()` ou `acquireTokenRedirect()` sur la même ressource.  L’utilisateur est obligé d’effectuer une authentification multifacteur. Une fois que l’utilisateur a terminé l’authentification multifacteur, l’application émet un nouveau jeton d’accès pour la ressource demandée.

Pour tester ce scénario, consultez notre [exemple de code Pour le compte de SPA JS](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Cet exemple de code utilise la stratégie d’accès conditionnel et l’API Web précédemment inscrites avec un SPA JS pour illustrer ce scénario. Il explique comment gérer correctement le défi de revendications et obtenir un jeton d’accès qui peut être utilisé pour votre API Web. Vous pouvez également extraire [l’exemple de code Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) général pour obtenir des conseils sur une SPA Angular


## <a name="see-also"></a>Voir aussi

* Vous pouvez en savoir plus sur les fonctionnalités, consultez [Accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Pour obtenir plus d’exemples de code Azure AD, consultez [Référentiel Github d’exemples de code](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Pour plus d’informations sur les kits SDK ADAL et l’accès à la documentation de référence, consultez [guide des bibliothèques](active-directory-authentication-libraries.md).
* Pour en savoir plus sur les scénarios multi-locataire, consultez [comment connecter les utilisateurs à l’aide du modèle multi-locataire](active-directory-devhowto-multi-tenant-overview.md).
