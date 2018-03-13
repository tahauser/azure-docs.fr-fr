---
title: "Vue d’ensemble des points de terminaison de streaming Azure Media Services | Microsoft Docs"
description: "Cette rubrique fournit une vue d’ensemble des points de terminaison de streaming Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: cfowler
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 80d625a6ab2d3c6d5de0a90fbff0760888154d70
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="streaming-endpoints-overview"></a>Vue d’ensemble des points de terminaison de streaming 

##<a name="overview"></a>Vue d'ensemble

Dans Microsoft Azure Media Services (AMS), un **point de terminaison de diffusion en continu** représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de diffusion de contenu (CDN) pour être redistribué. Media Services fournit également une intégration transparente au CDN Azure. Le flux sortant d’un service StreamingEndpoint peut être un flux dynamique, une vidéo à la demande ou un téléchargement progressif de votre ressource dans votre compte Media Services. Chaque compte Azure Media Services comprend une valeur de point de terminaison de streaming par défaut. Vous pouvez créer d’autres points de terminaison de streaming sous votre compte. Il existe deux versions du point de terminaison de streaming : 1.0 et 2.0. À compter du 10 janvier 2017, les nouveaux comptes AMS incluront la version 2.0 du point de terminaison de streaming **par défaut**. Les autres points de terminaison que vous ajoutez à ce compte seront également de la version 2.0. Cette modification n’aura aucune incidence sur les comptes existants ; les StreamingEndpoint existants auront la version 1.0 et pourront être mis à niveau vers la version 2.0. Avec cette modification, il y aura des changements de comportement, de facturation et de fonctionnalités (pour plus d’informations, consultez la section **Types et versions de streaming** ci-dessous).

