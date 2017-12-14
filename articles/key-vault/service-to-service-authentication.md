---
title: "Authentification de service à service sur Azure Key Vault à l’aide de .NET"
description: "Utilisez la bibliothèque de Microsoft.Azure.Services.AppAuthentication pour effectuer l’authentification auprès d’Azure Key Vault à l’aide de .NET."
keywords: "informations d’identification locales pour l’authentification Auzre Key Vault"
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: 
ms.service: microsoft-keyvault
ms.technology: 
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: bff4b15ca2f1c985c4b4e27d159adaa5fd039553
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Authentification de service à service auprès d’Azure Key Vault à l’aide de .NET

Pour vous authentifier auprès d’Azure Key Vault, vous avez besoin d’informations d’identification Azure Active Directory (AD), secret partagé ou certificat. La gestion de ces informations d’identification peut être difficile. Il peut être tentant de regrouper les informations d’identification au sein d’une application, en les incluant dans des fichiers source ou de configuration.

L’élément `Microsoft.Azure.Services.AppAuthentication` de la bibliothèque .NET simplifie ce problème. Il utilise les informations d’identification du développeur pour l’authentification pendant le développement local. Lorsque la solution est déployée ultérieurement vers Azure, la bibliothèque bascule automatiquement vers les informations d’identification de l’application.  

Il est plus sûr d’utiliser les informations d’identification du développeur pendant le développement local, car vous n’avez pas besoin de créer des informations d’identification Azure AD ou de partager des informations d’identification entre les développeurs.

La bibliothèque `Microsoft.Azure.Services.AppAuthentication` gère l’authentification automatiquement, ce qui vous permet de vous concentrer sur votre solution, plutôt que sur vos informations d’identification.

La bibliothèque `Microsoft.Azure.Services.AppAuthentication` prend en charge le développement local avec Microsoft Visual Studio, Azure CLI ou l’authentification intégrée Azure AD. Une fois déployée sur Azure App Services ou une machine virtuelle Azure, la bibliothèque utilise automatiquement la fonction [Managed Service Identity](/azure/active-directory/msi-overview) (MSI). Aucune modification du code ou de la configuration n’est requise. La bibliothèque prend également en charge les [informations d’identification client](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) Azure AD lorsque la fonction MSI n’est pas disponible, ou lorsque le contexte de sécurité du développeur ne peut être déterminé pendant le développement local.

<a name="asal"></a>
## <a name="using-the-library"></a>Utilisation de la bibliothèque

Pour les applications .NET, la façon la plus simple d’utiliser Managed Service Identity (MSI) consiste à tirer parti du package `Microsoft.Azure.Services.AppAuthentication`. Voici comment démarrer :

1. Ajoutez une référence au package NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) à votre application.

2. Ajoutez le code suivant :

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

La classe `AzureServiceTokenProvider` met en cache le jeton en mémoire et le récupère à partir d’Azure AD, juste avant l’expiration. Par la suite, vous n’aurez pas besoin de vérifier la date d’expiration avant d’appeler la méthode `GetAccessTokenAsync`. Il vous suffit d’appeler la méthode lorsque vous souhaitez utiliser le jeton. 

