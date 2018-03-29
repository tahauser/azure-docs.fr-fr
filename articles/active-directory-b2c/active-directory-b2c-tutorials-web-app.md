---
title: Utiliser Azure Active Directory B2C pour l’authentification utilisateur dans une application web ASP.NET
description: Didacticiel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur pour une application web ASP.NET.
services: active-directory-b2c
author: davidmu1
ms.author: davidmu
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: c2a52a387860de640e290746b25c164090819654
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-authenticate-users-with-azure-active-directory-b2c-in-an-aspnet-web-app"></a>Didacticiel : authentifier les utilisateurs avec Azure Active Directory B2C dans une application web ASP.NET

Ce didacticiel vous montre comment utiliser Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application web ASP.NET. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes des réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire un exemple d’application web ASP.NET dans votre locataire Azure AD B2C.
> * Créer des stratégies pour l’inscription et la connexion des utilisateurs, la modification d’un profil, et la réinitialisation d’un mot de passe.
> * Configurer l’exemple d’application web pour utiliser votre locataire Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


* Utiliser votre propre [locataire Azure AD B2C](active-directory-b2c-get-started.md)
* Installer [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.

## <a name="register-web-app"></a>Inscrire une API web

Les applications doivent être [inscrites](../active-directory/develop/active-directory-dev-glossary.md#application-registration) dans votre locataire avant qu’elles ne puissent recevoir des [jetons d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token) de la part de Azure Active Directory. L’inscription d’une application crée un [id d’application](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) pour celle-ci dans votre client. 

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre client Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Sélectionnez **Azure AD B2C** dans la liste des services du portail Azure.

2. Dans les paramètres B2C, cliquez sur **Applications**, puis sur **Ajouter**.

    Pour inscrire l’exemple d’application web dans votre locataire, utilisez les paramètres suivants :

    ![Ajouter une nouvelle application](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Mon exemple d’application web | Entrez un **nom** décrivant votre application aux consommateurs. | 
    | **Inclure une application/API web** | OUI | Sélectionnez **Oui** pour une application web. |
    | **Autoriser le flux implicite** | OUI | Sélectionnez **Oui** puisque l’application utilise la [connexion OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de réponse** | `https://localhost:44316` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre application. Dans ce didacticiel, l’exemple s’exécute localement (localhost) et écoute sur le port 44316. |
    | **Client natif** | Non  | Dans la mesure où il s’agit d’une application web et pas d’un client natif, sélectionnez Non. |

3. Cliquez sur **Créer** pour inscrire votre application.

Les applications inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre application web dans la liste. Le volet de propriétés de l’application web s’affiche.

![Propriétés de l’application web](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Notez **l’ID du client d’application**. Cet ID identifie l’application de manière unique. Il est nécessaire pour configurer l’application ultérieurement dans le didacticiel.

### <a name="create-a-client-password"></a>Créer un mot de passe client

Azure AD B2C utilise l’autorisation OAuth2 pour [les applications clientes](../active-directory/develop/active-directory-dev-glossary.md#client-application). Les applications web sont [les clients confidentiels](../active-directory/develop/active-directory-dev-glossary.md#web-client) et nécessitent une clé secrète client (mot de passe). L’ID du client d’application et la clé secrète client sont utilisés lorsque l’application web s’authentifie auprès d’Azure Active Directory. 

1. Sélectionnez la page Clés de l’application web inscrit et cliquez sur **Générer une clé**.

2. Cliquez sur **Enregistrer** pour afficher la clé.

    ![page générale des clés de l’application](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

La clé s’affiche une seule fois dans le portail. Il est important de la copier et d’enregistrer sa valeur. Vous avez besoin de cette valeur pour configurer votre application. Gardez la clé en sécurité. Ne la partagez pas publiquement.

## <a name="create-policies"></a>Création des stratégies

Une stratégie d’Azure AD B2C définit les flux de travail des utilisateurs. Par exemple, la connexion, l’inscription, le changement des mots de passe et la modification des profils sont des flux de travail courants.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Création d’une stratégie d’inscription ou de connexion

Pour inscrire des utilisateurs puis les connecter à l’application web, créez une **stratégie d’inscription ou de connexion**.

1. Dans la page du portail Azure AD B2C, sélectionnez **Stratégies d’inscription ou de connexion** et cliquez sur **Ajouter**.

    Pour configurer votre stratégie, utilisez les paramètres suivants :

    ![Ajouter une stratégie d’inscription ou de connexion](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | Entrez un **nom** pour la stratégie. Le nom de la stratégie est préfixé avec **b2c_1_**. Vous utilisez le nom complet de la stratégie **b2c_1_SiUpIn** dans l’exemple de code. | 
    | **Fournisseur d’identité** | Inscription par e-mail | Le fournisseur d’identité utilisé pour identifier l’utilisateur. |
    | **Attributs de l’inscription** | Nom d’affichage et Code Postal | Sélectionnez les attributs à collecter auprès de l'utilisateur pendant l'inscription. |
    | **Revendications de l’application** | Nom d’affichage, Code Postal, L’utilisateur est nouveau, ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/active-directory-dev-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token). |

2. Cliquez sur **Créer** pour créer votre stratégie. 

### <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil

Pour permettre aux utilisateurs de réinitialiser eux-mêmes les informations de leur profil utilisateur, créez une **stratégie de modification de profil**.

1. Dans la page du portail Azure AD B2C, sélectionnez **Stratégies de modification de profil** et cliquez sur **Ajouter**.

    Pour configurer votre stratégie, utilisez les paramètres suivants :

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | Entrez un **nom** pour la stratégie. Le nom de la stratégie est préfixé avec **b2c_1_**. Vous utilisez le nom complet de la stratégie **b2c_1_SiPe** dans l’exemple de code. | 
    | **Fournisseur d’identité** | Local Account SignIn | Le fournisseur d’identité utilisé pour identifier l’utilisateur. |
    | **Attributs de profil** | Nom d’affichage et Code Postal | Sélectionnez les attributs que les utilisateurs peuvent modifier durant la modification du profil. |
    | **Revendications de l’application** | Nom d’affichage, Code Postal, L’utilisateur est nouveau, ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/active-directory-dev-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token) après une modification de profil réussie. |

2. Cliquez sur **Créer** pour créer votre stratégie. 

### <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

Pour activer la réinitialisation du mot de passe sur votre application, vous devez créer une **stratégie de réinitialisation de mot de passe**. Cette stratégie décrit les expériences des clients lors de la réinitialisation du mot de passe et le contenu des jetons que l’application reçoit en cas d’opération réussie.

1. Dans la page du portail Azure AD B2C, sélectionnez **Stratégies de réinitialisation de mot de passe** et cliquez sur **Ajouter**.

    Pour configurer votre stratégie, utilisez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | Entrez un **nom** pour la stratégie. Le nom de la stratégie est préfixé avec **b2c_1_**. Vous utilisez le nom complet de la stratégie **b2c_1_SSPR** dans l’exemple de code. | 
    | **Fournisseur d’identité** | Réinitialiser le mot de passe à l’aide d’une adresse e-mail | Il s’agit du fournisseur d’identité utilisé pour identifier l’utilisateur. |
    | **Revendications de l’application** | ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/active-directory-dev-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token) après une réinitialisation réussie du mot de passe. |

2. Cliquez sur **Créer** pour créer votre stratégie. 

## <a name="update-web-app-code"></a>Mettre à jour le code d’application web

Maintenant que vous avez une application web inscrite et des stratégies créées, vous devez configurer votre application pour utiliser votre locataire Azure AD B2C. Dans ce didacticiel, vous configurez un exemple d’application web. 

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

L’exemple d’application web ASP.NET est une simple application de liste de tâches pour la création et la mise à jour d’une liste de tâches. L’application utilise [les composants d’intergiciel (middleware) Microsoft OWIN](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/) pour permettre aux utilisateurs de s’inscrire afin d’utiliser l’application dans votre locataire Azure AD B2C. En créant une stratégie d’Azure AD B2C, les utilisateurs peuvent utiliser un compte de réseaux sociaux ou créer un compte pour l’utiliser comme une identité afin d’accéder à l’application. 

L’exemple de solution contient deux projets :

**Exemple d’application web (TaskWebApp) :** application web permettant de créer et de modifier une liste des tâches. L’application web utilise la stratégie **Inscription ou connexion** pour inscrire ou connecter des utilisateurs.

**Exemple d’application d’API web (TaskService) :** API web qui prend en charge les fonctionnalités de la liste des tâches de création, de lecture, de mise à jour et de suppression. L’API web est protégée par Azure AD B2C et appelée par l’application web.

Vous devez modifier l’application pour utiliser l’inscription de l’application dans votre client. Vous devez également configurer les stratégies créées. L’exemple d’application web définit les valeurs de configuration en tant que paramètres d’application dans le fichier Web.config. Pour modifier les paramètres d’application :

1. Ouvrez la solution **B2C-WebAPI-DotNet** dans Visual Studio.

2. Dans le projet d’application web **TaskWebApp**, ouvrez le fichier **Web.config** et effectuez les mises à jour suivantes :

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    
    <add key="ida:ClientId" value="The Application ID for your web app registered in your tenant" />
    
    <add key="ida:ClientSecret" value="Client password (client secret)" />
    ```
3. Mettez à jour les paramètres de stratégie à l’aide du nom généré lorsque vous avez créé vos stratégies.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    <add key="ida:EditProfilePolicyId" value="b2c_1_SiPe" />
    <add key="ida:ResetPasswordPolicyId" value="b2c_1_SSPR" />
    ```

## <a name="run-the-sample-web-app"></a>Exécuter l’exemple d’application web

Dans l’Explorateur de solutions, faites un clic droit sur le projet **TaskWebApp** puis cliquez sur **Définir comme projet de démarrage**

Appuyez sur **F5** pour lancer l’application web. Le navigateur par défaut se lance à l’adresse du site web local `https://localhost:44316/`. 

L’exemple d’application prend en charge l’inscription et la connexion des utilisateurs, la modification d’un profil, et la réinitialisation d’un mot de passe. Ce didacticiel met en évidence la manière dont un utilisateur s’inscrit pour utiliser l’application à l’aide d’une adresse e-mail. Vous pouvez étudier les autres scénarios vous-même.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

1. Cliquez sur le lien **S’inscrire / se connecter** dans la bannière supérieure pour vous inscrire en tant qu’utilisateur de l’application web. Cette méthode utilise la stratégie **b2c_1_SiUpIn** que vous avez définie dans une étape précédente.

2. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne possédez pas encore de compte, cliquez sur le lien **Inscrivez-vous maintenant**. 

3. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’abonnement collecte également le mot de passe et les attributs demandés définis dans la stratégie.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis. 

    ![Flux de travail d’abonnement](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Cliquez sur **Créer** pour créer un compte local dans le locataire Azure AD B2C.

Maintenant l’utilisateur peut utiliser son adresse e-mail pour vous connecter et utiliser l’application web.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser votre client Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre client Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer un locataire Azure AD B2C, créer des stratégies et mettre à jour de l’exemple d’application web pour utiliser votre locataire Azure AD B2C. Passez au prochain didacticiel pour apprendre à inscrire, configurer et appeler une API web ASP.NET protégée par votre client Azure AD B2C.

> [!div class="nextstepaction"]
> [Didacticiel : Utiliser Azure Active Directory B2C pour protéger une API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)
