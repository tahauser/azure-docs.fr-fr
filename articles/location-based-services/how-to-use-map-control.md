---
title: "Guide pratique pour utiliser la bibliothèque Map Control dans Azure Location Based Services | Microsoft Docs"
description: "Découvrez comment utiliser la bibliothèque Javascript côté client Map Control dans Azure Location Based Services."
services: location-based-services
keywords: "N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO."
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 06743640aae5e06d0160105458d9a3cfa35d5040
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Guide pratique pour utiliser la bibliothèque Map Control dans Azure Location Based Services
La bibliothèque Javascript côté client Map Control vous permet d’effectuer le rendu de cartes et des fonctionnalités Azure Location Based Services intégrées dans votre application web ou mobile. 

## <a name="prerequisites"></a>configuration requise
Un compte et une clé d’abonnement Azure Location Based Services. Pour plus d’informations sur la création d’un compte et la récupération d’une clé d’abonnement, consultez [Guide pratique pour gérer votre compte et vos clés Azure Location Based Services](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Créer une nouvelle carte dans une page web à l’aide de l’API Map Control
Vous pouvez intégrer une carte dans une page web à l’aide de la bibliothèque JavaScript côté client Map Control.

1. Créez un fichier et nommez-le MapSearch.html.

2. Ajoutez la feuille de styles Azure Location Based Services et les références de la source du script à l’élément `<head>` du fichier :

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Afin d’effectuer le rendu d’une nouvelle carte dans votre navigateur, ajoutez une référence **#map** dans l’élément `<style>`.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Afin d’initialiser le contrôle de la carte, définissez une nouvelle section dans le corps HTML et créez un script. Utilisez votre propre clé d’abonnement de votre compte Azure Location Based Services. 

    ```html
    <div id="map">
        <script>
            var subscriptionKey = "<_subscriptionKey_>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        <script>
    <div>
    ```
    
5. Ouvrez le fichier dans votre navigateur web et consultez la carte ayant fait l’objet du rendu.