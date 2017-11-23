---
title: "Gérer l’expiration de contenu web dans Azure Content Delivery Network | Microsoft Docs"
description: "Découvrez comment gérer l’expiration de contenu Azure Web Apps/Cloud Services, ASP.NET ou IIS dans Azure CDN."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Gérer l’expiration de contenu web dans Azure Content Delivery Network
 dans Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Stockage Blob Azure](cdn-manage-expiration-of-blob-content.md)
> 

Les fichiers d’un serveur web d’origine accessible à tous peuvent être mis en cache dans Azure Content Delivery Network (CDN) jusqu’à l’expiration de leur durée de vie (TTL). La durée de vie est déterminée par [l’en-tête `Cache-Control`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) dans la réponse HTTP du serveur d’origine. Cet article explique comment définir les en-têtes `Cache-Control` pour la fonctionnalité Web Apps de Microsoft Azure App Service, Azure Cloud Services, les applications ASP.NET et les sites Internet Information Services, qui sont tous configurés de façon similaire.

> [!TIP]
> Vous pouvez choisir de ne pas définir de durée de vie sur un fichier. Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut.
> 
> Pour plus d’informations sur la façon dont Azure CDN accélère l’accès aux fichiers et à d’autres ressources, consultez [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Définition d’en-têtes Cache-Control dans les fichiers de configuration
Pour le contenu statique, tel que les images et les feuilles de style, vous pouvez contrôler la fréquence de mise à jour en modifiant les fichiers **applicationHost.config** ou **web.config** de votre application web. L’élément **system.webServer\staticContent\clientCache** du fichier de configuration définit l’en-tête `Cache-Control` pour votre contenu. Pour les fichiers **web.config**, les paramètres de configuration affectent tous les éléments du dossier et de tous ses sous-dossiers, sauf s’ils sont remplacés au niveau sous-dossier. Par exemple, vous pouvez définir un paramètre de durée de vie par défaut dans le dossier racine pour mettre en cache tout le contenu statique des trois derniers jours, et définir un sous-dossier avec un contenu plus variable pour mettre en cache le contenu pendant six heures seulement. Bien que les paramètres du fichier **applicationHost.config** affectent toutes les applications sur le site, ils sont remplacés par les paramètres des fichiers **web.config** existants dans les applications.

L’exemple de code XML suivant montre comment configurer **clientCache** pour spécifier un âge maximal de trois jours :  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Le fait de spécifier **UseMaxAge** déclenche l’ajout d’un en-tête `Cache-Control: max-age=<nnn>` à la réponse basée sur la valeur spécifiée dans l’attribut **CacheControlMaxAge**. Le format de la période pour l’attribut **cacheControlMaxAge** est `<days>.<hours>:<min>:<sec>`. Pour plus d’informations sur le nœud **clientCache**, consultez [Cache client <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Définition d’en-têtes Cache-Control dans le code
Pour les applications ASP.NET, vous pouvez contrôler par programmation le comportement de mise en cache dans CDN en définissant la propriété **HttpResponse.Cache**. Pour plus d’informations sur la propriété **HttpResponse.Cache**, consultez les pages [HttpResponse.Cache, propriété](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) et [HttpCachePolicy, classe](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Pour mettre en cache par programmation le contenu d’application dans ASP.NET, suivez ces étapes :
   1. Vérifiez que le contenu est marqué comme pouvant être mis en cache en définissant `HttpCacheability` sur *Public*. 
   2. Définissez un validateur de cache en appelant une des méthodes suivantes :
      - Appelez `SetLastModified` pour définir un horodatage LastModified.
      - Appelez `SetETag` pour définir une valeur `ETag`.
   3. Si vous le souhaitez, spécifiez un délai d’expiration du cache en appelant `SetExpires`. Sinon, l’approche de cache par défaut décrite précédemment dans ce document s’applique.

Par exemple, pour mettre en cache du contenu pendant une heure, ajoutez le code C# suivant :  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Étapes suivantes
* [Découvrir les détails de l’élément **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Consulter la documentation de la propriété **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Lire la documentation concernant la **classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

