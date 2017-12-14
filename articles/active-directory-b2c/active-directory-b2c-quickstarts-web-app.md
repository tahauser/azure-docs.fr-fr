---
title: "Évaluer une application web activée pour Azure AD B2C | Microsoft Docs"
description: "Se connecter à la version d’évaluation, s’inscrire, modifier le profil et réinitialiser le mot de passe des parcours utilisateur à l’aide d’un environnement de test Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: bc56da695145f396a2899fb9dc7add3af9a549e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Évaluer une application web activée pour Azure AD B2C

Azure Active Directory B2C assure la gestion des identités cloud pour protéger votre application, votre entreprise et vos clients. Ce guide de démarrage rapide utilise un exemple d’application de liste de tâches pour illustrer :

> [!div class="checklist"]
> * La connexion avec une page de connexion personnalisée.
> * La connexion avec un fournisseur d’identité sociale.
> * La création et la gestion de votre compte et de votre profil utilisateur Azure AD B2C.
> * L’appel d’une API web sécurisée par Azure AD B2C.

## <a name="prerequisites"></a>Composants requis

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**. 
* Un compte de réseau social Facebook, Google, Microsoft ou Twitter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez ou clonez l’exemple d’application](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) à partir de GitHub.

## <a name="run-the-app-in-visual-studio"></a>Exécuter l’application dans Visual Studio

Dans l’exemple de dossier de projet de l’application, ouvrez la solution `B2C-WebAPI-DotNet.sln` dans Visual Studio. 

La solution est un exemple d’application de liste des tâches consistant en deux projets :

* **TaskWebApp** : application web MVC ASP.NET qui permet à un utilisateur de gérer les éléments de sa liste de tâches.  
* **TaskService** : un backend d’API web ASP.NET qui gère les opérations effectuées sur les éléments de la liste des tâches d’un utilisateur. L’application web appelle cette API web et affiche les résultats.

Pour ce guide de démarrage rapide, vous devez exécuter les projets `TaskWebApp` et `TaskService` simultanément. 

1. Dans le menu Visual Studio, sélectionnez **Projets > Définir les projets de démarrage...** . 
2. Sélectionnez la case d’option **Plusieurs projets de démarrage**.
3. Pour les deux projets, définissez le paramètre **Action** sur **Démarrer**. Cliquez sur **OK**.

![Définir la page de démarrage dans Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Sélectionnez **Déboguer > Démarrer le débogage** pour générer et exécuter les deux applications. Chaque application s’ouvre dans son propre onglet de navigateur :

`https://localhost:44316/` : cette page correspond à l’application web ASP.NET. Dans ce démarrage rapide, vous allez interagir directement avec cette application.
`https://localhost:44332/` : cette page correspond à l’API web appelée par l’application web ASP.NET.

## <a name="create-an-account"></a>Créer un compte

Cliquez sur le lien **S’inscrire / Se connecter** dans l’application web ASP.NET pour démarrer le workflow **d’inscription ou de connexion**. Lorsque vous créez un compte, vous pouvez utiliser un compte de fournisseur d’identité sociale existant ou un compte de messagerie. Pour ce démarrage rapide, utilisez un compte de fournisseur d’identité sociale provenant de Facebook, Google, Microsoft ou Twitter.

![Exemple d’application web ASP.NET](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>S’inscrire au moyen d’un fournisseur d’identité sociale

Pour vous inscrire au moyen d’un fournisseur d’identité sociale, cliquez sur le bouton en regard du fournisseur d’identité que vous souhaitez utiliser. 

![Fournisseur pour se connecter ou s’inscrire](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Vous devez vous authentifier (vous connecter) avec les informations d’identification de votre compte de réseau social et autoriser l’application à lire les informations depuis votre compte de réseau social. En accordant l’accès, l’application peut extraire des informations de profil du compte de réseau social, telles que votre nom et votre ville. 

Terminez le processus de connexion pour le fournisseur d’identité. Par exemple, si vous choisissez Twitter, entrez vos informations d’identification Twitter et cliquez sur **Connexion**.

![Authentification et autorisation au moyen d’un compte de réseau social](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Les détails du profil de votre nouveau compte Azure AD B2C sont préremplis avec les informations de votre compte de réseau social.

Mettez à jour les champs Nom d’affichage, Poste et Ville, puis cliquez sur **Continuer**.  Les valeurs que vous entrez sont utilisées pour le profil de votre compte d’utilisateur Azure AD B2C.

![Détails du profil d’inscription du nouveau compte](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Vous avez effectué les actions suivantes :

> [!div class="checklist"]
> * Authentification avec un fournisseur d’identité.
> * Création d’un compte d’utilisateur Azure AD B2C. 

## <a name="edit-your-profile"></a>Modifier votre profil

Azure Active Directory B2C fournit des fonctionnalités permettant aux utilisateurs de mettre à jour leurs profils. Dans la barre de menus de l’application web, cliquez sur le nom de votre profil et sélectionnez **Modifier le profil** pour modifier le profil que vous avez créé.

![Modifier le profil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Modifiez votre **Nom d’affichage** et votre **Ville**.  Cliquez sur **Continuer** pour mettre à jour votre profil.

![Mettre à jour le profil](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Notez que votre nom d’affichage en haut à droite de la page est le nom mis à jour. 

## <a name="access-a-secured-web-api-resource"></a>Accéder à une ressource de l’API web sécurisée

Cliquez sur **Liste de tâches** pour entrer et modifier des éléments de votre liste des tâches. L’application web ASP.NET inclut un jeton d’accès dans la requête émise à la ressource d’API web pour demander l’autorisation d’effectuer des opérations sur les éléments de la liste des tâches de l’utilisateur. 

Entrez du texte dans la zone de texte **Nouvel élément**. Cliquez sur **Ajouter** pour appeler l’API web sécurisée Azure AD B2C qui ajoute un élément de liste des tâches.

![Ajouter un élément de la liste des tâches](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

Vous avez utilisé votre compte d’utilisateur Azure AD B2C pour effectuer un appel autorisé à une API web sécurisée Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

L’exemple utilisé dans ce démarrage rapide peut être utilisé pour essayer d’autres scénarios Azure AD B2C, notamment :

* Création d’un compte local avec une adresse e-mail.
* Réinitialisation du mot de passe de votre compte local.

Si vous êtes prêt à aller plus loin pour créer votre propre locataire Azure AD B2C et configurer l’exemple pour qu’il s’exécute avec votre propre locataire, essayez le didacticiel suivant.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET avec inscription, connexion, modification du profil et réinitialisation du mot de passe Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)