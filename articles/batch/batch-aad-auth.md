---
title: Utiliser Azure Active Directory pour l’authentification de solutions de service Azure Batch | Microsoft Docs
description: Batch prend en charge Azure AD pour l’authentification auprès du service Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: danlep
ms.openlocfilehash: e2d97929ada820a1ce969c59fb69b3518fe2eb6c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Authentification de solutions de service Batch avec Active Directory

Azure Batch prend en charge l’authentification avec [Azure Active Directory][aad_about] (Azure AD). Azure AD est le service Microsoft de gestion des répertoires et des identités basé sur le cloud mutualisé. Azure lui-même utilise Azure AD pour authentifier ses clients, ses administrateurs de services fédérés et ses utilisateurs professionnels.

Lorsque vous utilisez l’authentification Azure AD avec Azure Batch, vous pouvez vous authentifier de deux manières :

- À l’aide de l’**authentification intégrée** pour authentifier un utilisateur qui interagit avec l’application. Une application utilisant l’authentification intégrée collecte les informations d’identification d’un utilisateur et les utilise pour authentifier l’accès aux ressources Batch.
- À l’aide d’un **principal de service** pour authentifier une application sans assistance. Un principal de service définit la stratégie et les autorisations pour une application afin de représenter l’application lors de l’accès aux ressources au moment de l’exécution.

