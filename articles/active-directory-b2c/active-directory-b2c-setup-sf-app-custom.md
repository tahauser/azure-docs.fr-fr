---
title: 'Azure Active Directory B2C : ajout d’un fournisseur SAML Salesforce à l’aide de stratégies personnalisées | Microsoft Docs'
description: Découvrez comment créer et gérer des stratégies personnalisées Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.openlocfilehash: 1ccf5c58eab9df9016224a91ddda952a05457e2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C : connexion à l’aide de comptes Salesforce via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment utiliser des [stratégies personnalisées](active-directory-b2c-overview-custom.md) afin de configurer la connexion pour les utilisateurs d’une organisation Salesforce spécifique.

## <a name="prerequisites"></a>Prérequis


### <a name="azure-ad-b2c-setup"></a>Configuration d’Azure AD B2C

Vérifiez que vous avez suivi toutes les étapes montrant comment [bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md) dans Azure Active Directory B2C (Azure AD B2C).

Il s’agit des actions suivantes :

* Créer un locataire Azure AD B2C
* Créer une application Azure AD B2C
* Inscrire deux applications de moteur de stratégie
* Configurer des clés
* Configurer le pack de démarrage

### <a name="salesforce-setup"></a>Configuration Salesforce

Dans cet article, nous partons du principe que vous avez déjà effectué les opérations suivantes :

