---
title: "Fournisseur d’état de session ASP.NET du cache | Microsoft Docs"
description: "Apprenez à stocker l’état de session ASP.NET à l’aide du Cache Redis Azure"
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: wesmc
ms.openlocfilehash: 485375f2f2ffb83b7d0fdeef8daab5880a8bbc27
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Fournisseur d’état de session ASP.NET pour Cache Redis Azure
Cache Redis Azure intègre un fournisseur d'état de session, qui vous permet de stocker l'état de votre session dans un cache plutôt qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur d’état de session de la mise en cache, configurez d’abord votre cache, puis configurez votre application ASP.NET pour la mise en cache à l’aide du package NuGet de l’état de session Cache Redis.

Dans une application cloud réelle, il n’est souvent pas pratique d’éviter de stocker une forme d’état de session utilisateur, mais certaines approches ont davantage d’incidence que d’autres sur les performances et l'extensibilité. Si vous devez stocker un état, la meilleure solution consiste à veiller à ce qu’il reste de petite taille et à le stocker dans des cookies. Si cette approche est impossible, le mieux est d’utiliser l’état de session ASP.NET avec un fournisseur de cache distribué en mémoire. La pire solution du point de vue des performances et de l’extensibilité consiste à utiliser un fournisseur d’état de session s'appuyant sur une base de données. Cette rubrique fournit des conseils sur l’utilisation du fournisseur d’état de session ASP.NET pour le Cache Redis Azure. Pour plus d’informations sur les autres options d’état de session, consultez [Options d’état de session ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Stockage de l'état de session ASP.NET dans le cache
Pour configurer une application cliente dans Visual Studio avec le package NuGet de l’état de session Cache Redis, cliquez sur **Gestionnaire de package NuGet**, **Console du Gestionnaire de package** dans le menu **Outils**.

Exécutez la commande suivante depuis la fenêtre `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Si vous utilisez la fonction de clustering du niveau Premium, vous devez utiliser [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) version 2.0.1 ou ultérieure, sans quoi une exception est levée. Passer à la version 2.0.1 ou ultérieure est une modification avec rupture. Pour plus d’informations, consultez [Détails de la modification avec rupture pour la version 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Au moment de la mise à jour de cet article, la version actuelle de ce package est 2.2.3.
> 
> 

Le package NuGet du fournisseur d’état de session Redis a une dépendance sur le package StackExchange.Redis.StrongName. Le package StackExchange.Redis.StrongName est automatiquement installé s’il ne figure pas déjà dans votre projet.

>[!NOTE]
>Il existe, en plus du package StackExchange.Redis.StrongName avec nom fort, une version de StackExchange.Redis sans nom fort. Si votre projet utilise la version StackExchange.Redis sans nom fort, vous devez la désinstaller, sans quoi vous aurez des conflits de noms dans votre projet. Pour plus d’informations sur ces packages, consultez la section [Configuration des clients de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Le package NuGet télécharge et ajoute les références d’assembly nécessaires et ajoute la section suivante dans votre fichier web.config. Cette section contient la configuration requise pour que votre application ASP.NET utilise le fournisseur d’état de session du cache Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

La section commentée fournit un exemple d’attributs et de paramétrage pour chacun de ces attributs.

Configurez les attributs avec les valeurs du panneau de votre cache sur le portail Microsoft Azure et configurez les autres valeurs selon votre choix. Pour obtenir des instructions sur l’accès aux propriétés de votre cache, consultez la section [Configuration des paramètres de cache Redis](cache-configure.md#configure-redis-cache-settings).

* **host** : spécifiez le point de terminaison de votre cache.
* **port** : utilisez votre port non SSL ou votre port SSL, selon les paramètres ssl.
* **accessKey** : utilisez la clé primaire ou secondaire pour votre cache.
* **ssl** : choisissez « true » si vous souhaitez sécuriser les communications cache/client avec ssl ; sinon, choisissez « false ». Veillez à spécifier le port approprié.
  * Le port non SSL est désactivé par défaut pour les nouveaux caches. Spécifiez true pour utiliser le port SSL pour ce paramètre. Pour plus d’informations sur l’activation du port non SSL, consultez la section relative aux [ports d’accès](cache-configure.md#access-ports) dans la rubrique [Configuration d’un cache](cache-configure.md).
* **throwOnError** : true si vous voulez lever une exception en cas d’échec, ou false si vous souhaitez que l’opération échoue en silence. Pour contrôler un échec, vous pouvez vérifier la propriété statique Microsoft.Web.Redis.RedisSessionStateProvider.LastException. La valeur par défaut est true.
* **retryTimeoutInMilliseconds** : intervalle, en millisecondes, au cours duquel interviennent les nouvelles tentatives d’exécution des opérations ayant échoué. La première nouvelle tentative intervient après 20 millisecondes. Les tentatives suivantes se produisent à chaque seconde jusqu’à l’expiration de l’intervalle retryTimeoutInMilliseconds. Une dernière tentative d’exécution est effectuée immédiatement après cet intervalle. Si le problème persiste, l’exception est renvoyée à l’appelant, en fonction du paramètre throwOnError. La valeur 0 par défaut signifie qu’aucune nouvelle tentative n’est effectuée.
* **databaseId** : spécifie la base de données à utiliser pour les données de sortie du cache. Si ce champ n’est pas spécifié, la valeur 0 sera utilisée par défaut.
* **applicationName** : les clés sont stockées dans redis sous `{<Application Name>_<Session ID>}_Data`. Ce schéma d’affectation permet à plusieurs applications de partager la même instance Redis. Ce paramètre est facultatif. Si vous n’indiquez aucune valeur, une valeur par défaut sera utilisée.
* **connectionTimeoutInMilliseconds** : ce paramètre vous permet de remplacer le paramètre connectTimeout dans le client StackExchange.Redis. S’il n’est pas spécifié, le paramètre par défaut connectTimeout 5000 est utilisé. Pour plus d’informations, consultez le [modèle de configuration StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** : ce paramètre vous permet de remplacer le paramètre syncTimeout dans le client StackExchange.Redis. S’il n’est pas spécifié, le paramètre par défaut syncTimeout 1000 est utilisé. Pour plus d’informations, consultez le [modèle de configuration StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Pour plus d’informations sur ces propriétés, consultez la publication du blog d’origine [Announcing ASP.NET Session State Provider for Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)(en anglais).

N’oubliez pas de commenter la section relative au fournisseur d’état de session standard InProc dans votre fichier web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Une fois ces étapes effectuées, votre application est configurée pour utiliser le fournisseur d’état de session du Cache Redis. Lorsque vous utilisez l’état de session dans votre application, il est stocké dans une instance de Cache Redis Azure.

> [!IMPORTANT]
> Les données stockées dans le cache doivent être sérialisables, contrairement aux données qui peuvent être stockées dans le fournisseur d’état de session en mémoire ASP.NET par défaut. Lorsque vous utilisez le fournisseur d’état de session pour Redis, assurez-vous que les types de données qui sont stockés dans l’état de session sont sérialisables.
> 
> 

## <a name="aspnet-session-state-options"></a>Options d’état de session ASP.NET
* Fournisseur d’état de session en mémoire : ce fournisseur stocke l’état de session dans la mémoire. Ce fournisseur présente l’avantage d’être à la fois simple et rapide. Vous ne pourrez cependant pas faire évoluer vos applications Web si vous utilisez le fournisseur en mémoire dans la mesure où il n’est pas distribué.
* Fournisseur d’état de session dans le serveur SQL : ce fournisseur stocke l’état de session dans le serveur SQL. Utilisez ce fournisseur si vous souhaitez stocker l’état de session dans un stockage permanent. Vous pouvez faire évoluer votre application Web, mais l’utilisation du serveur SQL pour la session a un impact sur les performances de votre application Web.
* Fournisseur d’état de session distribué en mémoire (par exemple, fournisseur d’état de session du Cache Redis) : ce fournisseur associe tous les avantages. Votre application Web peut bénéficier d’un fournisseur d’état de session à la fois simple, rapide et évolutif. Étant donné que ce fournisseur stocke l’état de session dans un cache et que votre application doit prendre en considération toutes les caractéristiques associées lorsqu’elle communique avec un cache distribué en mémoire (par exemple, les défaillances réseau temporaires). Pour obtenir des recommandations sur l’utilisation du Cache, consultez la page [Caching Guidance](../best-practices-caching.md) dans le [Guide de conception et d’implémentation des applications Azure Cloud](https://github.com/mspnp/azure-guidance) de Microsoft Patterns & Practices.

Pour plus d'informations sur l’état de session et obtenir d’autres meilleures pratiques, consultez la page [Web Development Best Practices (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article [Fournisseur de caches de sortie ASP.NET pour le Cache Redis Azure](cache-aspnet-output-cache-provider.md).

