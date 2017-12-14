---
title: "Modifications apportées au point de terminaison Azure AD v2.0 | Microsoft Docs"
description: "Description des modifications apportées aux protocoles de version préliminaire publique v2.0 du modèle d’application."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e6c5b891-0b5d-47dc-8184-5d0ef6a1a006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3d344529d1ac9891e5a961de630dea3c1e4caab4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="important-updates-to-the-v20-authentication-protocols"></a>Mises à jour importantes des protocoles d’authentification v2.0
À l’intention des développeurs ! Au cours des deux prochaines semaines, nous allons apporter quelques mises à jour à nos protocoles d’authentification v2.0, qui peuvent impliquer des modifications de dernière minute pour les applications que vous avez écrites pendant la période de la version préliminaire.  

## <a name="who-does-this-affect"></a>Quelles sont les applications concernées ?
Les applications qui ont été écrites pour utiliser le point de terminaison d’authentification convergé v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Pour plus d’informations sur le point de terminaison v2.0, cliquez [ici](active-directory-appmodel-v2-overview.md).

Si vous avez développé une application utilisant le point de terminaison v2.0 en la codant directement dans le protocole v2.0, à l’aide de nos intergiciels web OpenID Connect ou OAuth ou à l’aide d’autres bibliothèques tierces pour effectuer l’authentification, vous devez vous préparer à tester vos projets et à leur apporter les modifications nécessaires.

## <a name="who-doesnt-this-affect"></a>Quelles sont les applications non concernées ?
Les applications qui ont été écrites par rapport au point de terminaison d’authentification Azure AD de production :

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Ce protocole n’est pas susceptible de subir des modifications.

En outre, si votre application utilise **uniquement** notre bibliothèque ADAL pour effectuer l’authentification, vous n’avez pas à modifier quoi que ce soit.  ADAL protège votre application des modifications.  

## <a name="what-are-the-changes"></a>Quelles sont les modifications ?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Suppression de la valeur x5t des en-têtes JWT
Le point de terminaison v2.0 utilise largement les jetons JWT, qui contiennent une section de paramètres d’en-tête avec des métadonnées pertinentes sur le jeton.  Si vous décodez l’en-tête de l’un de nos jetons JWT actuels, le résultat ressemble à ce qui suit :

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Où les propriétés « x5t » et « kid » identifient la clé publique à utiliser pour valider la signature du jeton, récupérée à partir du point de terminaison des métadonnées OpenID Connect.

La modification que nous effectuons ici consiste à supprimer la propriété « x5t ».  Vous pouvez continuer à utiliser les mêmes mécanismes pour valider les jetons, mais vous devez utiliser uniquement la propriété « kid » pour récupérer la clé publique appropriée, comme spécifié dans le protocole OpenID Connect. 

> [!IMPORTANT]
> **Votre tâche : vous assurer que votre application ne dépend pas de l’existence de la valeur x5t.**
> 
> 

### <a name="removing-profileinfo"></a>Suppression de profile_info
Auparavant, le point de terminaison v2.0 retournait un objet JSON encodé en Base64 dans les réponses de jeton `profile_info`.  Lorsque vous demandiez un jeton d’accès à partir du point de terminaison v2.0 en envoyant une requête à :

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

La réponse ressemblait à l’objet JSON suivant :

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

La valeur `profile_info` contenait des informations sur l’utilisateur connecté à l’application : son nom d’affichage, son prénom, son nom, son adresse de messagerie, son identificateur, etc.  Le paramètre `profile_info` était essentiellement utilisé pour la mise en cache des jetons et l’affichage.

Nous procédons actuellement à la suppression de la valeur `profile_info` , mais ne vous inquiétez pas, nous fournirons toujours ces informations aux développeurs à un endroit légèrement différent.  À la place du paramètre `profile_info`, le point de terminaison v2.0 retourne désormais un paramètre `id_token` dans chaque réponse de jeton :

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Vous pouvez décoder et analyser le paramètre id_token pour récupérer les informations que vous receviez de profile_info.  Le paramètre id_token est un jeton JWT (JSON Web Token) comportant du contenu, comme spécifié par OpenID Connect.  Le code permettant d’effectuer cette opération est très semblable : il vous suffit d’extraire le segment de milieu (le corps) du paramètre id_token et de le décoder en Base64 pour accéder à l’objet JSON qu’il contient.

