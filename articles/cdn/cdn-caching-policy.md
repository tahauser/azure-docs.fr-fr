---
title: "Gérer la stratégie de mise en cache CDN dans Azure Media Services | Microsoft Docs"
description: "Découvrez comment gérer la stratégie de mise en cache CDN dans Azure Media Services."
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: bcb5a6e118c779e7e13ba8472d5bba510cddeb12
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gérer la stratégie de mise en cache CDN dans Azure Media Services
Azure Media Services fournit la diffusion en continu adaptative HTTP et le téléchargement progressif. La diffusion en continu HTTP est hautement évolutive de par les avantages de mise en cache dans le proxy et les couches CDN, et la mise en cache côté client. Les points de terminaison de la diffusion en continu fournissent des fonctionnalités générales de diffusion en continu et de configuration pour les en-têtes HTTP du cache. Les points de terminaison de diffusion en continu définissent le contrôle de cache HTTP : les en-têtes max-age et Expires. Pour plus d'informations sur les en-têtes de cache HTTP, rendez-vous sur le site [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>En-têtes de mise en cache par défaut
Les points de terminaison de diffusion en continu appliquent par défaut des en-têtes de cache de 3 jours pour les données de diffusion en continu à la demande (fragments/segments de médias réels) et les fichiers manifestes (ou sélections). Pour la diffusion en continu en direct, les points de terminaison de diffusion en continu appliquent des en-têtes de cache de 3 jours pour les données (fragments/segments de médias réels) et une en-tête de cache de 2 secondes pour les requêtes de fichier manifeste (ou sélections). Lorsque le programme en direct opte pour une diffusion en continu à la demande (archive en direct), les en-têtes de cache de diffusion en continu à la demande s’appliquent.

## <a name="azure-cdn-integration"></a>Intégration d’Azure CDN
Azure Media Services propose un [CDN intégré](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) pour les points de terminaison de diffusion en continu. Les en-têtes de contrôle du cache s'applique aux points de terminaison de diffusion en continu de la même façon qu’aux points de terminaison de diffusion en continu fonctionnant avec CDN. Azure CDN utilise les valeurs configurées de cache de points de terminaison de diffusion en continu pour définir la durée de vie des objets mis en cache en interne, et il utilise également cette valeur pour définir les en-têtes de cache de distribution. Lors de l'utilisation des points de terminaison de diffusion en continu fonctionnant avec CDN, il est déconseillé de définir des petites valeurs de cache Définir de petites valeurs diminue les performances et réduit le profit du CDN. Il est interdit de définir des en-têtes de cache inférieure à 600 secondes pour les points de terminaison de diffusion en continu fonctionnant avec CDN.

> [!IMPORTANT]
>Azure Media Services est complètement intégré à Azure CDN. En un seul clic, vous pouvez intégrer tous les fournisseurs Azure CDN disponibles (Akamai et Verizon) à votre point de terminaison de streaming, y compris les produits CDN Standard et Premium. Pour plus d’informations, consultez cette [annonce](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Les frais liés au transfert des données à partir d’un point de terminaison de streaming vers CDN sont désactivés uniquement si le CDN est activé sur les API de point de terminaison de streaming ou s’il utilise la section de point de terminaison de streaming du portail Azure. L’intégration manuelle ou la création directe d’un point de terminaison CDN à l’aide des API du CDN ou de la section du portail ne désactivent pas les frais de données.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configuration des en-têtes de cache avec Azure Media Services
Vous pouvez utiliser le portail Azure ou des API d’Azure Media Services pour configurer les valeurs des en-têtes de cache.

1. Pour configurer les en-têtes de cache à l'aide du portail Azure, reportez-vous à la section [Gestion des points de terminaison de diffusion en continu](../media-services/media-services-portal-manage-streaming-endpoints.md) de la page relative à la configuration du point de terminaison de diffusion en continu.
2. API REST d'Azure Media Services, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Kit de développement logiciel (SDK) .NET Azure Media Services, [Propriétés StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordre de priorité de la configuration du cache
1. La valeur configurée du cache Azure Media Services remplace la valeur par défaut.
2. S'il n'existe aucune configuration manuelle, les valeurs par défaut s'appliquent.
3. Des en-têtes de cache de 2 secondes s’appliquent par défaut aux fichiers manifestes de diffusion en continu en direct (ou sélections) quelle que soit la configuration d'Azure Media ou d’Azure Storage, et elles sont irremplaçables.

