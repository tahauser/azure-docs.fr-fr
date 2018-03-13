---
title: Application hybride Azure WCF Relay locale/dans le cloud (.NET) | Microsoft Docs
description: "Découvrez comment créer une application hybride locale/de cloud .NET à l’aide d’Azure WCF Relay."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: fe13de5f83fe18a85f8d46d0eee039159e1a60e9
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>Application .NET locale/de cloud hybride avec Azure WCF Relay

Cet article montre comment créer une application cloud hybride avec Microsoft Azure et Visual Studio. Le didacticiel part du principe que vous n’avez pas d’expérience en tant qu’utilisateur d’Azure. En moins de 30 minutes, vous disposez d’une application qui utilise plusieurs ressources Azure s’exécutant dans le cloud.

Vous apprendrez à effectuer les opérations suivantes :

* créer ou adapter un service Web existant qui sera utilisé par une solution Web ;
* utiliser le service Azure WCF Relay pour partager des données entre une application Azure et un service web hébergé ailleurs.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Avantages de l’utilisation d’Azure Relay pour les solutions hybrides

Les solutions d'entreprise se composent généralement d'une combinaison de code personnalisé écrit pour répondre aux exigences nouvelles et uniques de l'entreprise ainsi qu'aux fonctionnalités existantes offertes par des solutions et des systèmes déjà en place.

Les architectes de solutions commencent à utiliser le cloud, car celui-ci permet de gérer plus facilement les exigences de mise à l'échelle tout en offrant des coûts opérationnels faibles. Ainsi, ils découvrent que les ressources des services existants qu'ils souhaiteraient exploiter comme des blocs de construction pour leurs solutions se situent à l'intérieur du pare-feu d'entreprise et hors de portée pour une solution cloud. Bon nombre de services internes ne sont pas créés ni hébergés pour être facilement exposés dans le périmètre du réseau d'entreprise.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) est conçu pour rendre accessibles les services web WCF (Windows Communication Foundation) existants, d’une manière sécurisée, aux solutions qui résident à l’extérieur du périmètre de l’entreprise, sans exiger de modifications intrusives de l’infrastructure de réseau d’entreprise. Ces services de relais sont toujours hébergés dans leur environnement existant, mais ils délèguent l’écoute des sessions et demandes entrantes au service de relais hébergé sur le cloud. Azure Relay protège également ces services de tout accès non autorisé à l’aide de l’authentification par [signature d’accès partagé](../service-bus-messaging/service-bus-sas.md) (SAP).

## <a name="solution-scenario"></a>Scénario de la solution
Dans ce didacticiel, vous allez créer un site web ASP.NET qui vous permet de voir une liste de produits sur la page d’inventaire des produits.

![][0]

Ce didacticiel part du principe que vous disposez des informations sur les produits dans un système local existant, et utilise Azure Relay pour atteindre ce système. La simulation met en scène un service Web exécuté dans une simple application console et appuyé par un ensemble de produits en mémoire. Vous allez pouvoir exécuter cette application console sur votre propre ordinateur et déployer le rôle Web dans Azure. Ainsi, vous verrez comment procède le rôle Web en cours d'exécution dans le centre de données Azure pour appeler votre ordinateur, même si ce dernier réside certainement derrière au moins un pare-feu et une couche de traduction d'adresses réseau (NAT, network address translation).

## <a name="set-up-the-development-environment"></a>Configuration de l’environnement de développement

Avant de commencer à développer votre application Azure, téléchargez les outils et configurez votre environnement de développement :

