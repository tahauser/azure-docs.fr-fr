---
title: "Autoriser les applications à récupérer les secrets d’un coffre de clés dans Azure Stack | Microsoft Docs"
description: "Utiliser un exemple d’application à utiliser avec Azure Stack Key Vault"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: 50103dca21d047c5cee211b2250e750739131bc1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Exemple d’application qui utilise des clés et des secrets stockés dans un coffre de clés

Dans cet article, vous découvrez comment exécuter un exemple d’application (HelloKeyVault) qui récupère les clés et les secrets d’un coffre de clés dans Azure Stack.

## <a name="prerequisites"></a>Composants requis 

Effectuez les étapes prérequises suivantes à partir du [kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
* Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Créer et obtenir les paramètres du coffre de clés et de l’application

Vous devez d’abord créer un coffre de clés dans Azure Stack et inscrire une application dans Azure Active Directory (Azure AD). Vous pouvez créer et inscrire les coffres de clés à l’aide du portail Azure ou de PowerShell. Cet article explique comment effectuer les tâches avec PowerShell. Par défaut, ce script PowerShell crée une application dans Active Directory. Toutefois, vous pouvez également utiliser l’une de vos applications existantes. Veillez à fournir une valeur pour les variables `aadTenantName` et `applicationPassword`. Si vous ne spécifiez pas de valeur pour la variable `applicationPassword`, ce script génère un mot de passe aléatoire. 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

La capture d’écran suivante montre la sortie du script précédent :

![Configuration de l’application](media/azure-stack-kv-sample-app/settingsoutput.png)

Prenez note des valeurs de **VaultUrl**, **AuthClientId** et **AuthClientSecret** renvoyées par le script précédent. Vous utiliserez ces valeurs pour exécuter l’application HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Télécharger et exécuter l’exemple d’application

Téléchargez l’exemple de coffre de clés à partir de la page [Azure Key Vault client samples](https://www.microsoft.com/en-us/download/details.aspx?id=45343). Extrayez le contenu du fichier .zip sur votre station de travail de développement. Il existe deux exemples dans le dossier d’exemples. Nous utilisons l’exemple HelloKeyVault dans cet article. Accédez au dossier **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**, puis ouvrez l’application HelloKeyVault dans Visual Studio. 

Ouvrez le fichier HelloKeyVault\App.config et remplacez les valeurs de l’élément <appSettings> par les valeurs de **VaultUrl**, **AuthClientId** et **AuthClientSecret** renvoyées par le script précédent. Notez que par défaut, le fichier App.config contient un espace réservé pour *AuthCertThumbprint*, mais utilisez *AuthClientSecret* à la place. Après avoir remplacé les valeurs, regénérez la solution et démarrez l’application.

![Paramètres de l’application](media/azure-stack-kv-sample-app/appconfig.png)
 
L’application se connecte à Azure AD, puis utilise ce jeton pour s’authentifier auprès du coffre de clés dans Azure Stack. L’application effectue des opérations comme créer, chiffrer, envelopper (wrap), supprimer, etc. sur les clés et les secrets du coffre de clés. Vous pouvez également transmettre des paramètres spécifiques, comme *encrypt* et *decrypt* à l’application, ce qui garantit que l’application exécute seulement ces opérations sur le coffre. 


## <a name="next-steps"></a>Étapes suivantes
[Déployer une machine virtuelle avec un mot de passe Key Vault](azure-stack-kv-deploy-vm-with-secret.md)

[Déployer une machine virtuelle avec un certificat Key Vault](azure-stack-kv-push-secret-into-vm.md)



