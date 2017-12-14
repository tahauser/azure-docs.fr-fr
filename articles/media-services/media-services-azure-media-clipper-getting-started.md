---
title: "Bien démarrer avec Azure Media Clipper | Microsoft Docs"
description: "Bien démarrer avec Azure Media Clipper, outil de création de clips vidéo à partir d’éléments AMS"
services: media-services
keywords: "clip;sous-clip;encodage;média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Créer des clips avec Azure Media Clipper
Cette section montre les étapes de base permettant de bien démarrer avec Azure Media Clipper. Les sections qui suivent fournissent des détails sur la configuration d’Azure Media Clipper.

- Tout d’abord, ajoutez les liens suivants pour Azure Media Player et Azure Media Clipper au début de votre document. Nous vous recommandons de spécifier explicitement une version de Clipper et d’Azure Media Player dans les URL. N’utilisez pas la version la plus récente de ces ressources en production, car elles sont susceptibles de changer à la demande.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Ensuite, ajoutez les classes suivantes à l’élément div où vous souhaitez instancier Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Si vous le souhaitez, pour activer le thème sombre, ajoutez la classe dark-skin :

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Ensuite, instanciez Clipper avec l’appel d’API suivant :

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Les paramètres de l’appel de méthode d’initialisation sont les suivants :
- `selector` {OBLIGATOIRE, chaîne} : sélecteur CSS de l’élément HTML correspondant où le widget doit être restitué.
- `restVersion` {OBLIGATOIRE, chaîne} : version de l’API REST Azure Media Services à cibler. La version REST définit le format de la sortie générée par le widget. Actuellement, seule la version 2.0 est prise en charge.
- `submitSubclipCallback` {OBLIGATOIRE, promesse} : fonction de rappel appelée quand l’utilisateur clique sur le bouton « Envoyer » du widget. La fonction de rappel doit attendre la sortie générée par le widget (une configuration de travail de rendu ou une définition de filtre). Pour plus d’informations, consultez Envoyer un rappel de sous-clip.
- `logLevel` {FACULTATIF, {'info', 'warn', 'error'}} : niveau de journalisation à afficher dans la console du navigateur. Valeur par défaut : error
- `minimumMarkerGap` {FACULTATIF, entier} : taille minimale d’un sous-clip (en secondes). Remarque : la valeur doit être supérieure ou égale à 6, qui est également la valeur par défaut.
- `singleBitrateMp4Profile` {FACULTATIF, objet JSON} : profil mp4 à débit unique à utiliser pour la configuration du travail de rendu générée par le widget. S’il n’est pas fourni, le [profil MP4 à débit unique par défaut](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p) est utilisé.
- `multiBitrateMp4Profile`{FACULTATIF, objet JSON} : profil mp4 multidébit à utiliser pour la configuration du travail de rendu générée par le widget. S’il n’est pas fourni, le [profil MP4 multidébit par défaut](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p) est utilisé.
- `keymap` {FACULTATIF, objet JSON} : permet de personnaliser les raccourcis clavier du widget. Pour plus d’informations, consultez [Raccourcis clavier personnalisables](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {FACULTATIF, promesse} : fonction de rappel appelée pour charger (de façon asynchrone) une nouvelle page d’actifs dans le volet d’actifs chaque fois que l’utilisateur fait défiler jusqu’en bas du volet. Pour plus d’informations, consultez Rappel de chargeur du volet d’actifs.
- `height` {FACULTATIF, nombre} : hauteur totale du widget (la hauteur minimale est de 600 px sans le volet d’actifs, et de 850 px avec le volet d’actifs).
- `subclippingMode` (FACULTATIF, {'all', 'render', 'filter'}) : modes de sous-découpage autorisés. La valeur par défaut est all.
- `filterAssetsTypes` (FACULTATIF, valeur booléenne) : ce paramètre vous permet d’afficher/masquer la liste déroulante de filtres dans le volet d’actifs. La valeur par défaut est true.
- `speedLevels` (FACULTATIF, array) : ce paramètre vous permet de définir différents niveaux de vitesse pour le lecteur vidéo. Pour plus d’informations, consultez la [documentation d’Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions).
- `resetOnJobDone` (FACULTATIF, valeur booléenne) : ce paramètre permet à Clipper de réinitialiser le sous-découpage à l’état initial quand un travail est envoyé avec succès.
- `autoplayVideo` (FACULTATIF, valeur booléenne) : ce paramètre permet à Clipper de lire automatiquement la vidéo lors de son chargement. La valeur par défaut est true.
- `language` {FACULTATIF, chaîne} : définit la langue du widget. Si vous ne spécifiez pas ce paramètre, le widget tente de localiser les messages conformément à la langue du navigateur. Si aucune langue n’est détectée dans le navigateur, le widget utilise l’anglais par défaut. Pour plus d’informations, consultez la section [Configurer la localisation](media-services-azure-media-clipper-localization.md).
- `languages`{FACULTATIF, JSON} : ce paramètre remplace le dictionnaire de langues par défaut par un dictionnaire personnalisé défini par l’utilisateur. Pour plus d’informations, consultez la section [Configurer la localisation](media-services-azure-media-clipper-localization.md).
- `extraLanguages`(Facultatif, JSON) : ce paramètre ajoute de nouvelles langues au dictionnaire par défaut. Pour plus d’informations, consultez la section [Configurer la localisation](media-services-azure-media-clipper-localization.md).

## <a name="typescript-definition"></a>Définition de TypeScript
Vous trouverez un fichier de définition de [TypeScript](https://www.typescriptlang.org/) pour Clipper [ici](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API Azure Media Clipper
Cette section décrit la surface d’API fournie par Clipper.

- `ready(handler)` : offre un mode d’exécution de JavaScript dès que le Clipper est complètement chargé et prêt à être utilisé.
- `load(assets)` : charge une liste d’éléments dans la chronologie d’éléments (ne doit pas être utilisé avec assetsPanelLoaderCallback). Pour plus d’informations sur la façon de charger des actifs dans Clipper, consultez [cet article](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)` : définit le niveau de journalisation à afficher dans la console du navigateur. Les valeurs possibles sont `info`, `warn` et `error`.
- `setHeight(height)` : définit la hauteur totale du widget en pixels (la hauteur minimale est de 600 px sans le volet d’actifs, et de 850 px avec le volet d’actifs).
- `version` : obtient la version de widget.

## <a name="next-steps"></a>Étapes suivantes
Consultez les étapes suivantes pour configurer Azure Media Clipper :
- [Chargement d’actifs dans Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configuration de raccourcis clavier personnalisés](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Envoi des travaux de découpage à partir de Clipper](media-services-azure-media-clipper-submit-job.md)
- [Configuration de la localisation](media-services-azure-media-clipper-localization.md)