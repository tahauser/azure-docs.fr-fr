---
title: "Utiliser Azure Media Content Moderator pour détecter du contenu potentiellement osé et réservé aux adultes | Microsoft Docs"
description: "La modération de vidéo permet de détecter le contenu potentiellement osé et réservé aux adultes dans des vidéos."
services: media-services
documentationcenter: 
author: sanjeev3
manager: mikemcca
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: 1b473a6aef87e5f4c75be2becbf814ecaaab6f3a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Utiliser Azure Media Content Moderator pour détecter du contenu potentiellement osé et réservé aux adultes

## <a name="overview"></a>Vue d'ensemble
Le processeur multimédia (MP) **Azure Media Content Moderator** vous permet d’utiliser la modération assistée par ordinateur pour vos vidéos. Par exemple, vous souhaiterez peut-être détecter du contenu potentiellement osé et réservé aux adultes dans des vidéos et demander à vos équipes humaines en charge de la modération d’en examiner le contenu.

Le processeur multimédia **Azure Media Content Moderator** est actuellement disponible en préversion.

Cet article apporte des précisions sur **Azure Media Content Moderator** et illustre son utilisation avec le kit SDK Media Services pour .NET.

## <a name="content-moderator-input-files"></a>Fichiers d’entrée de Content Moderator
Fichiers vidéo. Les formats suivants sont actuellement pris en charge : MP4, MOV et WMV.

## <a name="content-moderator-output-files"></a>Fichiers de sortie de Content Moderator
La sortie modérée au format JSON inclut les images clés et les captures détectées automatiquement. Les images clés sont retournées avec des scores de confiance sur le contenu potentiellement osé ou réservé aux adultes. Elles comprennent également un indicateur booléen qui indique si un passage en revue est recommandé. Les valeurs de l’indicateur de recommandation de passage en revue sont attribuées en fonction des seuils internes pour les scores relatifs au contenu osé et réservé aux adultes.

## <a name="elements-of-the-output-json-file"></a>Éléments du fichier de sortie JSON

Le travail génère un fichier de sortie JSON qui contient des métadonnées sur les captures et les images clés détectées, et qui indique si celles-ci contiennent du contenu osé ou réservé aux adultes.

Le fichier JSON de sortie contient les éléments suivants :

### <a name="root-json-elements"></a>Éléments JSON racine

| Élément | Description |
| --- | --- |
| version |La version de Content Moderator. |
| échelle de temps |« Cycles » par seconde de la vidéo. |
| Offset |Le décalage des horodatages. Cette valeur sera toujours 0 dans la version 1.0 des API vidéo. Cette valeur pourrait changer à l’avenir. |
| taux de trames |Images par seconde de la vidéo. |
| width |La largeur de l’image vidéo de sortie, en pixels.|
| height |La hauteur de l’image vidéo de sortie, en pixels.|
| totalDuration |La durée de l’entrée vidéo, en « cycles ». |
| [fragments](#fragments-json-elements) |Les métadonnées sont mémorisées dans différents segments appelés fragments. Chaque fragment est une capture détectée automatiquement avec des valeurs de début (start), de durée (duration), un nombre d’intervalles et des événements (event). |

### <a name="fragments-json-elements"></a>Fragments d’éléments JSON

|Élément|Description|
|---|---|
| start |L’heure de début du premier événement en « cycles ». |
| duration |La durée du fragment en « cycles ». |
| interval |L’intervalle de chaque entrée d’événement dans le fragment en « cycles ». |
| [events](#events-json-elements) |Chaque événement représente un clip et chaque clip contient des images clés détectées et suivies pendant cette durée. Il s’agit d’un tableau d’événements. Le tableau externe représente un intervalle de temps. Le tableau interne se compose de 0 événement ou plus ayant eu lieu à ce moment précis.|

### <a name="events-json-elements"></a>Éléments JSON d’événements

|Élément|Description|
|---|---|
| reviewRecommended | `true` ou `false` selon que **adultScore** ou **racyScore** dépasse les seuils internes. |
| adultScore | Score de confiance pour le contenu potentiellement réservé aux adultes, sur une échelle de 0,00 à 0,99. |
| racyScore | Score de confiance pour le contenu potentiellement osé, sur une échelle de 0,00 à 0,99. |
| index | Index de l’image sur une échelle allant de l’index de la première image à l’index de la dernière image. |
| timestamp | L’emplacement de l’image, en « cycles ». |
| shotIndex | L’index de la capture parente. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Démarrage rapide et exemple de sortie de Content Moderation

### <a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)
Lors de la création d’une tâche avec **Azure Media Content Moderator**, vous devez spécifier une présélection de configuration. La présélection de configuration suivante est uniquement valable pour la modération de contenu.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Exemple de code .NET

L’exemple de code .NET suivant utilise le kit SDK Media Services .NET pour exécuter un travail Content Moderator. Il utilise une ressource de services multimédia comme entrée contenant la vidéo à modérer.
Consultez le [Démarrage rapide vidéo de Content Moderator](../cognitive-services/Content-Moderator/video-moderation-api.md) pour obtenir le code source complet et le projet Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
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
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Liens connexes
[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

[Démonstrations Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>étapes suivantes

En savoir plus sur la [solution de modération et de révision de vidéo](../cognitive-services/Content-Moderator/video-moderation-human-review.md) de Content Moderator.

Obtenez le code source complet et le projet Visual Studio dans le [démarrage rapide de modération de video](../cognitive-services/Content-Moderator/video-moderation-api.md). 

Découvrez comment générer des [revues de vidéos](../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) à partir de votre sortie modérée et [modérez des transcriptions](../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) dans .NET.

Consultez le [didacticiel de modération et de révision de vidéo](../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md) .NET détaillé. 
