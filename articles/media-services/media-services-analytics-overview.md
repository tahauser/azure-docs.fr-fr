---
title: Media Analytics sur la plateforme Media Services | Microsoft Docs
description: "Présentation de la version préliminaire publique de Media Analytics, un ensemble de services de reconnaissance vocale et de vision par ordinateur destinés aux entreprises et répondant aux exigences de conformité, de sécurité et de présence mondiale"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: 88c854a6a2bc98a6851246c0ac3481869bbd9c34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics sur la plateforme Media Services
## <a name="overview"></a>Vue d'ensemble
Les organisations sont de plus en plus nombreuses à privilégier les supports vidéo pour former leurs employés, susciter l’intérêt de leurs clients et présenter leurs activités commerciales. Le cloud computing offre un moyen de stocker, diffuser et accéder à ces fichiers multimédias volumineux. Mais lorsque la bibliothèque de contenu vidéo d’une société augmente, elle nécessite des moyens tout aussi efficaces d’extraire des analyses du contenu. 

Pour répondre à ce besoin croissant, Azure Media Services propose Azure Media Analytics. Media Analytics est une collection de composants visuels et vocaux qui aident les organisations et les entreprises à extraire des connaissances exploitables de leurs fichiers vidéo. Reposant sur les principaux composants de la plateforme Azure Media Services, Media Analytics est à même de traiter les données multimédias à l’échelle souhaitée.

Avec Media Analytics, les développeurs peuvent rapidement ajouter des fonctionnalités avancées aux applications. Il répond aux attentes des grandes entreprises en matière d’échelle, de conformité, de sécurité et de présence mondiale.

Le diagramme suivant montre Media Analytics et d’autres parties principales de la plateforme Media Services. 

