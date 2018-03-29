---
title: Informations de référence sur les jetons - Azure AD B2C | Microsoft Docs
description: Types de jetons émis dans Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: e5cc6a0974f9481491518779209ec5256870921f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: références sur les jetons

Azure Active Directory B2C (Azure AD B2C) émet plusieurs types de jetons de sécurité lors du traitement de chaque [flux d’authentification](active-directory-b2c-apps.md). Ce document décrit le format, les caractéristiques en matière de sécurité et le contenu de chaque type de jeton.

## <a name="types-of-tokens"></a>Types de jetons
Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md)qui utilise à la fois les jetons d’accès et les jetons d’actualisation. Il prend également en charge l’authentification et la connexion via [OpenID Connect](active-directory-b2c-reference-protocols.md), ce qui introduit un troisième type de jeton : le jeton d’ID. Chacun de ces jetons est représenté en tant que jeton du porteur.

Un jeton du porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le porteur désigne toute partie qui peut présenter le jeton. La partie doit d’abord s’authentifier auprès d’Azure AD B2C pour recevoir un jeton du porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons du porteur n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS).

Si un jeton du porteur est transmis en dehors d’un canal sécurisé, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons du porteur pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée.

Pour en savoir plus sur les aspects de sécurité des jetons du porteur, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par Azure AD B2C sont implémentés en tant que jetons web JSON (JWT). Un jeton JWT constitue un moyen compact et sécurisé pour les URL de transférer des informations entre deux parties. Les jetons JWT contiennent des informations appelées revendications. Il s’agit des assertions d’informations concernant le porteur et le sujet du jeton. Les revendications dans les jetons JWT sont des objets JSON codés et sérialisés pour la transmission. Étant donné que les jetons JWT émis par Azure AD B2C sont signés, mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un jeton JWT à des fins de débogage. Pour ce faire, plusieurs outils sont à votre disposition, notamment [jwt.ms](https://jwt.ms). Pour plus d’informations sur les jetons JWT, consultez les [spécifications des jetons JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Jetons d’ID

Les jetons d’ID constituent une forme de jeton de sécurité que votre application reçoit des points de terminaison `/authorize` et `/token` d’Azure AD B2C. Ils sont représentés en tant que [jetons JWT](#types-of-tokens)et contiennent des revendications que vous pouvez utiliser pour identifier l’utilisateur dans votre application. Lorsque les jetons d’ID sont acquis à partir du point de terminaison `/authorize`, ils utilisent le [flux implicite](active-directory-b2c-reference-spa.md), souvent employé en cas d’ouverture de session sur des applications web JavaScript. Lorsque les jetons d’ID sont acquis à partir du point de terminaison `/token`, ils utilisent le [flux de code confidentiel](active-directory-b2c-reference-oidc.md), qui cache le jeton au navigateur. Le jeton est ainsi envoyé en toute sécurité dans des requêtes HTTP pour permettre la communication entre deux composants de la même application ou du même service. Vous pouvez utiliser les revendications dans un jeton d’ID comme vous le souhaitez. Ils sont souvent utilisés pour afficher les informations de compte ou pour prendre des décisions de contrôle d’accès dans une application.  

Les jetons d’ID sont signés, mais ils ne sont actuellement pas chiffrés. Lorsque votre application ou votre API reçoit un jeton d’ID, elle doit [valider la signature](#token-validation) pour prouver que le jeton est authentique. Votre application ou votre API doit également valider certaines revendications du jeton pour prouver qu’il est valide. Les revendications validées par une application varient selon les spécifications du scénario, mais il existe certaines [validations de revendication communes](#token-validation) auxquelles votre application doit procéder dans chaque scénario.

#### <a name="sample-id-token"></a>Exemple de jeton d’ID
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Jetons d’accès

Les jetons d’accès constituent également une forme de jeton de sécurité que votre application reçoit des points de terminaison `/authorize` et `/token` d’Azure AD B2C. Ils sont également représentés en tant que [jetons JWT](#types-of-tokens) et contiennent des revendications que vous pouvez utiliser pour identifier les autorisations octroyées à vos API. Les jetons d’accès sont signés, mais ils ne sont actuellement pas chiffrés. Les jetons d’accès permettent de fournir l’accès aux API et serveurs de ressources. Découvrez plus d’informations sur [l’utilisation des jetons d’accès](active-directory-b2c-access-tokens.md). 

Lorsque votre API reçoit un jeton d’accès, elle doit [valider la signature](#token-validation) pour prouver que le jeton est authentique. Votre API doit également valider certaines revendications du jeton pour prouver qu’il est valide. Les revendications validées par une application varient selon les spécifications du scénario, mais il existe certaines [validations de revendication communes](#token-validation) auxquelles votre application doit procéder dans chaque scénario.

### <a name="claims-in-id-and-access-tokens"></a>Revendications dans les jetons d’ID et d’accès

Lorsque vous utilisez Azure AD B2C, vous disposez d’un contrôle affiné du contenu de vos jetons. Vous pouvez configurer des [stratégies](active-directory-b2c-reference-policies.md) pour envoyer certains ensembles de données utilisateur dans les revendications dont votre application a besoin pour ses opérations. Ces revendications peuvent inclure des propriétés standard telles que `displayName` et `emailAddress` de l’utilisateur. Elles peuvent également inclure des [attributs d’utilisateur personnalisés](active-directory-b2c-reference-custom-attr.md) que vous pouvez définir dans votre répertoire B2C. Chaque jeton d’ID et d’accès que vous recevez contient un certain ensemble de revendications relatives à la sécurité. Vos applications peuvent utiliser ces revendications pour authentifier de manière sécurisée les utilisateurs et les demandes.

Les revendications dans les jetons d’ID ne sont pas retournées dans un ordre particulier. En outre, de nouvelles revendications peuvent être introduites dans les jetons d’ID à tout moment. Votre application ne doit pas s’arrêter lors de l’ajout de nouvelles revendications. Voici les revendications qui doivent exister dans les jetons d’ID et d’accès émis par Azure AD B2C. Les éventuelles revendications supplémentaires sont déterminées par des stratégies. Pour vous entraîner, essayez d’inspecter les revendications de l’exemple de jeton d’ID en le collant dans [jwt.ms](https://jwt.ms). Vous trouverez des informations supplémentaires dans les [spécifications OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

| NOM | Revendication | Exemple de valeur | Description |
| --- | --- | --- | --- |
| Audience |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Une revendication d’audience identifie le destinataire du jeton. Dans Azure AD B2C, l’audience est l’ID attribué à votre application dans le portail d’inscription de l’application. Votre application doit valider cette valeur et rejeter le jeton s’il ne correspond pas. |
| Émetteur |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |La revendication identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton. Elle identifie également le répertoire Azure AD dans lequel l’utilisateur a été authentifié. Votre application doit valider la revendication de l’émetteur de manière à s’assurer que le jeton provient bien du point de terminaison Azure Active Directory v2.0. |
| Émis à |`iat` |`1438535543` |Cette revendication est l’heure à laquelle le jeton a été émis, représentée en heure epoch. |
| Heure d’expiration |`exp` |`1438539443` |Cette revendication d’heure d’expiration est l’heure à laquelle le jeton n’est plus valide, représentée en heure epoch. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| Pas avant |`nbf` |`1438535543` |Cette revendication est l’heure à laquelle le jeton devient valide, représentée en heure epoch. Cela correspond généralement à l’heure à laquelle le jeton a été émis. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| Version |`ver` |`1.0` |Version du jeton d’ID, telle que définie par Azure AD. |
| Hachage de code |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Le hachage de code n’est inclus dans un jeton d’ID que si ce dernier est émis en même temps qu’un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d’informations sur la façon d’effectuer cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Hachage de jeton d’accès |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Le hachage de jeton d’accès n’est inclus dans un jeton d’ID que si ce dernier est émis en même temps qu’un jeton d’accès OAuth 2.0. Un hachage de jeton d’accès peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d’informations sur la façon d’effectuer cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Valeur à usage unique |`nonce` |`12345` |La valeur à usage unique est une stratégie d’atténuation des attaques par relecture de jetons. Votre application peut spécifier une valeur à usage unique dans une demande d’autorisation à l’aide du paramètre de requête `nonce` . La valeur que vous fournissez dans la demande est émise sans aucune modification dans la revendication `nonce` du jeton d’ID. Cela permet à votre application de comparer la valeur à celle spécifiée dans la demande et d’associer la session de l’application à un jeton d’ID donné. Votre application doit effectuer cette validation au cours du processus de validation du jeton d’ID. |
| Objet |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Principal sur lequel portent les informations d’assertion du jeton, comme l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. Vous pouvez l’utiliser pour effectuer des vérifications d’autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Par défaut, la revendication de l’objet est remplie avec l’ID d’objet de l’utilisateur dans le répertoire. Pour plus d’informations, consultez [Azure Active Directory B2C : configuration du jeton, de la session et de l’authentification unique](active-directory-b2c-token-session-sso.md). |
| Référence de classe du contexte d’authentification |`acr` |Non applicable |Non utilisé actuellement, sauf dans le cas de stratégies plus anciennes. Pour plus d’informations, consultez [Azure Active Directory B2C : configuration du jeton, de la session et de l’authentification unique](active-directory-b2c-token-session-sso.md). |
| Stratégie d’infrastructure de confiance |`tfp` |`b2c_1_sign_in` |Nom de la stratégie utilisée pour acquérir le jeton d’ID. |
| Moment de l’authentification |`auth_time` |`1438535543` |Cette revendication est l’heure à laquelle l’utilisateur a entré ses informations d’identification pour la dernière fois, représentée en heure epoch. |

### <a name="refresh-tokens"></a>Jetons d’actualisation
Les jetons d’actualisation sont des jetons de sécurité que votre application peut utiliser pour acquérir de nouveaux jetons d’ID et d’accès dans un flux OAuth 2.0. Ils permettent à votre application d’obtenir un accès à long terme à des ressources au nom d’un utilisateur, et ce sans nécessiter l’intervention de l’utilisateur.

Pour recevoir un jeton d’actualisation dans une réponse de jeton, votre application doit demander l’étendue `offline_acesss` . Pour en savoir plus sur les étendues `offline_access` , reportez-vous à la [référence du protocole Azure AD B2C](active-directory-b2c-reference-protocols.md).

Les jetons d’actualisation sont, et seront toujours, entièrement opaques pour votre application. Ils sont émis par Azure AD et ne peuvent être inspectés et interprétés que par Azure AD. Les jetons d’actualisation sont de longue durée. Toutefois, quand vous écrivez votre application, faites en sorte qu’elle n’attende pas un jeton d’actualisation d’une durée particulière. Les jetons d’actualisation peuvent être rendus non valides à tout moment, et ce pour diverses raisons. Pour savoir si un jeton d'actualisation est valide, votre application doit tenter de l'échanger en faisant une demande de jeton auprès d'Azure AD. C'est la seule façon de faire.

Quand vous échangez un jeton d’actualisation contre un nouveau jeton (et si l’étendue `offline_access` a été accordée à votre application), vous recevez un nouveau jeton d’actualisation dans la réponse du jeton. Vous devez enregistrer le jeton d’actualisation qui vient d’être émis. Il doit remplacer le jeton d’actualisation utilisé précédemment dans la demande. Vous avez ainsi la garantie que vos jetons d’actualisation resteront valides le plus longtemps possible.

## <a name="token-validation"></a>Validation du jeton
Pour valider un jeton, votre application doit valider la signature et les revendications de ce jeton.

Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d’explorer ces options plutôt que d’implémenter votre propre logique de validation. Les informations contenues dans ce guide peuvent vous apprendre à utiliser correctement ces bibliothèques.

### <a name="validate-the-signature"></a>valider la signature
Un jeton JWT contient trois segments séparés par le caractère `.` . Le premier segment est appelé *l’en-tête*, le deuxième le *corps* et le troisième la *signature*. Le segment de signature peut être utilisé pour valider l’authenticité du jeton afin qu’il soit approuvé par votre application.

Les jetons Azure AD B2C sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. L’en-tête du jeton contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton :

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La revendication `alg` indique l’algorithme utilisé pour signer le jeton. La revendication `kid` indique la clé publique utilisée pour signer le jeton.

À tout moment, Azure AD peut signer un jeton à l’aide de l’un des ensembles de paires de clés publique-privée. Étant donné qu’Azure AD alterne le jeu de clés possible de façon périodique, votre application doit être écrite de manière à gérer automatiquement ces changements de clés. Pour vérifier les mises à jour apportées aux clés publiques utilisées par Azure AD, spécifiez une fréquence raisonnable d’environ 24 heures.

Azure AD B2C a un point de terminaison des métadonnées OpenID Connect. Cela permet aux applications d’extraire des informations sur Azure AD B2C lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Votre répertoire B2C contient un document de métadonnées JSON pour chaque stratégie. Par exemple, le document de métadonnées pour la stratégie `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l’emplacement suivant :

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

où `fabrikamb2c.onmicrosoft.com` est le répertoire B2C utilisé pour authentifier l’utilisateur et `b2c_1_sign_in` est la stratégie utilisée pour acquérir le jeton. Afin de déterminer la stratégie utilisée pour signer un jeton (et l’emplacement à partir duquel extraire les métadonnées), deux options sont possibles. Tout d’abord, le nom de la stratégie est inclus dans la revendication `acr` du jeton. Vous pouvez analyser les revendications contenues dans le corps du jeton JWT par le biais d’un décodage base-64 du corps et la désérialisation de la chaîne JSON résultante. La revendication `acr` sera le nom de la stratégie qui a été utilisée pour émettre le jeton.  L'autre option consiste à coder la stratégie dans la valeur du paramètre `state` lors de l'émission de la requête, puis à la décoder pour déterminer la stratégie qui a été utilisée. Les 2 méthodes sont valides.

Le document de métadonnées est un objet JSON qui contient plusieurs informations utiles. Celles-ci incluent l’emplacement des points de terminaison requis pour effectuer l’authentification OpenID Connect. Elles comprennent également un `jwks_uri`qui indique l’emplacement de l’ensemble des clés publiques utilisées pour signer les jetons. Cet emplacement est fourni ici, mais il est préférable d’extraire cet emplacement de manière dynamique à l’aide du document de métadonnées et d’analyser `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Le document JSON situé à cette URL contient toutes les informations sur les clés publiques utilisées à un moment donné. Votre application peut utiliser les revendications `kid` dans l’en-tête JWT pour sélectionner la clé publique utilisée dans le document JSON pour signer un jeton donné. Elle peut ensuite procéder à la validation des signatures à l’aide de la clé publique correcte et de l’algorithme indiqué.

Ce document ne contient pas la description de la procédure de validation de la signature. De nombreuses bibliothèques open source sont disponibles pour vous aider à ce sujet si vous en avez besoin.

### <a name="validate-the-claims"></a>Valider les revendications
Quand votre application ou API reçoit un jeton d’ID, elle doit également procéder à plusieurs vérifications sur les revendications dans le jeton d’ID. Ces vérifications portent notamment sur les revendications suivantes :

* Revendication **Audience** : vérifie que le jeton d’ID était bien destiné à votre application.
* Revendications **Pas avant** et **Heure d’expiration** : vérifient que le jeton d’ID n’est pas arrivé à expiration.
* Revendication **Émetteur** : vérifie que le jeton a effectivement été émis à votre application par Azure AD.
* **Valeur à usage unique**: stratégie d’atténuation des attaques par relecture de jetons.

Pour obtenir une liste complète des validations que votre application doit effectuer, reportez-vous à la [spécification OpenID Connect](https://openid.net). Les valeurs attendues pour ces revendications sont détaillées dans la [section Jetons](#types-of-tokens)ci-dessus.  

## <a name="token-lifetimes"></a>Durées de vie des jetons
Les durées de vie du jeton suivantes sont fournies afin d’approfondir vos connaissances. Elles peuvent vous être utiles lorsque vous développez et déboguez des applications. Quand vous écrivez vos applications, faites en sorte qu’elles n’attendent pas des durées de vie constantes, car celles-ci sont amenées à changer à un moment donné. Découvrez plus d’informations sur la [personnalisation des durées de vie des jetons](active-directory-b2c-token-session-sso.md) dans Azure AD B2C.

| par jeton | Durée de vie | Description |
| --- | --- | --- |
| Jetons d’ID |1 heure |Les jetons d’ID sont généralement valides 1 heure. Votre application web peut utiliser cette durée de vie pour conserver ses propres sessions avec les utilisateurs (recommandé). Vous pouvez également choisir une durée de vie de session différente. Si votre application a besoin d’obtenir un nouveau jeton d’ID, elle doit simplement faire une nouvelle demande de connexion auprès d’Azure AD. Si l’utilisateur a une session de navigateur valide avec Azure AD, il est possible qu’il ne doive pas entrer à nouveau ses informations d’identification. |
| Jetons d’actualisation |Jusqu’à 14 jours |Un jeton d’actualisation est valide pendant 14 jours au maximum. Cependant, un jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit continuer à essayer d’utiliser un jeton d’actualisation jusqu’à ce que la demande échoue, ou jusqu’à ce que votre application remplace le jeton d’actualisation par un autre. Un jeton d’actualisation peut devenir non valide si 90 jours se sont écoulés depuis que l’utilisateur a entré ses informations d’identification pour la dernière fois. |
| Codes d’autorisation |5 minutes |La durée de vie des codes d’autorisation est intentionnellement limitée. Ils doivent être utilisés immédiatement après réception pour les jetons d’accès, les jetons d’ID ou les jetons d’actualisation. |

