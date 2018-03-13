---
title: "Azure Active Directory B2C : Bien démarrer avec les stratégies personnalisées | Microsoft Docs"
description: "Bien démarrer avec les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 86b86c7c670b34b4f3303adbcb55aff8d5edb53a
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Une fois que vous avez effectué les étapes de cet article, votre stratégie personnalisée prend en charge l’inscription ou la connexion de « comptes locaux » via une adresse e-mail et un mot de passe. Vous préparez également votre environnement à l’ajout de fournisseurs d’identité (par exemple Facebook ou Azure AD). Nous vous encourageons à effectuer ces étapes avant de découvrir d’autres utilisations de l’infrastructure d’expérience d’identité Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>configuration requise

Avant de continuer, vérifiez que vous disposez d’un locataire Azure AD B2C, qui est pour l’ensemble de vos utilisateurs, applications, stratégies, etc. Si vous n’en avez pas déjà un, vous devez en [créer un locataire Azure AD B2C](active-directory-b2c-get-started.md). Nous encourageons fortement tous les développeurs à suivre les procédures pas à pas relatives aux stratégies intégrées d’Azure AD B2C et à configurer leurs applications avec des stratégies intégrées avant de continuer. Vos applications fonctionneront avec les deux types de stratégies après une modification mineure du nom de la stratégie pour appeler la stratégie personnalisée.

>[!NOTE]
>Pour accéder à la modification de la stratégie personnalisée, vous avez besoin d’un abonnement Azure valide lié à votre locataire. Si vous n’avez pas [lié votre client Azure AD B2C à un abonnement Azure](active-directory-b2c-how-to-enable-billing.md) ou si votre abonnement Azure est désactivé, le bouton Infrastructure d’expérience d’identité n’est pas disponible.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Ajouter à votre locataire B2C des clés de signature et de chiffrement utilisables par les stratégies personnalisées

1. Ouvrez le panneau **Infrastructure d’expérience d’identité** dans les paramètres de votre locataire Azure AD B2C.
2. Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3. S’il n’existe pas, créez B2C_1A_TokenSigningKeyContainer :<br>
    a. Sélectionnez **Ajouter**. <br>
    b. Sélectionnez **Générer**.<br>
    c. Pour **Nom**, utilisez `TokenSigningKeyContainer`. <br> 
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.<br>
    d. Pour **Type de clé**, utilisez **RSA**.<br>
    e. Pour **Dates**, utilisez les valeurs par défaut. <br>
    f. Pour **Utilisation de la clé**, utilisez **Signature**.<br>
    g. Sélectionnez **Créer**.<br>
4. S’il n’existe pas, créez B2C_1A_TokenEncryptionKeyContainer :<br>
 a. Sélectionnez **Ajouter**.<br>
 b. Sélectionnez **Générer**.<br>
 c. Pour **Nom**, utilisez `TokenEncryptionKeyContainer`. <br>
   Il est possible que le préfixe `B2C_1A`_ soit ajouté automatiquement.<br>
 d. Pour **Type de clé**, utilisez **RSA**.<br>
 e. Pour **Dates**, utilisez les valeurs par défaut.<br>
 f. Pour **Utilisation de la clé**, utilisez **Chiffrement**.<br>
 g. Sélectionnez **Créer**.<br>
5. Créez B2C_1A_FacebookSecret. <br>
Si vous disposez déjà d’un secret pour l’application Facebook, ajoutez-le en tant que clé de stratégie à votre client. Dans le cas contraire, vous devez créer la clé avec une valeur d’espace réservé afin que vos stratégies puissent être validées.<br>
 a. Sélectionnez **Ajouter**.<br>
 b. Pour **Options**, utilisez **Manuel**.<br>
 c. Pour **Nom**, utilisez `FacebookSecret`. <br>
 Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.<br>
 d. Dans la zone **Secret**, entrez votre FacebookSecret à partir de developers.facebook.com ou entrez `0` comme espace réservé. *Il ne s’agit pas de votre ID d’application Facebook.* <br>
 e. Pour **Utilisation de la clé**, utilisez **Signature**. <br>
 f. Cliquez sur **Créer** et confirmez la création.

## <a name="register-identity-experience-framework-applications"></a>Inscrire les applications de l’infrastructure d’expérience d’identité

Azure AD B2C vous impose d’inscrire deux applications supplémentaires qui sont utilisées par le moteur pour inscrire et connecter les utilisateurs.

