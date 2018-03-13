---
title: "Protéger un serveur d’API Web back-end avec Azure Active Directory et Gestion des API | Microsoft Docs"
description: "Découvrez comment protéger un serveur principal d’API web avec Azure Active Directory et Gestion des API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 2a5be24aba8a675290045b282cc64dda4b7c594e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Protection d’un serveur principal d’API web avec Azure Active Directory et Gestion des API

Cette rubrique montre comment générer un backend d’API web et le protéger à l’aide du protocole OAuth 2.0 avec Azure Active Directory et Gestion des API.  

## <a name="create-an-azure-ad-directory"></a>Création d’un répertoire Azure AD
Pour protéger votre backend d’API web à l’aide d’Azure Active Directory, vous devez d’abord disposer d’un locataire AAD. Pour créer un client AAD, ouvrez une session dans le [portail Azure Classic](https://manage.windowsazure.com), puis cliquez sur **Nouveau**->**App Services**->**Active Directory**->**Répertoire**->**Création personnalisée**. 

![Azure Active Directory][api-management-create-aad-menu]

Dans cet exemple, un répertoire nommé **APIMDemo** est créé avec un domaine par défaut nommé **DemoAPIM.onmicrosoft.com**. 

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Création d’un service API web protégé par Azure Active Directory
Dans cette étape, un serveur principal d’API web est créé à l’aide de Visual Studio 2013. Pour créer un projet d’API web dans Visual Studio, cliquez sur **Fichier**->**Nouveau**->**Projet**, puis choisissez **Application web ASP.NET** à partir de la liste **web** de modèles. 

![Visual Studio][api-management-new-web-app]

Cliquez sur **API web** à partir de **Sélectionner un modèle** pour créer un projet d’API web. Pour configurer l’authentification Azure Active Directory, cliquez sur **Modifier l’authentification**.

![Nouveau projet][api-management-new-project]

Cliquez sur **Comptes professionnels** et spécifiez le **domaine** de votre client AAD. Dans cet exemple, le domaine est **DemoAPIM.onmicrosoft.com**. Le domaine de votre répertoire peut être obtenu à partir de l’onglet **Domaines** de votre répertoire.

![Domaines][api-management-aad-domains]

Configurez les paramètres souhaités dans la boîte de dialogue **Modifier l’authentification** et cliquez sur **OK**.

![Modifier l’authentification][api-management-change-authentication]

Lorsque vous cliquerez sur **OK** , Visual Studio tentera d’inscrire votre application à votre répertoire Azure AD et vous pourrez être invité à vous connecter via Visual Studio. Connectez-vous à l’aide d’un compte administrateur de votre répertoire.

![Connexion à Visual Studio][api-management-sign-in-vidual-studio]

Pour configurer ce projet comme une API web d’Azure, cochez la case **Hôte sur le cloud** puis cliquez sur **OK**.

![Nouveau projet][api-management-new-project-cloud]

Vous pouvez être invité à vous connecter à Azure, et vous pouvez ensuite configurer l’application web.

![Configuration][api-management-configure-web-app]

Dans cet exemple, un nouveau **Plan App Service** nommé **APIMAADDemo** est spécifié.

Cliquez sur **OK** pour configurer l’application web et créer le projet.

## <a name="add-the-code-to-the-web-api-project"></a>Ajout d’un code au projet d’API web

Dans cet exemple, l’API web implémente un service de calcul de base à l’aide d’un modèle et d’un contrôleur. Pour ajouter le modèle de service, cliquez avec le bouton droit sur **Modèles** dans l’**Explorateur de solutions** et choisissez **Ajouter** puis **Classe**. Nommez la classe `CalcInput` , puis cliquez sur **Ajouter**.

Ajoutez l’instruction suivante `using` au début du fichier `CalcInput.cs`.

```csharp
using Newtonsoft.Json;
```

Remplacez la classe générée par le code suivant.

```csharp
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Cliquez droit sur **Contrôleurs** dans l’**Explorateur de solutions** et choisissez **Ajouter**->**Contrôleur**. Choisissez **Contrôleur API 2 web - Vide**, puis cliquez sur **Ajouter**. Saisissez **CalcController** pour le nom de Contrôleur, puis cliquez sur **Ajouter**.

![Ajouter un contrôleur][api-management-add-controller]

Ajoutez l’instruction suivante `using` au début du fichier `CalcController.cs`.

```csharp
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Remplacez la classe de contrôleur générée par le code suivant. Ce code implémente les opérations `Add`, `Subtract`, `Multiply` et `Divide` de l’API de calcul de base.

```csharp
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Appuyez sur **F6** pour générer et vérifier la solution.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Pour publier le projet sur Azure, cliquez droit sur le projet **APIMAADDemo** dans Visual Studio et choisissez **Publier**. Conservez les paramètres par défaut dans la boîte de dialogue **Publier le site web** et cliquez sur **Publier**.

![Publier un site web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Accord d’autorisations à l’application de service du serveur principal Azure AD
Une nouvelle application pour le service principal est créée dans votre répertoire Azure AD dans le cadre du processus de configuration et de publication de votre projet d’API web.

![Application][api-management-aad-backend-app]

Cliquez sur le nom de l’application pour configurer les autorisations requises. Accédez à l’onglet **Configurer** et faites défiler la page jusqu’à la section **Autorisations pour d’autres applications**. Cliquez sur la liste déroulante **Autorisations d’application** en regard de **Windows** **Azure Active Directory**, cochez la case **Lire des données d’annuaire**, puis cliquez sur **Enregistrer**.

![Ajout d’autorisations][api-management-aad-add-permissions]

> [!NOTE]
> Si **Windows** **Azure Active Directory** n’est pas répertorié sous les autorisations d’autres applications, cliquez sur **Ajouter une application** et ajoutez-le à partir de la liste.
> 
> 

Prenez note de l’ **URI ID d’application** , dont vous aurez besoin pour configurer une application Azure AD pour le portail des développeurs Gestion des API lors d’une étape ultérieure.

![URI ID d’application][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importation de l’API dans Gestion des API
Les API sont configurées à partir du portail des éditeurs d’API, accessible par le biais du Portail Azure. Pour y accéder, cliquez sur **Portail des éditeurs** dans la barre d’outils de votre service de gestion des API. Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance] dans le didacticiel [Gérer votre première API][Manage your first API].

![Portail des éditeurs][api-management-management-console]

Il est possible d’ [ajouter manuellement des opérations aux API](api-management-howto-add-operations.md), ou bien de les importer. Dans cette vidéo, à partir de 6’40’’, les opérations sont importées au format Swagger.

Créez un fichier nommé `calcapi.json` avec le contenu suivant et enregistrez-le sur votre ordinateur. Vérifiez que l’attribut `host` pointe vers le serveur principal de votre API web. Dans cet exemple, `"host": "apimaaddemo.azurewebsites.net"` est utilisé.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Pour importer l’API de calculatrice, cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Importer l’API**.

![Bouton Importer l’API][api-management-import-api]

Procédez comme suit pour configurer l’API de calculatrice.

1. Cliquez sur **À partir du fichier**, accédez au fichier `calculator.json` que vous avez enregistré, puis cliquez sur la case d’option **Swagger**.
2. Entrez **calc** dans la zone de texte **Suffixe de l’URL d’API web**.
3. Cliquez dans la zone **Produits (facultatif)** et choisissez **Starter**.
4. Cliquez sur **Enregistrer** pour importer l’API.

![Add new API][api-management-import-new-api]

Une fois l’API importée, la page Résumé de l’API s’affiche dans le portail des éditeurs.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Appel de l’API sans succès à partir du portail de développement
À ce stade, l’API a été importée dans Gestion des API, mais ne peut pas encore être appelée avec succès là partir du portail des développeurs, car le service du serveur principal est protégé par l’authentification Azure AD. 

Cliquez sur **Portail des développeurs** en haut à droite du portail de publication.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API**, puis sur l’API **Calculatrice**.

![Portail des développeurs][api-management-dev-portal-apis]

Cliquez sur **Essayer**.

![Essayer][api-management-dev-portal-try-it]

Cliquez sur **Envoyer** et observez l’état de réponse obtenu, à savoir **401 Non autorisé**.

![Envoyer][api-management-dev-portal-send-401]

La demande n’est pas autorisée, car l’API du serveur principal est protégée par Azure Active Directory. Avant d’appeler correctement l’API, le portail des développeurs doit être configuré afin d’autoriser les développeurs utilisant OAuth 2.0. Ce processus est décrit dans les sections suivantes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Inscription du portail des développeurs en tant qu’application AAD
La première étape de la configuration du portail des développeurs pour autoriser les développeurs utilisant OAuth 2.0 consiste à inscrire le portail des développeurs en tant qu’application AAD. 

Accédez au locataire Azure AD. Dans cet exemple, sélectionnez **APIMDemo** et accédez à l’onglet **Applications**.

![Nouvelle application][api-management-aad-new-application-devportal]

Cliquez sur le bouton **Ajouter** pour créer une application Azure Active Directory, puis choisissez **Ajouter une application développée par mon organisation**.

![Nouvelle application][api-management-new-aad-application-menu]

Choisissez **Application web et/ou API web**, saisissez un nom, puis cliquez sur la flèche Suivant. Dans cet exemple, **APIMDeveloperPortal** est utilisé.

![Nouvelle application][api-management-aad-new-application-devportal-1]

Pour **URL d’authentification**, saisissez l’URL de votre service Gestion des API et ajoutez `/signin`. Dans cet exemple, `https://contoso5.portal.azure-api.net/signin` est utilisé.

Pour **URL d’ID d’application**, saisissez l’URL de votre service Gestion des API et ajoutez quelques caractères uniques. Il peut s’agir des caractères de votre choix. Dans cet exemple, `https://contoso5.portal.azure-api.net/dp` est utilisé. Lorsque les **Propriétés de l’application** sont configurées, cliquez sur la coche pour créer l’application.

![Nouvelle application][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configuration du serveur d’autorisation OAuth 2.0 dans Gestion des API
La prochaine étape consiste à configurer le serveur d’autorisation OAuth 2.0 dans Gestion des API 

Cliquez sur **Sécurité** dans le menu Gestion des API de gauche, cliquez sur **OAuth 2.0**, puis cliquez sur **Ajouter au serveur d’autorisation**.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server]

Entrez un nom et une description facultative dans les champs **Nom** et **Description**. Ces champs sont utilisés pour identifier le serveur d’autorisation OAuth 2.0 dans l’instance de service Gestion des API. Dans cet exemple, **Démonstration de serveur d’autorisation** est utilisé. Ensuite, lorsque vous spécifierez un serveur OAuth 2.0 à utiliser pour l’authentification pour une API, vous allez sélectionner ce nom.

Pour l’**URL de la page d’enregistrement client**, saisissez une valeur d’espace réservé telle que `http://localhost`.  L’ **URL de la page d’enregistrement client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs OAuth 2.0 qui prennent en charge la gestion des comptes utilisateur. Dans cet exemple, les utilisateurs ne peuvent pas créer et configurer leurs propres comptes. Un espace réservé est donc utilisé.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-1]

