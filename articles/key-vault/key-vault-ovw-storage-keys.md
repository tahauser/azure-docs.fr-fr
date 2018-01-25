---
ms.assetid: 
title: "Clés de compte de stockage Azure Key Vault"
description: "Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés."
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 6ebac5fc90e259b19e0a4103a732754384232a44
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Clés de compte de stockage Azure Key Vault

Avant l’apparition des clés de compte de stockage Azure Key Vault, les développeurs devaient gérer leurs propres clés de compte de stockage Azure (ASA) et les faire tourner manuellement ou par le biais d’une automatisation externe. À présent, les clés de compte de stockage Key Vault sont implémentées sous forme de [secrets Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) pour l’authentification avec un compte de stockage Azure. 

La fonctionnalité de clé de compte de stockage Azure (ASA) gère le roulement des secrets pour vous. Elle vous évite également d’avoir à établir un contact direct avec une clé ASA en proposant les signatures d’accès partagé (SAP) sous forme de méthode. 

Pour plus d’informations générales sur les comptes de stockage Azure, consultez la page [À propos des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Prise en charge des interfaces

Vous trouverez la liste complète et les liens vers nos interfaces de programmation et de script dans le [Guide du développeur Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Ce que gère Key Vault

Key Vault exécute plusieurs fonctions de gestion interne à votre place lorsque vous utilisez des clés de compte de stockage managé.

- Azure Key Vault gère les clés d’un compte de stockage Azure (ASA).
    - En interne, Azure Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure.  
    - Azure Key Vault régénère (fait tourner) les clés régulièrement. 
    - Les valeurs de clés ne sont jamais retournées en réponse à l’appelant. 
    - Azure Key Vault gère les clés des comptes de stockage ainsi que des comptes de stockage Classic. 
- Azure Key Vault permet au propriétaire du coffre / de l’objet (vous) de créer des définitions de SAP (de compte ou de service).
    - La valeur de la SAP, créée à l’aide de sa définition, est retournée sous forme de secret par le biais du chemin d’accès de l’URI REST. Pour plus d’informations, consultez la page [Opérations du compte de stockage Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Aide pour l’affectation de noms

- Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.  
- Un nom de définition de SAP doit avoir une longueur comprise entre 1 et 102 caractères, composés uniquement de 0-9, a-z et A-Z.

## <a name="developer-experience"></a>Expérience de développement

### <a name="before-azure-key-vault-storage-keys"></a>Avant l’apparition des clés de stockage Azure Key Vault 

Les développeurs avaient besoin d’appliquer les pratiques suivantes avec une clé de compte de stockage pour obtenir un accès au stockage Azure. 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Après l’apparition des clés de stockage Azure Key Vault 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Assistance développeur

- Autorisez uniquement Key Vault à gérer vos clés ASA. N’essayez pas de les gérer vous-même, vous interféreriez avec les processus de Key Vault. 
- Ne permettez pas que les clés ASA soient gérées par plusieurs objets Key Vault. 
- Si vous devez régénérer manuellement vos clés ASA, nous vous recommandons de le faire avec Key Vault. 

## <a name="getting-started"></a>Prise en main

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuration des autorisations de contrôle d’accès en fonction des permissions (RBAC)

L’identité de l’application Azure Key Vault a besoin d’autorisations pour *répertorier* et *regénérer* des clés pour un compte de stockage. Configurez-les en suivant les étapes ci-dessous :

- Obtenez l’ObjectId de l’identité Azure Key Vault : 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Attribuez le rôle d’opérateur de clés de stockage à l’identité Azure Key Vault : 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Pour un type de compte classique, réglez le paramètre de rôle sur *« Rôle de service d’opérateur de clé de compte de stockage classique »*.

## <a name="working-example"></a>Exemple d’utilisation

L’exemple suivant montre la création d’un compte de stockage Azure managé Key Vault et les définitions de signature d’accès partagé (SAP) associées.

### <a name="assumptions"></a>Hypothèses

Les instructions suivantes sont données dans le cadre de cet exemple.

- Votre ressource de stockage se trouve dans : */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*.

- Le nom de votre coffre de clés est : *yourtest1*.

### <a name="get-a-service-principal"></a>Obtenir un principal de service

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

La sortie de la commande précédente comprend votre principal de service, que nous appellerons *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Définir des autorisations

Vérifiez que vos autorisations de stockage sont définies sur *Tout*. Vous pouvez obtenir votre youruserPrincipalId et définir les autorisations pour le coffre à l’aide des commandes suivantes.

```powershell
$youruserPrincipalId = (Get-AzureRmADUser -SearchString "your user principal name").Id
```
À présent, recherchez votre nom et obtenez l’ObjectId connexe, que vous utiliserez lors de la définition des autorisations pour le coffre.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $youruserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Autoriser l’accès

Vous devez accorder au service Key Vault l’accès aux comptes de stockage avant de pouvoir créer un compte de stockage managé et des définitions SAP.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Créer un compte de stockage

Créez maintenant un compte de stockage managé et deux définitions SAP. La SAP de compte fournit un accès au service blob avec différentes autorisations.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Régénération

Définition de la période de régénération à l’aide des commandes suivantes.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Définir les définitions SAP

Définissez les définitions SAP dans Key Vault pour votre compte de stockage managé.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Obtenir un jeton

Obtenez les jetons SAP correspondants et effectuez des appels vers le stockage.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Créer le stockage

Notez que la tentative d’accès avec *$sastoken1* échoue, mais que nous sommes en mesure d’établir l’accès avec *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Résumé de l’exemple

Vous pouvez accéder au contenu blob de stockage avec le jeton SAP qui a un accès en écriture.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets PowerShell appropriées

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>Intégration des comptes de stockage 

Exemple : en tant que propriétaire d’un objet Key Vault, vous ajoutez un objet de compte de stockage à votre coffre Azure Key Vault pour intégrer un compte de stockage.

Lors de l’intégration, Key Vault doit vérifier que l’identité du compte en cours d’intégration dispose des autorisations adéquates pour *répertorier* et *régénérer* les clés de stockage. Pour vérifier ces autorisations, Key Vault obtient un jeton OBO (On Behalf Of) de la part du service d’authentification, audience définie pour le gestionnaire de ressources Azure, et effectue un appel de la *liste* clé auprès du service de stockage Azure. Si l’appel de la *liste* échoue, la création de l’objet Key Vault échoue avec le code d’état HTTP *Interdit*. Les clés listées de cette manière sont mises en cache avec le stockage de votre entité de coffre de clés. 

Key Vault doit vérifier que l’identité a l’autorisation de *régénérer* pour pouvoir prendre possession de la régénération des clés. Pour vérifier que l’identité, par le biais du jeton OBO, ainsi que l’identité interne Key Vault disposent de ces autorisations :

- Key Vault liste les autorisations de contrôle d’accès en fonction du rôle sur la ressource du compte de stockage.
- Key Vault valide la réponse par le rapprochement d’actions et d’inactions avec des expressions régulières. 

Recherchez des exemples de prise en charge dans la section relative aux [exemples de clés de comptes de stockage gérés de Key Vault](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Si l’identité n’a pas d’autorisation de *régénération* ou que l’identité interne de Key Vault n’a pas l’autorisation de *répertorier* ou de *régénérer* des éléments, la demande d’intégration échoue en renvoyant un message et un code d’erreur appropriés. 

Le jeton OBO ne fonctionne que si l’on utilise des applications clientes natives internes de PowerShell ou CLI.

## <a name="other-applications"></a>Autres applications

- Les jetons SAP, construits à l’aide de clés de compte de stockage Key Vault, assurent un accès encore plus contrôlé à un compte de stockage Azure. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Voir aussi

- [Présentation des clés, des secrets et des certificats](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog de l’équipe Key Vault](https://blogs.technet.microsoft.com/kv/)