La méthode `GetAccessTokenAsync` requiert un identificateur de ressource. Pour en savoir plus, voir [Quels services Azure prennent en charge l’identité du service administré ?](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Exemples

Les exemples ci-dessous illustrent le fonctionnement de la bibliothèque `Microsoft.Azure.Services.AppAuthentication` :

1. [Utilisation de Managed Service Identity pour récupérer un secret à partir d’Azure Key Vault lors de l’exécution](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Déploiement par programme d’un modèle Azure Resource Manager à partir d’une machine virtuelle Azure avec MSI](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Utilisation d’un exemple .NET Core et MSI pour appeler des services Azure à partir d’une machine virtuelle Linux Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Authentification du développement local

Pour le développement local, il existe deux scénarios d’authentification principaux :

- [Authentification auprès des services Azure](#authenticating-to-azure-services)
- [Authentification auprès de services personnalisés](#authenticating-to-custom-services)

Ici, vous découvrez les exigences associées à chaque scénario, ainsi que les outils pris en charge.


### <a name="authenticating-to-azure-services"></a>Authentification auprès des services Azure

Les machines locales ne prennent pas en charge Managed Service Identity (MSI).  Par conséquent, la bibliothèque `Microsoft.Azure.Services.AppAuthentication` utilise vos informations d’identification de développeur pour s’exécuter dans votre environnement de développement local. Lorsque la solution est déployée sur Azure, la bibliothèque utilise MSI pour basculer vers un flux d’octroi d’informations d’identification de client OAuth 2.0.  Cela signifie que vous pouvez sans problème tester le même code en local et à distance.

Pour le développement local, `AzureServiceTokenProvider` extrait des jetons à l’aide de **Visual Studio**, de **l’interface de ligne de commande Azure** (CLI) ou de **l’authentification intégrée Azure AD**. Le système essaie chaque option de manière séquentielle et utilise la première option qui aboutit. Si aucune option ne fonctionne, une exception `AzureServiceTokenProviderException` est levée, avec des informations détaillées.

### <a name="authenticating-with-visual-studio"></a>Authentification avec Visual Studio

Pour utiliser Visual Studio, vérifiez les éléments suivants :

1. Vous avez installé [Visual Studio 2017 version 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) ou plus.

2. L’extension [d’authentification de l’application pour Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) est installée.
 
3. Vous vous êtes connecté à Visual Studio et avez sélectionné un compte à utiliser pour le développement local. Utilisez l’option **Outils**&nbsp;>&nbsp;**Options**&nbsp;>&nbsp;**Azure Services Authentication**pour choisir un compte de développement local. 

Si vous rencontrez des problèmes lorsque vous utilisez Visual Studio, telles que les erreurs concernant le fichier du fournisseur de jetons, lisez attentivement ces étapes. 

Il peut également être nécessaire d’authentifier à nouveau votre jeton de développeur.  Pour ce faire, accédez à **Outils**&nbsp;>&nbsp;**Options**>**Azure&nbsp;Service&nbsp;Authentication** et recherchez un lien **Réauthentifier** sous le compte sélectionné.  Cliquez sur ce lien pour effectuer l’authentification. 

### <a name="authenticating-with-azure-cli"></a>Authentification avec Azure CLI

Pour utiliser Azure CLI pour le développement local, procédez comme suit :

1. Installez [Azure CLI v2.0.12](/cli/azure/install-azure-cli) ou plus. Mettez à niveau les versions antérieures. 

2. Exécutez la commande **az login** pour vous connecter à Azure.

Utilisez la commande `az account get-access-token` pour vérifier l’accès.  Si vous recevez une erreur, vérifiez que l’étape 1 a réussi. 

Si la fonction Azure CLI n’est pas installée dans le répertoire par défaut, vous pouvez recevoir une erreur signalant que `AzureServiceTokenProvider` ne peut pas trouver le chemin d’accès à Azure CLI.  Utilisez la variable d’environnement **AzureCLIPath** pour définir le dossier d’installation Azure CLI. Le paramètre `AzureServiceTokenProvider` ajoute le répertoire spécifié dans la variable d’environnement **AzureCLIPath** à la variable d’environnement **Path**, le cas échéant.

Si vous êtes connecté à Azure CLI à l’aide de plusieurs comptes, ou si votre compte peut accéder à plusieurs abonnements, vous devez spécifier l’abonnement spécifique à utiliser.  Pour ce faire, utilisez la commande suivante :

```
az account set --subscription <subscription-id>
```

Cette commande génère une sortie uniquement en cas d’échec.  Pour vérifier les paramètres de compte en cours, utilisez la commande suivante :

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Authentification via la fonction d’intégration Azure AD

Pour utiliser l’authentification Azure Active Directory, vérifiez les éléments suivants :

- Votre annuaire Active Directory local [est synchronisé avec Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Votre code s’exécute sur un ordinateur joint au domaine.


### <a name="authenticating-to-custom-services"></a>Authentification auprès de services personnalisés

Lorsqu’un service appelle les services Azure, les étapes précédentes sont applicables, car ces services permettent d’accéder aux utilisateurs et aux applications.  

Lorsque vous créez un service qui appelle un service personnalisé, utilisez les informations d’identification du client Azure AD pour l’authentification de développement local.  Nous avons deux options : 

1.  Utilisez un principal de service pour la connexion à Azure :

    1.  [Créez un principal du service](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Utilisez Azure CLI pour vous connecter :

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Comme le principal du service ne dispose pas nécessairement d’un accès à un abonnement, utilisez l’argument `--allow-no-subscriptions`.

2.  Utilisez des variables d’environnement pour spécifier les détails du principal de service.  Pour en savoir plus, voir [Exécution de l’application à l’aide d’un principal de service](#running-the-application-using-a-service-principal).

Une fois que vous êtes connecté à Azure, `AzureServiceTokenProvider` récupère un jeton pour le développement local à l’aide du principal de service.

Cela s’applique uniquement au développement local. Lorsque votre solution est déployée sur Azure, la bibliothèque bascule sur l’authentification MSI.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Exécution de l’application à l’aide de Managed Service Identity 

Lorsque vous exécutez votre code dans Azure App Service ou une machine virtuelle Azure pour laquelle MSI est activé, la bibliothèque utilise automatiquement Managed Service Identity. Le code n’a pas besoin d’être modifié. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Exécution de l’application à l’aide d’un principal de service 

Il peut être nécessaire de créer une information d’identification de client Azure Active Directory pour s’authentifier. Voici quelques exemples communs :

1. Votre code s’exécute dans un environnement de développement local, mais non sous l’identité du développeur.  Service Fabric, par exemple, utilise le [compte NetworkService](/azure/service-fabric/service-fabric-application-secret-management) pour le développement local.
 
2. Votre code s’exécute dans un environnement de développement local, et vous êtes authentifié auprès d’un service personnalisé. Vous n’utilisez donc pas votre identité de développeur. 
 
3. Votre code s’exécute sur une ressource de calcul Azure qui ne prend pas en charge Managed Service Identity, comme Azure Batch.

Procédez comme suit pour vous connecter à Azure AD à l’aide d’un certificat :

1. Créez un [certificat de principal de service](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Déployez le certificat dans le magasin _LocalMachine_ ou _CurrentUser_. 

3. Définissez la variable d’environnement **AzureServicesAuthConnectionString** sur :

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Remplacez _{AppId}_, _{TenantId}_ et _{Thumbprint}_ par les valeurs générées à l’étape 1.

    Le paramètre **CertificateStoreLocation** doit avoir la valeur _CurrentUser_ ou _LocalMachine_, en fonction de votre plan de déploiement.

4. Exécutez l’application. 

Pour vous connecter à l’aide d’une information d’identification de secret partagé Azure AD, procédez comme suit :

1. Créez un [principal de service avec un mot de passe](/azure/azure-resource-manager/resource-group-authenticate-service-principal) et accordez-lui un accès au coffre de clés. 

2. Définissez la variable d’environnement **AzureServicesAuthConnectionString** sur :

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Remplacez _{AppId}_, _{TenantId}_ et _{ClientSecret}_ par les valeurs générées à l’étape 1.

3. Exécutez l’application. 

Une fois les systèmes correctement configurés, le code n’a pas besoin d’être modifié plus avant.  `AzureServiceTokenProvider` utilise la variable d’environnement et le certificat pour l’authentification auprès d’Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Prise en charge de chaînes de connexion

Par défaut, `AzureServiceTokenProvider` utilise plusieurs méthodes pour récupérer un jeton. 

Pour contrôler le processus, utilisez une chaîne de connexion passée au constructeur `AzureServiceTokenProvider` ou spécifiée dans la variable d’environnement *AzureServicesAuthConnectionString*. 

Les options suivantes sont prises en charge :

| Option de&nbsp;chaîne&nbsp;de connexion | Scénario | Commentaires|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Développement local | Le paramètre AzureServiceTokenProvider utilise AzureCli pour obtenir un jeton. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Développement local | Le paramètre AzureServiceTokenProvider utilise Visual Studio pour obtenir un jeton. |
| `RunAs=CurrentUser;` | Développement local | Le paramètre AzureServiceTokenProvider utilise l’authentification intégrée Azure AD pour obtenir un jeton. |
| `RunAs=App;` | Identité du service administré | Le paramètre AzureServiceTokenProvider utilise Managed Service Identity pour obtenir le jeton. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Principal du service | `AzureServiceTokenProvider` utilise un certificat pour obtenir un jeton de la part d’Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Principal du service | `AzureServiceTokenProvider` utilise un certificat pour obtenir un jeton de la part d’Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Principal du service |`AzureServiceTokenProvider` utilise un secret pour obtenir un jeton de la part d’Azure AD. |


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Managed Service Identity](/azure/app-service/app-service-managed-service-identity).

- Découvrir les différentes façons [d’authentifier et d’autoriser les applications](/azure/app-service/app-service-authentication-overview).

- En savoir plus sur les [scénarios d’authentification Azure AD](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).