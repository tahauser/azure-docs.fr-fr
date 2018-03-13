---
title: "Stratégies de restriction des accès de la Gestion des API Azure | Microsoft Docs"
description: "Découvrez les stratégies de restriction des accès disponibles dans la Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 11cc5841d2f804f0d120dddda226bf05a0612607
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="api-management-access-restriction-policies"></a>Stratégies de restriction des accès de la Gestion des API
Cette rubrique est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Stratégies de restriction des accès  
  
-   [Check HTTP header](api-management-access-restriction-policies.md#CheckHTTPHeader) : applique l’existence et/ou la valeur d’un en-tête HTTP.  
-   [Limit call rate by subscription](api-management-access-restriction-policies.md#LimitCallRate) : empêche les pics d’utilisation de l’API en limitant le débit d’appels par abonnement.  
-   [Limit call rate by key](#LimitCallRateByKey) : empêche les pics d’utilisation de l’API en limitant le débit d’appels par clé.  
-   [Restrict caller IPs](api-management-access-restriction-policies.md#RestrictCallerIPs) : filtre (autorise/rejette) les appels de certaines adresses IP spécifiques et/ou de certaines plages d’adresses.  
-   [Set usage quota by subscription](api-management-access-restriction-policies.md#SetUsageQuota) : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par abonnement.  
-   [Set usage quota by key](#SetUsageQuotaByKey) : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par clé.  
-   [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) : applique l’existence et la validité d’un JWT extrait d’un en-tête HTTP ou d’un paramètre de requête spécifié.  
  
##  <a name="CheckHTTPHeader"></a> Check HTTP header  
 Utilisez la stratégie `check-header` pour imposer un en-tête HTTP donné à une demande. Vous pouvez éventuellement vérifier si l’en-tête a une certaine valeur ou une valeur comprise dans une plage de valeurs autorisées. Si la vérification échoue, la stratégie met fin au traitement de la demande et renvoie le message d’erreur et le code d’état HTTP spécifiés par la stratégie.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="True">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>exemples  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>Éléments  
  
|NOM|Description|Obligatoire|  
|----------|-----------------|--------------|  
|check-header|Élément racine.|OUI|  
|value|Valeur autorisée de l’en-tête HTTP. Lorsque plusieurs éléments de valeurs sont spécifiés, la vérification est considérée comme réussie si l’une des valeurs correspond.|Non |  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|Message d’erreur à renvoyer dans le corps de la réponse HTTP si l’en-tête n’existe pas ou a une valeur non valide. Les éventuels caractères spéciaux de ce message doivent être correctement placés dans une séquence d’échappement.|OUI|N/A|  
|failed-check-httpcode|Code d’état HTTP à renvoyer si l’en-tête n’existe pas ou a une valeur non valide.|OUI|N/A|  
|header-name|Nom de l’en-tête HTTP à vérifier.|OUI|N/A|  
|ignore-case|Peut avoir la valeur True ou False. S’il a la valeur True, la casse est ignorée lors de la comparaison de la valeur de l’en-tête à l’ensemble des valeurs acceptables.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound, outbound  
  
-   **Étendues de la stratégie :** global, product, API, operation  
  
##  <a name="LimitCallRate"></a> Limit call rate by subscription  
 La stratégie `rate-limit` évite les pics d’utilisation des API par abonnement en limitant le débit d’appels à un nombre spécifié pour une période donnée. Lorsque cette stratégie est déclenchée, l’appelant reçoit le code d’état de réponse `429 Too Many Requests`.  
  
> [!IMPORTANT]
>  Cette stratégie ne peut être utilisée qu’une seule fois par document de stratégie.  
>   
>  Les [expressions de stratégie](api-management-policy-expressions.md) ne peuvent être utilisées dans aucun attribut de cette stratégie.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="name" calls="number" renewal-period="seconds">  
        <operation name="name" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>exemples  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Éléments  
  
|NOM|Description|Obligatoire|  
|----------|-----------------|--------------|  
|set-limit|Élément racine.|OUI|  
|api|Ajoutez un ou plusieurs éléments de ce type pour imposer une limite de débit d’appels aux API au sein du produit. Les limites de débit d’appels au niveau du produit et de l’API s’appliquent indépendamment les unes des autres.|Non |  
|operation|Ajoutez un ou plusieurs éléments de ce type pour imposer une limite de débit d’appels aux opérations au sein d’une API. Les limites de débit d’appels au niveau du produit, de l’API et de l’opération s’appliquent indépendamment les unes des autres.|Non |  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|Nom|Nom de l’API à laquelle la limite de débit s’applique.|OUI|N/A|  
|calls|Nombre maximal d’appels autorisés au cours de l’intervalle de temps spécifié dans le paramètre `renewal-period`.|OUI|N/A|  
|renewal-period|Période en secondes après laquelle le quota se réinitialise.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** product  
  
##  <a name="LimitCallRateByKey"></a> Limite de débit d’appels par clé  
 La stratégie `rate-limit-by-key` évite les pics d’utilisation des API par clé en limitant le débit d’appels à un nombre spécifié pour une période donnée. La clé peut avoir une valeur de chaîne arbitraire ; elle est généralement fournie par le biais d’une expression de stratégie. Une condition d’incrément facultative peut être ajoutée pour spécifier quelles demandes doivent être comptées dans la limite. Lorsque cette stratégie est déclenchée, l’appelant reçoit le code d’état de réponse `429 Too Many Requests`.  
  
 Pour plus d’informations et d’exemples sur cette stratégie, consultez la page [Limitation avancée des demandes dans la Gestion des API Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Cette stratégie ne peut être utilisée qu’une seule fois par document de stratégie.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>exemples  
 Dans l’exemple suivant, la limite de débit est indexée par l’adresse IP de l’appelant.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Éléments  
  
|NOM|Description|Obligatoire|  
|----------|-----------------|--------------|  
|set-limit|Élément racine.|OUI|  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|calls|Nombre maximal d’appels autorisés au cours de l’intervalle de temps spécifié dans le paramètre `renewal-period`.|OUI|N/A|  
|counter-key|Clé à utiliser pour la stratégie de limite de débit.|OUI|N/A|  
|increment-condition|Expression booléenne spécifiant si la demande doit être comptée dans le quota (`true`).|Non |N/A|  
|renewal-period|Période en secondes après laquelle le quota se réinitialise.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** global, product, API, operation  
  
##  <a name="RestrictCallerIPs"></a> Restrict caller IPs  
 La stratégie `ip-filter` filtre (autorise/rejette) les appels de certaines adresses IP et/ou de certaines plages d’adresses.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>exemples  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Éléments  
  
|NOM|Description|Obligatoire|  
|----------|-----------------|--------------|  
|ip-filter|Élément racine.|OUI|  
|address|Spécifie une adresse IP unique à filtrer.|Au moins un élément `address` ou `address-range` est requis.|  
|address-range from="address" to="address"|Spécifie une plage d’adresses IP à filtrer.|Au moins un élément `address` ou `address-range` est requis.|  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|address-range from="address" to="address"|Plage d'adresses IP pour lesquelles autoriser ou refuser l'accès.|Obligatoire lorsque l’élément `address-range` est utilisé.|N/A|  
|ip-filter action="allow &#124; forbid"|Spécifie si les appels doivent être autorisés ou non pour les adresses IP et plages spécifiées.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
-   **Étendues de la stratégie :** global, product, API, operation  
  
##  <a name="SetUsageQuota"></a> Set usage quota by subscription  
 La stratégie `quota` applique un volume d’appels et/ou un quota de bande passante renouvelable ou illimité par abonnement.  
  
> [!IMPORTANT]
>  Cette stratégie ne peut être utilisée qu’une seule fois par document de stratégie.  
>   
>  Les [expressions de stratégie](api-management-policy-expressions.md) ne peuvent être utilisées dans aucun attribut de cette stratégie.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="name" calls="number" bandwidth="kilobytes">  
        <operation name="name" calls="number" bandwidth="kilobytes" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>exemples  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Éléments  
  
|NOM|Description|Obligatoire|  
|----------|-----------------|--------------|  
|quota|Élément racine.|OUI|  
|api|Ajoutez un ou plusieurs éléments de ce type pour imposer un quota aux API au sein du produit. Les quotas au niveau du produit et de l’API s’appliquent indépendamment les uns des autres.|Non |  
|operation|Ajoutez un ou plusieurs éléments de ce type pour imposer un quota aux opérations au sein d’une API. Les quotas au niveau du produit, de l’API et de l’opération s’appliquent indépendamment les uns des autres.|Non |  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|Nom|Nom de l’API ou de l’opération à laquelle s’applique le quota.|OUI|N/A|  
|bandwidth|Nombre maximal de kilo-octets autorisés au cours de l’intervalle de temps spécifié dans le paramètre `renewal-period`.|Il est obligatoire de spécifier `calls`, `bandwidth` ou les deux.|N/A|  
|calls|Nombre maximal d’appels autorisés au cours de l’intervalle de temps spécifié dans le paramètre `renewal-period`.|Il est obligatoire de spécifier `calls`, `bandwidth` ou les deux.|N/A|  
|renewal-period|Période en secondes après laquelle le quota se réinitialise.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
-   **Étendues de la stratégie :** product  
  
##  <a name="SetUsageQuotaByKey"></a> Set usage quota by key  
 La stratégie `quota-by-key` applique un volume d’appels et/ou un quota de bande passante renouvelable ou illimité par clé. La clé peut avoir une valeur de chaîne arbitraire ; elle est généralement fournie par le biais d’une expression de stratégie. Une condition d’incrément facultative peut être ajoutée pour spécifier quelles demandes doivent être comptées dans le quota.  
  
 Pour plus d’informations et d’exemples sur cette stratégie, consultez la page [Limitation avancée des demandes dans la Gestion des API Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Cette stratégie ne peut être utilisée qu’une seule fois par document de stratégie.  
>   
>  Les [expressions de stratégie](api-management-policy-expressions.md) ne peuvent être utilisées dans aucun attribut de cette stratégie.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>exemples  
 Dans l’exemple suivant, le quota est indexé par l’adresse IP de l’appelant.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Éléments  
  
|NOM|Description|Obligatoire|  
|----------|-----------------|--------------|  
|quota|Élément racine.|OUI|  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|bandwidth|Nombre maximal de kilo-octets autorisés au cours de l’intervalle de temps spécifié dans le paramètre `renewal-period`.|Il est obligatoire de spécifier `calls`, `bandwidth` ou les deux.|N/A|  
|calls|Nombre maximal d’appels autorisés au cours de l’intervalle de temps spécifié dans le paramètre `renewal-period`.|Il est obligatoire de spécifier `calls`, `bandwidth` ou les deux.|N/A|  
|counter-key|Clé à utiliser pour la stratégie de quota.|OUI|N/A|  
|increment-condition|Expression booléenne spécifiant si la demande doit être comptée dans le quota (`true`).|Non |N/A|  
|renewal-period|Période en secondes après laquelle le quota se réinitialise.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
-   **Étendues de la stratégie :** global, product, API, operation  
  
##  <a name="ValidateJWT"></a> Validate JWT  
 La stratégie `validate-jwt` applique l’existence et la validité d’un JWT extrait d’un en-tête HTTP ou d’un paramètre de requête spécifié.  
  
> [!IMPORTANT]
>  La stratégie `validate-jwt` exige que la revendication inscrite `exp` soit incluse dans le jeton JWT, sauf si l’attribut `require-expiration-time` est spécifié et a la valeur `false`.  
> La stratégie `validate-jwt` prend en charge les algorithmes de signature HS256 et RS256. Pour HS256, la clé doit être fournie en ligne au sein de la stratégie au format encodé en base 64. Pour RS256, la clé doit être fournie par le biais d’un point de terminaison de configuration Open ID.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>Exemples  
  
#### <a name="azure-mobile-services-token-validation"></a>Validation d’un jeton Azure Mobile Services  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Validation d’un jeton Azure Active Directory  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Validation d’un jeton Azure Active Directory B2C  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autoriser l’accès aux opérations à partir de revendications de jetons  
 Cet exemple montre comment utiliser la stratégie [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) pour préautoriser l’accès aux opérations à partir de revendications de jetons. Pour une démonstration de la configuration et de l’utilisation de cette stratégie, consultez la page [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Cloud Cover, épisode 177 : Plus de fonctionnalités de la Gestion des API avec Vlad Vinogradsky) et rendez-vous directement à 13 min 50 s. Rendez-vous directement à 15’00’’ pour afficher les stratégies configurées dans l’éditeur de stratégies, puis à 18’50’’ pour une démonstration de l’appel d’une opération à partir du portail des développeurs avec et sans le jeton d’autorisation requis.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Éléments  
  
|Élément|Description|Obligatoire|  
|-------------|-----------------|--------------|  
|validate-jwt|Élément racine.|OUI|  
|audiences|Contient la liste des revendications d’audience acceptables qui peuvent être présentes sur le jeton. Si plusieurs valeurs d’audience sont présentes, chacune est tentée jusqu’à ce que toutes soient épuisées (auquel cas la validation échoue) ou que l’une d’elles réussisse. Au moins une audience doit être spécifiée.|Non |  
|issuer-signing-keys|Liste de clés de sécurité encodées en base 64 utilisé pour valider les jetons signés. Si plusieurs clés de sécurité sont présentes, chacune est tentée jusqu’à ce que toutes soient épuisées (auquel cas la validation échoue) ou que l’une d’elles réussisse (utile pour la substitution de jeton). Les éléments clés ont un attribut `id` facultatif utilisé pour comparer à la revendication `kid`.|Non |  
|issuers|Liste des services principaux acceptables qui ont émis le jeton. Si plusieurs valeurs d’émetteur sont présentes, chacune est tentée jusqu’à ce que toutes soient épuisées (auquel cas la validation échoue) ou que l’une d’elles réussisse.|Non |  
|openid-config|Élément utilisé pour spécifier un point de terminaison de configuration Open ID conforme à partir duquel l’émetteur et les clés de signature peuvent être obtenus.|Non |  
|required-claims|Contient une liste de revendications censées être présentes sur le jeton pour qu’il soit considéré comme valide. Si l’attribut `match` a la valeur `all`, toutes les valeurs de revendication de la stratégie doivent être présentes dans le jeton pour que la validation réussisse. Si l’attribut `match` a la valeur `any`, au moins une revendication doit être présente dans le jeton pour que la validation réussisse.|Non |  
|zumo-master-key|Clé principale pour les jetons émis par Azure Mobile Services.|Non |  
  
### <a name="attributes"></a>Attributs  
  
|NOM|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|clock-skew|Intervalle de temps. Permet de spécifier l’écart maximal de durée estimée entre les horloges système de l’émetteur du jeton et l’instance de gestion des API.|Non |0 seconde|  
|failed-validation-error-message|Message d’erreur à renvoyer dans le corps de la réponse HTTP si le JWT n’est pas validé. Les éventuels caractères spéciaux de ce message doivent être correctement placés dans une séquence d’échappement.|Non |Le message d’erreur par défaut dépend du problème de validation, par exemple « JWT absent ».|  
|failed-validation-httpcode|Code d’état HTTP à renvoyer si le JWT n’est pas validé.|Non |401|  
|header-name|Nom de l’en-tête HTTP contenant le jeton.|Soit `header-name`, soit `query-parameter-name` doit être spécifié, mais pas les deux.|N/A|  
|id|L’attribut `id` sur l’élément `key` vous permet de spécifier la chaîne qui sera comparée à la revendication `kid` dans le jeton (le cas échéant) pour déterminer la clé appropriée à utiliser pour la validation de la signature.|Non |N/A|  
|match|L’attribut `match` sur l’élément `claim` spécifie si toutes les valeurs de revendication de la stratégie doivent être présentes dans le jeton pour que la validation réussisse. Les valeurs possibles sont les suivantes :<br /><br /> -                          `all` : toutes les valeurs de revendication de la stratégie doivent être présentes dans le jeton pour que la validation réussisse.<br /><br /> -                          `any` : au moins une valeur de revendication doit être présente dans le jeton pour que la validation réussisse.|Non |tout|  
|query-paremeter-name|Nom du paramètre de la requête contenant le jeton.|Soit `header-name`, soit `query-paremeter-name` doit être spécifié, mais pas les deux.|N/A|  
|require-expiration-time|Booléen. Spécifie si une revendication d’expiration est requise dans le jeton.|Non |true|
|require-scheme|Le nom du schéma de jeton, par ex. « Support ». Lorsque cet attribut est défini, la stratégie garantit que le schéma spécifié est présent dans la valeur d’en-tête d’autorisation.|Non |N/A|
|require-signed-tokens|Booléen. Spécifie si un jeton doit être signé.|Non |true|  
|séparateur|Chaîne. Spécifie un séparateur (par exemple, « , ») à utiliser pour extraire un ensemble de valeurs à partir d’une revendication à valeurs multiples.|Non |N/A| 
|url|URL du point de terminaison de configuration Open ID à partir de laquelle les métadonnées de configuration Open ID peuvent être récupérées. La réponse devrait correspondre aux spécifications définies dans l’URL :`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`.  Pour Azure Active Directory, utilisez l’URL suivante : `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration`, en remplaçant par le nom de votre client d’annuaire, par exemple, `contoso.onmicrosoft.com`.|OUI|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
-   **Étendues de la stratégie :** global, product, API, operation  
  
## <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [API Management policy samples](policy-samples.md) (Exemples de stratégie de gestion d’API)   
