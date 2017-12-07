---
title: "Créer un déclencheur HTTP avec une liaison d’entrée Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment utiliser Azure Functions avec des déclencheurs HTTP pour interroger Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 3fca64db9e19f8295fc462b790beb95f6796ae4c
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Créer un déclencheur HTTP Azure Functions avec une liaison d’entrée Azure Cosmos DB

Azure Cosmos DB est un service de base de données multimodèle mondialement distribué sans schéma ni serveur. Une Azure Function est un service de calcul sans serveur qui vous permet d’exécuter du code à la demande. Associez ces deux services Azure pour disposer d’une base pour une architecture sans serveur qui vous permet de vous concentrer sur la création de superbes applications sans vous soucier de l’approvisionnement et de la maintenance des serveurs pour répondre à vos besoins de calcul et de base de données.

Ce didacticiel s’appuie sur le code créé dans [API Graph - Démarrage rapide pour .NET](create-graph-dotnet.md). Ce didacticiel ajoute une Azure Function qui contient un [déclencheur HTTP](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-bindings-http-webhook.md#http-trigger). Le déclencheur HTTP utilise une [liaison d’entrée](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-triggers-bindings.md) Azure Cosmos DB pour récupérer des données dans la base de données graphique créée dans le démarrage rapide. Ce déclencheur HTTP particulier recherche des données dans Azure Cosmos DB, mais les liaisons d’entrée d’Azure Cosmos DB peuvent être utilisées pour récupérer des valeurs d’entrée de données dont votre fonction a besoin.

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un projet Azure Function 
> * Créer un déclencheur HTTP
> * Publier Azure Function
> * Connecter Azure Function à la base de données Azure Cosmos DB

## <a name="prerequisites"></a>Composants requis

- [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/), y compris la charge de travail de **développement Azure**.

    ![Installer Visual Studio 2017 avec la charge de travail de développement Azure](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- Après avoir installé ou mis à niveau vers Visual Studio 2017 version 15.3, vous devez mettre à jour manuellement les outils Visual Studio 2017 pour Azure Functions. Vous pouvez mettre à jour les outils à partir du menu **Outils** sous **Extensions et mises à jour...**  > **Mises à jour** > **Visual Studio Marketplace** > **Azure Functions et Web Jobs Tools** > **Mettre à jour**.

- Suivez le didacticiel [Créer une application .NET à l’aide de l’API Graph](tutorial-develop-graph-dotnet.md) ou obtenez l’exemple de code dans le référentiel GitHub [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) et créez le projet.
 
## <a name="build-a-function-in-visual-studio"></a>Créer une fonction dans Visual Studio

1. Ajoutez un projet **Azure Functions** à votre solution en cliquant avec le bouton droit sur le nœud de la solution dans l’**Explorateur de solutions**, puis sélectionnez **Ajouter** > **Nouveau projet**. Sélectionnez **Azure Functions** dans la boîte de dialogue et nommez-le **PeopleDataFunctions**.

   ![Ajouter un projet Azure Functions à la solution](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. Une fois le projet Azure Functions créé, il ne reste plus qu’à effectuer quelques mises à jour et installations liées à NuGet. 

    a. Pour vous assurer que vous disposez du dernier Kit de développement logiciel (SDK) Functions, utilisez le NuGet Manager pour mettre à jour le package **Microsoft.NET.Sdk.Functions**. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**. Dans l’onglet **Installé**, sélectionnez Microsoft.NET.Sdk.Functions, puis cliquez sur **Mettre à jour**.

   ![Mettre à jour les packages NuGet](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. Dans l’onglet **Parcourir**, entrez **azure.graphs** pour rechercher le package **Microsoft.Azure.Graphs**, puis cliquez sur **Installer**. Ce package contient le Kit de développement logiciel (SDK) client API Graph .NET.

   ![Installer l’API Graph](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. Dans l’onglet **Parcourir**, entrez **mono.csharp** pour rechercher le package **Mono.CSharp**, puis cliquez sur **Installer**.

   ![Installer Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. Votre Explorateur de solutions doivent maintenant inclure les packages que vous avez installés, comme illustré ici. 
   
   Nous devons ensuite écrire du code. Nous allons donc ajouter un nouvel élément **Azure Function** au projet. 

    a. Cliquez avec le droit de la souris sur le nœud de projet dans **l’Explorateur de solutions**, puis choisissez **Ajouter** > **Nouvel élément**.   
    b. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Éléments Visual C#**, sélectionnez **Azure Function**, tapez **Search** comme nom de votre projet, puis cliquez sur **Ajouter**.  
 
   ![Crée une nouvelle fonction appelée Search](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Azure Function répondra aux requêtes HTTP, le modèle de déclencheur HTTP est donc approprié ici.
   
   Dans la zone **Nouvelle Azure Function**, sélectionnez **Déclencheur HTTP**. Nous souhaitons que cette Azure Function soit « grande ouverte », nous définissons donc les **Droits d’accès** sur **Anonyme**, ce qui autorise tout le monde. Cliquez sur **OK**.

   ![Définir les droits d’accès sur anonyme](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. Après avoir ajouté Search.cs au projet Azure Function, copiez ces instructions d’**utilisation** dans les instructions d’utilisation existantes :

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. Remplacez ensuite le code de classe d’Azure Function par le code ci-dessous. Le code recherche dans la base de données Azure Cosmos DB à l’aide de l’API Graph pour toutes les personnes ou pour la personne spécifique identifiée par le paramètre de chaîne de requête `name`.

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   Le code correspond essentiellement à la même logique de connexion que dans l’application de console d’origine ayant formé la base de données, avec une requête simple pour récupérer les enregistrements correspondants.

## <a name="debug-the-azure-function-locally"></a>Déboguer Azure Function localement

Maintenant que le code est terminé, vous pouvez utiliser les outils de débogage locaux d’Azure Function et l’émulateur pour exécuter le code localement afin de le tester.

1. Pour que le code s’exécute correctement, vous devez le configurer pour une exécution locale avec vos informations de connexion Azure Cosmos DB. Vous pouvez utiliser le fichier local.settings.json pour configurer Azure Function pour une exécution locale comme vous utiliseriez le fichier App.config pour configurer l’application de console d’origine pour l’exécution.

    Pour cela, ajoutez les lignes de code suivantes à local.settings.json, puis copiez Endpoint et AuthKey du fichier App.Config dans le projet GraphGetStarted, comme illustré dans l’image suivante.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![Définir le point de terminaison et la clé d’autorisation dans le fichier local.settings.json](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Remplacez le projet StartUp par la nouvelle application Functions. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **PeopleDataFunctions**, puis sélectionnez **Définir comme projet de démarrage**.

3. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Dépendances** dans le projet **PeopleDataFunctions**, puis cliquez sur **Ajouter une référence**. Dans la liste, sélectionnez System.Configuration, puis cliquez sur **OK**.

3. Exécutons maintenant l’application. Appuyez sur F5 pour lancer l’outil de débogage local, func.exe, avec le code d’Azure Function hébergé et prêt à l’emploi.

   À la fin de la sortie initiale de func.exe, nous constatons qu’Azure Function est hébergé sur localhost:7071. Ceci est utile pour le tester dans un client.

   ![Tester le client](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. Pour tester Azure Function, utilisez [Visual Studio Code](http://code.visualstudio.com/) avec l’extension de Huachao Mao, [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). REST Client offre des capacités de requête HTTP locale ou distante d’un simple clic droit. 

    Pour cela, créez un nouveau fichier nommé test-function-locally.http et ajoutez le code suivant :

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Cliquez maintenant avec le bouton droit sur la première ligne de code, puis sélectionnez **Envoyer la requête** comme illustré dans l’image suivante.

   ![Envoyer une requête REST à partir de Visual Studio Code](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   La réponse HTTP brute vous est présentée à partir des en-têtes Azure Function exécutés localement, du contenu de corps JSON, de tous les éléments.

   ![Response REST](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Sélectionnez maintenant la deuxième ligne de code, puis sélectionnez **Envoyer la requête**. En ajoutant le paramètre de chaîne de requête `name` avec une valeur connue dans la base de données, nous pouvons filtrer les résultats renvoyés par Azure Function.

   ![Filtrer les résultats de Azure Function](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

Une fois que Azure Function est validée et semble fonctionner correctement, la dernière étape consiste à la publier sur Azure App Service et à la configurer pour s’exécuter dans le cloud.

## <a name="publish-the-azure-function"></a>Publier Azure Function

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

   ![Publier le nouveau projet](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Nous sommes prêts à le publier dans le cloud afin de le tester dans un scénario publiquement disponible. Dans l’onglet **Publier**, sélectionnez **Azure Function App**, sélectionnez **Créer nouveau** pour créer une Azure Function dans votre abonnement Azure, puis cliquez sur **Publier**.

   ![Créer une nouvelle application Azure Function](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. Dans la boîte de dialogue **Publier**, procédez comme suit :
   
    a. Dans **Nom de l’application**, donnez un nom unique à la fonction.

    b. Dans **Abonnement**, sélectionnez l’abonnement Azure à utiliser.
   
    c. Dans **Groupe de ressources**, créez un nouveau groupe de ressources et utiliser le même nom que pour l’application.
   
    d. Pour **Plan App Service**, cliquez sur **Nouveau** pour créer un nouveau plan App Service basé sur la consommation, car nous souhaitons utiliser la méthode de facturation de paiement à l’utilisation pour Azure Function sans serveur. Utilisez les valeurs par défaut sur la page **Configurer un plan App Service**, puis cliquez sur **OK**.
   
    e. Pour **Compte de stockage**, cliquez également sur **Nouveau** pour créer un compte de stockage à utiliser avec Azure Function au cas où nous devrions prendre en charge des objets blob, tables ou files d’attente pour déclencher l’exécution d’autres fonctionnalités. Utilisez les valeurs par défaut sur la page **Compte de stockage**, puis cliquez sur **OK**.

    f. Cliquez ensuite sur le bouton **Créer** de la boîte de dialogue pour créer toutes les ressources dans votre abonnement Azure. Visual Studio télécharge un profil de publication (un fichier XML simple) qu’il utilisera la prochaine fois que vous publierez votre code d’Azure Function.

   ![Créer le compte de stockage](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    Visual Studio affiche ensuite une page de publication que vous pouvez utiliser si vous apportez des modifications à la fonction et que vous devez la republier. Vous n’avez rien à faire dans cette page pour le moment.

4. Une fois que Azure Function est publiée, vous pouvez accéder à la page du [portail Azure](https://portal.azure.com/) de votre Azure Function. Ici, vous pouvez voir un lien vers les **paramètres d’application** de Azure Function. Ouvrez ce lien pour configurer Azure Function dynamique pour la connectivité à la base de données Azure Cosmos DB avec vos données personnelles.

   ![Passer en revue les paramètres d’application](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Comme vous l’avez fait précédemment dans le fichier App.config de l’application de console et dans le fichier local.settings.json de l’application Azure Function, vous devez ajouter Endpoint et AuthKey à la base de données Azure Cosmos DB pour la fonction publiée. Ainsi, vous ne devez jamais enregistrer le code de configuration contenant vos clés, vous pouvez les configurer dans le portail et vérifiez qu’ils ne sont pas stockés dans le contrôle de code source. Pour ajouter chaque valeur, cliquez sur le bouton **Ajouter un paramètre**, ajoutez **Endpoint** et votre valeur du fichier app.config, puis cliquez de nouveau sur le bouton **Ajouter un paramètre** et ajoutez **AuthKey** et votre valeur personnalisée. Une fois que vous avez ajouté et enregistré les valeurs, vos paramètres doivent ressembler à ceci.

   ![Configurer Endpoint et AuthKey](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. Une fois que Azure Function est configurée correctement dans votre abonnement Azure, vous pouvez utiliser à nouveau l’extension Visual Studio Code REST Client pour rechercher l’URL Azure Function disponible publiquement. Ajoutez ces deux lignes de code à test-function-locally.http, puis exécutez chaque ligne pour tester cette fonction. Remplacez le nom de la fonction dans l’URL par le nom de votre fonction.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    La fonction renvoie les données récupérées dans Azure Cosmos DB.

    ![Utiliser le client REST pour rechercher Azure Function](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Créé un projet Azure Function 
> * Créé un déclencheur HTTP
> * Publié Azure Function
> * Connecté la fonction à la base de données Azure Cosmos DB

Vous pouvez maintenant passer à la section Concepts pour plus d’informations sur Cosmos DB.

> [!div class="nextstepaction"]
> [Diffusion mondiale](distribute-data-globally.md) 

Cet article est basé sur un blog de la série de blogs [Brady Gaster’s Schemaless & Serverless](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless). Consultez son blog pour des publications supplémentaires de la série.
