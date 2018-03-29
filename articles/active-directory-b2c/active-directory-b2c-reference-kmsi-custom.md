---
title: 'Azure Active Directory B2C : Maintenir la connexion (KMSI) | Microsoft Docs'
description: Une rubrique qui montre comment configurer la fonctionnalité « Maintenir la connexion »
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
ms.openlocfilehash: 073ba8eef7f2f42d1c308fb20d3bfdbfc8d321b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C : activer la fonctionnalité « Maintenir la connexion (KMSI) »  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C permet désormais à vos applications web et natives d’activer la fonctionnalité « Maintenir la connexion (KMSI) ». Cette fonctionnalité octroie l’accès à l’application aux utilisateurs qui reviennent dessus sans les inviter à saisir une nouvelle fois leur nom d’utilisateur et mot de passe. Cet accès est révoqué lorsque l’utilisateur se déconnecte. 

Nous déconseillons aux utilisateurs d’activer cette option sur les ordinateurs publics. 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>Prérequis


Un locataire Azure AD B2C configuré pour se connecter/s’inscrire à un compte local, comme décrit dans [Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Comment activer la fonctionnalité KMSI

Apportez les modifications suivantes à votre stratégie d’extensions d’infrastructure de confiance.

## <a name="adding-a-content-definition-element"></a>Ajout d’un élément de définition de contenu 

Le nœud `BuildingBlocks` de votre fichier d’extension doit inclure un élément `ContentDefinitions`. 

1. Dans la section `ContentDefinitions`, définissez une nouvelle `ContentDefinition` avec l’ID `api.signuporsigninwithkmsi`.
2. Votre nouvelle `ContentDefinition` doit inclure `LoadUri`, `RecoveryUri` et `DataUri` comme suit.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` est un identificateur compréhensible par une machine qui affiche une case à cocher KMSI sur les pages de connexion. Veillez à ne pas modifier cette valeur. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>Ajouter un fournisseur de revendications de connexion à un compte local 

Vous pouvez définir Local Account SignIn comme fournisseur de revendications sur le nœud `<ClaimsProvider>` dans le fichier d’extension de votre stratégie :

1. Ouvrez le fichier d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. 
2. Recherchez la section `<ClaimsProviders>`. Si elle n’existe pas, ajoutez-la sous le nœud racine.
3. Le pack de démarrage de [Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md) comprend un fournisseur de revendications « Local Account SignIn ». 
4. Dans le cas contraire, ajoutez un nœud `<ClaimsProvider>` comme suit :

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
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

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Créer une option KMSI dans un parcours utilisateur

Vous devez maintenant ajouter le fournisseur de revendications Local Account SignIn à votre parcours utilisateur. 

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2. Recherchez l’élément `<UserJourneys>` et copiez l’ensemble du nœud `<UserJourney>` qui inclut `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du nœud `<UserJourney>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.
5. Renommez l’ID du nouveau parcours utilisateur (par exemple, en `SignUpOrSignInWithKmsi`).
6. Enfin, dans `OrchestrationStep 1`, remplacez l’élément `ContentDefinitionReferenceId` par l’élément `api.signuporsigninwithkmsi` que vous avez défini lors des étapes précédentes. Cela active la case à cocher dans le parcours utilisateur. 
7. Vous avez terminé la modification du fichier d’extension. Enregistrez et chargez ce fichier. Faites en sorte que toutes les validations réussissent.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>Créer un fichier de partie de confiance (RP)

Ensuite, mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé :

1. Faites une copie de SignUpOrSignIn.xml dans votre répertoire de travail. Ensuite, renommez-le (par exemple, SignUpOrSignInWithKmsi.xml).

2. Ouvrez le nouveau fichier, puis mettez à jour l’attribut `PolicyId` pour `<TrustFrameworkPolicy>` avec une valeur unique. Il s’agit du nom de votre stratégie (par exemple, SignUpOrSignInWithKmsi).

3. Modifiez l’attribut `ReferenceId` dans `<DefaultUserJourney>` pour qu’il corresponde à l’`Id` du nouveau parcours utilisateur que vous avez créé (par exemple, SignUpOrSignInWithKmsi).

4. La fonctionnalité KMSI est configurée dans `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** contrôle la durée pendant laquelle l’utilisateur reste connecté. Dans l’exemple suivant, la session KMSI expire automatiquement après 14 jours, quelle que soit la fréquence à laquelle l’utilisateur effectue une authentification silencieuse.

   Définir la valeur `KeepAliveInDays` sur 0 désactive la fonctionnalité KMSI. Par défaut, cette valeur est définie sur 0.

6. Si **`SessionExpiryType`** est *Continue*, la session KMSI est prolongée de 14 jours chaque fois que l’utilisateur effectue une authentification silencieuse.  Si l’option *Continue* est sélectionnée, nous vous recommandons de laisser le nombre de jours sur la valeur minimum. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. Enregistrez vos modifications, puis chargez le fichier.

8. Pour tester la stratégie personnalisée que vous avez chargée, dans le portail Azure, accédez au panneau Stratégie, puis cliquez sur **Exécuter maintenant**.


## <a name="link-to-sample-policy"></a>Lien vers un exemple de stratégie

Vous pouvez trouver l’exemple de stratégie [ici](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








