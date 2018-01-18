---
title: "Codecs et formats standard de l’encodeur multimédia"
description: "Cette rubrique fournit une vue d’ensemble des codecs et formats Media Encoder Standard."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 036c192c5f52a1943bc2799ad6c7e6db7bbffcc4
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Codecs et formats standard de l’encodeur multimédia
Ce document contient la liste des formats de fichier d’importation et d’exportation les plus courants que vous pouvez utiliser avec l’encodeur multimédia.

## <a name="input-containerfile-formats"></a>Formats de conteneurs/fichiers d’entrée
| Formats de fichier (extensions de fichier) | Prise en charge |
| --- | --- | --- | --- |
| FLV (avec les codecs H.264 et AAC) (.flv) |Oui |
| MXF    (.mxf) |Oui |
| GXF    (.gxf) |Oui |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Oui |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Oui |
| AVI (8 bits/10 bits non compressé) (.avi) |Oui |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Oui |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |OUI |
| Matroska/WebM (.mkv) |Oui |
| WAVE/WAV (.wav) |Oui |
| QuickTime (.mov) |Oui |

> [!NOTE]
> La liste ci-dessus répertorie les extensions de fichier les plus couramment rencontrées. Media Encoder Standard prend en charge de nombreuses autres extensions (par exemple : .m2ts, .mpeg2video, .qt). Si vous essayez d’encoder un fichier et que vous obtenez un message d’erreur indiquant que le format n’est pas pris en charge, déposez un commentaire [ici](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formats audio dans des conteneurs d’entrée
Media Encoder Standard prend en charge la transmission des formats audio suivants dans des conteneurs d’entrée :

* Fichiers MXF, GXF et QuickTime contenant des pistes audio avec des échantillons Interleaved Stereo ou 5.1

or

* Fichiers MXF, GXF et QuickTime où l’audio est transmis sous forme de pistes PCM distinctes, mais où le mappage de canaux (vers la stéréo ou 5.1) peut être déduit des métadonnées du fichier

Le mappage de canaux explicite/fourni par l’utilisateur sera pris en charge dans un avenir proche.

## <a name="input-video-codecs"></a>Codecs vidéo d’entrée
| Codecs vidéo d’entrée | Prise en charge |
| --- | --- | --- | --- |
| AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2 |
| Avid DNxHD (dans MXF) |Oui |
| DVCPro/DVCProHD (dans MXF) |Oui |
| Vidéo numérique (dans les fichiers AVI) |Oui |
| JPEG 2000 |Oui |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Jusqu’à un profil de 422 |
| MPEG-1 |Oui |
| VC-1/WMV9 |Oui |
| Canopus HQ/HQX |Non  |
| MPEG-4 partie 2 |Oui |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Oui |
| YUV420 non compressé ou mezzanine |Oui |
| Apple ProRes 422 |Oui |
| Apple ProRes 422 LT |Oui |
| Apple ProRes 422 HQ |Oui |
| Apple ProRes Proxy |Oui |
| Apple ProRes 4444 |Oui |
| Apple ProRes 4444 XQ |Oui |

## <a name="input-audio-codecs"></a>Codecs audio d’entrée
| Codecs audio d’entrée | Prise en charge |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |Oui |
| MPEG Layer 2 |Oui |
| MP3 (MPEG-1 Audio Layer 3) |Oui |
| Windows Media Audio |Oui |
| WAV/PCM |Oui |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Oui |
| [Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Oui |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Oui |
| AMR (adaptive multi-rate) |Oui |
| AES (SMPTE 331M et 302M, AES3-2003) |Non  |
| Dolby® E |Non  |
| Dolby® Digital (AC3) |Non  |
| Dolby® Digital Plus (E-AC3) |Non  |

## <a name="output-formats-and-codecs"></a>Formats de sortie et codecs
Le tableau suivant répertorie les codecs et les formats de fichiers pris en charge pour l'exportation.

| Format de fichier | Codec vidéo | Codec audio |
| --- | --- | --- |
| MP4  <br/><br/>(y compris les conteneurs MP4 à vitesses de transmission multiples) |H.264 (profils High, Main et Baseline) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (profils High, Main et Baseline) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Voir aussi
[Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md)

[Encodage avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

