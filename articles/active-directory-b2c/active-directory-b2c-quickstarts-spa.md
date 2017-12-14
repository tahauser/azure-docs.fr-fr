---
title: "Évaluer une application à page unique Azure AD B2C | Microsoft Docs"
description: "Se connecter à la version d’évaluation, s’inscrire, modifier le profil et réinitialiser le mot de passe des parcours utilisateur à l’aide d’un environnement de test Azure AD B2C"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: ba8ee4657309ab2a541f4c7b3fd4879542eee63c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>Évaluer une application à page unique configurée avec Azure AD B2C

## <a name="about-this-sample"></a>À propos de cet exemple

Azure Active Directory B2C assure la gestion des identités cloud pour protéger votre application, votre entreprise et vos clients.  Ce guide de démarrage rapide utilise un exemple d’application à une seule page pour illustrer les points suivants :

* Utilisation de la stratégie **S’inscrire ou se connecter** pour créer ou se connecter à un fournisseur d’identité sociale, ou à un compte local avec une adresse e-mail. 
* **Appel d’une API** pour récupérer votre nom d’affichage à partir d’une ressource sécurisée Azure AD B2C.

## <a name="prerequisites"></a>Composants requis

* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - **Développement web et ASP.NET**

* Installez [Node.js](https://nodejs.org/en/download/)

* Un compte de réseau social Facebook, Google, Microsoft ou Twitter. Si vous n’avez pas de compte de réseau social, une adresse de messagerie valide est nécessaire.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez ou clonez l’exemple d’application](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) à partir de GitHub.

## <a name="run-the-sample-application"></a>Exécuter l’exemple d’application

Pour exécuter cet exemple à partir de l’invite de commandes Node.js : 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

La fenêtre de console affiche le numéro de port de l’application web en cours d’exécution sur votre ordinateur.

```
Listening on port 6420...
```

Ouvrez `http://localhost:6420` dans un navigateur web pour accéder à l’application web.


![Exemple d’application dans un navigateur](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Créer un compte

Cliquez sur le bouton **Connexion** pour démarrer le flux de travail **S’inscrire ou se connecter** d’Azure AD B2C. Lors de la création d’un compte, vous pouvez utiliser un compte e-mail ou un compte de fournisseur d’identité sociale existant.

### <a name="sign-up-using-a-social-identity-provider"></a>S’inscrire au moyen d’un fournisseur d’identité sociale

Pour vous inscrire au moyen d’un fournisseur d’identité sociale, cliquez sur le bouton en regard du fournisseur d’identité que vous souhaitez utiliser. Si vous préférez utiliser une adresse e-mail, passez à la section [S’inscrire au moyen d’une adresse e-mail](#sign-up-using-an-email-address).

![Fournisseur pour se connecter ou s’inscrire](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

Vous devez vous authentifier (vous connecter) avec les informations d’identification de votre compte de réseau social et autoriser l’application à lire les informations depuis votre compte de réseau social. En accordant l’accès, l’application peut extraire des informations de profil du compte de réseau social, telles que votre nom et votre ville. 

![Authentification et autorisation au moyen d’un compte de réseau social](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

Les détails du profil de votre nouveau compte sont préremplis avec les informations de votre compte de réseau social. 

![Détails du profil d’inscription du nouveau compte](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

Mettez à jour les champs Nom d’affichage, Poste et Ville, puis cliquez sur **Continuer**.  Les valeurs que vous entrez sont utilisées pour le profil de votre compte d’utilisateur Azure AD B2C.

Vous avez créé un compte d’utilisateur Azure AD B2C qui utilise un fournisseur d’identité. 

Étape suivante : Section [Appeler une ressource](#call-a-resource).

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

Si vous choisissez de ne pas vous authentifier à l’aide d’un compte de réseau social, vous pouvez créer un compte d’utilisateur Azure AD B2C en utilisant simplement une adresse e-mail valide. Un compte d’utilisateur local Azure AD B2C utilise Azure Active Directory comme fournisseur d’identité. Pour utiliser votre adresse e-mail, cliquez sur le lien **Vous ne possédez pas encore de compte ? Inscrivez-vous maintenant**.

![Connexion ou inscription au moyen d’une adresse e-mail](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

Entrez une adresse e-mail valide et cliquez sur **Envoyer le code de vérification**. Une adresse e-mail valide est requise pour recevoir le code de vérification d’Azure AD B2C. 

Entrez le code de vérification reçu par e-mail, puis cliquez sur **Vérifier le code**.

Ajoutez vos informations de profil et cliquez sur **Créer**.

![Inscription avec un nouveau compte au moyen d’une adresse e-mail](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

Vous avez créé un compte d’utilisateur local Azure AD B2C.

## <a name="call-a-resource"></a>Appeler une ressource

Une fois connecté, vous pouvez cliquer sur le bouton **Call Web API** (Appeler une API web) pour que votre nom d’affichage soit retourné par l’appel de l’API web sous forme d’un objet JSON. 

![Réponse de l’API web](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à créer votre propre locataire Azure AD B2C et à configurer l’exemple pour qu’il s’exécute avec votre locataire. 

> [!div class="nextstepaction"]
> [Créer un locataire Azure Active Directory B2C dans le portail Azure](active-directory-b2c-get-started.md)