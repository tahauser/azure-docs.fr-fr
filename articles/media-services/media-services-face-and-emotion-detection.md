---
title: "Détection des visages et des émotions avec Azure Media Analytics | Microsoft Docs"
description: "Cette rubrique illustre la détection faciale et d’émotions avec Azure Media Analytics."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 7a16745fc21d03f81ca6140ace54f84468749364
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Détection des visages et des émotions avec Azure Media Analytics
## <a name="overview"></a>Vue d'ensemble
Le processeur multimédia **Azure Media Face Detector** vous permet de compter, de suivre le mouvement, voire de mesurer la participation du public ainsi que ses réactions en analysant les expressions faciales. Ce service présente deux fonctionnalités : 

* **Détection faciale**
  
    La détection faciale détecte et suit les visages humains au sein d’une vidéo. Plusieurs visages peuvent être détectés et, par la suite, suivis dans leurs mouvements ; les métadonnées d’horodatage et de localisation seront retournées dans un fichier JSON. Lors du suivi, la fonctionnalité tente d’attribuer un identificateur cohérent à un visage en mouvement, même si ce dernier quitte momentanément l’image ou s’il est caché.
  
  > [!NOTE]
  > Ce service n’effectue pas la reconnaissance faciale. Une personne qui quitte l’image ou dont le visage est caché pendant une période prolongée se voit attribuer un nouvel identifiant à son retour.
  > 
  > 
* **Détection d’émotions**
  
    La détection d’émotions est un composant facultatif du processeur multimédia de détection faciale, qui analyse plusieurs caractéristiques émotionnelles sur les visages détectés, telles que le bonheur, la tristesse, la peur, la colère et bien plus encore. 

Le processeur multimédia **Azure Media Face Detector** est uniquement disponible en version préliminaire.

Cet article apporte des précisions sur **Azure Media Face Detector** et illustre son utilisation avec le kit SDK Media Services pour .NET.

## <a name="face-detector-input-files"></a>Fichiers d’entrée du détecteur facial
Fichiers vidéo. Les formats suivants sont actuellement pris en charge : MP4, MOV et WMV.

## <a name="face-detector-output-files"></a>Fichiers de sortie du détecteur facial
L’API de détection et de suivi facial permet une détection d’emplacement et un suivi de visage très précis ; elle peut détecter jusqu’à 64 visages humains dans une vidéo. Les visages filmés de face donnent les meilleurs résultats ; les visages filmés de côté ou les visages de taille réduite (24 x 24 pixels ou moins) peuvent ne pas être aussi précis.

Les visages détectés et suivis sont retournés avec les coordonnées (point gauche et supérieur, largeur, hauteur) indiquant l’emplacement des visages dans l’image en pixels, mais aussi un numéro d’identification pour chaque visage, indiquant le suivi de cette personne. Les numéros d’identification des visages peuvent être réinitialisés dans des cas où le visage filmé de face sort de l’image ou si un élément vient se superposer ; certaines personnes peuvent ainsi se voir attribuer plusieurs identifiants.

## <a id="output_elements"></a>Éléments du fichier de sortie JSON

[!INCLUDE [media-services-analytics-output-json](../../includes/media-services-analytics-output-json.md)]

Face Detector utilise des techniques de fragmentation (où les métadonnées peuvent être divisées en segments temporels, et vous téléchargez uniquement ce dont vous avez besoin) et de segmentation (où les événements sont fractionnés s’ils sont trop larges). Des calculs simples peuvent vous aider à transformer les données. Par exemple, si un événement a démarré à 6 300 (cycles), avec une échelle de temps de 2 997 (cycles par seconde) et si la fréquence d’images est de 29,97 (images par seconde), alors :

* Démarrage/Échelle de temps = 2,1 secondes
* Secondes x fréquence d'images = 63 images

## <a name="face-detection-input-and-output-example"></a>Exemple d’entrée et de sortie de détection faciale
### <a name="input-video"></a>Vidéo d’entrée
[Vidéo d’entrée](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)
Lors de la création d’une tâche de vidéo **Azure Media Face Detector**, vous devez spécifier une présélection de configuration. La présélection de configuration suivante est uniquement valable pour la détection faciale.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Descriptions des attributs
| Nom de l’attribut | Description |
| --- | --- |
| Mode |Fast : traitement rapide, mais moins précis (par défaut).|

### <a name="json-output"></a>Sortie JSON
L’exemple suivant de sortie JSON a été tronqué.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Exemple d’entrée et de sortie de détection d’émotion
### <a name="input-video"></a>Vidéo d’entrée
[Vidéo d’entrée](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)
Lors de la création d’une tâche de vidéo **Azure Media Face Detector**, vous devez spécifier une présélection de configuration. La présélection de configuration suivante spécifie la création d’un JSON en fonction de la détection d’émotion.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Descriptions des attributs
| Nom de l’attribut | Description |
| --- | --- |
| Mode |Faces : détection faciale uniquement.<br/>PerFaceEmotion : retourne les valeurs d’émotion indépendamment pour chaque détection faciale.<br/>AggregateEmotion : retourne les valeurs d’émotion moyennes pour tous les visages dans l’image. |
| AggregateEmotionWindowMs |Utilisez cet attribut si le mode AggregateEmotion est sélectionné. Spécifie la longueur de la vidéo utilisée pour produire chaque résultat agrégé, en millisecondes. |
| AggregateEmotionIntervalMs |Utilisez cet attribut si le mode AggregateEmotion est sélectionné. Spécifie à quelle fréquence les résultats agrégés doivent être produits. |

#### <a name="aggregate-defaults"></a>Valeurs d'agrégation par défaut
Les valeurs ci-dessous sont des valeurs recommandées pour les paramètres de fenêtre et d’intervalle d’agrégation. La valeur AggregateEmotionWindowMs doit être supérieure à AggregateEmotionIntervalMs.

|| Valeur(s) par défaut | Valeur(s) max | Valeur(s) min |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0,25|
| AggregateEmotionIntervalMs |0.5 |1 |0,25|

### <a name="json-output"></a>Sortie JSON
Sortie JSON pour l’émotion agrégée (tronquée) :

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Limites
* Les formats de fichier vidéo d’entrée pris en charge incluent WMV, MOV et MP4.
* Les visages sont détectables selon une plage de tailles allant de 24 x 24 à 2 048 x 2 048 pixels. Les visages en dehors de cette plage ne sont pas détectés.
* Pour chaque vidéo, le nombre maximal de visages retourné est de 64.
* Certains visages peuvent ne pas être détectés en raison de défis techniques, par exemple, les prises au très grand angle (position de la tête) et les occlusions importantes. Les prises frontales et quasi-frontales produisent les meilleurs résultats.

## <a name="net-sample-code"></a>Exemple de code .NET

Le programme suivant montre comment effectuer les tâches suivantes :

1. Créer un élément multimédia et charger un fichier multimédia dans l’élément multimédia.
2. Créer un travail avec une tâche de détection faciale basée sur un fichier de configuration qui contient la présélection JSON suivante : 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Télécharger les fichiers JSON de sortie. 

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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);

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

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Liens connexes
[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

[Démonstrations Azure Media Analytics](http://amslabs.azurewebsites.net/demos/Analytics.html)

