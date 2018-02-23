---
title: Codecs et formats de Media Encoder Premium Workflow | Microsoft Docs
description: "Cette rubrique offre une vue d’ensemble des codecs et formats de Media Encoder Premium Workflow"
services: media-services
documentationcenter: 
author: juliako
manager: erik43
editor: 
ms.assetid: b197fce8-3b9b-4189-8d08-486810c0426f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: eb1cadec240dc7f6e3ac5b8932d66c3d55c76e42
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Codecs et formats de Media Encoder Premium Workflow
> [!NOTE]
> Pour les questions relatives à Encoder Premium, envoyez un e-mail à mepd@microsoft.com.
> 
> Le processeur multimédia Media Encoder Premium Workflow présenté dans cette rubrique n’est pas disponible en Chine. 
> 
> 

Ce document contient la liste des codecs et formats de fichiers d’entrée et de sortie pris en charge par la version préliminaire publique de l’encodeur **Media Encoder Premium Workflow** .

[Codecs et formats d’entrée de Media Encoder Premium Workflow](#input_formats)

[Codecs et formats de sortie de Media Encoder Premium Workflow](#output_formats)

**Media Encoder Premium Workflow** prend en charge le sous-titrage décrit dans [cette](#closed_captioning) section 

## <a id="input_formats"></a>Codecs et formats d’entrée de Media Encoder Premium Workflow
La section suivante répertorie les codecs et les formats de fichiers que ce processeur multimédia prend en charge en entrée.

### <a name="input-containerfile-formats"></a>Formats de conteneurs/fichiers d’entrée
* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2 Transport Streams
* MPEG-2 Program Streams
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8 bits/10 bits non compressé)

### <a name="input-video-codecs"></a>Codecs vidéo d’entrée
* AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra
* Avid DNxHD (dans MXF)
* DVCPro/DVCProHD (dans MXF)
* JPEG2000
* MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codecs audio d’entrée
* AES (SMPTE 331M et 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Codecs et formats de sortie de Media Encoder Premium Workflow
La section suivante répertorie les codecs et les formats de fichiers pris en charge en sortie avec ce processeur multimédia.

### <a name="output-containerfile-formats"></a>Formats de conteneurs/fichiers de sortie
* Adobe® Flash® F4V
* MXF (OP1a, XDCAM et AS02)
* DPP (y compris AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8 bits/10 bits non compressé)
* Format de fichier Smooth Streaming (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Codecs vidéo de sortie
* AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra)
* Avid DNxHD (dans MXF)
* DVCPro/DVCProHD (dans MXF)
* MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
* MPEG-1
* Windows Media Video/VC-1
* Création de miniatures JPEG

### <a name="output-audio-codecs"></a>Codecs audio de sortie
* AES (SMPTE 331M et 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) jusqu'à 7.1
* AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Si vous encodez en Dolby® Digital (AC3), la sortie peut uniquement être écrite dans un fichier MP4 ISO.

## <a id="closed_captioning"></a>Prise en charge du sous-titrage
En entrée, **Media Encoder Premium Workflow** prend en charge :

1. Fichiers sous contrôle de code source (SCC)
2. Fichiers SMPTE-TT
3. CEA-608/CEA-708 : transporté comme données utilisateur (messages SEI de flux élémentaires H.264, ATSC/53, SCTE20) ou comme données connexes dans des fichiers MXF/GXF
4. Fichiers de sous-titres STL

En sortie, les options suivantes sont disponibles :

1. conversion de CEA-608 vers CEA-708
2. Transition CEA-608/CEA-708 (incorporé dans des messages SEI de flux élémentaires H.264 ou transporté comme données connexes dans des fichiers MXF)
3. SCC
4. SMPTE Timed Text (de la source CEA-608 par SMPTE RP2052 ; création de fichiers DFXP incluse)
5. Fichier de sous-titre SRT
6. Flux de sous-titres DVB

Remarque : tous les formats de sortie mentionnés ci-dessus ne sont pas pris en charge dans le cadre d'une remise diffusée en continu dans Azure Media Services.

## <a name="known-issues"></a>Problèmes connus
Si votre vidéo d’entrée ne contient pas de sous-titres, l’élément multimédia de sortie actif comportera toujours un fichier TTML vide. 

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

