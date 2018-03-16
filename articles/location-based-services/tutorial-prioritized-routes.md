---
title: "Itinéraires multiples avec Azure Location Based Services | Microsoft Docs"
description: "Rechercher des itinéraires pour différents modes de déplacement à l’aide d’Azure Location Based Services"
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 986e8667ca421baa78647e77e43fef92d81a7982
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Rechercher des itinéraires pour différents modes de déplacement à l’aide d’Azure Location Based Services

Ce didacticiel montre comment utiliser votre compte Azure Location Based Services et le SDK Route Service pour trouver l’itinéraire vers un point d’intérêt, en fonction de votre mode de déplacement. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer votre requête Route Service
> * Afficher les itinéraires priorisés par mode de déplacement

## <a name="prerequisites"></a>Prérequis


Avant de continuer, assurez-vous de [créer votre compte Azure Location Based Services](./tutorial-search-location.md#createaccount) et [d’obtenir une clé de votre compte](./tutorial-search-location.md#getkey). Vous pouvez également consulter l’utilisation des API Map Control et Search Service comme indiqué dans le didacticiel [Rechercher des points d’intérêt de proximité à l’aide d’Azure Location Based Services](./tutorial-search-location.md). Vous pouvez également découvrir l’utilisation de base des API Route Service comme indiqué dans le didacticiel [Obtenir l’itinéraire vers un point d’intérêt à l’aide d’Azure Location Based Services](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Configurer votre requête Route Service

Utilisez les étapes suivantes pour créer une page HTML statique incorporée avec l’API Map Control de Location Based Services. 

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapTruckRoute.html**. 
2. Ajoutez les composants HTML suivants au fichier :

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    Comme vous pouvez le constater, l’en-tête HTML contient les emplacements des fichiers CSS et JavaScript pour la bibliothèque Azure Location Based Services. Notez en outre le segment *script* ajouté dans le corps du fichier HTML, destiné à contenir le code JavaScript inline permettant d’accéder à l’API Azure Map Control.
3. Ajoutez le code JavaScript suivant au bloc *script* du fichier HTML. Remplacez l’espace réservé *<insert-key>* par la clé primaire de votre compte Location Based Services.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** fournit le contrôle d’une carte web visuelle et interactive et est un composant de l’API Azure Map Control.

4. Ajoutez le code JavaScript suivant au bloc *script*, pour ajouter l’affichage du flux du trafic à la carte :

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Ce code définit le flux du trafic sur `relative`, qui correspond à la vitesse de la route correspondant à un trafic fluide. Vous pouvez également le définir sur vitesse `absolute` de la route, ou sur `relative-delay` qui affiche la vitesse relative lorsqu’elle diffère du trafic fluide. 

5. Ajoutez le code JavaScript suivant pour créer les épingles pour les points de départ et d’arrivée de l’itinéraire :

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Ce code crée deux [objets GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) pour représenter les points de départ et d’arrivée de l’itinéraire. 

6. Ajoutez le code JavaScript suivant pour ajouter des couches de *linestrings* à Map Control, pour afficher des itinéraires en fonction du mode de transport, par exemple, _voiture_ et _camion_.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

7. Ajoutez le code JavaScript suivant pour ajouter à la carte les points de départ et d’arrivée :

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    L’API **map.setCameraBounds** ajuste la fenêtre de la carte selon les coordonnées des points de départ et d’arrivée. L’API **map.addPins** ajoute les points au contrôle de carte sous la forme de composants visuels.

8. Enregistrez le fichier **MapTruckRoute.html** sur votre machine. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Afficher les itinéraires priorisés par mode de déplacement

Cette section montre comment utiliser l’API Route Service d’Azure Location Based Services pour rechercher plusieurs itinéraires entre un point de départ donné et une destination, en fonction de votre mode de transport. Route Service fournit des API pour planifier l’itinéraire le plus rapide, court ou économique entre deux points, en tenant compte des conditions de trafic en temps réel. Grâce à la base de données de trafic historique complète d’Azure, il permet également aux utilisateurs de planifier des itinéraires, durées comprises, pour n’importe quels jour et heure. 

1. Pour obtenir l’itinéraire pour un camion avec Route Service, ouvrez le fichier **MapTruckRoute.html** créé à la section précédente et ajoutez le code JavaScript suivant au bloc *script*.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Cet extrait de code crée un objet [XMLHttpRequest](https://xhr.spec.whatwg.org/), puis ajoute un gestionnaire d’événements pour analyser la réponse entrante. Pour obtenir une réponse correcte, il crée un tableau de coordonnées pour l’itinéraire retourné et l’ajoute à la couche `truckRouteLayerName` de la carte. 
    
    Cet extrait de code envoie aussi la requête à Route Service, afin d’obtenir l’itinéraire pour des points de départ et d’arrivée spécifiés, pour la clé de votre compte. Les paramètres facultatifs suivants sont utilisés pour indiquer l’itinéraire pour un poids lourd :
   - Le paramètre `travelMode=truck` spécifie le mode de déplacement en tant que *camion*. Les autres modes de déplacement pris en charge sont *taxi*, *bus*, *van*, *moto* et par défaut *voiture*.
   - Les paramètres `vehicleWidth`, `vehicleHeight` et `vehicleLength` spécifient les dimensions du véhicule en mètres, et ne sont pris en compte que si le mode de déplacement est *camion*.
   - Le paramètre `vehicleLoadType` classe la cargaison comme dangereuse et restreinte sur certaines routes. Actuellement, ce paramètre n’est également pris en charge que pour le mode *camion*.

2. Ajoutez le code JavaScript suivant pour obtenir l’itinéraire pour une voiture à l’aide de Route Service :

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Cet extrait de code crée un autre objet [XMLHttpRequest](https://xhr.spec.whatwg.org/), puis ajoute un gestionnaire d’événements pour analyser la réponse entrante. Pour obtenir une réponse correcte, il crée un tableau de coordonnées pour l’itinéraire retourné et l’ajoute à la couche `carRouteLayerName` de la carte. 
    
    Cet extrait de code envoie aussi la requête à Route Service, afin d’obtenir l’itinéraire pour des points de départ et d’arrivée spécifiés, pour la clé de votre compte. Étant donné qu’aucun autre paramètre n’est utilisé, l’itinéraire pour le mode de déplacement par défaut *voiture* est retourné. 

3. Enregistrez le fichier **MapTruckRoute.html** localement, ouvrez-le dans le navigateur web de votre choix, puis observez le résultat. Si la connexion aux API Location Based Services est couronnée de succès, vous devez voir une carte semblable à celle ci-après. 

    ![Itinéraires priorisés avec Azure Route Service](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Notez que l’itinéraire pour camion est affiché en bleu et l’itinéraire pour voiture est affiché en violet.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer votre requête Route Service
> * Afficher les itinéraires priorisés par mode de déplacement

Consultez les articles **Concepts** et **Guide pratique** pour découvrir le Kit SDK d’Azure Location Based Services en détail. 
