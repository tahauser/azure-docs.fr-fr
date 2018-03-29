---
title: 'Azure Active Directory B2C : Intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur'
description: Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure Active Directory B2C comme validation d’une entrée de l’utilisateur.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/30/2017
ms.author: davidmu
ms.openlocfilehash: 9429f690940e1282d94b4d67ad1c618a24655cb5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure Active Directory B2C comme validation d’une entrée de l’utilisateur
Avec l’infrastructure d’expérience d’identité, sur laquelle repose Azure Active Directory B2C (Azure AD B2C), vous pouvez intégrer une API RESTful à un parcours utilisateur. Dans cette procédure pas à pas, vous allez étudier la façon dont Azure AD B2C interagit avec les services RESTful de .NET Framework (API web).

## <a name="introduction"></a>Introduction
Avec Azure AD B2C, vous pouvez ajouter votre propre logique métier à un parcours utilisateur en appelant votre service RESTful. L’infrastructure d’expérience d’identité envoie des données au service RESTful dans une collection *Revendications d’entrée* et reçoit des données de RESTful dans une collection *Revendications de sortie*. Avec l’intégration de service RESTful, vous pouvez :

* **Valider les données d’entrée utilisateur** : cette action empêche que les données mal formées soient rendues persistantes dans Azure AD. Si la valeur de l’utilisateur n’est pas valide, votre service RESTful retourne un message d’erreur qui demande à l’utilisateur de fournir une entrée. Par exemple, vous pouvez vérifier que l’adresse e-mail fournie par l’utilisateur existe dans la base de données de clients.
* **Remplacer les revendications d’entrée** : par exemple, si un utilisateur entre le prénom en lettres minuscules ou majuscules, vous pouvez mettre en forme le nom en ne mettant que la première lettre en majuscule.
* **Enrichir les données utilisateur en les intégrant davantage aux applications métier d’entreprise**  : votre service RESTful peut recevoir l’adresse e-mail de l’utilisateur, interroger la base de données de clients et retourner le numéro de fidélité de l’utilisateur à Azure AD B2C. Les revendications retournées peuvent être stockées dans le compte Azure AD de l’utilisateur, évaluées dans les *étapes d’orchestration* suivantes ou incluses dans le jeton d’accès.
* **Exécuter la logique métier personnalisée**: vous pouvez envoyer des notifications Push, mettre à jour des bases de données d’entreprise, exécuter un processus de migration utilisateur, gérer les autorisations, auditer des bases de données et effectuer d’autres actions.

Vous pouvez concevoir l’intégration aux services RESTful comme suit :

* **Profil technique de validation** : l’appel au service RESTful se produit à l’intérieur du profil technique de validation du profil technique spécifié. Le profil technique de validation valide les données fournies par l’utilisateur avant la suite du parcours utilisateur. Avec le profil technique de validation, vous pouvez :
   * Envoyer des revendications d’entrée
   * Valider les revendications d’entrée et générer des messages d’erreur personnalisés
   * Renvoyer des revendications de sortie

* **Échange de revendications** : cette conception est similaire au profil technique de validation, mais elle se produit dans une étape d’orchestration. Cette définition est limitée à :
   * Envoyer des revendications d’entrée
   * Renvoyer des revendications de sortie

## <a name="restful-walkthrough"></a>Procédure pas à pas RESTful
Dans cette procédure pas à pas, vous allez développer une API web .NET Framework qui valide l’entrée utilisateur et fournit le numéro de fidélité d’un utilisateur. Par exemple, votre application peut accorder l’accès aux *avantages platinum* en fonction du numéro de fidélité.

Vue d’ensemble :
* Développer le service RESTful (API web .NET Framework)
* Utiliser le service RESTful dans le parcours utilisateur
* Envoyer les revendications d’entrée et lire celles-ci dans votre code
* Valider le prénom de l’utilisateur
* Renvoyer un numéro de fidélité 
* Ajouter le numéro de fidélité à un jeton web JSON (JWT)

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Étape 1 : Créer une API web ASP.NET

1. Dans Visual Studio, créez un projet en sélectionnant **Fichier** > **Nouveau** > **Projet**.

2. Dans la fenêtre **Nouveau projet**, sélectionnez **Visual C#** > **Web** > **Application web ASP.NET(.NET Framework)**.