>[!NOTE]
>Vous devez créer deux applications permettant de se connecter à l’aide de comptes locaux : IdentityExperienceFramework (une application web) et ProxyIdentityExperienceFramework (une application native) avec des autorisations déléguées de l’application IdentityExperienceFramework. Les comptes locaux existent uniquement dans votre client. Vos utilisateurs se connectent avec une combinaison unique adresse e-mail/mot de passe pour accéder à vos applications inscrites auprès du locataire.

### <a name="create-the-identityexperienceframework-application"></a>Créer l’application IdentityExperienceFramework

1. Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Ouvrez le panneau **Azure Active Directory** (pas le panneau **Azure AD B2C**). Vous devrez peut-être sélectionner **Plus de services** pour le trouver.
3. Sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription d’application**.
   * Pour **Nom**, utilisez `IdentityExperienceFramework`.
   * Pour **Type d’application**, utilisez **Application/API web**.
   * Pour **URL de connexion**, utilisez `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, où `yourtenant` est le nom de domaine de votre locataire Azure AD B2C.
5. Sélectionnez **Créer**.
6. Une fois qu’elle est créée, sélectionnez l’application nouvellement créée **IdentityExperienceFramework**.<br>
   * Sélectionner **Propriétés**.<br>
   * Copiez l’ID d’application et enregistrez-le pour une utilisation ultérieure.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Créer l’application ProxyIdentityExperienceFramework

1. Sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription d’application**.
   * Pour **Nom**, utilisez `ProxyIdentityExperienceFramework`.
   * Pour **Type d’application**, utilisez **Native**.
   * Pour **URI de redirection**, utilisez `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, où `yourtenant` est votre locataire Azure AD B2C.
1. Sélectionnez **Créer**.
1. Une fois qu’elle a été créée, sélectionnez l’application **ProxyIdentityExperienceFramework**.<br>
   * Sélectionner **Propriétés**. <br>
   * Copiez l’ID d’application et enregistrez-le pour une utilisation ultérieure.
1. Sélectionnez **Autorisations requises**.
1. Sélectionnez **Ajouter**.
1. Sélectionnez **Sélectionner une API**.
1. Recherchez le nom IdentityExperienceFramework. Sélectionnez **IdentityExperienceFramework** dans les résultats puis cliquez sur **Sélectionner**.
1. Activez la case à cocher en regards de **Accéder à IdentityExperienceFramework** puis cliquez sur **Sélectionner**.
1. Sélectionnez **Terminé**.
1. Sélectionnez **Accorder des autorisations** puis confirmez en sélectionnant **Oui**.

## <a name="download-starter-pack-and-modify-policies"></a>Télécharger le pack de démarrage et modifier les stratégies

Les stratégies personnalisées sont un ensemble de fichiers XML qui doivent être chargés sur votre client Azure AD B2C. Nous proposons des packs de démarrage pour vous permettre d’être rapidement opérationnel. Chaque pack de démarrage de la liste suivante contient le plus petit nombre possible de profils techniques et de parcours utilisateur nécessaires pour réaliser les scénarios décrits :
 * LocalAccounts. Permet d’utiliser uniquement les comptes locaux.
 * SocialAccounts. Permet d’utiliser uniquement les comptes sociaux (ou fédérés).
 * **SocialAndLocalAccounts**. Nous utilisons ce fichier pour la procédure pas à pas.
 * SocialAndLocalAccountsWithMFA. Les options d’authentification sociale, locale et multifacteur sont incluses ici.

Chaque pack de démarrage contient :

