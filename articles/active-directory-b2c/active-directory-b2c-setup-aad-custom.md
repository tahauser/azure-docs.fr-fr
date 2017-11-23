---
title: "Azure Active Directory B2C : Ajouter un fournisseur Azure AD à l’aide de stratégies personnalisées | Microsoft Docs"
description: "Découvrir les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 2b91c75ec73a8b47a67241e3b69c2ed68d5f9b2a
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C : se connecter à l’aide de comptes Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs d’une organisation Azure Active Directory (Azure AD) spécifique en utilisant des [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Conditions préalables

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :

1. Création d’un locataire Azure Active Directory B2C (Azure AD B2C).
2. Création d’une application Azure AD B2C.
3. Inscription de deux applications de moteur de stratégie.
4. Configuration des clés.
5. Configuration du pack de démarrage.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation.

>[!NOTE]
> Nous utilisons « contoso.com » pour l’organisation (locataire) Azure AD et « fabrikamb2c.onmicrosoft.com » comme locataire Azure AD B2C dans les instructions suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu supérieur, sélectionnez votre compte. Dans la liste **Répertoire**, choisissez l’organisation (locataire) Azure AD auprès de laquelle vous voulez inscrire votre application (contoso.com).
1. Cliquez sur **Autres services** dans le volet gauche, puis recherchez « Inscriptions d’applications ».
1. Sélectionnez **Nouvelle inscription d’application**.
1. Entrez un nom pour votre application (par exemple, `Azure AD B2C App`).
1. Pour le type d’application, sélectionnez **Application web/API**.
1. Pour **URL de connexion**, entrez l’URL suivante où `yourtenant` est remplacé par le nom de votre locataire Azure AD B2C (`fabrikamb2c.onmicrosoft.com`) :

    >[!NOTE]
    >La valeur de « yourtenant » doit être en minuscules dans **l’URL de connexion**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Enregistrez l’ID de l’application.
1. Sélectionnez l’application que vous venez de créer.
1. Dans le panneau **Paramètres**, sélectionnez **Clés**.
1. Créez une clé et enregistrez-la. Vous allez l’utiliser dans les étapes de la prochaine section.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Ajouter la clé d’Azure AD à Azure AD B2C

Vous devez stocker la clé d’application contoso.com dans votre locataire Azure AD B2C. Pour ce faire :
1. Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité** > **Clés de stratégie**.
1. Sélectionnez **+Ajouter**.
1. Sélectionnez ou entrez les options suivantes :
   * Sélectionnez **Manuel**.
   * Pour **Nom**, choisissez un nom correspondant au nom de votre locataire Azure AD (par exemple, `ContosoAppSecret`).  Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
   * Collez votre clé d’application dans la zone **Secret**.
   * Sélectionnez **Signature**.
1. Sélectionnez **Créer**.
1. Vérifiez que vous avez créé la clé `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Ajoutez un fournisseur de revendications dans votre stratégie de base

Si vous souhaitez que les utilisateurs se connectent à l’aide d’Azure AD, vous devez définir Azure AD comme fournisseur de revendications. En d’autres termes, vous devez spécifier un point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. 

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD au nœud `<ClaimsProvider>` dans le fichier d’extension de votre stratégie :

1. Ouvrez le fichier d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail.
1. Recherchez la section `<ClaimsProviders>`. Si elle n’existe pas, ajoutez-la sous le nœud racine.
1. Ajoutez un nœud `<ClaimsProvider>` comme suit :

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sous le nœud `<ClaimsProvider>`, mettez à jour la valeur de `<Domain>` sur une valeur unique utilisable pour la distinguer d’autres fournisseurs d’identité.
1. Sous le nœud `<ClaimsProvider>`, mettez à jour la valeur de `<DisplayName>` sur un nom convivial pour le fournisseur de revendications. Cette valeur n’est pas utilisée actuellement.

### <a name="update-the-technical-profile"></a>Mise à jour du profil technique

Pour obtenir un jeton à partir du point de terminaison Azure AD, vous devez définir les protocoles qu’Azure AD B2C doit utiliser pour communiquer avec Azure AD. Cela s’effectue à l’intérieur de l’élément `<TechnicalProfile>` de `<ClaimsProvider>`.
1. Mettez à jour l’ID du nœud `<TechnicalProfile>`. Cet ID est utilisé pour faire référence à ce profil technique à partir d’autres parties de la stratégie.
1. Mettez à jour la valeur pour `<DisplayName>`. Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion.
1. Mettez à jour la valeur pour `<Description>`.
1. Azure AD utilisant le protocole OpenID Connect, vérifiez que la valeur de `<Protocol>` est bien `"OpenIdConnect"`.

Vous devez mettre à jour la section `<Metadata>` dans le XML évoqué précédemment pour refléter les paramètres de configuration de votre locataire Azure AD spécifique. Dans le fichier XML, mettez à jour les valeurs des métadonnées comme suit :

1. Définissez `<Item Key="METADATA">` sur `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, où `yourAzureADtenant` est le nom de votre locataire Azure AD (contoso.com).
1. Ouvrez votre navigateur, puis accédez à l’URL `METADATA` que vous venez de mettre à jour.
1. Dans le navigateur, recherchez l’objet « issuer » et copiez sa valeur. Elle doit ressembler à ceci : `https://sts.windows.net/{tenantId}/`.
1. Collez la valeur de `<Item Key="ProviderName">` dans le fichier XML.
1. Définissez `<Item Key="client_id">` sur l’ID d’application de l’inscription de l’application.
1. Définissez `<Item Key="IdTokenAudience">` sur l’ID d’application de l’inscription de l’application.
1. Vérifiez que `<Item Key="response_types">` est défini sur `id_token`.
1. Vérifiez que `<Item Key="UsePolicyInRedirectUri">` est défini sur `false`.

Vous devez également lier le secret Azure AD que vous avez inscrit dans votre locataire Azure AD B2C aux informations `<ClaimsProvider>` d’Azure AD :

* Dans la section `<CryptographicKeys>` du fichier XML ci-dessus, mettez à jour la valeur pour `StorageReferenceId` sur l’ID de référence du secret que vous avez défini (par exemple, `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre répertoire Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment. Pour ce faire :

1. Accédez au panneau **Toutes les stratégies** dans votre locataire Azure AD B2C.
1. Cochez la case **Remplacer la stratégie si elle existe**.
1. Téléchargez le fichier d’extension (TrustFrameworkExtensions.xml) et vérifiez que sa validation n’échoue pas.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Inscrire le fournisseur de revendications AD Azure dans un parcours utilisateur

Vous devez maintenant ajouter le fournisseur d’identité Azure AD à l’un de vos parcours utilisateur. À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, nous créons un doublon d’un modèle de parcours utilisateur et le modifions afin qu’il dispose également du fournisseur d’identité Azure AD :

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
1. Recherchez l’élément `<UserJourneys>` et copiez l’ensemble du nœud `<UserJourney>` qui inclut `Id="SignUpOrSignIn"`.
1. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité du nœud `<UserJourney>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.
1. Renommez l’ID du nouveau parcours utilisateur (par exemple, en `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Afficher le « bouton »

L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur un écran d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` pour Azure AD, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément :

1. Recherchez le nœud `<OrchestrationStep>` incluant `Order="1"` dans le parcours utilisateur que vous venez de créer.
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Définissez `TargetClaimsExchangeId` sur une valeur appropriée. Nous vous recommandons de suivre la même convention que pour les autres : *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications Azure AD :

1. Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Mettez à jour `Id` sur la même valeur que celle de `TargetClaimsExchangeId` dans la section précédente.
1. Mettez à jour `TechnicalProfileReferenceId` sur l’ID du profil technique créé précédemment (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Téléchargez le fichier d’extension mis à jour

Vous avez terminé la modification du fichier d’extension. Enregistrez-le. Chargez ensuite le fichier et vérifiez que toutes les validations réussissent.

### <a name="update-the-rp-file"></a>Mise à jour du fichier RP

Vous devez maintenant mettre à jour le fichier de partie de confiance qui initialisera le parcours utilisateur que vous venez de créer :

1. Effectuez une copie de SignUpOrSignIn.xml dans votre répertoire de travail et renommez-le (par exemple, SignUpOrSignInWithAAD.xml).
1. Ouvrez le nouveau fichier et mettez à jour l’attribut `PolicyId` pour `<TrustFrameworkPolicy>` avec une valeur unique (par exemple, SignUpOrSignInWithAAD). <br> Celle-ci correspond au nom de votre stratégie (par exemple, B2C\_1A\_SignUpOrSignInWithAAD).
1. Modifiez l’attribut `ReferenceId` dans `<DefaultUserJourney>` pour qu’il corresponde à l’ID du nouveau parcours utilisateur que vous avez créé (SignUpOrSignUsingContoso).
1. Enregistrez vos modifications et chargez le fichier.

## <a name="troubleshooting"></a>Résolution de problèmes

Testez la stratégie personnalisée que vous venez de charger en ouvrant son panneau et en cliquant sur **Exécuter maintenant**. Pour diagnostiquer des problèmes, consultez la page sur la [résolution des problèmes](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Étapes suivantes

Envoyez vos commentaires à l’adresse [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