* Vous êtes déjà inscrit à un compte Salesforce. Vous pouvez vous inscrire pour bénéficier d’un [compte Édition Développeur gratuit](https://developer.salesforce.com/signup).
* Vous avez déjà [configuré un domaine My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pour votre organisation Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Configurer Salesforce pour que les utilisateurs puissent fédérer

Pour aider Azure AD B2C à communiquer avec Salesforce, vous devez obtenir l’URL de métadonnées de Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurer Salesforce en tant que fournisseur d’identité

> [!NOTE]
> Dans cet article, nous supposons que vous utilisez [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Connectez-vous à Salesforce](https://login.salesforce.com/). 
2. Dans le menu de gauche, sous **Settings (Paramètres)**, développez **Identity (Identité)**, puis cliquez sur **Identity Provider (Fournisseur d’identité)**.
3. Cliquez sur **Enable Identity Provider (Activer le fournisseur d’identité)**.
4. Sous **Select the certificate (Sélectionner le certificat)**, sélectionnez le certificat que vous souhaitez que Salesforce utilise pour communiquer avec Azure AD B2C (vous pouvez utiliser le certificat par défaut). Cliquez sur **Enregistrer**. 

### <a name="create-a-connected-app-in-salesforce"></a>Créer une application connectée dans Salesforce

1. Dans la page **Identity Provider (Fournisseur d’identité)**, accédez à **Service Providers (Fournisseurs de services)**.
2. Cliquez sur **Service Providers are now created via Connected Apps (Les fournisseurs de services sont maintenant créés via des applications connectées). Cliquez ici.**
3. Sous **Basic Information (Informations de base)**, entrez les valeurs requises pour votre application connectée.
4. Sous **Web App Settings (Paramètres de l’application web)**, activez la case à cocher **Enable SAML (Activer SAML)**.
5. Dans le champ **Entity ID (ID d’entité)**, entrez l’URL suivante. Veillez à remplacer la valeur de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. Dans le champ **ACS URL (URL ACS)**, entrez l’URL suivante. Veillez à remplacer la valeur de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Conservez les valeurs par défaut pour tous les autres paramètres.
8. Faites défiler vers le bas de la liste, puis cliquez sur **Enregistrer**.

### <a name="get-the-metadata-url"></a>Obtenir l’URL des métadonnées

1. Dans la page de présentation de votre application connectée, cliquez sur **Manage (Gérer)**.
2. Copiez la valeur de **point de terminaison de découverte des métadonnées**, puis enregistrez-la. Vous l’utiliserez plus loin dans cet article.

### <a name="set-up-salesforce-users-to-federate"></a>Configurer des utilisateurs Salesforce pour fédérer

1. Dans la page **Manage (Gérer)** de votre application connectée, accédez à **Profiles (Profils)**.
2. Cliquez sur **Manage Profiles (Gérer les profils)**.
3. Sélectionnez les profils (ou groupes d’utilisateurs) que vous souhaitez fédérer avec Azure AD B2C. En tant qu’un administrateur système, activez la case à cocher **Administrateur système** afin de pouvoir fédérer à l’aide de votre compte Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Générer un certificat de signature pour Azure AD B2C

Les demandes envoyées à Salesforce doivent être signées par Azure AD B2C. Pour générer un certificat de signature, ouvrez Azure PowerShell, puis exécutez les commandes suivantes.

> [!NOTE]
> Veillez à mettre à jour le nom et le mot de passe du locataire dans les deux lignes du haut.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Ajouter un certificat de signature SAML à Azure AD B2C

Chargez le certificat de signature sur votre locataire Azure AD B2C : 

1. Accédez à votre locataire Azure AD B2C. Cliquez sur **Paramètres** > **Infrastructure d’expérience d’identité** > **Clés de stratégie**.
2. Cliquez sur **+Ajouter**, puis :
    1. Cliquez sur **Options** > **Charger**.
    2. Entrez un **Nom** (par exemple, SAMLSigningCert). Le préfixe *B2C_1A_* est ajouté automatiquement au nom de votre clé.
    3. Pour sélectionner votre certificat, choisissez **contrôle du fichier de téléchargement**. 
    4. Entrez le mot de passe du certificat que vous avez défini dans le script PowerShell.
3. Cliquez sur **Créer**.
4. Vérifiez que vous avez créé une clé (par exemple, B2C_1A_SAMLSigningCert). Prenez note du nom complet (y compris *B2C_1A_*). Vous ferez référence à cette clé plus loin dans la stratégie.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Créer le fournisseur de revendications SAML Salesforce dans votre stratégie de base

Vous devez définir Salesforce comme fournisseur de revendications pour que les utilisateurs puissent se connecter à l’aide de Salesforce. En d’autres termes, vous devez spécifier le point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison *fournira* un ensemble de *revendications* utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique a été authentifié. Pour faire cela, ajoutez `<ClaimsProvider>` pour Salesforce dans le fichier d’extension de votre stratégie :

1. Dans votre répertoire de travail, ouvrez le fichier d’extension (TrustFrameworkExtensions.xml).
2. Recherchez la section `<ClaimsProviders>`. Si elle n’existe pas, créez-la sous le nœud racine.
3. Ajoutez un `<ClaimsProvider>` :

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

Sous le nœud `<ClaimsProvider>` :

1. Modifiez la valeur de `<Domain>` en la définissant sur une valeur unique qui distingue `<ClaimsProvider>` d’autres fournisseurs d’identité.
2. Mettez à jour la valeur de `<DisplayName>` en la définissant sur un nom d’affichage pour le fournisseur de revendications. Cette valeur n’est pas utilisée actuellement.

### <a name="update-the-technical-profile"></a>Mise à jour du profil technique

Pour obtenir un jeton SAML à partir de Salesforce, définissez les protocoles qu’Azure AD B2C doit utiliser pour communiquer avec Azure Active Directory (Azure AD). Faites cela dans l’élément `<TechnicalProfile>` de `<ClaimsProvider>` :

1. Mettez à jour l’ID du nœud `<TechnicalProfile>`. Cet ID est utilisé pour faire référence à ce profil technique à partir d’autres parties de la stratégie.
2. Mettez à jour la valeur pour `<DisplayName>`. Cette valeur apparaît sur le bouton de connexion dans votre page de connexion.
3. Mettez à jour la valeur pour `<Description>`.
4. Salesforce utilise le protocole SAML 2.0. Vérifiez que la valeur de `<Protocol>` est **SAML2**.

Mettez à jour la section `<Metadata>` dans le code XML précédent afin de refléter les paramètres de votre compte Salesforce. Dans le code XML, mettez à jour les valeurs de métadonnées :

1. Mettez à jour la valeur de `<Item Key="PartnerEntity">` avec l’URL des métadonnées de Salesforce que vous avez copiée précédemment. Son format est le suivant: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. Dans la section `<CryptographicKeys>`, mettez à jour la valeur des deux instances de `StorageReferenceId` sur le nom de la clé de votre certificat de signature (par exemple, B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

Votre stratégie est à présent configurée pour qu’Azure AD B2C sache comment communiquer avec Salesforce. Essayez de charger le fichier d’extension de votre stratégie pour vérifier qu’il n’y a pas de problème. Pour charger le fichier d’extension de votre stratégie :

1. Dans votre locataire Azure AD B2C, accédez au tableau **Toutes les stratégies**.
2. Activez la case à cocher **Remplacer la stratégie si elle existe**.
3. Chargez le fichier d’extension (TrustFrameworkExtensions.xml). Vérifiez que sa validation n’échoue pas.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Enregistrer le fournisseur de revendications SAML Salesforce dans un parcours utilisateur

Ajoutez ensuite le fournisseur d’identité SAML Salesforce à l’un de vos parcours utilisateur. À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucune page d’inscription ou de connexion. Pour ajouter le fournisseur d’identité à une page de connexion, commencez par créer un doublon d’un modèle de parcours utilisateur actuel. Ensuite, modifiez le modèle afin qu’il ait également le fournisseur d’identité Azure AD.

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2. Recherchez l’élément `<UserJourneys>`, puis copiez l’intégralité de la valeur `<UserJourney>`, en incluant Id=”SignUpOrSignIn”.
3. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml). Recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, créez-en un.
4. Collez l’intégralité de l’élément `<UserJourney>` copié en tant qu’enfant de l’élément `<UserJourneys>`.
5. Renommez l’ID du nouvel élément `<UserJourney>` (par exemple, SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Afficher le bouton du fournisseur d’identité

L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription ou de connexion. L’ajout d’un élément `<ClaimsProviderSelection>` pour Salesforce a pour effet qu’un nouveau bouton s’affiche quand un utilisateur accède à cette page. Pour afficher le bouton de fournisseur d’identité :

1. Dans l’élément `<UserJourney>` que vous avez créé, recherchez l’élément `<OrchestrationStep>` avec `Order="1"`.
2. Ajoutez le code XML suivant :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Définissez `TargetClaimsExchangeId` sur une valeur logique. Nous vous recommandons de suivre la même convention de dénomination que pour les autres éléments (par exemple, *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Lier le bouton de fournisseur d’identité à une action

À présent que vous avez un bouton de fournisseur d’identité en place, liez-le à une action. En l’occurrence, l’action pour Azure AD B2C est de communiquer avec Salesforce pour recevoir un jeton SAML. Vous pouvez faire cela en liant le profil technique de votre fournisseur de revendications SAML Salesforce :

1. Dans le nœud `<UserJourney>`, recherchez l’élément `<OrchestrationStep>` avec `Order="2"`.
2. Ajoutez le code XML suivant :

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Mettez à jour `Id` sur la même valeur que celle utilisée précédemment pour `TargetClaimsExchangeId`.
4. Mettez à jour `TechnicalProfileReferenceId` sur l’`Id` du profil technique que vous avez créé précédemment (par exemple, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Téléchargez le fichier d’extension mis à jour

Vous avez terminé la modification du fichier d’extension. Enregistrez et chargez ce fichier. Faites en sorte que toutes les validations réussissent.

### <a name="update-the-relying-party-file"></a>Mettre à jour le fichier de partie de confiance

Ensuite, mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé :

1. Faites une copie de SignUpOrSignIn.xml dans votre répertoire de travail. Ensuite, renommez-le (par exemple, SignUpOrSignInWithAAD.xml).
2. Ouvrez le nouveau fichier, puis mettez à jour l’attribut `PolicyId` pour `<TrustFrameworkPolicy>` avec une valeur unique. Il s’agit du nom de votre stratégie (par exemple, SignUpOrSignInWithAAD).
3. Modifiez l’attribut `ReferenceId` dans `<DefaultUserJourney>` pour qu’il corresponde à l’`Id` du nouveau parcours utilisateur que vous avez créé (par exemple, SignUpOrSignUsingContoso).
4. Enregistrez vos modifications, puis chargez le fichier.

## <a name="test-and-troubleshoot"></a>Tester et résoudre les problèmes

Pour tester la stratégie personnalisée que vous venez de charger, dans le portail Azure, accédez au panneau Stratégie, puis cliquez sur **Exécuter maintenant**. En cas d’échec, consultez [Résoudre les problèmes des stratégies personnalisées](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Étapes suivantes

Envoyez vos commentaires à l’adresse [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
