---
title: "Prise en main du stockage d’objets blob Azure et des services connectés de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Comment prendre en main le stockage d’objets blob Azure dans le cadre d’un projet ASP.NET Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: kraig
ms.openlocfilehash: cb406e528568dafd1e142943f5273ad58e550609
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main du stockage d’objets blob Azure et des services connectés de Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets ou blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Stockage Blob.

Ce didacticiel montre comment écrire du code ASP.NET pour des scénarios courants d’utilisation du stockage d’objets blob Azure. Ces scénarios incluent la création d’un conteneur d’objets blob, ainsi que le chargement, la création d’une liste, le téléchargement et la suppression d’objets blob.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Conditions préalables

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Créer un contrôleur MVC 

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs**.

2. Dans le menu contextuel, sélectionnez **Ajouter** > **Contrôleur**.

    ![Capture d’écran de l’Explorateur de solutions, avec les options Ajouter et Contrôleur en surbrillance](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Dans la boîte de dialogue **Ajouter la structure**, cliquez sur **Contrôleur MVC 5 - vide**, puis sélectionnez **Ajouter**.

    ![Capture d’écran de la boîte de dialogue Ajouter une structure](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Dans la boîte de dialogue **Ajouter un contrôleur**, nommez le contrôleur *BlobsController*, puis sélectionnez **Ajouter**.

    ![Capture d'écran de la boîte de dialogue Ajouter un contrôleur](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Ajoutez les directives `using` suivantes au fichier `BlobsController.cs` :

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Se connecter à un compte de stockage et récupérer une référence de conteneur

Un conteneur d’objets blob est une hiérarchie imbriquée d’objets blobs et de dossiers. Le reste des étapes de ce document requiert une référence à un conteneur d’objets blob ; ce code doit donc être placé dans sa propre méthode à des fins de réutilisation.

Les étapes suivantes permettent de créer une méthode pour se connecter au compte de stockage à l’aide de la chaîne de connexion dans **Web.config**. Elles créent également une référence à un conteneur.  Le nom du paramètre de chaîne de connexion dans **Web.config** est au format `<storageaccountname>_AzureStorageConnectionString`. 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **GetCloudBlobContainer** qui renvoie un **CloudBlobContainer**.  Veillez à remplacer `<storageaccountname>_AzureStorageConnectionString` par le nom réel de la clé dans **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Même si *test-blob-container* n’existe pas encore, ce code crée une référence à ce conteneur. Ainsi, celui-ci pourra être créé avec la méthode `CreateIfNotExists` indiquée à l’étape suivante.

## <a name="create-a-blob-container"></a>Création d’un conteneur d’objets blob

Les étapes suivantes montrent comment créer un conteneur d’objets blob :

1. Ajoutez une méthode appelée `CreateBlobContainer` qui renvoie un `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Récupérez un objet `CloudBlobContainer` représentant une référence au nom souhaité pour le conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Appelez la méthode `CloudBlobContainer.CreateIfNotExists` pour créer le conteneur s’il n’existe pas encore. La méthode `CloudBlobContainer.CreateIfNotExists` renvoie **true** si le conteneur n’existe pas et est créé avec succès. Sinon, la méthode retourne **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Mettez à jour l’élément `ViewBag` avec le nom du conteneur d’objets blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Le code suivant illustre la méthode `CreateBlobContainer` achevée :

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Vues**.

2. Dans le menu contextuel, sélectionnez **Ajouter** > **Nouveau dossier**. Nommez ce nouveau dossier *Objets blob*. 
 
1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** et cliquez avec le bouton droit sur **Objets blob**.

4. Dans le menu contextuel, sélectionnez **Ajouter** > **Affichage**.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **CreateBlobContainer** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `CreateBlobContainer.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Créer un conteneur d’objets blob** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Capture d’écran d’un conteneur d’objets blob](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Comme mentionné précédemment, la méthode `CloudBlobContainer.CreateIfNotExists` renvoie **true** uniquement lorsque le conteneur n’existe pas et est créé. Par conséquent, si l’application s’exécute alors que le conteneur existe, la méthode renvoie la valeur **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Charger un objet blob dans un conteneur d’objets blob

Une fois que le [conteneur d’objets blob](#create-a-blob-container) est créé, chargez des fichiers dans ce conteneur. Cette section présente la procédure à suivre pour charger un fichier local dans un conteneur d’objets blob. Dans ces étapes, partez du principe qu’il existe un conteneur d’objets blob nommé *test-blob-container*. 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée `UploadBlob` qui renvoie une chaîne.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dans la méthode `UploadBlob`, récupérez un objet `CloudBlobContainer` représentant une référence au nom souhaité pour le conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Le stockage Azure prend en charge différents types d’objets blob. Ce didacticiel utilise des objets blob de blocs. Pour récupérer une référence à un objet blob de blocs, appelez la méthode `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Le nom de l’objet blob fait partie de l’URL utilisée pour récupérer un objet blob et peut être n’importe quelle chaîne, y compris le nom du fichier.

1. Une fois que vous disposez d’une référence d’objet blob, vous pouvez charger n’importe quel flux de données vers cet objet en appelant la méthode `UploadFromStream`pour l’objet de cette référence. La méthode `UploadFromStream` crée l’objet blob s’il n’existe pas ou le remplace s’il est déjà présent. (Remplacez *&lt;file-to-upload>* par un chemin d’accès complet au fichier à charger.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Le code suivant montre la méthode `UploadBlob` achevée (avec un chemin d’accès complet au fichier à charger) :

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Upload blob** (Charger l’objet blob).  Le mot *success* doit apparaître.
    
    ![Capture d’écran de vérification de réussite](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Répertorier les objets blob d’un conteneur d’objets blob

Cette section explique comment répertorier les objets blob d’un conteneur d’objets blob. Les exemples de code font référence au conteneur *test-blob-container* créé dans la section [Création d’un conteneur d’objets blob](#create-a-blob-container).

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée `ListBlobs` qui renvoie un `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Dans la méthode `ListBlobs`, récupérez un objet `CloudBlobContainer` représentant une référence au conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Pour répertorier les objets blob dans un conteneur d’objets blob, utilisez la méthode `CloudBlobContainer.ListBlobs`. La méthode `CloudBlobContainer.ListBlobs` renvoie un objet `IListBlobItem` pouvant être casté en un objet `CloudBlockBlob`, `CloudPageBlob` ou `CloudBlobDirectory`. L’extrait de code suivant énumère tous les objets blob dans un conteneur d’objets blob. Chaque objet blob est casté en un objet correspondant à son type. Son nom (ou l’URI dans le cas d’un **CloudBlobDirectory**) est ajouté à une liste.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Outre des objets blobs, les conteneurs d’objets blob peuvent contenir des répertoires. Partez du principe que vous disposez d’un conteneur d’objets blob appelé *test-blob-container* qui présente la hiérarchie suivante :

        foo.png
        dir1/bar.png
        dir2/baz.png

    Avec l’exemple de code précédent, la liste de la chaîne **blobs** contient des valeurs similaires à ce qui suit :

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Comme illustré, la liste inclut uniquement les entités de niveau supérieur ; elle n’inclut pas celles qui sont imbriquées (*bar.png* et *baz.png*). Pour répertorier toutes les entités d’un conteneur d’objets blob, modifiez le code de façon que la méthode **CloudBlobContainer.ListBlobs** passe sur **true** pour le paramètre **useFlatBlobListing**.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    La définition du paramètre **useFlatBlobListing** sur **true** renvoie une liste plate de toutes les entités du conteneur d’objets blob. Cela génère les résultats suivants :

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Le code suivant illustre la méthode **ListBlobs** achevée :

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** et cliquez avec le bouton droit sur **Objets blob**.

2. Dans le menu contextuel, sélectionnez **Ajouter** > **Affichage**.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez `ListBlobs` pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez `ListBlobs.cshtml` et remplacez le contenu par le code suivant :

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **List blobs** (Créer une liste d’objets blob) pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Capture d’écran Lister les objets blob](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Télécharger des objets blob

Cette section montre comment télécharger un objet blob. Vous pouvez le conserver dans le stockage local ou lire le contenu dans une chaîne. Les exemples de code font référence au conteneur *test-blob-container* créé dans la section [Création d’un conteneur d’objets blob](#create-a-blob-container).

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée `DownloadBlob` qui renvoie une chaîne.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Dans la méthode `DownloadBlob`, récupérez un objet `CloudBlobContainer` représentant une référence au conteneur d’objets blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Récupérez un objet de référence d’objet blob en appelant la méthode `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Pour télécharger un objet blob, utilisez la méthode `CloudBlockBlob.DownloadToStream`. Le code suivant transfère le contenu d’un objet blob dans un objet de flux. Cet objet est ensuite conservé dans un fichier local. (Remplacez *&lt;local-file-name>* par le nom de fichier complet représentant l’emplacement à utiliser pour le téléchargement de l’objet blob.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Le code suivant montre la méthode `ListBlobs` achevée (avec un chemin d’accès complet au fichier en cours de création) :
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Download blob** (Télécharger l’objet blob) pour télécharger l’objet blob. L’objet blob spécifié dans l’appel de méthode `CloudBlobContainer.GetBlockBlobReference` effectue le téléchargement vers l’emplacement spécifié dans l’appel de méthode `File.OpenWrite`.  Le texte *success!* doit s’afficher dans le navigateur. 

## <a name="delete-blobs"></a>Suppression d’objets blob

Les étapes suivantes montrent comment supprimer un objet blob :

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée `DeleteBlob` qui renvoie une chaîne.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Dans la méthode `DeleteBlob`, récupérez un objet `CloudBlobContainer` représentant une référence au conteneur d’objets blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Récupérez un objet de référence d’objet blob en appelant la méthode `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Pour supprimer un objet blob, utilisez la méthode `Delete`.

    ```csharp
    blob.Delete();
    ```
    
    La méthode `DeleteBlob` achevée doit ressembler à ce qui suit :
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Supprimer l’objet blob** pour supprimer l’objet blob spécifié dans l’appel de méthode `CloudBlobContainer.GetBlockBlobReference`. Le texte *success!* doit s’afficher dans le navigateur. Sélectionnez le bouton **Précédent** du navigateur, puis **Lister les objets blob** pour vérifier que l’objet blob ne se trouve plus dans le conteneur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à stocker, répertorier et récupérer des objets blob dans le stockage Azure à l’aide d’ASP.NET. Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.

  * [Prise en main du stockage de tables Azure et des services connectés de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Prise en main du stockage de files d’attente Azure et des services connectés de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
