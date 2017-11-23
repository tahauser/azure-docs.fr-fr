---
title: "Bien démarrer avec Azure Media Clipper | Microsoft Docs"
description: "Bien démarrer avec Azure Media Clipper, outil de création de clips multimédias à partir d’actifs."
services: media-services
keywords: "clip;sous-clip;encodage;média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
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
- `language` {FACULTATIF, chaîne} : définit la langue du widget. Si vous ne spécifiez pas ce paramètre, le widget tente de localiser les messages conformément à la langue du navigateur. Si aucune langue n’est détectée dans le navigateur, le widget utilise l’anglais par défaut. Pour plus d’informations, consultez la section sur les langues prises en charge.
- `languages`{FACULTATIF, JSON} : ce paramètre remplace le dictionnaire de langues par défaut par un dictionnaire personnalisé défini par l’utilisateur. Pour plus d’informations, consultez la section sur les langues prises en charge.
- `extraLanguages`(Facultatif, JSON) : ce paramètre ajoute de nouvelles langues au dictionnaire par défaut. Pour plus d’informations, consultez la section sur les langues prises en charge.

## <a name="typescript-definition"></a>Définition de TypeScript
Vous trouverez un fichier de définition de [TypeScript](https://www.typescriptlang.org/) pour Clipper [ici](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API Azure Media Clipper
Cette section décrit la surface d’API fournie par Clipper.

- `load(assets)` : charge une liste d’actifs dans le volet d’actifs (ne doit pas être utilisé avec `assetsPanelLoaderCallback`). Pour plus d’informations sur la façon de charger des actifs dans Clipper, consultez [cet article](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)` : définit le niveau de journalisation à afficher dans la console du navigateur. Les valeurs possibles sont `info`, `warn` et `error`.
- `setHeight(height)` : définit la hauteur totale du widget en pixels (la hauteur minimale est de 600 px sans le volet d’actifs, et de 850 px avec le volet d’actifs).
- `version` : obtient la version de widget.

## <a name="configuring-azure-media-clipper"></a>Configuration d’Azure Media Clipper
Consultez les étapes suivantes pour configurer Azure Media Clipper :
- [Chargement d’actifs dans Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configuration de raccourcis clavier personnalisés](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Envoi des travaux de découpage à partir de Clipper](media-services-azure-media-clipper-submit-job.md)

## <a name="supported-languages"></a>Langues prises en charge
Le widget Clipper est disponible dans 18 langues. Pour définir la langue du widget, vous devez définir le paramètre `language` lors de l’initialisation. Passez la chaîne de code de langue parmi celles de la liste suivante :
- Chinois (simplifié) : zh-hans
- Chinois (traditionnel) : zh-hant
- Tchèque : cs
- Néerlandais, flamand : nl
- Anglais : en
- Français : fr
- Allemand : de
- Hongrois : hu
- Italien : it
- Japonais : ja
- Coréen : ko
- Polonais : pl
- Portugais (Brésil) : pt-br
- Portugais (Portugal) : pt-pt
- Russe : ru
- Espagnol : es
- Suédois : sv
- Turc : tr

Pour définir un dictionnaire personnalisé ou étendre le dictionnaire de langue par défaut, vous devez définir respectivement le paramètre `languages` ou `extraLanguages`. Passez un dictionnaire personnalisé à l’aide du format JSON suivant :

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

Par exemple, l’exemple suivant définit les chaînes localisées en anglais :

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```