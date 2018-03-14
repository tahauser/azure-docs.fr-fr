---
title: Configuration du Cache Redis Azure | Microsoft Docs
description: "Comprendre la configuration de Redis par défaut pour Cache Redis Azure et apprendre à configurer vos instances de Cache Redis Azure"
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: a65832a30a570944ff30d02c2f173df345bde32c
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-azure-redis-cache"></a>Configuration de Cache Redis Azure
Cette rubrique montre comment consulter et mettre à jour la configuration de vos instances de Cache Redis Azure et présente la configuration du serveur Redis par défaut pour les instances de Cache Redis Azure.

> [!NOTE]
> Pour plus d’informations sur la configuration et l’utilisation des fonctionnalités du cache Premium, voir [Comment configurer la persistance](cache-how-to-premium-persistence.md), [Comment configurer le clustering](cache-how-to-premium-clustering.md)et [Comment configurer la prise en charge de réseau virtuel](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Configuration des paramètres de cache Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Les paramètres de Cache Redis Azure sont affichés et configurés dans le panneau **Cache Redis** avec le **Menu Ressource**.

![Paramètres de Cache Redis](./media/cache-configure/redis-cache-settings.png)

Vous pouvez afficher et configurer les paramètres suivants à l’aide du **Menu Ressource**.

* [Vue d’ensemble](#overview)
* [Journal d’activité](#activity-log)
* [Contrôle d’accès (IAM)](#access-control-iam)
* [Balises](#tags)
* [Diagnostiquer et résoudre les problèmes](#diagnose-and-solve-problems)
* [Paramètres](#settings)
    * [Clés d’accès](#access-keys)
    * [Paramètres avancés](#advanced-settings)
    * [Redis Cache Advisor](#redis-cache-advisor)
    * [Mettre à l'échelle](#scale)
    * [Taille du cluster Redis](#cluster-size)
    * [Persistance des données Redis](#redis-data-persistence)
    * [Planification de mises à jour](#schedule-updates)
    * [Géoréplication](#geo-replication)
    * [Réseau virtuel](#virtual-network)
    * [Pare-feu](#firewall)
    * [Propriétés](#properties)
    * [Verrous](#locks)
    * [Script Automation](#automation-script)
* [Administration](#administration)
    * [Importer des données](#importexport)
    * [Exporter des données](#importexport)
    * [Reboot](#reboot)
* [Surveillance](#monitoring)
    * [Mesures Redis](#redis-metrics)
    * [Règles d'alerte](#alert-rules)
    * [Diagnostics](#diagnostics)
* [Paramètres de support et dépannage](#support-amp-troubleshooting-settings)
    * [Intégrité des ressources](#resource-health)
    * [Nouvelle demande de support](#new-support-request)


## <a name="overview"></a>Vue d’ensemble

La **Vue d’ensemble** fournit des informations de base sur votre cache, notamment le nom, les ports, le niveau tarifaire et des mesures de cache sélectionnées.

### <a name="activity-log"></a>Journal d’activité

Cliquez sur **Journaux d’activité** pour afficher les actions effectuées sur votre cache. Vous pouvez également utiliser le filtrage pour développer cette vue afin d’inclure d’autres ressources. Pour en savoir plus sur l’utilisation des journaux d’audit, voir [Afficher les journaux d’activité pour auditer les actions sur les ressources](../azure-resource-manager/resource-group-audit.md). Pour plus d’informations sur la surveillance des événements du Cache Redis Azure, consultez [Opérations et alertes](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Contrôle d’accès (IAM)

La section **Contrôle d’accès (IAM)** fournit une prise en charge pour le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure, pour aider les entreprises à répondre aux exigences de gestion des accès de façon simple et précise. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

### <a name="tags"></a>Balises

La section **Balises** vous aide à organiser vos ressources. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostiquer et résoudre les problèmes

Cliquez sur **Diagnostiquer et résoudre les problèmes** pour afficher les problèmes courants et les stratégies de résolution associées.



## <a name="settings"></a>Paramètres
La section **Paramètres** vous permet d’accéder aux paramètres suivants et de les configurer pour votre cache.

* [Clés d’accès](#access-keys)
* [Paramètres avancés](#advanced-settings)
* [Redis Cache Advisor](#redis-cache-advisor)
* [Mettre à l'échelle](#scale)
* [Taille du cluster Redis](#cluster-size)
* [Persistance des données Redis](#redis-data-persistence)
* [Planification de mises à jour](#schedule-updates)
* [Géoréplication](#geo-replication)
* [Réseau virtuel](#virtual-network)
* [Pare-feu](#firewall)
* [Propriétés](#properties)
* [Verrous](#locks)
* [Script Automation](#automation-script)



### <a name="access-keys"></a>Clés d’accès
Cliquez sur **Clés d’accès** pour afficher ou régénérer les clés d’accès de votre cache. Ces clés sont utilisées par les clients qui se connectent à votre cache.

![Clés d’accès de Cache Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Paramètres avancés
Vous pouvez configurer les paramètres suivants dans le panneau **Paramètres avancés**.

* [Ports d’accès](#access-ports)
* [Stratégies de mémoire](#memory-policies)
* [Notifications de Keyspace (paramètres avancés)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Ports d’accès
Par défaut, l’accès non SSL est désactivé pour les nouveaux caches. Pour activer le port non SSL, cliquez sur **Non** pour **Autoriser l’accès uniquement via SSL** dans le **panneau Paramètres avancés**, puis cliquez sur **Enregistrer**.

![Ports d’accès de Cache Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Stratégies de mémoire
Les paramètres **Stratégie Maxmemory**, **maxmemory-reserved** et **maxfragmentationmemory-reserved** dans le panneau **Paramètres avancés** configurent les stratégies de mémoire du cache.

![Stratégie Maxmemory de Cache Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

La **stratégie MaxMemory** configure la stratégie d’éviction pour le cache, et vous permet de choisir parmi les stratégies d’éviction suivantes :

* `volatile-lru` : il s’agit de la valeur par défaut.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Pour plus d’informations sur les stratégies `maxmemory`, voir [Stratégies d’éviction](http://redis.io/topics/lru-cache#eviction-policies).

Le paramètre **maxmemory-reserved** détermine la quantité de mémoire (en mégaoctets) réservée pour les opérations non cache telles que la réplication pendant le basculement. La définition de ce paramètre vous permet d’avoir une expérience de serveur Redis plus cohérente lorsque votre charge varie. Cette valeur doit être plus élevée pour les charges de travail comportant de nombreuses écritures. Lorsque la mémoire est réservée pour ces opérations, elle n’est pas disponible pour le stockage des données mises en cache.

Le paramètre **maxfragmentationmemory réservés** détermine la quantité de mémoire en Mo réservée pour la fragmentation de la mémoire. La définition de ce paramètre vous permet d’avoir un serveur Redis plus cohérent lorsque le cache est plein ou presque, et que le taux de fragmentation est également élevé. Lorsque la mémoire est réservée pour ces opérations, elle n’est pas disponible pour le stockage des données mises en cache.

Un aspect à prendre en compte lors du choix d’une nouvelle valeur de réservation de mémoire (**maxmemory-reserved** ou **maxfragmentationmemory-reserved**) est la manière dont cette modification peut affecter un cache déjà en cours d’exécution, qui contient de grandes quantités de données. Par exemple, si vous disposez d’un cache de 53 Go avec 49 Go de données, modifiez la valeur de réservation en la définissant sur 8 Go. Cela a pour effet d’abaisser la mémoire maximale disponible pour le système à 45 Go. Si vos valeurs `used_memory` ou `used_memory_rss` actuelles sont supérieures à la nouvelle limite de 45 Go, le système doit supprimer des données jusqu’à ce que les valeurs `used_memory` et `used_memory_rss` soient toutes deux inférieures à 45 Go. La suppression de données peut augmenter la fragmentation de la charge et de la mémoire du serveur. Pour plus d’informations sur les métriques de cache, telles que `used_memory` et `used_memory_rss`, voir [Mesures disponibles et intervalles de création des rapports](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Les paramètres **maxmemory-reserved** and **maxfragmentationmemory-reserved** sont uniquement disponibles pour les caches Standard et Premium.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notifications de Keyspace (paramètres avancés)
Accédez au panneau **Paramètres avancés** pour configurer les notifications de keyspace Redis. Les notifications de keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Paramètres avancés de Cache Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Les notifications de keyspace et le paramètre **notify-keyspace-events** sont uniquement disponibles pour les caches Standard et Premium.
> 
> 

Pour plus d’informations, voir [Notifications de keyspace Redis](http://redis.io/topics/notifications). Pour un exemple de code, voir le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor
Le panneau **Redis Cache Advisor** affiche des recommandations pour votre cache. Pendant les opérations normales, aucune recommandation n’est affichée. 

![Recommandations](./media/cache-configure/redis-cache-no-recommendations.png)

Si une condition se produit pendant les opérations de votre cache (notamment l’utilisation élevée de la mémoire, la bande passante réseau ou la charge du serveur), une alerte s’affiche dans le panneau **Cache Redis** .

![Recommandations](./media/cache-configure/redis-cache-recommendations-alert.png)

Des informations complémentaires sont disponibles dans le panneau **Recommandations** .

![Recommandations](./media/cache-configure/redis-cache-recommendations.png)

Vous pouvez surveiller ces mesures dans les sections [Graphiques de surveillance](cache-how-to-monitor.md#monitoring-charts) et [Graphiques d’utilisation](cache-how-to-monitor.md#usage-charts) du panneau **Cache Redis**.

Chaque niveau tarifaire est associé à des limites spécifiques concernant les connexions clientes, la mémoire et la bande passante. Si votre cache est proche de la capacité maximale pour ces mesures pendant une période prolongée, une recommandation est créée. Pour plus d’informations sur les mesures et limites évaluées par l’outil **Recommandations**, consultez le tableau suivant :

| Mesure du Cache Redis | Plus d’informations |
| --- | --- |
| Utilisation de la bande passante réseau |[Performances du cache - Bande passante disponible](cache-faq.md#cache-performance) |
| Clients connectés |[Configuration du serveur Redis par défaut - maxclients](#maxclients) |
| Charge du serveur |[Graphiques d’utilisation - Charge du serveur Redis](cache-how-to-monitor.md#usage-charts) |
| Utilisation de la mémoire |[Performance du cache - Taille](cache-faq.md#cache-performance) |

Pour mettre à niveau votre cache, cliquez sur **Mettre à niveau maintenant** afin de modifier le niveau tarifaire et de [mettre à l’échelle](#scale) votre cache. Pour plus d’informations sur le choix d’un niveau tarifaire, voir la section [Que propose Cache Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Scale
Cliquez sur **Mise à l’échelle** pour afficher ou modifier le niveau tarifaire de votre cache. Pour plus d’informations sur la mise à l’échelle, voir [Mise à l’échelle du cache Redis Azure](cache-how-to-scale.md).

![Niveau de tarification de Cache Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Taille du cluster Redis
Cliquez sur **Taille du cluster Redis (PREVIEW)** pour changer la taille du cluster pour un cache de niveau Premium en cours d’exécution avec le clustering activé.

> [!NOTE]
> Notez que si le niveau Premium du cache Redis Azure a été publié en disponibilité générale, la fonctionnalité Taille du cluster Redis est actuellement en version préliminaire.
> 
> 

![Taille du cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Pour modifier la taille du cluster, utilisez le curseur ou entrez un nombre compris entre 1 et 10 dans la zone de texte **Nombre de partitions**, puis cliquez sur **OK** pour enregistrer.

> [!IMPORTANT]
> Le clustering Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer le clustering Redis pour un cache Redis Azure Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Persistance des données Redis
Cliquez sur **Persistance des données Redis** pour activer, désactiver ou configurer la persistance des données de votre cache Premium. Cache Redis Azure offre la persistance Redis grâce à la [persistance RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) et à la [persistance AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Pour plus d’informations, consultez [Comment configurer la persistance pour un cache Redis Azure Premium](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> La persistance des données Redis est disponible uniquement pour les caches de niveau Premium. 
> 
> 

### <a name="schedule-updates"></a>Planifier les mises à jour
Le panneau **Planification de mises à jour** vous permet de désigner une fenêtre de maintenance pour les mises à jour du serveur Redis de votre cache. 

> [!IMPORTANT]
> Notez que la fenêtre de maintenance s’applique uniquement aux mises à jour du serveur Redis et non à toutes les mises à jour d’Azure ou celles du système d’exploitation des machines virtuelles qui hébergent le cache.
> 
> 

![Planifier les mises à jour](./media/cache-configure/redis-schedule-updates.png)

Pour spécifier une fenêtre de maintenance, vérifiez les jours choisis et spécifiez l’heure de début de la fenêtre de maintenance pour chaque jour, puis cliquez sur **OK**. Notez que l’heure de la maintenance est au format UTC. 

> [!IMPORTANT]
> La fonctionnalité **Planifier les mises à jour** est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et pour obtenir des instructions, consultez la page [Administration du cache Redis Azure - Planification de mises à jour](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Géoréplication

Le panneau **Géoréplication** fournit un mécanisme de lien de deux instances de cache Redis Azure de niveau Premium. Un cache est désigné comme le cache lié principal et l’autre comme cache lié secondaire. Le cache lié secondaire est en lecture seule et les données écrites dans le cache principal sont répliquées vers le cache lié secondaire. Cette fonctionnalité peut être utilisée pour répliquer un cache entre des régions Azure.

> [!IMPORTANT]
> La **Géoréplication** est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et pour obtenir des instructions, consultez [Comment configurer la géoréplication pour le Cache Redis Azure](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Réseau virtuel
La section **Réseau virtuel** permet de configurer les paramètres de réseau virtuel de votre cache. Pour plus d’informations sur la création d’un cache Premium avec la prise en charge de réseau virtuel et sur la mise à jour de ses paramètres, consultez [Comment configurer la prise en charge de réseau virtuel pour un cache Redis Azure Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Les paramètres de réseau virtuel sont disponibles uniquement pour les caches Premium qui ont été configurés avec la prise en charge de réseau virtuel lors de la création du cache. 
> 
> 

### <a name="firewall"></a>Pare-feu

Cliquez sur **Pare-feu** pour afficher et configurer des règles de pare-feu pour votre Cache Redis Azure Premium.

![Pare-feu](./media/cache-configure/redis-firewall-rules.png)

Vous pouvez spécifier des règles de pare-feu avec une des valeurs de début et de fin de plage d’adresses IP. Lorsque des règles de pare-feu sont configurées, seules les connexions client à partir des plages d’adresses IP spécifiées peuvent se connecter au cache. Lorsqu’une règle de pare-feu est enregistrée, il y a un bref délai avant que la règle soit effective. Ce délai est généralement inférieur à une minute.

> [!IMPORTANT]
> Les connexions depuis les systèmes de surveillance de cache Redis Azure sont toujours autorisées, même si des règles de pare-feu sont configurées.
> 
> Les règles de pare-feu son uniquement disponibles pour les caches de niveau Premium.
> 
> 

### <a name="properties"></a>properties
Cliquez sur **Propriétés** pour afficher des informations sur le cache, y compris le point de terminaison et les ports du cache.

![Propriétés de Cache Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Verrous
La section **Verrous** permet de verrouiller un abonnement, une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou de modifier de manière accidentelle des ressources critiques. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script Automation

Cliquez sur **Script Automation** pour générer et exporter un modèle de vos ressources déployées pour de futurs déploiements. Pour plus d’informations sur le fonctionnement des modèles, consultez [Déployer des ressources à l’aide de modèles Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Paramètres d’administration
Les paramètres de la section **Administration** vous permettent d’effectuer les tâches administratives suivantes pour votre cache. 

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importer des données](#importexport)
* [Exporter des données](#importexport)
* [Reboot](#reboot)


### <a name="importexport"></a>Importation/Exportation
L’importation/exportation est une opération de gestion de données de Cache Redis Azure. Elle permet d’importer et d’exporter des données dans le cache en important ou en exportant un instantané de base de données du Cache Redis (RDB) depuis un cache Premium vers un objet blob de pages, dans un compte Azure Storage. L’Importation/Exportation vous permet de migrer entre différentes instances de Cache Redis Azure ou de remplir le cache de données avant utilisation.

L’importation peut servir à récupérer les fichiers RDB compatibles Redis depuis un serveur Redis en cours d’exécution dans un environnement ou dans un cloud, y compris si Redis est exécuté sur Linux, Windows ou n’importe quel fournisseur de cloud tel qu’Amazon Web Services. Importer des données est un moyen simple de créer un cache pré-rempli de données. Pendant le processus d’importation, le Cache Azure Redis charge les fichiers RDB d’Azure Storage dans la mémoire, puis insère les clés dans le cache.

L’exportation vous permet d’exporter les données stockées dans le Cache Azure Redis vers des fichiers RDB compatibles. Vous pouvez utiliser cette fonctionnalité pour déplacer des données d’une instance de Cache Redis Azure à une autre, ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur la machine virtuelle qui héberge l’instance de serveur de Cache Redis Azure, puis téléchargé vers le compte de stockage désigné. Lorsque l’opération d’exportation se termine avec un état de réussite ou d’échec, le fichier temporaire est supprimé.

> [!IMPORTANT]
> L’importation/exportation est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et pour obtenir des instructions, consultez la rubrique [Importer et exporter des données dans le cache Redis Azure](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reboot
Le panneau **Redémarrer** permet de redémarrer les nœuds de votre cache. Cette fonctionnalité de redémarrage vous permet de tester votre application pour garantir la résilience en cas de panne d’un nœud de cache.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Si vous avez un cache premium avec activation du clustering, vous pouvez sélectionner les partitions du cache à redémarrer.

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs nœuds de votre cache, sélectionnez les nœuds souhaités, puis cliquez sur **Reboot**. Si vous avez un cache premium avec activation du clustering, sélectionnez les partitions à redémarrer, puis cliquez sur **Reboot**. Après quelques minutes, les nœuds sélectionnés sont redémarrés et sont de nouveau en ligne.

> [!IMPORTANT]
> Le redémarrage est désormais disponible pour tous les niveaux de tarification. Pour plus d’informations et pour obtenir des instructions, consultez la page [Administration du cache Redis Azure - Redémarrage](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Surveillance

La section **Surveillance** vous permet de configurer les diagnostics et la surveillance de votre cache Redis. Pour plus d’informations sur la surveillance et les diagnostics du cache Redis Azure, voir [Surveillance du cache Redis Azure](cache-how-to-monitor.md).

![Diagnostics](./media/cache-configure/redis-cache-diagnostics.png)

* [Mesures Redis](#redis-metrics)
* [Règles d'alerte](#alert-rules)
* [Diagnostics](#diagnostics)

### <a name="redis-metrics"></a>Mesures Redis
Cliquez sur **Mesures Redis** pour [affichage des mesures](cache-how-to-monitor.md#view-cache-metrics) pour votre cache.

### <a name="alert-rules"></a>Règles d'alerte

Cliquez sur **Règles d’alerte** pour configurer des alertes en fonction des métriques du Cache Redis. Pour plus d'informations, consultez [Alertes](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostics

Par défaut, les mesures de cache dans Azure Monitor sont [stockées pendant 30 jours](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive), puis supprimées. Pour prolonger vos mesures de cache sur plus de 30 jours, cliquez sur **Diagnostics** pour [configurer le compte de stockage](cache-how-to-monitor.md#export-cache-metrics) utilisé pour stocker les diagnostics de cache.

>[!NOTE]
>En plus d’archiver vos mesures de cache vers le stockage, vous pouvez également [les diffuser vers un Hub d’événements ou les envoyer vers Log Analytics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

## <a name="support--troubleshooting-settings"></a>Paramètres de support et dépannage
Les paramètres de la section relative à la **prise en charge et à la résolution des problèmes** proposent des options pour la résolution des problèmes liés à votre cache.

![prise en charge et à la résolution des problèmes](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Intégrité des ressources](#resource-health)
* [Nouvelle demande de support](#new-support-request)

### <a name="resource-health"></a>Intégrité des ressources
**Resource Health** surveille vos ressources et vous indique si elles s’exécutent comme prévu. Pour plus d’informations sur le service Azure Resource Health, consultez [Vue d’ensemble d’Azure Resource Health](../resource-health/resource-health-overview.md).

> [!NOTE]
> Resource Health ne parvient pas à créer de rapport sur l’intégrité des instances du cache Redis Azure hébergées dans un réseau virtuel. Pour plus d’informations, voir [Toutes les fonctionnalités fonctionnent-elles lorsque vous hébergez un cache dans un réseau virtuel ?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nouvelle demande de support
Cliquez sur **Nouvelle demande de support** pour ouvrir une demande de support pour votre cache.





## <a name="default-redis-server-configuration"></a>Configuration du serveur Redis par défaut
Les nouvelles instances de Cache Redis Azure sont configurées avec les valeurs de configuration par défaut Redis suivantes.

> [!NOTE]
> Les paramètres de cette section ne peuvent pas être modifiés à l’aide de la méthode `StackExchange.Redis.IServer.ConfigSet`. Si cette méthode est appelée avec une des commandes de cette section, une exception semblable à la suivante est levée :  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Toutes les valeurs configurables, par exemple **max-memory-policy**, peuvent être modifiées via le portail Azure ou via des outils de gestion en ligne de commande, comme Azure CLI ou PowerShell.
> 
> 

| Paramètre | Valeur par défaut | Description |
| --- | --- | --- |
| `databases` |16 |Le nombre de bases de données par défaut est 16, mais vous pouvez configurer un nombre différent selon le niveau tarifaire.<sup>1</sup> La base de données par défaut est DB 0. Vous pouvez en sélectionner une autre en fonction de la connexion en utilisant `connection.GetDatabase(dbid)` où `dbid` est un nombre compris entre `0` et `databases - 1`. |
| `maxclients` |Dépend du niveau tarifaire<sup>2</sup> |Le nombre maximal de clients connectés autorisés en même temps. Une fois la limite atteinte, Redis ferme toutes les nouvelles connexions en envoyant une erreur « nombre maximal de clients atteint ». |
| `maxmemory-policy` |`volatile-lru` |La stratégie maxmemory est le paramètre définissant la sélection par Redis des éléments à supprimer lorsque `maxmemory` (la taille du cache que vous avez sélectionnée lorsque vous avez créé le cache) est atteinte. Avec Cache Redis Azure, le paramètre par défaut est `volatile-lru`. Ce dernier supprime les clés et l’expiration est définie à l’aide d’un algorithme LRU (dernier récemment utilisé). Ce paramètre peut être configuré dans le portail Azure. Pour plus d’informations, voir [Stratégies de mémoire](#memory-policies). |
| `maxmemory-samples` |3 |Pour économiser de la mémoire, les algorithmes LRU et TTL sont des algorithmes approximatifs et non des algorithmes précis. Par défaut, Redis vérifie trois clés et choisit celle qui a été utilisée le moins récemment. |
| `lua-time-limit` |5 000 |Temps d’exécution maximal d’un script Lua en millisecondes. Si la durée d’exécution maximale est atteinte, Redis consigne qu’un script est toujours en cours d’exécution après la durée maximale autorisée et commence à répondre aux requêtes avec une erreur. |
| `lua-event-limit` |500 |Taille maximale de la file d’attente des événements de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Les limites de mémoire tampon de sortie client peuvent servir à forcer la déconnexion des clients qui ne lisent pas les données à partir du serveur suffisamment rapidement pour une raison quelconque (une raison courante est qu’un client Pub/Sub ne peut pas consommer les messages aussi rapidement que le serveur de publication les génère). Pour plus d’informations, consultez [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>La limite de `databases` est différente pour chaque niveau tarifaire du cache Redis Azure, et peut être définie lors de la création du cache. Si aucun paramètre `databases` n’est spécifié lors de la création du cache, la valeur par défaut est 16.

* Caches De base et Standard
  * Cache C0 (250 Mo) : jusqu’à 16 bases de données
  * Cache C1 (1 Go) : jusqu’à 16 bases de données
  * Cache C2 (2,5 Go) : jusqu’à 16 bases de données
  * Cache C3 (6 Go) : jusqu’à 16 bases de données
  * Cache C4 (13 Go) : jusqu’à 32 bases de données
  * Cache C5 (26 Go) : jusqu’à 48 bases de données
  * Cache C6 (53 Go) : jusqu’à 64 bases de données
* Caches Premium
  * P1 (6 à 60 Go) - jusqu’à 16 bases de données
  * P2 (13 à 130 Go) - jusqu’à 32 bases de données
  * P3 (26 à 260 Go) - jusqu’à 48 bases de données
  * P4 (53 à 530 Go) - jusqu’à 64 bases de données
  * Tous les caches de niveau Premium avec cluster Redis activé : le cluster Redis prend uniquement en charge l’utilisation de la base de données 0 pour que la limite `databases` de chaque cache Premium avec cluster Redis activé soit effectivement 1 et que la commande [Sélectionner](http://redis.io/commands/select) ne soit pas autorisée. Pour en savoir plus, voir, [Dois-je apporter des modifications à mon application cliente pour utiliser le clustering ?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Pour plus d’informations sur les bases de données, consultez [Quelles sont les bases de données Redis ?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> Le paramètre `databases` peut être configuré uniquement lors de la création du cache et uniquement à l’aide de PowerShell, de l’interface de ligne de commande ou d’autres clients de gestion. Pour obtenir un exemple de configuration de `databases` lors de la création du cache à l’aide de PowerShell, voir [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients` est différent pour chaque niveau tarifaire du cache Redis Azure.

* Caches De base et Standard
  * Cache C0 (250 Mo) : jusqu’à 256 connexions
  * Cache C1 (1 Go) : jusqu’à 1 000 connexions
  * Cache C2 (2,5 Go) : jusqu’à 2 000 connexions
  * Cache C3 (6 Go) : jusqu’à 5 000 connexions
  * Cache C4 (13 Go) : jusqu’à 10 000 connexions
  * Cache C5 (26 Go) : jusqu’à 15 000 connexions
  * Cache C6 (53 Go) : jusqu’à 20 000 connexions
* Caches Premium
  * P1 (6 Go - 60 Go) : jusqu’à 7 500 connexions
  * P2 (13 Go - 130 Go) : jusqu’à 15 000 connexions
  * P3 (26 Go - 260 Go) : jusqu’à 30 000 connexions
  * P4 (53 Go - 530 Go) : jusqu’à 40 000 connexions

> [!NOTE]
> Si chaque taille de cache autorise *jusqu’à* un certain nombre de connexions, chaque connexion à Redis entraîne une surcharge. Un exemple d’une telle surcharge est l’utilisation du processeur et de la mémoire à la suite d’un chiffrement TLS/SSL. La limite maximale de connexions pour une taille de cache donnée suppose un cache peu chargé. Si la charge de la surcharge de connexion *plus* la charge des opérations de client dépasse la capacité du système, le cache peut rencontrer des problèmes de capacité, même si vous n’avez pas dépassé la limite de connexion pour la taille de cache actuelle.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Commandes Redis non prises en charge dans le Cache Redis Azure
> [!IMPORTANT]
> Étant donné que la configuration et la gestion des instances de Cache Redis Azure sont gérées par Microsoft, les commandes ci-après sont désactivées. Si vous essayez de les utiliser, vous recevez un message d’erreur du type `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * Enregistrer
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER : les commandes d’écriture du cluster sont désactivées, mais celles de lecture seule sont autorisées.
> 
> 

Pour plus d’informations sur les commandes Redis, consultez [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Console Redis
Vous pouvez émettre en toute sécurité des commandes aux instances de Cache Redis Azure à l’aide de la **Console Redis**disponible dans le portail Azure pour tous les niveaux de caches.

> [!IMPORTANT]
> - La console Redis ne fonctionne pas avec [VNET](cache-how-to-premium-vnet.md). Quand votre cache fait partie d’un réseau virtuel, seuls les clients de ce réseau virtuel peuvent accéder au cache. Console Redis s’exécutant dans votre navigateur local situé à l’extérieur du réseau virtuel, il ne peut pas se connecter à votre cache.
> - Les commandes Redis ne sont pas toutes prises en charge dans le Cache Redis Azure. Pour obtenir la liste des commandes Redis désactivées pour le Cache Redis Azure, consultez la section précédente intitulée [Commandes Redis non prises en charge dans le Cache Redis Azure](#redis-commands-not-supported-in-azure-redis-cache) . Pour plus d’informations sur les commandes Redis, consultez [http://redis.io/commands](http://redis.io/commands).
> 
> 

Pour accéder à la console Redis, cliquez sur **Console** dans le panneau **Cache Redis**.

![Console Redis](./media/cache-configure/redis-console-menu.png)

Pour émettre des commandes sur votre instance de cache, tapez simplement la commande souhaitée dans la console.

![Console Redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Utilisation de la Console Redis avec un cache premium en cluster

Lors de l’utilisation de la Console Redis avec un cache en cluster premium, vous pouvez émettre des commandes vers une partition unique du cache. Pour émettre une commande vers une partition spécifique, connectez-vous d’abord à la partition de votre choix en cliquant dessus dans le sélecteur de partition.

![Console Redis](./media/cache-configure/redis-console-premium-cluster.png)

Si vous tentez d’accéder à une clé stockée dans une autre partition que la partition connectée, un message d’erreur semblable au message suivant s’affiche.

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Dans l’exemple précédent, la partition 1 est la partition sélectionnée, mais `myKey` se trouve dans la partition 0, comme indiqué par la partie `(shard 0)` du message d’erreur. Dans cet exemple, pour accéder à `myKey`, sélectionnez la partition 0 à l’aide du sélecteur de partition et émettez ensuite la commande souhaitée.


## <a name="move-your-cache-to-a-new-subscription"></a>Déplacement du cache vers un nouvel abonnement
Pour déplacer votre cache vers un nouvel abonnement, cliquez sur **Déplacer**.

![Déplacer le Cache Redis](./media/cache-configure/redis-cache-move.png)

Pour plus d’informations sur le déplacement des ressources d’un groupe de ressources vers un autre et d’un abonnement vers un autre, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’utilisation des commandes Redis, voir [Exécution des commandes Redis](cache-faq.md#how-can-i-run-redis-commands).

