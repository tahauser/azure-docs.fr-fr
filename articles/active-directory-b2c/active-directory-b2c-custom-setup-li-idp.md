---
title: 'Azure Active Directory B2C : Ajout de LinkedIn en tant que fournisseur d’identités OAuth2 à l’aide de stratégies personnalisées'
description: Article sur la procédure à suivre pour configurer une application LinkedIn à l’aide du protocole OAuth2 et de stratégies personnalisées
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
ms.openlocfilehash: 77e2b9b283e4051370ffb905681135c27512834e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C : Ajout de LinkedIn en tant que fournisseur d’identités à l’aide de stratégies personnalisées
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article indique comment activer la connexion pour les utilisateurs d’un compte LinkedIn à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-linkedin-account-application"></a>Étape 1 : Créer une application de compte LinkedIn
Pour utiliser LinkedIn en tant que fournisseur d’identité dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application LinkedIn et lui fournir les paramètres appropriés. Vous pouvez inscrire une application LinkedIn en accédant à la [page d’inscription LinkedIn](https://LinkedIn.com/signup).

1. Accédez au site web de [gestion des applications LinkedIn](https://www.linkedin.com/secure/developer?newapp=), connectez-vous avec vos informations d’identification de compte LinkedIn, puis sélectionnez **Créer une application**.

    ![Compte LinkedIn : création d’une application](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Dans la page **Créer une application**, effectuez les opérations suivantes :

    a. Entrez le **Nom de la société**, un **Nom** descriptif pour celle-ci et une **Description** de votre nouvelle application.

    b. Chargez le **logo de votre application**.

    c. Sélectionnez une option **d’utilisation des applications**.

    d. Dans la zone **URL du site web**, collez **https://login.microsoftonline.com**.

    e. Tapez votre **Adresse e-mail professionnelle** et votre numéro de **Téléphone professionnel**.

    f. Au bas de la page, lisez et acceptez les termes du contrat, puis sélectionnez **Envoyer**.

    ![Compte LinkedIn : configuration des propriétés de l’application](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Sélectionnez **Authentification**, puis notez les valeurs **ID client** et **Secret du client**.

4. Dans la zone **URL de redirection autorisées**, collez **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Remplacez {*locataire*} par le nom de votre locataire (par exemple contosob2c.onmicrosoft.com). Assurez-vous d'utiliser le schéma HTTPS. 

    ![Compte LinkedIn : définition des URL de redirection autorisées](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >La clé secrète client est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

5. Sélectionnez **Ajouter**.

6. Sélectionnez **Paramètres**, Définissez **État de l’application** sur **Live**, puis sélectionnez **Mettre à jour**.

    ![Compte LinkedIn : définir l’état de l’application](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Étape 2 : Ajouter la clé d’application LinkedIn à Azure AD B2C
La fédération avec les comptes ADFS exige un secret de client pour le compte LinkedIn, afin de faire confiance à Azure AD B2C au nom de l’application. Pour stocker le secret d’application LinkedIn dans votre locataire Azure AD B2C, effectuez les opérations suivantes :  

1. Dans votre locataire Azure AD B2C, sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**.

2. Pour afficher les clés disponibles dans votre locataire, sélectionnez **Clés de stratégie**.

3. Sélectionnez **Ajouter**.

4. Dans la zone **Options**, sélectionnez **Charger**.

5. Dans la zone **Nom**, tapez **B2cRestClientCertificate**.  
    Il est possible que le préfixe *B2C_1A_* soit ajouté automatiquement.

6. Dans la zone **Secret**, entrez votre secret d’application LinkedIn à partir du [portail d’inscription des applications](https://apps.dev.microsoft.com).

7. Pour **Utilisation de la clé**, sélectionnez **Chiffrement**.

8. Sélectionnez **Créer**. 

9. Vérifiez que vous avez créé la clé `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Étape 3 : Ajouter un fournisseur de revendications à votre stratégie d’extension
Si vous souhaitez que les utilisateurs se connectent à l’aide de leur compte LinkedIn, vous devez définir LinkedIn en tant que fournisseur de revendications. En d’autres termes, vous devez spécifier les points de terminaison avec lesquels Azure AD B2C communique. Les points de terminaison fournissent un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez LinkedIn en tant que fournisseur de revendications, en ajoutant un nœud `<ClaimsProvider>` dans votre fichier de stratégie d’extension :

1. Dans votre répertoire de travail, ouvrez le fichier de stratégie d’extension *TrustFrameworkExtensions.xml*. 

2. Recherchez l’élément `<ClaimsProviders>`.

3. Dans l’élément `<ClaimsProviders>`, ajoutez l’extrait de code XML suivant : 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. Remplacez la valeur *client_id* par votre ID client d’application LinkedIn.

5. Enregistrez le fichier .

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Étape 4 : Inscrire le fournisseur de revendications du compte LinkedIn
Vous avez configuré le fournisseur d’identité. Toutefois, à ce stade, il n’est disponible dans aucune des fenêtres d’inscription ou de connexion. Maintenant, vous devez ajouter le fournisseur d’identité du compte LinkedIn au parcours `SignUpOrSignIn` de votre utilisateur.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Étape 4.1 : Effectuer une copie du parcours utilisateur
Pour que le parcours utilisateur soit disponible, vous créez un doublon d’un modèle de parcours utilisateur existant, puis vous ajoutez le fournisseur d’identité LinkedIn :

>[!NOTE]
>Si vous avez copié l’élément `<UserJourneys>` à partir du fichier de base de votre stratégie vers le fichier d’extension*TrustFrameworkExtensions.xml*, vous pouvez ignorer cette section.

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).

2. Recherchez l’élément `<UserJourneys>`, sélectionnez l’intégralité du contenu du nœud `<UserJourney>`, puis sélectionnez **Couper** pour déplacer le texte sélectionné dans le Presse-papiers.

3. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-le.

4. Collez l’intégralité du contenu du nœud `<UserJourney>`, que vous avez déplacé dans le Presse-papiers à l’étape 2, dans l’élément `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Étape 4.2 : Afficher le « bouton »
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre. Le nœud `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription ou de connexion. Si vous ajoutez un nœud `<ClaimsProviderSelection>` à un compte LinkedIn, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément, effectuez les opérations suivantes :

1. Recherchez le nœud `<UserJourney>` contenant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.

2. Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.

3. Dans l’élément `<ClaimsProviderSelections>`, ajoutez l’extrait de code XML suivant :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Étape 4.3 : Lier le bouton à une action
Un bouton étant maintenant en place, vous devez le lier à une action. Dans ce cas, l’action effectuée correspond à la communication entre Azure AD B2C et le compte LinkedIn pour recevoir un jeton. Liez le bouton à une action en reliant le profil technique de votre fournisseur de revendications de compte LinkedIn :

1. Recherchez le nœud `<OrchestrationStep>` contenant `Order="2"` dans le nœud `<UserJourney>`.

2. Dans l’élément `<ClaimsExchanges>`, ajoutez l’extrait de code XML suivant :

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Vérifiez que `Id` a la même valeur que `TargetClaimsExchangeId` dans la section précédente.
    >* Vérifiez que l’ID `TechnicalProfileReferenceId` est défini sur le profil technique que vous avez créé plus haut (LinkedIn-OAuth).

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
    Vous devriez maintenant être en mesure de vous connecter à l’aide du compte LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Étape 7 : (Facultatif) Inscrire le fournisseur de revendications du compte LinkedIn à un parcours utilisateur Modification de profil
Vous pouvez également ajouter le fournisseur d’identité du compte LinkedIn à votre parcours utilisateur `ProfileEdit`. Pour que le parcours utilisateur soit disponible, répétez l’étape 4. Cette fois-ci, sélectionnez le nœud `<UserJourney>` qui contient `Id="ProfileEdit"`. Enregistrez, chargez et testez votre stratégie.

## <a name="optional-download-the-complete-policy-files"></a>(Facultatif) Télécharger les fichiers de stratégie complets
Une fois que vous avez [pris en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md), nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée. Des [exemples de fichiers de stratégie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app) sont à votre disposition pour référence.
