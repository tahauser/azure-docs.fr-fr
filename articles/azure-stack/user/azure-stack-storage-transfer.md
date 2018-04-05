---
title: Outils du stockage Azure Stack
description: En savoir plus sur les outils de transfert de données du stockage Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/30/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: e26a38b8fd7d008a46eba2c41075c5af09a6616a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="tools-for-azure-stack-storage"></a>Outils du stockage Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Microsoft Azure Stack fournit un ensemble de services de stockage pour les disques, les objets blob, les tables, les files d’attente, ainsi que des fonctions de gestion de compte. Vous pouvez utiliser un ensemble d’outils de stockage Azure si vous voulez gérer des données ou les déplacer vers ou à partir du stockage Azure Stack. Cet article fournit une vue d’ensemble rapide des outils disponibles.

L’outil qui vous convient le mieux dépend de vos besoins :
* [AZCopy](#azcopy)

    Utilitaire de ligne de commande propre au stockage que vous pouvez télécharger pour copier des données d’un objet vers un autre au sein de votre compte de stockage ou entre des comptes de stockage.

* [Azure PowerShell](#azure-powershell)

    Interpréteur de ligne de commande basé sur les tâches et langage de génération de scripts conçu spécialement pour l’administration de systèmes.

* [interface de ligne de commande Azure](#azure-cli)

    Outil multiplateforme open source qui fournit un ensemble de commandes pour utiliser les plateformes Azure et Azure Stack.

* [Explorateur Stockage Microsoft (préversion)](#microsoft-azure-storage-explorer)

    Application autonome, facile à utiliser, avec une interface utilisateur.

En raison des différences des services de stockage Azure et Azure Stack, chaque outil décrit dans les sections suivantes peut avoir des exigences spécifiques. Pour une comparaison entre le stockage Azure et le stockage Azure Stack, consultez [Stockage Azure Stack : Différences et considérations](azure-stack-acs-differences.md).


## <a name="azcopy"></a>AzCopy
AzCopy est un utilitaire de ligne de commande conçu pour copier des données à partir et vers le stockage Blob et Table Microsoft Azure à l’aide de commandes simples avec des performances optimales. Vous pouvez copier des données d’un objet vers un autre au sein de votre compte de stockage ou entre des comptes de stockage. Il existe deux versions d’AzCopy : AzCopy sur Windows et AzCopy sur Linux. Azure Stack prend uniquement en charge la version Windows. 
 
### <a name="download-and-install-azcopy"></a>Téléchargement et installation d’AzCopy 

[Téléchargez](https://aka.ms/azcopyforazurestack) la version Windows d’AzCopy prise en charge par Azure Stack. Vous pouvez installer et utiliser AzCopy sur Azure Stack comme sur Azure. Pour en savoir plus, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../../storage/common/storage-use-azcopy.md). 

 - Pour la mise à jour 1802 ou plus récente, [téléchargez AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
 - Pour des versions antérieures, [téléchargez AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20150405).

### <a name="azcopy-command-examples-for-data-transfer"></a>Exemples de commandes AzCopy pour le transfert de données
Les exemples suivants montrent différents scénarios permettant de copier des données vers et à partir d’objets blob Azure Stack. Pour en savoir plus, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../../storage/storage-use-azcopy.md). 
#### <a name="download-all-blobs-to-local-disk"></a>Télécharger tous les objets blob sur le disque local
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>Télécharger un fichier unique dans le répertoire virtuel 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Déplacer des données entre le stockage Azure et Azure Stack 
Le transfert de données asynchrone entre le stockage Azure et Azure Stack n’est pas pris en charge. Vous devez spécifier le transfert avec l’option **/SyncCopy**. 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Problèmes connus d’AzCopy
* Toute opération AzCopy sur le stockage Fichier n’est pas disponible, car le stockage Fichier n’est pas encore disponible dans Azure Stack.
* Le transfert de données asynchrone entre le stockage Azure et Azure Stack n’est pas pris en charge. Vous pouvez spécifier le transfert avec l’option **/SyncCopy** pour copier les données.
* La version Linux d’AzCopy n’est pas prise en charge pour le stockage Azure Stack. 

## <a name="azure-powershell"></a>Azure PowerShell
Le module Azure PowerShell fournit des applets de commande pour gérer des services sur Azure et Azure Stack. Il s’agit d’un interpréteur en ligne de commande basé sur les tâches et d’un langage de génération de scripts conçu spécialement pour l’administration de systèmes.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Installer et configurer PowerShell pour Azure Stack
Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md) et [Configurer l’environnement PowerShell de l’utilisateur Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack"></a>Exemple de script PowerShell pour Azure Stack 
Cet exemple suppose que vous avez [installé PowerShell pour Azure Stack](azure-stack-powershell-install.md). Ce script vous permet d’effectuer la configuration et de demander à votre locataire Azure Stack des informations d’identification pour ajouter votre compte à l’environnement PowerShell local. Ensuite, le script définit l’abonnement Azure par défaut, crée un compte de stockage dans Azure, crée un conteneur dans ce nouveau compte de stockage et charge un fichier image existant (blob) dans ce conteneur. Une fois que le script répertorie tous les objets blob de ce conteneur, il crée un répertoire de destination sur votre ordinateur local et télécharge le fichier image.

1. Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
2. Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  
3. Ouvrez **Windows PowerShell ISE** et **Exécuter en tant qu’administrateur**, cliquez sur **Fichier** > **Nouveau** pour créer un fichier script.
4. Copiez le script ci-dessous et collez-le dans le nouveau fichier script.
5. Mettez à jour les variables du script en fonction de vos paramètres de configuration. 
6. Remarque : Ce script doit être exécuté à la racine du **AzureStack_Tools** téléchargé. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Problèmes connus de PowerShell 
La version actuelle du module Azure PowerShell compatible avec Azure Stack est 1.2.10. Elle est différente de la dernière version d’Azure PowerShell. Cette différence impacte le fonctionnement des services de stockage :

* Le format de la valeur de retour de `Get-AzureRmStorageAccountKey` dans la version 1.2.10 a deux propriétés : `Key1` et `Key2`, alors que la version actuelle d’Azure retourne un tableau contenant toutes les clés de compte.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Pour plus d’informations, consultez [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Azure CLI
L’interface de ligne de commande Azure (Azure CLI) est l’expérience de ligne de commande d’Azure pour gérer les ressources Azure. Vous pouvez l’installer sur Windows, Linux et macOS, et l’exécuter à partir de la ligne de commande. 

Azure CLI est optimisé pour la gestion et l’administration des ressources Azure à partir de la ligne de commande, et pour la création de scripts d’automatisation qui opèrent sur Azure Resource Manager. Elle offre de nombreuses fonctionnalités similaires à celles du portail Azure Stack, notamment l’accès étendu aux données.

Azure Stack nécessite la version 2.0 d’Azure CLI. Pour plus d’informations sur l’installation et la configuration d’Azure CLI avec Azure Stack, consultez [Installer et configurer Azure Stack CLI](azure-stack-version-profiles-azurecli2.md). Pour plus d’informations sur l’utilisation d’Azure CLI 2.0 pour effectuer plusieurs tâches en utilisant des ressources dans votre compte de stockage Azure Stack, consultez [Utilisation d’Azure CLI 2.0 avec le stockage Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Exemple de script Azure CLI pour Azure Stack 
Une fois que vous avez terminé l’installation et la configuration de l’interface CLI, vous pouvez essayer les étapes suivantes pour utiliser un petit exemple de script shell afin d’interagir avec les ressources de stockage Azure Stack. Le script commence par créer un conteneur dans votre compte de stockage, puis charge un fichier existant (un objet blob, par exemple) dans ce conteneur, répertorie tous les objets blob dans le conteneur et, enfin, télécharge le fichier vers une destination que vous spécifiez sur votre ordinateur local. Avant d’exécuter ce script, vérifiez que vous êtes connecté au stockage Azure Stack cible. 
1. Ouvrez votre éditeur de texte préféré, puis copiez et collez le script précédent dans l’éditeur.
2. Mettez à jour les variables du script pour refléter vos paramètres de configuration. 
3. Une fois que vous avez mis à jour les variables nécessaires, enregistrez le script et quittez l’éditeur. Les étapes suivantes supposent que vous avez nommé votre script my_storage_sample.sh.
4. Rendez le script exécutable, si nécessaire : `chmod +x my_storage_sample.sh`
5. Exécutez le script. Par exemple, dans Bash : `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Explorateur Stockage Microsoft Azure

L’Explorateur Stockage Microsoft Azure est une application autonome de Microsoft. Cet outil vous permet d’utiliser facilement des données du stockage Azure et du stockage Azure Stack sur Windows, macOS et Linux. Si vous voulez un moyen simple de gérer les données de votre stockage Azure Stack, utilisez l’Explorateur Stockage Microsoft Azure.

 - Pour en savoir plus sur la configuration de l’Explorateur Stockage Azure afin d’utiliser Azure Stack, consultez [Connecter l’Explorateur Stockage à un abonnement Azure Stack](azure-stack-storage-connect-se.md).
 - Pour en savoir sur l’Explorateur Stockage Microsoft Azure, consultez [Bien démarrer avec l’Explorateur Stockage (préversion)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Étapes suivantes
* [Connecter l’Explorateur Stockage à un abonnement Azure Stack](azure-stack-storage-connect-se.md)
* [Bien démarrer avec l’Explorateur Stockage (préversion)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Stockage Azure : Différences et considérations](azure-stack-acs-differences.md)
* [Introduction à Stockage Microsoft Azure](../../storage/common/storage-introduction.md)

