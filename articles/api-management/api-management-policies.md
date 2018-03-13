---
title: "Stratégies dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez les stratégies disponibles dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: cb0304cae1b92578eb3c9230ae9faeecdf13d750
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="api-management-policies"></a>Stratégies API Management
Cette section est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](api-management-howto-policies.md).  
  
 Les stratégies sont une fonctionnalité puissante du système qui permet à l’éditeur de modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions les plus utilisées comprennent la conversion du format XML au format JSON et la limitation du débit d'appels pour restreindre la quantité d'appels entrants d'un développeur. De nombreuses autres stratégies sont disponibles.  
  
 Les expressions de stratégie peuvent être utilisées comme valeurs d’attribut ou valeurs de texte dans l’une des stratégies de Gestion des API, sauf si la stratégie le spécifie autrement. Certaines stratégies, telles que les stratégies [Control flow](api-management-advanced-policies.md#choose) et [Set variable](api-management-advanced-policies.md#set-variable), sont basées sur des expressions de stratégie. Pour plus d’informations, consultez les rubriques [Stratégies avancées](api-management-advanced-policies.md#AdvancedPolicies) et [Expressions de stratégie](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Stratégies  
  
-   [Accès aux stratégies de restriction](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Check HTTP header](api-management-access-restriction-policies.md#CheckHTTPHeader) : applique l’existence et/ou la valeur d’un en-tête HTTP.  
    -   [Limit call rate by subscription](api-management-access-restriction-policies.md#LimitCallRate) : empêche les pics d’utilisation de l’API en limitant le débit d’appels par abonnement.  
    -   [Limit call rate by key](api-management-access-restriction-policies.md#LimitCallRateByKey) : empêche les pics d’utilisation de l’API en limitant le débit d’appels par clé.  
    -   [Restrict caller IPs](api-management-access-restriction-policies.md#RestrictCallerIPs) : filtre (autorise/rejette) les appels de certaines adresses IP spécifiques et/ou de certaines plages d’adresses.  
    -   [Set usage quota by subscription](api-management-access-restriction-policies.md#SetUsageQuota) : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par abonnement.  
    -   [Set usage quota by key](api-management-access-restriction-policies.md#SetUsageQuotaByKey) : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par clé.  
    -   [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) : applique l’existence et la validité d’un JWT extrait d’un en-tête HTTP ou d’un paramètre de requête spécifié.  
-   [Stratégies avancées](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Control flow](api-management-advanced-policies.md#choose) : applique de manière conditionnelle les instructions des stratégies en fonction de l’évaluation des expressions booléennes.  
    -   [Forward request](api-management-advanced-policies.md#ForwardRequest) : transfère la demande vers le service principal.  
    -   [Log to Event Hub](api-management-advanced-policies.md#log-to-eventhub) : envoie des messages au format spécifié à une cible de message définie par une entité Enregistreur.  
    -   [Retry](api-management-advanced-policies.md#Retry) : effectue une nouvelle tentative d’exécution des instructions de stratégie incluses, si la condition est remplie et jusqu’à ce qu’elle le soit. L’exécution se répète à intervalles réguliers et ce jusqu’au nombre de tentatives défini.  
    -   [Return response](api-management-advanced-policies.md#ReturnResponse) : abandonne l’exécution du pipeline et renvoie la réponse indiquée directement à l’appelant.  
    -   [Send one way request](api-management-advanced-policies.md#SendOneWayRequest) : envoie une demande à l’URL indiquée sans attendre de réponse.  
    -   [Send request](api-management-advanced-policies.md#SendRequest) : envoie une demande à l’URL indiquée.  
    -   [Set variable](api-management-advanced-policies.md#set-variable) : conserve une valeur dans une variable de contexte nommée pour permettre d’y accéder ultérieurement.  
    -   [Set request method](api-management-advanced-policies.md#SetRequestMethod) : permet de modifier la méthode HTTP d’une demande.  
    -   [Set status code](api-management-advanced-policies.md#SetStatus) : permet de donner la valeur spécifiée au code d’état HTTP.  
    -   [Trace](api-management-advanced-policies.md#Trace) : ajoute une chaîne à la sortie de l’[inspecteur d’API](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/).  
    -   [Wait](api-management-advanced-policies.md#Wait) : attend l’exécution des stratégies [Send request](api-management-advanced-policies.md#SendRequest), [Get value from cache](api-management-caching-policies.md#GetFromCacheByKey) ou [Control flow](api-management-advanced-policies.md#choose) pour continuer.  
-   [Stratégies d’authentification](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Authenticate with Basic](api-management-authentication-policies.md#Basic) : authentification avec un service principal à l’aide de l’authentification de base.  
    -   [Authenticate with client certificate](api-management-authentication-policies.md#ClientCertificate) : authentification avec un service principal à l’aide de certificats clients.  
-   [Stratégies de mise en cache](api-management-caching-policies.md#CachingPolicies)  
    -   [Get from cache](api-management-caching-policies.md#GetFromCache) : effectue une recherche dans le cache et renvoie une réponse mise en cache valide si elle est disponible.  
    -   [Store to cache](api-management-caching-policies.md#StoreToCache) : met en cache la réponse en fonction de la configuration de contrôle de cache spécifiée.  
    -   [Get value from cache](api-management-caching-policies.md#GetFromCacheByKey) : récupère un élément mis en cache par clé.  
    -   [Store value in cache](api-management-caching-policies.md#StoreToCacheByKey) : stocke un élément mis en cache par clé.  
    -   [Remove value from cache](api-management-caching-policies.md#RemoveCacheByKey) : supprime un élément du cache par clé.  
-   [Stratégies interdomaines](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Allow cross-domain calls](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : rend l'API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) : ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) : ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients utilisant JavaScript.  
-   [Stratégies de transformation](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Convert JSON to XML](api-management-transformation-policies.md#ConvertJSONtoXML) : convertit le corps de la demande ou de la réponse de JSON en XML.  
    -   [Convert XML to JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) :convertit le corps de la demande ou de la réponse de XML en JSON.  
    -   [Find and replace string in body](api-management-transformation-policies.md#Findandreplacestringinbody) : recherche une sous-chaîne de demande ou de réponse et la remplace par une autre sous-chaîne.  
    -   [Mask URLs in content](api-management-transformation-policies.md#MaskURLSContent) : réécrit (masque) les liens dans le corps de la réponse afin qu’ils pointent vers un lien équivalent via la passerelle.  
    -   [Set backend service](api-management-transformation-policies.md#SetBackendService) : modifie le service principal pour une demande entrante.  
    -   [Set body](api-management-transformation-policies.md#SetBody) : définit le corps du message pour les demandes entrantes et sortantes.  
    -   [Set HTTP header](api-management-transformation-policies.md#SetHTTPheader) : affecte une valeur à un en-tête de réponse et/ou de demande existant ou bien ajoute un nouvel en-tête de réponse et/ou de demande.  
    -   [Set query string parameter](api-management-transformation-policies.md#SetQueryStringParameter) : ajoute, supprime un paramètre de chaîne de requête de la demande ou le remplace par une autre valeur.  
    -   [URL de réécriture](api-management-transformation-policies.md#RewriteURL) : convertit une URL de demande de sa forme publique en une forme attendue par le service web.  
    -   [Transformer du code XML à l’aide d’une transformation XSLT](api-management-transformation-policies.md#XSLTransform) : applique une transformation de XSL en XML dans le corps de la réponse ou de la demande.  



## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [API Management policy samples](policy-samples.md) (Exemples de stratégie de gestion d’API)   
