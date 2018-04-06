---
title: 'Azure Active Directory B2C : échanges de revendications de l’API REST en tant qu’étape d’orchestration | Microsoft Docs'
description: Une rubrique sur les stratégies personnalisées d’Azure Active Directory B2C qui s’intègrent à une API
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.openlocfilehash: bde1ef711fe19b7e26f2d93b58d653dc816b1a07
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Procédure pas à pas : intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration

L’infrastructure d’expérience d’identité sur laquelle repose Azure Active Directory B2C (Azure AD B2C) permet au développeur d’identité d’intégrer une interaction avec une API RESTful dans un parcours utilisateur.  

À la fin de cette procédure pas à pas, vous pourrez créer un parcours utilisateur Azure AD B2C qui interagit avec les services RESTful.

L’IEF envoie des données dans des revendications et reçoit en retour des données via des revendications. L’échange de revendications de l’API REST :

- Peut être conçue comme une étape d’orchestration.
- Peut déclencher une action externe. Par exemple, elle peut enregistrer un événement dans une base de données externe.
- Peut être utilisée pour extraire une valeur puis la stocker dans la base de données utilisateur.

Vous pouvez utiliser ultérieurement les revendications reçues pour modifier le flux d’exécution.

Vous pouvez aussi concevoir l’interaction comme un profil de validation. Pour plus d’informations, consultez [Intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure Active Directory B2C comme validation d’une entrée de l’utilisateur](active-directory-b2c-rest-api-validation-custom.md).

Le scénario est le suivant : quand un utilisateur effectue une modification du profil, nous voulons :

1. Rechercher l’utilisateur dans un système externe.
2. Obtenir la ville dans laquelle cet utilisateur est inscrit.
3. Retourner cet attribut à l’application sous forme de revendication.

## <a name="prerequisites"></a>Prérequis


- Un locataire Azure AD B2C configuré pour effectuer une inscription/connexion à un compte local, comme décrit dans [Bien démarrer](active-directory-b2c-get-started-custom.md).
- Un point de terminaison API REST avec lequel vous allez interargir. Cette procédure pas à pas utilise comme exemple un webhook d’application de fonction Azure simple.
- *Recommandé* : suivez la procédure pas à pas d’échange de revendications de l’[API REST comme une étape de validation](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Étape 1 : préparation de la fonction de l’API REST

> [!NOTE]
> Cet article ne traite pas de la configuration des fonctions de l’API REST. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) fournit un excellent kit de ressources pour la création de services RESTful dans le cloud.

Nous avons configuré une fonction Azure qui reçoit une revendication appelée `email` puis qui retourne simplement la revendication `city` avec la valeur affectée `Redmond`. L’exemple de fonction Azure se trouve sur [Github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

La revendication `userMessage` retournée par la fonction Azure est facultative dans ce contexte et elle sera ignorée par l’infrastructure d’expérience d’identité. Vous pouvez éventuellement l’utiliser comme message passé à l’application et présenté ultérieurement à l’utilisateur.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Une application de fonction Azure facilite l’obtention de l’URL de la fonction, qui inclut l’identificateur de la fonction spécifique. Dans ce cas, l’URL est la suivante : https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Vous pouvez l’utiliser pour les tests.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Étape 2 : configuration de l’échange de revendications de l’API RESTful comme profil technique dans votre fichier TrustFrameworExtensions.xml

Le profil technique est la configuration complète de l’échange souhaité avec le service RESTful. Ouvrez le fichier TrustFrameworkExtensions.xml et ajoutez l’extrait de code XML suivant à l’intérieur de l’élément `<ClaimsProvider>`.

> [!NOTE]
> Dans le code XML suivant, le fournisseur RESTful `Version=1.0.0.0` est décrit comme étant le protocole. Considérez-le comme étant la fonction qui interagit avec le service externe. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

L’élément `<InputClaims>` définit les revendications qui seront envoyées depuis l’IEF vers le service REST. Dans cet exemple, le contenu de la revendication `givenName` sera envoyé au service REST en tant que revendication `email`.  

L’élément `<OutputClaims>` définit les revendications que l’IEF doit recevoir du service REST. Quel que soit le nombre de revendications reçues, l’infrastructure d’expérience d’identité utilisera seulement celles qui sont identifiées ici. Dans cet exemple, une revendication reçue en tant que `city` sera mappée à une revendication de l’infrastructure d’expérience d’identité appelée `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Étape 3 : ajout d’une nouvelle revendication `city` au schéma de votre fichier TrustFrameworkExtensions.xml

La revendication `city` n’est définie pour l’instant nulle part dans notre schéma. Ajoutez donc une définition à l’intérieur de l’élément `<BuildingBlocks>`. Vous pouvez trouver cet élément au début du fichier TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Étape 4 : inclusion de l’échange de revendications du service REST en tant qu’étape d’orchestration dans votre parcours utilisateur de modification de profil dans votre fichier TrustFrameworkExtensions.xml

Ajoutez une étape au parcours utilisateur de modification de profil, une fois que l’utilisateur a été authentifié (étapes d’orchestration 1 à 4 du fichier XML suivant) et qu’il a fourni les informations de profil mises à jour (étape 5).

> [!NOTE]
> Vous pouvez utiliser l’appel de l’API REST comme étape d’orchestration dans de nombreux cas d’utilisation. Il peut par exemple être utilisé comme mise à jour pour un système externe une fois qu’un utilisateur a terminé une tâche, comme sa première inscription ou une mise à jour de son profil, afin de synchroniser les informations. Dans ce cas, il est utilisé pour transférer les informations fournies à l’application après la modification du profil.

Copiez le code XML du parcours utilisateur de modification du profil du fichier TrustFrameworkBase.xml vers votre fichier TrustFrameworkExtensions.xml, à l’intérieur de l’élément `<UserJourneys>`. Effectuez la modification de l’étape 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Si l’ordre ne correspond pas à votre version, veillez à insérer le code comme étape avant le type `ClaimsExchange` `SendClaims`.

Le code XML final pour le parcours utilisateur doit ressembler à ceci :

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Étape 5 : ajout de la revendication `city` à votre fichier de stratégie de partie de confiance pour que la revendication soit envoyée à votre application

Ouvrez votre fichier de partie de confiance ProfileEdit.xml et modifiez l’élément `<TechnicalProfile Id="PolicyProfile">` en y ajoutant ceci : `<OutputClaim ClaimTypeReferenceId="city" />`.

Une fois que vous avez ajouté la nouvelle revendication, le profil technique ressemble à ceci :

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>Étape 6 : téléchargement et test de vos modifications

Remplacez les versions existantes de la stratégie.

1.  (Facultatif :) Enregistrez la version existante de votre fichier d’extensions (en la téléchargeant) avant de continuer. Nous vous recommandons de ne pas télécharger plusieurs versions du fichier d’extensions afin de ne pas compliquer l’opération.
2.  (Facultatif :) Renommez la nouvelle version de l’ID de stratégie pour le fichier de modification de stratégie en changeant `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Chargez le fichier d’extensions.
4.  Chargez le fichier de partie de confiance de modification de stratégie.
5.  Utilisez **Exécuter maintenant** pour tester la stratégie. Examinez le jeton retourné par l’infrastructure d’expérience d’identité à l’application.

Si tout est bien configuré, le jeton inclut la nouvelle revendication `city`, avec la valeur `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Étapes suivantes

[Use a REST API as a validation step](active-directory-b2c-rest-api-validation-custom.md) (Utilisation d’une API REST comme une étape de validation)

[Changer la modification de profil pour rassembler des informations supplémentaires de vos utilisateurs](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
