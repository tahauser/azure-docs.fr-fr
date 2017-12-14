---
title: "Évaluer une application de bureau Azure AD B2C | Microsoft Docs"
description: "Utiliser un environnement de test Azure AD B2C pour évaluer les procédures utilisateur de connexion, d’inscription, de modification d’un profil et de réinitialisation du mot de passe"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 51f5643f0bd975beb939c2d5a8853810fb609ec9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>Évaluer une application de bureau configurée avec Azure AD B2C

Azure Active Directory B2C assure la gestion des identités cloud pour vous aider à protéger votre application, votre entreprise et vos clients.  Ce guide de démarrage rapide utilise un exemple d’application de bureau Windows Presentation Foundation (WPF) pour illustrer :

* l’utilisation d’une stratégie **d’inscription ou de connexion** pour créer ou se connecter avec un fournisseur d’identité sociale ou un compte local à l’aide d’une adresse e-mail ; 
* **Appel d’une API** pour récupérer votre nom d’affichage à partir d’une ressource sécurisée Azure AD B2C.

## <a name="prerequisites"></a>Composants requis

* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - **Développement de bureau .NET**

* Un compte de réseau social Facebook, Google, Microsoft ou Twitter. Si vous n’avez pas de compte de réseau social, une adresse de messagerie valide est nécessaire.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez ou clonez l’exemple d’application](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) à partir de GitHub.

## <a name="run-the-app-in-visual-studio"></a>Exécuter l’application dans Visual Studio

Dans l’exemple de dossier de projet de l’application, ouvrez la solution `active-directory-b2c-wpf.sln` dans Visual Studio. 

Sélectionnez **Déboguer > Démarrer le débogage** pour générer et exécuter l’application. 

## <a name="create-an-account"></a>Créer un compte

Cliquez sur **Se connecter** pour démarrer le workflow **Inscription ou connexion**. Lorsque vous créez un compte, vous pouvez utiliser un compte de fournisseur d’identité sociale existant ou un compte de messagerie.

![Exemple d’application](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>S’inscrire au moyen d’un fournisseur d’identité sociale

Pour vous inscrire au moyen d’un fournisseur d’identité sociale, cliquez sur le bouton en regard du fournisseur d’identité que vous souhaitez utiliser. Si vous préférez utiliser une adresse e-mail, passez à la section [S’inscrire au moyen d’une adresse e-mail](#sign-up-using-an-email-address).

![Fournisseur pour se connecter ou s’inscrire](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

Vous devez vous authentifier (vous connecter) avec les informations d’identification de votre compte de réseau social et autoriser l’application à lire les informations depuis votre compte de réseau social. En accordant l’accès, l’application peut extraire des informations de profil du compte de réseau social, telles que votre nom et votre ville. 

![Authentification et autorisation au moyen d’un compte de réseau social](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

Les informations de profil de votre nouveau compte sont déjà prérenseignées à partir des informations obtenues sur votre compte de réseau social. Modifier les détails si vous le souhaitez, puis cliquez sur **Continuer**.

![Informations de profil d’inscription du nouveau compte](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

Vous venez de créer un compte d’utilisateur Azure AD B2C qui utilise un fournisseur d’identité. Après la connexion, le jeton d’accès apparaît dans la zone de texte *Informations sur le jeton*. Le jeton d’accès est utilisé lors de l’accès à la ressource de l’API.

![Jeton d’autorisation](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

Étape suivante : [passez à la section Modifier votre profil](#edit-your-profile).

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

Si vous choisissez de ne pas vous authentifier à l’aide d’un compte de réseau social, vous pouvez créer un compte d’utilisateur Azure AD B2C en utilisant simplement une adresse e-mail valide. Un compte d’utilisateur local Azure AD B2C utilise Azure Active Directory comme fournisseur d’identité. Pour utiliser votre adresse e-mail, cliquez sur le lien **Vous ne possédez pas encore de compte ? Inscrivez-vous maintenant**.

![Connexion ou inscription au moyen d’une adresse e-mail](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

Entrez une adresse e-mail valide et cliquez sur **Envoyer le code de vérification**. Une adresse e-mail valide est requise pour recevoir le code de vérification d’Azure AD B2C.

Entrez le code de vérification reçu par e-mail, puis cliquez sur **Vérifier le code**.

Ajoutez vos informations de profil et cliquez sur **Créer**.

![Inscription avec un nouveau compte au moyen d’une adresse e-mail](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

Vous venez de créer un nouveau compte d’utilisateur local Azure AD B2C. Après la connexion, le jeton d’accès apparaît dans la zone de texte *Informations sur le jeton*. Le jeton d’accès est utilisé lors de l’accès à la ressource de l’API.

![Jeton d’autorisation](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>Modifier votre profil

Azure Active Directory B2C fournit des fonctionnalités permettant aux utilisateurs de mettre à jour leurs profils. Cliquez sur **Modifier le profil** pour modifier le profil que vous avez créé.

![Modifier le profil](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

Choisissez le fournisseur d’identité associé au compte que vous avez créé. Par exemple, si vous avez utilisé Twitter comme fournisseur d’identité lors de la création de votre compte, choisissez Twitter pour modifier les informations du profil associé.

![Choisir un fournisseur associé au profil à modifier](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

Modifiez votre **Nom d’affichage** ou votre **Ville**. 

![Mettre à jour le profil](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

Un nouveau jeton d’accès apparaît dans la zone de texte *Informations sur le jeton*. Si vous souhaitez vérifier les modifications apportées à votre profil, copiez et collez le jeton d’accès dans le décodeur de jeton https://jwt.ms.

![Jeton d’autorisation](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>Accéder à une ressource

Cliquez sur **Appeler l’API** pour envoyer une requête à la ressource sécurisée Azure AD B2C https://fabrikamb2chello.azurewebsites.net/hello. 

![Appeler une API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

L’application inclut dans la requête le jeton d’accès affiché dans la zone de texte *Informations sur le jeton*. L’API renvoie le nom d’affichage contenu dans le jeton d’accès.

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à créer votre propre locataire Azure AD B2C et à configurer l’exemple pour qu’il s’exécute avec votre locataire. 

> [!div class="nextstepaction"]
> [Créer un locataire Azure Active Directory B2C dans le portail Azure](active-directory-b2c-get-started.md)
