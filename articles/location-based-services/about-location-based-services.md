---
title: "Vue d’ensemble d’Azure Location Based Services | Microsoft Docs"
description: "Présentation d’Azure Location Based Services (préversion)"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: overview
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9b4b54c3a4cf0ed4350f570259f6997e4398682b
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Présentation d’Azure Location Based Services (préversion)
Azure Location Based Services est un portefeuille de services géospatiaux comprenant des API de service pour les cartes, la recherche, la création d'itinéraires, le trafic et les fuseaux horaires. Le portefeuille de services conformes à l’API Azure One vous permet d’utiliser des outils pour développeur que vous connaissez afin de développer et de mettre à l’échelle rapidement des solutions intégrant des informations d’emplacement dans vos solutions Azure. Azure Location Based Services dote les développeurs de tous les secteurs de puissantes fonctionnalités géospatiales, avec des données de mappage actualisées requises pour fournir un contexte géographique aux applications mobiles et web. Azure Location Based Services est un jeu d’API REST conforme à l’API Azure One et s’accompagne d’un contrôle JavaScript web permettant un développement très aisé, flexible et portable sur plusieurs supports. 

Azure Location Based Services est constitué de cinq services principaux pour booster les applications Azure nécessitant un contexte géographique. Chacun de ces services est expliqué en détail ci-dessous.

**Render Service** : Render Service est conçu pour les développeurs afin de créer des applications web et mobiles autour du mappage. Le service utilise des images graphiques raster de haute qualité disponibles en 19 niveaux de zoom ou des images de cartes au format vectoriel entièrement personnalisables.

![Azure Location Based Services Map.png](media/about-location-based-services/Introduction_Map.png)

**Route Service** : Route Service intègre des fonctionnalités robustes de calcul géométrique d’infrastructure réelle et les directions de plusieurs modes de transport. Le service permet aux développeurs de calculer les directions pour plusieurs modes de déplacement : voiture, camion, vélo ou marche à pied ; ainsi que plusieurs entrées comme les conditions de trafic, les restrictions de poids ou le transport de matières dangereuses.

![Azure Location Based Services Route.png](media/about-location-based-services/Introduction_Route.png)

**Search Service** : Search Service est conçu pour les développeurs et permet de rechercher des adresses, des lieux, des listes d’entreprises par nom ou catégorie, et d’autres informations d’ordre géographique. Search Service peut aussi [géocoder en inversé](https://en.wikipedia.org/wiki/Reverse_geocoding) les adresses et rues de croisement en fonction d’une latitude/longitude. 

![Azure Location Based Services Search.png](media/about-location-based-services/Introduction_Search.png)

**Time Zone Service** : Time Zone Service vous permet d’interroger des informations actuelles, historiques et futures sur un fuseau horaire à l’aide de paires latitude-longitude ou d’un [ID IANA](http://www.iana.org/). Time Zone Service permet aussi de convertir les ID de fuseau horaire Microsoft Windows en fuseaux horaires IANA, de récupérer un décalage de fuseau horaire par rapport à UTC et d’obtenir l’heure actuelle dans un fuseau horaire respectif. Une réponse JSON type à une requête Time Zone Service ressemble à ce qui suit :

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

**Traffic Service** : Traffic Service est une suite de services web conçue pour les développeurs afin de créer des applications web et mobiles nécessitant des informations sur le trafic. L’offre est organisée de la manière suivante :
1. Traffic Flow : fournit les vitesses et durées de déplacement observées en temps réel pour toutes les routes clés du réseau ; et, 
2. Traffic Incidents : fournit une vue précise des embouteillages et des incidents sur le réseau routier.

![Trafic Azure Location Based Services](media/about-location-based-services/Introduction_Traffic.png)

Azure Location Based Services est conçu pour la mobilité et peut alimenter des applications multiplateforme étant donné que le modèle de programmation est agnostique et prend en charge la sortie JSON via des API REST. En outre, Azure LBS offre une bibliothèque Map Control JavaScript pratique avec un modèle de programmation simple pour un développement rapide et aisé des applications web et mobiles. 

Azure Location Based Services utilise un schéma d’authentification basé sur clé, donc l’accès au service se fait en accédant au [portail Azure](http://portal.azure.com) et en créant un compte Azure Location Based Services. Votre compte est doté de deux clés prégénérées pour vous. Commencez à intégrer ces fonctionnalités d’emplacement directement dans vos applications en utilisant l’une de vos clés dans les requêtes envoyées au service Azure Location Based Services.

Inscrivez-vous pour obtenir un [compte Azure Location Based Services dès aujourd’hui !](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>Étapes suivantes

Vous disposez maintenant d’une vue d’ensemble d’Azure Location Based Services (préversion). L’étape suivante consiste à tester un exemple d’application utilisant Location Based Services, et à créer un scénario de bout en bout dans votre application web.

> [!div class="nextstepaction"]
> [Lancer une démonstration de recherche interactive sur une carte avec Azure Location Based Services (préversion)](quick-demo-map-app.md)
> [Rechercher des points d’intérêt de proximité à l’aide d’Azure Location Based Services](tutorial-search-location.md)