En outre, à partir de la version 2.15 (publiée en janvier 2017), Azure Media Services a ajouté les propriétés suivantes à l’entité du point de terminaison de streaming : **CdnProvider**, **CdnProfile**, **FreeTrialEndTime** et **StreamingEndpointVersion**. Pour une présentation détaillée de ces propriétés, consultez [ceci](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Lorsque vous créez un compte Azure Media Services, un point de terminaison de streaming par défaut est créé pour vous dans l’état **Arrêté**. Il n’est pas possible de supprimer le point de terminaison de streaming par défaut. En fonction de la disponibilité d’Azure CDN dans la région ciblée, le point de terminaison de streaming par défaut qui vient d’être créé comprendra également l’intégration du fournisseur CDN « StandardVerizon ». 

>[!NOTE]
>L’intégration d’Azure CDN peut être désactivée avant de démarrer le point de terminaison de streaming.

Cette rubrique donne une vue d’ensemble des fonctionnalités principales offertes par les points de terminaison de streaming.

## <a name="streaming-types-and-versions"></a>Types et versions de streaming

### <a name="standardpremium-types-version-20"></a>Types Standard/Premium (version 2.0)

À compter de la version publiée en janvier 2017 de Media Services, il existe deux types de streaming : **Standard** et **Premium**. Ces types font partie de la version de la version « 2.0 » du point de terminaison de streaming.

type|Description
---|---
**Standard**|Il s’agit de l’option par défaut, prévue pour fonctionner dans la majorité des scénarios.<br/>Avec cette option, vous obtenez un contrat de niveau de service limité/fixe. Les 15 premiers jours après le démarrage du point de terminaison sont gratuits.<br/>Si vous créez plusieurs points de terminaison de streaming, seul le premier est gratuit pendant les 15 premiers jours. Les autres sont facturés dès que vous les démarrez. <br/>Notez que la version d’évaluation gratuite s’applique uniquement aux comptes Media Services nouvellement créés et aux points de terminaison de streaming par défaut. Les points de terminaison de streaming existants et ceux créés en plus ne comprennent pas la période d’évaluation gratuite, qu’ils soient mis à niveau vers la version 2.0 ou créés directement en tant que version 2.0.
**Premium**|Cette option convient aux scénarios professionnels qui nécessitent plus de mise à l’échelle ou de contrôle.<br/>Contrat de niveau de service variable basé sur la capacité d’unité de streaming Premium (SU) achetée et les points de terminaison de streaming en service dans un environnement isolé et n’étant pas en concurrence pour les ressources.

Pour plus d’informations, consultez la section **Comparaison des types de streaming** qui suit.

### <a name="classic-type-version-10"></a>Type classique (version 1.0)

Pour les utilisateurs qui ont créé des comptes AMS avant la version du 10 janvier 2017, vous avez un type **classique** de point de terminaison de streaming. Ce type fait partie de la version de la version « 1.0 » du point de terminaison de streaming.

Si votre point de terminaison de streaming **version « 1.0 »** continu a 1 ou plusieurs unités de streaming (SU) Premium, le point de terminaison sera Premium et fournira toutes les fonctionnalités AMS (tout comme le **Standard/Premium**) sans aucune étape de configuration supplémentaire.

>[!NOTE]
>Les points de terminaison de streaming de type **Classique** continu (version « 1.0 » et 0 SU), fournissent des fonctionnalités limitées et n’incluent pas de contrat de niveau de service. Il est recommandé de migrer vers le type **Standard** pour obtenir une meilleure expérience et utiliser des fonctionnalités telles que l’empaquetage dynamique ou le chiffrement et d’autres fonctionnalités qui accompagnent le type **Standard**. Pour migrer vers le type **Standard**, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez **Opt-in to Standard** (Abonnement Standard). Pour plus d’informations sur la migration, consultez la section [Migration](#migration-between-types).
>
>Faites attention, car cette opération ne peut pas être restaurée et a un impact sur la tarification.
>
 
## <a name="comparing-streaming-types"></a>Comparaison des types de streaming

### <a name="versions"></a>Versions

|type|StreamingEndpointVersion|ScaleUnits|CDN|Facturation|Contrat SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Classique|1.0|0|N/D|Gratuit|N/D|
|Point de terminaison de streaming Standard|2.0|0|OUI|Payant|OUI|
|Unités de streaming Premium|1.0|>0|OUI|Payant|OUI|
|Unités de streaming Premium|2.0|>0|OUI|Payant|OUI|

### <a name="features"></a>Caractéristiques

Fonctionnalité|standard|Premium
---|---|---
Gratuit les 15 premiers jours| OUI |Non 
Throughput |Jusqu'à 600 Mbits/s lorsqu’Azure CDN n’est pas utilisé. Mis à l’échelle avec CDN.|200 Mbits/s par unité de streaming (SU). Mis à l’échelle avec CDN.
Contrat SLA | 99.9|99,9 (200 Mbits/s par SU).
CDN|Azure CDN, CDN tiers ou sans CDN.|Azure CDN, CDN tiers ou sans CDN.
La facturation est calculée sur la base d'un taux| Quotidien|Quotidien
Chiffrement dynamique|OUI|OUI
l’empaquetage dynamique|OUI|OUI
Scale|Mise à l’échelle automatique vers le débit cible.|Unités de diffusion en continu supplémentaires
Hôte de filtrage d’IP/G20/personnalisé|OUI|OUI
Téléchargement progressif|OUI|OUI
Utilisation recommandée |Recommandé pour la plupart des scénarios de streaming.|Utilisation professionnelle.<br/>Si vous pensez que vos besoins dépassent ce qu’offre l’abonnement Standard. Contactez-nous (amsstreaming@microsoft.com) si vous prévoyez une taille d’audience simultanée supérieure à 50 000 utilisateurs.


## <a name="migration-between-types"></a>Migration entre les types

À partir | À | Action
---|---|---
Classique|standard|Abonnement nécessaire
Classique|Premium| Échelle (unités de diffusion en continu supplémentaires)
Standard/Premium|Classique|Non disponible (si la version du point de terminaison de streaming est 1.0. Il est possible de passer en mode classique en définissant ScaleUnits sur « 0 »)
Standard (avec/sans CDN)|Premium avec les mêmes configurations|Autorisé dans l’état **démarré**. (via le portail Azure)
Premium (avec/sans CDN)|Standard avec les mêmes configurations|Autorisé dans l’état **démarré** (via le portail Azure)
Standard (avec/sans CDN)|Premium avec une configuration différente|Autorisé dans l’état **arrêté** (via le portail Azure). Non autorisé en état En cours d’exécution.
Premium (avec/sans CDN)|Standard avec une configuration différente|Autorisé dans l’état **arrêté** (via le portail Azure). Non autorisé en état En cours d’exécution.
Version 1.0 avec SU > = 1 avec CDN|Standard/Premium avec aucun CDN|Autorisé dans l’état **arrêté**. Non autorisé en état **démarré**.
Version 1.0 avec SU > = 1 avec CDN|Standard, avec/sans CDN|Autorisé dans l’état **arrêté**. Non autorisé en état **démarré**. Le CDN version 1.0 sera supprimé et le nouveau créé et démarré.
Version 1.0 avec SU > = 1 avec CDN|Premium avec/sans CDN|Autorisé dans l’état **arrêté**. Non autorisé en état **démarré**. Le CDN classique sera supprimé et le nouveau créé et démarré.

## <a name="next-steps"></a>étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