Ensuite, spécifiez l’**URL de point de terminaison d’autorisation** et l’**URL de point de terminaison de jeton**.

![Serveur d’autorisation][api-management-add-authorization-server-1a]

Ces valeurs peuvent être récupérées à partir de la page **Points de terminaison d’app** de l’application AAD que vous avez créée pour le portail des développeurs. Pour accéder aux points de terminaison, accédez à l’onglet **Configurer** pour l’application AAD et cliquez sur **Afficher les points de terminaison**.

![Application][api-management-aad-devportal-application]

![Afficher les points de terminaison][api-management-aad-view-endpoints]

Copiez la valeur de **Point de terminaison d’autorisation OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison d’autorisation**.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-2]

Copiez la valeur de **Point de terminaison de jeton OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison de jeton**.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-2a]

En plus d’effectuer un collage dans le point de terminaison de jeton, ajoutez un paramètre de corps supplémentaire nommé **ressource** et pour la valeur, utilisez l’**URI ID d’application** de l’application AAD pour le service principal qui a été créé lors de la publication du projet Visual Studio.

![URI ID d’application][api-management-aad-sso-uri]

Ensuite, spécifiez les informations d’identification du client. Ce sont les informations d’identification de ressource à laquelle vous voulez accéder, dans ce cas le portail des développeurs.

