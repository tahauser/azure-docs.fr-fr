---
title: 'Azure Active Directory B2C : Ajout d’ADFS en tant que fournisseur d’identités SAML à l’aide de stratégies personnalisées'
description: Un guide pratique sur la configuration d’ADFS 2016 à l’aide du protocole SAML et de stratégies personnalisées
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
ms.openlocfilehash: af102bbc3bc7608fe641db19f4af8c760907a564
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C : Ajout d’ADFS en tant que fournisseur d’identités SAML à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article vous montre comment activer l’identification pour les utilisateurs de comptes ADFS à l’aide des [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Prérequis


Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :

1.  Création d’une approbation de partie de confiance ADFS.
2.  Ajout du certificat d’approbation de partie de confiance ADFS à Azure AD B2C.
3.  Ajout du fournisseur de revendications à une stratégie.
4.  Inscription du fournisseur de revendications de compte ADFS à un parcours utilisateur.
5.  Téléchargement et test de la stratégie sur un client Azure AD B2C.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Créer une approbation de partie de confiance prenant en charge les revendications

Pour utiliser ADFS en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une approbation de partie de confiance ADFS et lui fournir les paramètres appropriés.

Pour ajouter une nouvelle approbation de partie de confiance en utilisant le composant logiciel enfichable de gestion ADFS et configurer manuellement les paramètres, effectuez la procédure suivante sur un serveur de fédération.

L’appartenance au groupe **Administrateurs** ou équivalent sur l’ordinateur local est la condition minimale requise pour effectuer cette procédure. Examinez les informations relatives à l’utilisation des comptes et appartenances de groupe appropriés sur les [Groupes de domaine et locaux par défaut](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  Dans Gestionnaire de serveur, cliquez sur **Outils**, puis sélectionnez sur **Gestion ADFS**.

2.  Cliquez sur **Ajouter une approbation de partie de confiance**.
    ![Ajouter une approbation de partie de confiance](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Sur la page **Bienvenue**, choisissez **Prise en charge des revendications** et cliquez sur **Démarrer**.
    ![Sur la page d’accueil, choisissez Prise en charge des revendications](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Sur la page **Sélectionner une source de données**, cliquez sur **Entrer manuellement les données relatives à la partie de confiance**, puis sur **Suivant**.
    ![Entrer les informations sur la partie de confiance](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Sur la page **Spécifier le nom d’affichage**, saisissez un nom dans **Nom d’affichage**. Sous **Notes**, saisissez une description pour cette partie de confiance, puis cliquez sur **Suivant**.
    ![Spécifier le nom d’affichage et les notes](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  facultatif. Si vous disposez d’un certificat de chiffrement de jeton facultatif, allez sur la page **Configurer certificat**, cliquez sur **Parcourir** pour localiser votre fichier de certificat, puis cliquez sur **Suivant**.
    ![Configurer le certificat](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Sur la page **Configurer l’URL**, cochez la case **Activer la prise en charge du protocole WebSSO SAML 2.0**. Sous **URL du service SSO SAML 2.0 de la partie de confiance**, saisissez l’URL du point de terminaison de service Security Assertion Markup Language (SAML) pour cette partie de confiance, puis cliquez sur **Suivant**.  Pour **URL du service SSO SAML 2.0 de la partie de confiance**, collez la `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Remplacez {tenant} par le nom de votre client (par exemple, contosob2c.onmicrosoft.com) et remplacez {policy} par votre nom de stratégie d’extension (par exemple, B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Le nom de la stratégie est celui dont signup_or_signin hérite. Dans ce cas, il s’agit de : `B2C_1A_TrustFrameworkExtensions`.
    >Par exemple l’URL peut être :   https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![URL du service SSO SAML 2.0 de la partie de confiance](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Sur la page **Configurer les identificateurs**, spécifiez la même URL qu’à l’étape précédente, cliquez sur **Ajouter** pour les ajouter à la liste, puis cliquez sur **Suivant**.
    ![Identificateurs d’approbation de partie de confiance](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Dans la page **Choisir la stratégie de contrôle d’accès**, sélectionnez une stratégie puis cliquez sur **Suivant**.
    ![Choisir la stratégie de contrôle d’accès](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Sur la page **Prêt à ajouter l’approbation**, passez en revue les paramètres, puis cliquez sur **Suivant** pour enregistrer vos informations d’approbation de partie de confiance.
    ![Enregistrer vos informations d’approbation de partie de confiance](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Sur la page **Terminer**, cliquez sur **Fermer**, cette action affiche automatiquement la boîte de dialogue **Modifier les règles de revendication**.
    ![Modifier les règles de revendication](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Cliquez sur **Ajouter une règle**.  
      ![Ajouter une nouvelle règle](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  Dans **Modèle de règle de revendication**, sélectionnez **Envoyer les attributs LDAP en tant que revendications**.
    ![Sélectionner la règle de modèle « Envoyer les attributs LDAP comme des revendications »](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Fournissez le **Nom de règle de revendication**. Pour le **Magasin d’attributs** sélectionnez **Sélectionner Active Directory**, ajoutez les revendications suivantes, puis cliquez sur **Terminer** et **OK**.
    ![Définir les propriétés de règle](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  Dans le gestionnaire de serveur, sélectionnez **Approbations de partie de confiance** , puis sélectionnez l’approbation de partie de confiance vous avez créée, puis cliquez sur **Propriétés**.
    ![Modifier les propriétés de la partie de confiance](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Sur la fenêtre de propriétés d’approbation de partie de confiance (B2C Demo), cliquez sur l’onglet **Signature** puis cliquez sur **Ajouter**.  
    ![Définir signature](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Ajoutez votre certificat de signature (fichier .cert sans clé privée).  
    ![Ajouter votre certificat de signature](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  Sur la fenêtre de propriétés d’approbation de partie de confiance (B2C Demo), cliquez sur l’onglet **Avancé** et modifiez le **Algorithme de hachage sécurisé** sur **SHA-1**, puis cliquez sur **Ok**.  
    ![Définir l’algorithme de hachage sécurisé SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Ajouter la clé d’application de compte ADFS à Azure AD B2C
La fédération avec les comptes ADFS requiert une clé secrète client pour le compte ADFS pour accorder la confiance à Azure AD B2C au nom de l’application. Vous devez enregistrer votre certificat ADFS dans votre client Azure AD B2C. 

1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Télécharger**.
5.  Pour **Nom**, utilisez `ADFSSamlCert`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans le Télécharger fichier,** sélectionnez votre fichier de certificat .pfx avec la clé privée. Remarque : ce certificat (avec la clé privée) doit être le même que celui qui délivré et utilisé pour la partie de confiance d’ADFS.
![Télécharger la clé de stratégie](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Cliquez sur **Créer**
8.  Vérifiez que vous avez créé la clé `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Ajouter un fournisseur de revendications à une stratégie d’extension
Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte ADFS, vous devez définir le compte ADFS comme fournisseur de revendications. En d’autres termes, vous devez spécifier un point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez ADFS comme fournisseur de revendications, en ajoutant le nœud `<ClaimsProvider>` dans votre fichier de stratégie d’extension :

1. Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.
2. Recherchez la section `<ClaimsProviders>`
3. Ajoutez l’extrait XML suivant sous l’élément `ClaimsProviders` et remplacez `identityProvider` par votre serveur DNS (valeur arbitraire qui indique votre domaine), puis enregistrez le fichier. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Inscription du fournisseur de revendications de compte ADFS à un parcours utilisateur Inscription ou Connexion
À ce stade, le fournisseur d’identité a été configuré.  Toutefois, il n’est disponible dans aucun des écrans d’inscription/de connexion. Maintenant vous devez ajouter le fournisseur d’identité du compte AD FS au parcours `SignUpOrSignIn` de votre utilisateur. Pour le rendre disponible, nous créons un doublon d’un modèle de parcours utilisateur existant.  Ensuite, nous le modifions pour qu’il comporte le fournisseur d’identité ADFS :

>[!NOTE]
>Si vous avez précédemment copié l’élément `<UserJourneys>` à partir du fichier de base de votre stratégie vers le fichier d’extension (TrustFrameworkExtensions.xml), vous pouvez ignorer cette section.

1.  Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2.  Recherchez l’élément `<UserJourneys>` et copiez la totalité du contenu du nœud `<UserJourneys>`.
3.  Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
4.  Collez l’intégralité du contenu du nœud `<UserJournesy>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.

### <a name="display-the-button"></a>Afficher le bouton
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre.  L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` au compte ADFS, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément :

1.  Recherchez le nœud `<UserJourney>` comprenant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.
2.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec le compte ADFS pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications compte ADFS :

1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Assurez-vous que `Id` a la même valeur que celle de `TargetClaimsExchangeId` dans la section précédente.
> * Vérifiez que `TechnicalProfileReferenceId` est défini sur le profil de technique que vous avez créé plus haut (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Charger la stratégie sur un client
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Ouvrez le panneau **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**.
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="test-the-custom-policy-by-using-run-now"></a>Tester la stratégie personnalisée en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez être en mesure de vous connecter à l’aide de votre compte ADFS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Facultatif] Inscription du fournisseur de revendications de compte ADFS à un parcours utilisateur Modification de profil
Vous pouvez également ajouter le fournisseur d’identité du compte ADFS au parcours utilisateur `ProfileEdit` de votre utilisateur. Pour le rendre disponible, nous répétons les deux dernières étapes :

### <a name="display-the-button"></a>Afficher le bouton
1.  Ouvrez le fichier d’extension de votre stratégie (par exemple, TrustFrameworkExtensions.xml).
2.  Recherchez le nœud `<UserJourney>` comprenant `Id="ProfileEdit"` dans le parcours utilisateur que vous avez copié.
3.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
4.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action
1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Tester la stratégie personnalisée de modification de profil en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
2.  Ouvrez **B2C_1A_ProfileEdit**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez être en mesure de vous connecter à l’aide de votre compte ADFS.

## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets
Facultatif : nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée après avoir effectué la prise en main des stratégies personnalisées. [Exemples de fichiers de stratégie pour référence uniquement](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
