---
title: "Stratégies d’authentification dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez les stratégies d’authentification disponibles dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4d13d9dbea9da9db5bfe9a9af85fdbf9eab1ae84
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-authentication-policies"></a>Stratégies d’authentification dans Gestion des API
Cette rubrique est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](http://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Stratégies d’authentification  
  
-   [Authenticate with Basic](api-management-authentication-policies.md#Basic) : authentification avec un service principal à l’aide de l’authentification de base.  
  
-   [Authenticate with client certificate](api-management-authentication-policies.md#ClientCertificate) : authentification avec un service principal à l’aide de certificats clients.  
  
##  <a name="Basic"></a> Authenticate with Basic  
 La stratégie `authentication-basic` permet l’authentification auprès d’un service principal à l’aide de l’authentification de base. Cette stratégie définit en réalité l’en-tête d’autorisation HTTP sur la valeur correspondant aux informations d’identification fournies dans la stratégie.  
  
### <a name="policy-statement"></a>Déclaration de stratégie  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Exemple  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Requis|  
|----------|-----------------|--------------|  
|authentification-basic|Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Requis|Default|  
|----------|-----------------|--------------|-------------|  
|username|Spécifie le nom d’utilisateur associé aux informations d’identification de base.|Oui|N/A|  
|password|Spécifie le mot de passe associé aux informations d’identification de base.|Oui|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound (entrant)  
  
-   **Étendues de la stratégie :** API  
  
##  <a name="ClientCertificate"></a> Authenticate with client certificate  
 La stratégie `authentication-certificate` permet l’authentification auprès d’un service principal à l’aide d’un certificat client. Le certificat doit être [installé dans Gestion des API](http://go.microsoft.com/fwlink/?LinkID=511599) en premier et identifié par son empreinte.  
  
### <a name="policy-statement"></a>Déclaration de stratégie  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Exemple  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Requis|  
|----------|-----------------|--------------|  
|authentication-certificate|Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Requis|Default|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Empreinte du certificat client.|Oui|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound (entrant)  
  
-   **Étendues de la stratégie :** API  
  

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transformer des API](transform-api.md)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [Exemples de stratégie](policy-samples.md)   
