---
title: Prise en main du Stockage Table Azure et des services connectés de Visual Studio (ASP.NET) | Microsoft Docs
description: Comment prendre en main le Stockage Table Azure dans un projet ASP.NET dans Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 763195be2d2000a6935bd5735843d9781a11e596
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main du Stockage Table Azure et des services connectés de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d'ensemble

Le stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.

Ce didacticiel montre comment écrire du code ASP.NET pour des scénarios courants en utilisant des entités de stockage de table Azure. Ces scénarios incluent la création d’une table ainsi que l'ajout, l'interrogation et la suppression d’entités de table. 

## <a name="prerequisites"></a>Prérequis


* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Compte Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Créer un contrôleur MVC 

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs**, puis sélectionnez **Ajouter -> Contrôleur** dans le menu contextuel.

    ![Ajouter un contrôleur à une application ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Dans la boîte de dialogue **Ajouter la structure**, cliquez sur **Contrôleur MVC 5 - vide**, puis sélectionnez **Ajouter**.

    ![Spécifier le type de contrôleur MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Dans la boîte de dialogue **Ajouter un contrôleur**, nommez le contrôleur *TablesController*, puis sélectionnez **Ajouter**.

    ![Nommer le contrôleur MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Ajoutez les directives *using* suivantes au fichier `TablesController.cs` :

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Créer une classe de modèle

La plupart des exemples de cet article utilisent une classe dérivée de **TableEntity** appelée **CustomerEntity**. Les étapes suivantes vous guident tout au long de la déclaration de cette classe comme une classe de modèle :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Modèles**, puis sélectionnez **Ajouter -> Classe** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter un nouvel élément**, nommez la classe **CustomerEntity**.

1. Ouvrez le fichier `CustomerEntity.cs` et ajoutez l'instruction **using** suivante :

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modifiez la classe de sorte que, lorsque vous avez terminé, la classe est déclarée comme dans le code suivant. La classe déclare une entité nommée **CustomerEntity** qui utilise le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Création d’une table

Les étapes suivantes montrent comment créer une table :

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `TablesController.cs` .

1. Ajoutez une méthode appelée **CreateTable** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **CreateTable**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudTableClient** représentant un client du service de Table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet **CloudTable** représentant une référence au nom de la table souhaitée. La méthode **CloudTableClient.GetTableReference** n’effectue pas de demande auprès du stockage de table. La référence est renvoyée, que la table existe ou non. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Appelez la méthode **CloudTable.CreateIfNotExists** pour créer la table, le cas échéant. La méthode **CloudTable.CreateIfNotExists** renvoie **true** si la table n’existe pas et a été correctement créée. Sinon, la valeur **false** est retournée.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Mettez à jour l’objet **ViewBag** avec le nom de la table.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Tables**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **CreateTable** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `CreateTable.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Créer une table** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Créer une table](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Comme mentionné précédemment, la méthode **CloudTable.CreateIfNotExists** renvoie **true** uniquement lorsque la table n’existe pas et est créée. Par conséquent, si vous exécutez l’application alors que la table existe, la méthode renverra **false**. Pour exécuter l’application plusieurs fois, vous devez supprimer la table avant d’exécuter à nouveau l’application. La suppression de la table peut être effectuée via la méthode **CloudTable.Delete**. Vous pouvez également le faire à partir du [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou de l’[Explorateur de stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table

Les *entités* mappent vers les objets C\# en utilisant une classe personnalisée dérivée d’une **TableEntity**. Pour ajouter une entité à une table, créez une classe définissant les propriétés de votre entité. Cette section vous explique comment définir une classe d’entité utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d’une entité identifient l’entité de façon unique dans la table. Les requêtes d’entités dont les clés de partition sont identiques sont plus rapides que les entités dont les clés de partition sont différentes, mais le fait d’utiliser différentes clés de partition améliore l’extensibilité des opérations parallèles. Si une propriété doit être stockée dans le service de table, il doit s’agir d’une propriété publique d’un type pris en charge qui expose à la fois les valeurs de définition et de récupération.
La classe d’entité *doit* déclarer un constructeur public sans paramètre.

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment).

1. Ouvrez le fichier `TablesController.cs` .

1. Ajoutez la directive suivante afin que le code dans le `TablesController.cs` fichier puisse accéder à la classe **CustomerEntity** :

    ```csharp
    using StorageAspnet.Models;
    ```

1. Ajoutez une méthode appelée **AddEntity** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **AddEntity**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudTableClient** représentant un client du service de Table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet **CloudTable** qui représente une référence à la table à laquelle vous souhaitez ajouter la nouvelle entité. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciez et initialisez la classe **CustomerEntity**.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Créez un objet **TableOperation** qui insère l’entité du client.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Exécutez l’insertion en appelant la méthode **CloudTable.Execute**. Vous pouvez vérifier le résultat de cette opération en inspectant la propriété **TableResult.HttpStatusCode**. Un code d’état de 2xx indique que l’action demandée par le client a été correctement traitée. Par exemple, l’aboutissement de l’insertion de nouvelles entités entraîne l’affichage d’un code d’état HTTP de 204, qui indique que l’opération a été correctement traitée et que le serveur n’a renvoyé aucun contenu.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Mettez à jour l'objet **ViewBag** avec le nom de table et les résultats de l’opération d’insertion.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Tables**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **AddEntity** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `AddEntity.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Ajouter une entité** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Ajouter une entité](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Vous pouvez vérifier que l’entité a été ajoutée en suivant les étapes décrites dans la section [Obtention d'une seule entité](#get-a-single-entity). Vous pouvez également utiliser l'[Explorateur Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour afficher toutes les entités de vos tables.

## <a name="add-a-batch-of-entities-to-a-table"></a>Ajout d’un lot d’entités à une table

Vous pouvez [ajouter une entité à une table](#add-an-entity-to-a-table), ou en ajouter plusieurs au sein d’un lot. L'ajout d'entités au sein d'un lot réduit le nombre d’allers-retours entre votre code et le service de Table Azure. Les étapes suivantes expliquent comment ajouter plusieurs entités à une table via une seule opération d'insertion :

> [!NOTE]
> 
> Cette section suppose que vous avez effectué les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment).

1. Ouvrez le fichier `TablesController.cs` .

1. Ajoutez une méthode appelée **AddEntities** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **AddEntities**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudTableClient** représentant un client du service de Table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet **CloudTable** qui représente une référence à la table à laquelle vous souhaitez ajouter les nouvelles entités. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciez quelques objets client basés sur la classe de modèle **CustomerEntity** présentée dans la section [Ajout d'une entité à une table](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Obtenez un objet **TableBatchOperation**.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Ajoutez des entités à l’objet de l'opération d’insertion par lot.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Exécutez l’insertion par lot en appelant la méthode **CloudTable.ExecuteBatch**.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. La méthode **CloudTable.ExecuteBatch** retourne une liste d'objets **TableResult** où chaque objet **TableResult** peut être examiné pour déterminer la réussite ou l’échec de chaque opération individuelle. Pour cet exemple, passez la liste dans une vue afin que cette vue affiche les résultats de chaque opération. 
 
    ```csharp
    return View(results);
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Tables**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **AddEntities** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `AddEntities.cshtml` et modifiez-le pour qu’il se présente comme suit.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Ajouter des entités** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Ajouter des entités](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Vous pouvez vérifier que l’entité a été ajoutée en suivant les étapes décrites dans la section [Obtention d'une seule entité](#get-a-single-entity). Vous pouvez également utiliser l'[Explorateur Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour afficher toutes les entités de vos tables.

## <a name="get-a-single-entity"></a>Obtention d'une seule entité

Cette section explique comment obtenir une entité unique à partir d’une table à l’aide de la clé de ligne de l’entité et la clé de partition. 

> [!NOTE]
> 
> Cette section suppose que vous avez terminé les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment) et utilise les données de la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table). 

1. Ouvrez le fichier `TablesController.cs` .

1. Ajoutez une méthode appelée **GetSingle** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **GetSingle**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudTableClient** représentant un client du service de Table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet **CloudTable** qui représente une référence à la table à partir de laquelle vous extrayez l'entité.
 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Créez un objet d’opération de récupération qui prend un objet d’entité dérivé de **TableEntity**. Le premier paramètre est *partitionKey* et le deuxième, *rowKey*. À l’aide de la classe **CustomerEntity** et des données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table), l’extrait de code suivant interroge la table pour obtenir une entité **CustomerEntity** avec une valeur *partitionKey* égale à « Smith » et une valeur *rowKey* égale à « Ben » :

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Exécutez l’opération de récupération.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Passez le résultat dans la vue pour l’afficher.

    ```csharp
    return View(result);
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Tables**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **GetSingle** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `GetSingle.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Get Single** (Obtenir une entité unique) pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Obtenir une entité unique](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obtenir toutes les entités d’une partition

Comme indiqué dans la section [Ajout d'une entité à une table](#add-an-entity-to-a-table), la combinaison d’une partition et d'une clé de ligne identifie de manière unique une entité dans une table. Les requêtes d'entités dont les clés de partition sont identiques sont plus rapides que celles d'entités dont les clés de partition sont différentes. Cette section montre comment rechercher toutes les entités d'une table à partir d’une partition spécifiée.  

> [!NOTE]
> 
> Cette section suppose que vous avez terminé les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment) et utilise les données de la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table). 

1. Ouvrez le fichier `TablesController.cs` .

1. Ajoutez une méthode appelée **GetPartition** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **GetPartition**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudTableClient** représentant un client du service de Table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet **CloudTable** qui représente une référence à la table à partir de laquelle vous extrayez les entités.
 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanciez un objet **TableQuery** spécifiant la requête dans la clause **Where**. À l’aide de la classe **CustomerEntity** et des données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table), l’extrait de code suivant interroge la table pour obtenir toutes les entités pour lesquelles le paramètre **PartitionKey** (nom du client) est égal à « Smith » :

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dans une boucle, appelez la méthode **CloudTable.ExecuteQuerySegmented** passant l’objet de requête que vous avez instancié à l’étape précédente.  La méthode **CloudTable.ExecuteQuerySegmented** renvoie un objet **TableContinuationToken** qui indique qu’il n’existe plus d’entités à récupérer (lorsque la valeur est **null**). Dans la boucle, utilisez une autre boucle pour effectuer l’itération sur les entités renvoyées. Dans l’exemple de code suivant, chaque entité renvoyée est ajoutée à une liste. À la fin de la boucle, la liste est passée dans une vue pour être affichée : 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Tables**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **GetPartition** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `GetPartition.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Obtenir une partition** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Obtenir une partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Suppression d’une entité

Cette section montre comment supprimer une entité d'une table.

> [!NOTE]
> 
> Cette section suppose que vous avez terminé les étapes [Configuration de l’environnement de développement](#set-up-the-development-environment) et utilise les données de la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table). 

1. Ouvrez le fichier `TablesController.cs` .

1. Ajoutez une méthode appelée **DeleteEntity** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dans la méthode **DeleteEntity**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet **CloudTableClient** représentant un client du service de Table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet **CloudTable** qui représente une référence à la table à partir de laquelle vous supprimez l'entité.
 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Créez un objet d’opération de suppression qui prend un objet d’entité dérivé de **TableEntity**. Dans ce cas, nous utilisons la classe **CustomerEntity** et les données présentées dans la section [Ajout d’un lot d’entités à une table](#add-a-batch-of-entities-to-a-table). La valeur **ETag** de l'entité doit être valide.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Exécutez l'opération de suppression.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Passez le résultat dans la vue pour l’afficher.

    ```csharp
    return View(result);
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Tables**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **DeleteEntity** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `DeleteEntity.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Supprimer une entité** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Obtenir une entité unique](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.

  * [Prise en main du stockage d’objets blob Azure et des services connectés de Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Prise en main du stockage de files d’attente Azure et des services connectés de Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
