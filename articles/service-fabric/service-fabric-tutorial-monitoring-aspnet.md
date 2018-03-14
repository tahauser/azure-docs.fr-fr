---
title: Surveillance et diagnostics des services ASP.NET Core dans Azure Service Fabric | Microsoft Docs
description: "Dans ce didacticiel, vous découvrez comment configurer la surveillance et les diagnostics pour une application Azure Service Fabric ASP.NET Core."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 26cca3604faa46e7398b24a2e8c25a6ad9650c18
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Didacticiel : surveiller et diagnostiquer une application ASP.NET Core dans Service Fabric
Ce didacticiel est la quatrième partie de la série. Il décrit les étapes requises pour configurer la surveillance et les diagnostics pour une application ASP.NET Core s’exécutant sur un cluster Service Fabric à l’aide d’Application Insights. Nous collecterons les données de télémétrie à partir de l’application développée dans la première partie du didacticiel [Créer une application .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md). 

Dans ce quatrième volet, vous apprenez à :
> [!div class="checklist"]
> * Configurer Application Insights pour votre application
> * Collecter les données de télémétrie de réponse pour effectuer le suivi de la communication basée sur HTTP entre les services
> * Utiliser la fonctionnalité de mappage des applications dans Application Insights
> * Ajouter des événements personnalisés à l’aide de l’API Application Insights

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer une application .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Déployer l’application sur un cluster distant](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Configurer la surveillance et les diagnostics pour l’application

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
- [Installez le Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Télécharger l’exemple d’application de vote
Si vous n’avez pas généré l’exemple d’application de vote lors de la [première partie de cette série de didacticiels](service-fabric-tutorial-create-dotnet-app.md), vous pouvez le télécharger. Dans une fenêtre Commande ou sur un terminal, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Configurer une ressource Application Insights
Application Insights est la plateforme de gestion des performances des applications pour Azure et la plateforme recommandée par Service Fabric pour la surveillance et les diagnostics d’applications. Pour créer une ressource Application Insights, accédez au [portail Azure](https://portal.azure.com). Cliquez sur **Créer une ressource** dans le menu de navigation gauche pour ouvrir la Place de marché Azure. Cliquez sur **Surveillance et gestion**, puis sur **Application Insights**.

![Créer une ressource AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Vous devez à présent remplir les informations requises sur les attributs de la ressource à créer. Entrez un *nom*, un *groupe de ressources* et un *abonnement* appropriés. Sélectionnez *l’emplacement* où vous allez déployer votre cluster Service Fabric ultérieurement. Dans ce didacticiel, nous allons déployer l’application dans un cluster local, donc le champ *Emplacement* n’est pas requis. Le *Type d’application* doit être conservé sur « Application web ASP.NET ». 

![Attributs de ressource AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Une fois que vous avez rempli les informations requises, cliquez sur **Créer** pour approvisionner la ressource. Ce processus doit prendre environ une minute. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Ajouter Application Insights aux services de l’application

Lancez Visual Studio 2017 avec élévation de privilèges. Pour ce faire, cliquez avec le bouton droit sur l’icône Visual Studio dans le menu Démarrer et choisissez **Exécuter en tant qu’administrateur**. Cliquez sur **Fichier** > **Ouvrir** > **Projet/Solution** et accédez à l’application de vote (créée dans la première partie du didacticiel ou clonée à partir de git). Ouvrez *Voting.sln* et si vous êtes invité à restaurer les packages NuGet de l’application, cliquez sur **Oui**.

Suivez ces étapes pour configurer Application Insights pour les services VotingWeb et VotingData :
1. Cliquez avec le bouton droit sur le nom du service, puis cliquez sur **Configurer Application Insights...**.

    ![Configurer AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Cliquez sur **Démarrer gratuitement**.
3. Connectez-vous à votre compte (celui avec lequel vous avez également configuré votre abonnement Azure) et sélectionnez l’abonnement où vous avez créé la ressource Application Insights. Recherchez la ressource sous *Ressource Application Insights existante* dans la liste déroulante « Ressource ». Cliquez sur **Inscrire** pour ajouter Application Insights à votre service.

    ![Inscrire AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Cliquez sur **Terminer** une fois que la boîte de dialogue qui s’affiche indique que l’action est terminée.
    
Veillez à effectuer les étapes ci-dessus pour **les deux** services dans l’application afin de terminer la configuration d’Application Insights pour l’application. La même ressource Application Insights est utilisée pour les deux services afin d’afficher les demandes entrantes et sortantes et la communication entre les services.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Ajouter le package NuGet Microsoft.ApplicationInsights.ServiceFabric.Native aux services

Application Insights a deux packages NuGet spécifiques à Service Fabric qui peuvent être utilisés selon le scénario. L’un est utilisé avec les services natifs de Service Fabric et l’autre avec les conteneurs et les exécutables invités. Dans ce cas, nous utiliserons le package NuGet Microsoft.ApplicationInsights.ServiceFabric.Native pour tirer parti de la présentation du contexte de service qu’il offre. Pour en savoir plus sur le Kit SDK Application Insights et les packages NuGet spécifiques à Service Fabric, consultez [Microsoft Application Insights pour Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md). 

Voici les étapes de configuration du package NuGet :
1. Cliquez avec le bouton droit sur la **solution de vote** au sommet de l’Explorateur de solutions et cliquez sur **Gérer les packages NuGet pour la solution...**.
2. Cliquez sur **Parcourir** dans le menu de navigation supérieur dans la fenêtre « NuGet – Solution » et cochez la case **Inclure la préversion** en regard de la barre de recherche.
3. Recherchez `Microsoft.ApplicationInsights.ServiceFabric.Native` et cliquez sur le package NuGet approprié.
4. Sur la droite, cliquez sur les deux cases à cocher en regard des deux services dans l’application, **VotingWeb** et **VotingData** et cliquez sur **Installer**.
    ![Inscription AI terminée](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. Cliquez sur **OK** dans la boîte de dialogue *Réviser les modifications* qui s’affiche et *acceptez la licence*. Ceci termine l’ajout du package NuGet aux services.
6. Vous devez maintenant configurer l’initialiseur de télémétrie dans les deux services. Pour cela, ouvrez *VotingWeb.cs* et *VotingData.cs*. Pour les deux, exécutez la procédure suivante :
    1. Ajoutez-les *à l’aide* d’instructions en haut de chaque *\<ServiceName>.cs* :
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. Dans l’instruction *return* imbriquée de *CreateServiceInstanceListeners()* ou *CreateServiceReplicaListeners()*, sous *ConfigureServices* > *services*, entre les deux services Singleton déclarés, ajoutez : `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Cette opération ajoute le *Contexte de service* à vos données de télémétrie, ce qui vous permet de mieux comprendre la source de votre télémétrie dans Application Insights. Votre instruction *return* imbriquée dans *VotingWeb.cs* doit ressembler à ceci :
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    De même, dans *VotingData.cs*, vous devriez avoir :

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

À ce stade, vous êtes prêt à déployer l’application. Cliquez sur **Démarrer** en haut (ou sur **F5**), et Visual Studio génère et package l’application, configure votre cluster local et y déploie l’application. 

Une fois le déploiement de l’application terminé, accédez à [localhost:8080](localhost:8080), où vous devriez être en mesure de voir l’exemple d’application de vote à page unique. Votez pour quelques éléments de votre choix afin de créer des exemples de données et de télémétrie. Personnellement, j’ai choisi les desserts !

![Exemples de votes AI](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

N’hésitez pas également à *supprimer* certaines options de vote lorsque vous avez terminé d’ajouter quelques votes.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Afficher les données de télémétrie et la mise en correspondance d’applications dans Application Insights 

Accédez à votre ressource Application Insights dans le portail Azure et, dans la barre de navigation gauche de la ressource, cliquez sur **Préversions** sous *Configurer*. **Activez** la *Mise en correspondance d’applications contenant plusieurs rôles* dans la liste des préversions disponibles.

![Activer la mise en correspondance d’applications AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

Cliquez sur **Vue d’ensemble** pour revenir à la page d’accueil de votre ressource. Puis cliquez sur **Recherche** en haut pour voir les traces entrantes. L’affichage des traces dans Application Insights prend quelques minutes. Si aucune trace ne s’affiche, patientez une minute et cliquez sur le bouton **Actualiser** en haut.
![Afficher les traces AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Faites défiler la fenêtre *Recherche* vers le bas pour afficher toutes les données de télémétrie entrantes que vous obtenez instantanément avec Application Insights. Pour chaque action que vous avez effectuée dans l’application de vote, il doit exister une demande PUT sortante à partir de *VotingWeb* (Votes PUT/Put [nom]), une demande PUT entrante à partir de *VotingData* (PUT VoteData/Put [nom]), suivies d’une paire de demandes GET pour l’actualisation des données affichées. Il doit également y avoir une trace de dépendance pour HTTP sur localhost, car il s’agit de demandes HTTP. Voici un exemple de ce que vous verrez sur la façon dont un vote est ajouté : ![exemple de trace de demande AI](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Vous pouvez cliquer sur l’une des traces pour afficher plus d’informations la concernant. Application Insights fournit des informations utiles sur la demande, notamment le *temps de réponse* et *l’URL de la demande*. En outre, étant donné que vous avez ajouté le package NuGet spécifique à Service Fabric, vous obtiendrez également des données relatives à votre application dans le contexte d’un cluster Service Fabric dans la section *Données personnalisées* en dessous. Ces données incluent le contexte du service, afin de voir les éléments *PartitionID* et *ReplicaId* de la source de la demande et des problèmes mieux ciblés lors du diagnostic des erreurs dans votre application.

![Détails de la trace AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

En outre, étant donné que nous avons activé le plan d’application, sur la page *Vue d’ensemble*, cliquez sur l’icône **Mise en correspondance d’applications** pour afficher vos deux services connectés.

![Détails de la trace AI](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

La mise en correspondance d’applications peut vous aider à mieux comprendre la topologie de votre application, en particulier si vous commencez à ajouter plusieurs services différents qui fonctionnent ensemble. Elle vous fournit également des données de base sur les taux de réussite des demandes et peut vous aider à diagnostiquer les demandes ayant échoué pour comprendre où se situe le problème. Pour en savoir plus sur la mise en correspondance d’applications, consultez [Mise en correspondance d’applications dans Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Ajouter une instrumentation personnalisée à votre application

Bien qu’Application Insights fournisse un grand nombre de données de télémétrie prêtes à l’emploi, vous voudrez peut-être ajouter une instrumentation personnalisée. Ceci peut être dû aux besoins de votre entreprise ou pour améliorer les diagnostics lorsque des problèmes apparaissent dans votre application. Application Insights a une API pour ingérer des événements et des métriques personnalisés, sur laquelle vous pouvez en apprendre plus [ici](../application-insights/app-insights-api-custom-events-metrics.md).

Ajoutons des événements personnalisés à *VoteDataController.cs* (sous *VotingData* > *Contrôleurs*) pour effectuer le suivi lorsque des votes sont ajoutés et supprimés à partir de l’élément *votesDictionary* sous-jacent. 
1. Ajoutez `using Microsoft.ApplicationInsights;` à la fin de l’autre à l’aide d’instructions.
2. Déclarez un nouveau *TelemetryClient* au début de la classe, sous la création de *IReliableStateManager* : `private TelemetryClient telemetry = new TelemetryClient();`.
3. Dans la fonction *Put()*, ajoutez un événement qui confirme qu’un vote a été ajouté. Ajoutez `telemetry.TrackEvent($"Added a vote for {name}");` une fois la transaction terminée, juste avant l’instruction *OkResult* retournée.
4. Dans *Delete()*, il existe un élément « if/else » basé sur la condition que *votesDictionary* contient des votes pour une option de vote donnée. 
    1. Ajoutez un événement qui confirme la suppression d’un vote dans l’instruction *if*, après *await tx.CommitAsync()* : `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Ajoutez un événement pour indiquer que la suppression n’a pas eu lieu dans l’instruction *else*, avant l’instruction retournée : `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Voici un exemple de ce à quoi vos fonctions *Put()* et *Delete()* peuvent ressembler après l’ajout des événements :

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Une fois ces modifications effectuées, **démarrez** l’application pour qu’elle génère et déploie la dernière version. Une fois le déploiement de l’application terminé, accédez à [localhost:8080](localhost:8080), puis ajoutez et supprimez des options de vote. Ensuite, revenez à votre ressource Application Insights pour voir les traces de la dernière exécution (comme auparavant, les traces peuvent prendre entre 1 et 2 minutes pour s’afficher dans Application Insights). Pour tous les votes que vous avez ajoutés et supprimés, vous devriez maintenant voir un « Événement personnalisé*, ainsi que toutes les données de télémétrie de réponse. 

![événements personnalisés](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :
> [!div class="checklist"]
> * Configurer Application Insights pour votre application
> * Collecter les données de télémétrie de réponse pour effectuer le suivi de la communication basée sur HTTP entre les services
> * Utiliser la fonctionnalité de mappage des applications dans Application Insights
> * Ajouter des événements personnalisés à l’aide de l’API Application Insights

Maintenant que vous avez terminé la configuration de la surveillance et des diagnostics pour votre application ASP.NET, essayez ce qui suit :
- [Explorer la surveillance et les diagnostics dans Service Fabric](service-fabric-diagnostics-overview.md)
- [Analyse d’événement Service Fabric avec Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
- Pour en savoir plus sur Application Insights, consultez [Documentation Application Insights](https://docs.microsoft.com/azure/application-insights/)
