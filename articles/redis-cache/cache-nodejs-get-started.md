---
title: Guide pratique pour utiliser Cache Redis Azure avec Node.js | Microsoft Docs
description: Prise en main du Cache Redis Azure avec Node.js et node_redis
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: wesmc
ms.openlocfilehash: e8b4818ad2b876ce4d55cf8aa2aa0e0162dea963
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Utilisation du Cache Redis Azure avec Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Le Cache Redis Azure permet d'accéder à un cache Redis sécurisé et dédié, géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Node.js. 

## <a name="prerequisites"></a>Prérequis
Installez [node_redis](https://github.com/mranney/node_redis) :

    npm install redis

Ce didacticiel utilise [node_redis](https://github.com/mranney/node_redis). Pour obtenir des exemples d’utilisation des autres clients Node.js, consultez la documentation individuelle pour les [clients Redis Node.js](http://redis.io/clients#nodejs)répertoriés.

## <a name="create-a-redis-cache-on-azure"></a>Créer un Cache Redis sur Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Récupérer les clés d’accès et le nom hôte
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Connexion au cache en toute sécurité à l’aide de SSL
Les dernières versions de [node_redis](https://github.com/mranney/node_redis) prennent en charge la connexion au Cache Redis Azure avec SSL. L’exemple suivant montre comment se connecter au Cache Redis Azure à l’aide du point de terminaison SSL 6380. Remplacez `<name>` par le nom de votre cache et `<key>` par votre clé primaire ou secondaire tel que décrit dans la section précédente [Récupérer les clés d’accès et le nom hôte](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> Le port non SSL est désactivé pour les nouvelles instances Cache Redis Azure. Si vous utilisez un autre client qui ne prend pas en charge SSL, la procédure d’activation du port non SSL est expliquée [ici](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Ajouter un élément au cache et le récupérer
L’exemple suivant montre comment se connecter à une instance du Cache Redis Azure et de stocker ou récupérer un élément dans le cache. Pour plus d’exemples d’utilisation de Redis avec le client [node_redis](https://github.com/mranney/node_redis), consultez [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Sortie :

    OK
    value


## <a name="next-steps"></a>étapes suivantes
* [Activez les diagnostics du cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir [surveiller](cache-how-to-monitor.md) l’intégrité de votre cache.
* Lisez la [documentation Redis](http://redis.io/documentation)officielle.