Pour en savoir plus sur Azure AD, consultez la [documentation sur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Points de terminaison pour l’authentification

Pour authentifier des applications Batch avec Azure AD, vous devez inclure des points de terminaison connus dans votre code.

### <a name="azure-ad-endpoint"></a>Point de terminaison Azure AD

Le point de terminaison d’autorité Azure AD est :

`https://login.microsoftonline.com/`

Pour s’authentifier avec Azure AD, vous utilisez ce point de terminaison avec l’ID client (ID de répertoire). L’ID client identifie le client Azure AD pour l’authentification. Pour récupérer l’ID client, suivez les étapes décrites dans [Obtenir l’ID client pour Azure Active Directory](#get-the-tenant-id-for-your-active-directory) :

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Le point de terminaison spécifique du client est requis lorsque vous vous authentifiez à l’aide d’un principal de service. 
> 
> Le point de terminaison spécifique du client est facultatif lorsque vous vous authentifiez à l’aide de l’authentification intégrée, mais est recommandé. Vous pouvez toutefois utiliser également le point de terminaison commun Azure AD. Le point de terminaison commun fournit une interface de collecte d’informations d’identification générique lorsqu’un client spécifique n’est pas spécifié. Le point de terminaison commun est `https://login.microsoftonline.com/common`.
>
>

Pour plus d’informations sur les points de terminaison Azure AD, consultez [Scénarios d’authentification pour Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Point de terminaison de ressource Batch

Utilisez le **point de terminaison de ressource Azure Batch** pour obtenir un jeton d’authentification des demandes au service Batch :

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Inscrire votre application avec un client

La première étape d’utilisation d’Azure AD pour l’authentification consiste à inscrire votre application dans un client Azure AD. L’inscription de l’application vous permet d’appeler la [Bibliothèque d’authentification Active Directory][aad_adal] (ADAL) Azure à partir de votre code. La bibliothèque ADAL fournit une API pour l’authentification avec Azure AD à partir de votre application. L’inscription de votre application est nécessaire si vous prévoyez d’utiliser l’authentification intégrée ou un principal de service.

Lorsque vous inscrivez votre application, vous fournissez des informations sur votre application à Azure AD. Azure AD fournit ensuite un ID d’application que vous utilisez pour associer votre application à Azure AD lors de l’exécution. Pour en savoir plus sur l’ID d’application, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Suivez les étapes de la section [Ajout d’une application](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dans [Intégration d’applications dans Azure Active Directory][aad_integrate] pour inscrire votre application Batch. Si vous inscrivez votre application en tant qu’application native, vous pouvez spécifier n’importe quel URI valide pour l’**URI de redirection**. Aucun point de terminaison réel n’est nécessaire.

Une fois que vous avez inscrit votre application, vous verrez l’ID d’application :

![Inscrire votre application Batch auprès d’Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Pour plus d’informations sur l’inscription d’une application avec Azure AD, consultez [Scénarios d’authentification pour Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obtenir l’ID client pour votre Active Directory

L’ID client identifie le client Azure AD qui fournit des services d’authentification à votre application. Pour obtenir l’ID client, procédez comme suit :

1. Dans le portail Azure, sélectionnez votre Active Directory.
2. Cliquez sur **Propriétés**.
3. Copiez la valeur GUID fournie pour l’ID de répertoire. Cette valeur est également appelée l’ID client.

![Copier l’ID de répertoire](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Utiliser l’authentification intégrée

Pour l’authentification avec l’authentification intégrée, vous devez autoriser votre application à se connecter à l’API de service Batch. Cette étape permet à votre application d’authentifier des appels de l’API de service Batch avec Azure AD.

Une fois que vous avez [inscrit votre application](#register-your-application-with-an-azure-ad-tenant), procédez comme suit dans le portail Azure pour lui accorder l’accès au service Batch :

1. Dans le volet de navigation de gauche du portail Azure, choisissez **Tous les services**. Cliquez sur **Inscriptions des applications**.
2. Recherchez le nom de votre application dans la liste des inscriptions d’application :

    ![Rechercher le nom de votre application](./media/batch-aad-auth/search-app-registration.png)

3. Ouvrez le panneau **Paramètres** de votre application. Dans la section **Accès API**, sélectionnez **Autorisations requises**.
4. Dans le panneau **Autorisations requises**, cliquez sur le bouton **Ajouter**.
5. À l’étape 1, recherchez l’API Batch. Recherchez chacune de ces chaînes, jusqu’à ce que vous trouviez l’API :
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Les locataires Azure AD les plus récents peuvent utiliser ce nom.
    3. La chaîne **ddbf3205-c6bd-46ae-8127-60eb93363864** correspond à l’ID de l’API Batch. 
6. Une fois que vous avez trouvé l’API Batch, sélectionnez-la, puis cliquez sur le bouton **Sélectionner**.
6. À l’étape 2, activez la case à cocher en regard de **Access Azure Batch Service** (Accéder au service Azure Batch) et cliquez sur le bouton **Sélectionner**.
7. Cliquez sur le bouton **Terminé**.

Le panneau **Autorisations requises** indique à présent que votre application Azure AD autorise l’accès à la bibliothèque ADAL et à l’API de service Batch. Les autorisations sont accordées automatiquement à ADAL lorsque vous commencez par inscrire votre application auprès d’Azure AD.

![Accorder des autorisations d’API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Utiliser un principal de service 

Pour authentifier une application qui s’exécute sans assistance, vous utilisez un principal de service. Après avoir inscrit votre application, procédez comme suit dans le portail Azure pour configurer un principal de service :

1. Demandez une clé secrète pour votre application.
2. Attribuez un rôle RBAC à votre application.

### <a name="request-a-secret-key-for-your-application"></a>Demander une clé secrète pour votre application

Lorsque votre application s’authentifie avec un service principal, elle envoie l’ID d’application et une clé secrète à Azure AD. Vous devez créer et copier la clé secrète à utiliser à partir de votre code.

Suivez les étapes ci-dessous dans le portail Azure :

1. Dans le volet de navigation de gauche du portail Azure, choisissez **Tous les services**. Cliquez sur **Inscriptions des applications**.
2. Recherchez le nom de votre application dans la liste des inscriptions d’application.
3. Affichez le panneau **Paramètres**. Dans la section **Accès API**, sélectionnez **Clés**.
4. Pour créer une clé, entrez une description de la clé. Sélectionnez ensuite la durée de la clé, un ou deux ans. 
5. Cliquez sur le bouton **Enregistrer** pour créer et afficher la clé. Copiez la valeur de clé dans un endroit sûr, car vous n’y avez plus accès lorsque vous quittez le panneau. 

    ![Créer une clé secrète](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Attribuer un rôle RBAC à votre application

Pour s’authentifier avec un principal de service, vous devez attribuer un rôle RBAC à votre application. Procédez comme suit :

1. Dans le portail Azure, accédez au compte Batch utilisé par votre application.
2. Dans le panneau **Paramètres** du compte Batch, sélectionnez **Contrôle d’accès (IAM)**.
3. Cliquez sur le bouton **Add** . 
4. Dans la liste déroulante **Rôle**, choisissez le rôle _Collaborateur_ ou _Lecteur_ pour votre application. Pour plus d’informations sur ces rôles, consultez [Prise en main du contrôle d’accès en fonction du rôle dans le portail Azure](../active-directory/role-based-access-control-what-is.md).  
5. Dans le champ **Sélectionner**, entrez le nom de votre application. Sélectionnez votre application dans la liste, puis cliquez sur **Enregistrer**.

Votre application doit maintenant apparaître dans vos paramètres de contrôle d’accès avec un rôle RBAC qui lui est attribué. 

![Attribuer un rôle RBAC à votre application](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obtenir l’ID client pour votre Azure Active Directory

L’ID client identifie le client Azure AD qui fournit des services d’authentification à votre application. Pour obtenir l’ID client, procédez comme suit :

1. Dans le portail Azure, sélectionnez votre Active Directory.
2. Cliquez sur **Propriétés**.
3. Copiez la valeur GUID fournie pour l’ID de répertoire. Cette valeur est également appelée l’ID client.

![Copier l’ID de répertoire](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Exemples de code

Les exemples de code de cette section montrent comment s’authentifier avec Azure AD à l’aide de l’authentification intégrée et d’un principal de service. Ces exemples de code utilisent .NET, mais les concepts sont similaires pour d’autres langages.

> [!NOTE]
> Un jeton d’authentification Azure AD expire au bout d’une heure. Lorsque vous utilisez un objet **BatchClient** longue durée, nous vous recommandons de récupérer un jeton à partir d’ADAL à chaque demande pour garantir que vous disposez toujours d’un jeton valide. 
>
>
> Pour mettre cela en place dans .NET, écrivez une méthode qui récupère le jeton à partir d’Azure AD et qui transmet cette méthode à un objet **BatchTokenCredentials** en tant que délégué. La méthode du délégué est appelée à chaque demande au service Batch pour garantir qu’un jeton valide est fourni. Par défaut, ADAL met en cache des jetons pour qu’un nouveau jeton soit récupéré à partir d’Azure AD uniquement lorsque cela est nécessaire. Pour plus d’informations sur les jetons dans Azure AD, consultez [Scénarios d’authentification pour Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemple de code : utilisation de l’authentification intégrée Azure AD avec Batch .NET

Pour s’authentifier avec l’authentification intégrée à partir de Batch .NET, vous devez référencer les packages [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) et [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Ajoutez les instructions `using` suivantes dans votre code :

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Référencez le point de terminaison Azure AD dans votre code, y compris l’ID client. Pour récupérer l’ID client, suivez les étapes décrites dans [Obtenir l’ID client pour Azure Active Directory](#get-the-tenant-id-for-your-active-directory) :

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Référencez le point de terminaison de ressource de service Batch :

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Référencez votre compte Batch :

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Spécifiez l’ID d’application (ID client) pour votre application. L’ID d’application est disponible dans votre inscription d’application dans le portail Azure :

```csharp
private const string ClientId = "<application-id>";
```

Copiez également l’URI de redirection que vous avez spécifiée pendant l’inscription. L’URI de redirection spécifié dans votre code doit correspondre à l’URI de redirection que vous avez fourni lors de l’inscription de l’application :

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Écrivez une méthode de rappel pour acquérir le jeton d’authentification à partir d’Azure AD. La méthode de rappel **GetAuthenticationTokenAsync** illustrée appelle ici la bibliothèque ADAL pour authentifier un utilisateur qui interagit avec l’application. La méthode **AcquireTokenAsync** fournie par la bibliothèque ADAL demande à l’utilisateur ses informations d’identification, et l’application continue une fois que l’utilisateur les a fournies (sauf si elle dispose déjà d’informations d’identification mises en cache) :

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Créez un objet **BatchTokenCredentials** qui prend le délégué comme paramètre. Utilisez ces informations d’identification pour ouvrir un objet **BatchClient**. Vous pouvez utiliser cet objet **BatchClient** pour les opérations suivantes sur le service Batch :

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemple de code : utilisation d’un principal de service Azure AD avec Batch .NET

Pour s’authentifier avec un principal de service à partir de Batch .NET, vous devez référencer les packages [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) et [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Ajoutez les instructions `using` suivantes dans votre code :

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Référencez le point de terminaison Azure AD dans votre code, y compris l’ID client. Lorsque vous utilisez un principal de service, vous devez indiquer un point de terminaison spécifique du client. Pour récupérer l’ID client, suivez les étapes décrites dans [Obtenir l’ID client pour Azure Active Directory](#get-the-tenant-id-for-your-active-directory) :

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Référencez le point de terminaison de ressource de service Batch :  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Référencez votre compte Batch :

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Spécifiez l’ID d’application (ID client) pour votre application. L’ID d’application est disponible dans votre inscription d’application dans le portail Azure :

```csharp
private const string ClientId = "<application-id>";
```

Spécifiez la clé secrète que vous avez copiée à partir du portail Azure :

```csharp
private const string ClientKey = "<secret-key>";
```

Écrivez une méthode de rappel pour acquérir le jeton d’authentification à partir d’Azure AD. La méthode de rappel **GetAuthenticationTokenAsync** illustrée ici appelle la bibliothèque ADAL pour l’authentification sans assistance :

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Créez un objet **BatchTokenCredentials** qui prend le délégué comme paramètre. Utilisez ces informations d’identification pour ouvrir un objet **BatchClient**. Vous pouvez ensuite utiliser cet objet **BatchClient** pour les opérations suivantes sur le service Batch :

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure AD, consultez la [documentation sur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Des exemples détaillés illustrant l’utilisation d’ADAL sont disponibles dans la bibliothèque [Exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

Pour en savoir plus sur les principaux de service, consultez [Objets application et principal de service dans Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Pour créer un principal de service à l’aide du portail Azure, consultez [Utiliser le portail Azure pour créer une application et un principal de service Active Directory pouvant accéder aux ressources](../resource-group-create-service-principal-portal.md). Vous pouvez également créer un principal de service avec PowerShell ou Azure CLI.

Pour authentifier des applications de gestion Batch à l’aide d’Azure AD, consultez [Solutions de gestion Batch avec Active Directory](batch-aad-auth-management.md).

Pour obtenir un exemple Python sur la manière de créer un client Batch authentifié à l’aide d’un jeton Azure AD, consultez l’exemple [Authentification Azure Active Directory](http://azure-sdk-for-python.readthedocs.io/en/latest/batch.html#azure-active-directory-authentication) dans la documentation Kit SDK Azure pour Python.

[aad_about]: ../active-directory/active-directory-whatis.md "Qu’est-ce qu’Azure Active Directory ?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénarios d’authentification pour Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Intégration d’applications dans Azure Active Directory"
[azure_portal]: http://portal.azure.com