Au cours des deux prochaines semaines, vous devrez coder votre application pour récupérer les informations utilisateur des paramètres `id_token` ou `profile_info` selon celui qui sera présent.  Ce faisant, lorsque la modification sera effectuée, votre application pourra gérer en toute transparence la transition de `profile_info` à `id_token` sans interruption.

> [!IMPORTANT]
> **Votre tâche : vérifier que votre application ne dépend pas de l’existence de la valeur `profile_info`.**
> 
> 

### <a name="removing-idtokenexpiresin"></a>Suppression de id_token_expires_in
Comme pour le paramètre `profile_info`, nous allons également supprimer le paramètre `id_token_expires_in` dans les réponses.  Auparavant, le point de terminaison v2.0 retournait une valeur pour le paramètre `id_token_expires_in` avec chaque réponse id_token, par exemple dans une réponse d’autorisation :

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou dans une réponse de jeton :

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

La valeur `id_token_expires_in` indiquait le nombre de secondes pendant lesquelles le paramètre id_token restait valide.  À présent, nous allons supprimer complètement la valeur `id_token_expires_in` .  À la place, vous pourrez utiliser les revendications OpenID Connect `nbf` et `exp` standard pour examiner la validité d’un paramètre id_token.  Pour plus d’informations sur ces revendications, consultez les [Informations de référence sur les jetons v2.0](active-directory-v2-tokens.md)

> [!IMPORTANT]
> **Votre tâche : vérifier que votre application ne dépend pas de l’existence de la valeur `id_token_expires_in`.**
> 
> 

### <a name="changing-the-claims-returned-by-scopeopenid"></a>Modification des revendications retournées par scope=openid
Cette modification sera la plus importante : de fait, elle concerne pratiquement toutes les applications qui utilisent le point de terminaison v2.0.  De nombreuses applications envoient des requêtes vers le point de terminaison v2.0 en utilisant l’étendue `openid`, comme :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Aujourd’hui, lorsque l’utilisateur autorise l’étendue `openid`, votre application reçoit une mine d’informations sur l’utilisateur dans le paramètre id_token résultant.  Ces revendications peuvent inclure son nom, son nom d’utilisateur par défaut, son adresse de messagerie, son ID objet, etc.

Au cours de cette mise à jour, nous allons modifier les informations auxquelles l’étendue `openid` permet à votre application d’accéder afin d’assurer une meilleure conformité avec la spécification OpenID Connect.  L’étendue `openid` permettra uniquement à votre application de connecter l’utilisateur et de recevoir un identificateur propre à l’application pour l’utilisateur dans la revendication `sub` du paramètre id_token.  Les revendications contenues dans un paramètre id_token avec uniquement l’étendue `openid` accordée seront dépourvues des informations d’identification personnelle.  Exemples de revendication contenus dans le paramètre id_token :

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Si vous souhaitez obtenir des informations d’identification personnelle (PII) sur l’utilisateur dans votre application, votre application devra lui demander des autorisations supplémentaires.  Nous introduisons la prise en charge de deux nouvelles étendues de la spécification OpenID Connect, les étendues `email` et `profile`, qui vous permettront d’effectuer cette opération.

* L’étendue `email` est très directe : elle permet à votre application d’accéder à l’adresse de messagerie principale de l’utilisateur via la revendication `email` contenue dans le paramètre id_token.  Notez que la revendication `email` n’est pas toujours présente dans le paramètre id_token : elle n’est incluse que si elle est disponible dans le profil de l’utilisateur.
* L’étendue `profile` permet à votre application d’accéder à toutes les autres informations de base sur l’utilisateur (nom, nom d’utilisateur par défaut, ID objet, etc.).

