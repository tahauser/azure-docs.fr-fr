---
title: 'Azure Active Directory B2C : ajouter un compte Microsoft (MSA) comme fournisseur d’identité à l’aide de stratégies personnalisées'
description: Exemple utilisant Microsoft comme fournisseur d’identité à l’aide du protocole OpenID Connect (OIDC)
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
ms.openlocfilehash: a49e9589322eeb90a713321b4fbe4c4820609f7a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C : ajouter un compte Microsoft (MSA) comme fournisseur d’identité à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article montre comment permettre aux utilisateurs de se connecter à partir d’un compte Microsoft (MSA) à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :

1.  créer une application de compte Microsoft ;
2.  ajouter la clé d’application de compte Microsoft à Azure AD B2C ;
3.  ajouter le fournisseur de revendications à une stratégie ;
4.  inscrire le fournisseur de revendications des comptes Microsoft à un parcours utilisateur ;
5.  charger et tester la stratégie sur un client Azure AD B2C

## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft
Pour utiliser un compte Microsoft en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application de compte Microsoft et lui fournir les paramètres appropriés. Il vous faut un compte Microsoft. Si vous n’en avez pas, voir [https://www.live.com/](https://www.live.com/).

1.  Accédez au [Portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) et connectez-vous avec votre compte Microsoft.
2.  Cliquez sur **Ajouter une application**.

    ![Compte Microsoft : ajouter une application](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Donnez un **Nom** à votre application, indiquez une **adresse e-mail de contact**, décochez la case **Aidez-moi à commencer** et cliquez sur **Créer**.

    ![Compte Microsoft : inscrire une application](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Copiez la valeur **ID de l’application**. Vous en aurez besoin pour configurer le compte Microsoft comme fournisseur d’identité dans votre client.

    ![Compte Microsoft : copier la valeur ID de l’application](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Cliquez sur **Ajouter une plateforme**.

    ![Compte Microsoft - Ajouter une plateforme](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Dans la liste des plateformes, choisissez **Web**.

    ![Compte Microsoft : dans la liste des plateformes, choisissez Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **Rediriger les URI** . Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com).

    ![Compte Microsoft : définir des URL de redirection](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Cliquez sur **Générer un nouveau mot de passe** dans la section **Secrets de l’application**. Copiez le nouveau mot de passe affiché à l’écran. Vous en aurez besoin pour configurer le compte Microsoft comme fournisseur d’identité dans votre client. Ce mot de passe est une information d’identification de sécurité importante.

    ![Compte Microsoft - générer un nouveau mot de passe](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Compte Microsoft : copier le nouveau mot de passe](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Cochez la case **Support du Kit de développement logiciel (SDK) Live** dans la section **Options avancées**. Cliquez sur **Enregistrer**.

    ![Compte Microsoft - Support du Kit SDK Live](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Ajouter la clé d’application de compte Microsoft à Azure AD B2C
La fédération avec les comptes Microsoft requiert une clé secrète client pour que le compte Microsoft fasse confiance à Azure AD B2C au nom de l’application. Vous devez enregistrer la clé secrète de votre application Microsoft dans le client Azure AD B2C :   

1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Manuel**.
5.  Pour **Nom**, utilisez `MSASecret`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans la zone **Secret**, entrez la clé secrète de votre application Microsoft, provenant de https://apps.dev.microsoft.com.
7.  Pour **Utilisation de la clé**, utilisez **Signature**.
8.  Cliquez sur **Créer**
9.  Vérifiez que vous avez créé la clé `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Ajouter un fournisseur de revendications à une stratégie d’extension
Si vous souhaitez que les utilisateurs se connectent avec un compte Microsoft, vous devez le définir comme fournisseur de revendications. En d’autres termes, vous devez spécifier un point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier que chaque utilisateur s’est authentifié.

Définissez le compte Microsoft comme fournisseur de revendications, en ajoutant le nœud `<ClaimsProvider>` à votre fichier de stratégie d’extension :

1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.
2.  Recherchez la section `<ClaimsProviders>`
3.  Ajoutez l’extrait de code XML suivant sous l’élément `ClaimsProviders` :

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

4.  Remplacez la valeur `client_id` par votre ID client d’application de compte Microsoft.

5.  Enregistrez le fichier .

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Inscrire le fournisseur de revendications de compte Microsoft à un parcours utilisateur Inscription ou Connexion

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Vous devez à présent ajouter le fournisseur d’identité de compte Microsoft au parcours `SignUpOrSignIn` de votre utilisateur. Pour le rendre disponible, nous dupliquons un modèle de parcours utilisateur existant.  Ensuite, nous ajoutons le fournisseur d’identité de compte Microsoft :

> [!NOTE]
>
>Si vous avez déjà copié l’élément `<UserJourneys>` du fichier de base de votre stratégie dans le fichier d’extension `TrustFrameworkExtensions.xml`, vous pouvez ignorer cette section.

1.  Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2.  Recherchez l’élément `<UserJourneys>` et copiez la totalité du contenu du nœud `<UserJourneys>`.
3.  Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
4.  Collez l’intégralité du contenu du nœud `<UserJournesy>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.

### <a name="display-the-button"></a>Afficher le bouton
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre.  L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` au compte Microsoft, un nouveau bouton apparaît quand l’utilisateur accède à la page. Pour ajouter cet élément :

1.  Recherchez le nœud `<UserJourney>` comprenant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.
2.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action
Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action consiste, dans ce cas, à permettre à Azure AD B2C de communiquer avec le compte Microsoft pour recevoir un jeton. Associez le bouton à une action en liant le profil technique du fournisseur de revendications de votre compte Microsoft :

1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Vérifiez que `Id` a la même valeur que `TargetClaimsExchangeId` dans la section précédente.
>   * Vérifiez que l’ID `TechnicalProfileReferenceId` est défini sur le profil de technique que vous avez créé plus haut (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Charger la stratégie sur un client
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Ouvrez le panneau **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**.
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="test-the-custom-policy-by-using-run-now"></a>Tester la stratégie personnalisée en utilisant Exécuter maintenant

1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
> [!NOTE]
>
>**Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).
2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez pouvoir vous connecter avec un compte Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Facultatif] Inscrire le fournisseur de revendications de compte Microsoft à un parcours utilisateur Modification de profil
Vous pouvez également ajouter le fournisseur d’identité de compte Microsoft au parcours `ProfileEdit` de votre utilisateur. Pour le rendre disponible, nous répétons les deux dernières étapes :

### <a name="display-the-button"></a>Afficher le bouton
1.  Ouvrez le fichier d’extension de votre stratégie (par exemple, TrustFrameworkExtensions.xml).
2.  Recherchez le nœud `<UserJourney>` comprenant `Id="ProfileEdit"` dans le parcours utilisateur que vous avez copié.
3.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
4.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action
1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Tester la stratégie personnalisée de modification de profil en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
2.  Ouvrez **B2C_1A_ProfileEdit**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez pouvoir vous connecter avec un compte Microsoft.

## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets
Facultatif : nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