![Flux de travail VOD](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Les processeurs multimédias Media Analytics créent des fichiers MP4 ou JSON. Si un processeur multimédia produit un fichier MP4, vous pouvez télécharger ce dernier progressivement. Si un processeur multimédia a produit un fichier JSON, vous pouvez télécharger ce dernier à partir d’Azure Blob Storage. 

## <a name="media-analytics-services"></a>Services Media Analytics

### <a name="indexer"></a>Indexeur
Avec Azure Media Indexer, vous pouvez activer la recherche pour du contenu et générer des pistes de sous-titrage. Par rapport à la version précédente d’Azure Media Indexer 2 Preview a une indexation plus rapide et une prise en charge plus large des langues. Les langues prises en charge sont l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois, le portugais et l’arabe. Pour obtenir des informations détaillées et des exemples, consultez [Traiter les vidéos avec l’Indexeur multimédia Azure 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse associe des fonctionnalités de stabilisation vidéo et de création de séquences pour créer rapidement des vidéos immédiatement diffusables à partir de vos vidéos plus longues. En plus de la création de vidéos en accéléré, Hyperlapse vous permet de stabiliser des vidéos tremblantes qui ont été prises avec un téléphone portable ou un caméscope. Pour obtenir des informations détaillées et des exemples, consultez [Fichiers multimédia hyperlapse avec Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Motion Detector
Vous pouvez utiliser Motion Detector pour détecter les mouvements dans les vidéos à arrière-plan fixe. Cela permet d’identifier les faux positifs sur les événements de mouvement détectés par les caméras de surveillance. Pour obtenir des informations détaillées et des exemples, consultez [Détection de mouvement pour Azure Media Analytics](media-services-motion-detection.md).
### <a name="face-detector"></a>Face Detector
Avec Face Detector, vous pouvez détecter les visages et les émotions qui y sont exprimées, comme la joie, la tristesse et la surprise. Ce service est très utile dans plusieurs secteurs d’activité, comme expliqué plus tard, notamment pour l’agrégation et l’analyse des réactions des personnes participant à un événement. Pour obtenir des informations détaillées et des exemples, consultez [Détection des visages et des émotions pour Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Video Summarization (Création de résumés de vidéos)
La synthèse d’une vidéo peut vous aider à créer des synthèses de longues vidéos en sélectionnant automatiquement des extraits intéressants à partir de la vidéo source. Cette fonctionnalité est utile quand vous voulez offrir une présentation rapide de ce qui se trouve dans une vidéo longue. Pour obtenir des informations détaillées et des exemples, consultez [Utiliser Azure Media Video Thumbnails pour créer une synthèse d’une vidéo](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Reconnaissance optique de caractères
Avec Azure Media OCR (reconnaissance optique de caractères), vous pouvez convertir le contenu texte de fichiers vidéo en un texte numérique modifiable et pouvant faire l’objet d’une recherche. Vous pouvez ensuite automatiser l’extraction de métadonnées explicites à partir du signal vidéo de votre contenu multimédia.
### <a name="scalable-face-redaction"></a>Rédaction de face évolutive
Azure Media Redactor est un processeur multimédia Media Analytics qui offre la rédaction de face évolutive dans le cloud. La rédaction de face vous permet de modifier votre vidéo afin de flouter les visages des individus sélectionnés. Vous souhaitez peut-être utiliser le service de rédaction de face dans les médias d’actualité ou lorsque la sécurité publique est impliquée. Quelques minutes de séquences vidéo contenant plusieurs visages peuvent nécessiter des heures de traitement manuel, mais avec ce service, le processus de rédaction de face ne nécessite que quelques étapes simples. Pour plus d'informations, consultez l’article [Éditer les visages avec Azure Media Analytique](media-services-face-redaction.md).
### <a name="content-moderation"></a>Modération du contenu
Azure Content Moderator vous permet d’utiliser une modération assistée par ordinateur pour vos vidéos. Par exemple, vous souhaiterez peut-être détecter du contenu potentiellement osé et réservé aux adultes dans des vidéos et demander à vos équipes humaines en charge de la modération d’en examiner le contenu. La modération manuelle des vidéos pour détecter du contenu indésirable est une tâche fastidieuse et coûteuse. Ce service et les outils de révision associés vous permettent de combiner la modération assistée par ordinateur avec des capacités humaines pour obtenir des résultats optimaux efficacement et à moindre coût. Pour plus d’informations, voir l’article [Traiter vos vidéos avec Azure Content Moderator](media-services-content-moderation.md).

## <a name="common-scenarios"></a>Scénarios courants
Media Analytics peut aider les organisations et entreprises à mieux exploiter et cibler leurs contenus vidéo et à améliorer la gestion des gros volumes de contenus vidéo. Voici quelques exemples de scénario :

* **Centres d’appel**. Même avec le développement des médias sociaux, les centres d’appels client sont encore largement utilisés pour faciliter les opérations des services client. Ces données audio contiennent une mine d’informations sur les clients, qui peuvent être utilisées pour mieux répondre aux attentes des clients. Avec Media Indexer, les organisations peuvent extraire du texte et créer des index de recherche et des tableaux de bord. Ils peuvent ensuite extraire des informations sur les réclamations, les causes des réclamations et autres données pertinentes.
* **Modération du contenu généré par les utilisateurs**. Des médias d’information aux services de police, de nombreuses organisations ont des portails publics sur lesquels est publié du contenu multimédia généré par l’utilisateur, comme des vidéos et des images. Le volume de contenu peut fortement augmenter en raison d’événements inattendus. Dans ce type de scénario, il est difficile de vérifier manuellement et correctement la pertinence de tout le contenu. Les clients peuvent déléguer cette tâche au service de modération du contenu pour se concentrer sur le contenu approprié.
* **Surveillance**. La généralisation de l’utilisation des caméras IP s’accompagne d’un inventaire croissant de vidéos de surveillance. L’examen manuel des vidéos de surveillance est long et sujet aux erreurs humaines. Media Analytics fournit plusieurs services, tels que la détection de mouvement, la détection des visages et Hyperlapse, pour faciliter l’examen, la gestion et la création d’éléments dérivés.

## <a name="media-analytics-media-processors"></a>Processeurs multimédia Media Analytics
Cette section répertorie tous les processeurs multimédia Media Analytics et montre comment utiliser .NET ou REST pour obtenir un objet de processeur multimédia (MP).

### <a name="mp-names"></a>Noms MP
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Détecteur de visage Azure Media
* Détecteur de mouvement Azure Media
* Miniatures vidéo Azure Media
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
La fonction suivante sélectionne un des noms MP spécifiés et retourne un objet MP.

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


### <a name="rest"></a>REST
Demande :

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Réponse :

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Démonstrations
Consultez les [Démonstrations Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articles connexes
Consultez [l’Annonce concernant Media Services Analytics](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
