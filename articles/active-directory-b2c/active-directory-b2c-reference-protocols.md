---
title: 'Azure Active Directory B2C : Protocoles d’authentification | Microsoft Docs'
description: Création d’applications directement à l’aide des protocoles pris en charge par Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: 9159a1ff55ada7f2c3bdcc60a15eae5ddc7e08b2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C : protocoles d’authentification
Azure Active Directory B2C (Azure AD B2C) fournit l’identité en tant que service pour vos applications en prenant en charge deux protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit conforme aux normes, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. 

Les informations fournies dans ce guide sont utiles si vous écrivez votre code en envoyant et en traitant directement des requêtes HTTP, plutôt qu’en utilisant une bibliothèque open source. Nous vous recommandons de lire les informations de cette page avant d’entrer dans les détails de chaque protocole. En revanche, si vous connaissez déjà Azure AD B2C, vous pouvez accéder directement aux [guides de référence du protocole](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Concepts de base
Chaque application qui utilise Azure AD B2C doit être inscrite dans votre répertoire B2C sur le [portail Azure](https://portal.azure.com). Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

* un **ID d’application** qui identifie de manière unique votre application ;
* un **URI de redirection** ou un **identificateur de package** pouvant être utilisé pour diriger des réponses vers votre application ;
* quelques valeurs spécifiques au scénario. Pour en savoir plus, découvrez [comment inscrire votre application](active-directory-b2c-app-registration.md).

Une fois inscrite, l’application communique avec Azure Active Directory (Azure AD) en transmettant les requêtes au point de terminaison :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Dans presque tous les flux OAuth et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* Le **serveur d’autorisation** est le point de terminaison Azure AD. Il traite de manière sécurisée toutes les informations relatives à l’utilisateur, l’accès ainsi que les relations de confiance entre les parties des flux. Il est chargé de vérifier l’identité de l’utilisateur, l’octroi et la révocation de l’accès aux ressources et l’émission de jetons. Il est également connu sous le nom de fournisseur d’identité.

* Le **propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.

* Le **client OAuth** est votre application. Il est identifié par son ID d’application. Il s’agit généralement de la partie avec laquelle l’utilisateur final interagit. Elle demande également des jetons provenant du serveur d’autorisation. Le propriétaire de la ressource doit accorder au client l’autorisation d’accéder à la ressource.

* Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser le client OAuth en toute sécurité. Il utilise également les jetons d’accès du porteur pour garantir l’octroi de l’accès à une ressource.

## <a name="policies"></a>Stratégies
En fait, les stratégies Azure AD B2C constituent la fonctionnalité la plus importante du service. Azure AD B2C étend les protocoles OAuth 2.0 et OpenID Connect standard en introduisant des stratégies qui permettent à Azure AD B2C d’effectuer des opérations d’authentification et d’autorisation bien plus simples. 

Les stratégies décrivent entièrement les expériences liées à l’identité du consommateur, telles que l’inscription, la connexion et la modification de profil. Elles peuvent être définies dans une interface utilisateur d’administration et exécutées à l’aide d’un paramètre de requête spécial dans les requêtes d’authentification HTTP. 

Les stratégies ne constituant pas une fonctionnalité standard d’OAuth 2.0 ni d’OpenID Connect, vous devez prendre le temps de les comprendre. Pour plus d’informations, consultez le [guide de référence sur les stratégies Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Jetons
L’implémentation d’OAuth 2.0 et d’OpenID Connect par Azure AD B2C utilise massivement les jetons du porteur, y compris ceux représentés sous forme de jetons web JSON (JWT). Un jeton du porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ».

En ce sens, le porteur désigne toute partie qui peut présenter le jeton. Une partie doit d’abord s’authentifier auprès d’Azure AD pour recevoir un jeton du porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée.

Bien que certains jetons de sécurité intègrent des mécanismes de protection contre l’utilisation par des parties non autorisées, les jetons du porteur n’en sont pas dotés et ils doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). 

Si un jeton du porteur est transmis en dehors d’un canal sécurisé, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons du porteur pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée.

Pour connaître d’autres aspects de la sécurité des jetons du porteur, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Pour plus d’informations sur les différents types de jetons utilisés dans Azure AD B2C, consultez [la référence sur les jetons Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocoles
Quand vous êtes prêt à examiner des exemples de requêtes, commencez à lire l’un des didacticiels ci-dessous. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin d’aide pour déterminer le flux qui vous convient, consultez les [types d’applications que vous pouvez créer avec Azure AD B2C](active-directory-b2c-apps.md).

* [Génération d’une application mobile et native avec OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Génération d’applications web avec Open ID Connect](active-directory-b2c-reference-oidc.md)
* [Génération d'applications de page unique avec le flux implicite OAuth 2.0](active-directory-b2c-reference-spa.md)

