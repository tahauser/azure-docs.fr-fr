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
ms.openlocfilehash: 6062e6ae22971055e186b5d1068ba78c900a7a4c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Chargement d’actifs dans Azure Media Clipper
Il existe deux méthodes pour charger des actifs dans Azure Media Clipper :
1. Transmission d’une bibliothèque d’actifs de manière statique
2. Génération d’une liste d’actifs de manière dynamique par le biais d’une API

## <a name="loading-static-asset-library"></a>Chargement de bibliothèque d’actifs statique
Dans ce cas, vous transmettez un ensemble statique d’actifs à Clipper. Chaque actif inclut un ID de filtre/actif AMS, un nom et une URL de streaming publiée. Le cas échéant, vous pouvez transmettre un jeton d’authentification de protection du contenu ou un tableau d’URL miniatures. Si elles sont transmises, les miniatures sont renseignées dans l’interface. Dans les scénarios où la bibliothèque d’actifs est statique et petite, vous pouvez transmettre le contrat d’actif pour chaque actif de la bibliothèque.

Pour charger une bibliothèque d’actifs statique, utilisez la méthode **load** pour transmettre une représentation JSON de chaque actif. L’exemple de code suivant illustre la représentation JSON d’un actif.

```javascript
var assets = 
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
};
var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.load(assets)
```

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

## <a name="loading-dynamic-asset-library"></a>Chargement d’une bibliothèque d’actifs dynamique
Vous pouvez également charger des actifs de manière dynamique par un rappel. Dans les scénarios où les actifs sont générés de manière dynamique ou quand la bibliothèque est importante, vous devez charger par le biais du rappel. Pour charger des actifs de manière dynamique, vous devez implémenter la fonction de rappel facultative onLoadAssets. Cette fonction est transmise à Clipper lors de l’initialisation. Les actifs résolus doivent respecter le même contrat que les actifs chargés de manière statique. L’exemple de code suivant illustre la signature de méthode, l’entrée attendue et la sortie attendue.

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
            // TODO: perform the back-end AJAX request to get the assets using the filter parameters (search, skip, take).
            var assets = [{
                // asset (input contract)
            }, {
                // asset (input contract)
            }];

            if (/* everything turned out fine */ assets !== null) {
                resolve({
                    total: 100,
                    assets: assets
                });
            }
            else {
                reject(Error("error details"));
            }
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