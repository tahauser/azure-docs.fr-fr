---
title: "Vue d’ensemble d’Azure CDN | Microsoft Docs"
description: "Découvrez le réseau de distribution de contenu (CDN) Azure et comment l'utiliser pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/10/2017
ms.author: v-semcev
ms.openlocfilehash: cdcf07b6af2bd915345361c0bda2dcd9abe5486e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="overview-of-the-azure-content-delivery-network"></a>Vue d'ensemble d'Azure Content Delivery Network

Le réseau de distribution de contenu (CDN) Azure met en cache le contenu web statique à des emplacements stratégiques afin de fournir un débit maximal pour la distribution sécurisée de contenu aux utilisateurs. Le CDN offre aux développeurs une solution globale pour la distribution rapide de contenu haut débit en mettant en cache le contenu sur des nœuds physiques dans le monde entier. 

> [!NOTE]
> Cet article décrit le réseau de distribution de contenu Azure, son fonctionnement et les fonctionnalités de chaque produit Azure CDN. Pour passer ces informations et suivre un didacticiel sur la création d’un point de terminaison de réseau de distribution de contenu, [Prise en main d’Azure CDN](cdn-create-new-endpoint.md). Pour obtenir la liste actuelle des emplacements de nœuds CDN, consultez la page [Emplacements des points de présence CDN Azure](cdn-pop-locations.md).

Les avantages de l’utilisation d’un CDN pour mettre en cache les ressources de site web incluent :

* De meilleures performances et une expérience enrichie pour les utilisateurs finaux, en particulier ceux qui utilisent des applications ayant recours à de nombreux allers-retours pour charger le contenu.
* Une mise à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple le début de l’événement de lancement d’un produit.
* La distribution des requêtes utilisateur et la diffusion de contenu directement depuis des serveurs Edge sont là pour que le trafic transmis à l’origine soit moins important.

## <a name="how-it-works"></a>Fonctionnement
![Présentation du CDN](./media/cdn-overview/cdn-overview.png)

1. Un utilisateur (Alice) demande un fichier (également appelé ressource) à l’aide d’une URL avec un nom de domaine spécial, par exemple `<endpointname>.azureedge.net`. Le DNS achemine la requête vers l’emplacement du meilleur point de présence (POP), généralement le POP géographiquement le plus proche de l’utilisateur.
2. Si les serveurs Edge du point de présence ne disposent pas du fichier dans leur cache, le serveur Edge demande le fichier à l'origine.  L'origine peut être une application web Azure, un service cloud Azure, un compte de stockage Azure ou n'importe quel serveur web accessible publiquement.
3. L'origine renvoie les fichiers sur le serveur Edge, notamment les en-têtes HTTP facultatifs décrivant la durée de vie du fichier.
4. Le serveur Edge met en cache le fichier et le renvoie au demandeur d'origine (Alice).  Le fichier reste en cache sur le serveur Edge jusqu’à la fin de la durée de vie.  Si l’origine n’a pas spécifié de durée de vie, elle est par défaut de 7 jours.
5. Des utilisateurs supplémentaires peuvent demander le même fichier à l’aide de la même URL et peuvent également être dirigés vers ce même point de présence.
6. Si la durée de vie du fichier n'a pas expiré, le serveur Edge renvoie le fichier à partir du cache. L’expérience utilisateur est en conséquence plus rapide et plus réactive.

## <a name="azure-cdn-features"></a>Fonctionnalités d’Azure CDN
Il existe trois produits Azure CDN :  **Azure CDN Standard fourni par Akamai**, **Azure CDN Standard fourni par Verizon** et **Azure CDN Premium fourni par Verizon**.  Le tableau suivant répertorie les fonctionnalités disponibles avec chaque produit.

|  | Standard Akamai | Standard Verizon | Premium Verizon |
| --- | --- | --- | --- |
| __Fonctionnalités de performance et optimisations__ |
| [Accélération de site dynamique](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  [Accélération de Site dynamique - Compression d’Image adaptative](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  [Accélération de Site dynamique - Récupération de l’objet](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [Optimisation du streaming vidéo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [Optimisation des fichiers volumineux](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [Équilibrage de charge du serveur global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Purge rapide](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Préchargement de ressources](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| Paramètres du cache/des en-têtes (à l’aide des [règles de mise en cache](cdn-caching-rules.md)) |**&#x2713;** |**&#x2713;** | |
| Paramètres du cache/des en-têtes (à l’aide du [moteur de règles](cdn-rules-engine.md)) | | |**&#x2713;** |
| [Mise en cache des chaînes de requête](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Double pile IPv4/IPv6 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Assistance HTTP/2](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __Sécurité__ |
| Prise en charge HTTPS avec un point de terminaison CDN |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS sur un domaine personnalisé](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtrage géographique](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jeton d’authentification](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [Protection DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __Analyse et création de rapports__ |
| [Journaux de diagnostic Azure](cdn-azure-diagnostic-logs.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rapports principaux de Verizon](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [Rapports personnalisés de Verizon](cdn-verizon-custom-reports.md) | |**&#x2713;** |**&#x2713;** |
| [Rapports HTTP avancés](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [Statistiques en temps réel](cdn-real-time-stats.md) | | |**&#x2713;** |
| [Performances de nœuds Edge](cdn-edge-performance.md) | | |**&#x2713;** |
| [Alertes en temps réel](cdn-real-time-alerts.md) | | |**&#x2713;** |
| __Simplicité d'utilisation__ |
| Intégration simple des services Azure tels que [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md) et [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestion via [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md). |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Moteur de distribution de contenu personnalisable et basé sur des règles](cdn-rules-engine.md) | | |**&#x2713;** |
| Redirection/réécriture d’URL (à l’aide du [moteur de règles](cdn-rules-engine.md)) | | |**&#x2713;** |
| Règles d’appareil mobile (à l’aide du [moteur de règles](cdn-rules-engine.md)) | | |**&#x2713;** |

\* Verizon prend en charge l’envoi de fichiers et de médias volumineux directement via la livraison web générale.


> [!TIP]
> Une idée de fonctionnalité à ajouter à Azure CDN ?  [Envoyez-nous vos commentaires](https://feedback.azure.com/forums/169397-cdn)! 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir Azure CDN, consultez la section [Prise en main d’Azure CDN](cdn-create-new-endpoint.md).

Si vous êtes un client CDN existant, vous pouvez désormais gérer vos points de terminaison CDN via le [portail Microsoft Azure](https://portal.azure.com) ou avec [PowerShell](cdn-manage-powershell.md).

Pour voir CDN en action, regardez la [vidéo de la session Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Apprenez à automatiser Azure CDN avec [.NET](cdn-app-dev-net.md) ou [Node.js](cdn-app-dev-node.md).

Pour des informations sur les tarifs, consultez [Tarifs Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

