---
title: "Utiliser Azure Media Video Thumbnails pour créer une synthèse d’une vidéo | Microsoft Docs"
description: "La synthèse d’une vidéo peut vous aider à créer des synthèses de longues vidéos en sélectionnant automatiquement des extraits intéressants à partir de la vidéo source. Elle est utile quand vous voulez offrir une présentation rapide de ce qui se trouve dans une vidéo longue."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 4f40c9364d02929fe5bb193b4e8eb0a0157d34d2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Utiliser Azure Media Video Thumbnails pour créer une synthèse d’une vidéo
## <a name="overview"></a>Vue d'ensemble
Le processeur multimédia **Azure Media Video Thumbnails** vous permet de créer une synthèse d’une vidéo pour les clients souhaitant juste voir un aperçu d’une vidéo longue. Par exemple, les clients peuvent vouloir visionner une courte « synthèse d’une vidéo » quand ils passent le pointeur sur une miniature. En ajustant les paramètres d’ **Azure Media Video Thumbnails** via une configuration prédéfinie, vous pouvez faire appel à la puissance de la technologie de détection et de concaténation d’images pour générer de façon algorithmique un sous-clip descriptif.  

Le processeur multimédia **Azure Media Video Thumbnail** est actuellement en version préliminaire.

Cet article donne des informations détaillées sur **Azure Media Video Thumbnail** et montre comment l’utiliser avec le Kit SDK Media Services pour .NET.

## <a name="limitations"></a>Limites

Dans certains cas, si votre vidéo n’est pas composée de scènes différentes, la sortie sera composée d’un seul plan.

## <a name="video-summary-example"></a>Exemple de synthèse d’une vidéo
Voici quelques exemples de ce que le processeur Azure Media Video Thumbnails peut faire :

### <a name="original-video"></a>Vidéo d’origine
[Vidéo d’origine](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Résultat de la vidéo miniature
[Résultat de la vidéo miniature](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)
Lors de la création d’une tâche de vidéo miniature avec **Azure Media Video Thumbnails**, vous devez spécifier une configuration prédéfinie. L’exemple de miniature ci-dessus a été créé avec la configuration JSON de base suivante :

```json
    {
        "version":"1.0"
    }
```

Actuellement, vous pouvez modifier les paramètres suivants :

| Paramètre | Description |
| --- | --- |
| outputAudio |Spécifie si la vidéo obtenue contient, ou non, des données audio. <br/>Les valeurs autorisées sont : True ou False. La valeur par défaut est True. |
| fadeInFadeOut |Spécifie si des transitions en fondu sont, ou non, utilisées entre les différentes miniatures du film.  <br/>Les valeurs autorisées sont : True ou False.  La valeur par défaut est True. |
| maxMotionThumbnailDurationInSecs |Entier qui spécifie la durée que doit avoir la vidéo obtenue.  La valeur par défaut dépend de la durée de la vidéo d’origine. |

Le tableau suivant décrit la durée par défaut, quand **maxMotionThumbnailInSecs** n’est pas utilisé.

|  |  |  |
| --- | --- | --- | --- | --- |
| Durée de la vidéo |d < 3 min |3 min < d < 15 min |
| Durée de la miniature |15 s (2-3 scènes) |30 s (3-5 scènes) |

Le code JSON suivant définit les paramètres disponibles.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>Exemple de code .NET

Le programme suivant montre comment effectuer les tâches suivantes :

1. Créer un élément multimédia et charger un fichier multimédia dans l’élément multimédia.
2. Créer un travail avec une tâche de vidéo miniature basée sur un fichier de configuration qui contient la présélection JSON suivante : 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Télécharger les fichiers de sortie. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

Configurez votre environnement de développement et ajoutez des informations de connexion au fichier app.config selon la procédure décrite dans l’article [Développement Media Services avec .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exemple

```csharp
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];

            // Field for service context.
            private static CloudMediaContext _context = null;

            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

                progressJobTask.Wait();

                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

        }
    }
```

### <a name="video-thumbnail-output"></a>Résultat de la vidéo miniature
[Résultat de la vidéo miniature](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Liens connexes
[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

[Démonstrations Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

