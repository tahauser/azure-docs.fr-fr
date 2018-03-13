---
title: "Guide pratique pour rechercher une adresse à l’aide d’Azure Location Based Services (préversion) | Microsoft Docs"
description: "Découvrez comment rechercher une adresse à l’aide d’Azure Location Based Services (préversion)"
services: location-based-services
keywords: "N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO."
author: philmea
ms.author: philmea
ms.date: 11/29/2017
ms.topic: article
ms.service: location-based-services
ms.openlocfilehash: cacaaab869d3a7752b5a750f01bbfbdaf79814f7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-find-an-address-using-the-azure-location-based-services-preview-search-service"></a>Guide pratique pour rechercher une adresse à l’aide d’Azure Location Based Services (préversion)
Search Service est un ensemble d’API RESTful destinées aux développeurs souhaitant mettre en place des fonctionnalités de recherche d’adresses, de lieux, de points d’intérêt, de listes d’entreprises et autres informations d’ordre géographique. Search Service affecte une combinaison latitude/longitude à une adresse, intersection, caractéristique géographique ou point d’intérêt spécifique. Les valeurs de latitude et de longitude retournées par les API Search Service peuvent être utilisées comme paramètres dans d’autres services Azure Location Based Services, tels que les API Route et Traffic Flow.

