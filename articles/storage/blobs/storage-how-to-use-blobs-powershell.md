---
title: "Effectuer des opérations sur Stockage Blob Azure (stockage d’objets) avec PowerShell | Microsoft Docs"
description: "Didacticiel : effectuer des opérations sur Stockage Blob Azure (stockage d’objets) avec PowerShell"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 09/14/2017
ms.author: tamram
ms.openlocfilehash: 8b87ecc3fefe5338269a6ad584e9a097ff3fb633
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Effectuer des opérations sur Stockage File d’attente Azure avec Azure PowerShell

Stockage Blob Azure est un service permettant de stocker de gros volumes de données d’objets non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Cet article décrit les opérations de base dans Stockage Blob Azure, comme le chargement, le téléchargement et la suppression d’objets blob. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un conteneur. 
> * Charger des objets blob
> * Créer la liste des objets blob d’un conteneur 
> * Télécharger des objets blob
> * Copier des objets blob
> * Suppression d’objets blob
> * Afficher et définir les métadonnées et les propriétés d’un objet blob
> * Gérer la sécurité à l’aide de signatures d’accès partagé

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. À l’instar des répertoires de votre ordinateur dans lesquels vous pouvez organiser des fichiers, les conteneurs vous permettent d’organiser des groupes d’objets blob. Un compte de stockage peut avoir un nombre illimité de conteneurs ; sa seule limite est la quantité d’espace qu’il occupe dans le compte de stockage (jusqu’à 500 To). 

Quand vous créez un conteneur, vous pouvez définir le niveau d’accès, ce qui vous permet de définir qui peut accéder à des objets blob dans le conteneur. Par exemple, un conteneur peut être privé (niveau d’accès = `Off`), ce qui signifie que toute personne souhaitant y accéder doit disposer d’une signature d’accès partagé ou des clés d’accès du compte de stockage. Si vous ne spécifiez pas le niveau d’accès du conteneur au moment de sa création, il est privé par défaut.

Vous souhaitez peut-être que les images dans votre conteneur soient accessibles publiquement. Par exemple, si vous voulez stocker des images pour les afficher sur votre site web, celles-ci doivent être publiques. Dans ce cas, affectez au conteneur le niveau d’accès `blob` pour que toute personne disposant d’une URL pointant vers un objet blob dans le conteneur puisse accéder à l’objet blob.

