---
title: 'Azure Active Directory B2C : Ajout de Google+ en tant que fournisseur d’identités OAuth2 à l’aide de stratégies personnalisées'
description: Exemple d’utilisation de Google+ en tant que fournisseur d’identité à l’aide du protocole OAuth2
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
ms.openlocfilehash: f12bbc2472aa2d83088c7d4d7b8f173744fb36b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C : Ajout de Google+ en tant que fournisseur d’identités OAuth2 à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ce guide vous montre comment activer l’identification pour les utilisateurs de comptes Google+ à l’aide des [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prérequis


Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :

1.  Création d’une application de compte Google+.
2.  Ajout de la clé d’application de compte Google+ à Azure AD B2C
3.  Ajout du fournisseur de revendications à une stratégie
4.  Inscription du fournisseur de revendications de compte Google+ à un parcours utilisateur
5.  Téléchargement et test de la stratégie sur un client Azure AD B2C

## <a name="create-a-google-account-application"></a>Création d’une application de compte Google+
Pour utiliser Google+ en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Google+ et lui fournir les paramètres appropriés. Vous pouvez inscrire une application Google+ ici : [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Accédez à la [Console développeur de Google](https://console.developers.google.com/) et connectez-vous avec les informations d’identification de votre compte Google+.
2.  Cliquez sur **Créer un projet**, saisissez un **nom de projet**, puis cliquez sur **Créer**.

3.  Cliquez sur le **menu Projets**.

    ![Compte Google+ - Sélectionner le projet](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Cliquez sur le bouton **+**.

    ![Compte Google+ - Créer un nouveau projet](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Entrez un **Nom de projet**, puis cliquez sur **Créer**.

    ![Compte Google+ : Nouveau projet](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Attendez que le projet soit prêt, puis cliquez sur le **menu Projets**.

    ![Compte Google+ - Attente jusqu'à ce que le nouveau projet soit prêt à l’emploi](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Cliquez sur le nom de votre projet.

    ![Compte Google+ - Sélectionner le nouveau projet](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Cliquez sur **Gestionnaire d’API**, puis sur **Informations d’identification** dans le volet de navigation de gauche.
9.  Cliquez sur l’onglet **OAuth consent screen** en haut.

    ![Compte Google+ - Définition de l’écran de consentement OAuth](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Sélectionnez ou spécifiez une valeur valide pour **Adresse de messagerie**, fournissez une valeur pour **Nom de produit**, puis cliquez sur **Enregistrer**.

    ![Google+ - Informations d’identification de l’application](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Cliquez sur **Nouvelles informations d’identification**, puis sur **ID client OAuth**.

    ![Google+ - Créer de nouvelles informations d’identification d’application](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  Sous **Type d’application**, sélectionnez **Application web**.

    ![Google+ - Sélectionner un type d’application](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Fournissez un **nom** pour votre application, entrez `https://login.microsoftonline.com` dans le champ **Origines JavaScript autorisées**, et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URI de redirection autorisée**. Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com). La valeur **{tenant}** respecte la casse. Cliquez sur **Créer**.

    ![Google+ - Fournir les origines autorisées de JavaScript et les URI de redirection](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de configurer Google+ en tant que fournisseur d'identité dans votre client. **Client secret** est une information d’identification de sécurité importante.

    ![Google+ - Copiez les valeurs de ID client et Clé secrète client](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Ajouter la clé d’application de compte Google+ à Azure AD B2C
La fédération avec les comptes Google+ requiert une clé client secrète pour le compte Google+ pour accorder la confiance à Azure AD B2C au nom de l’application. Vous devez enregistrer votre clé secrète d’application Google+ dans le client Azure AD B2C :  

1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Manuel**.
5.  Pour **Nom**, utilisez `GoogleSecret`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans la boîte **Secret**, saisissez votre clé secrète d’application depuis la [console développeurs de Google](https://console.developers.google.com/) copiée ci-dessus.
7.  Pour **Utilisation de la clé**, utilisez **Signature**.
8.  Cliquez sur **Créer**
9.  Vérifiez que vous avez créé la clé `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Ajouter un fournisseur de revendications à une stratégie d’extension

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Google+, vous devez définir le compte Google+ comme fournisseur de revendications. En d’autres termes, vous devez spécifier un point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez le compte Google+ comme fournisseur de revendications, en ajoutant le nœud `<ClaimsProvider>` dans votre fichier de stratégie d’extension :

1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.
2.  Recherchez la section `<ClaimsProviders>`
3.  Ajoutez l’extrait XML suivant sous l’élément `ClaimsProviders` et remplacez la valeur `client_id` par votre ID de client d’application de compte Google+ avant d’enregistrer le fichier.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Inscription du fournisseur de revendications de compte Google+ à un parcours utilisateur Inscription ou Connexion

Le fournisseur d’identité a été configuré.  Toutefois, il n’est disponible dans aucun des écrans d’inscription/de connexion. Ajoutez le fournisseur d’identité du compte Google+ au parcours utilisateur `SignUpOrSignIn` de votre utilisateur. Pour le rendre disponible, nous dupliquons un modèle de parcours utilisateur existant.  Ensuite, nous ajoutons le fournisseur d’identité de compte Google+ :

>[!NOTE]
>
>Si vous avez copié l’élément `<UserJourneys>` à partir du fichier de base de votre stratégie vers le fichier d’extension (TrustFrameworkExtensions.xml), vous pouvez ignorer cette section.

1.  Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2.  Recherchez l’élément `<UserJourneys>` et copiez la totalité du contenu du nœud `<UserJourneys>`.
3.  Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
4.  Collez l’intégralité du contenu du nœud `<UserJourney>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.

### <a name="display-the-button"></a>Afficher le bouton
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre.  L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` pour un compte Google+, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément :

1.  Recherchez le nœud `<UserJourney>` comprenant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.
2.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action
Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec le compte Google+ pour recevoir un jeton.

1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Assurez-vous que `Id` a la même valeur que celle de `TargetClaimsExchangeId` dans la section précédente
> * Vérifiez que l’ID `TechnicalProfileReferenceId` est défini sur le profil de technique que vous avez créé plus haut (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Charger la stratégie sur un client
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Ouvrez le panneau **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**.
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="test-the-custom-policy-by-using-run-now"></a>Tester la stratégie personnalisée en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >
    >    **Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. 
    >    Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).


2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez être en mesure de vous connecter à l’aide de votre compte Google+.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Facultatif] Inscription du fournisseur de revendications de compte Google+ à un parcours utilisateur Modification de profil
Vous pouvez également ajouter le fournisseur d’identité du compte Google+ au parcours utilisateur `ProfileEdit` de votre utilisateur. Pour le rendre disponible, nous répétons les deux dernières étapes :

### <a name="display-the-button"></a>Afficher le bouton
1.  Ouvrez le fichier d’extension de votre stratégie (par exemple, TrustFrameworkExtensions.xml).
2.  Recherchez le nœud `<UserJourney>` comprenant `Id="ProfileEdit"` dans le parcours utilisateur que vous avez copié.
3.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
4.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action
1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>Charger la stratégie sur un client
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Ouvrez le panneau **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**.
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Tester la stratégie personnalisée de modification de profil en utilisant Exécuter maintenant

1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
2.  Ouvrez **B2C_1A_ProfileEdit**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez être en mesure de vous connecter à l’aide de votre compte Google+.

## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets
Facultatif : nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
