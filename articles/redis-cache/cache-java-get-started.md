---
title: Utilisation du Cache Redis Azure avec Java | Microsoft Docs
description: Prise en main du Cache Redis Azure avec Java
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/31/2017
ms.author: wesmc
ms.openlocfilehash: a93dbf78c9cb94f6c20c8569a69e068ceb3b98bc
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Utilisation du Cache Redis Azure avec Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Le Cache Redis Azure permet d’accéder à un cache Redis dédié géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Java.

## <a name="prerequisites"></a>Prérequis
[Jedis](https://github.com/xetorthio/jedis) : client Java pour Redis

Ce didacticiel utilise Jedis, mais vous pouvez utiliser n'importe quel client Java parmi ceux répertoriés ici : [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Créer un Cache Redis sur Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Récupérer les clés d’accès et le nom hôte
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Connexion au cache en toute sécurité à l’aide de SSL
Les dernières versions de [jedis](https://github.com/xetorthio/jedis) prennent en charge la connexion au Cache Redis Azure avec SSL. L’exemple suivant montre comment se connecter au Cache Redis Azure à l’aide du point de terminaison SSL 6380. Remplacez `<name>` par le nom de votre cache et `<key>` par votre clé primaire ou secondaire tel que décrit dans la section précédente [Récupérer les clés d’accès et le nom hôte](#retrieve-the-host-name-and-access-keys).

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> Le port non SSL est désactivé pour les nouvelles instances Cache Redis Azure. Si vous utilisez un autre client qui ne prend pas en charge SSL, la procédure d’activation du port non SSL est expliquée [ici](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Ajouter un élément au cache et le récupérer
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Étapes suivantes
* [Activez les diagnostics du cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin de pouvoir [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’intégrité de votre cache.
* Lisez la [documentation Redis](http://redis.io/documentation)officielle.
* Découvrez comment [configurer une application Spring Initializr pour utiliser Cache Redis](cache-java-spring-boot-initializer-with-redis-cache.md).