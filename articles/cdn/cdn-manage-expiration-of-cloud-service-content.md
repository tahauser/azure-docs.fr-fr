---
title: "Gérer l’expiration de contenu web dans Azure Content Delivery Network | Microsoft Docs"
description: "Découvrez comment gérer l’expiration des contenus d’Azure Web Apps/Services cloud, d’ASP.NET ou d’IIS dans le réseau de distribution de contenu (CDN) Azure."
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
ms.openlocfilehash: dca6ca5f21f4a4f1701af57eb40d92094b6a4754
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Gérer l’expiration de contenu web dans Azure Content Delivery Network
> [!div class="op_single_selector"]
> * [Contenu web Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [stockage d’objets blob Azure](cdn-manage-expiration-of-blob-content.md)
> 

Les fichiers d’un serveur web d’origine accessible à tous peuvent être mis en cache dans Azure Content Delivery Network (CDN) jusqu’à l’expiration de leur durée de vie (TTL). La durée de vie est déterminée par l’en-tête `Cache-Control`dans la réponse HTTP du serveur d’origine. Cet article explique comment définir les en-têtes `Cache-Control` pour la fonctionnalité Web Apps de Microsoft Azure App Service, Services cloud Azure, les applications ASP.NET et les sites IIS (Internet Information Services), qui sont tous configurés de façon similaire. Vous pouvez définir l’en-tête `Cache-Control` à l’aide de fichiers de configuration ou par programme. 

Vous pouvez également contrôler les paramètres de cache à partir du portail Azure, en définissant les [règles de mise en cache CDN](cdn-caching-rules.md). Si vous avez configuré une ou plusieurs règles de mise en cache, puis défini leur comportement sur **Remplacer** ou **Ignorer le cache**, les paramètres de mise en cache fournis à l’origine décrits dans cet article sont ignorés. Pour plus d’informations sur les concepts généraux de mise en cache, consultez la section [Fonctionnement de la mise en cache](cdn-how-caching-works.md).

> [!TIP]
> Vous pouvez choisir de ne pas définir de durée de vie sur un fichier. Dans ce cas, Azure CDN applique automatiquement une durée de vie par défaut de sept jours, sauf si vous avez configuré des règles de mise en cache dans le portail Azure. Cette valeur TTL par défaut s’applique uniquement aux optimisations de remise web générales. Pour optimiser les fichiers volumineux, la durée de vie par défaut est de 1 jour, et pour les optimisations du streaming multimédia, la durée de vie par défaut est de 1 an.
> 
> Pour plus d’informations sur la façon dont Azure CDN accélère l’accès aux fichiers et à d’autres ressources, consultez [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Définition d’en-têtes Cache-Control à l’aide de fichiers de configuration
Pour le contenu statique, tel que les images et les feuilles de style, vous pouvez contrôler la fréquence de mise à jour en modifiant les fichiers de configuration **applicationHost.config** ou **web.config** de votre application web. L’élément `<system.webServer>/<staticContent>/<clientCache>` dans l’un des fichiers définit l’en-tête `Cache-Control` pour votre contenu.

### <a name="using-applicationhostconfig-files"></a>Utilisation des fichiers ApplicationHost.config
Le fichier **ApplicationHost.config** est le fichier racine du système de configuration IIS. Les paramètres de configuration du fichier **applicationHost.config** affectent toutes les applications sur le site, mais sont remplacés par les paramètres des fichiers **web.config** existants pour une application web.

### <a name="using-webconfig-files"></a>Utilisation de fichiers Web.config
Avec un fichier **Web.config**, vous pouvez personnaliser le comportement de l’ensemble de votre application web ou un répertoire spécifique de celle-ci. En général, vous avez au moins un fichier **Web.config** dans le dossier racine de votre application web. Pour chaque fichier **web.config** d’un dossier spécifique, les paramètres de configuration affectent tous les éléments du dossier et de tous ses sous-dossiers, sauf s’ils sont remplacés au niveau sous-dossier par un autre fichier **Web.config**. Par exemple, vous pouvez définir un élément `<clientCache>` dans un fichier **Web.config** dans le dossier racine de votre application web pour mettre en cache tout le contenu statique de votre application web pendant trois jours. Vous pouvez également ajouter un fichier **Web.config** dans un sous-dossier avec un contenu plus variable (par exemple, `\frequent`) et définir son élément `<clientCache>` pour mettre en cache le contenu du sous-dossier pendant 6 heures. Il en résulte que le contenu de l’ensemble du site web est mis en cache pendant trois jours, à l’exception du contenu du répertoire `\frequent` qui est mis en cache pendant six heures seulement.  

L’exemple de code XML suivant montre comment configurer l’élément `<clientCache>` dans un fichier de configuration pour spécifier un âge maximal de trois jours :  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Pour utiliser l’attribut **cacheControlMaxAge**, vous devez définir la valeur de l’attribut **cacheControlMode** sur `UseMaxAge`. Ce paramètre a provoqué l’ajout de l’en-tête HTTP et de la directive `Cache-Control: max-age=<nnn>` à la réponse. Le format de la valeur de période pour l’attribut **cacheControlMaxAge** est `<days>.<hours>:<min>:<sec>`. Sa valeur est convertie en secondes et est utilisée comme valeur de la directive `Cache-Control` `max-age`. Pour plus d’informations sur l’élément `<clientCache>`, consultez [Cache client <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Définition d’en-têtes Cache-Control par programme
Pour les applications ASP.NET, contrôlez par programme le comportement de mise en cache dans CDN en définissant la propriété **HttpResponse.Cache** de l’API .NET. Pour plus d’informations sur la propriété **HttpResponse.Cache**, consultez les pages [HttpResponse.Cache, propriété](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) et [HttpCachePolicy, classe](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Pour mettre en cache par programmation le contenu d’application dans ASP.NET, suivez ces étapes :
   1. Vérifiez que le contenu est marqué comme pouvant être mis en cache en définissant `HttpCacheability` sur `Public`. 
   2. Définissez un validateur de cache en appelant une des méthodes `HttpCachePolicy` suivantes :
      - Appelez `SetLastModified` pour définir une valeur d’horodatage pour l’en-tête `Last-Modified`.
      - Appelez `SetETag` pour définir une valeur pour l’en-tête `ETag`.
   3. Si vous le souhaitez, spécifiez un délai d’expiration du cache en appelant `SetExpires` pour définir une valeur pour l’en-tête `Expires`. Sinon, l’approche de cache par défaut décrite précédemment dans ce document s’applique.

Par exemple, pour mettre en cache du contenu pendant une heure, ajoutez le code C# suivant :  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Test de l’en-tête Cache-Control
Vous pouvez facilement vérifier les paramètres de durée de vie de votre contenu web. Avec les [outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de votre navigateur, vérifiez que votre contenu web comprend l’en-tête de réponse `Cache-Control`. Vous pouvez également utiliser un outil tel que **wget**, [Postman](https://www.getpostman.com/) ou [Fiddler](http://www.telerik.com/fiddler) pour examiner les en-têtes de réponse.

## <a name="next-steps"></a>Étapes suivantes
* [Découvrir les détails de l’élément **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Consulter la documentation de la propriété **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Lire la documentation concernant la **classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [En savoir plus sur les concepts de mise en cache](cdn-how-caching-works.md)
