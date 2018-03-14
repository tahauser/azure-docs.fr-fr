---
title: "Évaluer une application monopage activée pour Azure AD B2C"
description: "Démarrage rapide pour essayer une application monopage qui utilise Azure Active Directory B2C pour authentifier et inscrire des utilisateurs."
services: active-directory-b2c
documentationcenter: 
author: PatAltimore
manager: mtillman
ms.reviewer: saraford
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 2/13/2018
ms.author: patricka
ms.openlocfilehash: e659fd228c2294313a62b331c8e530b7d34073ac
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-test-drive-an-azure-ad-b2c-enabled-single-page-app"></a>Démarrage rapide : Évaluer une application monopage activée pour Azure AD B2C

Azure Active Directory (Azure AD) B2C assure la gestion des identités de cloud pour protéger votre application, votre entreprise et vos clients. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes des réseaux sociaux et de comptes d’entreprise à l’aide de protocoles standard ouverts.

Dans ce démarrage rapide, vous utilisez un exemple d’application monopage activée pour Azure AD B2C pour vous connecter à l’aide d’un fournisseur d’identité sociale et appeler une API web protégée par Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.
* Installez [Node.js](https://nodejs.org/en/download/)
* Un compte de réseau social Facebook, Google, Microsoft ou Twitter.

## <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="run-the-sample-application"></a>Exécuter l’exemple d’application

Pour exécuter cet exemple à partir de l’invite de commandes Node.js : 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

L’application Node.js génère le numéro de port qu’il écoute sur localhost.

```
Listening on port 6420...
```

Accédez à l’URL de l’application `http://localhost:6420` dans un navigateur web.

![Exemple d’application dans un navigateur](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Créer un compte

Cliquez sur le bouton **Connexion** pour démarrer le flux de travail **Inscription ou Connexion** d’Azure AD B2C basé sur une stratégie Azure AD B2C. 

L’exemple prend en charge plusieurs options d’inscription, notamment l’utilisation d’un fournisseur d’identité sociale ou la création d’un compte local à l’aide d’une adresse e-mail. Pour ce démarrage rapide, utilisez un compte de fournisseur d’identité sociale provenant de Facebook, Google, Microsoft ou Twitter. 

### <a name="sign-up-using-a-social-identity-provider"></a>S’inscrire au moyen d’un fournisseur d’identité sociale

Azure AD B2C présente une page de connexion personnalisée d’une marque fictive appelée Wingtip Toys pour l’exemple d’application web. 

1. Pour vous inscrire au moyen d’un fournisseur d’identité sociale, cliquez sur le bouton en regard du fournisseur d’identité que vous souhaitez utiliser.

    ![Fournisseur pour se connecter ou s’inscrire](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Vous vous authentifiez (vous connectez) avec les informations d’identification de votre compte de réseau social et autoriser l’application à lire les informations de votre compte de réseau social. En accordant l’accès, l’application peut extraire des informations de profil du compte de réseau social, telles que votre nom et votre ville. 

2. Terminez le processus de connexion pour le fournisseur d’identité. Par exemple, si vous choisissez Twitter, entrez vos informations d’identification Twitter et cliquez sur **Connexion**.

    ![Authentification et autorisation au moyen d’un compte de réseau social](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

    Les détails du profil de votre nouveau compte sont préremplis avec les informations de votre compte de réseau social. 

3. Mettez à jour les champs Nom d’affichage, Poste et Ville, puis cliquez sur **Continuer**.  Les valeurs que vous entrez sont utilisées pour le profil de votre compte d’utilisateur Azure AD B2C.

    Vous avez créé un compte d’utilisateur Azure AD B2C qui utilise un fournisseur d’identité. 

## <a name="access-a-protected-web-api-resource"></a>Accéder à une ressource de l’API web protégée

Cliquez sur le bouton **Call Web API** (Appeler une API web) pour que votre nom d’affichage soit retourné par l’appel de l’API web sous forme d’un objet JSON. 

![Réponse de l’API web](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

L’exemple d’application monopage inclut un jeton d’accès Azure AD dans la requête envoyée à la ressource de l’API web protégée pour effectuer l’opération permettant de retourner l’objet JSON.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser votre locataire Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels ou démarrages rapides Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre client Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé un exemple d’application ASP.NET activée pour Azure AD B2C pour vous connecter avec une page de connexion personnalisée, vous connecter avec un fournisseur d’identité sociale, créer un compte Azure AD B2C et appeler une API web protégée par Azure AD B2C. 

L’étape suivante consiste à créer votre propre locataire Azure AD B2C et à configurer l’exemple pour qu’il s’exécute avec votre locataire. 

> [!div class="nextstepaction"]
> [Créer un locataire Azure Active Directory B2C dans le portail Azure](active-directory-b2c-get-started.md)