![Informations d'identification du client][api-management-client-credentials]

Pour obtenir **l’ID client**, accédez à l’onglet **Configurer** de l’application AAD pour le Portail des développeurs et copiez **l’ID client**.

Pour obtenir la **Clé secrète client**, cliquez sur la liste déroulante **Sélectionner une durée** dans la section **Clés** et spécifiez un intervalle. Dans cet exemple, la valeur 1 an est utilisée.

![ID client][api-management-aad-client-id]

Cliquez sur **Enregistrer** pour enregistrer la configuration et afficher la clé. 

> [!IMPORTANT]
> Notez sa valeur. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne peut plus être affichée.
> 
> 

Copiez la clé dans le presse-papiers, revenez au portail de publication, collez la clé dans la zone de texte **Clé secrète client**, puis cliquez sur **Enregistrer**.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-3]

Immédiatement après les informations d’identification du client, un code d’autorisation s’affiche. Copiez ce code d’autorisation, revenez à la page de configuration d’application du portail des développeurs Azure AD et collez le code d’autorisation dans le champ **URL de réponse**, puis cliquez de nouveau sur **Enregistrer**.

![URL de réponse][api-management-aad-reply-url]

L’étape suivante consiste à configurer les autorisations pour le portail des développeurs AAD. Cliquez sur **Autorisations d’application** et cochez la case **Lire les données du répertoire**. Cliquez sur **Enregistrer** pour enregistrer cette modification, puis cliquez sur **Ajouter l’application**.

