---
title: 'Azure Active Directory B2C : Modifier l’inscription dans les stratégies personnalisées et configurer le fournisseur autodéclaré'
description: Une procédure pas à pas sur l’ajout de revendications pour inscrire et configurer la saisie utilisateur
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2017
ms.author: davidmu
ms.openlocfilehash: b9ea48f0700509194602dd15ff42c7c689685a53
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C : Modifier l’inscription pour ajouter de nouvelles recommandations et configurer la saisie utilisateur.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dans cet article, vous allez ajouter une nouvelle entrée fournie par l’utilisateur (une revendication) à votre parcours utilisateur d’inscription.  Vous allez configurer l’entrée en tant que liste déroulante et définir si elle est nécessaire.

## <a name="prerequisites"></a>Prérequis


* Suivez les étapes de l’article [Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).  Testez le parcours utilisateur d’inscription/de connexion pour inscrire un nouveau compte local avant de continuer.


La collecte des données initiales de vos utilisateurs est obtenue via l’inscription/la connexion.  Des revendications supplémentaires peuvent être collectées ultérieurement via des parcours utilisateur de modification de profil. Chaque fois qu’Azure AD B2C rassemble des informations directement à partir de l’utilisateur de manière interactive, l’infrastructure d’expérience d’identité utilise son `selfasserted provider`. Les étapes ci-dessous s’appliquent à chaque fois que ce fournisseur est utilisé.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Définir la revendication, son nom d’affichage et le type de saisie utilisateur
Demandons à l’utilisateur la ville où il vit.  Ajoutez l’élément suivant à l’élément `<ClaimsSchema>` dans le fichier de stratégie TrustFrameworkBase :

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Vous pouvez sélectionner d’autres choix pour personnaliser la revendication.  Pour un schéma complet, reportez-vous au **guide de référence technique sur l’infrastructure d’expérience d’identité**.  Ce guide sera bientôt publié dans la section de référence.

* `<DisplayName>` est une chaîne qui définit *l’étiquette* orientée utilisateur.

* `<UserHelpText>` permet à l’utilisateur de comprendre ce qui est nécessaire.

* `<UserInputType>` comprend les quatre options suivantes mises en surbrillance ci-dessous :
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` - Applique une seule sélection.
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

    * `DropdownSingleSelect` - Permet la sélection d’une valeur valide uniquement.

![Capture d’écran de l’option de liste déroulante](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```


* `CheckboxMultiSelect` Permet la sélection d’une ou de plusieurs valeurs.

![Capture d’écran de l’option de sélection multiple](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Ajouter la revendication pour le parcours utilisateur d’inscription/de connexion

1. Ajoutez la revendication en tant que `<OutputClaim ClaimTypeReferenceId="city"/>` à `LocalAccountSignUpWithLogonEmail` de TechnicalProfile (trouvé dans le fichier de stratégie TrustFrameworkBase).  Notez que ce TechnicalProfile utilise le SelfAssertedAttributeProvider.

  ```xml
  <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    <DisplayName>Email signup</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
      <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
      <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
      <Item Key="language.button_continue">Create</Item>
    </Metadata>
    <CryptographicKeys>
      <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
    </CryptographicKeys>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
      <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" />
      <OutputClaim ClaimTypeReferenceId="newUser" />
      <!-- Optional claims, to be collected from the user -->
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surName" />
      <OutputClaim ClaimTypeReferenceId="city"/>
    </OutputClaims>
    <ValidationTechnicalProfiles>
      <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
    </ValidationTechnicalProfiles>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

2. Ajoutez la revendication à l’AAD-UserWriteUsingLogonEmail en tant que `<PersistedClaim ClaimTypeReferenceId="city" />` pour écrire la revendication dans le répertoire AAD après sa collecte auprès de l’utilisateur. Vous pouvez ignorer cette étape si vous préférez ne pas conserver la revendication dans le répertoire pour une utilisation ultérieure.

  ```xml
  <!-- Technical profiles for local accounts -->
  <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
    <Metadata>
      <Item Key="Operation">Write</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
    </InputClaims>
    <PersistedClaims>
      <!-- Required claims -->
      <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
      <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
      <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
      <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
      <!-- Optional claims. -->
      <PersistedClaim ClaimTypeReferenceId="givenName" />
      <PersistedClaim ClaimTypeReferenceId="surname" />
      <PersistedClaim ClaimTypeReferenceId="city" />
    </PersistedClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

3. Ajouter la revendication au TechnicalProfile qui lit à partir du répertoire lorsqu’un utilisateur se connecte en tant que `<OutputClaim ClaimTypeReferenceId="city" />`

  ```xml
  <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
    <Metadata>
      <Item Key="Operation">Read</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
    </InputClaims>
    <OutputClaims>
      <!-- Required claims -->
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <!-- Optional claims -->
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="otherMails" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  </TechnicalProfile>
  ```

4. Ajoutez `<OutputClaim ClaimTypeReferenceId="city" />` au fichier de stratégie de partie de confiance SignUporSignIn.xml pour que cette revendication soit envoyée à l’application dans le jeton après un parcours utilisateur réussi.

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
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>Testez la stratégie personnalisée à l’aide de « Exécuter maintenant »

1. Ouvrez le **panneau Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité > Stratégies personnalisées**.
2. Sélectionnez la stratégie personnalisée que vous avez téléchargée, puis cliquez sur le bouton **Exécuter maintenant**.
3. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.

L’écran d’inscription en mode test doit ressembler à ceci :

![Capture d’écran de l’option d’inscription modifiée](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Le jeton qui est renvoyé à vote application inclut désormais la revendication `city` comme illustré ci-dessous.
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>Facultatif : Supprimer la vérification de l’adresse e-mail du parcours d’inscription

Pour ignorer la vérification de l’adresse e-mail, l’auteur de la stratégie peut choisir de supprimer `PartnerClaimType="Verified.Email"`. L’adresse e-mail sera requise mais pas vérifiée, sauf si « Requis » = true est supprimé.  Réfléchissez bien pour savoir si cette option peut vous être utile !

La vérification de l’adresse e-mail est activée par défaut dans `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">`, dans le fichier de stratégie TrustFrameworkBase du pack de démarrage :
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Étapes suivantes

Ajoutez la nouvelle revendication aux flux des connexions aux comptes sociaux en modifiant les éléments TechnicalProfiles répertoriés ci-dessous. Ces derniers sont utilisés par les connexions aux comptes sociaux/fédérés pour écrire et lire les données utilisateur avec alternativeSecurityId en tant que localisateur.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```
