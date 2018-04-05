---
title: Prise en main du Stockage File d’attente Azure et des services connectés de Visual Studio (ASP.NET) | Microsoft Docs
description: Comment prendre en main le Stockage File d’attente Azure dans un projet ASP.NET dans Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 89baa741dcd916b70514f9703255b9e6d01c689b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main du Stockage File d’attente Azure et des services connectés de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d'ensemble

Le stockage de files d’attente Azure fournit une messagerie cloud entre les composants d’application. Lors de la conception d'applications pour la mise à l'échelle, des composants d'application sont souvent découplés, de sorte qu'ils peuvent être mis à l'échelle indépendamment. Le stockage de files d’attente offre une messagerie asynchrone pour la communication entre les composants d’application, qu’ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le stockage de files d’attente prend également en charge la gestion des tâches asynchrones et la création des flux de travail de processus.

Ce didacticiel montre comment écrire du code ASP.NET pour des scénarios courants en utilisant des entités de stockage de file d’attente Azure. Ces scénarios incluent des tâches courantes telles que la création d’une file d’attente Azure, l'ajout, la modification, la lecture et la suppression de messages de file d’attente.

##<a name="prerequisites"></a>Prérequis


* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Compte Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Créer un contrôleur MVC 

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs**, puis sélectionnez **Ajouter -> Contrôleur** dans le menu contextuel.

    ![Ajouter un contrôleur à une application ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Dans la boîte de dialogue **Ajouter la structure**, cliquez sur **Contrôleur MVC 5 - vide**, puis sélectionnez **Ajouter**.

    ![Spécifier le type de contrôleur MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Dans la boîte de dialogue **Ajouter un contrôleur**, nommez le contrôleur *QueuesController*, puis sélectionnez **Ajouter**.

    ![Nommer le contrôleur MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Ajoutez les directives *using* suivantes au fichier `QueuesController.cs` :

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Créer une file d’attente

Les étapes suivantes montrent comment créer une file d'attente :

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `QueuesController.cs` . 

1. Ajoutez une méthode appelée **CreateQueue** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **CreateQueue**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenez un objet **CloudQueue** représentant une référence au nom de la file d’attente souhaitée. La méthode **CloudQueueClient.GetQueueReference** n’effectue pas de demande auprès du stockage de files d'attente. La référence est renvoyée, que la file d'attente existe ou non. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Appelez la méthode **CloudQueue.CreateIfNotExists** pour créer la file d’attente, le cas échéant. La méthode **CloudQueue.CreateIfNotExists** renvoie **true** si la file d’attente n’existe pas et a été correctement créée. Sinon, la valeur **false** est retournée.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Mettez à jour l’objet **ViewBag** avec le nom de la file d'attente.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Files d'attente**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **CreateQueue** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `CreateQueue.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Créer une file d'attente** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Créer la file d’attente](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Comme mentionné précédemment, la méthode **CloudQueue.CreateIfNotExists** renvoie **true** uniquement lorsque la file d'attente n’existe pas et est créée. Par conséquent, si vous exécutez l’application alors que la file d'attente existe, la méthode renverra **false**. Pour exécuter l’application plusieurs fois, vous devez supprimer la file d'attente avant d’exécuter à nouveau l’application. La suppression de la file d’attente peut être effectuée via la méthode **CloudQueue.Delete**. Vous pouvez également le faire à partir du [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou de l’[Explorateur de stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Ajout d'un message à une file d'attente

Une fois que vous avez [créé une file d’attente](#create-a-queue), vous pouvez y ajouter des messages. Cette section vous guide dans le processus d'ajout d’un message à une file d’attente *test-queue*. 

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `QueuesController.cs` .

1. Ajoutez une méthode appelée **AddMessage** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **AddMessage**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenez un objet **CloudQueueContainer** représentant une référence à la file d’attente. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Créez l’objet **CloudQueueMessage** représentant le message que vous souhaitez ajouter à la file d’attente. Un objet **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Appelez la méthode **CloudQueue.AddMessage** pour ajouter le message à la file d’attente.

    ```csharp
    queue.AddMessage(message);
    ```

1. Créez et définissez quelques propriétés**ViewBag** à afficher dans la vue.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Files d'attente**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **AddMessage** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `AddMessage.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Ajouter un message** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Ajouter un message](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Les deux sections - [Lire un message depuis une file d’attente, sans le supprimer](#read-a-message-from-a-queue-without-removing-it) et [Lire et supprimer un message dans une file d’attente](#read-and-remove-a-message-from-a-queue) montrent comment lire les messages d’une file d’attente.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Lire un message depuis une file d’attente, sans le supprimer

Cette section illustre comment lire rapidement un message mis en file d’attente (c’est-à-dire lire le premier message sans le supprimer).  

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `QueuesController.cs` .

1. Ajoutez une méthode appelée **PeekMessage** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **PeekMessage**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenez un objet **CloudQueueContainer** représentant une référence à la file d’attente. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Appelez la méthode **CloudQueue.PeekMessage** pour lire le premier message de la file d’attente, sans le supprimer. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Mettez à jour l'objet **ViewBag** avec deux valeurs : le nom de la file d’attente et le message qui été lu. L'objet **CloudQueueMessage** expose deux propriétés permettant d'obtenir la valeur de l'objet : **CloudQueueMessage.AsBytes** et **CloudQueueMessage.AsString**. **AsString** (utilisé dans cet exemple) retourne une chaîne, tandis que **AsBytes** retourne un tableau d’octets.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Files d'attente**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **PeekMessage** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `PeekMessage.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Lire furtivement un message** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Lire furtivement un message](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Lire et supprimer un message dans une file d’attente

Dans cette section, vous allez apprendre à lire et à supprimer un message d’une file d’attente.   

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `QueuesController.cs` .

1. Ajoutez une méthode appelée **ReadMessage** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **ReadMessage**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenez un objet **CloudQueueContainer** représentant une référence à la file d’attente. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Appelez la méthode **CloudQueue.GetMessage** pour lire le premier message de la file d’attente. La méthode **CloudQueue.GetMessage** rend le message invisible pendant 30 secondes (par défaut) pour tout autre code lisant les messages, afin qu’aucun autre code ne puisse modifier ou supprimer ce message pendant le traitement. Pour modifier la durée d’invisibilité d’un message, changez la valeur du paramètre **visibilityTimeout** passé à la méthode **CloudQueue.GetMessage**.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Appelez la méthode **CloudQueueMessage.Delete** pour supprimer le message dans la file d’attente.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Mettez à jour l’objet **ViewBag** avec le message supprimé et le nom de la file d'attente.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Files d'attente**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **ReadMessage** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `ReadMessage.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Lire et supprimer un message** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Lire et supprimer un message](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Obtention de la longueur de la file d'attente

Cette section montre comment obtenir la longueur de la file d’attente (nombre de messages). 

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `QueuesController.cs` .

1. Ajoutez une méthode appelée **GetQueueLength** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **ReadMessage**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenez un objet **CloudQueueContainer** représentant une référence à la file d’attente. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Appelez la méthode **CloudQueue.FetchAttributes** pour récupérer les attributs de la file d’attente (y compris sa longueur). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Accédez à la propriété **CloudQueue.ApproximateMessageCount** pour obtenir la longueur de la file d’attente.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Mettez à jour l’objet **ViewBag** avec le nom de la file d'attente et sa longueur.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Files d'attente**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **GetQueueLength** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `GetQueueLengthMessage.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Obtention de la longueur de la file d'attente** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Obtention de la longueur de la file d'attente](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Suppression d'une file d'attente
Cette section montre comment supprimer une file d’attente. 

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `QueuesController.cs` .

1. Ajoutez une méthode appelée **DeleteQueue** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **DeleteQueue**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenez un objet **CloudQueueClient** représentant un client du service de File d’attente.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenez un objet **CloudQueueContainer** représentant une référence à la file d’attente. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Appelez la méthode **CloudQueue.Delete** pour supprimer la file d’attente représentée par l’objet **CloudQueue**.

    ```csharp
    queue.Delete();
    ```

1. Mettez à jour l’objet **ViewBag** avec le nom de la file d'attente et sa longueur.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Files d'attente**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **DeleteQueue** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `DeleteQueue.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Obtention de la longueur de la file d'attente** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Supprimer une file d'attente](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.

  * [Prise en main du stockage d’objets blob Azure et des services connectés de Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Prise en main du stockage de tables Azure et des services connectés de Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
