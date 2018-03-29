---
title: 'Azure Active Directory B2C : ajout de vos propres attributs aux stratégies personnalisées et utilisation dans la modification de profil | Microsoft Docs'
description: Une procédure pas à pas sur l’utilisation des propriétés d’extension et des attributs personnalisés, ainsi que sur la manière de les inclure dans l’interface utilisateur
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: e0595a67b90e1be7bb992ef7bda4343e692d8957
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C : création et utilisation d’attributs personnalisés dans une stratégie personnalisée de modification de profil

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans cet article, vous allez créer un attribut personnalisé dans votre répertoire Azure AD B2C et utiliser ce nouvel attribut comme une revendication personnalisée dans le parcours utilisateur Modification de profil.

## <a name="prerequisites"></a>Prérequis


Suivez les étapes de l’article [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Utilisation d’attributs personnalisés pour recueillir des informations sur vos consommateurs dans Azure Active Directory B2C à l’aide des stratégies personnalisées
Votre répertoire Azure Active Directory (Azure AD) B2C est fourni avec un ensemble intégré d’attributs : prénom, nom, ville, code postal, userPrincipalName, etc.  Vous serez souvent amené à créer vos propres attributs.  Par exemple : 
* Une application côté client a besoin de conserver un attribut tel que « LoyaltyNumber ».
* Un fournisseur d’identité a un identificateur d’utilisateur unique qui doit être enregistré, par exemple « uniqueUserGUID ».
* Un parcours utilisateur personnalisé doit conserver un état de l’utilisateur, par exemple « migrationStatus ».

Avec Azure AD B2C, vous pouvez étendre l’ensemble d’attributs stocké sur chaque compte utilisateur. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

Les propriétés d’extension étendent le schéma des objets utilisateur dans le répertoire.  Les termes propriété d’extension, attribut personnalisé et revendication personnalisée font référence à la même chose dans le contexte de cet article, et le nom varie en fonction du contexte (application, objet, de stratégie).

Les propriétés d’extension ne peuvent être inscrites que sur un objet application, même si elles peuvent contenir des données pour un utilisateur. La propriété est attachée à l’application. L’accès en écriture pour enregistrer une propriété d’extension doit être accordé à l’objet application. 100 propriétés d’extension (de TOUS les types et de TOUTES les applications) peuvent être écrites dans n’importe objet unique. Les propriétés d’extension sont ajoutées au type de répertoire cible et deviennent immédiatement accessibles dans le client du répertoire Azure AD B2C.
Si l’application est supprimée, ces propriétés d’extension, ainsi que toutes les données qu’elles contiennent pour tous les utilisateurs, sont également supprimées. Si une propriété d’extension est supprimée par l’application, elle est supprimée de l’objet de répertoire cible, et les valeurs sont supprimées.

Les propriétés d’extension n’existent que dans le contexte d’une application inscrite dans le client. L’ID d’objet de cette application doit être inclus dans le TechnicalProfile qui l’utilise.

>[!NOTE]
>Le répertoire Azure AD B2C inclut généralement une application web nommée `b2c-extensions-app`.  Cette application est principalement utilisée par les stratégies B2C intégrées pour les revendications personnalisées créées via le portail Azure.  Il est recommandé, pour les utilisateurs expérimentés, d’utiliser cette application pour enregistrer des extensions pour les stratégies B2C personnalisées.  Les instructions de cette procédure figurent dans la section Étapes suivantes de cet article.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Création d’une nouvelle application pour stocker les propriétés d’extension

1. Ouvrez une session de navigation et accédez au [portail Azure](https://portal.azure.com), puis connectez-vous avec les informations d’identification administratives du répertoire B2C que vous souhaitez configurer.
1. Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**. Vous devrez peut-être sélectionner Plus de services> pour le trouver.
1. Sélectionnez **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application**.
1. Indiquez les entrées recommandées ci-après :
  * Spécifiez un nom pour l’application web : **WebApp-GraphAPI-DirectoryExtensions**
  * Type d’application : application web/API
  * URL d’ouverture de session : https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Sélectionnez **Créer. Un message confirmant la création de l’application s’affiche dans les **notifications**.
1. Sélectionnez l’application web que vous venez de créer : **WebApp-GraphAPI-DirectoryExtensions**
1. Dans Paramètres, sélectionnez **Autorisations requises**.
1. Sélectionnez l’API **Windows Azure Active Directory**.
1. Dans Autorisations d’application, cochez l’option **Lire et écrire les données de l’annuaire**, puis cliquez sur **Enregistrer**.
1. Sélectionnez **Accorder des autorisations**, puis confirmez en cliquant sur **Oui**.
1. Copiez dans le presse-papiers et enregistrez les identificateurs suivants à partir de WebApp-GraphAPI-DirectoryExtensions > Paramètres > Propriétés >
*  **ID de l’application**. Exemple : `103ee0e6-f92d-4183-b576-8c3739027780`
* **ID objet**. Exemple : `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modification de votre stratégie personnalisée pour ajouter ApplicationObjectId

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
>Le <TechnicalProfile Id="AAD-Common"> est appelé « commun », car ses éléments sont inclus et réutilisés dans tous les TechnicalProfiles d’Azure Active Directory, à l’aide de l’élément : `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>Lorsque le TechnicalProfile écrit pour la première fois dans la propriété d’extension qui vient d’être créée, vous pouvez rencontrer une erreur unique.  La propriété d’extension est créée lors de la première utilisation.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Utilisation d’une nouvelle propriété d’extension ou d’un nouvel attribut personnalisé dans un parcours utilisateur


1. Ouvrez le fichier de partie de confiance (RP) qui décrit votre parcours utilisateur Modification de profil.  Si vous débutez, nous vous conseillons de télécharger votre version déjà configurée du fichier de partie de confiance PolicyEdit directement à partir de la section Stratégie personnalisée d’Azure B2C sur le portail Azure.  Vous pouvez également ouvrir votre fichier XML à partir de votre dossier de stockage.
2. Ajoutez une revendication personnalisée `loyaltyId`.  Si vous incluez la revendication personnalisée dans l’élément `<RelyingParty>`, elle sera transférée en tant que paramètre dans les éléments TechnicalProfiles UserJourney et sera incluse dans le jeton pour l’application.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Ajoutez une définition de revendication dans le fichier de stratégie d’extension `TrustFrameworkExtensions.xml` à l’intérieur de l’élément `<ClaimsSchema>`, comme indiqué.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Ajoutez la même définition de revendication dans le fichier de stratégie de base `TrustFrameworkBase.xml`.  
>L’ajout d’une définition `ClaimType` dans les fichiers de base et d’extensions n’est normalement pas nécessaire. Toutefois, étant donné que dans les étapes suivantes vous ajouterez « extension_loyaltyId » aux éléments TechnicalProfiles du fichier de base, sans cette définition, le programme de validation de stratégie rejettera le chargement du fichier de base.
>Il peut être utile de suivre l’exécution du parcours utilisateur nommé « ProfileEdit » dans le fichier TrustFrameworkBase.xml.  Recherchez le parcours utilisateur du même nom dans votre éditeur et vérifiez que l’étape d’orchestration 5 appelle TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Recherchez et examinez ce TechnicalProfile pour vous familiariser avec le flux.
5. Ajoutez loyaltyId comme revendication d’entrée et de sortie dans l’élément TechnicalProfile « SelfAsserted-ProfileUpdate ».
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Ajoutez la revendication dans l’élément TechnicalProfile « AAD-UserWriteProfileUsingObjectId » pour conserver la valeur de la revendication dans la propriété d’extension, pour l’utilisateur actuellement présent dans le répertoire.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Ajoutez la revendication dans l’élément TechnicalProfile « AAD UserReadUsingObjectId » pour lire la valeur de l’attribut d’extension chaque fois qu’un utilisateur se connecte. Jusqu'à présent les éléments TechnicalProfiles ont été modifiés dans le flux des comptes locaux uniquement.  Si vous souhaitez placer le nouvel attribut dans le flux d’un compte fédéré/social, vous devez modifier un autre ensemble d’éléments TechnicalProfiles. Reportez-vous aux étapes suivantes.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>L’élément IncludeTechnicalProfile ajoute tous les éléments de « AAD-Common » à cet élément TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Testez la stratégie personnalisée à l’aide de « Exécuter maintenant »
1. Ouvrez le **panneau Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité > Stratégies personnalisées**.
1. Sélectionnez la stratégie personnalisée que vous avez téléchargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.

Le jeton d’ID renvoyé à votre application inclura la nouvelle propriété d’extension sous la forme d’une revendication personnalisée, précédée de « extension_loyaltyId ». Consultez les exemples.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Ajoutez la nouvelle revendication aux flux des connexions aux comptes sociaux en modifiant les éléments TechnicalProfiles répertoriés ci-dessous. Ces deux éléments TechnicalProfiles sont utilisés par les connexions aux comptes sociaux/fédérés pour écrire et lire les données utilisateur à l’aide d’alternativeSecurityId en tant que localisateur de l’objet utilisateur.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Utilisation des mêmes attributs d’extension pour les stratégies intégrées et les stratégies personnalisées.
Lorsque vous ajoutez des attributs d’extension (ou attributs personnalisés) via le portail, ils sont inscrits à l’aide de **b2c-extensions-app, qui se trouve dans chaque locataire B2C.  Pour utiliser ces attributs d’extension dans votre stratégie personnalisée :
1. Sur portal.azure.com, dans votre locataire B2C, accédez à **Azure Active Directory**, puis sélectionnez **Inscriptions des applications**
2. Recherchez **b2c-extensions-app**, puis sélectionnez-la.
3. Sous Éléments principaux, notez **l’ID de l’application** et **l’ID de l’objet**.
4. Ajoutez-les aux métadonnées de votre profil technique AAD-Common de la façon suivante :

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Pour maintenir une cohérence avec le portail, créez ces attributs dans l’interface utilisateur du portail *avant* de les utiliser dans vos stratégies personnalisées.  Lorsque vous créez un attribut « ActivationStatus » dans le portail, vous devez le référencer de la façon suivante :

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Informations de référence

* Un **profil technique (PT)** est un type d’élément qui peut être considéré comme une *fonction* qui définit le nom d’un point de terminaison, ses métadonnées et son protocole, et détaille l’échange de revendications que l’IEF doit effectuer.  Lorsque cette *fonction* est appelée au cours d’une étape d’orchestration ou à partir d’un autre élément TechnicalProfile, InputClaims et OutputClaims sont fournis comme paramètres par l’appelant.


* Pour plus d’informations sur les propriétés d’extension, consultez l’article [Extensions de schéma d’annuaire | Concepts de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

>[!NOTE]
>Les attributs d’extension dans l’API Graph sont nommés à l’aide de la convention `extension_ApplicationObjectID_attributename`. Les stratégies personnalisées désignent les attributs d’extensions par extension_attributename, omettant ainsi l’élément ApplicationObjectId dans le fichier XML
