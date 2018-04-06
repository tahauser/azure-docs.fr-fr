---
title: Azure Media Services - Signalisation de métadonnées chronométrées dans une vidéo en flux continu | Documents Microsoft
description: Cette spécification présente deux modes pris en charge par Media Services pour la signalisation de métadonnées chronométrées à l’intérieur d’une vidéo en flux continu. Cela inclut la prise en charge de signaux génériques de métadonnées chronométrées, ainsi que la signalisation SCTE-35 pour l'insertion de jointures publicitaires.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: ae726b141f5f44b1eb0887cbd988881e41e163c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalisation de métadonnées chronométrées dans une vidéo en flux continu


## <a name="1-introduction"></a>1 Introduction 
Afin de faciliter l'insertion d’annonces ou d'événements personnalisés sur un lecteur de client, les diffuseurs utilisent souvent des métadonnées chronométrées incorporées dans la vidéo. Pour permettre ces scénarios, Media Services prend en charge le transport de métadonnées chronométrées avec le média, du point de réception du canal de diffusion en continu en direct à l'application cliente.
Cette spécification présente deux modes pris en charge par Media Services pour les métadonnées chronométrées à l’intérieur des signaux de diffusion en continu en direct :

1. la signalisation [SCTE-35] qui respecte les pratiques décrites par [SCTE-67] ;

2. un mode générique de signalisation des métadonnées chronométrées pour les messages dont le mode de signalisation n’est pas [SCTE-35].

### <a name="12-conformance-notation"></a>1.2 Note concernant la conformité
« DOIT », « NE DOIT PAS », « OBLIGATOIRE », « DEVRA », « NE DEVRA PAS », « DEVRAIT », « NE DEVRAIT PAS », « RECOMMANDÉ », « NON RECOMMANDÉE », « PEUT » et « OPTIONNELLE » figurant dans ce document doivent être interprétés de la manière décrite dans le document RFC 2119.

### <a name="13-terms-used"></a>1.3 Termes utilisés

| Terme              | Définition                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Heure de présentation | Heure à laquelle un événement est présenté à un spectateur. Il s’agit de l’heure dans la chronologie du média à laquelle un spectateur voit l’événement. Par exemple, l’heure de présentation d’un message de commande splice_info() SCTE-35 est splice_time(). |
| Heure d’arrivée      | Heure à laquelle un message d’événement arrive. L’heure d’arrivée est généralement distincte de l’heure de présentation de l’événement, dans la mesure où les événements sont envoyés avant l’heure de leur présentation.                                     |
| Piste partiellement allouée      | Piste de média qui n’est pas continue et qui est synchronisée avec une piste parente ou de contrôle.                                                                                                                                    |
| Origine            | Service de diffusion en continu de média Azure.                                                                                                                                                                                                |
| Récepteur de canal      | Service de diffusion en continu en direct Azure.                                                                                                                                                                                           |
| HLS               | Protocole de diffusion en continu HTTP Apple                                                                                                                                                                                               |
| DASH              | Diffusion en continu adaptative dynamique sur HTTP.                                                                                                                                                                                             |
| Lisse            | Protocole de diffusion en continu lisse.                                                                                                                                                                                                        |
| MPEG2-TS          | Flux de transport MPEG-2.                                                                                                                                                                                                         |
| RTMP              | Protocole multimédia en temps réel.                                                                                                                                                                                                    |
| uimsbf            | Entier non signé, avec le bit le plus significatif en tête.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 Réception de métadonnées chronométrées
## <a name="21-rtmp-ingest"></a>2.1 Réception RTMP
RTMP prend en charge les signaux de métadonnées chronométrées envoyés sous forme de messages de signal AMF incorporés dans le flux RTMP. Les messages de signal peuvent être envoyés avant que l’événement réel ou la jointure publicitaire doivent se produire. Pour prendre en charge ce scénario, l’heure réelle de la jointure ou du segment sont envoyées dans le message de signal. Pour plus d'informations, voir [AMF0].

Le tableau suivant décrit le format de la charge utile du message AMF que Media Services ingère.

Le nom du message AMF peut être utilisé pour différencier plusieurs flux d’événements du même type.  Pour les messages [SCTE-35], le nom du message AMF DOIT être « onAdCue », comme recommandé dans [SCTE-67].  Tous les champs non répertoriés ci-dessous DOIVENT être ignorés, afin que l’innovation de cette conception ne soit pas bloquée dans le futur.

