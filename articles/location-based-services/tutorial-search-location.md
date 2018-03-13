---
title: Effectuer une recherche avec Azure Location Based Services | Microsoft Docs
description: "Rechercher des points d’intérêt de proximité à l’aide d’Azure Location Based Services"
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
ms.openlocfilehash: 791992028d11633fc20f55ae1a34e7fcd442bf3a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="search-nearby-points-of-interest-using-azure-location-based-services"></a>Rechercher des points d’intérêt de proximité à l’aide d’Azure Location Based Services

Ce didacticiel montre comment configurer un compte avec Azure Location Based Services, puis utiliser les API fournies pour rechercher un point d’intérêt. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte avec Azure Location Based Services
> * Connaître la clé primaire de votre compte Azure Location Based Services
> * Créer une nouvelle page web à l’aide de l’API Map Control
> * Utiliser Search Service pour rechercher un point d’intérêt de proximité

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Créer un compte avec Azure Location Based Services

Suivez ces étapes pour créer un compte Location Based Services.

1. En haut à gauche du [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**.
2. Dans la zone *Rechercher dans le marketplace*, tapez **location based services**.
3. Dans *Résultats*, cliquez sur **Location Based Services (préversion)**. Cliquez sur le bouton **Créer** qui s’affiche sous la carte. 
4. Dans la page **Créer un compte Location Based Services**, entrez les valeurs suivantes :
    - Le *Nom* de votre nouveau compte. 
    - *L’Abonnement* à utiliser pour ce compte.
    - Le *Groupe de ressources* pour ce compte. Vous pouvez choisir de *Créer* ou d’utiliser un groupe de ressources *Existant*.
    - Sélectionnez *l’Emplacement du groupe de ressources*.
    - Lisez les *Conditions d’utilisation de la préversion* et cochez la case pour les accepter. 
    - Enfin, cliquez sur le bouton **Créer**.
   
    ![Créer un compte Location Based Services dans le portail](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obtenir la clé primaire de votre compte

Une fois votre compte Location Based Services correctement créé, suivez les étapes ci-après pour le lier à ses API de recherche de cartes :

1. Ouvrez votre compte Location Based Services dans le portail.
2. Accédez aux **PARAMÈTRES** de votre compte, puis sélectionnez **Clés**.
3. Copiez la **Clé primaire** dans le Presse-papiers. Enregistrez-la localement en vue de son utilisation ultérieure. 

    ![Obtenir la clé primaire dans le portail](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Créer une page web à l’aide de l’API Azure Map Control
L’API Azure Map Control est une bibliothèque cliente pratique qui vous permet d’intégrer facilement Azure Location Based Services à votre application web. Elle masque la complexité des appels de service REST bruts et améliore votre productivité grâce à des composants personnalisables et dont vous pouvez définir le style. Les étapes suivantes vous montrent comment créer une page HTML statique incorporée avec l’API Map Control de Location Based Services. 

1. Sur votre ordinateur local, créez un fichier et nommez-le **MapSearch.html**. 
2. Ajoutez les composants HTML au fichier :

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    Notez que l’en-tête HTML inclut les fichiers de ressources CSS et JavaScript hébergés par la bibliothèque Azure Map Control. En outre, un segment *script* a été ajouté au corps (*body*) du fichier HTML. Ce segment est destiné à contenir du code JavaScript inline pour accéder aux API d’Azure Location Based Service.
 
3.  Ajoutez le code JavaScript suivant au bloc *script* du fichier HTML. Utilisez la clé primaire à partir de votre compte Location Based Services dans le script. 

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Ce segment lance l’API Map Control pour votre clé de compte Azure Location Based Services. **Atlas** est l’espace de noms qui contient les API Azure Map Control et les composants visuels associés. **atlas.Map** fournit le contrôle d’une carte web visuelle et interactive. Vous pouvez voir à quoi ressemble la carte en ouvrant la page HTML dans le navigateur. 

4. Ajoutez le code JavaScript suivant au bloc *script* pour ajouter une couche de marqueurs de recherche au contrôle de carte :

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

5. Enregistrez le fichier sur votre machine. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>Utiliser Search Service pour rechercher des points d’intérêt de proximité

Cette section montre comment utiliser l’API Search Service d’Azure Location Based Services pour rechercher un point d’intérêt sur la carte. Il s’agit d’une API RESTful destinée aux développeurs souhaitant mettre en place des fonctionnalités de recherche d’adresses, de points d’intérêt et autres informations d’ordre géographique. Search Service affecte une latitude et une longitude à une adresse spécifiée. 

1. Pour voir comment fonctionne Search Service, ouvrez le fichier **MapSearch.html** créé à la section précédente et ajoutez le code JavaScript suivant au bloc *script*. 
    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```
    Cet extrait de code crée un objet [XMLHttpRequest](https://xhr.spec.whatwg.org/), puis ajoute un gestionnaire d’événements pour analyser la réponse entrante. Si la réponse est correcte, il collecte l’adresses, le nom, la latitude et la longitude de chaque emplacement retourné, dans la variable `searchPins`. Enfin, il ajoute cette collection de points d’emplacement au contrôle `map` sous la forme de marqueurs. 

2. Ajoutez le code suivant au bloc *script* pour envoyer l’objet XMLHttpRequest au service Search Service d’Azure Location Based Services :

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "&api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Cet extrait de code utilise l’API de recherche de base de Search Service, appelée **Fuzzy Search** (recherche approximative). Il gère le jeu d’entrées le plus approximatif de n’importe quelle combinaison de jetons d’adresse ou de *point d’intérêt*. Il recherche les **stations-service** de proximité en fonction d’une adresse, exprimée en latitude et longitude, et d’un rayon. Il utilise la clé principale de votre compte fournie plus haut dans l’exemple de fichier pour appeler Location Based Services. Il retourne les résultats sous forme de paires latitude/longitude correspondant aux emplacements trouvés. Vous pouvez voir les marqueurs de recherche en ouvrant la page HTML dans le navigateur. 

3. Ajoutez les lignes suivantes au bloc *script* afin de créer des fenêtres contextuelles pour les points d’intérêt retournés par Search Service :

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    L’API **atlas.Popup** génère une fenêtre d’informations ancrée à la position requise sur la carte. Cet extrait de code définit le contenu et la position de la fenêtre contextuelle et ajoute un écouteur d’événements au contrôle `map`, qui réagit au passage du pointeur de la _souris_ au-dessus du marqueur associé à la fenêtre contextuelle. 

4. Enregistrez le fichier, ouvrez le fichier **MapSearch.html** dans le navigateur web de votre choix, puis observez le résultat. À ce stade, la carte dans le navigateur affiche les fenêtres contextuelles d’informations quand vous pointez sur les marqueurs de recherche affichés, comme dans l’exemple ci-dessous. 

    ![Azure Map Control et Search Service](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un compte avec Azure Location Based Services
> * Obtenir la clé primaire de votre compte
> * Créer une page web à l’aide de l’API Map Control
> * Utiliser Search Service pour rechercher des points d’intérêt de proximité

Passez au didacticiel [Obtenir l’itinéraire vers un point d’intérêt à l’aide d’Azure Location Based Services](./tutorial-route-location.md) pour savoir comment obtenir l’itinéraire vers votre point d’intérêt à l’aide d’Azure Location Based Services. 
