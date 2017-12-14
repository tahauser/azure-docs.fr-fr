---
title: "Envoyer des travaux de détourage Azure Media Clipper | Microsoft Docs"
description: "Étapes pour l’envoi de travaux de détourage d’Azure Media Clipper"
services: media-services
keywords: "clip;sous-clip;encodage;média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Envoyer des travaux de détourage d’Azure Media Clipper
Azure Media Clipper nécessite une méthode **submitSubclipCallback** d’implémentation pour gérer la soumission de travaux de détourage. Cette fonction sert à implémenter une requête HTTP POST de la sortie Clipper à un service web. C’est dans ce service web que vous pouvez soumettre le travail d’encodage. La sortie du Clipper peut être une présélection d’encodage Media Encoder Standard pour les travaux rendus ou la charge utile API REST pour les appels filtrés du manifeste dynamique. Ce modèle de transmission directe est nécessaire, car les informations d’identification du compte Media Services ne sont pas sécurisées dans le navigateur du client.

Le diagramme de séquences suivant illustre le flux de travail entre le navigateur client, votre service Web et Azure Media Services : ![diagramme de séquences Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Dans le diagramme précédent, les quatre entités sont : le navigateur de l’utilisateur final, votre service Web, le point de terminaison CDN hébergeant les ressources Clipper et Azure Media Services. Lorsque l’utilisateur final navigue vers votre page Web, la page obtient les ressources Clipper JavaScript et CSS du point de terminaison CDN d’hébergement. L’utilisateur final configure le travail de détourage ou l’appel de création du filtre du manifeste dynamique depuis son navigateur. Lorsque l’utilisateur final soumet le travail ou l’appel de création du filtre, le navigateur place la charge utile du travail sur un service Web que vous devez déployer. Ce service Web soumet enfin le travail de détourage ou l’appel de création du filtre à Azure Media Services à l’aide des informations d’identification de votre compte de services multimédia.

L’exemple de code suivant illustre un exemple de méthode **submitSubclipCallback**. Dans cette méthode, vous mettez en œuvre la requête HTTP POST de la présélection d’encodage Media Encoder Standard. Si la publication a réussi (**résultat**), la **promesse** est résolue, sinon elle est rejetée avec des détails de l’erreur.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
La sortie de la soumission des travaux peut être une présélection d’encodage Media Encoder Standard pour les travaux rendus ou la charge utile API REST pour les filtres du manifeste dynamique.

## <a name="submitting-encoding-job-to-create-video"></a>Soumission de travaux d’encodage pour la création de vidéo
Vous pouvez soumettre un travail d’encodage Media Encoder Standard pour créer un clip vidéo suffisamment précis. Le travail d’encodage produit des vidéos rendues, un nouveau fichier MP4 fragmenté.

Le contrat de sortie de travail pour détourage rendu est un objet JSON comprenant les propriétés suivantes :

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Afin d’exécuter le travail d’encodage, soumettez le travail d’encodage Media Encoder Standard avec la présélection associée. Consultez cet article pour obtenir des détails sur la soumission des travaux d’encodage à l’aide de [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) ou [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Création rapide de clips vidéo sans encodage
Plutôt que de créer un travail d’encodage, vous pouvez utiliser Azure Media Clipper pour créer des filtres de manifeste dynamique. Les filtres n’exigent pas d’encodage et peuvent être créés rapidement si un nouvel élément n’est pas créé. Le contrat de sortie pour détourage filtré est un objet JSON comprenant les propriétés suivantes :

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Pour soumettre l’appel REST pour créer un filtre de manifeste dynamique, soumettez la charge utile du filtre associé à l’aide de [l’API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).