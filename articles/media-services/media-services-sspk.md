---
title: "Licence du kit de portage du client de diffusion en continu lisse Microsoft®"
description: "En savoir plus sur la licence du kit de portage Smooth Streaming client Microsoft®."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: xpouyat
ms.openlocfilehash: 87a5a1981b05722f25a70fcb73a06db65bcbe0fd
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licence du kit de portage du client de diffusion en continu lisse Microsoft®
## <a name="overview"></a>Vue d'ensemble
Le kit de portage client Smooth Streaming Microsoft (**SSPK** en abrégé) est une implémentation du client Smooth Streaming optimisée pour aider les fabricants de périphériques intégrés, les opérateurs mobiles et du câble, les fournisseurs de services de contenu, les fabricants de combinés, les éditeurs de logiciels indépendants (ISV) et les fournisseurs de solutions à créer des produits et des services pour diffuser du contenu adaptatif dans un format Smooth Streaming. SSPK est une implémentation Smooth Streaming cliente indépendante de l’appareil et de la plateforme pouvant être transférée par le titulaire de la licence vers n’importe quel appareil ou plateforme. 

Vous trouverez ci-après une architecture de haut niveau, et le package du kit de portage Smooth Streaming IIS est l’implémentation de client Smooth Streaming fournie par Microsoft. Il inclut la logique de base pour une lecture de contenu Smooth Streaming. Ce contenu est ensuite transféré par des partenaires pour un appareil ou une plateforme spécifique, grâce à l’implémentation des interfaces appropriées. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
SSPK est concédé sous licence selon des conditions représentant une excellente valeur pour l’entreprise. La licence SSPK offre au secteur :

* Source de kit de portage Smooth Streaming en C++ 
  * implémente la fonctionnalité Client Smooth Streaming
  * ajoute une analyse de format, heuristique, une logique de mise en mémoire tampon, etc.
* API d’application joueur 
  * interfaces de programmation pour une interaction avec une application de lecteur multimédia
* Interface de couche d’abstraction de plateforme (PAL) 
  * interfaces de programmation pour l‘interaction avec le système d‘exploitation (threads, sockets)
* Interface de couche d’abstraction matérielle 
  * interfaces de programmation pour l‘interaction avec les décodeurs A/V matériels (décodage, rendu)
* Interface de gestion de droits numériques (Digital Rights Management - DRM) 
  * interfaces de programmation pour la gestion des DRM via la couche Abstraction DRM (DAL)
  * Le kit de portage PlayReady Microsoft est fourni séparément, mais s’intègre via cette interface. Pour plus d‘informations sur les licences pour appareil Microsoft PlayReady cliquez [ici](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Échantillons de l’implémentation 
  * exemple d‘implémentation PAL pour Linux
  * exemple d‘implémentation HAL pour GStreamer

## <a name="licensing-options"></a>Options de licence
Le kit de portage Smooth Streaming client est accessible aux détenteurs de licences sous forme de deux contrats de licence distincts : l’un correspondant au développement des produits intermédiaires Smooth Streaming client, et l’autre pour la distribution de produits Smooth Streaming finaux pour les clients finaux.

* Pour les fabricants de puces, les intégrateurs système ou les fournisseurs de logiciels indépendants (ISV) qui ont besoin d‘un kit de portage de code source pour développer des produits intermédiaires, un kit de portage client Smooth Streaming Microsoft, **Licence produit intermédiaire** doit être exécutée.
* Pour les fabricants de périphériques ou les éditeurs de logiciels qui exigent des droits de distribution pour les produits de Smooth Streaming pour client final, c’est le kit de portage client Smooth Streaming **licence du produit Final** qui doit être exécuté.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Licence de produit intermédiaire pour le kit Microsoft Smooth Streaming Client Porting
Sous cette licence, Microsoft propose un kit de portage Smooth Streaming client et les droits de propriété intellectuelle nécessaires pour développer et distribuer les produits intermédiaires de client de Smooth Streaming pour les autres titulaires de licences de kit de portage Smooth Streaming client les produits finaux client de Smooth Streaming.

#### <a name="fee-structure"></a>Structure des frais
Des frais de licence de 50 000 dollars US payables en une fois offrent un accès au kit de portage Smooth Streaming client. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licence de produit final pour le kit de portage Smooth Streaming client
Avec cette licence, Microsoft offre les droits de propriété intellectuelle nécessaires pour recevoir les produits intermédiaires de client de Smooth Streaming de la part d’autres titulaires de licences de kit de portage Smooth Streaming client et distribuer des produits Smooth Streaming à la marque de la société en continu lisse pour les clients finaux.

#### <a name="fee-structure"></a>Structure des frais
Le produit client final de Smooth Streaming est proposé selon un modèle soumis à redevance :

* 0,10 $ par implémentation de périphérique expédiés
* La redevance est limitée à 50 000 dollars chaque année
* Pas de redevance pour les 10 000 premières implémentations de périphérique chaque année 

## <a name="licensing-procedure-and-sspk-access"></a>Procédure d’achat de licence et accès à SSPK
Veuillez envoyer un e-mail à [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pour toute requête relative aux licences.

Le [portail de distribution SSPK Distribution](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) est accessible aux détenteurs de licences intermédiaires enregistrés.

Les titulaires de licence SSPK intermédiaire ou finale peuvent soumettre des questions techniques à [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Les titulaires de licence d’accord de produit intermédiaire de client de diffusion lisse Microsoft
* Adroit Business Solutions, Inc
* Advanced Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Purchasing Corporation
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* iWEDIA S.A. 
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL Technology Electronics (Huizhou) Co., Ltd.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Titulaires de licence d’accord de produit final de client de diffusion lisse Microsoft
* Advanced Digital Broadcast SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Purchasing Corporation
* Enseo, Inc.
* Filmflex Movies Limited
* Fluendo S.A.
* Gibson Innovations Limited
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Orange SA
* Saffron Digital Limited
* Sagemcom Broadband SAS
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* TCL Overseas Marketing (Macao Commercial Offshore) Limited
* Technicolor Delivery Technologies, SAS
* Tongfang Global Ltd.
* Top Victory Investments, Ltd.
* Toshiba Lifestyle Products & Services Corporation
* Universal Media Corporation /Slovaquie/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