## <a name="prerequisites"></a>configuration requise
* Installer [l’application Postman](https://www.getpostman.com/apps).

* Disposer d’un compte et d’une clé Azure Location Based Services. Pour plus d’informations sur la création d’un compte et la récupération d’une clé, consultez [Guide pratique pour gérer votre compte et vos clés Azure Location Based Services](how-to-manage-account-keys.md). 

## <a name="using-fuzzy-search"></a>Utilisation de Fuzzy Search

L’API par défaut pour Search Service est Fuzzy Search, qui gère les entrées de n’importe quelle combinaison de jetons d’adresse ou de point d’intérêt. Cette API de recherche, qui permet d’effectuer une recherche canonique à ligne unique, est utile quand vous ne savez pas ce que l’utilisateur entre comme requête de recherche. L’API Fuzzy Search est une combinaison de recherche et de géocodage de points d’intérêt. L’API peut également être affinée avec une position contextuelle (paire lat./lon.), limitée par des coordonnées et un rayon, ou exécutée plus généralement sans point d’ancrage à biais géographique.

La plupart des requêtes de recherche utilisent par défaut le paramétrage « maxFuzzyLevel = 1 » pour optimiser les performances et réduire les résultats inhabituels. Selon le cas, vous pouvez remplacer ce paramétrage par défaut en passant le paramètre de requête « maxFuzzyLevel = 2 » ou « maxFuzzyLevel = 3 ».

### <a name="search-for-an-address-using-fuzzy-search"></a>Rechercher une adresse à l’aide de Fuzzy Search

1. Ouvrez l’application Postman, cliquez sur New (Nouveau) | Create New (Créer), puis sélectionnez **GET request** (Requête GET). Entrez **Fuzzy Search** comme nom de requête, sélectionnez la collection ou le dossier dans lequel enregistrer la requête, puis cliquez sur **Save** (Enregistrer).

    Pour plus d’informations, consultez la documentation sur les requêtes de Postman.

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode **GET**, puis entrez l’URL de la requête pour le point de terminaison de votre API.

    ![Recherche approximative ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | https://atlas.microsoft.com/search/fuzzy/json? |
    | Authorization | No Auth (Pas d’autorisation) |

    L’attribut **json** dans le chemin de l’URL détermine le format de la réponse. Vous utilisez json tout au long de cet article pour des raisons de facilité d’utilisation et de lisibilité. Les formats de réponse sont disponibles dans la définition **Get Search Fuzzy** (Obtenir l’API de recherche approximative) du [Guide référence des API fonctionnelles de Location Based Services] (https://docs.microsoft.com/rest/api/location-based-services/search/getsearchfuzzy).

3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :

    ![Recherche approximative ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<votre clé Azure Location Based Services\> |
    | query | pizza |

4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse. 

    La chaîne de requête ambiguë « pizza » a retourné 10 résultats de point d’intérêt dans les catégories « pizza » et « restaurant ». Chaque résultat retourne une adresse postale, les valeurs latitude/longitude, un point de vue et les points d’entrée de l’emplacement.
    
    Les résultats peuvent varier pour cette requête et ne sont pas liés à un emplacement de référence particulier. Vous pouvez utiliser le paramètre **countrySet** pour ne spécifier que les pays que votre application doit couvrir ; en effet, le comportement par défaut consiste à rechercher dans le monde entier, ce qui peut engendrer des résultats superflus.

5. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |------------------|-------------------------|
    | countrySet | FR |
    
    Les résultats sont désormais délimités par le code de pays ; en l’occurrence, la requête retourne des pizzerias aux États-Unis.
    
    Pour fournir des résultats orientés sur un emplacement particulier, vous pouvez interroger un point d’intérêt et utiliser les valeurs de longitude et de latitude retournées dans l’appel au service Fuzzy Search. En l’occurrence, vous avez utilisé Search Service pour retourner l’emplacement de la tour Seattle Space Needle et utilisé les valeurs de latitude et de longitude pour orienter la recherche.
    
4. Dans Params (Paramètres), entrez la paire clé/valeur suivante, puis cliquez sur **Send** (Envoyer) :

    ![Recherche approximative ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Clé | Valeur |
    |-----|------------|
    | lat | 47.62039 |
    | lon | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Rechercher des propriétés et des coordonnées d’adresse 

Vous pouvez passer une adresse postale complète ou partielle à l’API Search Address et recevoir une réponse qui inclut les propriétés détaillées de l’adresse telles que la commune ou la subdivision, ainsi que sa position exprimée en latitude et longitude.

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Address Search** (Recherche d’adresses).
2. Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête pour le point de terminaison de votre API et sélectionnez un protocole d’autorisation, le cas échéant.

    ![Recherche d’adresses ](./media/how-to-search-for-address/address_search_url.png)
    
    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | https://atlas.microsoft.com/search/address/json? |
    | Authorization | No Auth (Pas d’autorisation) |

2. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :
    
    ![Recherche d’adresses ](./media/how-to-search-for-address/address_search_params.png)
    
    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<votre clé Azure Location Based Services\> |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse. 
    
    Dans ce cas, vous avez spécifié une requête d’adresse complète et recevez un résultat unique dans le corps de la réponse. 
    
4. Dans Params (Paramètres), modifiez la chaîne de requête comme suit :
    ```
        400 Broad, Seattle
    ```

5. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | typeahead | true |

    L’indicateur **typeahead** indique à l’API Address Search de traiter la requête en tant qu’entrée partielle et de retourner un tableau de valeurs prédictives.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Rechercher une adresse postale à l’aide d’une recherche d’adresse inverse
1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Reverse Address Search** (Recherche d’adresse inverse).

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode **GET**, puis entrez l’URL de la requête pour le point de terminaison de votre API.
    
    ![URL de la recherche d’adresse inverse ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | https://atlas.microsoft.com/search/address/reverse/json? |
    | Authorization | No Auth (Pas d’autorisation) |
    
2. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :
    
    ![Paramètres de la recherche d’adresse inverse ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<votre clé Azure Location Based Services\> |
    | query | 47.59093,-122.33263 |
    
3. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse. 
    
    La réponse inclut l’entrée de point d’intérêt Safeco Field avec la catégorie de point d’intérêt « stadium » (stade). 
    
4. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | number | true |

    Si la requête comporte le paramètre [number](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la réponse peut inclure le côté de la rue (gauche/droite) et également une position de décalage par rapport à ce numéro.
    
5. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | spatialKeys | true |

    Si le paramètre de requête [spatialKeys](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) est défini, la réponse contient les informations de clés géospatiales propriétaires d’un emplacement spécifié.

6. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Si le paramètre de requête [returnSpeedLimit](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) est défini, la réponse retourne la vitesse maximale autorisée.

7. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | returnRoadUse | true |

    Si le paramètre de requête [returnRoadUse](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) est défini, la réponse retourne le tableau de l’état du trafic routier des géocodes inverses au niveau des rues.

8. Ajoutez la paire Clé/Valeur suivante à la section **Params** puis cliquez sur **Envoyer** :

    | Clé | Valeur |
    |-----|------------|
    | roadUse | true |

    Vous pouvez limiter la requête de géocode inverse à un type spécifique de trafic routier à l’aide du paramètre de requête [roadUse](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters).
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Rechercher une intersection à l’aide d’une recherche d’intersection d’adresse inverse

1. Dans Postman, cliquez sur **New Request** (Nouvelle requête) | **GET request** (Requête GET) et nommez-la **Reverse Address Cross Street Search** (Recherche d’intersection d’adresse inverse).

2. Sous l’onglet Builder (Générateur), sélectionnez la méthode **GET**, puis entrez l’URL de la requête pour le point de terminaison de votre API.
    
    ![Recherche d’intersection d’adresse inverse ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Paramètre | Valeur suggérée |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la demande | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Authorization | No Auth (Pas d’autorisation) |
    
3. Cliquez sur **Params** (Paramètres), puis entrez les paires clé/valeur suivantes à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête :
    
    | Clé | Valeur |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<votre clé Azure Location Based Services\> |
    | query | 47.59093,-122.33263 |
    
4. Cliquez sur **Send** (Envoyer), puis examinez le corps de la réponse. 

## <a name="next-steps"></a>Étapes suivantes
- Explorez la documentation de l’API [Search Service d’Azure Location Based Services](https://docs.microsoft.com/rest/api/location-based-services/search). 
