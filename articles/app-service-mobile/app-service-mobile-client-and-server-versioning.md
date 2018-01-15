---
title: "Contrôle de version des Kits de développement logiciel (SDK) clients et serveurs dans Mobile Apps et Mobile Services | Microsoft Docs"
description: "Liste des Kits de développement logiciel (SDK) clients et des compatibilités avec les versions des Kits de développement logiciel (SDK) serveurs pour Mobile Services et Azure Mobile Apps"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 37bf36af535eb9b5c8b0ba38434b71f1a6686811
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Contrôle de version client et serveur dans Mobile Apps et Mobile Services
La dernière version d'Azure Mobile Services est la fonctionnalité **Mobile Apps** d'Azure App Service.

Les Kits de développement logiciel (SDK) clients et serveurs Mobile Apps se fondent à l'origine sur ceux de Mobile Services, mais ils ne sont *pas* compatibles les uns avec les autres.
Autrement dit, vous devez utiliser un SDK client *Mobile Apps* avec un SDK serveur *Mobile Apps* et il en est de même pour *Mobile Services*. Ce contrat s'applique au moyen d'une valeur d'en-tête spéciale utilisée par les SDK clients et serveurs, `ZUMO-API-VERSION`.

Remarque : chaque fois que ce document fait référence à un backend *Mobile Services* , il n'est pas nécessaire qu'il soit hébergé sur Mobile Services. Il est désormais possible de faire migrer un service mobile sur App Service sans aucune modification de code, mais dans ce cas le service utilisera toujours les versions du SDK *Mobile Services*.

Pour en savoir plus sur la migration vers App Service sans aucune modification de code, consultez l’article [Migrer un service Mobile Services sur Azure App Service].

## <a name="header-specification"></a>Spécification de l’en-tête
La clé `ZUMO-API-VERSION` peut être spécifiée dans l'en-tête HTTP ou dans la chaîne de requête. La valeur est une chaîne de version sous la forme **x.y.z**.

Par exemple : 

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Désactivation de la vérification de version
Vous pouvez désactiver la vérification de version en définissant la valeur **true** pour le paramètre d’application **MS_SkipVersionCheck**. Spécifiez cette valeur dans votre fichier web.config ou dans la section Paramètres de l’application du portail Azure.

> [!NOTE]
> Il existe un certain nombre de différences de comportement entre Mobile Services et Mobile Apps, en particulier dans les domaines de la synchronisation hors connexion, de l'authentification et des notifications Push. Avant de désactiver la vérification de version, assurez-vous par des tests complets que ces modifications de comportement ne suppriment pas de fonctionnalités dans votre application.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Résumé des compatibilités pour toutes les versions
Le tableau ci-dessous indique les compatibilités entre tous les types de clients et de serveurs. Un backend est classé soit comme Mobile **Services**, soit comme Mobile **Apps** en fonction du SDK serveur qu’il utilise.

|  | **Mobile Services** Node.js ou .NET | **Mobile Apps** Node.js ou .NET |
| --- | --- | --- |
| [Clients Mobile Services] |OK |Erreur\* |
| [Clients Mobile Apps] |Erreur\* |OK |

\*Peut être contrôlée en spécifiant **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Client et serveur Mobile Services
Les SDK clients du tableau ci-dessous sont compatibles avec **Mobile Services**.

Remarque : les SDK clients Mobile Services n’envoient *pas* de valeur d’en-tête pour `ZUMO-API-VERSION`. Si le service reçoit cette valeur d'en-tête ou de chaîne de requête, une erreur est renvoyée, sauf en cas de désactivation expresse comme indiqué ci-dessus.

### <a name="MobileServicesClients"></a> SDK clients Mobile *Services*
| Plateforme cliente | Version | Valeur d'en-tête de version |
| --- | --- | --- |
| Client géré (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |n/a |
| iOS |[2.2.2](http://aka.ms/gc6fex) |n/a |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |n/a |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |n/a |

### <a name="mobile-services-server-sdks"></a>SDK serveurs Mobile *Services*
| Plateforme de serveur | Version | En-têtes de versions acceptés |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**Aucun en-tête de version ** |
| Node.js |(bientôt disponible) |**Aucun en-tête de version** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportement des backends Mobile Services
| ZUMO-API-VERSION | Valeur de MS_SkipVersionCheck | response |
| --- | --- | --- |
| Non spécifié |Quelconque |200 - OK |
| Valeur quelconque |True |200 - OK |
| Valeur quelconque |False/Non spécifié |400 - Requête incorrecte |

## <a name="2.0.0"></a>Client et serveur Azure Mobile Apps
### <a name="MobileAppsClients"></a> SDK clients Mobile *Apps*
La vérification de version a été introduite à partir des versions suivantes du SDK client pour **Azure Mobile Apps**:

| Plateforme cliente | Version | Valeur d'en-tête de version |
| --- | --- | --- |
| Client géré (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Kits de développement logiciel (SDK) du serveur Mobile *Apps*
La vérification de version est incluse dans les versions suivantes du SDK serveur :

| Plateforme de serveur | Foundation | En-têtes de versions acceptés |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportement des serveurs principaux Mobile Apps
| ZUMO-API-VERSION | Valeur de MS_SkipVersionCheck | response |
| --- | --- | --- |
| x.y.z ou Null |True |200 - OK |
| Null |False/Non spécifié |400 - Requête incorrecte |
| 1.x.y |False/Non spécifié |400 - Requête incorrecte |
| 2.0.0-2.x.y |False/Non spécifié |200 - OK |
| 3.0.0-3.x.y |False/Non spécifié |400 - Requête incorrecte |

## <a name="next-steps"></a>Étapes suivantes
* [Migrer un service Mobile Services sur Azure App Service]

[Clients Mobile Services]: #MobileServicesClients
[Clients Mobile Apps]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrer un service Mobile Services sur Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
