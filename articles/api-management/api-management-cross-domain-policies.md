---
title: "Stratégies inter-domaines dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez les stratégies inter-domaines disponibles dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-cross-domain-policies"></a>Gestion des API dans les stratégies de domaine
Cette rubrique est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a> Stratégies inter-domaines  
  
-   [Allow cross-domain calls](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : rend l'API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.  
-   [CORS](api-management-cross-domain-policies.md#CORS) : ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) : ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients utilisant JavaScript.  
  
##  <a name="AllowCrossDomainCalls"></a> Allow cross-domain calls  
 La stratégie `cross-domain` rend l’API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.  
  
### <a name="policy-statement"></a>Déclaration de stratégie  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Exemple  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Requis|  
|----------|-----------------|--------------|  
|inter-domaines|Élément racine. Les éléments enfants doivent être conformes à la [spécification de fichier de stratégie inter-domaines Adobe](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Oui|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound (entrant)  
-   **Étendues de la stratégie :** globale (globale)  
  
##  <a name="CORS"></a> CORS  
 La stratégie `cors` ajoute la prise en charge du partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels inter-domaines à partir des navigateurs clients.  
  
 CORS permet à un navigateur et à un serveur d'interagir et de déterminer si les demandes cross-origin doivent être autorisées ou non, par exemple dans le cas d'appels XMLHttpRequests passés via JavaScript sur une page web vers d'autres domaines). Cette stratégie offre plus de flexibilité que de simplement autoriser les demandes de même origine, mais elle est plus sûre que d'autoriser toutes les demandes cross-origin.  
  
### <a name="policy-statement"></a>Déclaration de stratégie  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>Exemple  
 Cet exemple montre comment prendre en charge les demandes en amont, telles que celles comportant des en-têtes personnalisés ou des méthodes autres que GET et POST. Pour prendre en charge les en-têtes personnalisés et autres verbes HTTP, utilisez les sections `allowed-methods` et `allowed-headers` comme indiqué dans l’exemple suivant.  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Requis|Default|  
|----------|-----------------|--------------|-------------|  
|cors|Élément racine.|Oui|N/A|  
|allowed-origins|Contient des éléments `origin` qui décrivent les origines autorisées pour les demandes inter-domaines. `allowed-origins` peut contenir un seul élément `origin` qui spécifie `*` pour autoriser toute origine, ou un ou plusieurs éléments `origin` contenant un URI.|Oui|N/A|  
|origin|La valeur peut être `*` pour autoriser toutes les origines, ou un URI qui spécifie une origine unique. L'URI doit comprendre un modèle, un hôte et un port.|Oui|Si le port n’est pas spécifié dans l’URI, le port 80 est utilisé pour HTTP et le port 443 pour HTTPS.|  
|allowed-methods|Cet élément est requis si les méthodes autres que GET ou POST sont autorisées. Contient des éléments `method` qui spécifient les verbes HTTP pris en charge.|Non|Si cette section n’est pas présente, les méthodes GET et POST sont prises en charge.|  
|statique|Spécifie un verbe HTTP.|Au moins un élément `method` est requis si la section `allowed-methods` est présente.|N/A|  
|allowed-headers|Cet élément contient des éléments `header` spécifiant les noms des en-têtes qui peuvent être inclus dans la demande.|Non|N/A|  
|expose-headers|Cet élément contient des éléments `header` spécifiant les noms des en-têtes accessibles par le client.|Non|N/A|  
|en-tête|Spécifie un nom d’en-tête.|Au moins un élément `header` est requis dans `allowed-headers` ou `expose-headers` si la section est présente.|N/A|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Requis|Default|  
|----------|-----------------|--------------|-------------|  
|allow-credentials|L’en-tête `Access-Control-Allow-Credentials` dans la réponse en amont est défini sur la valeur de cet attribut et affecte la capacité du client à envoyer des informations d’identification dans les demandes inter-domaines.|Non|false|  
|preflight-result-max-age|L’en-tête `Access-Control-Max-Age` dans la réponse en amont est défini sur la valeur de cet attribut et affecte la capacité de l’agent utilisateur à mettre en cache la réponse en amont.|Non|0|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound (entrant)  
-   **Étendues de la stratégie :** API, operation (API, opération)  
  
##  <a name="JSONP"></a> JSONP  
 La stratégie `jsonp` ajoute la prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels inter-domaines à partir des navigateurs clients utilisant JavaScript. JSONP est une méthode utilisée par les programmes JavaScript pour demander des données à un serveur se trouvant dans un autre domaine. JSONP passe outre la limite appliquée par la plupart des navigateurs web, selon laquelle l'accès aux pages web doit se trouver dans le même domaine.  
  
### <a name="policy-statement"></a>Déclaration de stratégie  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Exemple  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Si vous appelez la méthode sans le paramètre de rappel ?cb=XXX, elle renvoie un code JSON simple (sans enveloppe d’appel de fonction).  
  
 Si vous ajoutez le paramètre de rappel `?cb=XXX`, il renvoie un résultat JSONP, enveloppant les résultats JSON d’origine autour de la fonction de rappel, comme `XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Requis|  
|----------|-----------------|--------------|  
|jsonp|Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Requis|Default|  
|----------|-----------------|--------------|-------------|  
|callback-parameter-name|Appel de fonction JavaScript interdomaines avec comme préfixe le nom de domaine complet de l'emplacement de la fonction.|Oui|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) suivantes.  
  
-   **Sections de la stratégie :** outbound (sortant)  
-   **Étendues de la stratégie :** global, product, API, operation (global, produit, API, opération)  
  
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transformer des API](transform-api.md)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [Exemples de stratégie](policy-samples.md)   