Vous pouvez ainsi coder votre application en en divulguant le moins possible : vous pouvez vous contenter de demander à l’utilisateur les informations nécessaires à l’exécution de votre application.  Si vous souhaitez continuer à obtenir les informations utilisateur complètes reçues actuellement par votre application, vous devez inclure les trois étendues dans vos demandes d’autorisation :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Votre application peut commencer à envoyer immédiatement les étendues `email` et `profile`, et le point de terminaison v2.0 acceptera ces deux étendues et commencera à demander des autorisations aux utilisateurs, si nécessaire.  Toutefois, la modification de l’interprétation de l’étendue `openid` ne prendra pas effet avant quelques semaines.

> [!IMPORTANT]
> **Votre tâche : ajouter les étendues `profile` et `email` si votre application a besoin d’informations sur l’utilisateur.**  Notez que la bibliothèque ADAL inclut ces deux autorisations dans les requêtes par défaut. 
> 
> 

### <a name="removing-the-issuer-trailing-slash"></a>Suppression de la barre oblique finale dans la valeur issuer
Auparavant, la valeur issuer qui apparaissait dans les jetons du point de terminaison v2.0 prenait la forme suivante :

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Où le GUID était l’ID du locataire Azure AD qui a émis le jeton.  Avec ces modifications, la valeur issuer devient :

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

dans les deux jetons et dans le document de découverte OpenID Connect.

> [!IMPORTANT]
> **Votre travail : vous assurer que votre application accepte la valeur issuer avec et sans barre oblique finale lors de la validation de l’émetteur.**
> 
> 

## <a name="why-change"></a>Pourquoi introduire des modifications ?
La principale motivation ayant amené à introduire ces modifications est la conformité à la spécification standard OpenID Connect.  Avec cette conformité, nous espérons réduire les différences entre l’intégration aux services d’identité de Microsoft et l’intégration aux autres services d’identité du secteur d’activité.  Nous souhaitons permettre aux développeurs d’utiliser leurs bibliothèques d’authentification open source favorites sans avoir à les modifier pour prendre en compte les différences de Microsoft.

## <a name="what-can-you-do"></a>Que pouvez-vous faire ?
Dès aujourd’hui, vous pouvez commencer à effectuer toutes les modifications décrites ci-dessus.  Vous devez immédiatement :

1. **Supprimer toutes les dépendances dans le paramètre d’en-tête `x5t`.**
2. **Gérer en douceur la transition entre les paramètres `profile_info` et `id_token` dans les réponses de jeton.**
3. **Supprimer toutes les dépendances dans le paramètre de réponse `id_token_expires_in`.**
4. **Ajouter les étendues `profile` et `email` à votre application, si celle-ci a besoin d’informations utilisateur de base.**
5. **Accepter les valeurs issuer dans les jetons avec et sans barre oblique finale.**

Notre [documentation relative au protocole v2.0](active-directory-v2-protocols.md) a déjà été mise à jour pour refléter ces modifications. Vous pouvez donc l’utiliser à titre de référence pour vous aider à mettre à jour votre code.

Si vous avez d’autres questions sur l’étendue de ces modifications, n’hésitez pas à nous contacter sur Twitter à @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Quelle sera la fréquence des modifications du protocole ?
Nous ne prévoyons pas d’apporter d’autres modifications de ce type aux protocoles d’authentification.  Nous regroupons intentionnellement ces modifications dans une seule version afin que vous n’ayez pas à effectuer de nouveau ce type de mise à jour dans un temps rapproché.  Bien entendu, nous continuerons d’ajouter des fonctionnalités au service d’authentification v2.0 convergé dont vous pourrez bénéficier. Néanmoins, ces modifications devraient constituer des ajouts et pas une rupture du code existant.

Pour finir, nous souhaitons vous remercier d’avoir procédé à des essais pendant cette version préliminaire.  Les informations et les expériences de nos utilisateurs précoces nous ont été très utiles jusqu’à présent, et nous espérons que vous continuerez de partager vos opinions et vos idées.

Codez bien !

La Division d’identité de Microsoft

