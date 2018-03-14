---
title: "Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Ruby | Microsoft Docs"
description: "Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour Ruby, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seguler
ms.openlocfilehash: df885849e879317be6379767a09dd30a93687902
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Ruby

Dans ce guide de démarrage rapide, vous apprenez à utiliser Ruby pour charger, télécharger et lister des objets blob de blocs dans un conteneur de stockage blob Azure. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide : 
* Installer [Ruby](https://www.ruby-lang.org/en/downloads/)
* Installez la [bibliothèque Stockage Azure pour Ruby](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage) à l’aide du package rubygem. 

```
gem install azure-storage-blob
```

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application
L’[exemple d’application](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) utilisé dans ce démarrage rapide est une application Ruby de base.  

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir l’exemple d’application Ruby, recherchez le dossier storage-blobs-ruby-quickstart, puis ouvrez le fichier example.rb.  

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
Dans l’application, vous devez fournir le nom de votre compte de stockage et votre clé de compte pour créer l’instance `BlobService` associée à votre application. Ouvrez le fichier `example.rb` dans l’Explorateur de solutions de votre IDE. Remplacez les valeurs **accountname** et **accountkey** par votre nom de compte et votre clé de compte. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Exécution de l'exemple
Cet exemple permet de créer un fichier de test dans le dossier « Documents ». L’exemple de programme charge le fichier de test sur Stockage Blob, liste les objets blob du conteneur, puis télécharge le fichier sous un nouveau nom. 

Exécutez l’exemple. La sortie suivante est un exemple de sortie retournée durant l’exécution de l’application :
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quand vous appuyez sur une touche pour continuer, l’exemple de programme supprime le conteneur de stockage et les fichiers. Avant de continuer, recherchez les deux fichiers dans votre dossier « Documents ». Vous pouvez les ouvrir et constater qu’ils sont identiques.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Maintenant que vous avez compris l’exemple, ouvrez le fichier example.rb pour examiner le code. 

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage
La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent les uns sur les autres, chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet **BlobService** Stockage Azure pour configurer les informations d’identification de connexion. 
* Créez l’objet **Container**, qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

Une fois que vous avez le conteneur d’objets blob de cloud, vous pouvez créer l’objet **Block** qui pointe vers l’objet blob spécifique qui vous intéresse, puis effectuer des opérations de chargement, de téléchargement et de copie.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dans cette section, vous configurez une instance du client Stockage Azure, instanciez l’objet du service blob, créez un conteneur et définissez les autorisations sur le conteneur pour que les objets blob soient publics. Le conteneur est appelé **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Comme les objets blob de blocs sont les plus couramment utilisés, nous les utilisons dans ce démarrage rapide.  

Pour charger un fichier dans un objet blob, récupérez le chemin complet du fichier en joignant le nom de répertoire au nom de fichier sur votre disque local. Vous pouvez ensuite charger le fichier sur le chemin spécifié à l’aide de la méthode **create\_block\_blob()**. 

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement. Il stocke le fichier à charger en tant que **file\_path\_to\_file** et le nom de l’objet blob en tant que **local\_file\_name**. L'exemple suivant charge le fichier dans votre conteneur nommé **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Pour effectuer une mise à jour partielle du contenu d’un objet blob de bloc, exécutez la méthode **create\_block\_list()**. Les objets blob de blocs peuvent atteindre une taille maximale de 4.7 To et peuvent représenter toutes sortes d’éléments allant des feuilles de calcul Excel aux fichiers vidéo volumineux. Les objets blob de pages sont principalement utilisés pour les fichiers VHD utilisés pour stocker des machines virtuelles IaaS. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. Les objets blob d’ajout doivent être utilisés dans un modèle enregistreur unique. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de la méthode **list\_blobs()**. Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers en affichant les noms des objets blob trouvés dans un conteneur.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Télécharger les objets blob

Téléchargez les objets blob sur votre disque local à l’aide de la méthode **get\_blob()**. Le code suivant télécharge l’objet blob chargé dans une section précédente. « _DOWNLOADED » est ajouté en tant que suffixe au nom de l’objet blob, ce qui vous permet de voir les deux fichiers sur le disque local. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de la méthode **delete\_container()**. Si les fichiers créés ne sont plus nécessaires, utilisez la méthode **delete\_blob()** pour supprimer ces fichiers.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```

## <a name="next-steps"></a>étapes suivantes
 
Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et le stockage blob Azure avec Ruby. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le guide pratique des opérations Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](./storage-ruby-how-to-use-blob-storage.md)


Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