3. Dans la zone **Nom**, tapez un nom pour l’application (par exemple, *Contoso.AADB2C.API*), puis sélectionnez **OK**.

    ![Créer un projet Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Dans la fenêtre **Nouvelle application web ASP.NET**, sélectionnez un modèle **API web** ou **Application API Azure**.

    ![Sélectionner le modèle d’API web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Vérifiez que l’authentification est définie sur **Aucune authentification**.

6. Cliquez sur **OK** pour créer le projet.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Étape 2 : Préparer le point de terminaison de l’API REST

### <a name="step-21-add-data-models"></a>Étape 2.1 : Ajouter des modèles de données
Les modèles représentent les données des revendications d’entrée et de sortie dans votre service RESTful. Votre code lit les données d’entrée en désérialisant le modèle de revendications d’entrée à partir d’une chaîne JSON vers un objet C# (votre modèle). L’API web ASP.NET désérialise automatiquement le modèle de revendications de sortie dans JSON, puis écrit les données sérialisées dans le corps du message de réponse HTTP. 

Créez un modèle qui représente les revendications d’entrée en effectuant les opérations suivantes :

1. Si l’Explorateur de solutions n’est pas déjà ouvert, sélectionnez **Vue** > **Explorateur de solutions**. 
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Modèles**, sélectionnez **Ajouter**, puis **Classe**.

    ![Ajouter un modèle](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Nommez la classe `InputClaimsModel`, puis ajoutez les propriétés suivantes à la classe `InputClaimsModel` :

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Créez un modèle, `OutputClaimsModel`, puis ajoutez les propriétés suivantes à la classe `OutputClaimsModel` :

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Créez un autre modèle, `B2CResponseContent`, qui vous permet de générer des messages d’erreur de validation d’entrée. Ajoutez les propriétés suivantes à la classe `B2CResponseContent`, indiquez les références manquantes, puis enregistrez le fichier :

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Étape 2.2 : Ajouter un contrôleur
Dans l’API web, un _contrôleur_ est un objet qui gère les requêtes HTTP. Le contrôleur retourne les revendications de sortie, ou, si le prénom n’est pas valide, génère un message d’erreur HTTP de conflit.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Contrôleurs**, sélectionnez **Ajouter**, puis **Contrôleur**.

    ![Ajouter un contrôleur](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Dans la fenêtre **Ajouter une structure**, cliquez sur **Web API Controller - Empty** (Contrôleur d’API web - Vide), puis sélectionnez **Ajouter**.

    ![Sélectionner Contrôleur API web 2 - Vide](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Dans la fenêtre **Ajouter un contrôleur**, nommez le contrôleur **IdentityController**, puis sélectionnez **Ajouter**.

    ![Taper le nom du contrôleur](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    La génération de modèles automatiques crée un fichier nommé *IdentityController.cs* dans le dossier *Controllers*.

4. Si le fichier *IdentityController.cs* n’est pas déjà ouvert, double-cliquez dessus, puis remplacez le code dans le fichier par le code suivant :

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Étape 3 : Publier le projet sur Azure
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.API**, puis sélectionnez **Publier**.

    ![Publier dans Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Dans la fenêtre **Publier**, sélectionnez **Microsoft Azure App Service**, puis cliquez sur **Publier**.

    ![Créer un Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    La fenêtre **Créer App Service** s’ouvre. Vous y créez toutes les ressources Azure nécessaires pour exécuter l’application web ASP.NET dans Azure.

    > [!NOTE]
    >Pour plus d’informations sur la publication, consultez : [Création d’une application web ASP.NET dans Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Dans la zone **Nom de l’application web**, tapez un nom d’application unique (les caractères autorisés sont les plages a-z, 0-9, et les traits d’union (-)). L’URL de l’application web est http://<nom_app>.azurewebsites.NET, où *nom_app* est le nom de votre application web. Vous pouvez accepter le nom généré automatiquement, qui est unique.

    ![Fournir les propriétés App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Pour commencer à créer des ressources Azure, sélectionnez **Créer**.  
    Une fois l’application web ASP.NET créée, l’Assistant la publie sur Azure, puis la démarre dans le navigateur par défaut.

6. Copiez l’URL de l’application web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Étape 4 : Ajouter la nouvelle revendication `loyaltyNumber` au schéma de votre fichier TrustFrameworkExtensions.xml
La revendication `loyaltyNumber` n’est définie pour l’instant dans notre schéma. Ajoutez une définition dans l’élément `<BuildingBlocks>`, qui se trouve au début du fichier *TrustFrameworkExtensions.xml*.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Étape 5 : Ajouter un fournisseur de revendications 
Chaque fournisseur de revendications doit avoir un ou plusieurs profils techniques qui déterminent les points de terminaison et les protocoles nécessaires pour communiquer avec le fournisseur de revendications. 

Un fournisseur de revendications peut avoir plusieurs profils techniques pour diverses raisons. Par exemple, plusieurs profils techniques peuvent être définis, car le fournisseur de revendications prend en charge plusieurs protocoles, les points de terminaison peuvent avoir des fonctionnalités distinctes ou les publications peuvent contenir des revendications présentant de nombreux niveaux d’assurance. Il peut être acceptable de publier des revendications sensibles dans un parcours utilisateur, mais pas dans un autre. 

L’extrait de code XML suivant contient un nœud de fournisseur de revendications avec deux profils techniques :

* **TechnicalProfile Id="REST-API-SignUp"** : définit votre service RESTful. 
   * `Proprietary` est décrit en tant que protocole pour un fournisseur basé sur RESTful. 
   * `InputClaims` définit les revendications qui seront envoyées depuis Azure AD B2C vers le service REST. 

   Dans cet exemple, le contenu de la revendication `givenName` est envoyé au service REST en tant que `firstName`, le contenu de la revendication `surname` est envoyé au service REST en tant que `lastName` et `email` est envoyé tel quel. L’élément `OutputClaims` définit les revendications qui sont récupérées du service RESTful vers Azure AD B2C.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"** : ajoute un profil technique de validation à un profil technique existant (défini dans la stratégie de base). Pendant le parcours d’inscription, le profil technique de validation appelle le profil technique précédent. Si le service RESTful retourne une erreur HTTP 409 (erreur de conflit), le message d’erreur est affiché à l’utilisateur. 

Recherchez le nœud `<ClaimsProviders>`, puis ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviders>` :

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Étape 6 : Ajouter la revendication `loyaltyNumber` à votre fichier de stratégie de partie de confiance pour que la revendication soit envoyée à votre application
Ouvrez votre fichier de partie de confiance *SignUpOrSignIn.xml* et modifiez l’élément TechnicalProfile Id="PolicyProfile" en y ajoutant ceci : `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Une fois que vous avez ajouté la nouvelle revendication, le code de partie de confiance ressemble à ceci :

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Étape 7 : Charger la stratégie sur votre locataire

1. Sur le [portail Azure](https://portal.azure.com), basculez vers le [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), puis ouvrez **Azure AD B2C**.

2. Sélectionnez **Infrastructure d’expérience d’identité**.

3. Ouvrez **Toutes les stratégies**. 

4. Sélectionnez **Charger la stratégie**.

5. Activez la case à cocher **Remplacer la stratégie si elle existe**.

6. Chargez le fichier TrustFrameworkExtensions.xml, puis vérifiez que sa validation réussit.

7. Répétez l’étape précédente avec le fichier SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Étape 8 : Tester la stratégie personnalisée en utilisant Exécuter maintenant
1. Sélectionnez **Paramètres Azure AD B2C**, puis accédez à **Infrastructure d’expérience d’identité**.

    > [!NOTE]
    > **Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2. Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée, puis sélectionnez **Exécuter maintenant**.

    ![Fenêtre B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testez le processus en tapant **Test** dans la zone **Prénom**.  
    Azure AD B2C affiche un message d’erreur en haut de la fenêtre.

    ![Tester votre stratégie](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Dans la zone **Prénom**, tapez un nom (différent de « Test »).  
    Azure AD B2C inscrit l’utilisateur, puis envoie un numéro de fidélité à votre application. Notez le numéro dans ce JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facultatif) Télécharger les fichiers de stratégie et le code complets
* Une fois que vous avez [pris en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md), nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée. Des [exemples de fichiers de stratégie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw) sont à votre disposition pour référence.
* Vous pouvez télécharger le code complet dans [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Étapes suivantes
* [Azure Active Directory B2C: Secure your RESTful services using HTTP basic authentication](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de l’authentification HTTP de base)
* [Sécuriser votre API RESTful avec des certificats clients](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
