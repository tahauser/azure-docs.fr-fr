---
title: "Gestion des erreurs dans les stratégies de la Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment répondre aux conditions d’erreur qui peuvent se produire lors du traitement des demandes dans la Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="error-handling-in-api-management-policies"></a>Gestion des erreurs dans les stratégies de la Gestion des API
La Gestion des API Azure permet aux éditeurs de répondre aux conditions d’erreur qui peuvent se produire lors du traitement des demandes au proxy en fournissant un objet `ProxyError`. L’objet `ProxyError` est accessible par la propriété [context.LastError](api-management-policy-expressions.md#ContextVariables) et peut être utilisé par les stratégies dans la section de stratégie `on-error`. Cette rubrique est une ressource de référence au sujet des fonctionnalités de gestion des erreurs dans la Gestion des API Azure.  
  
## <a name="error-handling-in-api-management"></a>Gestion des erreurs dans la Gestion des API  
 Les stratégies de la Gestion des API Azure sont divisées en sections `inbound`, `backend`, `outbound` et `on-error`, comme l’indique l’exemple suivant.  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 Lors du traitement d’une demande, les étapes intégrées sont exécutées avec toutes les stratégies qui se trouvent dans l’étendue de la demande. Si une erreur se produit, le traitement passe immédiatement à la section de stratégie `on-error`. La section de stratégie `on-error` peut être utilisée quelle que soit l’étendue, et les éditeurs d’API peuvent configurer un comportement personnalisé, par exemple l’enregistrement de l’erreur dans Event Hubs ou la création d’une nouvelle réponse à renvoyer à l’appelant.  
  
> [!NOTE]
>  La section `on-error` n’est pas présente par défaut dans les stratégies. Pour ajouter la section `on-error` à une stratégie, accédez à la stratégie de votre choix dans l’éditeur de stratégies et ajoutez-la. Pour plus d’informations sur la configuration des stratégies, consultez la page [Stratégies dans la Gestion des API](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  En l’absence de section `on-error`, les appelants recevront des messages de réponse HTTP 400 ou 500 si une condition d’erreur se produit.  
  
### <a name="policies-allowed-in-on-error"></a>Stratégies autorisées on-error  
 Les stratégies suivantes peuvent être utilisées dans la section de stratégie `on-error`.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 Lorsqu’une erreur se produit et que le contrôle passe à la section de stratégie `on-error`, l’erreur est enregistrée dans la propriété [context.LastError](api-management-policy-expressions.md#ContextVariables), à laquelle les stratégies peuvent accéder dans la section `on-error` et qui a les propriétés suivantes.  
  
|Nom|Type|Description|Requis|  
|----------|----------|-----------------|--------------|  
|Source|string|Désigne l’élément où l’erreur s’est produite. Peut être une stratégie ou un nom d’étape de pipeline intégrée.|Oui|  
|Motif|string|Code d’erreur informatique, utilisable dans la gestion des erreurs.|Non|  
|Message|string|Description lisible de l’erreur.|Oui|  
|Étendue|string|Nom de l’étendue où l’erreur s’est produite. Peut être « global », « product », « api » ou « operation ».|Non|  
|Section|string|Nom de la section où l’erreur s’est produite. Peut être « inbound », « backend », « outbound » ou « on-error ».|Non|  
|Chemin|string|Spécifie la stratégie imbriquée, par exemple, « choose[3]/when[2] ».|Non|  
|PolicyId|string|Valeur de l’attribut `id`, s’il est spécifié par le client, sur la stratégie où l’erreur s’est produite.|Non|  
  
> [!NOTE]
>  Toutes les stratégies ont un attribut `id` facultatif qui peut être ajouté à l’élément racine de la stratégie. Si cet attribut est présent dans une stratégie lorsqu’une condition d’erreur se produit, la valeur de l’attribut peut être récupérée à l’aide de la propriété `context.LastError.PolicyId`.  
  
## <a name="predefined-errors-for-built-in-steps"></a>Erreurs prédéfinies pour les étapes intégrées  
 Les erreurs suivantes sont prédéfinies pour les conditions d’erreur qui peuvent se produire lors de l’évaluation des étapes de traitement intégrées.  
  
|Source|Condition|Motif|Message|  
|------------|---------------|------------|-------------|  
|configuration|L’URI ne correspond à aucune API ou opération.|OperationNotFound|Impossible de faire correspondre la demande entrante à une opération.|  
|autorisation|Clé d’abonnement non fournie.|SubscriptionKeyNotFound|Accès refusé en raison de l’absence de clé d’abonnement. Veillez à inclure la clé d’abonnement pour effectuer des demandes auprès de cette API.|  
|autorisation|La valeur de la clé d’abonnement n’est pas valide.|SubscriptionKeyInvalid|Accès refusé en raison d’une clé d’abonnement non valide. Veillez à fournir une clé valide pour un abonnement actif.|  
  
## <a name="predefined-errors-for-policies"></a>Erreurs prédéfinies pour les stratégies  
 Les erreurs suivantes sont prédéfinies pour les conditions d’erreur qui peuvent se produire lors de l’évaluation de la stratégie.  
  
|Source|Condition|Motif|Message|  
|------------|---------------|------------|-------------|  
|rate-limit|Limite de débit dépassée.|RateLimitExceeded|Limite de débit dépassée.|  
|quota|Quota dépassé|QuotaExceeded|Quota de volume d’appels dépassé. Le quota sera réapprovisionné dans xx:xx:xx. - ou - Quota de bande passante dépassé. Le quota sera réapprovisionné dans xx:xx:xx.|  
|jsonp|La valeur du paramètre de rappel n’est pas valide (contient des caractères incorrects).|CallbackParameterInvalid|La valeur du paramètre de rappel {callback-parameter-name} n’est pas un identificateur JavaScript valide.|  
|ip-filter|Impossible d’analyser l’IP d’appelant de la demande.|FailedToParseCallerIP|Impossible d’établir l’adresse IP de l’appelant. Accès refusé.|  
|ip-filter|L’adresse IP de l’appelant ne figure pas dans la liste autorisée.|CallerIpNotAllowed|L’adresse IP de l’appelant {ip-address} n’est pas autorisée. Accès refusé.|  
|ip-filter|L’adresse IP de l’appelant figure dans la liste de blocage.|CallerIpBlocked|L’adresse IP de l’appelant est bloquée. Accès refusé.|  
|check-header|L’en-tête requis n’est pas présenté ou sa valeur est manquante.|HeaderNotFound|En-tête {header-name} introuvable dans la demande. Accès refusé.|  
|check-header|L’en-tête requis n’est pas présenté ou sa valeur est manquante.|HeaderValueNotAllowed|La valeur {header-value} de l’en-tête {header-name} n’est pas autorisée. Accès refusé.|  
|validate-jwt|Jeton JWT manquant dans la demande.|TokenNotFound|JWT introuvable dans la demande. Accès refusé.|  
|validate-jwt|Échec de validation de la signature.|TokenSignatureInvalid|<message from jwt library\>. Accès refusé.|  
|validate-jwt|Audience non valide.|TokenAudienceNotAllowed|<message from jwt library\>. Accès refusé.|  
|validate-jwt|Émetteur non valide.|TokenIssuerNotAllowed|<message from jwt library\>. Accès refusé.|  
|validate-jwt|Jeton expiré.|TokenExpired|<message from jwt library\>. Accès refusé.|  
|validate-jwt|La clé de la signature n’a pas été résolue par l’ID.|TokenSignatureKeyNotFound|<message from jwt library\>. Accès refusé.|  
|validate-jwt|Revendications requises manquantes dans le jeton.|TokenClaimNotFound|Jeton JWT manquant dans les revendications suivantes : <c1\>, <c2\>, … Accès refusé.|  
|validate-jwt|Incompatibilité des valeurs des revendications.|TokenClaimValueNotAllowed|La valeur {claim-value} de la revendication {revendication-name} n’est pas autorisée. Accès refusé.|  
|validate-jwt|Autres échecs de validation.|JwtInvalid|<message from jwt library\>|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transformer des API](transform-api.md)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [Exemples de stratégie](policy-samples.md)   