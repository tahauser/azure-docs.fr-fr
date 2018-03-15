---
title: "Rechercher un itinéraire avec Azure Location Based Services | Microsoft Docs"
description: "Obtenir l’itinéraire vers un point d’intérêt à l’aide d’Azure Location Based Services"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7303347444952d9c09dc6c04eea5b962e18729b4
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Obtenir l’itinéraire vers un point d’intérêt à l’aide d’Azure Location Based Services

Ce didacticiel montre comment utiliser votre compte Azure Location Based Services et le SDK Route Service pour trouver l’itinéraire vers un point d’intérêt. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Obtenir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

## <a name="prerequisites"></a>Prérequis

Avant de continuer, assurez-vous de [créer votre compte Azure Location Based Services](./tutorial-search-location.md#createaccount) et [d’obtenir la clé d’abonnement pour votre compte](./tutorial-search-location.md#getkey). Vous pouvez également apprendre à utiliser les API Map Control et Search Service en consultant le didacticiel [Rechercher des points d’intérêt de proximité à l’aide d’Azure Location Based Services](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Obtenir les coordonnées d’une adresse

Utilisez les étapes suivantes pour créer une page HTML statique incorporée avec l’API Map Control de Location Based Services. 

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapRoute.html**. 
2. Ajoutez les composants HTML suivants au fichier :

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Comme vous pouvez le constater, l’en-tête HTML contient les emplacements des fichiers CSS et JavaScript pour la bibliothèque Azure Location Based Services. Notez en outre le segment *script* dans le corps du fichier HTML, destiné à contenir le code JavaScript inline permettant d’accéder aux API d’Azure Location Based Service.

3. Ajoutez le code JavaScript suivant au bloc *script* du fichier HTML. Utilisez la clé primaire à partir de votre compte Location Based Services dans le script.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** fournit le contrôle d’une carte web visuelle et interactive et est un composant de l’API Azure Map Control.

4. Ajoutez le code JavaScript suivant au bloc *script*. Cette opération ajoute une couche de *chaînes de lignes* au contrôle de carte pour afficher l’itinéraire :

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

5. Ajoutez le code JavaScript suivant pour créer les points de départ et d’arrivée de l’itinéraire :

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Ce code crée deux [objets GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire. Le point d’arrivée est la combinaison latitude/longitude de l’une des *stations-service* recherchées dans le didacticiel précédent [Recherche de points d’intérêt de proximité à l’aide d’Azure Location Based Services](./tutorial-search-location.md).

6. Ajoutez le code JavaScript suivant pour ajouter à la carte les marqueurs des points de départ et d’arrivée :

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    L’API **map.setCameraBounds** ajuste la fenêtre de la carte selon les coordonnées des points de départ et d’arrivée. L’API **map.addPins** ajoute les points au contrôle de carte sous la forme de composants visuels.

7. Enregistrez le fichier **MapRoute.html** sur votre machine. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

Cette section montre comment utiliser l’API Route Service d’Azure Location Based Services pour trouver l’itinéraire entre un point de départ donné et une destination. Route Service fournit des API pour planifier l’itinéraire le plus rapide, court ou économique entre deux points, en tenant compte des conditions de trafic en temps réel. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. 

1. Pour voir comment fonctionne Route Service, ouvrez le fichier **MapRoute.html** créé à la section précédente et ajoutez le code JavaScript suivant au bloc *script*.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    Cet extrait de code crée un objet [XMLHttpRequest](https://xhr.spec.whatwg.org/), puis ajoute un gestionnaire d’événements pour analyser la réponse entrante. Pour que la réponse soit correcte, il construit un tableau de coordonnées pour les segments de ligne du premier itinéraire retourné. Il ajoute ensuite ce jeu de coordonnées pour cet itinéraire à la couche de *chaînes de lignes* de la carte.

2. Ajoutez le code suivant au bloc *script* pour envoyer l’objet XMLHttpRequest au service Route Service d’Azure Location Based Services :

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    La requête ci-dessus montre les paramètres obligatoires, à savoir votre clé de compte et les coordonnées des points de départ et d’arrivée, dans l’ordre indiqué. 

3. Enregistrez le fichier **MapRoute.html** localement, ouvrez-le dans le navigateur web de votre choix, puis observez le résultat. Si la connexion aux API Location Based Services réussit, vous devez voir une carte semblable à celle ci-après. 

    ![Azure Map Control et Route Service](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Obtenir les coordonnées d’une adresse
> * Interroger Route Service afin d’obtenir des indications pour rejoindre un point d’intérêt

Passez au didacticiel [Rechercher des itinéraires pour différents modes de déplacement à l’aide d’Azure Location Based Services](./tutorial-prioritized-routes.md) afin de savoir comment utiliser Azure Location Based Services pour déterminer quel itinéraire privilégier vers votre point d’intérêt suivant le mode de transport. 
