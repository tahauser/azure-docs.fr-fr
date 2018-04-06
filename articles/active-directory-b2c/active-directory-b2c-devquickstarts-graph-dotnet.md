---
title: Utiliser l’API Graph - Azure AD B2C | Microsoft Docs
description: Comment appeler l’API Graph pour un client B2C à l’aide d’une identité d’application pour automatiser le processus.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.openlocfilehash: ff3aa44a4e2513f4d3e5ac2eed84715b8fe9b004
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C : Utiliser l’API Graph Azure AD

>[!NOTE]
> Vous devez utiliser [l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) pour gérer les utilisateurs d’un annuaire Azure AD B2C. Il ne s’agit pas de l’API Microsoft Graph. En savoir plus [ici](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Les clients Azure Active Directory (Azure AD) B2C sont souvent très volumineux. Par conséquent, de nombreuses tâches courantes de gestion de client doivent être effectuées par programmation. La gestion des utilisateurs en est un parfait exemple. Il se peut que vous ayez besoin de migrer un magasin d’utilisateurs existant vers un client B2C ou que vous souhaitiez héberger l’inscription des utilisateurs sur votre page et créer des comptes d’utilisateur dans votre répertoire Azure AD B2C en arrière-plan. Pour effectuer ces types de tâches, vous devez être en mesure de créer, de lire, de mettre à jour et de supprimer des comptes d’utilisateur. Vous pouvez faire tout cela à l’aide de l’API Azure AD Graph.

Pour les clients B2C, il existe deux modes principaux de communication avec l’API Graph.

* Pour les tâches interactives, à exécution unique, vous devez agir comme un compte d’administrateur dans le client B2C lorsque vous exécutez ces tâches. Avec ce mode, un administrateur doit se connecter à l’aide de ses informations d’identification avant de pouvoir effectuer des appels à l’API Graph.
* Pour les tâches automatisées, en continu, vous devez utiliser un compte de service doté des privilèges nécessaires pour effectuer des tâches de gestion. Dans Azure AD, vous pouvez le faire en inscrivant une application et en l’authentifiant auprès d’Azure AD à l’aide d’un **ID d’application** qui utilise [l’octroi des informations d’identification du client OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Dans ce cas, l’application joue son propre rôle, et non celui d’un utilisateur, pour appeler l’API Graph.

Dans cet article, nous allons expliquer comment exécuter le cas d’utilisation automatisée. Pour cette démonstration, nous allons créer un `B2CGraphClient` .NET 4.5 qui effectuera les opérations de création, de lecture, de mise à jour et de suppression (CRUD) d’utilisateurs. Le client aura une interface de ligne de commande (CLI) Windows permettant d’appeler des méthodes différentes. Toutefois, le code est écrit pour se comporter de façon non interactive et automatisée.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtention d’un client Azure AD B2C
Avant de pouvoir créer des applications ou des utilisateurs, ou interagir avec Azure AD, vous avez besoin d’un client Azure AD B2C comportant un compte d’administrateur général. Si vous n’avez pas encore de client, suivez le guide de [prise en main d’Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Inscrire votre application dans votre locataire
Une fois que vous avez un locataire B2C, vous devez inscrire votre application par le biais du [portail Azure](https://portal.azure.com).

> [!IMPORTANT]
> Pour utiliser l’API Graph avec votre client B2C, vous allez devoir inscrire une application dédiée à l’aide du menu générique *Inscriptions d’applications* du Portail Azure, et **NON** à l’aide du menu *Applications* d’Azure AD B2C. Vous ne pouvez pas réutiliser les applications B2C existantes que vous avez inscrites dans le menu *Applications* d’Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Choisissez votre client Azure AD B2C en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le volet de navigation de gauche, choisissez **Tous les services**, cliquez sur **Inscriptions des applications**, puis cliquez sur **Ajouter**.
4. Suivez les invites et créez une application. 
    1. Sélectionnez **Application web/API** en tant que Type d’application.    
    2. Fournissez **toutes les URL de connexion** (par exemple, https://B2CGraphAPI) comme elle ne convient pas pour cet exemple.  
5. L’application va maintenant s’afficher dans la liste des applications. Cliquez sur celle-ci pour obtenir l’**ID de l’application** (également appelé ID client). Copiez-le, car vous en aurez besoin dans une section ultérieure.
6. Dans le menu Paramètres, cliquez sur **Clés** et ajoutez une nouvelle clé (également appelée clé secrète client). Copiez-la également pour une utilisation dans une section ultérieure.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Configurer les autorisations Créer, Lire et Mettre à jour pour votre application
Vous devez maintenant configurer votre application pour obtenir toutes les autorisations requises pour créer, lire, mettre à jour et supprimer des utilisateurs.

1. Toujours dans le menu Inscriptions d’applications du Portail Azure, sélectionnez votre application.
2. Dans le menu Paramètres, cliquez sur **Autorisations requises**.
3. Dans le menu Autorisations requises, cliquez sur **Windows Azure Active Directory**.
4. Dans le menu Activer l’accès, sélectionnez l’autorisation **Accéder en lecture et en écriture aux données de l’annuaire** dans **Autorisations des applications** et cliquez sur **Enregistrer**.
5. Enfin, dans le menu Autorisations requises, cliquez sur le bouton **Accorder des autorisations**.

Vous disposez maintenant d’une application autorisée à créer, lire et mettre à jour des utilisateurs de votre client B2C.

> [!NOTE]
> Le processus complet d’octroi d’autorisations peut prendre quelques minutes.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Configurer les autorisations de suppression pour votre application
Actuellement, l’autorisation *Accéder en lecture et en écriture aux données de l’annuaire* n’inclut **PAS** la possibilité d’effectuer des suppressions telles que la suppression des utilisateurs. Si vous souhaitez donner à votre application la possibilité de supprimer des utilisateurs, vous devez effectuer ces étapes supplémentaires impliquant PowerShell. Dans le cas contraire, vous pouvez passer à la section suivante.

Tout d’abord, si vous ne l’avez pas déjà fait, installez le [module Azure AD PowerShell version 1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0) :

```powershell
Install-Module MSOnline
```

Une fois le module PowerShell installé, connectez-vous à votre locataire Azure AD B2C.

> [!IMPORTANT]
> Vous devez utiliser un compte d’administrateur de locataire B2C qui est **local** pour le locataire B2C. Ces comptes se présentent comme suit : myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Maintenant, nous allons utiliser l’**ID d’application** dans le script ci-dessous pour affecter à l’application le rôle d’administrateur de compte utilisateur qui va lui permettre de supprimer des utilisateurs. Comme ces rôles ont des identificateurs bien connus, il vous suffit d’entrer votre **ID d’application** dans le script ci-dessous.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

À présent, votre application dispose également des autorisations pour supprimer des utilisateurs sur votre locataire B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Téléchargement, configuration et création de l’exemple de code
Tout d’abord, téléchargez l’exemple de code et exécutez-le. Nous l’examinerons ensuite plus en détail.  Vous pouvez [télécharger l’exemple de code en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Vous pouvez également le cloner dans un répertoire de votre choix :

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Ouvrez la solution `B2CGraphClient\B2CGraphClient.sln` Visual Studio dans Visual Studio. Dans le projet `B2CGraphClient`, ouvrez le fichier `App.config`. Remplacez les trois paramètres de l’application par vos propres valeurs :

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, cliquez avec le bouton droit sur la solution `B2CGraphClient` et générez à nouveau l’exemple. Si l’opération aboutit, vous devez maintenant disposer d’un fichier exécutable `B2C.exe` dans le répertoire `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Création d’opérations CRUD d’utilisateurs à l’aide de l’API Graph
Pour utiliser le B2CGraphClient, ouvrez une invite de commandes Windows `cmd` et remplacez votre répertoire par le répertoire `Debug`. Exécutez ensuite la commande `B2C Help` .

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Ceci affiche une brève description de chaque commande. À chaque fois que vous appelez une de ces commandes, `B2CGraphClient` envoie une demande à l’API Azure AD Graph .

### <a name="get-an-access-token"></a>Obtention d’un jeton d’accès
Toute demande envoyée à l’API Graph requiert un jeton d’accès pour l’authentification. `B2CGraphClient` utilise la bibliothèque d’authentification Active Directory (ADAL) open source pour vous aider à acquérir des jetons d’accès. La bibliothèque ADAL facilite l’acquisition des jetons en fournissant une API simple et en prenant soin de certains détails importants, tels que la mise en cache des jetons d’accès. Cependant, vous n’êtes pas obligé d’utiliser la bibliothèque ADAL pour obtenir des jetons. Vous pouvez également en obtenir en créant des requêtes HTTP.

> [!NOTE]
> Cet exemple de code utilise la bibliothèque ADAL v2 afin de communiquer avec l’API Graph.  Vous devez utiliser la bibliothèque ADAL v2 ou v3 afin d’obtenir des jetons d’accès qui peuvent être utilisés avec l’API Azure AD Graph .
> 
> 

Lorsque `B2CGraphClient` est exécuté, il crée une instance de la classe `B2CGraphClient`. Le constructeur de cette classe définit une structure d’authentification de la bibliothèque ADAL :

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Nous allons utiliser la commande `B2C Get-User` à titre d’exemple. Lorsque `B2C Get-User` est appelé sans entrées supplémentaires, l’interface de ligne de commande appelle la méthode `B2CGraphClient.GetAllUsers(...)`. Cette méthode appelle `B2CGraphClient.SendGraphGetRequest(...)`, qui envoie une requête HTTP GET à l’API Graph. Avant d’envoyer la demande GET, `B2CGraphClient.SendGraphGetRequest(...)` obtient un jeton d’accès à l’aide de la bibliothèque ADAL :

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Vous pouvez obtenir un jeton d’accès pour l’API Graph en appelant la méthode `AuthenticationContext.AcquireToken(...)` de la bibliothèque ADAL. La bibliothèque ADAL renvoie un `access_token` qui représente l’identité de l’application.

### <a name="read-users"></a>Lecture des utilisateurs
Pour obtenir la liste des utilisateurs ou un utilisateur particulier à partir de l’API Graph, vous pouvez envoyer une demande HTTP `GET` au point de terminaison `/users`. Une requête pour obtenir tous les utilisateurs d’un client se présente ainsi :

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour afficher cette requête, exécutez :

 ```cmd
 B2C Get-User
 ```

Deux points importants sont à prendre en considération :

* Le jeton d’accès acquis via la bibliothèque ADAL est ajouté à l’en-tête `Authorization` à l’aide du schéma `Bearer`.
* Pour les clients B2C, vous devez utiliser le paramètre de requête `api-version=1.6`.

Ces détails sont gérés dans la méthode `B2CGraphClient.SendGraphGetRequest(...)` :

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Création de comptes d’utilisateurs clients
Quand vous créez des comptes d’utilisateur dans votre client B2C, vous pouvez envoyer une demande HTTP `POST` au point de terminaison `/users` :

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La plupart des propriétés de cette requête sont requises pour créer des utilisateurs clients. Cliquez [ici](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)pour en savoir plus. Veuillez noter que les commentaires `//` ont été inclus à des fins d’illustration. Ne les incluez pas dans une requête réelle.

Pour afficher la requête, exécutez l’une des commandes suivantes :

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

La commande `Create-User` prend un fichier .json comme paramètre d’entrée. Celui-ci contient une représentation JSON d’un objet utilisateur. L’exemple de code contient deux exemples de fichiers .json : `usertemplate-email.json` et `usertemplate-username.json`. Vous pouvez modifier ces fichiers en fonction de vos besoins. Outre les champs obligatoires ci-dessus, ces fichiers incluent plusieurs champs facultatifs que vous pouvez utiliser. Vous trouverez plus d’informations sur ces champs facultatifs dans la [référence d’entité API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Vous pouvez voir comment la demande POST est construite dans `B2CGraphClient.SendGraphPostRequest(...)`, qui :

* attache un jeton d’accès à l’en-tête `Authorization` de la demande ;
* définit le paramètre `api-version=1.6`;
* inclut l’objet utilisateur JSON dans le corps de la requête.

> [!NOTE]
> Si les comptes que vous souhaitez migrer à partir d’un magasin d’utilisateurs existant ont un mot de passe moins fort que les [règles de mot de passe fort d’Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), vous pouvez désactiver la condition de mot de passe fort à l’aide de la valeur `DisableStrongPassword` dans la propriété `passwordPolicies`. Par exemple, vous pouvez modifier la demande de création d’utilisateur fournie ci-dessus comme suit : `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Mise à jour de comptes d’utilisateurs clients
Le processus de mise à jour d’objets utilisateur est similaire à celui utilisé pour créer des objets utilisateur. Cependant, ce processus fait appel à la méthode HTTP `PATCH` :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Essayez de mettre à jour un utilisateur en mettant à jour vos fichiers JSON avec de nouvelles données. Vous pouvez ensuite utiliser `B2CGraphClient` pour exécuter l’une des commandes suivantes :

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspectez la méthode `B2CGraphClient.SendGraphPatchRequest(...)` pour plus d’informations sur l’envoi de cette requête.

### <a name="search-users"></a>Rechercher des utilisateurs
Vous pouvez rechercher des utilisateurs dans votre client B2C de deux façons. En utilisant l’ID objet de l’utilisateur ou l’identificateur de connexion de l’utilisateur (par exemple, la propriété `signInNames` ).

Exécutez l’une des commandes suivantes pour rechercher un utilisateur spécifique :

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Voici quelques exemples :

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Suppression d’utilisateurs
Le processus de suppression d’un utilisateur est simple. Utilisez la méthode HTTP `DELETE` et construisez l’URL avec l’ID objet correct :

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour obtenir un exemple, saisissez la commande suivante et consultez la requête de suppression affichée dans la console :

```cmd
B2C Delete-User <object-id-of-user>
```

Inspectez la méthode `B2CGraphClient.SendGraphDeleteRequest(...)` pour plus d’informations sur l’envoi de cette requête.

L’API Azure AD Graph permet d’effectuer de nombreuses actions en plus de la gestion des utilisateurs. La [référence de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fournit des informations détaillées sur chaque action, ainsi que des exemples de demande.

## <a name="use-custom-attributes"></a>Utilisation d’attributs personnalisés
La plupart des applications grand public doivent stocker un certain type d’informations de profil utilisateur personnalisé. Pour ce faire, une solution consiste à définir un attribut personnalisé dans votre client B2C. Vous pouvez ensuite traiter cet attribut de la même façon que toute autre propriété d’un objet utilisateur. Vous pouvez mettre à jour l’attribut, le supprimer, l’interroger, l’envoyer en tant que revendication dans les jetons de connexion, etc.

Pour définir un attribut personnalisé dans votre client B2C, consultez la [référence d’attribut personnalisé dans B2C](active-directory-b2c-reference-custom-attr.md).

Vous pouvez afficher les attributs personnalisés définis dans votre client B2C à l’aide de `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Le résultat de ces fonctions révèle les détails de chaque attribut personnalisé, tels que :

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Vous pouvez utiliser le nom complet, tel que `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, en tant que propriété de vos objets utilisateur.  Mettez à jour votre fichier .json avec la nouvelle propriété et une valeur pour la propriété, puis exécutez :

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Avec `B2CGraphClient`, vous disposez d’une application de service capable de gérer les utilisateurs de votre client B2C par programmation. `B2CGraphClient` utilise sa propre identité d’application pour s’authentifier auprès de l’API Azure AD Graph. et acquiert des jetons à l’aide d’une clé secrète client. Lorsque vous intégrez cette fonctionnalité dans votre application, prenez en considération les quelques points clés suivants pour les applications B2C :

* Vous devez accorder les autorisations appropriées à l’application dans le client.
* Pour le moment, vous devez utiliser la bibliothèque ADAL (pas MSAL) pour obtenir des jetons d’accès. (vous pouvez également envoyer des messages de protocole directement, sans utiliser de bibliothèque).
* Lorsque vous appelez l’API Graph, utilisez `api-version=1.6`.
* Lorsque vous créez et mettez à jour des utilisateurs clients, certaines propriétés sont requises, comme décrit plus haut.

Si vous avez des questions ou souhaitez effectuer d’autres actions à l’aide de l’API Graph sur votre client B2C, laissez un commentaire sur cet article ou enregistrez un problème dans le référentiel d’exemples de code GitHub.