### <a name="signal-syntax"></a>Syntaxe de signal
Pour le mode simple RTMP, Media Services prend en charge un message de signal AMF nommé « onAdCue » avec le format suivant :

### <a name="simple-mode"></a>Mode simple

| Nom du champ | Type de champ | Requis ? | Descriptions                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | Chaîne     | Obligatoire | Message de l'événement.  Doit être « SpliceOut » pour désigner une jointure en mode simple.                                              |
| id         | Chaîne     | Obligatoire | Identificateur unique décrivant la jointure ou le segment. Identifie cette instance du message.                            |
| duration   | Number     | Obligatoire | Durée de la jointure. Les unités sont des fractions de seconde.                                                                |
| elapsed    | Number     | Facultatif | Lorsque le signal est répété pour prendre en charge le réglage, ce champ est la quantité de temps de présentation qui s’est écoulée depuis le début de la jointure. Les unités sont des fractions de seconde. Lorsque vous utilisez le mode simple, cette valeur ne doit pas dépasser la durée originale de la jointure.                                                  |
| time       | Number     | Obligatoire | Doit être l’heure de la jointure, dans le temps de présentation. Les unités sont des fractions de seconde.                                     |

---------------------------

### <a name="scte-35-mode"></a>Mode SCTE-35

| Nom du champ | Type de champ | Requis ? | Descriptions                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | Chaîne     | Obligatoire | Message de l'événement.  Pour les messages [SCTE-35], il DOIT s’agir du binaire codé en Base64 (norme IETF RFC 4648) splice_info_section() de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [SCTE-67].                                              |
| Type       | Chaîne     | Obligatoire | URN ou URL identifiant le schéma de message. Par exemple, « urn:example:signaling:1.0 ».  Pour les messages [SCTE-35], il DOIT s’agir de « urn : scte:scte35:2013a:bin » de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [SCTE-67].  |
| id         | Chaîne     | Obligatoire | Identificateur unique décrivant la jointure ou le segment. Identifie cette instance du message.  Les messages dont la sémantique est identique auront la même valeur.|
| duration   | Number     | Obligatoire | Durée de l’événement ou du segment de jointure publicitaire, si elle est connue. Si elle est inconnue, la valeur doit être 0.                                                                 |
| elapsed    | Number     | Facultatif | Lorsque le signal publicitaire [SCTE-35] est répété pour le réglage, ce champ est la quantité de temps de présentation qui s’est écoulée depuis le début de la jointure. Les unités sont des fractions de seconde. En mode de [SCTE-35], cette valeur peut dépasser la durée originale spécifiée de la jointure ou du segment.                                                  |
| time       | Number     | Obligatoire | Heure de présentation de l’événement ou de la jointure publicitaire.  L’heure et la durée de présentation DOIVENT être en phase avec les points d’accès du flux (SAP) de type 1 ou 2, comme défini dans la norme [ISO-14496-12] Annexe I. Pour les sorties HLS, l’heure et la durée doivent correspondre aux limites de segment. L’heure et la durée de présentation de messages d’événements différents au sein du même flux d’événements NE DOIVENT PAS se chevaucher. Les unités sont des fractions de seconde.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 Annulation et mises à jour

Des messages peuvent être annulés ou mis à jour par l’envoi de plusieurs messages avec les mêmes heure et ID de présentation. L’heure et l’ID de présentation identifient de façon unique l’événement, et le dernier message reçu pour une heure de présentation spécifique qui correspond aux contraintes de décompte est le message traité. L’événement mis à jour remplace tout message reçu précédemment. La contrainte de décompte est de quatre secondes. Les messages reçus au moins quatre secondes avant l’heure de présentation seront traités.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Réception de MP4 fragmentée (diffusion en continu lisse)
Pour les exigences relatives à la réception de flux en direct, voir [LIVE-FMP4]. Les sections suivantes fournissent des détails sur la réception de métadonnées de présentation chronométrées.  Les métadonnées de présentation chronométrées sont reçues en tant que piste partiellement allouée, telle que définie dans la zone de manifeste du serveur en direct (voir MS-SSTR) et la zone de vidéo (« moov »).  Chaque fragment se compose d’une zone de fragment vidéo (« moof ») et d’une zone de données multimédias (« mdat »), où la zone « mdat » est le message binaire.

