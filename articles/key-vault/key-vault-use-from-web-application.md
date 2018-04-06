---
title: Utilisation d’Azure Key Vault à partir d’une application web | Microsoft Docs
description: Utilisez ce didacticiel pour vous aider à apprendre comment utiliser Azure Key Vault à partir d'une application web.
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 93ef76729123c7187c3dd616419b5771324fe2cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Utilisation d'Azure Key Vault à partir d'une application web

## <a name="introduction"></a>Introduction

Utilisez ce didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d'une application web. Celui-ci vous guide dans le processus d'obtention d'une clé secrète à partir d'Azure Key Vault afin de pouvoir être utilisé dans votre application web.

**Durée estimée :** 15 minutes

Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* un URI pour une clé secrète dans Azure Key Vault,
* un ID client et une clé secrète client pour une application Web enregistrés avec Azure Active Directory qui a accès à votre Key Vault,
* une application web. Nous afficherons les étapes d'une application ASP.NET MVC déployée dans Azure en tant qu'application web.

>[!IMPORTANT]
>* Cet exemple est basé sur une ancienne méthode d’approvisionnement manuel des identités AAD. Il existe à ce jour une nouvelle fonctionnalité en préversion, nommée [Identité du service administré (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), qui peut configurer automatiquement les identités AAD. Pour plus d’informations, consultez l’exemple suivant sur [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/).

> [!NOTE]
>* Il est essentiel que vous ayez effectué les étapes répertoriées dans [Prise en main d'Azure Key Vault](key-vault-get-started.md) pour ce didacticiel afin que vous ayez l'URI pour une clé secrète et un ID client ainsi qu'une clé secrète client pour une application web.


L'application web qui accédera à Key Vault est celle qui est enregistrée dans Azure Active Directory et est autorisée à accéder à votre Key Vault. Si cela n'est pas le cas, revenez à Inscrire une Application dans le didacticiel de prise en main et répétez les étapes répertoriées.

Ce didacticiel est conçu pour les développeurs web qui comprennent les principes fondamentaux de création d'applications web sur Azure. Pour plus d'informations sur Azure Web Apps, consultez [Vue d'ensemble de Web Apps](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Ajouter des packages NuGet

Deux packages doivent être installés pour votre application web.

* Bibliothèque d'authentification Active Directory : contient des méthodes pour interagir avec Azure Active Directory et gérer l'identité de l'utilisateur
* Bibliothèque Azure Key Vault : contient des méthodes pour interagir avec Azure Key Vault

Ces deux packages peuvent être installés à l’aide de la console du Gestionnaire de Package en utilisant la commande Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modification du fichier Web.Config

Il existe trois paramètres d'application qui doivent être ajoutés au fichier web.config comme suit.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Si vous ne souhaitez pas héberger votre application comme une application web d'Azure, vous devriez ajouter les valeurs réelles ID client, Clé secrète client et une clé secrèteURI au fichier Web.config. Sinon, laissez ces valeurs factices ; nous ajouterons les valeurs réelles sur le Portail Azure pour renforcer le niveau de sécurité.

## <a id="gettoken"></a>Ajout d'une méthode pour obtenir un jeton d'accès

Pour utiliser l'API Key Vault, vous avez besoin d'un jeton d'accès. Le client Key Vault gère les appels de l'API Key Vault, mais vous devez lui fournir une fonction qui lui fait obtenir le jeton d'accès.  

Voici le code pour obtenir un jeton d'accès avec Azure Active Directory. Ce code peut être placé n'importe où dans votre application. Vous pouvez par exemple ajouter une classe Utils ou EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* Actuellement, la fonctionnalité Managed Service Identity (MSI) constitue la façon la plus simple de s’authentifier. Pour en savoir plus, consultez le lien suivant vers l’exemple qui utilise [Key Vault avec MSI dans une application dans .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) et le [didacticiel pour MSI avec App Service et Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* Pour authentifier une application Azure AD, il est également possible d’utiliser une clé secrète client et un ID client. L'utiliser dans votre application Web permet une séparation des tâches et davantage de contrôle sur la gestion de clés. Toutefois, vous devez pour cela placer la clé secrète client dans vos paramètres de configuration, ce qui, dans certains cas, peut s’avérer aussi dangereux que de placer le secret que vous souhaitez protéger dans vos paramètres de configuration. Vous trouverez ci-dessous des informations sur l’utilisation d’un ID client et d’un certificat, au lieu de l’ID client et de la clé secrète client, pour authentifier l’application Azure AD.

## <a id="appstart"></a>Récupération de la clé secrète dans Application Start

Nous avons maintenant besoin du code pour appeler l'API Key Vault et récupérer la clé secrète. Le code suivant peut être placé n'importe où tant qu'il est appelé avant que vous ne deviez l'utiliser. Ce code a été créé dans l'événement Application Start dans Global.asax afin qu'il s'exécute au démarrage et rende la clé secrète disponible à l'application.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Ajout de paramètres d’application sur le Portail Azure (facultatif)

Si vous disposez d’une application web Azure, vous pouvez maintenant ajouter les valeurs réelles d’AppSettings sur le Portail Azure. Ce faisant, les valeurs réelles ne seront pas dans le fichier Web.config, mais protégés via le portail où vous avez des fonctionnalités de contrôle d'accès distinctes. Ces valeurs seront remplacées par les valeurs que vous avez entrées dans votre fichier Web.config. Assurez-vous que les noms sont identiques.

![Paramètres d’application affichés sur le Portail Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Authentification avec un certificat et non une clé secrète client

Il est également possible d'authentifier une application Azure AD à l'aide d'un ID client et d’un certificat, plutôt qu'un ID client et une clé secrète client. Voici les étapes pour utiliser un certificat dans une application Web Azure :

1. obtenir ou créer un certificat ;
2. associer le certificat à une application Azure AD ;
3. ajouter du code à votre application Web pour utiliser le certificat ;
4. ajouter un certificat à votre application Web.

### <a name="get-or-create-a-certificate"></a>obtenir ou créer un certificat ;

Dans notre cas, nous créerons un certificat de test. Voici quelques exemples de commandes que vous pouvez utiliser dans une invite de commandes de développeur pour créer un certificat. Accédez au répertoire dans lequel vous souhaitez créer les fichiers de certificat.  En outre, pour les dates de début et de fin du certificat, utilisez la date actuelle plus un an.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Prenez note de la date de fin et du mot de passe pour le fichier.pfx (dans cet exemple : 31/07/2018 et test123). Vous en aurez besoin ultérieurement.

Pour plus d’informations sur la création d’un certificat de test, consultez [How to: Create Your Own Test Certificate](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>associer le certificat à une application Azure AD ;

Maintenant que vous disposez d’un certificat, vous devez l’associer à une application Azure AD. Actuellement, le Portail Azure ne prend pas en charge ce worklfow ; en revanche, PowerShell, oui. Exécutez les commandes suivantes pour associer le certificat à l’application Azure AD :

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Après avoir exécuté ces commandes, vous pouvez voir l'application dans Azure AD. Lors de votre recherche, veillez à bien sélectionner « Applications que ma société possède » au lieu de « Applications que ma société utilise » dans la boîte de dialogue de recherche.

Pour plus d’informations sur les objets d’application Azure AD et de principal du service, consultez la page [Objets de principal du service et d’application](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>ajouter du code à votre application Web pour utiliser le certificat ;

Nous allons maintenant ajouter du code à votre application web pour accéder au certificat et l’utiliser pour l’authentification.

Tout d'abord vient le code d'accès au certificat.

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

Notez que l'emplacement StoreLocation est CurrentUser, et non LocalMachine. Et que nous choisissons « false » pour la méthode Find, car nous utilisons un certificat de test.

Vient ensuite le code qui utilise le CertificateHelper et crée un ClientAssertionCertificate, nécessaire pour l'authentification.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Voici le nouveau code pour obtenir le jeton d'accès. Il remplace la méthode GetToken de l’exemple précédent. Elle a un nom différent pour des raisons pratiques.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

J'ai inséré tout ce code dans la classe Utils de mon projet d’application Web pour plus de facilité d'utilisation.

La dernière modification de code a lieu dans la méthode Application_Start. Nous devons tout d'abord appeler la méthode GetCert() pour charger le ClientAssertionCertificate. Nous modifions ensuite la méthode de rappel que nous transmettons lors de la création d'un nouveau KeyVaultClient. Notez que cela remplace le code de l’exemple précédent.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Ajouter un certificat à une application web par le biais du Portail Azure

L’ajout d’un certificat à une application web est un processus simple qui comporte deux étapes. Tout d’abord, connectez-vous au Portail Azure et accédez à votre application web. Sur le volet Paramètres de votre application Web, cliquez sur l'entrée « Custom domains and SSL ». Sur le panneau qui s’ouvre, vous pourrez charger le certificat que vous avez créé dans l’exemple précédent, KVWebApp.pfx. Veillez à vous souvenir du mot de passe du pfx.

![Ajouter un certificat à une application web sur le Portail Azure][2]

La dernière chose que vous devez faire consiste à ajouter un paramètre d’application à votre application web, nommé WEBSITE\_LOAD\_CERTIFICATES et avec la valeur *. Cela garantit que tous les certificats sont chargés. Si vous souhaitez charger uniquement les certificats que vous avez téléchargés, vous pouvez entrer une liste séparée par des virgules de leurs empreintes numériques.

Pour en savoir plus sur l'ajout d'un certificat à une application Web, consultez [Using Certificates in Azure Websites Applications](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Ajouter un certificat comme secret à Key Vault

Au lieu de charger votre certificat directement sur le service d’application web, vous pouvez le stocker dans Key Vault en tant que secret et l’y déployer. Il s’agit d’un processus en deux étapes décrit dans le billet de blog suivant [Deploying Azure Web App Certificate through Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Étapes suivantes

Pour les références de programmation, consultez la page [Référence de l'API cliente C# du coffre de clés](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
