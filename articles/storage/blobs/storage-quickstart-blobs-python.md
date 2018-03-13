---
title: "Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Python | Microsoft Docs"
description: "Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour Python, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: a82ec92449730a784e7cd36d7f364a9a4516c15f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-python"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Python

Dans ce guide de démarrage rapide, vous apprenez à utiliser Python pour charger, télécharger et lister des objets blob de blocs dans un conteneur de Stockage Blob Azure. 

## <a name="prerequisites"></a>configuration requise

Pour effectuer ce démarrage rapide : 
* Installez [Python](https://www.python.org/downloads/)
* Téléchargez et installez le [SDK Stockage Azure pour Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application
L’[exemple d’application](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) utilisé dans ce démarrage rapide est une application Python de base.  

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir le programme Python, recherchez le dossier storage-blobs-python-quickstart, puis le fichier example.py.  

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
Dans l’application, vous devez fournir votre nom de compte de stockage et votre clé de compte pour créer un objet `BlockBlobService`. Ouvrez le fichier `example.py` dans l’Explorateur de solutions de votre IDE. Remplacez les valeurs **accountname** et **accountkey** par votre nom de compte et votre clé de compte. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Exécution de l'exemple
Cet exemple permet de créer un fichier de test dans le dossier « Documents ». L’exemple de programme charge le fichier de test sur Stockage Blob, liste les objets blob du conteneur, puis télécharge le fichier sous un nouveau nom. 

Exécutez l’exemple. La sortie suivante est un exemple de sortie retournée durant l’exécution de l’application :
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quand vous appuyez sur une touche pour continuer, l’exemple de programme supprime le conteneur de stockage et les fichiers. Avant de continuer, recherchez les deux fichiers dans votre dossier « Documents ». Vous pouvez les ouvrir et constater qu’ils sont identiques.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Comme vous savez maintenant ce que fait l’exemple, ouvrez le fichier example.py pour examiner le code. 

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage
La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent les uns sur les autres, chacun est utilisé par le suivant dans la liste.

* Instanciez l’objet **BlockBlobService**, qui pointe vers le service blob de votre compte de stockage. 

* Instanciez l’objet **CloudBlobContainer** qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

Une fois que vous avez le conteneur d’objets blob de cloud, vous pouvez instancier l’objet **CloudBlockBlob** qui pointe vers l’objet blob spécifique qui vous intéresse, puis effectuer des opérations de chargement, de téléchargement et de copie.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dans cette section, vous instanciez les objets, créez un conteneur, puis définissez des autorisations sur le conteneur pour que les objets blob soient publics. Le conteneur est appelé **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Comme les objets blob de blocs sont les plus couramment utilisés, nous les utilisons dans ce démarrage rapide.  

Pour charger un fichier dans un objet blob, récupérez le chemin complet du fichier en joignant le nom de répertoire au nom de fichier sur votre disque local. Vous pouvez ensuite charger le fichier vers le chemin spécifié à l’aide de la méthode **create\_blob\_from\_path**. 

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement. Il stocke le fichier à charger en tant que **file\_path\_to\_file** et le nom de l’objet blob en tant que **local\_file\_name**. L'exemple suivant charge le fichier dans votre conteneur nommé **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Il existe plusieurs méthodes de chargement que vous pouvez utiliser avec Stockage Blob. Par exemple, si vous avez un flux de mémoire, vous pouvez utiliser la méthode **create\_blob\_from\_stream** à la place de **create\_blob\_from\_path**. 

Les objets blob de blocs peuvent atteindre une taille maximale de 4.7 To et peuvent représenter toutes sortes d’éléments allant des feuilles de calcul Excel aux fichiers vidéo volumineux. Les objets blob de pages sont principalement utilisés pour les fichiers VHD utilisés pour stocker des machines virtuelles IaaS. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de la méthode **list_blobs**. Cette méthode retourne un générateur. Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers en affichant les noms des objets blob trouvés dans un conteneur.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Télécharger les objets blob

Téléchargez les objets blob sur votre disque local à l’aide de la méthode **get\_blob\_to\_path**. Le code suivant télécharge l’objet blob chargé dans une section précédente. « _DOWNLOADED » est ajouté en tant que suffixe au nom de l’objet blob, ce qui vous permet de voir les deux fichiers sur le disque local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de **delete\_container**. Si les fichiers créés ne sont plus nécessaires, utilisez la méthode **delete\_blob** pour supprimer ces fichiers.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Étapes suivantes
 
Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et Stockage Blob Azure avec Python. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le guide pratique des opérations Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](./storage-python-how-to-use-blob-storage.md)
 

Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