#### <a name="221-live-server-manifest-box"></a>2.2.1 Zone de manifeste du serveur en direct
La piste partiellement allouée DOIT être déclarée dans la zone de manifeste du serveur en direct avec une entrée \<textstream\>, et DOIT avoir l’ensemble d’attributs suivants :

| **Nom de l’attribut** | **Type de champ** | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Obligatoire      | DOIT être « 0 », indiquant une piste avec un débit binaire variable inconnu.                                                                                                                                                                                                 |
| parentTrackName    | Chaîne         | Obligatoire      | DOIT être le nom de la piste parent sur laquelle les codes temporels de la piste partiellement allouée sont alignés à l’échelle de temps. La piste parent ne peut pas être une piste partiellement allouée.                                                                                                                    |
| manifestOutput     | Booléen        | Obligatoire      | DOIT être « true » pour indiquer que la piste partiellement allouée sera incorporée dans le manifeste du client lisse.                                                                                                                                                               |
| Subtype            | Chaîne         | Obligatoire      | DOIT être le code « DATA » de quatre caractères.                                                                                                                                                                                                                         |
| Schéma             | Chaîne         | Obligatoire      | DOIT être un URN ou une URL identifiant le schéma de message ; par exemple, « urn:example:signaling:1.0 ». Pour les messages [SCTE-35], il DOIT s’agir de « urn : scte:scte35:2013a:bin » de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [SCTE-67]. |
| trackName          | Chaîne         | Obligatoire      | DOIT être le nom de la piste partiellement allouée. Le trackName peut servir à différencier plusieurs flux d’événements dont le schéma est identique. Chaque flux d’événements doit avoir un nom de piste unique.                                                                           |
| échelle de temps          | Number         | Facultatif      | DOIT être l’échelle de temps de la piste parent.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Zone de vidéo

La zone de vidéo (« moov ») suit la zone de manifeste du serveur en direct en tant que partie de l’en-tête de flux pour une piste partiellement allouée.

La zone « moov » doit contenir une zone **TrackHeaderBox (« tkhd »)**, telle que définie dans la norme [ISO-14496-12] avec les contraintes suivantes :

| **Nom du champ** | **Type de champ**          | **Obligatoire ?** | **Description**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | Entier non signé 64 bits | Obligatoire      | DOIT être 0, étant donné que la zone de piste contient zéro exemple et que la durée totale des exemples dans la zone de piste est 0. |
-------------------------------------

La zone « moov » DOIT contenir un **HandlerBox (« hdlr »)**, tel que défini dans la norme [ISO-14496-12] avec les contraintes suivantes :

| **Nom du champ** | **Type de champ**          | **Obligatoire ?** | **Description**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | Entier non signé 32 bits | Obligatoire      | DOIT être « meta ». |
-------------------------------------

La zone « stsd » doit contenir une zone de MetaDataSampleEntry avec un nom de codage tel que défini dans la norme [ISO-14496-12].  Par exemple, pour les messages SCTE-35, le nom de codage DOIT être « scte ».

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Zone de fragment vidéo et zone de données multimédias

Les fragments de piste partiellement allouée se composent d’une zone de fragment vidéo (« moof ») et d’une zone de données multimédias (« mdat »).

La zone MovieFragmentBox (« moof ») doit contenir une zone **TrackFragmentExtendedHeaderBox (« uuid »)** telle que définie dans [FMP4] avec les champs suivants :

| **Nom du champ**         | **Type de champ**          | **Obligatoire ?** | **Description**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | Entier non signé 64 bits | Obligatoire      | DOIT être l’heure d’arrivée de l’événement. Cette valeur aligne le message sur la piste parent.   |
| fragment_duration      | Entier non signé 64 bits | Obligatoire      | DOIT être la durée de l’événement. La durée peut être zéro pour indiquer que la durée est inconnue. |

------------------------------------


La zone de MediaDataBox (« mdat ») doit avoir le format suivant :

