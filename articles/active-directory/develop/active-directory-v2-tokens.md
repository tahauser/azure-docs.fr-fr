---
title: Informations de référence sur les jetons Azure Active Directory v2.0 | Microsoft Docs
description: Types de jeton et de revendication émis par le point de terminaison Azure AD v2.0
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4479b3d34824b88f0a666b6185a6bc89337358a9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Informations de référence sur les jetons Azure Active Directory v2.0
Le point de terminaison Azure Active Directory (Azure AD) v2.0 émet plusieurs types de jeton de sécurité dans chaque [flux d’authentification](active-directory-v2-flows.md). Ces informations de référence décrivent le format, les caractéristiques en matière de sécurité et le contenu de chaque type de jeton.

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Types de jetons
Le point de terminaison v2.0 prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-v2-protocols.md) qui utilise des jetons d’accès et des jetons d’actualisation. Le point de terminaison v2.0 prend également en charge l’authentification et la connexion à l’aide [d’OpenID Connect](active-directory-v2-protocols.md). OpenID Connect introduit un troisième type de jeton, le jeton d’ID. Chacun de ces jetons est représenté en tant que jeton du *porteur*.

Un jeton du porteur est un jeton de sécurité léger qui octroie au porteur l’accès à une ressource protégée. En ce sens, le porteur désigne toute partie qui peut présenter le jeton. Même si une partie doit s’authentifier auprès d’Azure AD pour recevoir le jeton du porteur, si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons du porteur n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton du porteur est transmis sans ce type de sécurité, une partie malveillante peut utiliser une attaque de l’intercepteur « man in the middle » pour acquérir le jeton et l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons du porteur. Pour en savoir plus sur les aspects de sécurité des jetons du porteur, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

