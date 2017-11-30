---
title: "Utilisation du stockage d’objets blob (Stockage Blob Azure) à partir de Python | Microsoft Docs"
description: "Stockez des données non structurées dans le cloud avec Azure Blob Storage (stockage d’objets)."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: b94a0f95454f9243ef09ce37a62466bca4003a91
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Utilisation du stockage d’objets blob Azure à partir de Python
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d'ensemble
Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets/blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Blob Storage.

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage d’objets blob. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent le chargement, l'énumération, le téléchargement et la suppression d'objets blob.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Télécharger et installer le SDK Stockage Azure pour Python

Le SDK Stockage Azure pour Python requiert Python 2.7, 3.3, 3.4, 3.5 ou 3.6 et est fourni dans 4 packages différents : `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` et `azure-storage-queue`. Dans ce didacticiel, nous allons utiliser le package `azure-storage-blob`.
 
### <a name="install-via-pypi"></a>Effectuer l’installation via PyPi

Pour effectuer l’installation via PyPI (Python Package Index), tapez :

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Si vous effectuez une mise à niveau à partir du SDK Stockage Azure pour Python version 0.36 ou antérieure, vous devez d’abord effectuer une désinstallation au moyen de `pip uninstall azure-storage` car nous ne publierons plus le SDK Stockage pour Python dans un package unique.
> 
> 

Pour connaître les autres méthodes d’installation, visitez la page consacrée au [SDK Stockage Azure pour Python sur Github](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Créez un conteneur.
Selon le type d’objet blob que vous souhaitez utiliser, créez un objet **BlockBlobService**, **AppendBlobService** ou **PageBlobService**. Le code suivant utilise un objet **BlockBlobService** . Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder au stockage d’objet blob de blocs Azure par programme.

```python
from azure.storage.blob import BlockBlobService
```

Le code suivant crée un objet **BlockBlobService** à l’aide du nom et de la clé du compte de stockage.  Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Dans l’exemple de code suivant, vous pouvez utiliser un objet **BlockBlobService** pour créer le conteneur s’il n’existe pas.

```python
block_blob_service.create_container('mycontainer')
```

Par défaut, le nouveau conteneur est privé. Vous devez donc indiquer votre clé d’accès au stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les objets blob du conteneur soient accessibles à tout le monde, vous pouvez créer le conteneur et lui attribuer le niveau d’accès public en utilisant le code suivant.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

L’autre possibilité consiste à modifier un conteneur déjà créé, à l’aide du code suivant.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

Après cette modification, tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais vous êtes le seul à pouvoir les modifier ou les supprimer.

## <a name="upload-a-blob-into-a-container"></a>Charger un objet blob dans un conteneur
Pour créer un objet blob de blocs et télécharger des données, utilisez les méthodes **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** ou **create\_blob\_from\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**create\_blob\_from\_path** télécharge le contenu d’un fichier à partir du chemin spécifié, et **create\_blob\_from\_stream** télécharge le contenu à partir d’un flux/fichier déjà ouvert. **create\_blob\_from\_bytes** télécharge un tableau d’octets, et **create\_blob\_from\_text** télécharge la valeur texte spécifiée à l’aide de l’encodage spécifié (par défaut UTF-8).

L’exemple suivant charge le contenu du fichier **sunset.png** dans l’objet blob **myblockblob**.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Création d'une liste d'objets blob dans un conteneur
Pour répertorier les objets blob dans un conteneur, utilisez la méthode **list\_blobs**. Cette méthode retourne un générateur. Le code suivant sort le **nom** de chaque objet blob d'un conteneur sur la console.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Télécharger des objets blob
Pour télécharger des données d’un objet blob, utilisez **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de **get\_blob\_to\_path** pour télécharger le contenu de l’objet blob **myblockblob** et le stocker dans le fichier **out-sunset.png**.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Supprimer un objet blob
Pour supprimer un objet blob, appelez **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Écriture dans un objet blob d’ajout
Il est optimisé pour les opérations d’ajout, telles que la journalisation. Comme un objet blob de blocs, un objet blob d’ajout est composé de blocs. Mais lorsqu’il est ajouté à un objet blob d’ajout, un nouveau bloc l’est toujours à la fin. Vous ne pouvez pas mettre à jour ou supprimer un bloc dans un objet blob d’ajout. Les ID de bloc dans un objet blob d’ajout ne sont pas visibles, comme pour un objet blob de blocs.

Chaque bloc d’un objet blob d’ajout peut avoir une taille différente (jusqu’à 4 Mo), et un objet blob d’ajout peut contenir au maximum 50 000 blocs. La taille maximale d’un objet blob d’ajout est donc légèrement supérieure à 195 Go (4 Mo x 50 000 blocs).

L’exemple ci-dessous crée un objet blob d’ajout et y ajoute des données pour simuler une opération de journalisation simple.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les bases du stockage d’objets blob, consultez les liens suivants pour en savoir plus.

* [Centre de développement Python](https://azure.microsoft.com/develop/python/)
* [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog de l'équipe Azure Storage]
* [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]

[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