| **Nom du champ**          | **Type de champ**                   | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | Entier non signé 32 bits (uimsbf) | Obligatoire      | Détermine le format du contenu de la zone « mdat ». Les versions non reconnues seront ignorées. Actuellement, la seule version prise en charge est 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Entier non signé 32 bits (uimsbf) | Obligatoire      | Identifie cette instance du message. Des messages dont la sémantique est identique ont la même valeur ; le traitement de toute zone de message d’événement ayant le même ID est suffisant.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Entier non signé 32 bits (uimsbf) | Obligatoire      | La somme de fragment_absolute_time, spécifié dans TrackFragmentExtendedHeaderBox et le presentation_time_delta, DOIT être l’heure de présentation de l’événement. L’heure et la durée de présentation DOIVENT être en phase avec les points d’accès du flux (SAP) de type 1 ou 2, comme défini dans la norme [ISO-14496-12] Annexe I. Pour les sorties HLS, l’heure et la durée doivent correspondre aux limites de segment. L’heure et la durée de présentation de messages d’événements différents au sein du même flux d’événements NE DOIVENT PAS se chevaucher. |
| Message                 | tableau d’octets                       | Obligatoire      | Message de l'événement. Pour les messages [SCTE-35], le message est le binaire splice_info_section(), bien que la norme [SCTE-67] recommande autre chose. Pour les messages [SCTE-35], il DOIT s’agir de splice_info_section() de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [SCTE-67]. Pour les messages [SCTE-35], le binaire splice_info_section() est la charge utile de la zone « mdat », et il n’EST PAS codé en Base64.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 Annulation et mises à jour
Des messages peuvent être annulés ou mis à jour par l’envoi de plusieurs messages avec les mêmes heure et ID de présentation.  L’heure et l’ID de présentation identifient l’événement. Le dernier message reçu pour une heure de présentation spécifique qui répond aux contraintes de décompte est le message traité. Le message mis à jour remplace tous les messages reçus précédemment.  La contrainte de décompte est de quatre secondes. Les messages reçus au moins quatre secondes avant l’heure de présentation seront traités. 


## <a name="3-timed-metadata-delivery"></a>3 Remise de métadonnées chronométrées

Les données de flux de données d’événements sont opaques pour Media Services. Media Services transmet simplement trois éléments d’informations entre le point de terminaison de réception et le point de terminaison client. Les propriétés suivantes sont fournies au client conformément à la norme [SCTE-67] et/ou au protocole de diffusion en continu du client :

1.  Schéma : URN ou URL identifiant le schéma du message.

2.  Heure de présentation : heure de présentation de l’événement dans la chronologie du média.

3.  Durée : durée de l’événement.

4.  ID : identificateur unique facultatif pour l’événement.

5.  Message : données de l’événement.


## <a name="31-smooth-streaming-delivery"></a>3.1 Remise par diffusion en continu lisse

