---
title: 'Azure Active Directory B2C : ajoutez Twitter en tant que fournisseur d’identité OAuth1 à l’aide de stratégies personnalisées'
description: Utiliser Twitter en tant que fournisseur d’identité à l’aide du protocole OAuth1
services: active-directory-b2c
documentationcenter: ''
author: yoelhor
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: 629e0bbaa7c62ef5d381085588c6a99c203c41cb
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C : ajoutez Twitter en tant que fournisseur d’identité OAuth1 à l’aide de stratégies personnalisées
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article indique comment activer la connexion pour les utilisateurs d’un compte Twitter à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-twitter-account-application"></a>Étape 1 : Créer une application de compte Twitter
Pour utiliser Twitter en tant que fournisseur d’identité dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application Twitter et lui fournir les paramètres appropriés. Vous pouvez inscrire une application Twitter en accédant à la [page d’inscription Twitter](https://twitter.com/signup).

1. Accédez au site web [Twitter Developers](https://apps.twitter.com/), connectez-vous avec vos identifiants Twitter, puis sélectionnez **Create New App** (Créer une application).

    ![Compte Twitter - Créer une application](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. Dans la fenêtre **Create an application** (Créer une application), effectuez les opérations suivantes :
 
    a. Entrez le **nom** et une **description** pour votre nouvelle application. 

    b. Dans la zone **Site web**, collez **https://login.microsoftonline.com**. 

    c. Dans la zone **URL de rappel**, entrez **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Remplacez {*locataire*} par le nom de votre locataire (par exemple contosob2c.onmicrosoft.com). Assurez-vous d'utiliser le schéma HTTPS. 

    d. En bas de la page, lisez les conditions et acceptez-les, puis sélectionnez **Create your Twitter application** (Créer votre application Twitter).

    ![Compte Twitter - Ajouter une nouvelle application](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. Dans la fenêtre **B2C demo** (Démo B2C), sélectionnez **Settings** (Paramètres), cochez la case **Allow this application to be used to sign in with Twitter** (Autoriser l’utilisation de cette application pour se connecter avec Twitter), puis sélectionnez **Update Settings** (Mettre à jour les paramètres).

4. Sélectionnez **Keys and Access Tokens** (Clés et jetons d’accès), puis notez les valeurs de **Consumer Key (API Key)** (Clé de consommateur [clé API]) et **Consumer Secret (API Secret)** (Secret de consommateur [secret API]).

    ![Compte Twitter - Définir les propriétés de l’application](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Étape 2 : Ajouter votre clé d’application de compte Twitter à Azure AD B2C
La fédération avec les comptes Twitter nécessite un secret de consommateur pour que le compte Twitter accorde la confiance à Azure AD B2C au nom de l’application. Pour stocker le secret de consommateur de l’application Twitter dans votre locataire Azure AD B2C, effectuez les opérations suivantes : 

1. Dans votre locataire Azure AD B2C, sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**.

2. Pour afficher les clés disponibles dans votre locataire, sélectionnez **Clés de stratégie**.

3. Sélectionnez **Ajouter**.

4. Dans la zone **Options**, sélectionnez **Manuel**.

5. Dans la zone **Nom**, sélectionnez **TwitterSecret**.  
    Il est possible que le préfixe *B2C_1A_* soit ajouté automatiquement.

6. Dans la zone **Secret**, entrez votre secret d’application Microsoft à partir du [portail d’inscription des applications](https://apps.dev.microsoft.com).

7. Pour **Utilisation de la clé**, utilisez **Chiffrement**.

8. Sélectionnez **Créer**.

9. Vérifiez que vous avez créé la clé `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Étape 3 : Ajouter un fournisseur de revendications à votre stratégie d’extension

Si vous voulez que les utilisateurs se connectent à l’aide d’un compte Twitter, vous devez définir Twitter comme fournisseur de revendications. En d’autres termes, vous devez spécifier les points de terminaison avec lesquels Azure AD B2C communique. Les points de terminaison fournissent un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez Twitter comme fournisseur de revendications en ajoutant le nœud `<ClaimsProvider>` dans votre fichier de stratégie d’extension :

1. Ouvrez le fichier de stratégie d’extension *TrustFrameworkExtensions.xml* à partir de votre répertoire de travail. 

2. Recherchez la section `<ClaimsProviders>`.

3. Dans le nœud `<ClaimsProviders>`, ajoutez l’extrait de code XML suivant :  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Remplacez la valeur de *client_id* par la clé de consommateur de votre application de compte Twitter.

5. Enregistrez le fichier .

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Étape 4 : Inscrire le fournisseur de revendications de compte Twitter à votre parcours utilisateur d’inscription ou de connexion
Vous avez configuré le fournisseur d’identité. Toutefois, à ce stade, il n’est disponible dans aucune des fenêtres d’inscription ou de connexion. Vous devez maintenant ajouter le fournisseur d’identité du compte Twitter au parcours utilisateur `SignUpOrSignIn` de votre utilisateur.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Étape 4.1 : Effectuer une copie du parcours utilisateur
Pour que le parcours utilisateur soit disponible, vous créez un doublon d’un modèle de parcours utilisateur existant, puis vous ajoutez le fournisseur d’identité Twitter :

>[!NOTE]
>Si vous avez copié l’élément `<UserJourneys>` à partir du fichier de base de votre stratégie vers le fichier d’extension*TrustFrameworkExtensions.xml*, vous pouvez passer à la section suivante.

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).

2. Recherchez l’élément `<UserJourneys>`, sélectionnez l’intégralité du contenu du nœud `<UserJourney>`, puis sélectionnez **Couper** pour déplacer le texte sélectionné dans le Presse-papiers.

3. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-le.

4. Collez l’intégralité du contenu du nœud `<UserJourney>`, que vous avez déplacé dans le Presse-papiers à l’étape 2, dans l’élément `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Étape 4.2 : Afficher le « bouton »
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre. Le nœud `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription ou de connexion. Si vous ajoutez un nœud `<ClaimsProviderSelection>` à un compte Twitter, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément, effectuez les opérations suivantes :

1. Recherchez le nœud `<UserJourney>` contenant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.

2. Localisez le nœud `<OrchestrationStep>` qui contient `Order="1"`.

3. Dans l’élément `<ClaimsProviderSelections>`, ajoutez l’extrait de code XML suivant :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Étape 4.3 : Lier le bouton à une action
Un bouton étant maintenant en place, vous devez le lier à une action. L’action est dans ce cas la communication d’Azure AD B2C avec le compte Twitter pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications du compte Twitter :

1. Recherchez le nœud `<OrchestrationStep>` contenant `Order="2"` dans le nœud `<UserJourney>`.
2. Dans l’élément `<ClaimsExchanges>`, ajoutez l’extrait de code XML suivant :

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Vérifiez que `Id` a la même valeur que `TargetClaimsExchangeId` dans la section précédente.
    >* Vérifiez que l’ID `TechnicalProfileReferenceId` est défini sur le profil technique que vous avez créé plus haut (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Étape 5 : Charger la stratégie sur votre locataire
1. Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), puis sélectionnez **Azure AD B2C**.

2. Sélectionnez **Infrastructure d’expérience d’identité**.

3. Sélectionnez **Toutes les stratégies**.

4. Sélectionnez **Charger la stratégie**.

5. Activez la case à cocher **Remplacer la stratégie si elle existe**.

6. Chargez les fichiers *TrustFrameworkBase.xml* et *TrustFrameworkExtensions.xml* et vérifiez que leur validation réussit.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Étape 6 : Tester la stratégie personnalisée en utilisant la commande Exécuter maintenant

1. Sélectionnez **Paramètres Azure AD B2C**, puis **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >L’option Exécuter maintenant nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2. Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée, puis sélectionnez **Exécuter maintenant**.  
    Vous devriez maintenant être en mesure de vous connecter à l’aide du compte Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Étape 7 : (Facultatif) Inscrire le fournisseur de revendications du compte Twitter au parcours utilisateur de modification de profil
Vous pouvez également ajouter le fournisseur d’identité du compte Twitter à votre parcours utilisateur `ProfileEdit`. Pour que le parcours utilisateur soit disponible, répétez l’étape 4. Cette fois-ci, sélectionnez le nœud `<UserJourney>` qui contient `Id="ProfileEdit"`. Enregistrez, chargez et testez votre stratégie.


## <a name="optional-download-the-complete-policy-files"></a>(Facultatif) Télécharger les fichiers de stratégie complets
Une fois que vous avez [pris en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md), nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée. Des [exemples de fichiers de stratégie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app) sont à votre disposition pour référence.
