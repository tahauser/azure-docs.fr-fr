---
title: "Créer une identité pour l’application Azure avec PowerShell | Microsoft Docs"
description: "Explique comment utiliser Azure PowerShell pour créer une application et un principal du service Azure Active Directory, et comment accorder à l’application l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle. Cet article montre comment authentifier l’application avec un mot de passe ou un certificat."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/28/2017
ms.author: tomfitz
ms.openlocfilehash: 103e4ca5ffd6c9dfe5043af9d8f75763705eb939
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell

Lorsque vous avez une application ou un script qui doit pouvoir accéder à des ressources, vous pouvez configurer une identité pour l’application et authentifier l’application avec ses propres informations d’identification. Cette identité est connue en tant que principal de service. Cette approche vous permet d’effectuer les opérations suivantes :

* Affecter à l’identité de l’application des autorisations différentes de vos propres autorisations. En règle générale, ces autorisations sont strictement limitées à ce que l’application doit faire.
* Utilisez un certificat pour l’authentification lors de l’exécution d’un script sans assistance.

Cet article explique comment utiliser [Azure PowerShell](/powershell/azure/overview) pour configurer tout ce dont vous avez besoin pour qu’une application puisse s’exécuter sous ses propres informations d’identification et sous sa propre identité.

## <a name="required-permissions"></a>Autorisations requises
Pour réaliser les étapes décrites dans cet article, vous devez disposer des autorisations suffisantes dans votre annuaire Azure Active Directory et votre abonnement Azure. Plus précisément, vous devez être en mesure de créer une application dans l’annuaire Azure Active Directory et d’affecter un rôle au principal du service. 

