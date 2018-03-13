---
title: "Débogueur de captures instantanées Azure Application Insights pour les applications .NET | Microsoft Docs"
description: "Des captures instantanées de débogage sont collectées automatiquement lorsque des exceptions sont levées dans des applications .NET de production"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 8d6f2347e06e58ec2b506aa9eaf716b3f71f3a77
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Captures instantanées de débogage sur exceptions levées dans des applications .NET

Quand une exception se produit, vous pouvez collecter automatiquement une capture instantanée de débogage à partir de votre application web dynamique. La capture instantanée indique l’état du code source et des variables au moment où l’exception a été levée. Le Débogueur de capture instantanée (préversion) dans [Azure Application Insights](app-insights-overview.md) analyse la télémétrie des exceptions à partir de votre application web. Il collecte des captures instantanées sur les principales exceptions levées afin que vous disposiez des informations dont vous avez besoin pour diagnostiquer des problèmes de production. Incluez le [package NuGet de collecte des captures instantanées](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application, et configurez éventuellement les paramètres de collecte dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Les captures instantanées apparaissent sur les [exceptions](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

Vous pouvez afficher les captures instantanées de débogage dans le portail pour consulter la pile des appels et inspecter les variables à chaque frame de pile des appels. Pour améliorer la puissance de débogage du code source, ouvrez les captures instantanées à l’aide de Visual Studio 2017 Enterprise en [téléchargeant l’extension du Débogueur de capture instantanée pour Visual Studio](https://aka.ms/snapshotdebugger). Dans Visual Studio, vous pouvez également [définir des points de capture instantanée pour prendre des captures instantanées de manière interactive](https://aka.ms/snappoint) sans attendre la levée d’une exception.

La collecte de captures instantanées est disponible pour :
* les applications .NET framework et ASP.NET exécutant .NET Framework 4.5 ou version ultérieure ;
* les applications .NET core 2.0 et ASP.NET Core 2.0 s’exécutant sous Windows.

Les environnements suivants sont pris en charge :
* Azure App Service
* Service Cloud Azure exécutant la famille de systèmes d’exploitation 4 ou ultérieur
* Services Azure Service Fabric exécutant Windows Server 2012 R2 ou ultérieur
* Machines virtuelles Azure exécutant Windows Server 2012 R2 ou ultérieur
* Machine locale physique ou virtuelle exécutant Windows Server 2012 R2 ou ultérieur

> [!NOTE]
> Les applications clientes (par exemple, WPF, Windows Forms ou UWP) ne sont pas prises en charge.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web](app-insights-asp-net.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application. 

3. Examinez les options par défaut que le package a ajoutées à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Dans certains cas (par exemple, des versions plus anciennes de la plateforme .NET), il se peut que vous deviez [configurer la collecte des exceptions](app-insights-asp-net-exceptions.md#exceptions) afin de voir celles-ci avec des captures instantanées dans le portail.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET Core 2.0

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web ASP.NET Core](app-insights-asp-net-core.md).

    > [!NOTE]
    > Être sûr que votre application fait référence à la version 2.1.1 ou plus récente, du package Microsoft.ApplicationInsights.AspNetCore.

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Modifiez la classe `Startup` de votre application pour ajouter et configurer le processeur de télémétrie du collecteur de captures instantanées.

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Configurez le collecteur de captures instantanées en ajoutant une section SnapshotCollectorConfiguration au fichier appsettings.json. Par exemple : 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurer la collecte de captures instantanées pour d’autres applications .NET

1. Si votre application n’est pas instrumentée avec Application Insights, commencez par [activer Application Insights et définir la clé d’instrumentation](app-insights-windows-desktop.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Il se peut que vous deviez modifier votre code pour les signaler. Le code de gestion des exceptions dépend de la structure de votre application, mais en voici un exemple :
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```
    
## <a name="grant-permissions"></a>Accorder des autorisations

Les propriétaires de l’abonnement Azure peuvent inspecter des captures instantanées. Les autres utilisateurs doivent y être autorisés par un propriétaire.

Pour accorder cette autorisation, vous devez attribuer le rôle `Application Insights Snapshot Debugger` aux utilisateurs chargés d’inspecter les captures instantanées. Ce rôle peut être attribué à des utilisateurs ou à des groupes par les propriétaires d’abonnements pour la ressource Application Insights cible, ou pour son groupe de ressources ou son abonnement.

1. Ouvrez le panneau Contrôle d’accès (IAM).
1. Cliquez sur le bouton +Ajouter.
1. Sélectionnez le débogueur de capture instantanée d’Application Insights dans la liste déroulante des rôles.
1. Entrez le nom de l’utilisateur à ajouter.
1. Cliquez sur le bouton Enregistrer pour ajouter l’utilisateur au rôle.


> [!IMPORTANT]
> Les captures instantanées des valeurs de variables et de paramètres peuvent contenir des informations personnelles ou sensibles.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Déboguer des captures instantanées dans le portail Application Insights

Si une capture instantanée est disponible pour une exception ou un ID de problème donné, le bouton **Ouvrir la capture instantanée de débogage** s’affiche dans [l’exception](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

![Bouton Ouvrir la capture instantanée de débogage sur l’exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Une pile d’appel et un volet de variables s’affichent dans la vue Capture instantanée de débogage. Quand vous sélectionnez des images de la pile d’appel dans le volet correspondant, vous voyez les variables et les paramètres locaux de cet appel de fonction dans le volet des variables.

![Afficher la capture instantanée de débogage dans le portail](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Les captures instantanées peuvent contenir des informations sensibles qui, par défaut, ne sont pas visibles. Pour afficher les captures instantanées, le rôle `Application Insights Snapshot Debugger` doit vous être attribué.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Déboguer des captures instantanées avec Visual Studio 2017 Enterprise
1. Cliquez sur le bouton **Télécharger la capture instantanée** pour télécharger un fichier `.diagsession`, qui peut être ouvert par Visual Studio 2017 Enterprise. 

2. Pour ouvrir le fichier `.diagsession`, vous devez d’abord [télécharger et installer l’extension du débogueur de captures instantanées pour Visual Studio](https://aka.ms/snapshotdebugger).

3. Une fois le fichier de capture instantanée ouvert, la page de débogage de minidump s’affiche dans Visual Studio. Cliquez sur **Debug Managed Code** (Déboguer le code managé) pour démarrer le débogage de la capture instantanée. La capture instantanée s’ouvre sur la ligne de code où l’exception a été levée et vous permet de déboguer l’état actuel du processus.

    ![Afficher la capture instantanée de débogage dans Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

La capture instantanée téléchargée contient tous les fichiers de symboles qui ont été détectés sur votre serveur d’applications web. Ces fichiers de symboles sont nécessaires pour associer les données de capture instantanée au code source. Pour les applications App Service, veillez à activer le déploiement des symboles lorsque vous publiez vos applications web.

## <a name="how-snapshots-work"></a>Fonctionnement des captures instantanées

Au démarrage de votre application, un processus distinct de chargeur de captures instantanées qui surveille les demandes de captures instantanées de votre application est créé. Quand une capture instantanée est demandée, un cliché instantané du processus en cours d’exécution est effectué en 10 à 20 millisecondes environ. Le processus de cliché instantané est ensuite analysé et une capture instantanée est créée sans interrompre l’exécution du processus principal ni le trafic aux utilisateurs. La capture instantanée est ensuite chargée sur Application Insights, ainsi que les fichiers de symboles (.pdb) nécessaires à son affichage.

## <a name="current-limitations"></a>Limitations actuelles

### <a name="publish-symbols"></a>Publier des symboles
Le débogueur de captures instantanées nécessite que des fichiers de symboles se trouvent sur le serveur de production pour le décodage des variables et pour fournir une fonctionnalité de débogage dans Visual Studio. La version 15.2 de Visual Studio 2017 publie les symboles des builds de mise en production par défaut lors de sa publication dans App Service. Dans les versions antérieures, vous devez ajouter la ligne suivante à votre fichier de profil de publication `.pubxml` afin que les symboles soient publiés en mode Mise en production :

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pour Calcul Azure et d’autres types, vérifiez que les fichiers de symboles se trouvent dans le même dossier que le fichier .dll de l’application principale (généralement `wwwroot/bin`), ou sont disponibles dans le chemin d’accès actuel.

### <a name="optimized-builds"></a>Optimisation des versions
Dans certains cas, des variables locales ne sont pas visibles dans les builds de mise en production en raison d’optimisations appliquées pendant le processus de génération.

## <a name="troubleshooting"></a>Résolution de problèmes

Les conseils suivants peuvent vous aider à résoudre des problèmes rencontrés avec le Débogueur de capture instantanée.

### <a name="verify-the-instrumentation-key"></a>Vérifier la clé d’instrumentation

Assurez-vous que vous utilisez la clé d’instrumentation correcte dans votre application publiée. En règle générale, Application Insights lit la clé d’instrumentation à partir du fichier ApplicationInsights.config. Vérifiez que la valeur est identique à la clé d’instrumentation de la ressource Application Insights que vous voyez dans le portail.

### <a name="check-the-uploader-logs"></a>Vérifier les journaux du chargeur

Une fois une capture instantanée créée, un fichier minidump (.dmp) est créé sur le disque. Un processus distinct du chargeur prend ce fichier minidump et le charge, ainsi que tous les fichiers PDB associés, vers le stockage du Débogueur de capture instantanée d’Application Insights. Une fois le fichier minidump correctement chargé, il est supprimé du disque. Les fichiers journaux du chargeur de fichier minidump sont conservés sur le disque. Dans un environnement App Service, vous pouvez trouver ces fichiers journaux dans `D:\Home\LogFiles\Uploader_*.log`. Le site de gestion Kudu pour App Service permet de rechercher ces fichiers journaux.

1. Ouvrez votre application App Service dans le portail Azure.

2. Sélectionnez le panneau **Outils avancés**, ou recherchez **Kudu**.
3. Cliquez sur **Atteindre**.
4. Dans le zone de liste déroulante de la **Console de débogage**, sélectionnez **CMD**.
5. Cliquez sur **LogFiles**.

Vous devriez voir au moins un fichier dont le nom commence par `Uploader_` et dont l’extension est `.log`. Cliquez sur l’icône appropriée pour télécharger tous les fichiers journaux ou les ouvrir dans un navigateur.
Le nom de fichier inclut le nom de ma machine. Si votre instance App Service est hébergée sur plusieurs machines, il existe des fichiers journaux distincts pour chacune d’elles. Lorsque le chargeur détecte la présence d’un nouveau fichier minidump, celui-ci est enregistré dans le fichier journal. Voici un exemple chargement réussi :

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

Dans l’exemple précédent, la clé d’instrumentation est `c12a605e73c44346a984e00000000000`. Cette valeur doit correspondre à la clé d’instrumentation de votre application.
Le fichier minidump est associé à une capture instantanée dont l’ID est `139e411a23934dc0b9ea08a626db16c5`. Vous pouvez utiliser cet ID ultérieurement pour rechercher la télémétrie d’exception associée dans Application Insights - Analyses.

Le chargeur analyse les nouveaux fichiers PDB environ toutes les 15 minutes. Voici un exemple :

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Pour les applications qui ne sont _pas_ hébergées dans App Service, les fichiers journaux du chargeur figurent dans le même dossier que les fichiers minidump : `%TEMP%\Dumps\<ikey>` (où `<ikey>` est votre clé d’instrumentation).

### <a name="troubleshooting-cloud-services"></a>Dépannage de Cloud Services
Pour les rôles dans Cloud Services, le dossier temporaire par défaut peut être trop petit pour contenir les fichiers minidump, conduisant à des pertes d’instantanés.
L’espace nécessaire varie selon le jeu de travail total de votre application et le nombre d’instantanés simultanés.
Le jeu de travail du rôle web ASP.NET 32 bits contient en général de 200 à 500 Mo.
Vous devez autoriser au moins deux instantanés simultanés.
Par exemple, si votre application utilise un jeu de travail de 1 Go au total, vous devez vous assurer qu’il y a au moins 2 Go d’espace disque pour stocker les instantanés.
Suivez ces étapes pour configurer votre rôle service cloud avec une ressource locale dédiée pour les instantanés.

1. Ajoutez une nouvelle ressource locale à votre service cloud en modifiant le fichier de définition (.csdf) du service cloud. L’exemple suivant définit une ressource appelée `SnapshotStore` avec une taille de 5 Go.
```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
```

2. Modifiez la méthode `OnStart` de votre rôle pour ajouter une variable d’environnement qui pointe vers la ressource locale `SnapshotStore`.
```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
```

3. Mettez à jour le fichier ApplicationInsights.config de votre rôle pour remplacer l’emplacement de dossier temporaire utilisé par `SnapshotCollector`
```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
  </TelemetryProcessors>
```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Utilisez une recherche Application Insights pour trouver des exceptions avec des captures instantanées

Quand un instantané est créé, l’exception levée est marquée avec un ID d’instantané. Lorsque la télémétrie d’exception est signalée à Application Insights, cet ID d’instantané est inclus en tant que propriété personnalisée. Le panneau Rechercher dans Application Insights permet de trouver toute télémétrie avec la propriété personnalisée Search `ai.snapshot.id`.

1. Parcourez votre ressource Application Insights dans le portail Azure.
2. Cliquez sur **Rechercher**.
3. Tapez `ai.snapshot.id` dans la zone de texte Rechercher, puis appuyez sur Entrée.

![Rechercher une télémétrie avec un ID d’instantané dans le portail](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Si cette recherche ne retourne aucun résultat, cela signifie qu’aucun instantané n’a été signalé à Application Insights pour votre application dans l’intervalle de temps sélectionné.

Pour rechercher un ID d’instantané spécifique dans les fichiers journaux du chargeur, tapez cet ID dans la zone de recherche. Si vous ne trouvez la télémétrie d’une capture instantanée dont vous savez qu’elle a été chargée, procédez comme suit :

1. Vérifiez soigneusement que vous examinez la ressource Application Insights appropriée en vérifiant la clé d’instrumentation.

2. À l’aide de l’horodateur du fichier journal du chargeur, ajustez le filtre Intervalle de temps de la recherche pour couvrir cet intervalle.

Si vous ne voyez toujours pas d’exception avec cet ID d’instantané, cela signifie que la télémétrie de l’exception n’a pas été signalée à Application Insights. Cette situation peut se produire si votre application s’est arrêtée anormalement après avoir pris la capture instantanée, mais avant d’avoir signalé la télémétrie de l’exception. Dans ce cas, vérifiez les journaux d’App Service sous `Diagnose and solve problems` pour voir si des redémarrages intempestifs ou des exceptions non prises en charge se sont produits.

## <a name="next-steps"></a>Étapes suivantes

* [Définir des points de capture instantanée dans votre code](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) afin obtenir des captures instantanées sans attendre la levée d’une exception.
* [Diagnostiquer des exceptions dans vos applications web](app-insights-asp-net-exceptions.md) explique comment rendre visible à Application Insights un plus grand nombre d’exceptions. 
* [Détection intelligente](app-insights-proactive-diagnostics.md) permet de détecter automatiquement les anomalies relatives aux performances.
