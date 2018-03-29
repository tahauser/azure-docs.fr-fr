---
title: 'Azure Active Directory B2C : changement de mot de passe libre-service | Microsoft Docs'
description: Rubrique montrant comment configurer un changement de mot de passe libre-service pour vos consommateurs dans Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.openlocfilehash: 4c54abe8c3375b49b786c7849c7e963e5849fa3a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C : Configurer la modification du mot de passe dans des stratégies personnalisées  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Avec la fonctionnalité de modification de mot de passe, les consommateurs connectés (utilisant des comptes locaux) peuvent modifier leurs mots de passe, sans avoir à prouver leur authenticité avec une vérification par e-mail, comme décrit dans le [flux de réinitialisation de mot de passe libre-service.](active-directory-b2c-reference-sspr.md) Si la session expire avant que le consommateur n’effectue le flux du changement de mot de passe, l’utilisateur est invité à se reconnecter. 

## <a name="prerequisites"></a>Prérequis


Un locataire Azure AD B2C configuré pour effectuer une inscription/connexion à un compte local, comme décrit dans [Bien démarrer](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Guide pratique pour configurer le changement du mot de passe dans une stratégie personnalisée

Pour configurer la modification du mot de passe dans la stratégie personnalisée, effectuez les modifications suivantes dans votre stratégie d’extensions d’infrastructure de confiance. 

## <a name="define-a-claimtype-oldpassword"></a>Définir un type de revendication « oldPassword »

La structure globale de votre stratégie personnalisée doit inclure un `ClaimsSchema`et définir un nouveau `ClaimType` « oldPassword » comme indiqué ci-dessous, 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

L’objectif de ces éléments est le suivant :

- Le `ClaimsSchema` définit les revendications à valider,  Dans ce cas, « l’ancien mot de passe » sera validé. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Ajouter un fournisseur de revendications de modification de mot de passe avec ses éléments sous-jacents

Un fournisseur de revendications de modification de mot de passe permettra :

1. L’authentification de l’utilisation avec l’ancien mot de passe
2. Et si les champs « nouveau mot de passe » et « confirmer le mot de passe » correspondent, cette valeur est stockée dans le magasin de données B2C et le mot de passe est alors modifié. 

![img](images/passwordchange.jpg)

Ajoutez le fournisseur de revendications suivant à votre stratégie d’extensions. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>Ajouter les ID d’application à votre stratégie personnalisée

Ajoutez les ID d’application au fichier d’extensions (`TrustFrameworkExtensions.xml`) :

1. Dans le fichier d’extensions (TrustFrameworkExtensions.xml), recherchez les éléments `<TechnicalProfile Id="login-NonInteractive">` et `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`.

2. Remplacez toutes les instances de `IdentityExperienceFrameworkAppId` par l’ID de l’application d’infrastructure d’expérience d’identité comme décrit dans [Prise en main](active-directory-b2c-get-started-custom.md). Voici un exemple : 

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Remplacez toutes les instances de `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application d’infrastructure d’expérience d’identité de proxy comme décrit dans [Prise en main](active-directory-b2c-get-started-custom.md).

4. Enregistrez votre fichier d’extensions.



## <a name="create-a-password-change-user-journey"></a>Créer un parcours utilisateur pour la modification d’un mot de passe

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

Vous avez terminé la modification du fichier d’extension. Enregistrez et chargez ce fichier. Faites en sorte que toutes les validations réussissent.



## <a name="create-a-relying-party-rp-file"></a>Créer un fichier de partie de confiance (RP)

Ensuite, mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé :

1. Faites une copie de ProfileEdit.xml dans votre répertoire de travail. Renommez-le ensuite (par exemple, PasswordChange.xml).
2. Ouvrez le nouveau fichier, puis mettez à jour l’attribut `PolicyId` pour `<TrustFrameworkPolicy>` avec une valeur unique. Il s’agit du nom de votre stratégie (par exemple, PasswordChange).
3. Modifiez l’attribut `ReferenceId` dans `<DefaultUserJourney>` pour qu’il corresponde à l’`Id` du nouveau parcours utilisateur que vous avez créé (par exemple, PasswordChange).
4. Enregistrez vos modifications, puis chargez le fichier.
5. Pour tester la stratégie personnalisée que vous avez chargée, dans le portail Azure, accédez au panneau Stratégie, puis cliquez sur **Exécuter maintenant**.




## <a name="link-to-password-change-sample-policy"></a>Lien vers l’exemple de stratégie de modification de mot de passe

Vous pouvez trouver l’exemple de stratégie [ici](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










