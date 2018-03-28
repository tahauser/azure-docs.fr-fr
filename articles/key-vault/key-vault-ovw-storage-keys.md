---
ms.assetid: ''
title: Clés de compte de stockage Azure Key Vault
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: a3f8d540c7e4c8a86b151540980724777fd150fd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
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
1. Stockez la chaîne de connexion ou le jeton SAS dans les paramètres d’application Azure AppService ou un autre stockage.
1. Au démarrage de l’application, extrayez la chaîne de connexion ou le jeton SAS.
1. Créez [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) pour interagir avec le stockage.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Après l’apparition des clés de stockage Azure Key Vault

Les développeurs créent un [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) et l’exploitent pour obtenir le jeton SAS pour leur stockage. Ils créent ensuite [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) avec ce jeton.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Assistance développeur

- Autorisez uniquement Key Vault à gérer vos clés ASA. N’essayez pas de les gérer vous-même, vous interféreriez avec les processus de Key Vault.
- Ne permettez pas que les clés ASA soient gérées par plusieurs objets Key Vault.
- Si vous devez régénérer manuellement vos clés ASA, nous vous recommandons de le faire avec Key Vault.

## <a name="getting-started"></a>Prise en main

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuration des autorisations de contrôle d’accès en fonction des permissions (RBAC)

L’identité de l’application Azure Key Vault a besoin d’autorisations pour *répertorier* et *regénérer* des clés pour un compte de stockage. Configurez-les en suivant les étapes ci-dessous :

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Exemple d’utilisation

L’exemple suivant montre la création d’un compte de stockage Azure managé Key Vault et les définitions de signature d’accès partagé (SAP) associées.

### <a name="prerequisite"></a>Configuration requise

Vérifiez avoir terminé [Configurer les autorisations du Contrôle d'accès en fonction du rôle (RBAC)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Paramétrage

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Définissez ensuite les autorisations pour **votre compte** pour vous assurer que vous pouvez gérer toutes les autorisations de stockage dans Key Vault. Dans l’exemple ci-dessous, notre compte Azure est _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Créer un compte de stockage managé Key Vault

Créez maintenant un compte de stockage managé dans Azure Key Vault et utilisez une clé d’accès à partir de votre compte de stockage pour créer les jetons SAS.
- `-ActiveKeyName` utilise « key2 » pour générer les jetons SAS.
- `-AccountName` est utilisé pour identifier votre compte de stockage managé. Ci-dessous, nous utilisons le nom de compte de stockage pour qu’il soit simple, mais il peut s’agir de n’importe quel nom.
- `-DisableAutoRegenerateKey` spécifie de ne pas régénérer les clés de compte de stockage.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Régénération de clé

Si vous souhaitez que Key Vault régénère les clés d’accès de votre stockage régulièrement, vous pouvez définir une période de régénération. Ci-dessous, nous définissons une période de régénération de 3 jours. Après 3 jours, Key Vault régénère « key1 » et change la clé active « key2 » par « key1 ».

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Définir les définitions SAP

La SAP de compte fournit un accès au service blob avec différentes autorisations.
Définissez les définitions SAP dans Key Vault pour votre compte de stockage managé.
- `-AccountName` est le nom du compte de stockage managé dans Key Vault.
- `-Name` est l’identificateur du jeton SAS dans votre stockage.
- `-ValidityPeriod` définit la date d’expiration du jeton SAS généré.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Obtenir des jetons SAS

Obtenez les jetons SAP correspondants et effectuez des appels vers le stockage. `-SecretName` est construit à l’aide de l’entrée à partir des paramètres `AccountName` et `Name` lorsque vous avez exécuté [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/en-us/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Créer le stockage

Notez que la tentative d’accès avec *$readSasToken* échoue, mais que nous sommes en mesure d’établir l’accès avec *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Vous pouvez accéder au contenu blob de stockage avec le jeton SAP qui a un accès en écriture.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets PowerShell appropriées

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

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