Reportez-vous aux détails de gestion de la piste partiellement allouée dans les spécifications [FMP4] et [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Exemple de manifeste de client lisse
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Remise HLS Apple
Les métadonnées chronométrées pour la diffusion en continu HTTP Apple (HLS) peuvent être incorporées dans la sélection de segments à l’intérieur d’une balise M3U personnalisée.  La couche application peut analyser la sélection M3U et traiter les balises M3U. Azure Media Services incorporera les métadonnées chronométrées dans la balise EXT-X-CUE définie dans [HLS].  La balise EXT-X-CUE est actuellement utilisée par DynaMux pour les messages de type ADI3.  Pour prendre en charge les messages SCTE-35 et non SCTE-35, définissez les attributs de la balise EXT-X-CUE comme ci-dessous :

| **Nom de l’attribut** | **Type**                      | **Obligatoire ?**                             | **Description**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | Chaîne entre guillemets.                 | Obligatoire                                  | Message encodé sous forme de chaîne en Base64, comme décrit dans la norme [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). Pour les messages [SCTE-35], il s’agit de la zone splice_info_section() codée en Base64.                                                                                                |
| TYPE               | Chaîne entre guillemets.                 | Obligatoire                                  | URN ou URL identifiant le schéma de message. Par exemple, « urn:example:signaling:1.0 ». Pour les messages [SCTE-35], le type prend la valeur spéciale « scte35 ».                                                                                                                                |
| ID                 | Chaîne entre guillemets.                 | Obligatoire                                  | Identificateur unique de l’événement. Si l’ID n’est pas spécifié lors de la réception du message, Azure Media Services génère un id unique.                                                                                                                                          |
| DURATION           | Nombre décimal à virgule flottante. | Obligatoire                                  | Durée de l’événement. Si elle est inconnue, la valeur doit être 0. Les unités sont des fractions de seconde.                                                                                                                                                                                           |
| ELAPSED            | Nombre décimal à virgule flottante. | Facultatif, mais requis pour la fenêtre glissante. | Lorsque le signal est répété pour prendre en charge une fenêtre glissante de présentation, ce champ DOIT être le temps de présentation qui s’est écoulé depuis le début de l’événement. Les unités sont des fractions de seconde. Cette valeur peut dépasser la durée originale spécifiée de la jointure ou du segment. |
| TEMPS               | Nombre décimal à virgule flottante. | Obligatoire                                  | Heure de présentation de l’événement. Les unités sont des fractions de seconde.                                                                                                                                                                                                                    |


La couche d’application du lecteur HLS utilisera le TYPE pour identifier le format du message, décoder celui-ci, appliquer les conversions de temps nécessaires, et traiter l’événement.  Les événements sont synchronisé dans la sélection de segments de la piste parent, en fonction de leur horodatage.  Ils sont insérés devant le segment le plus proche (balise #EXTINF).

#### <a name="hls-segment-playlist-example"></a>Exemple de sélection de segment HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>Gestion des messages HLS

Les événements sont signalés dans la sélection de segments de chaque piste audio et vidéo. La position de la balise EXT-X-CUE DOIT toujours être immédiatement devant le premier segment HLS (pour jointure externe ou début de segment) ou immédiatement derrière les derniers segment HLS (pour jointure interne ou fin du segment) auxquels ses attributs TIME et DURATION se rapportent, comme requis par [HLS].

Quand une fenêtre glissante de présentation est activée, la balise EXT-X-CUE doit être répétée assez souvent pour que la jointure ou le segment soient toujours entièrement décrits dans la sélection de segments, et l’attribut ELAPSED DOIT être utilisé pour indiquer la durée d’activité de la jointure ou du segment, comme requis par [HLS].

Quand une fenêtre glissante de présentation est activée, les balises EXT-X-CUE sont supprimées de la sélection de segments lorsque le temps de multimédia auquel elles font référence est en dehors de la fenêtre glissante de présentation.

## <a name="33--dash-delivery"></a>3.3 Remise DASH
[DASH] permet de signaler les événements de trois façons :

1.  Événements signalés dans le MPD
2.  Événements signalés dans la bande dans la représentation (en utilisant la zone de message d’événement ('emsg'))
3.  Une combinaison de 1 et 2

Les événements signalés dans le MPD sont utiles pour une diffusion en continu VOD, car les clients ont accès à tous les événements, dès que le MPD est téléchargé. La solution dans la bande est utile pour la diffusion en continu, car les clients n’ont pas besoin de re-télécharger le MPD. Pour la segmentation basée sur le temps, le client détermine l’URL du segment suivant en ajoutant la durée et l’horodateur du segment actuel. Si cette demande échoue, le client suppose une discontinuité et télécharge le MPD. Autrement, il continue la diffusion en continu sans télécharger le MPD.

Azure Media Services ne fera pas la signalisation dans le MPD et la signalisation dans la bande à l’aide de la zone de message de l’événement.

#### <a name="mpd-signaling"></a>Signalisation MDP

Les événements seront signalés dans le MPD à l’aide de l’élément EventStream qui apparaît dans l’élément Period.

L’élément EventStream a les attributs suivants :

| **Nom de l’attribut** | **Type**                | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | chaîne                  | Obligatoire      | Identifie le schéma du message. Le schéma est défini sur la valeur de l’attribut Scheme dans la zone de manifeste du serveur en direct. La valeur DOIT être un URN ou une URL identifiant le schéma de message ; par exemple, « urn:example:signaling:1.0 ».                                                                |
| value              | chaîne                  | Facultatif      | Valeur de chaîne supplémentaire utilisée par les propriétaires du schéma pour personnaliser la sémantique du message. Afin de différencier plusieurs flux d’événements dont le schéma est identique, la valeur DOIT être définie sur le nom du flux d’événements (trackName pour une réception lisse ou nom du message AMF pour une réception RTMP). |
| Échelle de temps          | Entier non signé 32 bits | Obligatoire      | Échelle de temps, en battements par seconde, des champs d’heures et de durée dans la zone « emsg ».                                                                                                                                                                                                       |


Zéro ou plusieurs éléments d’événement sont contenus dans l’élément EventStream, et leurs attributs sont les suivants :

| **Nom de l’attribut**  | **Type**                | **Obligatoire ?** | **Description**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | Entier non signé 64 bits | Facultatif      | DOIT être le temps de présentation multimédia de l’événement par rapport au début de la période. L’heure et la durée de présentation DOIVENT être en phase avec les points d’accès de flux (SAP) de type 1 ou 2, comme définis dans [ISO-14496-12] l’annexe I. |
| duration            | Entier non signé 32 bits | Facultatif      | Durée de l’événement. DOIT être omis si la durée est inconnue.                                                                                                                                                 |
| id                  | Entier non signé 32 bits | Facultatif      | Identifie cette instance du message. Les messages dont la sémantique est identique auront la même valeur. Si l’ID n’est pas spécifié lors de la réception du message, Azure Media Services génère un id unique.             |
| Valeur de l’élément événement | chaîne                  | Obligatoire      | Message d’événement sous forme de chaîne en Base64, comme décrit dans la norme [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Syntaxe et exemple XML pour la signalisation (MPD) de manifeste DASH

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>Notez que presentationTime est l’heure de présentation de l’événement, pas l’heure d’arrivée du message.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 Signalisation de zone de message d’événement dans la bande
Un flux d’événement dans la bande nécessite que le MPD ait un élément InbandEventStream au niveau défini de l’adaptation.  Cet élément a un attribut schemeIdUri obligatoire et un attribut d’échelle de temps facultatif, qui s’affichent également dans la zone de message d’événement (« emsg »).  Les zones de message d’événement avec des identificateurs de schéma qui ne sont pas définis dans le MPD ne devraient pas être présentes. Si un client DASH détecte une zone de message d’événement avec un schéma qui n’est pas défini dans le MPD, le client est supposé l’ignorer.
La zone de message d’événement (« emsg ») fournit une signalisation pour les événements génériques liés à l’heure de la présentation multimédia. Le cas échéant, une zone « emsg » sera placée devant une zone « moof ».

### <a name="dash-event-message-box-emsg"></a>Zone de message d’événement DASH « emsg »
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Les champs de la zone DASHEventMessageBox sont définis ci-dessous :

| **Nom du champ**          | **Type de champ**          | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | chaîne                  | Obligatoire      | Identifie le schéma du message. Le schéma est défini sur la valeur de l’attribut Scheme dans la zone de manifeste du serveur en direct. La valeur DOIT être un URN ou une URL identifiant le schéma de message ; par exemple, « urn:example:signaling:1.0 ». Pour les messages [SCTE-35], il s’agit de la valeur spéciale « urn:scte:scte35:2013a:bin », bien que [SCTE-67] recommande autre chose. |
| Valeur                   | chaîne                  | Obligatoire      | Valeur de chaîne supplémentaire utilisée par les propriétaires du schéma pour personnaliser la sémantique du message. Afin de différencier plusieurs flux d’événements dont le schéma est identique, la valeur sera définie sur le nom du flux d’événements (trackName pour une réception lisse ou nom du message AMF pour une réception RTMP).                                                                  |
| Échelle de temps               | Entier non signé 32 bits | Obligatoire      | Échelle de temps, en battements par seconde, des champs d’heures et de durée dans la zone « emsg ».                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | Entier non signé 32 bits | Obligatoire      | Delta de temps de présentation multimédia entre l’heure de la présentation de l’événement et l’heure de présentation la plus précoce dans ce segment. L’heure et la durée de présentation DOIVENT être en phase avec les points d’accès de flux (SAP) de type 1 ou 2, comme définis dans [ISO-14496-12] l’annexe I.                                                                                            |
| event_duration          | Entier non signé 32 bits | Obligatoire      | Durée de l’événement ou 0xFFFFFFFF pour indiquer une durée inconnue.                                                                                                                                                                                                                                                                                          |
| ID                      | Entier non signé 32 bits | Obligatoire      | Identifie cette instance du message. Les messages dont la sémantique est identique auront la même valeur. Si l’ID n’est pas spécifié lors de la réception du message, Azure Media Services génère un id unique.                                                                                                                                                    |
| Message_data            | tableau d’octets              | Obligatoire      | Message de l'événement. Pour les messages [SCTE-35], leurs données sont le binaire splice_info_section(), bien que la norme [SCTE-67] recommande autre chose.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 Gestion des messages DASH

Les événements sont signalés dans la bande, dans la zone « emsg », pour les pistes tant audio que vidéo.  La signalisation se produit pour toutes les demandes de segments pour lesquelles la valeur presentation_time_delta est inférieure ou égale à 15 secondes. Quand une fenêtre de présentation glissante est activée, les messages d’événement sont supprimés du MPD lorsque la somme du temps et la durée du message d’événement sont inférieures au temps des données multimédias dans le manifeste.  En d’autres termes, les messages d’événements sont supprimés du manifeste lorsque le temps multimédia auquel ils font référence sort de la fenêtre glissante de présentation.

## <a name="4-scte-35-ingest"></a>4 Réception SCTE-35

Les messages [SCTE-35] sont reçus dans un format binaire à l’aide du schéma **« urn:scte:scte35:2013a:bin »** pour la réception lisse et le type **« scte35 »** pour la réception RTMP. Pour faciliter la conversion d’une synchronisation [SCTE-35], qui est basée sur des horodatages de présentation de flux de transport MPEG-2 (PTS), un mappage entre PTS (pts_time + pts_adjustment de splice_time()) et la chronologie du média sont fournis par l’heure de présentation de l’événement (champ fragment_absolute_time pour la réception lisse et champ d’heure pour la réception RTMP). Le mappage est nécessaire, car la valeur PTS 33 bits change environ toutes les 26,5 heures.

La réception de diffusion en continu lisse nécessite que la zone de données multimédias (« mdat ») contienne la valeur **splice_info_section()** définie dans le tableau 8-1 de [SCTE-35]. Pour la réception RTMP, l’attribut cue du message AMF est défini sur le Base64encoded **splice_info_section()**. Lorsque les messages ont le format décrit ci-dessus, ils sont envoyés aux clients HLS, Lisse et Dash conformément à la norme [SCTE-67].


## <a name="5-references"></a>5 Références

**[SCTE-35]** ANSI/SCTE 35 2013a – Digital Program Insertion Cueing Message for Cable, 2013a

**[SCTE-67]** ANSI/SCTE 67 2014 –Recommended Practice for SCTE 35: Digital Program Insertion Cueing Message for Cable

**[DASH]** ISO/IEC 23009-1 2014 – Information technology – Dynamic adaptive streaming over HTTP (DASH) – Part 1: Media Presentation description and segment formats, 2° édition

**[HLS]** [« HTTP Live Streaming », draft-pantos-http-live-streaming-14, 14 octobre 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** [« Microsoft Smooth Streaming Protocol », 15 mai 2014](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]** [« Action Message Format AMF0 »](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[FMP4]** [IIS Smooth Streaming File/Wire Format Specification](https://microsoft.sharepoint.com/teams/mediaservices/_layouts/15/WopiFrame.aspx?sourcedoc=%7bAC5A31A4-E455-4000-96E1-AB17BD083144%7d&file=IIS%20Smooth%20Streaming%20File%20Format%20Specification%20-%20v%202%203%2001%20latest%20draft.docx&action=default)

**[LIVE-FMP4]** [Azure Media Services Fragmented MP4 Live Ingest Specification](https://microsoft.sharepoint.com/teams/mediaservices/_layouts/15/WopiFrame.aspx?sourcedoc=%7b5CEE1122-AA28-4368-BC8E-9C0048BF1529%7d&file=AMS%20F-MP4%20Live%20Ingest%20Specification.docx&action=default)

**[ISO-14496-12]** ISO/IEC 14496-12: Part 12 ISO base media file format, quatrième édition 15/07/2012.

**[RTMP]** [« Adobe’s Real-Time Messaging Protocol », 21 décembre 2012](http://wwwimages.adobe.com/www.adobe.com/content/dam/Adobe/en/devnet/rtmp/pdf/rtmp_specification_1.0.pdf) 

------------------------------------------

## <a name="next-steps"></a>Étapes suivantes
Afficher les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
