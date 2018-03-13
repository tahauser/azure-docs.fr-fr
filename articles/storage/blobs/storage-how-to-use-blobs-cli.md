---
title: "Effectuer des opérations sur Stockage Blob Azure (stockage d’objets) avec Azure CLI | Microsoft Docs"
description: "Découvrez comment charger et télécharger des objets blob dans Stockage Blob Azure, puis créer une signature d’accès partagé (SAP) pour gérer l’accès à un objet blob dans votre compte de stockage."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: 4f781508652ee784fff61c8ecc76cb87ea47f0c2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Effectuer des opérations de stockage d’objets blob avec Azure CLI

Stockage Blob Azure est un service permettant de stocker de gros volumes de données d’objets non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Ce didacticiel décrit les opérations de base dans Stockage Blob Azure, notamment le chargement, le téléchargement et la suppression d’objets blob. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un conteneur.
> * Charger un fichier (objet blob) dans un conteneur
> * Créer la liste des objets blob d’un conteneur
> * Télécharger un objet blob depuis un conteneur
> * Copier un objet blob entre des comptes de stockage
> * Supprimer un objet blob
> * Afficher et modifier les propriétés et métadonnées d’un objet blob
> * Gérer la sécurité avec une signature d’accès partagé (SAP)

Ce didacticiel nécessite Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Créez un conteneur.

À l’instar des répertoires de votre ordinateur dans lesquels vous pouvez organiser des fichiers, les conteneurs vous permettent d’organiser des groupes d’objets blob. Un compte de stockage peut avoir un certain nombre de conteneurs. Vous pouvez stocker jusqu’à 500 To de données d’objets blob dans un conteneur, ce qui correspond à la quantité maximale de données dans un compte de stockage.