1. Installez le Kit de développement logiciel (SDK) Azure pour .NET depuis la page des [téléchargements SDK](https://azure.microsoft.com/downloads/).
2. Dans la colonne **.NET**, cliquez sur la version correspondant à votre version de [Visual Studio](http://www.visualstudio.com). Les étapes de ce didacticiel utilisent Visual Studio 2017.
3. Lorsque vous êtes invité à exécuter ou à enregistrer le programme d’installation, cliquez sur **Exécuter**.
4. Dans **Web Platform Installer**, cliquez sur **Installer**, puis poursuivez l’installation.
5. Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement de l’application. Le Kit de développement logiciel (SDK) comprend des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser les fonctionnalités de relais dans Azure, vous devez d’abord créer un espace de noms de service. Un espace de noms fournit un conteneur d’étendue pour l’adressage des ressources Azure au sein de votre application. Suivez les [instructions fournies ici](relay-create-namespace-portal.md) pour créer un espace de noms Relay.

## <a name="create-an-on-premises-server"></a>Création d’un serveur local

Vous créez d'abord un système local de catalogue de produits (fictif). Cela est assez simple : représentez-vous un système réel de catalogue de produits local avec une surface complète de services que nous essayons d'intégrer.

Ce projet est une application console Visual Studio et utilise le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pour inclure les bibliothèques et les paramètres de configuration Service Bus.

### <a name="create-the-project"></a>Création du projet

1. Avec les privilèges d’administrateur, démarrez Microsoft Visual Studio. Pour cela, cliquez avec le bouton droit sur l’icône de programme Visual Studio, puis cliquez sur **Exécuter en tant qu’administrateur**.
2. Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Modèles installés**, sous **Visual C#**, cliquez sur **Application console (.NET Framework)**. Dans la zone **Nom**, saisissez le nom **ProductsServer** :

   ![][11]
4. Cliquez sur **OK** pour créer le projet **ProductsServer**.
5. Si vous avez déjà installé le gestionnaire de package NuGet pour Visual Studio, passez à l'étape suivante. Sinon, accédez à [NuGet][NuGet], puis cliquez sur [Installer NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Suivez les invites pour installer le gestionnaire de package NuGet, puis redémarrez Visual Studio.
6. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet**ProductsServer**, puis cliquez sur **Gérer les packages NuGet**.
7. Cliquez sur l’onglet **Parcourir**, puis recherchez **WindowsAzure.ServiceBus**. Sélectionnez le package **WindowsAzure.ServiceBus**.
8. Cliquez sur **Installer**et acceptez les conditions d’utilisation.

   ![][13]

   Notez que les assemblys client nécessaires sont maintenant référencés.
8. Ajoutez une nouvelle classe pour votre contrat de produit. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsServer** et cliquez sur **Ajouter**, puis sur **Classe**.
9. Dans la zone **Nom**, saisissez le nom **ProductsContract.cs**. Cliquez ensuite sur **Add**.
10. Dans **ProductsContract.cs**, remplacez la définition d’espace de noms existante par le code suivant, qui définit le contrat du service.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. Dans Program.cs, remplacez la définition d’espace de noms existante par le code suivant, qui ajoute le service de profil et l’hôte correspondant :

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```
12. Dans l’Explorateur de solutions, double-cliquez sur le fichier **App.config** pour l’ouvrir dans l’éditeur de Visual Studio. En bas de l’élément `<system.ServiceModel>` (mais toujours au sein de `<system.ServiceModel>`), ajoutez le code XML suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms, et *yourKey*, par la clé SAS que vous avez récupérée précédemment sur le portail :

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
    L’erreur causée par « transportClientEndpointBehavior » n’est qu’un simple avertissement et ne provoque aucun problème de blocage pour cet exemple.
    
13. Toujours dans le fichier App.config, dans l’élément `<appSettings>`, remplacez la valeur de la chaîne de connexion par la chaîne de connexion obtenue précédemment à partir du portail.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Appuyez sur **Ctrl+Maj+B** ou, dans le menu **Générer**, cliquez sur **Générer la solution** pour générer l’application et vérifier que votre travail est correct.

## <a name="create-an-aspnet-application"></a>Création d’une application ASP.NET

Dans cette section, vous allez générer une application ASP.NET simple qui affiche des données récupérées de votre service de produit.

### <a name="create-the-project"></a>Création du projet

1. Vérifiez que Visual Studio fonctionne avec les privilèges d’administrateur.
2. Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Modèles installés**, sous **Visual C#**, cliquez sur **Application web ASP.NET (.NET Framework)**. Nommez le projet **ProductsPortal**. Cliquez ensuite sur **OK**.

   ![][15]

4. Dans la liste des **modèles ASP.NET** de la boîte de dialogue **Application web ASP.NET**, cliquez sur **MVC**.

   ![][16]

6. Cliquez sur le bouton **Modifier l’authentification**. Dans la boîte de dialogue **Modifier l’authentification**, vérifiez que l’option **Aucune authentification** est sélectionnée et cliquez sur **OK**. Pour ce didacticiel, vous déployez une application qui n’a pas besoin de connexion de l’utilisateur.

    ![][18]

7. De retour dans la boîte de dialogue **Nouvelle application web ASP.NET**, cliquez sur **OK** pour créer l’application MVC.
8. Vous devez maintenant configurer les ressources Azure d’une nouvelle application web. Suivez les étapes de [publication sur Azure décrites dans cet article](../app-service/app-service-web-get-started-dotnet-framework.md#publish-to-azure). Ensuite, revenez à ce didacticiel et passez à l’étape suivante.
10. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Modèles**, puis cliquez sur **Ajouter** et sur **Classe**. Dans la zone **Nom**, saisissez le nom **Product.cs**. Cliquez ensuite sur **Add**.

    ![][17]

### <a name="modify-the-web-application"></a>Modification de l’application Web

1. Dans le fichier Product.cs dans Visual Studio, remplacez la définition d’espace de noms existante par le code suivant.

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```
2. Dans l’Explorateur de solutions, développez le dossier **Controllers** et double-cliquez sur le fichier **HomeController.cs** pour l’ouvrir dans Visual Studio.
3. Dans le fichier **HomeController.cs**, remplacez la définition d’espace de noms par le code suivant.

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. Dans l’Explorateur de solutions, développez le dossier Views\Shared, puis double-cliquez sur le fichier **_Layout.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio.
5. Remplacez toutes les occurrences de **Mon application ASP.NET** par **Northwind Traders Products**.
6. Supprimez les liens **Home**, **About** et **Contact**. Dans l’exemple suivant, supprimez le code en surbrillance.

    ![][41]

7. Dans l’Explorateur de solutions, développez le dossier Views\Home, puis double-cliquez sur le fichier **Index.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio. Remplacez tout le contenu du fichier par le code suivant.

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```
8. Pour vérifier la qualité de votre travail, appuyez sur **Ctrl+Maj+B** pour générer le projet.

### <a name="run-the-app-locally"></a>Exécutez l’application localement.

Exécutez l'application afin de vérifier qu'elle fonctionne.

1. Assurez-vous que **ProductsPortal** est le projet actif. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Définir comme projet de démarrage**.
2. Dans Visual Studio, appuyez sur **F5**.
3. Votre application doit s’exécuter dans un navigateur :

   ![][21]

## <a name="put-the-pieces-together"></a>Assemblage des éléments

La prochaine étape consiste à raccorder le serveur de produits local et l’application ASP.NET.

1. S’il n’est pas déjà ouvert, rouvrez dans Visual Studio le projet **ProductsPortal** que vous avez créé dans la section [Création d’une application ASP.NET](#create-an-aspnet-application).
2. Comme vous l’avez fait dans la section « Création d’un serveur local », ajoutez le package NuGet aux références du projet. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsPortal**, puis cliquez sur **Gérer les packages NuGet**.
3. Recherchez **WindowsAzure.ServiceBus** et sélectionnez l’élément **WindowsAzure.ServiceBus**. Ensuite, terminez l’installation et fermez cette boîte de dialogue.
4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsPortal**, cliquez sur **Ajouter**, puis sur **Élément existant**.
5. Accédez au fichier **ProductsContract.cs** depuis le projet de console **ProductsServer**. Cliquez sur ProductsContract.cs afin de le mettre en surbrillance. Cliquez sur la flèche vers le bas en regard de l’option **Ajouter**, puis cliquez sur **Ajouter en tant que lien**.

   ![][24]

6. Ouvrez à présent le fichier **HomeController.cs** dans l’éditeur Visual Studio, puis remplacez la définition d’espace de noms existante par le code suivant. Assurez-vous de remplacer *yourServiceNamespace* par le nom de votre espace de noms de service et *yourKey*, par votre clé SAP. Le client peut alors appeler le service local, en renvoyant le résultat de l'appel.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution **ProductsPortal** (veillez à cliquer sur la solution et non sur le projet). Cliquez sur **Ajouter**, puis sur **Projet existant**.
8. Accédez au projet **ProductsServer**, puis double-cliquez sur le fichier solution **ProductsServer.csproj** pour l’ajouter.
9. **ProductsServer** doit être en cours d’exécution pour afficher les données sur **ProductsPortal**. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution **ProductsPortal**, puis cliquez sur **Propriétés**. La boîte de dialogue **Pages de propriétés** s’affiche.
10. Sur le côté gauche, cliquez sur **Projet de démarrage**. Sur le côté droit, cliquez sur **Plusieurs projets de démarrage**. Vérifiez que **ProductsServer** et **ProductsPortal** apparaissent dans cet ordre et que leur action définie est **Démarrer**.

      ![][25]

11. Toujours dans la boîte de dialogue **Propriétés**, cliquez sur **Dépendances du projet** sur le côté gauche.
12. Dans le menu déroulant **Projets**, cliquez sur **ProductsServer**. Vérifiez que **ProductsPortal** n’est pas sélectionné.
13. Dans la liste **Projets**, cliquez sur **ProductsPortal**. Vérifiez que **ProductsServer** est sélectionné.

    ![][26]

14. Cliquez sur **OK** dans la boîte de dialogue **Pages de propriétés**.

## <a name="run-the-project-locally"></a>Exécutez le projet localement.

Pour tester l’application localement dans Visual Studio, appuyez sur **F5**. Le serveur local (**ProductsServer**) doit démarrer, puis l’application **ProductsPortal** doit se lancer dans une fenêtre de navigateur. À présent, vous voyez que l'inventaire de produits répertorie des données récupérées du système local de service de produit.

![][10]

Appuyez sur **Actualiser** dans la page **ProductsPortal**. Chaque fois que vous actualisez la page, l’application de serveur affiche un message lors de l’appel de `GetProducts()` à partir de **ProductsServer**.

Fermez les deux applications avant de passer à l’étape suivante.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Déploiement du projet ProductsPortal dans une application web Azure

L’étape suivante consiste à publier une nouvelle fois l’application web Azure frontale **ProductsPortal**. Effectuez les actions suivantes :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsPortal**, puis cliquez sur **Publier**. Ensuite, cliquez sur **Publier** sur la page de **publication**.

  > [!NOTE]
  > Un message d’erreur peut s’afficher dans la fenêtre du navigateur lorsque le projet web **ProductsPortal** est démarré automatiquement après le déploiement. Ce comportement est normal. Il s’explique par le fait que l’application **ProductsServer** n’est pas encore en cours d’exécution.
>
>

2. Copiez l’URL de l’application web déployée, car vous en aurez besoin à l’étape suivante. Vous pouvez également obtenir cette URL à partir de la fenêtre Activité d’Azure App Service dans Visual Studio :

  ![][9]

3. Fermez la fenêtre du navigateur pour arrêter l’application en cours d’exécution.

### <a name="set-productsportal-as-web-app"></a>Définition de ProductsPortal en tant qu’application web

Avant d’exécuter l’application dans le cloud, vous devez vous assurer que **ProductsPortal** est démarré dans Visual Studio en tant qu’application web.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **ProductsPortal**, puis cliquez sur **Propriétés**.
2. Dans la colonne de gauche, cliquez sur **Web**.
3. Dans la section**Action de démarrage**, cliquez sur le bouton **URL de démarrage** puis, dans la zone de texte, entrez l’URL de l’application web précédemment déployée. Par exemple, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

4. Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Enregistrer tout**.
5. Dans le menu Générer de Visual Studio, cliquez sur **Régénérer la solution**.

## <a name="run-the-application"></a>Exécution de l'application

1. Appuyez sur F5 pour générer et exécuter l’application. Le serveur local (application de console **ProductsServer**) doit démarrer, puis l’application **ProductsPortal** doit se lancer dans une fenêtre de navigateur, comme le montre la capture d’écran suivante. Notez de nouveau que l’inventaire de produits répertorie les données récupérées à partir du système local du service du produit et les affiche dans l’application web. Vérifiez l’URL pour vous assurer que l’application **ProductsPortal** est exécutée dans le cloud comme une application web Azure.

   ![][1]

   > [!IMPORTANT]
   > L’application console **ProductsServer** doit être en cours d’exécution et en mesure de fournir les données à l’application **ProductsPortal**. Si le navigateur affiche une erreur, patientez quelques secondes, le temps que **ProductsServer** se charge et affiche le message suivant. Appuyez ensuite sur **Actualiser** dans le navigateur.
   >
   >

   ![][37]
2. Dans le navigateur, appuyez sur **Actualiser** sur la page **ProductsPortal**. Chaque fois que vous actualisez la page, l’application de serveur affiche un message lors de l’appel de `GetProducts()` à partir de **ProductsServer**.

    ![][38]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Relay, consultez les ressources suivantes :  

* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)  
* [Comment utiliser Azure Relay](relay-wcf-dotnet-get-started.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
