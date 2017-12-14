---
title: Charger des actifs dans Azure Media Clipper | Microsoft Docs
description: "Étapes pour le chargement de actifs dans Azure Media Clipper"
services: media-services
keywords: "clip;sous-clip;encodage;média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Chargement d’actifs dans Azure Media Clipper
Il existe deux méthodes pour charger des actifs dans Azure Media Clipper :
1. Transmission d’une bibliothèque d’actifs de manière statique
2. Génération d’une liste d’actifs de manière dynamique par le biais d’une API

## <a name="statically-load-videos-into-clipper"></a>Charger des vidéos de façon statique dans Clipper
Chargez des vidéos de façon statique dans Clipper pour permettre aux utilisateurs finaux de générer des clips sans sélectionner de vidéos à partir du panneau de sélection des éléments.

Dans ce cas, vous transmettez un ensemble statique d’actifs à Clipper. Chaque actif inclut un ID de filtre/actif AMS, un nom et une URL de streaming publiée. Le cas échéant, vous pouvez transmettre un jeton d’authentification de protection du contenu ou un tableau d’URL miniatures. Si elles sont transmises, les miniatures sont renseignées dans l’interface. Dans les scénarios où la bibliothèque d’actifs est statique et petite, vous pouvez transmettre le contrat d’actif pour chaque actif de la bibliothèque.

> [!NOTE]
> Lors du chargement statique des éléments dans Clipper, ces derniers sont ajoutés **directement à la chronologie**  et le **volet Élément multimédia n’est pas affiché**. Le premier élément multimédia est ajouté à la chronologie et les autres éléments sont empilés à droite de la chronologie).

Pour charger une bibliothèque d’actifs statique, utilisez la méthode **load** pour transmettre une représentation JSON de chaque actif. L’exemple de code suivant illustre la représentation JSON d’un actif.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Il est recommandé de mettre en chaîne l’appel de méthode Load() avec la méthode Ready(gestionnaire) comme indiqué dans l’exemple précédent. L’exemple précédent garantit que le widget est prêt avant le chargement des éléments.

> [!NOTE]
> Pour que les URL miniatures fonctionnent comme prévu dans la chronologie Clipper, elles doivent être distribuées uniformément dans la vidéo (en fonction de la durée) et dans l’ordre chronologique dans le tableau. Vous pouvez utiliser l’extrait de code JSON prédéfini suivant en guise de référence pour générer des images avec le processeur « Media Encoder Standard » :

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Charger des vidéos de façon dynamique dans Clipper
Chargez des vidéos de façon dynamique dans Clipper pour permettre aux utilisateurs finaux de sélectionner des vidéos depuis le panneau de sélection des éléments pour les détourer.

Vous pouvez également charger des actifs de manière dynamique par un rappel. Dans les scénarios où les actifs sont générés de manière dynamique ou quand la bibliothèque est importante, vous devez charger par le biais du rappel. Pour charger des actifs de manière dynamique, vous devez implémenter la fonction de rappel facultative onLoadAssets. Cette fonction est transmise à Clipper lors de l’initialisation. Les actifs résolus doivent respecter le même contrat que les actifs chargés de manière statique. L’exemple de code suivant illustre la signature de méthode, l’entrée attendue et la sortie attendue.

> [!NOTE]
> Lors du chargement des éléments de façon dynamique dans Clipper, ces derniers sont affichés dans le **panneau de sélection des éléments**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```