---
title: "Didacticiel d’utilisation d’Azure Active Directory B2C pour protéger une API Web ASP.NET"
description: "Didacticiel sur l’utilisation d’Active Directory B2C pour protéger une API Web ASP.NET et l’appeler à partir d’une application web ASP.NET."
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 0e9f324cec0d242c013a461d8580abd4faa97c8d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-use-azure-active-directory-b2c-to-protect-an-aspnet-web-api"></a>Didacticiel : Utiliser Azure Active Directory B2C pour protéger une API Web ASP.NET

Ce didacticiel vous montre comment appeler une ressource d’API web protégée par Azure Active Directory (Azure AD) B2C à partir d’une application web ASP.NET.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * inscrire une API web dans votre client Azure AD B2C ;
> * définir et configurer les étendues d’une API web ;
> * accorder à une application des autorisations d’accès à l’API web ;
> * mettre à jour l’exemple de code pour utiliser Azure AD B2C afin de protéger une API web.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>configuration requise

* Suivre [Use Azure Active Directory B2C for User Authentication in an ASP.NET Web App tutorial](active-directory-b2c-tutorials-web-app.md) (Didacticiel d’utilisation d’Azure Active Directory B2C pour l’authentification utilisateur dans une application web ASP.NET).
* Installer [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.

## <a name="register-web-api"></a>Inscrire une API web

Les ressources d’API web doivent être inscrites dans votre client pour pouvoir accepter les [demandes de ressources protégées](../active-directory/develop/active-directory-dev-glossary.md#resource-server) des [applications clientes](../active-directory/develop/active-directory-dev-glossary.md#client-application) qui présentent un [jeton d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token) d’Azure Active Directory et y répondre. L’inscription établit [l’objet principal de service et d’application](../active-directory/develop/active-directory-dev-glossary.md#application-object) dans votre client. 

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre client Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Sélectionnez **Azure AD B2C** dans la liste des services du portail Azure.

2. Dans le paramètres B2C, cliquez sur **Applications**, puis sur **+ Ajouter**.

    Pour inscrire l’exemple d’API web dans votre client, utilisez les paramètres ci-dessous.
    
    ![Ajouter une nouvelle API](media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Mon exemple d’API web | Entrez un **nom** qui décrit votre API web pour les développeurs. |
    | **Inclure une application/API web** | OUI | Sélectionnez **Oui** pour une API web. |
    | **Autoriser le flux implicite** | OUI | Sélectionnez **Oui** puisque l’API utilise la [connexion OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de réponse** | `https://localhost:44332` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre API. Dans ce didacticiel, l’exemple d’API web s’exécute localement (localhost) et écoute sur le port 44332. |
    | **URI ID d’application** | myAPISample | L’URI identifie de façon unique l’API dans le client. Vous pouvez ainsi inscrire plusieurs API par client. Les [étendues](../active-directory/develop/active-directory-dev-glossary.md#scopes) régissent l’accès à la ressource d’API protégée et sont définies par l’URI ID d’application. |
    | **Client natif** | Non  | Dans la mesure où il s’agit d’une API web et pas d’un client natif, sélectionnez Non. |
    
3. Cliquez sur **Créer** pour inscrire votre API.

Les API inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API web s’affiche.

![Propriétés de l’API Web](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Notez **l’ID du client d’application**. Cet ID identifie l’API de manière unique. Il est nécessaire pour configurer l’API ultérieurement dans le didacticiel.

L’inscription de l’API web avec Azure AD B2C définit une relation d’approbation. Étant donné que l’API est inscrite avec B2C, elle peut désormais approuver les jetons d’accès B2C reçus des autres applications.

## <a name="define-and-configure-scopes"></a>Définir et configurer des étendues

Les [étendues](../active-directory/develop/active-directory-dev-glossary.md#scopes) permettent de gérer l’accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, certains utilisateurs peuvent bénéficier d’un accès en lecture et en écriture tandis que d’autres peuvent disposer d’autorisations d’accès en lecture seule. Dans ce didacticiel, vous allez définir des autorisations d’accès en lecture et en écriture pour l’API web.

### <a name="define-scopes-for-the-web-api"></a>Définir les étendues de l’API web

Les API inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API web s’affiche.

Cliquez sur **Étendues publiées (préversion)**.

Pour configurer les étendues de l’API, ajoutez les entrées ci-dessous. 

![étendues définies dans l’api web](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Paramètre      | Valeur suggérée  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Portée** | Hello.Read | Accès en lecture à hello |
| **Portée** | Hello.Write | Accès en écriture à hello |

Les étendues publiées peuvent servir à accorder à une application cliente une autorisation d’accès à l’API web.

### <a name="grant-app-permissions-to-web-api"></a>Accorder à une application des autorisations d’accès à l’API web

Pour appeler une API web protégée à partir d’une application, vous devez accorder à cette application des autorisations d’accès à l’API. 

1. Dans le portail Azure, sélectionnez **Azure AD B2C** dans la liste des services, puis cliquez sur **Applications** pour afficher la liste des applications inscrites.

2. Dans la liste des applications, sélectionnez **Mon exemple d’application web** et cliquez sur **Accès d’API (préversion)**, puis sur **Ajouter**.

3. Dans la liste déroulante **Sélectionner une API**, sélectionnez votre API web inscrite **Mon exemple d’API web**.

4. Dans la liste déroulante **Sélectionnez des étendues**, sélectionnez les étendues que vous avez définies au cours de l’inscription de l’API web.

    ![sélection d’étendues pour l’application](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Cliquez sur **OK**.

L’application **Mon exemple d’application web** est inscrite pour appeler **Mon exemple d’API web** protégée. Un utilisateur [s’authentifie](../active-directory/develop/active-directory-dev-glossary.md#authentication) auprès d’Azure AD B2C pour utiliser l’application web. L’application web obtient un [octroi d’autorisation](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="update-web-api-code"></a>Mettre à jour le code de l’API web

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer le code de l’API web pour utiliser votre client Azure AD B2C. Dans ce didacticiel, vous allez configurer un exemple d’API web. 

L’exemple d’API web est inclus dans le projet que vous avez téléchargé dans le didacticiel préalable : [Use Azure Active Directory B2C for User Authentication in an ASP.NET Web App tutorial](active-directory-b2c-tutorials-web-app.md) (Didacticiel d’utilisation d’Azure Active Directory B2C pour l’authentification utilisateur dans une application web ASP.NET). Le cas échéant, terminez ce didacticiel préalable avant de poursuivre.

L’exemple de solution contient deux projets :

**Exemple d’application web (TaskWebApp) :** application web permettant de créer et de modifier une liste des tâches. L’application web utilise la stratégie **Inscription ou connexion** pour inscrire ou connecter des utilisateurs possédant une adresse e-mail.

**Exemple d’application d’API web (TaskService) :** API web qui prend en charge les fonctionnalités de la liste des tâches de création, de lecture, de mise à jour et de suppression. L’API web est sécurisée par Azure AD B2C et appelée par l’application web.

L’exemple d’application web et d’API web définit les valeurs de configuration en tant que paramètres d’application dans le fichier Web.config de chaque projet.

Ouvrez la solution **B2C-WebAPI-DotNet** dans Visual Studio.

### <a name="configure-the-web-app"></a>Configurer l’application web

1. Ouvrez **Web.config** dans le projet **TaskWebApp**.

2. Pour exécuter l’API localement, utilisez le paramètre localhost pour **api:TaskServiceUrl**. Modifiez le fichier Web.config comme suit : 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configurez l’URI de l’API. Il s’agit de l’URI que l’application web utilise pour effectuer la demande d’API. Configurez également les autorisations demandées.

```C#
<add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
<add key="api:ReadScope" value="Hello.Read" />
<add key="api:WriteScope" value="Hello.Write" />
```

### <a name="configure-the-web-api"></a>Configurer l’API web

1. Ouvrez **Web.config** dans le projet **TaskService**.

2. Configurez l’API pour utiliser votre client.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Définissez l’ID client sur l’ID d’application inscrite de votre API.

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. Mettez à jour le paramètre de stratégie à l’aide du nom généré lorsque vous avez créé votre stratégie d’inscription et de connexion.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    ```

5. Configurez le paramètre des étendues pour qu’il corresponde à ce que vous avez créé dans le portail.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample-web-app-and-web-api"></a>Exécuter l’exemple d’application web et d’API web

Vous devez exécuter les projets **TaskWebApp** et **TaskService**. 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Définir les projets de démarrage...**. 
2. Sélectionnez la case d’option **Plusieurs projets de démarrage**.
3. Pour les deux projets, définissez le paramètre **Action** sur **Démarrer**.
4. Pour enregistrer la configuration, cliquez sur OK.
5. Appuyez sur la touche **F5** pour exécuter les deux applications. Chaque application s’ouvre dans son propre onglet de navigateur. `https://localhost:44316/` est l’application web.
    `https://localhost:44332/` est l’API web.

6. Dans l’application web, cliquez sur le lien de connexion/inscription dans la bannière de menu pour vous inscrire pour l’application web. Utilisez le compte que vous avez créé dans le [didacticiel relatif à l’application web](active-directory-b2c-tutorials-web-app.md). 
7. Une fois connecté, cliquez sur le lien **Liste de tâches** et créez un élément de la liste de tâches.

Lorsque vous créez un élément de la liste de tâches, l’application web effectue une requête auprès de l’API web pour le générer. L’application web que vous avez protégée appelle l’API web protégée dans votre client Azure AD B2C.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser votre client Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre client Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>étapes suivantes

Cet article vous a expliqué en détail comment protéger une API Web ASP.NET en inscrivant et en définissant des étendues dans Azure AD B2C. Pour en savoir plus sur le développement de ce scénario, notamment sur les procédures pas à pas relatives au code, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET avec inscription, connexion, modification du profil et réinitialisation du mot de passe Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)