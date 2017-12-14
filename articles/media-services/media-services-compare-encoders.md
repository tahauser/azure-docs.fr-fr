---
title: "Comparaison d’encodeurs multimédia à la demande Azure | Microsoft Docs"
description: "Cette section compare les fonctionnalités d’encodage de **Media Encoder Standard** et de **Media Encoder Premium Workflow**."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 72b4a7b746d446e47b52cf34726a50dd52eaba97
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparaison d’encodeurs multimédia à la demande Azure

Cette rubrique compare les fonctionnalités d’encodage de **Media Encoder Standard** et de **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Fonctionnalités de traitement vidéo et audio

Le tableau suivant compare les fonctionnalités de Media Encoder Standard (MES) et Media Encoder Premium Workflow (MEPW). 

|Fonctionnalité|Media Encoder Standard|Media Encoder Premium Workflow|
|---|---|---|
|Appliquer une logique conditionnelle lors de l’encodage<br/>(par exemple, si l’entrée est en HD, encodez le type audio 5.1)|Non|Oui|
|Sous-titres|Non|[Oui](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correction professionnelle du niveau sonore Dolby®](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> avec Dialogue Intelligence™|Non|Oui|
|Désentrelacement, inverse telecine|De base|Qualité de diffusion|
|Détection et suppression des bordures noires <br/>(pillar box, cadres)|Non|Oui|
|Génération de miniatures|[Oui](media-services-dotnet-generate-thumbnail-with-mes.md)|[Oui](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Détourage/rognage et panorama de vidéos|[Oui](media-services-advanced-encoding-with-mes.md#trim_video)|Oui|
|Superpositions d’audio ou de vidéo|[Oui](media-services-advanced-encoding-with-mes.md#overlay)|[Oui](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Superpositions d’images|À partir d’images sources|À partir d’images et de textes sources|
|Plusieurs pistes de langue audio|Limité|[Oui](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Compteur de facturation utilisé par chaque encodeur
| Nom du processeur multimédia | Tarification applicable | Remarques |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODEUR |Les tâches d’encodage seront facturées en fonction de la durée totale, en minutes, de tous les fichiers multimédias produits, au débit spécifié [ici][1], sous la colonne ENCODEUR. |
| **Media Encoder Premium Workflow** |ENCODEUR PREMIUM |Les tâches d’encodage seront facturées en fonction de la durée totale, en minutes, de tous les fichiers multimédias produits, au débit spécifié [ici][1], sous la colonne ENCODEUR PREMIUM. |

## <a name="input-containerfile-formats"></a>Formats de conteneurs/fichiers d’entrée
| Formats de conteneurs/fichiers d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Oui |Oui |
| MXF/SMPTE 377M |Oui |Oui |
| GXF |Oui |Oui |
| MPEG-2 Transport Streams |Oui |Oui |
| MPEG-2 Program Streams |Oui |Oui |
| MPEG-4/MP4 |Oui |Oui |
| Windows Media/ASF |Oui |Oui |
| AVI (8 bits/10 bits non compressé) |Oui |Oui |
| 3GPP/3GPP2 |Oui |Non |
| Format de fichier Smooth Streaming (PIFF 1.3) |Oui |Non |
| [Enregistrement vidéo numérique Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Oui |Non |
| Matroska/WebM |Oui |Non |
| QuickTime (.mov) |Oui |Non |

## <a name="input-video-codecs"></a>Codecs vidéo d’entrée
| Codecs vidéo d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2 |Oui |
| Avid DNxHD (dans MXF) |Oui |Oui |
| DVCPro/DVCProHD (dans MXF) |Oui |Oui |
| JPEG2000 |Oui |Oui |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Jusqu’à un profil de 422 |Oui |
| MPEG-1 |Oui |Oui |
| Windows Media Video/VC-1 |Oui |Oui |
| Canopus HQ/HQX |Non |Non |
| MPEG-4 partie 2 |Oui |Non |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Oui |Non |
| Apple ProRes 422 |Oui |Non |
| Apple ProRes 422 LT |Oui |Non |
| Apple ProRes 422 HQ |Oui |Non |
| Apple ProRes Proxy |Oui |Non |
| Apple ProRes 4444 |Oui |Non |
| Apple ProRes 4444 XQ |Oui |Non |

## <a name="input-audio-codecs"></a>Codecs audio d’entrée
| Codecs audio d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M et 302M, AES3-2003) |Non |Oui |
| Dolby® E |Non |Oui |
| Dolby® Digital (AC3) |Non |Oui |
| Dolby® Digital Plus (E-AC3) |Non |Oui |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |Oui |Oui |
| MPEG Layer 2 |Oui |Oui |
| MP3 (MPEG-1 Audio Layer 3) |Oui |Oui |
| Windows Media Audio |Oui |Oui |
| WAV/PCM |Oui |Oui |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Oui |Non |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Oui |Non |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Oui |Non |

## <a name="output-containerfile-formats"></a>Formats de conteneurs/fichiers de sortie
| Formats de conteneurs/fichiers de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Non |Oui |
| MXF (OP1a, XDCAM et AS02) |Non |Oui |
| DPP (y compris AS11) |Non |Oui |
| GXF |Non |Oui |
| MPEG-4/MP4 |Oui |Oui |
| MPEG-TS |Oui |Oui |
| Windows Media/ASF |Non |Oui |
| AVI (8 bits/10 bits non compressé) |Non |Oui |
| Format de fichier Smooth Streaming (PIFF 1.3) |Non |Oui |

## <a name="output-video-codecs"></a>Codecs vidéo de sortie
| Codecs vidéo de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra) |Uniquement 8 bits 4:2:0 |Oui |
| HEVC (H.265 ; 8 bits et 10 bits ;)  |Non |Oui |
| Avid DNxHD (dans MXF) |Non |Oui |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Non |Oui |
| MPEG-1 |Non |Oui |
| Windows Media Video/VC-1 |Non |Oui |
| Création de miniatures JPEG |Oui |Oui |
| Création de miniatures PNG |Oui |Oui |
| Création de miniatures BMP |Oui |Non |

## <a name="output-audio-codecs"></a>Codecs audio de sortie
| Codecs audio de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M et 302M, AES3-2003) |Non |Oui |
| Dolby® Digital (AC3) |Non |Oui |
| Dolby® Digital Plus (E-AC3) jusqu'à 7.1 |Non |Oui |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |Oui |Oui |
| MPEG Layer 2 |Non |Oui |
| MP3 (MPEG-1 Audio Layer 3) |Non |Oui |
| Windows Media Audio |Non |Oui |

>[!NOTE]
>Si vous encodez en Dolby® Digital (AC3), la sortie peut uniquement être écrite dans un fichier MP4 ISO.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articles connexes
* [Exécution de tâches d’encodage avancées via la personnalisation des présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas et limitations](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