Le moyen le plus simple pour vérifier que votre compte dispose des autorisations adéquates est d’utiliser le portail. Consultez [Vérifier l’autorisation requise](resource-group-create-service-principal-portal.md#required-permissions).

Maintenant, passez à une section concernant l’authentification avec :

* [mot de passe](#create-service-principal-with-password)
* [certificat auto-signé](#create-service-principal-with-self-signed-certificate)
* [certificat délivré par une autorité de certification](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>Commandes PowerShell

Pour définir un principal de service, utilisez :

| Commande | Description |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Crée un principal de service Azure Active Directory |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Affecte le rôle RBAC spécifié au principal spécifiée, dans l’étendue spécifiée. |


## <a name="create-service-principal-with-password"></a>Créer un principal du service avec un mot de passe

Pour créer un principal de service avec le rôle Collaborateur pour votre abonnement, utilisez : 

```powershell
Login-AzureRmAccount
$password = convertto-securestring {provide-password} -asplaintext -force
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password $password
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

L’exemple reste en veille pendant 20 secondes pour laisser le temps au nouveau principal du service de se propager dans Azure Active Directory. Si votre script n’attend pas suffisamment, une erreur indiquant « Le principal {ID} n’existe pas dans le répertoire {DIR-ID}. » s’affiche.

Le script suivant vous permet de spécifier une portée différente de l’abonnement par défaut et réessaye l’attribution de rôle si une erreur se produit :

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $SecurePassword = convertto-securestring $Password -asplaintext -force
 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $SecurePassword
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Quelques points à noter concernant le script :

* Pour accorder l’accès aux identités pour l’abonnement par défaut, il est inutile de fournir les paramètres ResourceGroup et SubscriptionId.
* Spécifiez le paramètre ResourceGroup uniquement lorsque vous souhaitez limiter l’étendue de l’attribution de rôles à un groupe de ressources.
*  Dans cet exemple, vous ajoutez le principal du service au rôle Contributeur. Pour les autres rôles, voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).
* Le script reste en veille pendant 15 secondes pour laisser le temps au nouveau principal du service de se propager dans Azure Active Directory. Si votre script n’attend pas suffisamment, une erreur indiquant « Le principal {ID} n’existe pas dans le répertoire {DIR-ID}. » s’affiche.
* Si vous devez accorder au service principal l’accès à plus d’abonnements ou groupes de ressources, exécutez à nouveau l’applet de commande `New-AzureRMRoleAssignment` avec des portées différentes.


### <a name="provide-credentials-through-powershell"></a>Fournir des informations d’identification via PowerShell
Maintenant, vous devez vous connecter en tant qu’application pour effectuer des opérations. Pour le nom d’utilisateur, utilisez le paramètre `ApplicationId` que vous avez créé pour l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

L’ID client n’est pas sensible, vous pouvez donc l’incorporer directement dans votre script. Pour récupérer l’ID client, utilisez :

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Créer un principal du service avec un certificat auto-signé

Pour créer un principal de service avec un certificat auto-signé et le rôle Collaborateur pour votre abonnement, utilisez : 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

L’exemple reste en veille pendant 20 secondes pour laisser le temps au nouveau principal du service de se propager dans Azure Active Directory. Si votre script n’attend pas suffisamment, une erreur indiquant « Le principal {ID} n’existe pas dans le répertoire {DIR-ID}. » s’affiche.

Le script suivant vous permet de spécifier une portée différente de l’abonnement par défaut et réessaye l’attribution de rôle si une erreur se produit. Vous devez disposer d’Azure PowerShell 2.0 sur Windows 10 ou Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Quelques points à noter concernant le script :

* Pour accorder l’accès aux identités pour l’abonnement par défaut, il est inutile de fournir les paramètres ResourceGroup et SubscriptionId.
* Spécifiez le paramètre ResourceGroup uniquement lorsque vous souhaitez limiter l’étendue de l’attribution de rôles à un groupe de ressources.
* Dans cet exemple, vous ajoutez le principal du service au rôle Contributeur. Pour les autres rôles, voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).
* Le script reste en veille pendant 15 secondes pour laisser le temps au nouveau principal du service de se propager dans Azure Active Directory. Si votre script n’attend pas suffisamment, une erreur indiquant « Le principal {ID} n’existe pas dans le répertoire {DIR-ID}. » s’affiche.
* Si vous devez accorder au service principal l’accès à plus d’abonnements ou groupes de ressources, exécutez à nouveau l’applet de commande `New-AzureRMRoleAssignment` avec des portées différentes.

Si vous **n’avez pas Windows 10 ou Windows Server 2016 Technical Preview**, vous devez télécharger le [générateur de certificat auto-signé](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) depuis le centre de scripts Microsoft. Extrayez son contenu et importez l’applet de commande dont vous avez besoin.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
Dans le script, remplacez les deux lignes suivantes pour générer le certificat.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fournir un certificat via un script PowerShell automatisé
Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Azure Active Directory. Si vous disposez d’un seul abonnement, vous pouvez utiliser :

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

L’ID d’application et l’ID de client ne sont pas sensibles, vous pouvez donc les incorporer directement dans votre script. Pour récupérer l’ID client, utilisez :

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Si vous avez besoin extraire l’ID de l’application, utilisez :

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Créer un principal du service avec un certificat à partir de l’autorité de certification
Pour utiliser un certificat émis par une autorité de certification afin de créer le principal de service, utilisez le script suivant :

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

Quelques points à noter concernant le script :

* L’étendue de l’accès est limitée à l’abonnement.
* Dans cet exemple, vous ajoutez le principal du service au rôle Contributeur. Pour les autres rôles, voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).
* Le script reste en veille pendant 15 secondes pour laisser le temps au nouveau principal du service de se propager dans Azure Active Directory. Si votre script n’attend pas suffisamment, une erreur indiquant « Le principal {ID} n’existe pas dans le répertoire {DIR-ID}. » s’affiche.
* Si vous devez accorder au service principal l’accès à plus d’abonnements ou groupes de ressources, exécutez à nouveau l’applet de commande `New-AzureRMRoleAssignment` avec des portées différentes.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fournir un certificat via un script PowerShell automatisé
Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

L’ID d’application et l’ID de client ne sont pas sensibles, vous pouvez donc les incorporer directement dans votre script. Pour récupérer l’ID client, utilisez :

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Si vous avez besoin extraire l’ID de l’application, utilisez :

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Modifier les informations d’identification

Pour modifier les informations d’identification d’une application Active Directory, en raison d’une faille de sécurité ou de l’expiration des informations d’identification, utilisez les applets de commande [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) et [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Pour supprimer toutes les informations d’identification d’une application, utilisez :

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Pour ajouter un mot de passe, utilisez :

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Pour ajouter une valeur de certificat, créez un certificat auto-signé, comme indiqué dans cet article. Ensuite, utilisez :

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Enregistrer le jeton d’accès pour simplifier la connexion
Pour éviter de fournir des informations d’identification au principal de service chaque fois qu’il a besoin de se connecter, vous pouvez enregistrer le jeton d’accès.

Pour utiliser le jeton d’accès actuel dans une session ultérieure, enregistrez le profil.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Ouvrez le profil et examinez-en le contenu. Notez qu’il contient un jeton d’accès. Au lieu de vous reconnecter manuellement, chargez le profil.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> Le jeton d’accès arrive à expiration. L’utilisation d’un profil enregistré ne fonctionne donc que durant la validité du jeton.
>  

Vous pouvez également appeler des opérations REST à partir de PowerShell pour vous connecter. Dans la réponse d’authentification, vous pouvez récupérer le jeton d’accès en vue de l’utiliser avec d’autres opérations. Pour un exemple de récupération du jeton d’accès en appelant des opérations REST, consultez la section [Create the request](/rest/api/#create-the-request) (Créer la requête).

## <a name="debug"></a>Déboguer

Lors de la création d’un principal de service, vous pouvez rencontrer les erreurs suivantes :

* **« Authentication_Unauthorized »** ou **« Aucun abonnement trouvé dans le contexte. »** - Vous voyez cette erreur lorsque votre compte ne possède pas les [autorisations requises](#required-permissions) sur Azure Active Directory pour inscrire une application. En règle générale, vous obtenez cette erreur lorsque seuls des utilisateurs administrateurs dans votre annuaire Azure Active Directory peuvent inscrire des applications et que votre compte n’est pas un compte d’administrateur. Demandez à votre administrateur de vous affecter à un rôle d’administrateur ou d’autoriser les utilisateurs ordinaires à inscrire des applications.

* Le message indique que votre compte **« n’est pas autorisé à effectuer l’action ’Microsoft.Authorization/roleAssignments/write’ sur l’étendue ’/subscriptions/{guid}’ »** - Vous voyez cette erreur lorsque votre compte ne dispose pas d’autorisations suffisantes pour affecter un rôle à une identité. Demandez à votre administrateur d’abonnement de vous ajouter au rôle Administrateur de l’accès utilisateur.

## <a name="sample-applications"></a>Exemples d'applications
Pour plus d’informations sur la connexion en tant qu’application via différentes plateformes, voir :

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.JS](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>étapes suivantes
* Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
* Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](../active-directory/active-directory-application-objects.md). 
* Pour plus d’informations sur l’authentification Azure Active Directory, consultez la rubrique [Scénarios d’authentification pour Azure AD](../active-directory/active-directory-authentication-scenarios.md).
* Pour une liste des actions disponibles qui peuvent être autorisées ou refusées aux utilisateurs, consultez [Opérations du fournisseur de ressources Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

