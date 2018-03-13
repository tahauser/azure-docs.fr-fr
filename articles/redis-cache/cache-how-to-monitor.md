---
title: "Surveillance du Cache Redis Azure | Microsoft Docs"
description: "Découvrez comment surveiller l’état et les performances de vos instances de cache Redis Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 3a68435866e6fb5bf0210144e53918c35b416449
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-monitor-azure-redis-cache"></a>Surveillance du cache Redis Azure
Le cache Redis Azure utilise [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) afin d’offrir plusieurs possibilités de surveillance de vos instances de cache. Vous pouvez afficher les mesures, épingler des graphiques de mesure au Tableau d’accueil, personnaliser la plage de date et d’heure des graphiques de surveillance, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Ces outils vous permettent de surveiller l’intégrité de vos instances Cache Redis Azure et vous aident à gérer vos applications de mise en cache.

Les mesures des instances de Cache Redis Azure sont collectées à l’aide de la commande Redis [INFO](http://redis.io/commands/info) environ deux fois par minute et sont stockées automatiquement pendant 30 jours (consultez [Exporter des mesures de cache](#export-cache-metrics) pour configurer une stratégie de rétention différente) de façon à pouvoir être affichées dans les graphiques de mesures et évaluées par des règles d’alerte. Pour plus d’informations sur les différentes valeurs INFO utilisées pour chaque mesure de cache, consultez la section [Mesures disponibles et intervalles de création des rapports](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Pour voir les mesures de cache, [accédez](cache-configure.md#configure-redis-cache-settings) à votre instance de cache dans le [portail Azure](https://portal.azure.com).  Le Cache Redis Azure fournit des graphiques intégrés sur le **Panneau de vue d’ensemble** et le **Panneau de mesures Redis** . Chaque graphique peut être personnalisé en ajoutant ou en supprimant des mesures et en modifiant l’intervalle de création des rapports.

![Mesures Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Afficher des graphiques de mesures préconfigurés

Le **Panneau de vue d’ensemble** comporte les graphiques de surveillance préconfigurés suivants.

* [Graphiques de surveillance](#monitoring-charts)
* [Graphiques d’utilisation](#usage-charts)

### <a name="monitoring-charts"></a>Graphiques de surveillance
La **Section surveillance** du **Panneau de vue d’ensemble** affiche des **Correspondances et des absences**, des **Obtentions et des définitions**, des **Connexions** et des graphiques de **Commandes totales** .

![Graphiques de surveillance](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Graphiques d’utilisation
La **Section utilisation** du **Panneau de vue d’ensemble** affiche **la charge du serveur Redis**, **la Mémoire utilisée**, **la Bande passante réseau**, les graphiques **d’utilisation de l’UC** et affiche également le **Niveau tarifaire** de l’instance de cache.

![Graphiques d’utilisation](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Niveau tarifaire** affiche le niveau tarifaire du cache et peut être utilisé pour la [mise à l’échelle](cache-how-to-scale.md) du cache à un autre niveau tarifaire.

## <a name="view-metrics-with-azure-monitor"></a>Afficher les mesures avec Azure Monitor
Pour afficher les mesures Redis et créer des graphiques personnalisés à l’aide d’Azure Monitor, cliquez sur **Mesures** depuis le **menu Ressource**et personnalisez votre graphique à l’aide des mesures souhaitées, de l’intervalle de consignation désiré, du type de graphique voulu et bien plus encore.

![Mesures Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Pour plus d’informations sur l’utilisation des mesures à l’aide d’Azure Monitor, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exporter les mesures de cache
Par défaut, les mesures de cache dans Azure Monitor sont [stockées pendant 30 jours](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) , puis supprimées. Pour conserver vos mesures de cache pendant plus de 30 jours, vous pouvez [désigner un compte de stockage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) et spécifiez une stratégie de **Rétention (jours)** pour vos mesures de cache. 

Pour configurer un compte de stockage pour vos mesures de cache :

1. Cliquez sur **Diagnostics** depuis le **menu Ressource** du panneau **Cache Redis** .
2. Cliquez **Sur**.
3. Cochez **Archive vers un compte de stockage**.
4. Sélectionnez le compte de stockage dans lequel stocker les mesures de cache.
5. Cochez la case **1 minute** et spécifiez une stratégie de **Rétention (jours)** . Si vous ne souhaitez appliquer aucune stratégie de rétention et que vous souhaitez conserver les données indéfiniment, réglez **Rétention (jours)** à **0**.
6. Cliquez sur **Enregistrer**.

![Diagnostics Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>En plus d’archiver vos mesures de cache vers le stockage, vous pouvez également [les diffuser en continu vers un Hub d’événements ou les envoyer vers Log Analytics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

Pour accéder à vos mesures, vous pouvez les afficher dans le portail Azure, comme décrit précédemment dans cet article. Vous pouvez également y accéder à l’aide de l’ [API REST Azure Monitor Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> Si vous changez de compte de stockage, les données du compte de stockage configuré précédemment restent disponibles en téléchargement, mais elles ne sont pas affichées dans le portail Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Mesures disponibles et intervalles de création des rapports
Les mesures de cache font l’objet de rapports à différents intervalles : **Dernière heure**, **Aujourd’hui**, **Semaine dernière** et **Personnalisé**. Le panneau **Mesure** de chaque mesure affiche les valeurs moyennes, minimales et maximales de chaque mesure du graphique et certaines mesures affichent le total pour l’intervalle de création des rapports. 

Chaque mesure inclut deux versions. Une première mesure évalue les performances de la totalité du cache et, pour les caches qui utilisent le [clustering](cache-how-to-premium-clustering.md), une deuxième version de la mesure dont le nom inclut `(Shard 0-9)` évalue les performances d’une seule partition d’un cache. Par exemple, si un cache comporte 4 partitions, `Cache Hits` est la quantité totale d’accès pour le cache entier, tandis que `Cache Hits (Shard 3)` est simplement le nombre d’accès à cette partition du cache.

> [!NOTE]
> Même lorsque le cache est inactif sans applications clientes actives connectées, vous pouvez constater une certaine activité du cache, par exemple les opérations en cours d’exécution, l’utilisation de la mémoire et les clients connectés. Cette activité est normale dans le cadre du fonctionnement d’une instance de cache Redis Azure.
> 
> 

| Métrique | Description |
| --- | --- |
| Présences dans le cache |Nombre de recherches clés réussies au cours de l’intervalle de création des rapports. Celle-ci mappe à [ à partir de la commande Redis ](http://redis.io/commands/info)INFO`keyspace_hits`. |
| Absences dans le cache |Nombre de recherches clés non réussies au cours de l’intervalle de création des rapports. Cette valeur correspond à la commande Redis INFO `keyspace_misses` . Les absences dans le cache ne signifient pas nécessairement qu’il y a un problème dans le cache. Par exemple, en cas d’utilisation du mode de programmation de type cache-aside, une application recherche d’abord l’élément dans le cache. Si cet élément ne s’y trouve pas (Absence dans le cache), il est récupéré dans la base de données et ajouté au cache pour la prochaine fois. Les absences dans le cache sont un comportement normal pour le mode de programmation de type cache-aside. Si le nombre d’absences dans le cache est plus élevé que prévu, examinez la logique d’application qui remplit le cache et y lit les informations. Si des éléments sont supprimés du cache en raison d’une trop grande sollicitation de la mémoire, des absences dans le cache peuvent se produire, mais `Used Memory` ou `Evicted Keys` sont de meilleures mesures pour surveiller la pression sur la mémoire. |
| Clients connectés |Nombre de connexions client au cache au cours de l’intervalle de création des rapports spécifié. Cette valeur correspond à la commande Redis INFO `connected_clients` . Une fois la [limite de connexions](cache-configure.md#default-redis-server-configuration) atteinte, les tentatives de connexion ultérieures au cache échouent. Notez que même s’il n’y a aucune application cliente active, il peut rester quelques instances de clients connectés en raison de connexions et processus internes. |
| Clés exclues |Nombre d’éléments supprimés du cache au cours de l’intervalle de création des rapports, en raison de la limite `maxmemory` . Cette valeur correspond à la commande Redis INFO `evicted_keys` . |
| Clés expirées |Nombre d’éléments expirés dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur correspond à la commande Redis INFO `expired_keys` . |
| Nombre total de clés  | Le nombre maximal de clés dans le cache pendant la dernière période de rapport. Cette valeur correspond à la commande Redis INFO `keyspace` . En raison d’une limitation du système de mesures sous-jacent, pour les caches avec le clustering activé, Nombre total de clés retourne le nombre maximal de clés de la partition ayant eu le nombre maximal de clés pendant l’intervalle de consignation.  |
| Gets |Nombre d’opérations get dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur est la somme des valeurs suivantes obtenues de toutes les commandes Redis INFO : `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` et `cmdstat_getrange`. Elle est équivalente à la somme du nombre de présences et d’absences au cours de l’intervalle de création du rapport. |
| Charge du serveur Redis |Pourcentage de cycles dans lesquels le serveur Redis est occupé par le traitement et n’est pas inactif, en attente de messages. Si ce compteur atteint 100, c’est que le serveur Redis a atteint un plafond de performances et que le processeur ne peut pas fonctionner plus rapidement. Si vous voyez une forte charge de serveur Redis, des exceptions d’expiration seront levées dans le client. Dans ce cas, vous devez envisager d’effectuer une mise à l’échelle ou bien de partitionner vos données sur plusieurs caches. |
| Sets |Nombre d’opérations set dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur est la somme des valeurs suivantes obtenues de toutes les commandes Redis INFO : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` et `cmdstat_setnx`. |
| Total des opérations |Nombre total de commandes traitées par le serveur de cache au cours de l’intervalle spécifié. Cette valeur correspond à la commande Redis INFO `total_commands_processed` . Notez que lorsque le cache Redis Azure est uniquement utilisé pour publication et téléchargement, il n’y a aucune mesure pour `Cache Hits`, `Cache Misses`, `Gets` ou `Sets`, mais il y aura des mesures de `Total Operations` qui reflètent l’utilisation du cache pour ces opérations. |
| Mémoire utilisée |Quantité de mémoire cache, exprimée en Mo, utilisée pour les paires clé/valeur dans le cache au cours de l’intervalle de création des rapports. Cette valeur correspond à la commande Redis INFO `used_memory` . Elle n’inclut pas les métadonnées ou la fragmentation. |
| Taille de la mémoire résidente utilisée |Quantité de mémoire cache utilisée (exprimée en Mo) au cours de l’intervalle de création des rapports, fragmentation et métadonnées comprises. Cette valeur correspond à la commande Redis INFO `used_memory_rss` . |
| UC |Utilisation du processeur du serveur de cache Redis Azure sous forme de pourcentage au cours de l’intervalle de création des rapports spécifiée. Cette valeur correspond au compteur de performances `\Processor(_Total)\% Processor Time` du système d’exploitation. |
| Lecture du cache |Quantité de données lues dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création des rapports. Cette valeur est dérivée des cartes réseau qui prennent en charge la machine virtuelle qui héberge le cache. Elle n’est pas spécifique de Redis. **Cette valeur correspond à la bande passante réseau utilisée par ce cache. Si vous souhaitez configurer des alertes pour des limites de bande passante réseau côté serveur, vous pouvez les créer à l’aide de ce compteur `Cache Read`. Pour connaître les limites de bande passante observées pour les différents niveaux de tarification et tailles de cache, voir [ce tableau](cache-faq.md#cache-performance).** |
| Cache d’écriture |Quantité de données écrites dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création des rapports. Cette valeur est dérivée des cartes réseau qui prennent en charge la machine virtuelle qui héberge le cache. Elle n’est pas spécifique de Redis. Cette valeur correspond à la bande passante réseau des données envoyées au cache depuis le client. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertes
Vous pouvez configurer les paramètres pour recevoir des alertes en fonction des mesures et des journaux d’activité. Azure Monitor vous permet de configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer un e-mail de notification
* Appeler un webhook
* Appeler une application logique Azure

Pour configurer les Règles d’alerte de votre cache, cliquez sur **Règles d’alerte** depuis le **menu Ressource**.

![Surveillance](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Pour plus d’informations sur la configuration et l’utilisation des Alertes, consultez [Vue d’ensemble des Alertes](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Journaux d’activité
Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur vos instances de Cache Redis Azure. Ils étaient auparavant nommés « Journaux d’audit » ou « Journaux des opérations ». À l’aide des journaux d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur vos instances de Cache Redis Azure. 

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations (GET) de lecture.
>
>

Pour afficher les journaux d’activité de votre cache, cliquez sur **Journaux d’activité** depuis le **menu Ressource**.

Pour plus d’informations à propos des Journaux d’activité, consultez [Vue d’ensemble des Journaux d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