De nombreux jetons émis par le point de terminaison v2.0 sont implémentés en tant que jetons JSON Web Token (JWT). Un jeton JWT constitue un moyen compact et sécurisé pour les URL de transférer des informations entre deux parties. Les informations contenues dans un jeton JWT sont appelées une *revendication*. Il s’agit d’une assertion d’informations concernant le porteur et le sujet du jeton. Les revendications contenues dans un jeton JWT sont des objets JSON (JavaScript Object Notation) encodés et sérialisés pour la transmission. Comme les jetons JWT émis par le point de terminaison v2.0 sont signés, mais pas chiffrés, vous pouvez facilement inspecter leur contenu à des fins de débogage. Pour plus d’informations sur les jetons JWT, consultez les [spécifications des jetons JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Jetons d’ID
Un jeton d’ID est une forme de jeton de sécurité de connexion que votre application reçoit quand elle procède à l’authentification à l’aide [d’OpenID Connect](active-directory-v2-protocols.md). Les jetons d’ID sont représentés en tant que [jetons JWT](#types-of-tokens) et contiennent des revendications que vous pouvez utiliser pour connecter l’utilisateur à votre application. Vous pouvez utiliser les revendications d’un jeton d’ID de différentes façons. En règle générale, les administrateurs utilisent les jetons d’ID pour afficher les informations de compte ou pour prendre des décisions de contrôle d’accès dans une application. Le point de terminaison v2.0 émet un seul type de jeton d’ID, dont le jeu de revendications est cohérent quel que soit le type d’utilisateur connecté. Le format et le contenu des jetons d’ID sont identiques pour les utilisateurs titulaires d’un compte Microsoft personnel et d’un compte professionnel ou scolaire.

Pour le moment, les jetons d’ID sont signés, mais pas chiffrés. Quand votre application reçoit un jeton d’ID, elle doit [valider la signature](#validating-tokens) pour prouver l’authenticité du jeton et valider certaines revendications du jeton pour prouver sa validité. Les revendications validées par une application varient selon les spécifications du scénario, mais il existe certaines [validations de revendication communes](#validating-tokens) auxquelles votre application doit procéder dans chaque scénario.

Outre un exemple de jeton d’ID, nous vous indiquons les détails complets sur les revendications des jetons d’ID. Les revendications contenues dans les jetons d’ID ne sont pas retournées dans un ordre spécifique. En outre, de nouvelles revendications peuvent être introduites dans les jetons d’ID à tout moment. Votre application ne doit pas s’arrêter lorsque de nouvelles revendications sont ajoutées. La liste suivante répertorie les revendications qui sont correctement interprétées par votre application. Vous trouverez des informations supplémentaires dans les [spécifications OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Exemple de jeton d’ID
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Pour vous entraîner, inspectez les revendications de l’exemple de jeton d’ID, puis collez-le dans [calebb.net](http://calebb.net/).
>
>

#### <a name="claims-in-id-tokens"></a>Revendications des jetons d’ID
| NOM | Revendication | Exemple de valeur | Description |
| --- | --- | --- | --- |
| audience |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifie le destinataire du jeton. Dans les jetons d’ID, l’audience est l’ID attribué à votre application dans le portail d’inscription des applications Microsoft. Votre application doit valider cette valeur et rejeter le jeton si la valeur ne correspond pas. |
| émetteur |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le client Azure AD dans lequel l’utilisateur a été authentifié. Votre application doit valider la revendication de l’émetteur de manière à s’assurer que le jeton provient bien du point de terminaison v2.0. Il doit également utiliser la partie GUID de la revendication pour restreindre l’ensemble des clients qui peuvent se connecter à l’application. La partie GUID qui indique que l’utilisateur est un utilisateur consommateur d’un compte Microsoft est `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| émis à |`iat` |`1452285331` |Heure à laquelle le jeton a été émis, représentée en heure epoch. |
| heure d’expiration |`exp` |`1452289231` |Heure à laquelle le jeton n’est plus valide, représentée en heure epoch. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| pas avant |`nbf` |`1452285331` |Heure à laquelle le jeton est valide, représentée en heure epoch. Elle est généralement identique à l'heure d'émission. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| version |`ver` |`2.0` |Version du jeton d’ID, telle que définie par Azure AD. Pour le point de terminaison v2.0, la valeur est `2.0`. |
| ID client |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |GUID représentant le client Azure AD d’où provient l’utilisateur. Pour les comptes professionnels et scolaires, le GUID correspond à l’ID de client immuable de l’organisation à laquelle appartient l’utilisateur. Pour les comptes personnels, la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`. L’étendue `profile` est requise afin de recevoir cette revendication. |
| hachage de code |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Le hachage de code n’est inclus dans un jeton d’ID que si ce dernier est émis avec un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d’informations sur l’exécution de cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) . |
| hachage de jeton d’accès |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Le hachage de jeton d’accès n’est inclus dans un jeton d’ID que si ce dernier est émis avec un jeton d’accès OAuth 2.0. Il peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d’informations sur l’exécution de cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) . |
| nonce |`nonce` |`12345` |La valeur à usage unique est une stratégie d’atténuation des attaques par relecture de jetons. Votre application peut spécifier une valeur à usage unique dans une demande d’autorisation à l’aide du paramètre de requête `nonce` . La valeur que vous fournissez dans la requête est émise dans la revendication `nonce` du jeton d’ID, sans aucune modification. Votre application peut comparer la valeur à celle spécifiée dans la requête et associer la session de l’application à un jeton d’ID particulier. Votre application doit effectuer cette validation au cours du processus de validation du jeton d’ID. |
| Nom |`name` |`Babe Ruth` |La revendication de nom fournit une valeur explicite qui identifie le sujet du jeton. Il n’est pas certain que cette valeur soit unique. Elle est mutable et conçue pour être utilisée uniquement à des fins d’affichage. L’étendue `profile` est requise afin de recevoir cette revendication. |
| email |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Adresse de messagerie principale associée au compte d’utilisateur, le cas échéant. Sa valeur est mutable et peut changer au fil du temps. L’étendue `email` est requise afin de recevoir cette revendication. |
| nom d’utilisateur par défaut |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Nom d’utilisateur principal qui représente l’utilisateur dans le point de terminaison v2.0. Il peut s’agir d’une adresse e-mail, d’un numéro de téléphone ou d’un nom d’utilisateur générique sans format spécifié. Sa valeur est mutable et peut changer au fil du temps. Dans la mesure où elle est mutable, cette valeur ne doit pas utilisée pour prendre des décisions d’autorisation. L’étendue `profile` est requise afin de recevoir cette revendication. |
| subject |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. Vous pouvez l’utiliser pour effectuer des vérifications d’autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource et qu’il peut servir de clé dans les tables de base de données. Étant donné que le sujet est toujours présent dans les jetons émis par Azure AD, nous vous recommandons d’utiliser cette valeur dans un système d’autorisation général. Toutefois, l’objet est un identificateur par paire ; il est unique à un ID d’application donné.  Par conséquent, si un utilisateur se connecte à deux applications différentes à l’aide de deux ID clients différents, ces applications reçoivent deux valeurs différentes pour la revendication de l’objet.  Ceci peut être souhaitable ou non en fonction de vos besoins d’architecture et de confidentialité. |
| ID d’objet |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Identificateur immuable pour un objet dans le système d’identité Microsoft, dans cet exemple, un compte d’utilisateur.  Il peut également être utilisé pour effectuer des vérifications d’autorisation en toute sécurité et en tant que clé dans les tables de base de données. Cet ID identifie de manière unique l’utilisateur entre les applications ; deux applications différentes se connectant au même utilisateur auront la même valeur dans la revendication `oid`.  Cela signifie qu’il peut être utilisé lors de la formulation de requêtes auprès de services Microsoft en ligne, tels que Microsoft Graph.  Microsoft Graph renverra cet ID en tant que propriété `id` pour un compte d’utilisateur donné.  `oid` permettant à plusieurs applications de faire correspondre des utilisateurs, l’étendue `profile` est requise afin de recevoir cette revendication. Notez que si un utilisateur existe dans plusieurs locataires, l’utilisateur contient un ID d’objet différent dans chaque locataire. Ils sont considérés comme des comptes différents, même si l’utilisateur se connecte à chaque compte avec les mêmes informations d’identification. |

### <a name="access-tokens"></a>Jetons d’accès
Actuellement, les jetons d’accès émis par le point de terminaison v2.0 peuvent être utilisés uniquement par les services Microsoft. Vos applications n’ont pas besoin de valider ou d’inspecter les jetons d’accès dans les scénarios actuellement pris en charge. Vous pouvez traiter les jetons d’accès complètement opaques. Ce sont seulement des chaînes que votre application peut transmettre à Microsoft dans des requêtes HTTP.

Dans un avenir proche, le point de terminaison v2.0 permettra à votre application de recevoir des jetons d’accès à partir d’autres clients. Nous mettrons alors à jour les informations de cette rubrique de référence pour refléter les données dont vous avez besoin pour que votre application valide les jetons d’accès et effectue d’autres tâches similaires.

Quand vous demandez un jeton d’accès auprès du point de terminaison v2.0, ce dernier retourne également des métadonnées sur le jeton d’accès que votre application peut utiliser. Ces informations incluent le délai d’expiration du jeton d’accès et les étendues dans lesquelles il est valide. Votre application utilise ces métadonnées pour effectuer une mise en cache intelligente des jetons d’accès sans avoir à les analyser.

### <a name="refresh-tokens"></a>Jetons d’actualisation
Les jetons d’actualisation sont des jetons de sécurité que votre application peut utiliser pour obtenir de nouveaux jetons d’accès dans un flux OAuth 2.0. Votre application peut utiliser des jetons d’actualisation pour obtenir un accès à long terme à des ressources au nom d’un utilisateur, et ce sans nécessiter l’intervention de l’utilisateur.

Les jetons d’actualisation prennent en charge plusieurs ressources. Un jeton d’actualisation reçu au cours d’une demande de jeton pour une ressource peut être échangé contre des jetons d’accès à une ressource complètement différente.

Pour recevoir une actualisation dans une réponse de jeton, votre application doit demander et se voir accorder l’étendue `offline_acesss`. Pour en savoir plus sur l’étendue `offline_access`, consultez [l’article sur le consentement et les étendues](active-directory-v2-scopes.md).

Les jetons d’actualisation sont, et seront toujours, entièrement opaques pour votre application. Émis par le point de terminaison Azure AD v2.0, ils ne peuvent être inspectés et interprétés que par le point de terminaison v2.0. Les jetons d’actualisation sont de longue durée. Toutefois, quand vous écrivez votre application, faites en sorte qu’elle n’attende pas un jeton d’actualisation d’une durée particulière. Les jetons d’actualisation peuvent être rendus non valides à tout moment pour diverses raisons (voir [Révocation de jetons](active-directory-token-and-claims.md#token-revocation)). Pour savoir si un jeton d’actualisation est valide, votre application doit tenter de l’échanger en faisant une demande de jeton auprès du point de terminaison v2.0. C'est la seule façon de faire.

Quand vous échangez un jeton d’actualisation contre un nouveau jeton d’accès (et si l’étendue `offline_access` a été accordée à votre application), vous recevez un nouveau jeton d’actualisation dans la réponse du jeton. Enregistrez le jeton d’actualisation nouvellement émis pour remplacer celui que vous avez utilisé dans la demande. Vous avez ainsi la garantie que vos jetons d’actualisation resteront valides le plus longtemps possible.

## <a name="validating-tokens"></a>Validation des jetons
Pour le moment, la seule opération de validation que vos applications doivent effectuer est la validation des jetons d’ID. Pour valider un jeton d’ID, votre application doit valider à la fois la signature du jeton d’ID et les revendications qu’il contient.

<!-- TODO: Link -->
Microsoft fournit des bibliothèques et des exemples de code qui vous montrent comment gérer facilement la validation des jetons. Dans les sections suivantes, nous décrivons le processus sous-jacent. Plusieurs bibliothèques open source tierces sont également disponibles pour la validation de jetons JWT. Il existe au moins une bibliothèque pour la plupart des plateformes et langues.

### <a name="validate-the-signature"></a>valider la signature
Un jeton JWT contient trois segments séparés par le caractère `.` . Le premier segment est appelé *l’en-tête*, le deuxième le *corps*, et le troisième la *signature*. Le segment de signature peut être utilisé pour valider l’authenticité du jeton d’ID afin qu’il soit approuvé par votre application.

Les jetons d’ID sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. L’en-tête du jeton d’ID contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton. Par exemple : 

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

La revendication `alg` indique l’algorithme utilisé pour signer le jeton. La revendication `kid` indique la clé publique utilisée pour signer le jeton.

À tout moment, le point de terminaison v2.0 peut signer un jeton d’ID à l’aide d’un ensemble spécifique de paires de clés publique-privée. Étant donné que le point de terminaison v2.0 alterne régulièrement le jeu de clés possible, votre application doit être écrite de manière à gérer automatiquement ces changements de clés. Pour vérifier les mises à jour apportées aux clés publiques utilisées par le point de terminaison v2.0, spécifiez une fréquence raisonnable : toutes les 24 heures.

Pour acquérir les données de la clé de signature nécessaires pour valider la signature, utilisez le document de métadonnées OpenID Connect à l’emplacement suivant :

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Testez l’URL dans un navigateur.
>
>

Ce document de métadonnées est un objet JSON qui contient diverses informations utiles, comme l’emplacement des différents points de terminaison nécessaires pour effectuer l’authentification OpenID Connect.  Le document comprend également un *jwks_uri*, qui indique l’emplacement du jeu des clés publiques utilisées pour signer les jetons. Le document JSON situé dans le jwks_uri contient toutes les informations de clés publiques qui sont actuellement utilisées. Votre application peut utiliser la revendication `kid` de l’en-tête du jeton JWT pour sélectionner la clé publique utilisée dans ce document pour signer un jeton. Elle procède ensuite à la validation des signatures à l’aide de la clé publique correcte et de l’algorithme indiqué.

La procédure de validation des signatures n’est pas indiquée dans ce document. De nombreuses bibliothèques open source sont disponibles pour vous aider à ce sujet.

### <a name="validate-the-claims"></a>Valider les revendications
Quand votre application reçoit un jeton d’ID lors de la connexion de l’utilisateur, elle doit également procéder à quelques vérifications sur les revendications du jeton d’ID. Ces vérifications portent notamment sur les revendications suivantes :

* Revendication **audience** : il s’agit de vérifier que le jeton d’ID était bien destiné à votre application.
* Revendications **pas avant** et **heure d’expiration** : il s’agit de vérifier que le jeton d’ID n’est pas arrivé à expiration.
* Revendication **émetteur** : il s’agit de vérifier que le jeton a été émis vers votre application par le point de terminaison v2.0.
* **valeur à usage unique** : il s’agit d’atténuer les attaques par relecture de jetons.

Pour obtenir la liste complète des revendications que votre application doit valider, reportez-vous à la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Les valeurs attendues pour ces revendications sont détaillées dans la section [Jetons d’ID](# ID tokens).

## <a name="token-lifetimes"></a>Durées de vie des jetons
Nous indiquons les durées de vie des jetons ci-après uniquement à des fins d’information. Ces informations peuvent vous aider à développer et déboguer des applications. Quand vous écrivez vos applications, faites en sorte qu’elles n’attendent pas des durées de vie constantes, car les durées de vie des jetons peuvent changer à tout moment.

| par jeton | Durée de vie | Description |
| --- | --- | --- |
| Jetons d’ID (comptes professionnels ou scolaires) |1 heure |Les jetons d’ID sont généralement valides pendant 1 heure. Votre application web peut utiliser cette même durée de vie pour conserver sa propre session avec l’utilisateur (recommandé). Vous pouvez également choisir une durée de vie de session différente. Si votre application doit obtenir un nouveau jeton d’ID, elle doit procéder à une nouvelle demande de connexion auprès du point de terminaison d’autorisation v2.0. Si l’utilisateur dispose d’une session de navigateur valide avec le point de terminaison v2.0, il se peut qu’il ne soit pas tenu d’entrer de nouveau ses informations d’identification. |
| Jetons d’ID (comptes personnels) |24 heures |Les jetons d’ID pour les comptes personnels sont généralement valides 24 heures. Votre application web peut utiliser cette même durée de vie pour conserver sa propre session avec l’utilisateur (recommandé). Vous pouvez également choisir une durée de vie de session différente. Si votre application doit obtenir un nouveau jeton d’ID, elle doit procéder à une nouvelle demande de connexion auprès du point de terminaison d’autorisation v2.0. Si l’utilisateur dispose d’une session de navigateur valide avec le point de terminaison v2.0, il se peut qu’il ne soit pas tenu d’entrer de nouveau ses informations d’identification. |
| Jetons d’accès (comptes professionnels ou scolaires) |1 heure |Indiqués dans les réponses de jeton dans le cadre des métadonnées du jeton. |
| Jetons d’accès (comptes personnels) |1 heure |Indiqués dans les réponses de jeton dans le cadre des métadonnées du jeton. Les jetons d’accès émis au nom de comptes personnels peuvent être configurés pour une durée de vie différente, mais ils sont généralement valides pendant une heure. |
| Jetons d’actualisation (comptes professionnels ou scolaires) |Jusqu’à 14 jours |Un jeton d’actualisation est valide pendant 14 jours au maximum. Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit donc continuer d’utiliser un jeton d’actualisation jusqu’à ce qu’il échoue ou jusqu’à ce que votre application le remplace par un nouveau. Un jeton d’actualisation devient également non valide si l’utilisateur a entré ses informations d’identification il y a plus de 90 jours. |
| Jetons d’actualisation (comptes personnels) |Jusqu’à 1 an |Un jeton d’actualisation est valide pendant 1 an au maximum. Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit donc continuer d’utiliser un jeton d’actualisation jusqu’à ce qu’il échoue. |
| Codes d’autorisation (comptes professionnels ou scolaires) |10 minutes |Les codes d’autorisation, qui sont volontairement de courte durée, doivent être échangés immédiatement contre des jetons d’accès et des jetons d’actualisation quand les jetons sont reçus. |
| Codes d’autorisation (comptes personnels) |5 minutes |Les codes d’autorisation, qui sont volontairement de courte durée, doivent être échangés immédiatement contre des jetons d’accès et des jetons d’actualisation quand les jetons sont reçus. Les codes d’autorisation émis au nom de comptes personnels sont à usage unique. |
