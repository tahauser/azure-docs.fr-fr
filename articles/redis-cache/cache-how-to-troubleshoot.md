---
title: "Résolution des problèmes du cache Redis Azure | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes courants liés au cache Redis Azure."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: e5f6f423697d90e889ebde2cd203891e34278b3c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Résolution des problèmes du cache Redis Azure
Cet article fournit des conseils pour résoudre les catégories suivantes de problèmes liés au cache Redis Azure.

* [Résolution des problèmes côté client](#client-side-troubleshooting) : cette section fournit des instructions concernant l’identification et la résolution des problèmes provoqués par la connexion de l’application au cache Redis Azure.
* [Résolution des problèmes côté serveur](#server-side-troubleshooting) : cette section fournit des instructions concernant l’identification et la résolution des problèmes côté serveur du cache Redis Azure.
* [Exceptions au délai d’expiration de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) : cette section fournit des informations concernant la résolution des problèmes liés à l’utilisation du client StackExchange.Redis.

> [!NOTE]
> Dans ce guide, plusieurs procédures de résolution de problèmes comprennent des instructions pour exécuter des commandes Redis et surveiller diverses mesures de performances. Pour plus d’informations et d’instructions, consultez les articles dans la section [Informations supplémentaires](#additional-information) .
> 
> 

## <a name="client-side-troubleshooting"></a>Résolution des problèmes côté client
Cette section traite des problèmes qui surviennent à cause d’un souci sur l’application cliente.

* [Saturation de la mémoire sur le client](#memory-pressure-on-the-client)
* [Augmentation de trafic](#burst-of-traffic)
* [Utilisation importante du processeur du client](#high-client-cpu-usage)
* [Bande passante dépassée côté client](#client-side-bandwidth-exceeded)
* [Taille importante de la demande/réponse](#large-requestresponse-size)
* [Qu’est-il arrivé à mes données dans Redis ?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Saturation de la mémoire sur le client
#### <a name="problem"></a>Problème
Une saturation de la mémoire sur l’ordinateur client génère des problèmes de performances de toutes sortes, susceptibles de retarder le traitement des données envoyées par l’instance Redis sans délai. En cas de saturation de la mémoire, le système doit généralement paginer les données de la mémoire physique dans la mémoire virtuelle située sur le disque. Cette *pagination* ralentit considérablement le système.

#### <a name="measurement"></a>Mesure
1. Surveillez l’utilisation de la mémoire sur l’ordinateur et vérifiez qu’elle ne dépasse pas la mémoire disponible. 
2. Surveillez le compteur de performances `Page Faults/Sec`. La plupart des systèmes rencontrent des erreurs de page, même lors d’un fonctionnement normal. Donc, surveillez les pics dans ce compteur de performances, car ils correspondent à des délais d’attente.

#### <a name="resolution"></a>Résolution :
Agrandissez la taille de la machine virtuelle de votre client en lui attribuant davantage de mémoire ou analysez vos graphiques d’utilisation de la mémoire afin d’en réduire la consommation.

### <a name="burst-of-traffic"></a>Augmentation de trafic
#### <a name="problem"></a>Problème
Les augmentations de trafic combinées à des paramètres `ThreadPool` insatisfaisants peuvent retarder le traitement des données déjà envoyées par le serveur Redis, mais pas encore consommées côté client.

#### <a name="measurement"></a>Mesure
Surveillez comment évoluent vos statistiques `ThreadPool` au fil du temps à l’aide d’un code [tel que celui-ci](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Vous pouvez également consulter le message `TimeoutException` émis par StackExchange.Redis. Voici un exemple : 

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Dans le message précédent, plusieurs problèmes sont intéressants :

1. Notez que dans les sections `IOCP` et `WORKER`, la valeur de `Busy` est supérieure à la valeur de `Min`. Cette différence signifie que vos paramètres de `ThreadPool` ont besoin d’être ajustés.
2. Vous pouvez également voir `in: 64221`. Cette valeur indique que 64,211 octets ont été reçus au niveau de la couche de socket du noyau, mais ne sont pas encore lus par l’application (par exemple StackExchange.Redis). En général, cette différence signifie que votre application ne lit pas les données du réseau aussi rapidement que le serveur les lui envoie.

#### <a name="resolution"></a>Résolution :
Configurez les [paramètres de votre pool de threads](https://gist.github.com/JonCole/e65411214030f0d823cb) pour vous assurer qu’ils s’adaptent rapidement en cas d’augmentation du trafic.

### <a name="high-client-cpu-usage"></a>Utilisation importante du processeur du client
#### <a name="problem"></a>Problème
Une utilisation élevée du processeur sur le client révèle que le système n’arrive pas à effectuer le travail qu’on lui demande de faire. En d’autres termes, le client risque de ne pas pouvoir traiter une réponse de Redis dans les délais, même si Redis a envoyé la réponse rapidement.

#### <a name="measurement"></a>Mesure
Surveillez l’utilisation du processeur par tout le système dans le portail Azure ou à l’aide du compteur de performances associé. Ne tenez pas compte du processeur de *processus* , car un processus peut utiliser faiblement le processeur pendant que le processeur système est fortement sollicité. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration. Lorsque le processeur est très utilisé, vous pouvez également voir s’afficher des valeurs `in: XXX` élevées dans les messages d’erreur `TimeoutException`, comme ceux décrits dans la section [Augmentation de trafic](#burst-of-traffic).

> [!NOTE]
> StackExchange.Redis 1.1.603 et versions ultérieures inclut la mesure `local-cpu` dans les messages d’erreur `TimeoutException`. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
> 
> 

#### <a name="resolution"></a>Résolution :
Procédez à la mise à niveau vers une plus grande taille de machine virtuelle avec davantage de capacités processeur ou recherchez la cause des pics d’utilisation du processeur. 

### <a name="client-side-bandwidth-exceeded"></a>Bande passante dépassée côté client
#### <a name="problem"></a>Problème
Selon l’architecture des ordinateurs clients, ces ordinateurs peuvent afficher des limites concernant la bande passante réseau qu’ils peuvent utiliser. Si le client dépasse la bande passante disponible en surchargeant la capacité du réseau, les données ne sont pas traitées côté client aussi rapidement que le serveur les envoie. Cette situation peut entraîner des délais d’attente.

#### <a name="measurement"></a>Mesure
Surveillez comment votre utilisation de la bande passante évolue au fil du temps, à l’aide d’un code [tel que celui-ci](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ce code peut ne pas s’exécuter correctement dans certains environnements aux autorisations restreintes (tels que les sites web Azure).

#### <a name="resolution"></a>Résolution :
Augmentez la taille de la machine mémoire du client ou réduisez la consommation de la bande passante du réseau.

### <a name="large-requestresponse-size"></a>Taille importante de la demande/réponse
#### <a name="problem"></a>Problème
Une demande/réponse volumineuse peut entraîner des délais d’expiration. Par exemple, supposons que la durée du délai d’expiration que vous avez configurée sur votre client est de 1 seconde. Votre application demande deux clés (par exemple, « A » et « B ») en même temps (à l’aide de la même connexion réseau physique). La plupart des clients prennent en charge le traitement en « pipeline » des demandes, de sorte que les deux demandes A et B sont envoyées l’une après l’autre au serveur sans attendre les réponses. Le serveur renvoie les réponses dans le même ordre. Si la réponse A est suffisamment volumineuse, elle peut saturer la majeure partie du délai d’expiration des demandes suivantes. 

L’exemple suivant illustre ce cas de figure. Dans ce scénario, les demandes A et B sont envoyées rapidement, le serveur commence à renvoyer rapidement les réponses A et B, mais en raison du temps de transfert des données, la réponse B reste coincée derrière la réponse A et expire même si le serveur a répondu rapidement.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mesure
Cette demande/réponse est difficile à mesurer. Vous devez adapter votre code client pour suivre les requêtes et réponses volumineuses. 

#### <a name="resolution"></a>Résolution :
1. Redis est optimisé pour un grand nombre de petites valeurs plutôt que pour quelques grandes valeurs. La meilleure solution consiste à diviser vos données en valeurs plus petites. Lisez le billet [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) pour savoir pourquoi les valeurs moins élevées sont recommandées.
2. Augmentez la taille de votre machine virtuelle (pour le client et le serveur de cache Redis) afin d’obtenir une bande passante plus élevée et ainsi de réduire le temps de transfert des réponses volumineuses. L’obtention d’une bande passante supérieure uniquement sur le serveur ou sur le client peut ne pas suffire. Mesurez votre utilisation de la bande passante et comparez-la aux capacités de la taille de votre machine virtuelle.
3. Augmentez le nombre d’objets `ConnectionMultiplexer` utilisés et traitez les demandes en tourniquet sur différentes connexions.

### <a name="what-happened-to-my-data-in-redis"></a>Qu’est-il arrivé à mes données dans Redis ?
#### <a name="problem"></a>Problème
Je m’attendais à trouver certaines données dans mon instance du cache Redis Azure, mais elles semblent absentes.

#### <a name="resolution"></a>Résolution :
Consultez la page [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) pour identifier les causes et solutions possibles.

## <a name="server-side-troubleshooting"></a>Résolution des problèmes côté serveur
Cette section traite des problèmes qui surviennent en raison d’un souci sur le serveur du cache.

* [Saturation de la mémoire sur le serveur](#memory-pressure-on-the-server)
* [Utilisation élevée du processeur / charge importante du serveur](#high-cpu-usage-server-load)
* [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Saturation de la mémoire sur le serveur
#### <a name="problem"></a>Problème
La saturation de la mémoire sur le serveur génère toutes sortes de problèmes de performance susceptibles de retarder le traitement des demandes. En cas de saturation de la mémoire, le système doit généralement paginer les données de la mémoire physique dans la mémoire virtuelle située sur le disque. Cette *pagination* ralentit considérablement le système. Plusieurs causes peuvent être à l’origine de cette saturation de la mémoire : 

1. Le cache de données est saturé. 
2. Redis constate une fragmentation importante de la mémoire, le plus souvent provoquée par le stockage d’objets volumineux. Redis est optimisé pour les petits objets. Pour en savoir plus, consultez le billet [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ). 

#### <a name="measurement"></a>Mesure
Redis fournit deux mesures qui peuvent vous aider à identifier ce problème. Il s’agit de `used_memory` et de `used_memory_rss`. [Ces mesures](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) sont disponibles dans le portail Azure ou via la commande [Redis INFO](http://redis.io/commands/info).

#### <a name="resolution"></a>Résolution :
Vous pouvez apporter plusieurs modifications pour maintenir une utilisation correcte de la mémoire :

1. [Configurez une stratégie de mémoire](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) et définissez des délais d’expiration sur vos clés. Cette configuration peut ne pas suffire si vous constatez une fragmentation.
2. [Configurez une valeur maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) suffisamment grande pour compenser la fragmentation de la mémoire.
3. Divisez vos objets volumineux mis en cache en objets plus petits.
4. [mise à l’échelle](cache-how-to-scale.md) pour augmenter la taille du cache.
5. Si vous utilisez un [cache premium avec cluster Redis activé](cache-how-to-premium-clustering.md), vous pouvez [augmenter le nombre de partitions](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilisation élevée du processeur / charge importante du serveur
#### <a name="problem"></a>Problème
En cas d’utilisation élevée du processeur, le client risque de ne pas pouvoir traiter une réponse de Redis dans les délais, même si Redis a renvoyé la réponse rapidement.

#### <a name="measurement"></a>Mesure
Surveillez l’utilisation du processeur par tout le système dans le portail Azure ou à l’aide du compteur de performances associé. Ne tenez pas compte du processeur de *processus* , car un processus peut utiliser faiblement le processeur pendant que le processeur système est fortement sollicité. Regardez les pics d’utilisation du processeur qui correspondent à des délais d’expiration.

#### <a name="resolution"></a>Résolution :
* Passez en revue les conseils et alertes de la rubrique [Redis Cache Advisor](cache-configure.md#redis-cache-advisor).
* Consultez également les autres recommandations de cette rubrique, ainsi que la rubrique [Meilleures pratiques pour Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) afin de vérifier si vous avez utilisé toutes les options pour optimiser davantage votre cache et votre client. 
* Examinez les graphiques [Performance du Cache Redis Azure](cache-faq.md#azure-redis-cache-performance) et vérifiez si vous approchez des seuils supérieurs de votre niveau actuel. Si nécessaire, effectuez une [mise à l’échelle](cache-how-to-scale.md) vers une plus grande taille de cache avec davantage de capacités processeur. Si vous utilisez déjà le niveau Premium, vous souhaiterez peut-être [descendre en puissance avec clustering](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Bande passante dépassée côté serveur
#### <a name="problem"></a>Problème
Selon la taille des instances de cache, ces instances peuvent afficher des limites concernant la bande passante réseau qu’elles peuvent utiliser. Si le serveur dépasse la bande passante disponible, les données ne sont pas envoyées au client aussi rapidement. Cette situation peut entraîner des délais d’attente.

#### <a name="measurement"></a>Mesure
Vous pouvez surveiller la mesure `Cache Read` , qui évalue la quantité de données lues dans le cache en mégaoctets par seconde (Mo/s) pendant l’intervalle de création de rapport. Cette valeur correspond à la bande passante réseau utilisée par ce cache. Si vous souhaitez configurer des alertes pour des limites de bande passante réseau côté serveur, vous pouvez les créer à l’aide du compteur `Cache Read`. Comparez vos relevés aux valeurs indiquées dans [ce tableau](cache-faq.md#cache-performance) afin de connaître les limites de bande passante observées pour plusieurs tailles et niveaux de tarification du cache.

#### <a name="resolution"></a>Résolution :
Si vous êtes constamment près de la bande passante maximale observée pour votre taille et votre niveau de tarification de cache, envisagez une [mise à l’échelle](cache-how-to-scale.md) vers une taille ou un niveau de tarification offrant une bande passante réseau supérieure, en utilisant les valeurs de [ce tableau](cache-faq.md#cache-performance) comme guide.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceptions au délai d’expiration de StackExchange.Redis
Pour les opérations synchrones, StackExchange.Redis utilise un paramètre de configuration appelé `synctimeout`, qui a une valeur par défaut de 1000 ms. Si un appel synchrone ne se termine pas dans le délai imparti, le client StackExchange.Redis génère une erreur de délai d’expiration similaire à l’exemple suivant :

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ce message d’erreur contient des mesures qui peuvent vous aider à identifier la cause du problème et à en trouver la solution. Le tableau suivant contient des détails sur les mesures des messages d’erreur.

| Mesure de message d’erreur | Détails |
| --- | --- |
| inst |Dans le dernier intervalle de temps, aucune commande n’a été émise. |
| mgr |Le gestionnaire de socket exécute `socket.select`, c’est-à-dire qu’il demande au système d’exploitation de lui désigner un socket occupé. En fait, le lecteur n’effectue aucune lecture active sur le réseau, car il pense qu’il n’y a rien à faire. |
| file d'attente |73 opérations sont en cours. |
| qu |6 des opérations en cours sont dans en file d’attente et n’ont pas encore été envoyées au réseau sortant. |
| qs |67 des opérations en cours ont été envoyées au serveur, mais aucune réponse n’est disponible. La réponse peut être `Not yet sent by the server` ou `sent by the server but not yet processed by the client.`. |
| qc |Aucune des opérations en cours n’a reçu de réponse, mais elles sont marquées comme terminées en raison de leur attente dans la boucle d’exécution. |
| wr |Un enregistreur est actif (ce qui signifie que les 6 demandes en attente ne sont pas ignorées) octets/enregistreurs actifs |
| commencer |Aucun lecteur n’est actif et aucun octet n’est disponible en lecture sur la carte réseau octets/lecteurs actifs |

### <a name="steps-to-investigate"></a>Procédure d’analyse
1. Il est recommandé de suivre la procédure ci-dessous pour se connecter, en cas d’utilisation du client StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ````

    Pour plus d’informations, consultez [Utilisation du Cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Vérifiez que votre Cache Redis Azure et l’application cliente sont dans la même région dans Azure. Par exemple, vous pouvez rencontrer des délais d’expiration lorsque votre cache est dans la région Est des États-Unis, que le client se trouve dans l’Ouest des États-Unis et que la demande n’est pas traitée pendant l’intervalle `synctimeout`. Vous pouvez également rencontrer des délais d’expiration lorsque vous procédez à un débogage à partir de votre ordinateur de développement local. 
   
    Il est vivement recommandé de placer le cache et le client dans la même région Azure. Si votre implémentation nécessite des appels interrégionaux, vous devez régler l’intervalle `synctimeout` sur une valeur supérieure à la valeur par défaut (1000 ms), en incluant une propriété `synctimeout` dans la chaîne de connexion. L’exemple suivant montre un extrait de chaîne de connexion au cache StackExchange.Redis avec une valeur de 2 000 ms pour `synctimeout` .
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Vérifiez que vous utilisez la dernière version en date du [package NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Nous corrigeons en permanence le code pour le rendre plus robuste aux délais d’expiration. Il est donc primordial d’utiliser la dernière version.
3. Si des demandes sont impactées par les limitations de bande passante sur le serveur ou le client, leur traitement prend davantage de temps, ce qui entraîne des délais d’expiration. Pour voir si le délai d’expiration est dû à la bande passante réseau sur le serveur, consultez [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded). Pour voir si le délai d’expiration est dû à la bande passante réseau sur le client, consultez [Bande passante dépassée côté client](#client-side-bandwidth-exceeded).
4. Constatez-vous une utilisation intensive du processeur sur le serveur ou sur le client ?
   
   * Vérifiez si vous constatez une utilisation intensive du processeur sur votre client, qui pourrait empêcher le traitement de la demande pendant l’intervalle `synctimeout`, entraînant un délai d’expiration. Augmentez la taille de votre client ou répartissez la charge pour résoudre ce problème. 
   * Vérifiez si vous constatez une utilisation intensive du processeur sur votre serveur, à l’aide de la `CPU` [mesure de performance du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). L’entrée de demandes pendant que Redis utilise intensivement le processeur peut entraîner une expiration du traitement des demandes. Pour résoudre ce problème, vous pouvez répartir la charge sur plusieurs partitions dans un cache premium, ou effectuer une mise à niveau vers une taille ou un niveau de tarification supérieur. Pour plus d’informations, consultez [Bande passante dépassée côté serveur](#server-side-bandwidth-exceeded).
5. Le traitement de certaines commandes est-il trop long sur le serveur ? Les commandes qui prennent beaucoup de temps sur le serveur redis peuvent entraîner des délais d’expiration. Les commandes `mget` avec un nombre important de clés, `keys *` ou des scripts mal rédigés prennent notamment beaucoup de temps pour s’exécuter. Vous pouvez vous connecter à votre instance de cache Redis Azure à l’aide du client redis-cli, ou utiliser la [Console Redis](cache-configure.md#redis-console) et exécuter la commande [SlowLog](http://redis.io/commands/slowlog) pour identifier les demandes prenant davantage de temps que prévu. Le serveur Redis et StackExchange.Redis sont optimisés pour traiter un grand nombre de petites requêtes plutôt qu’un nombre réduit de demandes volumineuses. Fractionner vos données en segments plus petits peut améliorer les choses. 
   
    Pour plus d’informations sur la connexion au point de terminaison SSL du cache Redis Azure à l’aide de redis-cli et de stunnel, consultez le billet de blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Pour plus d’informations, consultez [SlowLog](http://redis.io/commands/slowlog).
6. Une charge élevée du serveur Redis peut provoquer des délais d’expiration. Vous pouvez surveiller la charge du serveur à l’aide de la `Redis Server Load` [mesure des performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Une charge serveur de 100 (valeur maximale) signifie que le serveur redis a été occupé à plein temps à traiter des demandes. Pour voir si certaines demandes monopolisent les capacités du serveur, exécutez la commande SlowLog comme indiqué dans le paragraphe précédent. Pour plus d’informations, consultez [Utilisation élevée du processeur / charge importante du serveur](#high-cpu-usage-server-load).
7. Un autre événement côté client a-t-il pu causer un blocage du réseau ? Sur le client (web, rôle de travail ou MV Iaas), vérifiez si un événement comme la mise à l’échelle du nombre d’instances du client, le déploiement d’une nouvelle version du client ou l’activation de la mise à l’échelle automatique, est présent.Lors de notre test, nous avons constaté que la mise à l’échelle automatique, l’augmentation ou la réduction de la taille des instances peut entraîner la perte de la connexion au réseau pendant plusieurs secondes. Le code de StackExchange.Redis résiste à de tels événements et rétablit la connexion. Pendant ce temps de reconnexion, toutes les demandes dans la file d’attente peuvent expirer.
8. Une demande volumineuse précédant plusieurs petites demandes dans le cache Redis a-t-elle pu voir son délai expirer ? Le paramètre `qs` du message d’erreur vous indique le nombre de requêtes envoyées du client au serveur, mais qui n’ont pas encore traité une réponse. Cette valeur peut continuer d’augmenter car StackExchange.Redis utilise une seule connexion TCP et ne peut lire qu’une réponse à la fois. L’expiration de la première opération n’empêche pas l’échange des données avec le serveur, et les autres demandes sont bloquées jusqu’à ce que la demande volumineuse soit terminée, ce qui provoque des délais d’expiration. Une solution pour réduire le risque de délais d’expiration consiste à vérifier que le cache est assez grand pour votre charge de travail et à fractionner les valeurs importantes en valeurs plus petites. Une autre solution consiste à utiliser un pool d’objets `ConnectionMultiplexer` dans votre client et à choisir le `ConnectionMultiplexer` le moins chargé lors de l’envoi d’une nouvelle demande. Ceci doit empêcher un délai de faire expirer les autres demandes.
9. Si vous utilisez `RedisSessionStateprovider`, vérifiez que vous avez configuré correctement le délai d’expiration de nouvelle tentative. `retrytimeoutInMilliseconds` doit être supérieur à `operationTimeoutinMilliseonds`, sans quoi aucune nouvelle tentative ne se produit. Dans l’exemple suivant, `retrytimeoutInMilliseconds` est réglé sur 3 000. Pour plus d’informations, voir [Fournisseur d’état de session ASP.NET pour Cache Redis Azure](cache-aspnet-session-state-provider.md) et [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Comment utiliser les paramètres de configuration du fournisseur d’état de session et du fournisseur de caches de sortie).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Vérifiez l’utilisation de la mémoire sur le serveur du cache Redis Azure en [surveillant](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` et `Used Memory`. Si une stratégie d’éviction est en place, Redis commence la suppression de clés lorsque `Used_Memory` atteint la taille du cache. Dans l’idéal, `Used Memory RSS` doit être légèrement supérieur à `Used memory`. Une différence importante traduit une fragmentation de la mémoire (interne ou externe). Lorsque `Used Memory RSS` est inférieur à `Used Memory`, cela signifie que le système d’exploitation a permuté une partie de la mémoire cache. Si cette permutation se produit, vous pouvez rencontrer des latences importantes. Comme Redis ne contrôle pas le mode de mappage de ses allocations aux pages mémoire, une valeur élevée de `Used Memory RSS` traduit souvent un pic d’utilisation de la mémoire. Lorsque Redis libère de la mémoire, celle-ci est redonnée à l’allocateur qui peut (ou pas) rendre la mémoire au système. Il peut y avoir une différence entre la valeur de `Used Memory` et la consommation de mémoire telle qu’elle est indiquée par le système d’exploitation. Ceci peut s’expliquer par le fait que la mémoire a été utilisée et libérée par Redis, mais pas restituée au système. Pour réduire les problèmes de mémoire, vous pouvez effectuer les étapes suivantes :
   
   * Augmentez la taille du cache, afin de ne plus être soumis aux limitations de mémoire du système.
   * Définissez des délais d’expiration sur les clés afin que les anciennes valeurs soient supprimées de manière proactive.
   * Surveillez la mesure de cache `used_memory_rss`. Lorsque cette valeur s’approche de la taille du cache, vous risquez de voir apparaître des problèmes de performance. Répartissez les données sur plusieurs partitions, si vous utilisez un cache premium, ou augmentez la taille du cache.
   
   Pour plus d’informations, consultez [Saturation de la mémoire sur le serveur](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informations supplémentaires
* [Que propose Cache Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Comment puis-je évaluer et tester les performances de mon cache ?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Comment exécuter des commandes Redis ?](cache-faq.md#how-can-i-run-redis-commands)
* [Surveillance du cache Redis Azure](cache-how-to-monitor.md)