Créez un conteneur pour stocker des objets blob avec la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Les noms de conteneur doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des traits d’union (-). Pour prendre connaissance des autres règles de nommage des objets blob et des conteneurs, consultez [Nommage et référencement des conteneurs, objets blob et métadonnées](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Activer l’accès en lecture public pour un conteneur

Par défaut, un conteneur qui vient d’être créé est privé. Autrement dit, l’accès au conteneur nécessite une [signature d’accès partagé](#create-a-shared-access-signature-sas) ou les clés d’accès du compte de stockage. Azure CLI vous permet de modifier ce comportement. Pour cela, choisissez le niveau d’autorisation du conteneur parmi les trois suivants :

| | |
|---|---|
| `--public-access off` | (Par défaut) Aucun accès en lecture public |
| `--public-access blob` | Accès en lecture public aux objets blob uniquement |
| `--public-access container` | Accès en lecture public aux objets blob, possibilité de lister les objets blob dans le conteneur |

Si vous choisissez `blob` ou `container` comme accès public, vous activez l’accès en lecture seule pour toute personne sur Internet. Par exemple, si vous souhaitez afficher des images stockées sous forme d’objets blob sur votre site web, vous devez activer l’accès en lecture public. Si vous souhaitez activer l’accès en lecture/écriture, vous devez utiliser une [signature d’accès partagé](#create-a-shared-access-signature-sas) à la place.

Activez l’accès en lecture public pour votre conteneur avec la commande [az storage container set-permission](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Charger un objet blob dans un conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus courants dans Stockage Azure. Les objets blob d’ajout sont utilisés quand les données doivent être ajoutées à un objet blob existant sans modifier son contenu existant, par exemple pour la journalisation. Les objets blob de pages stockent les fichiers de disque dur virtuel des machines virtuelles IaaS.

Dans cet exemple, nous chargeons un objet blob dans le conteneur que nous avons créé à la dernière étape avec la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe. Chargez plusieurs fichiers de manière à ce que plusieurs entrées apparaissent quand vous listez les objets blob à l’étape suivante.

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Listez les objets blob d’un conteneur avec la commande [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Exemple de sortie :

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Télécharger un objet blob

Téléchargez l’objet blob que vous avez chargé dans une étape précédente à l’aide de la commande [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Copier un objet blob entre des comptes de stockage

Vous pouvez copier des objets blob au sein d’un compte de stockage, ou vers des comptes de stockage et des régions et ce, de façon asynchrone.

L’exemple suivant indique comment copier des objets blob depuis un compte de stockage et les coller dans un autre. Nous créons d’abord un conteneur dans le compte de stockage source, en spécifiant l’accès en lecture public pour ses objets blob. Ensuite, nous chargeons un fichier dans le conteneur. Pour finir, nous copions l’objet blob de ce conteneur vers un conteneur du compte de stockage de destination.

Dans cet exemple, le conteneur de destination doit figurer dans le compte de stockage de destination pour que l’opération de copie réussisse. En outre, l’objet blob source spécifié dans l’argument `--source-uri` doit inclure un jeton de signature d’accès partagé, ou être accessible publiquement, comme dans cet exemple.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Supprimer un objet blob

Supprimez l’objet blob du conteneur à l’aide de la commande [az storage blob delete](/cli/azure/storage/blob#az_storage_blob_delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>Définir le type de contenu

Le type de contenu, également appelé type MIME, identifie le format des données dans le blob. Les navigateurs et les autres logiciels utilisent le type de contenu pour déterminer comment traiter les données. L’exemple suivant définit le type de contenu sur `image/png`.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Afficher et modifier les propriétés et métadonnées d’un objet blob

Chaque objet blob comprend plusieurs propriétés définies par le service (nom, type, longueur, etc.) que vous pouvez afficher avec la commande [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show). Vous pouvez également configurer un objet blob avec vos propres propriétés et leurs valeurs à l’aide de la commande [az storage blob metadata update](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_update).

Dans cet exemple, nous commençons par afficher les propriétés définies par le service d’un objet blob, puis nous le mettons à jour avec deux de nos propres propriétés de métadonnées. Enfin, nous affichons les propriétés de métadonnées de l’objet blob et leurs valeurs avec la commande [az storage blob metadata show](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Créer une signature d’accès partagé (SAP)

Les signatures d’accès partagé (SAP) constituent un moyen d’octroyer un accès limité aux objets dans votre compte de stockage, sans exposer les clé d’accès de votre compte de stockage. Pour produire un URI qui autorise l’accès à une ressource privée, créez un jeton SAP avec les autorisations et la fenêtre de validité (sa durée effective) désirées. Ajoutez-le ensuite comme chaîne de requête à l’URL d’une ressource pour former un URI SAP complet. Toute personne disposant de cet URI SAP (URL de la ressource plus jeton SAP) peut accéder à la ressource pendant la fenêtre de validité du jeton SAP. Par exemple, vous pouvez autoriser l’accès en lecture à un objet blob privé pendant deux minutes pour permettre à quelqu’un de l’afficher.

Dans les étapes suivantes, vous allez désactiver l’accès public à votre conteneur, tester l’accès privé uniquement, puis générer et tester un URI SAP.

### <a name="disable-container-public-access"></a>Désactiver l’accès public au conteneur

Tout d’abord, choisissez `off` comme niveau d’accès du conteneur. Cela signifie qu’une signature d’accès partagé ou une clé d’accès de compte de stockage est nécessaire pour accéder au conteneur ou à ses objets blob.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Vérifier l’accès privé

Pour vérifier que l’accès en lecture public aux objets blob du conteneur n’est pas autorisé, obtenez l’URL de l’un de ses objets blob avec la commande [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Accédez à l’URL de l’objet blob dans une fenêtre de navigation privée. L’erreur `ResourceNotFound` se produit, car l’objet blob est privé et vous n’avez pas inclus de signature d’accès partagé.

### <a name="create-a-sas-uri"></a>Créer un URI SAP

Nous allons maintenant créer un URI SAP qui autorise l’accès à l’objet blob. Dans l’exemple suivant, nous commençons par définir une variable avec l’URL de l’objet blob à l’aide de la commande [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url), puis nous définissons une autre variable avec un jeton SAP généré à l’aide de la commande [az storage blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas). Pour terminer, nous générons l’URI SAP complet de l’objet blob en concaténant les deux variables avec le séparateur de chaîne de requête `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Tester l’URI SAP

Dans une fenêtre de navigation privée, accédez à l’URI SAP complet que vous avez affiché avec la commande `echo` dans l’extrait de code précédent. Cette fois-ci, aucune erreur ne se produit et vous pouvez afficher ou télécharger l’objet blob.

Attendez que l’URL arrive à expiration (deux minutes dans cet exemple), puis accédez à l’URI dans une autre fenêtre de navigation privée. Vous recevez à présent l’erreur `AuthenticationFailed` car le jeton SAP a expiré.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment le compte de stockage créé et les objets blob chargés dans ce didacticiel, supprimez le groupe de ressources avec la commande [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez découvert les bases de l’utilisation d’objets blob dans Stockage Azure :

> [!div class="checklist"]
> * Créez un conteneur.
> * Charger un fichier (objet blob) dans un conteneur
> * Créer la liste des objets blob d’un conteneur
> * Télécharger un objet blob depuis un conteneur
> * Copier un objet blob entre des comptes de stockage
> * Supprimer un objet blob
> * Afficher et modifier les propriétés et métadonnées d’un objet blob
> * Gérer la sécurité avec une signature d’accès partagé (SAP)

Les ressources suivantes fournissent des informations supplémentaires sur l’utilisation d’Azure CLI et l’utilisation des ressources dans votre compte de stockage.

* Azure CLI
  * [Se connecter avec Azure CLI 2.0](/cli/azure/authenticate-azure-cli) : découvrez les différentes méthodes d’authentification avec l’interface de ligne de commande, notamment la connexion non interactive par le biais du [principal de service](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) pour l’exécution de scripts Azure CLI sans assistance.
  * [Informations de référence sur les commandes Azure CLI 2.0](/cli/azure/)
* Explorateur Stockage Microsoft Azure
  * L’[Explorateur Stockage Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
