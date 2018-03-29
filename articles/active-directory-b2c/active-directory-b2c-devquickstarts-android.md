---
title: 'Azure Active Directory B2C : Obtenir un jeton à l’aide d’une application Android | Microsoft Docs'
description: Cet article vous montre comment créer une application Android qui utilise AppAuth avec Azure Active Directory B2C pour gérer les identités utilisateur et authentifier les utilisateurs.
services: active-directory-b2c
documentationcenter: android
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.topic: article
ms.date: 03/06/2017
ms.author: davidmu
ms.openlocfilehash: 6c4c9359571882fbbea4e7701305e30e0f49f460
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure Active Directory B2C : Se connecter à l’aide d’une application Android

La plateforme d’identité Microsoft utilise des normes ouvertes telles que OAuth2 et OpenID Connect. Cela permet aux développeurs de tirer parti de toute bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons rédigé quelques procédures pas à pas comme celle-ci, afin d’expliquer la configuration des bibliothèques tierces pour se connecter à la plateforme d’identité Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) seront en mesure de se connecter à la plateforme Microsoft Identity.

> [!WARNING]
> Microsoft ne fournit pas de correctifs pour les bibliothèques tierces et ne les a pas vérifiées. Cet exemple utilise une bibliothèque tierce appelée AppAuth dont la compatibilité a été testée dans des scénarios de base avec Azure AD B2C. Les problèmes et les demandes de fonctionnalités doivent être soumis au projet open source de la bibliothèque. Pour plus d’informations, consultez [cet article](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).  
>
>

Si vous découvrez OAuth2 ou OpenID Connect, cet exemple de configuration n’est peut-être pas très parlant pour vous. Nous vous recommandons de consulter une brève [vue d’ensemble du protocole que nous avons décrit ici](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

## <a name="create-an-application"></a>Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD les informations nécessaires pour communiquer avec votre application en toute sécurité. Pour créer une application mobile, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

* Incluez un **client natif** dans l’application.
* Copiez l’ **ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.
* Configurez un **URI de redirection** de client natif (par exemple, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Vous en aurez besoin ultérieurement.

## <a name="create-your-policies"></a>Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient une seule expérience liée à l’identité : une connexion et une inscription combinées. Vous devez créer cette stratégie, comme décrit dans l’[article de référence sur les stratégies](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Lorsque vous créez la stratégie, prenez soin de :

* Choisir le **nom d’affichage** et un attribut d’inscription dans votre stratégie.
* Choisissez le **nom d’affichage** et **l’ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
* Copiez le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`.  Le nom de stratégie vous sera demandé ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos stratégies créées, vous pouvez générer votre application.

## <a name="download-the-sample-code"></a>Télécharger l’exemple de code

Nous avons fourni un exemple fonctionnel qui utilise AppAuth avec Azure AD B2C [sur GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Vous pouvez télécharger le code et l’exécuter. Vous pouvez prendre rapidement en main votre application à l’aide de votre propre configuration Azure AD B2C en suivant les instructions du fichier [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

L’exemple est une modification de l’exemple fourni par [AppAuth](https://openid.github.io/AppAuth-Android/). Consultez la page correspondante pour en savoir plus sur AppAuth et ses fonctionnalités.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modification de votre application pour utiliser Azure AD B2C avec AppAuth

> [!NOTE]
> AppAuth prend en charge Android API 16 (Jellybean) et versions ultérieures. Nous recommandons d’utiliser API 23 et versions ultérieures.
>

### <a name="configuration"></a>Configuration

Vous pouvez configurer la communication avec Azure AD B2C en spécifiant l’URI de détection ou en spécifiant les URI du point de terminaison d’autorisation et du point de terminaison de jeton. Dans les deux cas, vous aurez besoin des informations suivantes :

* ID client (par ex., contoso.onmicrosoft.com)
* Nom de la stratégie (par ex., B2C\_1\_SignUpIn)

Si vous choisissez de détecter automatiquement les URI du point de terminaison d’autorisation et de jeton URI, vous devrez extraire des informations de l’URI de détection. L’URI de détection peut être généré en remplaçant l’\_ID client et le nom de la stratégie\_ dans l’URL suivante :

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Vous pouvez alors obtenir les URI des points de terminaison d’autorisation et de jeton et créer un objet AuthorizationServiceConfiguration en exécutant la commande suivante :

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Plutôt que d’utiliser la détection pour obtenir les URI des points de terminaison d’autorisation et de jeton, vous pouvez également les spécifier explicitement en remplaçant l’\_ID client et le nom de la\_stratégie dans l’URL ci-dessous :

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Exécutez le code suivant pour créer votre objet AuthorizationServiceConfiguration :

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorisation

Après la configuration ou la récupération d’une configuration de service d’autorisation, une demande d’autorisation peut être créée. Pour créer la demande, vous aurez besoin des informations suivantes :

* ID client (par ex. 00000000-0000-0000-0000-000000000000)
* URI de redirection avec un schéma personnalisé (par ex., com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Les deux éléments doivent avoir été enregistrés là où vous avez [inscrit votre application](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Veuillez vous reporter au [guide d’AppAuth](https://openid.github.io/AppAuth-Android/) pour le reste du processus. Si vous avez besoin de prendre rapidement en main une application, consultez [notre exemple](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Suivez les étapes du fichier [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) pour entrer votre propre configuration Azure AD B2C.

Nous sommes ouverts aux commentaires et suggestions ! Si vous avez des difficultés avec cette rubrique, ou si vous avez des conseils pour améliorer ce contenu, faites-nous part de vos commentaires en bas de la page. En cas de demandes liées aux fonctionnalités, utilisez [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

