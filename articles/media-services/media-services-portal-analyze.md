---
title: "Analyser vos éléments multimédias à l’aide du portail Azure | Microsoft Docs"
description: "Cette rubrique explique comment traiter vos éléments multimédia avec des processeurs multimédia Media Analytics à l’aide du portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: fee9f8d2204869cbe5cac7f446e8011305e92bfa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analyser vos éléments multimédia à l’aide du portail Azure
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Vue d'ensemble
Azure Media Services Analytics est un ensemble de composants de reconnaissance vocale et de vision qui répondent aux besoins des entreprises en termes de conformité, de sécurité et de portée mondiale, et qui aident les organisations et les entreprises à extraire des informations exploitables de leurs fichiers vidéo. Pour une présentation plus détaillée d’Azure Media Services Analytics, consultez [cette](media-services-analytics-overview.md) rubrique. 

Cette rubrique explique comment traiter vos éléments multimédia avec des processeurs multimédia Media Analytics à l’aide du portail Azure. Les processeurs multimédia Media Analytics créent des fichiers MP4 ou JSON. Si un processeur multimédia a produit un fichier MP4, vous téléchargez celui-ci progressivement. Si un processeur multimédia a produit un fichier JSON, vous téléchargez celui-ci à partir du Stockage Blob Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Sélectionner une ressource que vous souhaitez analyser
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **Paramètres**, sélectionnez **Éléments multimédias**.  
   .
    ![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Sélectionnez l’élément que vous souhaitez analyser, puis appuyez sur le bouton **Analyser**.
   
    ![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Dans la fenêtre **Traiter un fichier multimédia avec Analytique multimédia**, sélectionnez le processeur. 
   
    Le reste de cet article explique pourquoi et comment utiliser chaque processeur. 
5. Appuyez sur **Créer** pour démarrer un travail.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Le processeur multimédia **Azure Media Indexer** permet d’effectuer des recherches dans les fichiers multimédias et le contenu et de générer des pistes de sous-titrage. Cette section fournit des détails sur les options que vous spécifiez pour ce processeur multimédia.

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Langage
La langue naturelle à reconnaître dans le fichier multimédia. Par exemple, anglais ou espagnol. 

### <a name="captions"></a>Sous-titres
Vous pouvez choisir un format de sous-titre qui sera généré à partir de votre contenu. Un travail d’indexation peut générer des fichiers de sous-titres dans les formats suivants :  

* **SAMI**
* **TTML**
* **WebVTT**

Les fichiers de sous-titre dans ces formats permettent de rendre un fichier audio et vidéo accessible aux malentendants.

### <a name="aib-file"></a>Fichier AIB
Sélectionnez cette option si vous souhaitez générer le fichier Audio Index Blob pour une utilisation avec SQL Server IFilter personnalisé. Pour plus d’informations, consultez [ce](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Mots clés
Sélectionnez cette option si vous souhaitez générer un fichier XML de mots clés. Ce fichier contient les mots clés extraits à partir du contenu de la reconnaissance vocale, avec les informations relatives à la fréquence et à la référence.

### <a name="job-name"></a>Nom du travail
Nom convivial qui vous permet d’identifier le travail. [Cet](media-services-portal-check-job-progress.md) article explique comment vous pouvez surveiller la progression d’un travail. 

### <a name="output-file"></a>Fichier de sortie
Nom convivial qui vous permet d’identifier le contenu en sortie. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse est un processeur multimédia qui crée des vidéos exceptionnelles image par image accélérées (time-lapse) à partir d’un contenu de caméra à la première personne (first-person camera) ou d’action.  Pour plus d’informations, consultez [cette rubrique](media-services-hyperlapse-content.md) . Cette section fournit des détails sur les options que vous spécifiez pour ce processeur multimédia.

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Vitesse
Indique la vitesse d’accélération de la vidéo d’entrée. La sortie est un rendu stabilisé et accéléré de l'entrée vidéo.

### <a name="job-name"></a>Nom du travail
Nom convivial qui vous permet d’identifier le travail. [Cet](media-services-portal-check-job-progress.md) article explique comment vous pouvez surveiller la progression d’un travail. 

### <a name="output-file"></a>Fichier de sortie
Nom convivial qui vous permet d’identifier le contenu en sortie. 

## <a name="azure-media-face-detector"></a>Détecteur de visage Azure Media
Le processeur multimédia **Azure Media Face Detector** vous permet de compter, de suivre le mouvement, voire de mesurer la participation du public ainsi que ses réactions en analysant les expressions faciales. Ce service présente deux fonctionnalités : 

* **Détection faciale**
  
    La détection faciale détecte et suit les visages humains au sein d’une vidéo. Plusieurs visages peuvent être détectés et, par la suite, suivis dans leurs mouvements ; les métadonnées d’horodatage et de localisation seront retournées dans un fichier JSON. Lors du suivi, la fonctionnalité tente d’attribuer un identificateur cohérent à un visage en mouvement, même si ce dernier quitte momentanément l’image ou s’il est caché.
  
  > [!NOTE]
  > Ce service n’effectue pas la reconnaissance faciale. Une personne qui quitte l’image ou dont le visage est caché pendant une période prolongée se voit attribuer un nouvel identifiant à son retour.
  > 
  > 
* **Détection d’émotions**
  
    La détection d’émotions est un composant facultatif du processeur multimédia de détection faciale, qui analyse plusieurs caractéristiques émotionnelles sur les visages détectés, telles que le bonheur, la tristesse, la peur, la colère et bien plus encore. 

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Mode de détection
Le processeur peut utiliser l’un des modes suivants :

* détection faciale
* détection d’émotion par visage
* détection d’émotion agrégée

### <a name="job-name"></a>Nom du travail
Nom convivial qui vous permet d’identifier le travail. [Cet](media-services-portal-check-job-progress.md) article explique comment vous pouvez surveiller la progression d’un travail. 

### <a name="output-file"></a>Fichier de sortie
Nom convivial qui vous permet d’identifier le contenu en sortie. 

## <a name="azure-media-motion-detector"></a>Détecteur de mouvement Azure Media
Le processeur multimédia **Azure Media Motion Detector** vous permet d’identifier efficacement les passages intéressants dans une vidéo qui, autrement, serait longue et monotone. La détection de mouvement peut être utilisée sur des séquences d’une caméra fixe pour identifier les passages de la vidéo où un mouvement se produit. Elle génère un fichier JSON contenant des métadonnées avec des horodateurs et le cadre de limitation de la vidéo où s’est produit l’événement.

Ciblant les vidéos de surveillance, cette technologie est en mesure de classer les mouvements en événements pertinents et en faux positifs, tels que les ombres et les variations d’éclairage. Cela vous permet de générer des alertes de sécurité à partir de séquences vidéo sans perdre de temps avec d’innombrables faux positifs, et tout en accédant rapidement aux moments clés dans des vidéos de surveillance extrêmement longues.

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniatures vidéo Azure Media
Ce processeur peut vous aider à créer des synthèses de longues vidéos en sélectionnant automatiquement des extraits intéressants à partir de la vidéo source. Elle est utile quand vous voulez offrir une présentation rapide de ce qui se trouve dans une vidéo longue. Pour obtenir des informations détaillées et des exemples, consultez [Utiliser Azure Media Video Thumbnails pour créer une synthèse d’une vidéo](media-services-video-summarization.md)

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nom du travail
Nom convivial qui vous permet d’identifier le travail. [Cet](media-services-portal-check-job-progress.md) article explique comment vous pouvez surveiller la progression d’un travail. 

### <a name="output-file"></a>Fichier de sortie
Nom convivial qui vous permet d’identifier le contenu en sortie. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Ce processeur vous aide à détecter du contenu osé ou pour adultes dans les vidéos. Le processeur détecte automatiquement des captures et images clés dans la vidéo. Il évalue les images clés pour déterminer si elles comprennent du contenu osé ou pour adultes, et suggère de les examiner en fonction des seuils par défaut. Pour des informations détaillées et des exemples, voir [Utiliser Azure Media Content Moderator pour modérer des vidéos](media-services-content-moderation.md)

![Modérer des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Utiliser « 2.0 ».

### <a name="mode"></a>Mode
La version 2.0 ignore le paramètre `Mode`.

## <a name="next-steps"></a>Étapes suivantes
Afficher les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
