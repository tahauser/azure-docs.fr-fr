---
title: "Bien démarrer avec le Stockage Blob Azure et les services connectés de Visual Studio (ASP.NET Core) | Microsoft Docs"
description: "Comment bien démarrer avec le Stockage Blob Azure dans un projet ASP.NET Core sur Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Prendre en main Stockage Blob Azure et les services connectés de Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Le Stockage Blob Azure est un service qui stocke des données non structurées dans le cloud sous forme d’objets ou d’objets blob. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Stockage Blob.

Ce didacticiel montre comment écrire du code ASP.NET Core pour des scénarios courants qui utilisent le Stockage Blob. Ces scénarios incluent la création d’un conteneur d’objets blob, ainsi que le chargement, la création d’une liste, le téléchargement et la suppression d’objets blob.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Conditions préalables

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Configuration de l’environnement de développement

Cette section explique comment configurer l’environnement de développement : création d’une application modèle-vue-contrôleur (MVC) ASP.NET, ajout d’une connexion Services connectés, ajout d’un contrôleur et spécification des directives requises portant sur les espaces de noms.

### <a name="create-an-aspnet-mvc-app-project"></a>Création d’un projet d’application MVC ASP.NET

1. Ouvrez Visual Studio.

1. Dans le menu principal, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Web** > **Application ASP.NET Core Web** > **AspNetCoreStorage**. Sélectionnez ensuite **OK**.

    ![Capture d’écran de la boîte de dialogue Nouveau projet de Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Dans la boîte de dialogue **Nouvelle application web ASP.NET Core**, sélectionnez **.NET Core** > **ASP.NET Core 2.0** > **Application web (modèle-vue-contrôleur)**. Sélectionnez ensuite **OK**.

    ![Capture d’écran de la boîte de dialogue Nouvelle application web ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utiliser les services connectés pour se connecter à un compte de stockage Azure

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.

2. Dans le menu contextuel, sélectionnez **Ajouter** > **Service connecté**.

1. Dans la boîte de dialogue **Services connectés**, sélectionnez **Stockage cloud avec le Stockage Azure**, puis **Configurer**.

    ![Capture d’écran de la boîte de dialogue Services connectés](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Dans la boîte de dialogue **Stockage Azure**, sélectionnez le compte de stockage Azure à utiliser dans le cadre de ce didacticiel. Pour créer un compte de stockage Azure, sélectionnez **Créer un compte de stockage** et remplissez le formulaire. Après avoir sélectionné un compte de stockage existant ou en avoir créé un, sélectionnez **Ajouter**. Visual Studio installe le package NuGet pour le Stockage Azure et une chaîne de connexion de stockage pour **appsettings.json**.

> [!TIP]
> Pour apprendre à créer un compte de stockage avec le [portail Azure](https://portal.azure.com), consultez [Créez un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Il est également possible de créer un compte de stockage Azure avec [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md) ou [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Créer un contrôleur MVC 

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs**.

2. Dans le menu contextuel, sélectionnez **Ajouter** > **Contrôleur**.

    ![Capture d’écran de l’Explorateur de solutions](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Dans la boîte de dialogue **Ajouter une structure**, sélectionnez **Contrôleur MVC - vide**, puis **Ajouter**.

    ![Capture d’écran de la boîte de dialogue Ajouter une structure](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Dans la boîte de dialogue **Ajouter un contrôleur MVC vide**, nommez le contrôleur *BlobsController*, puis sélectionnez **Ajouter**.

    ![Capture d’écran de la boîte de dialogue Ajouter un contrôleur MVC vide](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Ajoutez les directives `using` suivantes au fichier `BlobsController.cs` :

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Se connecter à un compte de stockage et récupérer une référence de conteneur

Un conteneur d’objets blob est une hiérarchie imbriquée d’objets blobs et de dossiers. Les autres étapes de ce document requièrent une référence à un conteneur d’objets blob ; dans un souci de réutilisabilité, ce code doit donc être placé dans sa propre méthode.

Les étapes suivantes créent une méthode permettant de se connecter au compte de stockage à l’aide de la chaîne de connexion présente dans **appsettings.json**. Elles créent également une référence à un conteneur. Le nom du paramètre de la chaîne de connexion présente dans **appsettings.json** est au format `<storageaccountname>_AzureStorageConnectionString`. 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **GetCloudBlobContainer** qui renvoie un **CloudBlobContainer**. Veillez à remplacer `<storageaccountname>_AzureStorageConnectionString` par le nom réel de la clé dans **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Même si *test-blob-container* n’existe pas encore, ce code crée une référence à ce conteneur. Ainsi, ce dernier pourra être créé avec la méthode `CreateIfNotExists` indiquée à l’étape suivante.

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
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Attribuez à `ViewBag` le nom du conteneur d’objets blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Le code suivant illustre la méthode `CreateBlobContainer` achevée :

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Affichages**.

2. Dans le menu contextuel, sélectionnez **Ajouter** > **Nouveau dossier**. Nommez ce nouveau dossier *Objets blob*. 

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** et cliquez avec le bouton droit sur **Objets blob**.

4. Dans le menu contextuel, sélectionnez **Ajouter** > **Affichage**.

1. Dans la boîte de dialogue **Ajouter un affichage**, entrez **CreateBlobContainer** comme nom de l’affichage, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `CreateBlobContainer.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Recherchez la liste non triée qui ressemble à ceci : `<ul class="nav navbar-nav">`.  Après le dernier élément `<li>` de la liste, ajoutez le code HTML suivant pour ajouter un autre élément de menu de navigation :

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Exécutez l’application, puis sélectionnez **Créer un conteneur d’objets blob** pour afficher des résultats qui devraient ressembler à la capture d’écran suivante :
  
    ![Capture d’écran Créer un conteneur d’objets blob](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Comme mentionné précédemment, la méthode `CloudBlobContainer.CreateIfNotExists` renvoie **true** uniquement lorsque le conteneur n’existe pas et est créé. Par conséquent, si l’application s’exécute alors que le conteneur existe, la méthode renvoie la valeur **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Charger un objet blob dans un conteneur d’objets blob

Une fois le [conteneur d’objets blob créé](#create-a-blob-container), chargez-y des fichiers. Cette section présente la procédure à suivre pour charger un fichier local dans un conteneur d’objets blob. Dans ces étapes, partez du principe qu’il existe un conteneur d’objets blob nommé *test-blob-container*. 

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

1. Le Stockage Azure prend en charge différents types d’objets blob. Ce didacticiel utilise des objets blob de blocs. Pour récupérer une référence à un objet blob de blocs, appelez la méthode `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Le nom de l’objet blob fait partie de l’URL utilisée pour récupérer un objet blob et peut être n’importe quelle chaîne, y compris le nom du fichier.

1. Dès qu’une référence d’objet blob est créée, il est possible d’y charger n’importe quel flux de données en appelant la méthode `UploadFromStream` de son objet. La méthode `UploadFromStream` crée l’objet blob s’il n’existe pas ou le remplace s’il est déjà présent. (Remplacez *&lt;file-to-upload>* par un chemin d’accès complet au fichier à charger.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
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
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément `<li>` de la liste, ajoutez le code HTML suivant pour ajouter un autre élément de menu de navigation :

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Exécutez l’application, puis sélectionnez **Upload blob** (Charger l’objet blob). Le mot *success!* (réussite) devrait apparaître.
    
    ![Capture d’écran de la vérification de réussite](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. Pour répertorier les objets blob dans un conteneur d’objets blob, utilisez la méthode `CloudBlobContainer.ListBlobsSegmentedAsync`. La méthode `CloudBlobContainer.ListBlobsSegmentedAsync` retourne un `BlobResultSegment`. Celui-ci contient des objets `IListBlobItem` pouvant être castés en objets `CloudBlockBlob`, `CloudPageBlob` ou `CloudBlobDirectory`. L’extrait de code suivant énumère tous les objets blob dans un conteneur d’objets blob. Chaque objet blob est casté en un objet correspondant à son type. Son nom (ou son URI dans le cas d’un `CloudBlobDirectory`) est ajouté à une liste.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    Le code suivant illustre la méthode `ListBlobs` achevée :

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. Dans la boîte de dialogue **Ajouter un affichage**, entrez `ListBlobs` comme nom de l’affichage, puis sélectionnez **Ajouter**.

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

1. Après le dernier élément `<li>` de la liste, ajoutez le code HTML suivant pour ajouter un autre élément de menu de navigation :

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Exécutez l’application, puis sélectionnez **Lister les objets blob** pour afficher des résultats qui devraient ressembler à la capture d’écran suivante :
  
    ![Capture d’écran Lister les objets blob](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

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
        blob.DownloadToStreamAsync(fileStream).Wait();
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
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément `<li>` de la liste, ajoutez le code HTML suivant pour ajouter un autre élément de menu de navigation :

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Exécutez l’application, puis sélectionnez **Download blob** (Télécharger l’objet blob) pour télécharger l’objet blob. L’objet blob spécifié dans l’appel de méthode `CloudBlobContainer.GetBlockBlobReference` effectue le téléchargement vers l’emplacement spécifié dans l’appel de méthode `File.OpenWrite`. Le texte *success!* (réussite) doit s’afficher dans le navigateur. 

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
    blob.DeleteAsync().Wait();
    ```
    
    La méthode `DeleteBlob` achevée doit ressembler à ce qui suit :
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. Dans **l’Explorateur de solutions**, développez le dossier **Affichages** > **Partagé** et ouvrez `_Layout.cshtml`.

1. Après le dernier élément `<li>` de la liste, ajoutez le code HTML suivant pour ajouter un autre élément de menu de navigation :

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Exécutez l’application, puis sélectionnez **Supprimer l’objet blob** pour supprimer l’objet blob spécifié dans l’appel de méthode `CloudBlobContainer.GetBlockBlobReference`. Le texte *success!* (réussite) doit s’afficher dans le navigateur. Sélectionnez le bouton **Précédent** du navigateur, puis **Lister les objets blob** pour vérifier que l’objet blob ne se trouve plus dans le conteneur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à stocker, à lister et à récupérer des objets blob dans le Stockage Azure à l’aide d’ASP.NET Core. Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.

  * [Bien démarrer avec le Stockage Table Azure et les services connectés de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Bien démarrer avec le Stockage File d’attente Azure et les services connectés de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
