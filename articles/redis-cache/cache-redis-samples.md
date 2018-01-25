---
title: Exemples de Cache Redis Azure | Microsoft Docs
description: "Découvrez comment utiliser le Cache Redis Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0b6f89807d3252b750bd5208a7f758a06c9903d6
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-redis-cache-samples"></a>Exemples de Cache Redis Azure
Cette rubrique fournit une liste d'exemples relatifs à Cache Redis Azure, qui couvrent les scénarios de connexion au cache, de lecture et écriture des données vers et depuis le cache, ainsi que l'utilisation des fournisseurs ASP.NET du Cache Redis. Certains de ces exemples sont des projets téléchargeables. D'autres fournissent des instructions étape par étape et incluent des extraits de code, mais ne sont pas liés à un projet téléchargeable.

## <a name="hello-world-samples"></a>Exemples Hello world
Les exemples de cette section montrent les principes fondamentaux de la connexion à une instance du Cache Redis Azure et ceux de la lecture et de l'écriture de données dans le cache à l'aide d'une variété de langages et de clients Redis.

L’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) montre comment effectuer différentes opérations de cache à l’aide du client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Cet exemple montre comment :

* Utiliser les différentes options de connexion
* Lire et écrire des objets dans le cache à l’aide des opérations synchrones et asynchrones
* Utiliser les commandes Redis MGET/MSET pour retourner les valeurs de clés spécifiées
* Effectuer des opérations Redis transactionnelles
* Utiliser des listes Redis et des ensembles triés
* Stocker des objets .NET à l’aide des sérialiseurs JsonConvert
* Utiliser les jeux Redis pour implémenter le balisage
* Travailler avec le Cluster Redis

Pour plus d’informations, consultez la documentation [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) sur Github. Pour plus de scénarios d’utilisation, consultez les tests unitaires [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests).

L’exemple [How to use Azure Redis Cache with Python](cache-python-get-started.md) montre comment utiliser le cache Redis Azure avec Python et le client [redis-py](https://github.com/andymccurdy/redis-py).

[Work with .NET objects in the cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) montre comment sérialiser des objets .NET afin de pouvoir les lire et les écrire depuis une instance du cache Redis Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utiliser le Cache Redis comme un fond de panier de montée en puissance pour ASP.NET SignalR
L’exemple [Use Redis Cache as a Scale out Backplane for ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) montre comment utiliser le cache Redis Azure comme fond de panier SignalR. Pour plus d’informations sur le fond de panier, consultez [SignalR Scaleout with Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Exemple de requête client dans le Cache Redis
Cet exemple compare les performances entre l'accès aux données à partir d'un cache et l'accès aux données à partir d'un espace de persistance. Cet exemple comporte deux projets.

* [Démonstration de l’amélioration des performances par la mise en cache des données avec le cache Redis](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Amorcer la base de données et le cache pour la démonstration](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>État de session ASP.NET et mise en cache de la sortie
L’exemple [Use Azure Redis Cache to store ASP.NET SessionState and OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) montre comment utiliser le cache Redis Azure pour stocker les valeurs de session et de cache de sortie d’ASP.NET avec les fournisseurs SessionState et OutputCache de Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Gérer le Cache Redis Azure avec MAML
L’exemple [Manage Azure Redis Cache using Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) montre comment utiliser les bibliothèques de gestion Azure pour gérer (créer, mettre à jour et supprimer) votre cache. 

## <a name="custom-monitoring-sample"></a>Exemple de surveillance personnalisée
L’exemple [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) montre comment accéder aux données de surveillance de votre cache Redis Azure en dehors du portail Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un clone de style Twitter écrit avec PHP et Redis
L’exemple [Retwis](https://github.com/SyntaxC4-MSFT/retwis) est Redis Hello World. Il s’agit d’un clone minimal de réseau social de style Twitter écrit avec Redis et PHP, à l’aide du client [Predis](https://github.com/nrk/predis) . Le code source est conçu pour être très simple et en même temps pour présenter les différentes structures de données Redis.

## <a name="bandwidth-monitor"></a>Surveiller la bande passante
L’exemple [Bandwidth monitor](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) vous permet de surveiller la bande passante utilisée sur le client. Pour mesurer la bande passante, exécutez l’exemple sur l’ordinateur client du cache, effectuez des appels au cache et observez la bande passante signalée par l’exemple de surveillance de la bande passante.

