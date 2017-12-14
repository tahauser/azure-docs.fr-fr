---
title: "Expressions de stratégie de la Gestion des API Azure | Microsoft Docs"
description: "Découvrez les expressions de stratégie dans la Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 58f7f71fd619eea2865ed42d2808fe6ae3e75c1f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-policy-expressions"></a>Expressions de stratégie de la Gestion des API
La syntaxe des expressions de stratégie est C# 6.0. Chaque expression a accès à la variable de [contexte](api-management-policy-expressions.md#ContextVariables) fournie implicitement et à un [sous-ensemble](api-management-policy-expressions.md#CLRTypes) autorisé de types .NET Framework.  
  
> [!TIP]
>  Pour plus d’informations sur les expressions de stratégie, regardez la vidéo [Expressions de stratégie](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/).  
>   
>  Pour une démonstration de la configuration des stratégies avec des expressions de stratégie, consultez la page [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Cloud Cover, épisode 177 : Plus de fonctionnalités de la Gestion des API avec Vlad Vinogradsky). Cette vidéo contient les démonstrations des expressions de stratégie suivantes.  
>   
>  -   10 min 30 s : découvrez comment appliquer la stratégie au niveau de l’API pour fournir des informations de contexte au service principal à l’aide des stratégies [Set query string parameter](api-management-transformation-policies.md#SetQueryStringParameter) et [Set HTTP header](api-management-transformation-policies.md#SetHTTPheader). À 12 min 10 s, une démonstration de l’appel d’une opération dans le portail des développeurs montre ces stratégies à l’œuvre.  
> -   13 min 50 s : découvrez comment utiliser la stratégie [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) pour préautoriser l’accès aux opérations à partir de revendications de jetons. Rendez-vous directement à 15’00’’ pour afficher les stratégies configurées dans l’éditeur de stratégies, puis à 18’50’’ pour une démonstration de l’appel d’une opération à partir du portail des développeurs avec et sans le jeton d’autorisation requis.  
> -   21 min 00 s : découvrez comment utiliser une trace [Inspecteur d’API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) pour voir comment les stratégies sont évaluées et connaître les résultats des évaluations.  
> -   25 min 25 s : découvrez comment utiliser les expressions de stratégie avec les stratégies [Get from cache](api-management-caching-policies.md#GetFromCache) et [Store to cache](api-management-caching-policies.md#StoreToCache) pour configurer la durée de mise en cache des réponses de la Gestion des API qui correspond à la mise en cache des réponses du service principal comme le spécifie la directive `Cache-Control` du service principal.  
> -   34 min 30 s : découvrez comment effectuer un filtrage du contenu en supprimant des éléments de données de la réponse reçue du service principal à l’aide des stratégies [Control flow](api-management-advanced-policies.md#choose) et [Set body](api-management-transformation-policies.md#SetBody). Commencez à 31 min 50 s pour voir une présentation de [l’API The Dark Sky Forecast](https://developer.forecast.io/) utilisée pour cette démonstration.  
> -   Pour télécharger les instructions de stratégies utilisées dans cette vidéo, consultez le référentiel GitHub [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).  
  
  
##  <a name="Syntax"></a> Syntaxe  
 Les expressions à instruction unique sont entre `@(expression)`, où `expression` est une instruction d’expression C# bien formée.  
  
 Les expressions à instructions multiples sont entre `@{expression}`. Tous les chemins d’accès de code au sein des expressions à instructions multiples doivent se terminer par une instruction `return`.  
  
##  <a name="PolicyExpressionsExamples"></a> Exemples  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Utilisation  
 Les expressions peuvent être utilisées comme valeurs d’attribut ou valeurs de texte dans l’une des [stratégies](api-management-policies.md) de la Gestion des API, sauf si la référence de la stratégie le spécifie autrement.  
  
> [!IMPORTANT]
>  Notez que, lorsque vous utilisez des expressions de stratégie, elles ne font l’objet que d’une vérification limitée lors de la définition de la stratégie. Étant donné que les expressions sont exécutées à l’exécution dans le pipeline inbound ou outbound par la passerelle, toutes les exceptions d’exécution générées par les expressions de stratégie entraîneront une erreur d’exécution dans l’appel d’API.  
  
##  <a name="CLRTypes"></a> Types .NET Framework autorisés dans les expressions de stratégie  
 Le tableau suivant liste les types .NET Framework et leurs membres qui sont autorisés dans les expressions de stratégie.  
  
|Type CLR|Méthodes prises en charge|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JArray|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JConstructor|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JContainer|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JObject|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JProperty|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JRaw|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JToken|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JTokenType|Toutes les méthodes sont prises en charge.|  
|Newtonsoft.Json.Linq.JValue|Toutes les méthodes sont prises en charge.|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Tout|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Tout|  
|System.Collections.Generic.ISet<TKey, TValue>|Tout|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key, Value|  
|System.Collections.Generic.List<TKey, TValue>|Tout|  
|System.Collections.Generic.Queue<TKey, TValue>|Tout|  
|System.Collections.Generic.Stack<TKey, TValue>|Tout|  
|System.Convert|Tout|  
|System.DateTime|Tout|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|Tout|  
|System.Decimal|Tout|  
|System.Double|Tout|  
|System.Guid|Tout|  
|System.IEnumerable<T\>|Tout|  
|System.IEnumerator<T\>|Tout|  
|System.Int16|Tout|  
|System.Int32|Tout|  
|System.Int64|Tout|  
|System.Linq.Enumerable<T\>|Toutes les méthodes sont prises en charge.|  
|System.Math|Tout|  
|System.MidpointRounding|Tout|  
|System.Nullable<T\>|Tout|  
|System.Random|Tout|  
|System.SByte|Tout|  
|System.Security.Cryptography. HMACSHA384|Tout|  
|System.Security.Cryptography. HMACSHA512|Tout|  
|System.Security.Cryptography.HashAlgorithm|Tout|  
|System.Security.Cryptography.HMAC|Tout|  
|System.Security.Cryptography.HMACMD5|Tout|  
|System.Security.Cryptography.HMACSHA1|Tout|  
|System.Security.Cryptography.HMACSHA256|Tout|  
|System.Security.Cryptography.KeyedHashAlgorithm|Tout|  
|System.Security.Cryptography.MD5|Tout|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Tout|  
|System.Security.Cryptography.SHA1|Tout|  
|System.Security.Cryptography.SHA1Managed|Tout|  
|System.Security.Cryptography.SHA256|Tout|  
|System.Security.Cryptography.SHA256Managed|Tout|  
|System.Security.Cryptography.SHA384|Tout|  
|System.Security.Cryptography.SHA384Managed|Tout|  
|System.Security.Cryptography.SHA512|Tout|  
|System.Security.Cryptography.SHA512Managed|Tout|  
|System.Single|Tout|  
|System.String|Tout|  
|System.StringSplitOptions|Tout|  
|System.Text.Encoding|Tout|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item|  
|System.Text.RegularExpressions.Group|Captures, Success|  
|System.Text.RegularExpressions.GroupCollection|Count, Item|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|.ctor, IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Tout|  
|System.Tuple|Tout|  
|System.UInt16|Tout|  
|System.UInt32|Tout|  
|System.UInt64|Tout|  
|System.Uri|Tout|  
|System.Xml.Linq.Extensions|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XAttribute|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XCData|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XComment|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XContainer|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XDeclaration|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XDocument|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XDocumentType|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XElement|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XName|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XNamespace|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XNode|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XNodeEqualityComparer|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XObject|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XProcessingInstruction|Toutes les méthodes sont prises en charge.|  
|System.Xml.Linq.XText|Toutes les méthodes sont prises en charge.|  
|System.Xml.XmlNodeType|Tout|  
  
##  <a name="ContextVariables"></a> Variable de contexte  
 Une variable nommée `context` est implicitement disponible dans toutes les [expressions](api-management-policy-expressions.md#Syntax) de stratégie. Ses membres fournissent des informations pertinentes pour la `\request`. Tous les membres `context` sont en lecture seule.  
  
|Variable de contexte|Méthodes, propriétés et valeurs de paramètres autorisées|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> Déploiement<br /><br /> LastError<br /><br /> Opération<br /><br /> Produit<br /><br /> Demande<br /><br /> RequestId: Guid<br /><br /> Réponse<br /><br /> Abonnement<br /><br /> Tracing: bool<br /><br /> Utilisateur<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificats : IReadOnlyDictionary<string, X509Certificate2>|  
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Pour plus d’informations sur context.LastError, consultez la page [Gestion des erreurs](api-management-error-handling-policies.md).|  
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Renvoie des valeurs d’en-tête de demande séparées par des virgules ou `defaultValue` si l’en-tête est introuvable.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Renvoie des valeurs d’en-tête de réponse séparées par des virgules ou `defaultValue` si l’en-tête est introuvable.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|  
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: string<br /><br /> Name: string|  
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Les méthodes `context.Request.Body.As<T>` et `context.Response.Body.As<T>` sont utilisées pour lire un corps de message de demande et de réponse dans un type `T` spécifié. Par défaut, la méthode utilise le flux du corps du message d’origine et le rend indisponible après son retour. Pour éviter cela en faisant en sorte que la méthode travaille sur une copie du flux du corps, définissez le paramètre `preserveContent` sur `true`. Accédez [ici](api-management-transformation-policies.md#SetBody) pour voir un exemple.|  
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|  
|IUserIdentity|Id: string<br /><br /> Provider: string|  
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Renvoie des valeurs de paramètre de requête séparées par des virgules ou `defaultValue` si le paramètre est introuvable.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Renvoie la valeur de variable avec conversion de type vers `T` ou `defaultValue` si la variable est introuvable.<br /><br /> Cette méthode lève une exception si le type spécifié ne correspond pas au type réel de la variable renvoyée.|  
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Si le paramètre d’entrée contient une valeur valide d’en-tête de demande d’authentification de base HTTP, la méthode renvoie un objet de type `BasicAuthCredentials` ; sinon, la méthode renvoie Null.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Si le paramètre d’entrée contient une valeur valide d’en-tête de demande d’authentification de base HTTP, la méthode renvoie `true` et le paramètre de résultats contient une valeur de type `BasicAuthCredentials` ; sinon, la méthode renvoie `false`.|  
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|  
|Jwt AsJwt(input: this string)|input: string<br /><br /> Si le paramètre d’entrée contient une valeur valide d’en-tête de jeton JWT, la méthode renvoie un objet de type `Jwt` ; sinon, la méthode renvoie `null`.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Si le paramètre d’entrée contient une valeur valide d’en-tête de jeton JWT, la méthode renvoie `true` et le paramètre de résultats contient une valeur de type `Jwt` ; sinon, la méthode renvoie `false`.|  
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Renvoie des valeurs de revendication séparées par des virgules ou `defaultValue` si l’en-tête est introuvable.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - texte brut à déchiffrer<br /><br />alg - nom de l'algorithme de chiffrement symétrique<br /><br />key - clé de chiffrement<br /><br />iv - vecteur d'initialisation<br /><br />Renvoie un texte brut chiffré.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - texte brut à déchiffrer<br /><br />alg - algorithme de chiffrement<br /><br />Renvoie un texte brut chiffré.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - texte brut à déchiffrer<br /><br />alg - algorithme de chiffrement<br /><br />key - clé de chiffrement<br /><br />iv - vecteur d'initialisation<br /><br />Renvoie un texte brut chiffré.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - texte à déchiffrer<br /><br />alg - nom de l'algorithme de chiffrement symétrique<br /><br />key - clé de chiffrement<br /><br />iv - vecteur d'initialisation<br /><br />Renvoie un texte brut.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - texte à déchiffrer<br /><br />alg - algorithme de chiffrement<br /><br />Renvoie un texte brut.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - texte à déchiffrer<br /><br />alg - algorithme de chiffrement<br /><br />key - clé de chiffrement<br /><br />iv - vecteur d'initialisation<br /><br />Renvoie un texte brut.|

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player] 
>
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transformer des API](transform-api.md)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [Exemples de stratégie](policy-samples.md)   