Pour créer le conteneur, définissez son nom, puis créez-le en choisissant les autorisations « blob ». Les noms de conteneur doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des traits d’union (-). Pour prendre connaissance des autres règles de nommage des objets blob et des conteneurs, consultez [Nommage et référencement des conteneurs, objets blob et métadonnées](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Créez un conteneur avec [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Affectez le niveau d’accès public. Le nom du conteneur dans cet exemple est *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Charger des objets blob dans un conteneur

Stockage Blob Azure prend en charge les objets blob de blocs, d’ajout et de pages.  Les fichiers de disque dur virtuel utilisés pour appuyer les machines virtuelles IaaS sont des objets blob de pages. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des fichiers stockés dans Stockage Blob sont des objets blob de blocs. 

Pour charger un fichier sur un objet blob de blocs, obtenez une référence de conteneur, puis obtenez une référence à l’objet blob de blocs dans ce conteneur. Une fois que vous avez la référence à l’objet blob, vous pouvez y charger des données à l’aide de [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Cette opération crée l’objet blob s’il n’existe pas ou le remplace s’il existe déjà.

Voici comment charger un objet blob dans un conteneur. Tout d’abord, définissez les variables qui pointent vers le répertoire sur l’ordinateur local où se trouvent les fichiers, puis définissez une variable pour le nom du fichier à charger. C’est utile quand vous souhaitez effectuer la même opération plusieurs fois. Chargez plusieurs fichiers pour que la liste des objets blob du conteneur comprenne plusieurs entrées.

Les exemples suivants chargent Image001.jpg et Image002.png à partir de l’emplacement D:\\_TestImages sur le disque local dans le conteneur que vous venez de créer.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Chargez un autre fichier. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Chargez autant de fichiers que vous le souhaitez avant de continuer.

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Utilisez [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) pour obtenir la liste des objets blob dans le conteneur, puis sélectionnez le nom de l’objet blob à afficher.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez les objets blob sur votre disque local. Commencez par définir une variable qui pointe vers le dossier local dans lequel vous souhaitez télécharger les objets blob. Puis, pour chaque objet blob à télécharger, définissez le nom et appelez [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) pour télécharger l’objet blob.

Cet exemple copie les objets blob dans D:\\_TestImages\Downloads sur le disque local. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Copier des objets blob

Tenez compte des points suivants concernant la copie d’objets blob. Vous pouvez simplement copier l’objet blob sous un nouveau nom dans le même emplacement. Vous pouvez copier l’objet blob dans un compte de stockage distinct. Vous pouvez copier un objet blob volumineux (par exemple, un fichier de disque dur virtuel) dans un autre compte de stockage. Chaque opération est différente.

### <a name="simple-blob-copy"></a>Copie d’objet blob simple

Vous pouvez effectuer une copie de l’un des objets blob dans un conteneur en le copiant dans un nouvel objet blob. Cet exemple le copie dans le même conteneur sous un nom différent, mais vous pouvez tout aussi facilement créer un autre conteneur pour y copier l’objet blob. Cet exemple montre comment utiliser [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) pour copier l’objet blob. Vous devez spécifier le nom du conteneur et celui de l’objet blob (source et destination).

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Copier un objet blob dans un autre compte de stockage 

Vous souhaitez peut-être copier un objet blob dans un compte de stockage distinct. Vous pouvez par exemple copier un fichier de disque dur virtuel qui appuie l’une de vos machines virtuelles vers un autre compte de stockage pour le sauvegarder. 

Configurez un deuxième compte de stockage, récupérez le contexte, configurez un conteneur dans ce compte de stockage, puis effectuez la copie. Cette partie du script est quasiment identique au script ci-dessus, à la différence près qu’elle fait appel au deuxième compte de stockage au lieu du premier.

```powershell
#create new storage account, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Copie d’objets blob volumineux de manière asynchrone

Si vous copiez un objet blob volumineux de plusieurs gigaoctets, vous pouvez tirer parti de la copie asynchrone. Pour cela, démarrez-la, puis revenez pour prendre connaissance de son état jusqu’à ce qu’elle soit terminée. De cette manière, vous n’êtes pas immobilisé pendant l’opération de copie.

Si vous effectuez la copie dans un même compte de stockage, l’opération est très rapide. Si vous effectuez la copie entre deux comptes de stockage dans la même région, la copie est assez rapide. En revanche, si votre source et votre destination sont dans des régions distinctes, l’opération peut prendre plusieurs heures en fonction de la distance et de la taille de votre fichier. 

Ce script utilise les mêmes variables définies dans le dernier exemple de copie. La différence tient au fait que cet exemple capture l’état de la copie et qu’il l’interroge toutes les 10 secondes jusqu’à la fin de l’opération. L’opération peut nécessiter plusieurs itérations si vous chargez un objet blob volumineux dans votre compte de stockage.

Utilisez [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) pour interroger l’état de la copie. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Suppression d’objets blob

Pour supprimer des objets blob d’un compte de stockage, utilisez [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Lire et écrire les métadonnées et les propriétés d’un objet blob

Pour accéder au jeu de propriétés et de méthodes d’un **IListBlobItem** retourné, vous devez le convertir en objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l’appel. Le code suivant montre comment récupérer et générer les propriétés de l’un des objets blob chargés précédemment en utilisant [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) et en convertissant le résultat en objet CloudBlockBlob.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Remplissez les propriétés système en appelant FetchAttributes, puis affichez ces propriétés. Vous pouvez changer la valeur de certaines propriétés accessibles en écriture. Cet exemple montre comment changer le type de contenu, également appelé type MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Chaque objet blob a ses propres métadonnées que vous pouvez définir. Par exemple, vous pouvez stocker le nom de l’utilisateur qui a chargé l’objet blob, ou la date et l’heure de son premier chargement. Les métadonnées se composent de paires clé/valeur. Vous pouvez les modifier à l’aide de PowerShell comme suit.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Gestion de la sécurité pour les objets blob 

Par défaut, Stockage Azure préserve la sécurité de vos données en limitant l’accès au propriétaire du compte, qui est en possession des clés d’accès au compte de stockage. Lorsque vous avez besoin de partager des données d’objets blob de votre compte de stockage, il est important de le faire sans compromettre la sécurité de vos clés d’accès au compte. Pour ce faire, vous pouvez utiliser l’URL d’une signature d’accès partagé. Celle-ci se compose d’une URL à la ressource qui inclut des paramètres de requête et d’un jeton de sécurité qui accorde un niveau spécifique d’autorisation pour une durée spécifique. Par exemple, vous pouvez autoriser l’accès en lecture à un objet blob privé pendant 5 minutes pour permettre à quelqu’un de l’afficher. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Définir le niveau d’accès privé pour le conteneur et ses objets blob

Commencez par choisir le niveau d’accès `Off`. Ce niveau exige une signature d’accès partagé ou la clé de compte pour accéder au conteneur. Utilisez [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Tester l’accès privé

Pour vérifier que vous n’avez pas accès aux objets blob dans le conteneur, construisez l’URL vers l’un des objets blob sans signature d’accès partagé et essayez d’afficher l’objet blob. L’URL a le format suivant (protocole HTTPS) :

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Cet exemple montre comment créer l’URL de l’objet blob.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Copiez l’URL de l’objet blob et collez-la dans une fenêtre de navigation privée. Étant donné que l’objet blob est privé et que vous n’avez pas inclus de signature d’accès partagé, une erreur se produit. 

### <a name="create-the-sas-uri"></a>Créer l’URI SAP

Créer un URI SAP : il s’agit du lien vers l’objet blob. Celui-ci inclut les paramètres de requête et le jeton de sécurité qui composent la signature d’accès partagé. Copiez cet URI dans une fenêtre de navigation privée. L’image s’affiche. 

Commencez par créer les dates et heures de début et d’expiration pour l’accès. Cet exemple utilise une fenêtre de 2 minutes. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Créez l’URI SAP à l’aide de [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). Il vous faut le nom du conteneur, le nom de l’objet blob, le contexte du compte de stockage, les autorisations à accorder (dans ce cas, lecture, écriture et suppression), ainsi que l’heure de début et de fin pour l’accès. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Copiez l’URI SAP et collez-le dans une fenêtre de navigation privée. L’image s’affiche.

Attendez que l’URL arrive à expiration (2 minutes dans cet exemple), puis collez l’URL dans une autre fenêtre de navigation privée. Cette fois-ci, vous obtenez une erreur d’autorisation et l’image n’est pas affichée puisque l’URI SAP a expiré.

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez toutes les ressources que vous avez créées. Vous pouvez pour cela supprimer le groupe de ressources, ce qui entraîne également la suppression de toutes les ressources contenues dans le groupe. Dans le cas présent, tous les comptes de stockage et le groupe de ressources sont supprimés.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>étapes suivantes

Ce didacticiel vous a présenté les bases de la gestion de stockage d’objets blob. Vous avez notamment appris à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créez un conteneur. 
> * Charger des objets blob
> * Créer la liste des objets blob d’un conteneur 
> * Télécharger des objets blob
> * Copier des objets blob
> * Suppression d’objets blob
> * Lire et écrire les métadonnées et les propriétés d’un objet blob
> * Gérer la sécurité à l’aide de signatures d’accès partagé

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Applets de commande Microsoft Azure PowerShell - Stockage
* [Applets de commande PowerShell - Stockage](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorateur Stockage Microsoft Azure
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
