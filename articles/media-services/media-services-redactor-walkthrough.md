---
title: Procédure d’édition des visages avec Azure Media Analytics | Microsoft Docs
description: Cette rubrique explique étape par étape comment exécuter un workflow de rédaction complet à l’aide d’Azure Media Services Explorer (AMSE) et d’Azure Media Redactor Visualizer (outil open source).
services: media-services
documentationcenter: ''
author: Lichard
manager: cfowler
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: rli; juliako;
ms.openlocfilehash: 6b33da21a32373196d86ec8c93f180b3d40b1c6e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Procédure d’édition des visages avec Azure Media Analytics

## <a name="overview"></a>Vue d'ensemble

**Azure Media Redactor** est un processeur multimédia [Azure Media Analytics](media-services-analytics-overview.md) qui offre la rédaction de face évolutive dans le cloud. La rédaction de face vous permet de modifier votre vidéo afin de flouter les visages des individus sélectionnés. Vous souhaitez peut-être utiliser le service de rédaction de face dans des scénarios de média et de sécurité publics. Quelques minutes de séquences vidéo contenant plusieurs visages peuvent nécessiter des heures de traitement manuel, mais avec ce service, le processus de rédaction de face ne nécessitera que quelques étapes simples. Pour plus d’informations, consultez [ce blog](https://azure.microsoft.com/blog/azure-media-redactor/).

Pour plus d’informations sur **Azure Media Redactor**, consultez la rubrique [Vue d’ensemble de la rédaction de face](media-services-face-redaction.md).

Cette rubrique explique étape par étape comment exécuter un workflow de rédaction complet à l’aide d’Azure Media Services Explorer (AMSE) et d’Azure Media Redactor Visualizer (outil open source).

Pour plus d’informations, consultez [ce](https://azure.microsoft.com/en-us/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Worfkflow d’Azure Media Services Explorer

La manière la plus simple de prendre en main Redactor consiste à utiliser l’outil AMSE open source sur github. Vous pouvez exécuter un workflow simplifié via le mode **combiné** si vous n’avez pas besoin d’accéder au json d’annotations ou aux images jpg de face.

### <a name="download-and-setup"></a>Téléchargement et configuration

1. Téléchargez l’outil AMSE [ici](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Connectez-vous à votre compte Media Services à l’aide de votre clé de service.

    Pour obtenir le nom du compte et les informations sur la clé, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez votre compte AMS. Sélectionnez Paramètres > Clés. La fenêtre Gérer les clés affiche le nom du compte ainsi que les clés primaires et secondaires. Copiez les valeurs du nom du compte et de la clé primaire.

![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Premier passage - Mode d’analyse

1. Chargez votre fichier multimédia via Ressource -> Charger, ou par un glisser -déplacer. 
1. Cliquez avec le bouton droit de la souris et traitez votre fichier multimédia en utilisant 	Analytique multimédia –> Azure Media Redactor –> Mode Analyze. 


![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

La sortie inclut un fichier json d’annotations avec les données d’emplacement de face, ainsi qu’un fichier jpg de chaque face détectée. 

![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Second passage – Mode de rédaction

1. Chargez votre ressource vidéo d’origine sur la sortie dès le premier passage et définissez-la comme ressource principale. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Facultatif) Chargez un fichier « Dance_idlist.txt » qui comprend une liste (avec délimitation par un saut de ligne) des ID que vous souhaitez éditer. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Facultatif) Apportez des modifications au fichier .json d’annotations, telles qu’une augmentation des limites du cadre englobant. 
4. Cliquez avec le bouton droit sur la ressource de sortie à partir du premier passage, sélectionnez le Redactor et exécutez-le avec le mode **Rédaction**. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Téléchargez ou partagez la ressource de sortie rédigée finale. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Outil open source Azure Media Redactor Visualizer

Un [outil de visualisation](https://github.com/Microsoft/azure-media-redactor-visualizer) open source est conçu pour aider les développeurs commençant à utiliser le format d’annotations avec l’analyse et à l’aide de la sortie.

Une fois le référentiel cloné, vous devrez télécharger FFMPEG à partir du [site officiel](https://ffmpeg.org/download.html) pour exécuter le projet.

Si vous êtes un développeur tentant d’analyser les données d’annotation JSON, recherchez des exemples de code dans Models.MetaData.

### <a name="set-up-the-tool"></a>Configuration de l’outil

1.  Téléchargez et générez la solution complète. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Téléchargez FFMPEG [ici](https://ffmpeg.org/download.html). Ce projet a été développé à l’origine avec la version be1d324 (04-10-2016) avec une liaison statique. 
3.  Copiez ffmpeg.exe et ffprobe.exe dans le même dossier de sortie en tant que AzureMediaRedactor.exe. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Exécutez AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Utiliser l’outil

1. Traitez votre vidéo dans votre compte Azure Media Services avec le processeur multimédia Redactor sur le mode Analyse. 
2. Téléchargez le fichier vidéo d’origine et la sortie du travail Rédaction - Analyse. 
3. Exécutez l’application du visualiseur et choisissez les fichiers ci-dessus. 

    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Affichez un aperçu de votre fichier. Sélectionnez les faces à flouter via le volet de droite. 
    
    ![Rédaction de face](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Le champ de texte en bas est mis à jour avec les ID de face. Créez un fichier appelé « idlist.txt » avec ces ID sous forme de liste délimitée par un saut de ligne. 

    >[!NOTE]
    > Le fichier idlist.txt doit être enregistré au format ANSI. Vous pouvez utiliser le Bloc-notes pour enregistrer au format ANSI.
    
6.  Chargez ce fichier dans la ressource de sortie de l’étape 1. Chargez la vidéo d’origine également dans cette ressource et définissez-la en tant que ressource principale. 
7.  Lancez le travail de rédaction sur cette ressource avec le mode « Rédaction » pour obtenir la vidéo rédigée finale. 

## <a name="next-steps"></a>Étapes suivantes 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Liens connexes
[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

[Démonstrations Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Annonce de Face Redaction pour Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