* Le [fichier de base](active-directory-b2c-overview-custom.md#policy-files) de la stratégie. Il n’y a que peu de modifications à apporter la base.
* Le [fichier d’extension](active-directory-b2c-overview-custom.md#policy-files) de la stratégie.  C’est sur ce fichier que portent la plupart des modifications de la configuration.
* Les [fichiers de partie de confiance](active-directory-b2c-overview-custom.md#policy-files) sont des fichiers propres à chaque tâche, appelés par l’application.

>[!NOTE]
>Si votre éditeur XML prend en charge la validation, validez la conformité des fichiers au schéma XML TrustFrameworkPolicy_0.3.0.0.xsd, qui se trouve dans le dossier racine du pack de démarrage. La validation du schéma XML identifie les erreurs avant le chargement.

 C’est parti :

1. Téléchargez active-directory-b2c-custom-policy-starterpack depuis GitHub. [Téléchargez le fichier zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou exécutez

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Ouvrez le dossier SocialAndLocalAccounts.  Le fichier de base (TrustFrameworkBase.xml) de ce dossier comporte le contenu nécessaire aux comptes locaux et aux comptes sociaux/d’entreprise. Le contenu social n’interfère pas avec les étapes d’opérationnalisation des comptes locaux.
3. Ouvrez TrustFrameworkBase.xml. Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.
4. Dans l’élément `TrustFrameworkPolicy` racine, changez la valeur des attributs `TenantId` et `PublicPolicyUri` en remplaçant `yourtenant.onmicrosoft.com` par le nom de domaine de votre locataire Azure AD B2C :
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` est le nom de la stratégie qui s’affiche sur le portail et le nom par lequel les autres fichiers de stratégie font référence à celui-ci.

5. Enregistrez le fichier .
6. Ouvrez TrustFrameworkExtensions.xml. Faites les deux mêmes modifications en remplaçant `yourtenant.onmicrosoft.com` par votre locataire Azure AD B2C. Procédez au même remplacement dans l’élément `<TenantId>`, ce qui fait un total de trois modifications. Enregistrez le fichier .
7. Ouvrez SignUpOrSignIn.xml. Faites les mêmes modifications en remplaçant `yourtenant.onmicrosoft.com` par votre locataire Azure AD B2C à trois endroits. Enregistrez le fichier .
8. Ouvrez les fichiers de réinitialisation du mot de passe et de modification du profil. Faites les mêmes modifications en remplaçant `yourtenant.onmicrosoft.com` par votre locataire Azure AD B2C à trois endroits dans chaque fichier. Enregistrez les fichiers.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Ajouter les ID d’application à votre stratégie personnalisée
Ajoutez les ID d’application au fichier d’extensions (`TrustFrameworkExtensions.xml`) :

1. Dans le fichier d’extensions (TrustFrameworkExtensions.xml), recherchez l’élément `<TechnicalProfile Id="login-NonInteractive">`.
2. Remplacez les deux instances de `IdentityExperienceFrameworkAppId` par l’ID de l’application d’infrastructure d’expérience d’identité que vous avez créée précédemment. Voici un exemple : 

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Remplacez les deux instances de `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application Infrastructure d’expérience d’identité de proxy que vous avez créée précédemment.
4. Enregistrez votre fichier d’extensions.

## <a name="upload-the-policies-to-your-tenant"></a>Charger les stratégies vers votre client

1. Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau **Azure AD B2C**.
2. Sélectionnez **Infrastructure d’expérience d’identité**.
3. Sélectionnez **Charger la stratégie**.

    >[!WARNING]
    >Les fichiers de stratégie personnalisée doivent être chargés dans l’ordre suivant :

1. Chargez TrustFrameworkBase.xml.
2. Chargez TrustFrameworkExtensions.xml.
3. Chargez SignUpOrSignin.xml.
4. Chargez vos autres fichiers de stratégie.

Lorsqu’un fichier est chargé, son nom est précédé de `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Tester la stratégie personnalisée en utilisant Exécuter maintenant

1. Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

   >[!NOTE]
   >**Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Les applications doivent être inscrites dans le locataire B2C, en utilisant la sélection du menu **Applications** dans Azure AD B2C, ou en utilisant l’infrastructure d’expérience d’identité pour appeler les stratégies intégrées et personnalisées. Seule une inscription par application est nécessaire.<br><br>
   Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).  

2. Ouvrez B2C_1A_signup_signin, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.

3. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.

4. Connectez-vous avec le même compte pour vérifier que votre configuration est correcte.

>[!NOTE]
>Souvent, la mauvaise configuration de l’application IdentityExperienceFramework est à l’origine des échecs de connexion.


## <a name="next-steps"></a>Étapes suivantes

### <a name="add-facebook-as-an-identity-provider"></a>Ajouter Facebook en tant que fournisseur d’identité
Pour configurer Facebook :
1. [Configurez une application Facebook dans developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Ajoutez le secret d’application Facebook à votre locataire Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. Dans le fichier de stratégie TrustFrameworkExtensions, remplacez la valeur de `client_id` par l’ID d’application Facebook :

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Chargez le fichier de stratégie TrustFrameworkExtensions.xml sur votre client.
5. Testez en utilisant **Exécuter maintenant** ou en appelant la stratégie directement à partir de votre application inscrite.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Ajouter Azure Active Directory en tant que fournisseur d’identité
Le fichier de base utilisé dans ce guide de démarrage contient déjà une partie du contenu dont vous avez besoin pour ajouter d’autres fournisseurs d’identité. Pour plus d’informations sur la configuration des connexions, consultez l’article [Azure Active Directory B2C : se connecter en utilisant des comptes Azure AD](active-directory-b2c-setup-aad-custom.md).

Pour une vue d’ensemble de stratégies personnalisées d’Azure AD B2C qui utilisent l’infrastructure d’expérience d’identité, consultez l’article [Azure Active Directory B2C : stratégies personnalisées](active-directory-b2c-overview-custom.md). 