![Ajout d’autorisations][api-management-add-devportal-permissions]

Cliquez sur l’icône de recherche, saisissez **APIM** dans le champ Commençant par, sélectionnez **APIMAADDemo**, puis cliquez sur la coche pour enregistrer.

![Ajout d’autorisations][api-management-aad-add-app-permissions]

Cliquez sur **Autorisations déléguées** pour **APIMAADDemo**, cochez la case **Accès à APIMAADDemo**, puis cliquez sur **Enregistrer**. Cela permet à l’application du portail des développeurs d’accéder au service du serveur principal.

![Ajout d’autorisations][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Activation de l’autorisation de l’utilisateur OAuth 2.0 pour l’API de calcul
Maintenant que le serveur OAuth 2.0 est configuré, vous pouvez le spécifier dans les paramètres de sécurité pour votre API. 

Cliquez sur **API** dans le menu situé à gauche et cliquez sur **Calculatrice** pour afficher et configurer ses paramètres.

![API de calcul][api-management-calc-api]

Accédez à l’onglet **Sécurité**, activez la case à cocher **OAuth 2.0**, sélectionnez le serveur d’autorisation souhaité dans la liste déroulante **Serveur d’autorisation**, puis cliquez sur **Enregistrer**.

![API de calcul][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Appel réussi de l’API de calcul à partir du portail des développeurs
Maintenant que l’autorisation OAuth 2.0 est configurée sur l’API, ses opérations peuvent être appelées avec succès à partir du portail des développeurs. 

Revenez à l’opération **Ajouter deux entiers** du service de calculatrice dans le portail des développeurs et cliquez sur **Essayer**. Observez le nouvel élément dans la section **Autorisation** correspondant au serveur d’autorisation que vous venez d’ajouter.

![API de calcul][api-management-calc-authorization-server]

Sélectionnez **Code d’autorisation** dans la liste déroulante d’autorisations et saisissez les informations d’identification du compte à utiliser. Si vous êtes déjà connecté avec ce compte, vous ne serez pas invité.

![API de calcul][api-management-devportal-authorization-code]

Cliquez sur **Envoyer** et observez l’**État de la réponse** obtenu, à savoir **200 OK**, et les résultats de l’opération dans le contenu de la réponse.

![API de calcul][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configuration d’une application de bureau pour appeler l’API

Configuration d’une application de bureau simple pour appeler l’API La première étape consiste à enregistrer l’application de bureau dans Azure AD et à lui donner accès au répertoire et au service du serveur principal. 

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configuration d’une stratégie de validation JWT pour autoriser des demandes

Utilisez la stratégie [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) pour pré-autoriser les demandes en validant les jetons d’accès de chaque demande entrante. Si la demande n’est pas validée par la stratégie Valider un JWT, la demande est bloquée par Gestion des API et n’est pas transmise au serveur principal.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Pour plus d’informations, consultez [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) et avancez jusqu’à 13:50. Rendez-vous directement à 15’00’’ pour afficher les stratégies configurées dans l’éditeur de stratégies, puis à 18’50’’ pour une démonstration de l’appel d’une opération à partir du portail des développeurs avec et sans le jeton d’autorisation requis.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
* Pour les autres méthodes permettant de sécuriser votre service principal, consultez [Authentification mutuelle des certificats](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
