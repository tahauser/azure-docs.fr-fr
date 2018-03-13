---
title: "Étendues, autorisations et consentement Azure Active Directory v2.0 | Microsoft Docs"
description: "Une description de l’autorisation dans le point de terminaison v2.0 Azure AD, relative notamment aux étendues, aux autorisations et aux consentements."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: b35e4a7619c23660d93d91219a92be7e93a35139
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Étendues, autorisations et consentement dans le point de terminaison Azure Active Directory v2.0
Les applications intégrées à Azure Active Directory (Azure AD) suivent un modèle d’autorisation, qui permet aux utilisateurs de contrôler le mode d’accès d’une application à leurs données. L’implémentation v2.0 de ce modèle d’autorisation a été mise à jour, et elle modifie la façon dont une application doit interagir avec Azure AD. Cet article aborde les concepts de base de ce modèle d’autorisation, notamment les étendues, les autorisations et le consentement.

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Étendues et autorisations
Azure AD implémente le protocole d’autorisation [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 est une méthode par le biais de laquelle une application tierce peut accéder aux ressources hébergées sur le web au nom d’un utilisateur. Les ressources hébergées sur le web qui s’intègrent à Azure AD présentent un identificateur de ressource, également appelé *URI d’ID d’application*. Voici, par exemple, quelques-unes des ressources hébergées sur le Web de Microsoft :

* L’API de messagerie unifiée Office 365 : `https://outlook.office.com`
* L’API Graph Azure AD : `https://graph.windows.net`
* Microsoft Graph : `https://graph.microsoft.com`

Cela s’applique également aux ressources tierces intégrées à Azure AD. Ces ressources peuvent également définir un ensemble d’autorisations à utiliser pour diviser la fonctionnalité de cette ressource en fragments plus réduits. Par exemple, [Microsoft Graph](https://graph.microsoft.io) a défini des autorisations pour effectuer les tâches suivantes, entre autres :

* Lire le calendrier d’un utilisateur
* Écrire dans le calendrier d’un utilisateur
* Envoi de messages en tant qu’utilisateur

En définissant ces types d’autorisation, la ressource dispose d’un contrôle affiné sur ses données et sur leur exposition. Une application tierce peut demander ces autorisations à un utilisateur d’application. L’utilisateur d’application doit approuver les autorisations pour que l’application puisse agir pour le compte de l’utilisateur. En fragmentant les fonctionnalités de la ressource en ensembles plus réduits d’autorisations, il est possible de créer des applications tierces pour qu’elles demandent uniquement les autorisations nécessaires à leur fonctionnement. Les utilisateurs de l’application savent avec précision la manière dont les applications utilisent leurs données, et s’inquiètent moins de leur éventuelle intention malveillante.

Dans Azure AD et OAuth, ces types d’autorisation sont appelés des *étendues*. Ils sont également parfois désignés sous le nom *d’autorisations oAuth2*. Une étendue est représentée dans Azure AD en tant que valeur de chaîne. Toujours dans l’exemple Microsoft Graph, la valeur d’étendue pour chaque autorisation est la suivante :

* Lire le calendrier d’un utilisateur en utilisant `Calendars.Read`
* Écrire dans le calendrier d’un utilisateur en utilisant `Calendars.ReadWrite`
* Envoi de messages en tant qu’utilisateur en utilisant `Mail.Send`

Une application peut demander ces autorisations en spécifiant les étendues dans les requêtes dirigées vers le point de terminaison v2.0.

## <a name="openid-connect-scopes"></a>Étendues OpenId Connect
L’implémentation v2.0 d’OpenID Connect comprend quelques étendues bien définies qui ne s’appliquent pas à une ressource particulière : `openid`, `email`, `profile` et `offline_access`.

### <a name="openid"></a>openid
Si une application exécute la connexion à l’aide [d’OpenID Connect](active-directory-v2-protocols.md), elle doit solliciter l’étendue `openid`. L’étendue `openid` s’affiche sur la page de consentement du compte professionnel, en tant qu’autorisation de connexion, et sur la page de consentement du compte Microsoft personnel en tant qu’autorisation « Afficher votre profil et se connecter aux applications et aux services à l’aide de votre compte Microsoft ». Avec cette autorisation, une application peut recevoir un identifiant utilisateur unique sous la forme de la revendication `sub`. Elle permet également à l’application d’accéder au point de terminaison des informations utilisateur. L’étendue `openid` peut être utilisée sur le point de terminaison de jeton v2.0 afin d’acquérir des jetons d’ID, qui peuvent être sollicités pour sécuriser les appels HTTP entre différents composants d’une application.

### <a name="email"></a>email
L’étendue `email` peut être utilisée avec l’étendue `openid` ainsi que d’autres. Elle permet à l’application d’accéder à l’adresse de messagerie principale de l’utilisateur sous la forme de la revendication `email`. La revendication `email` est incluse dans un jeton uniquement si une adresse de messagerie est associée au compte d’utilisateur, ce qui n’est pas toujours le cas. Si elle utilise l’étendue `email`, votre application doit être préparée à faire face à l’éventualité où la revendication `email` n’existerait pas dans le jeton.

### <a name="profile"></a>Profil
L’étendue `profile` peut être utilisée avec l’étendue `openid` ainsi que d’autres. Elle permet à l’application d’accéder à une quantité d’informations importante sur l’utilisateur, notamment le prénom de l’utilisateur, son nom de famille, son nom d’utilisateur privilégié et l’ID d’objet. Pour obtenir la liste complète des revendications de profil disponibles dans le paramètre id_token pour un utilisateur donné, consultez la page [de référence sur les jetons v2.0](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
[L’étendue `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permet à votre application d’accéder aux ressources pour le compte de l’utilisateur pendant une période prolongée. Dans la page de consentement du compte professionnel, cette étendue apparaît en tant qu’autorisation « Accéder à vos données à tout moment ». Dans la page de consentement du compte Microsoft personnel, elle apparaît en tant qu’autorisation « Accéder à vos informations à tout moment ». Lorsqu’un utilisateur approuve l’étendue `offline_access`, votre application peut recevoir les jetons d’actualisation du point de terminaison des jetons v2.0. Les jetons d’actualisation sont de longue durée. Votre application peut obtenir de nouveaux jetons d’accès lorsque les plus anciens arrivent à expiration.

Si votre application ne sollicite pas l’étendue `offline_access`, elle ne reçoit pas de jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md), vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Le jeton d’accès est valide pendant une courte durée : il arrive généralement à expiration en une heure. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant ce réacheminement, en fonction du type d’application, l’utilisateur peut devoir entrer à nouveau ses informations d’identification ou accepter une nouvelle fois les autorisations.

Pour en savoir plus sur la récupération et l’utilisation des jetons d’actualisation, consultez la page de [référence sur les protocoles v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Demande de consentement d’utilisateur individuel
Dans une demande d’autorisation [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), une application peut demander les autorisations nécessaires à l’aide du paramètre de requête `scope`. Par exemple, lorsqu’un utilisateur se connecte à une application, cette dernière envoie une requête semblable à l’exemple ci-dessous (avec des sauts de ligne ajoutés pour une meilleure lisibilité) :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Le paramètre `scope` est une liste d’étendues séparées par des espaces, demandées par l’application. Chaque étendue est indiquée par l’ajout de la valeur correspondante à l’identificateur de la ressource (URI d’ID d’application). Dans l’exemple de requête, l’application nécessite l’autorisation de lecture du calendrier de l’utilisateur et d’envoi de messages au nom de l’utilisateur.

Une fois que l’utilisateur a entré ses informations d’identification, le point de terminaison v2.0 recherche un enregistrement correspondant de *consentement d’utilisateur*. Si l’utilisateur n’a accepté aucune des autorisations sollicitées par le passé, le point de terminaison v2.0 demande à l’utilisateur d’octroyer les autorisations demandées.

![Consentement dans le compte professionnel](../../media/active-directory-v2-flows/work_account_consent.png)

Lorsque l’utilisateur approuve l’autorisation, le consentement est enregistré de manière à ce que l’utilisateur ne doive pas le fournir à nouveau lors des connexions suivantes au compte.

## <a name="requesting-consent-for-an-entire-tenant"></a>Demande de consentement d’un client entier
Souvent, lorsqu’une organisation achète une licence ou un abonnement à une application, elle souhaite la configurer entièrement pour ses employés. Dans le cadre de cette procédure, un administrateur peut autoriser l’application à agir au nom de n’importe quel employé. Si l’administrateur donne son consentement pour le client entier, les employés de l’organisation ne voient pas de page de consentement pour l’application.

Pour demander le consentement pour tous les utilisateurs d’un client, votre application peut utiliser le point de terminaison de consentement de l’administrateur.

## <a name="admin-restricted-scopes"></a>Étendues limitées aux administrateurs
Certaines autorisations à privilège élevé de l’écosystème Microsoft peuvent être définies sur *restreintes aux administrateurs*. Des exemples de ces types d’étendue incluent les autorisations suivantes :

* Lire le répertoire d’une organisation à l’aide de `Directory.Read`
* Écrire des données dans le répertoire d’une organisation à l’aide de `Directory.ReadWrite`
* Lire des groupes de sécurité dans le répertoire d’une organisation à l’aide de `Groups.Read.All`

Si un utilisateur consommateur peut accorder à une application l’accès à ce type de données, les utilisateurs d’organisation sont limités lorsqu’il s’agit d’octroyer l’accès au même jeu de données d’entreprise sensibles. Si votre application requiert l’accès à l’une de ces autorisations d’un utilisateur de l’organisation, ce dernier recevra un message erreur indiquant qu’il n’est pas autorisé à donner son consentement pour les autorisations de votre application.

Si votre application requiert l’accès aux étendues restreintes aux administrateurs pour les organisations, vous devez demander l’autorisation directement à un administrateur d’entreprise également à l’aide du point de terminaison de consentement de l’administrateur, décrit ci-dessous.

Lorsqu’un administrateur accorde ces autorisations via le point de terminaison de consentement de l’administrateur, le consentement est accordé à tous les utilisateurs dans le client.

## <a name="using-the-admin-consent-endpoint"></a>Utilisation du point de terminaison de consentement administrateur
Si vous suivez ces étapes, votre application peut rassembler les autorisations pour tous les utilisateurs dans un client, notamment les étendues restreintes aux administrateurs. Pour voir un exemple de code qui implémente les étapes, consultez [l’exemple d’étendues restreintes aux administrateurs](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application
1. Accédez à votre application dans le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou [créez une application](active-directory-v2-app-registration.md) si ce n’est déjà fait.
2. Recherchez la section **Autorisations pour Microsoft Graph**, puis ajoutez les autorisations nécessaires à votre application.
3. Veillez à **enregistrer** l’inscription de l’application.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recommandé : connecter l’utilisateur à votre application
En général, lorsque vous créez une application qui utilise le point de terminaison de consentement de l’administrateur, l’application doit disposer d’une page ou vue dans laquelle l’administrateur peut approuver ses autorisations. Cette page peut faire partie du flux d’inscription de l’application, des paramètres de l’application, ou ce peut être un flux de connexion dédié. Dans de nombreux cas, il est judicieux pour l’application d’afficher la vue de « connexion » uniquement après qu’un utilisateur se soit connecté avec un compte Microsoft professionnel ou scolaire.

Lorsque vous connectez l’utilisateur à votre application, vous pouvez identifier l’organisation à laquelle l’administrateur appartient, avant de lui demander d’approuver les autorisations nécessaires. Même si cela n’est pas strictement nécessaire, cela peut vous aider à créer une expérience plus intuitive pour les utilisateurs de l’organisation. Pour connecter l’utilisateur, suivez nos [didacticiels sur le protocole v2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations à un administrateur d’annuaire
Lorsque vous êtes prêt à demander les autorisations à l’administrateur de votre organisation, vous pouvez rediriger l’utilisateur vers le *point de terminaison de consentement de l’administrateur* v2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | Condition | Description |
| --- | --- | --- |
| locataire |Obligatoire |Le client d’annuaire auquel vous souhaitez demander l’autorisation. Peut être fourni au format GUID ou sous forme de nom convivial OU référencé de manière générique avec « common » comme indiqué dans l’exemple. |
| client_id |Obligatoire |ID d’application que le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a affecté à votre application. |
| redirect_uri |Obligatoire |URI de redirection où vous souhaitez que la réponse soit envoyée pour être gérée par votre application. Il doit correspondre exactement à l’un des URI de redirection que vous avez inscrits dans le portail d’inscription des applications. |
| state |Recommandé |Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Utilisez l’état pour encoder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou la vue sur laquelle ou laquelle il était positionné. |

À ce stade, Azure AD nécessite qu’un administrateur client se connecte pour terminer la demande. L’administrateur est invité à approuver toutes les autorisations que vous avez demandées pour votre application dans le portail d’inscription.

#### <a name="successful-response"></a>Réponse correcte
Si l’administrateur approuve les autorisations pour votre application, la réponse correcte ressemble à ce qui suit :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| --- | --- | --- |
| locataire |Client d’annuaire ayant accordé à votre application les autorisations demandées au format GUID. |
| state |Valeur incluse dans la requête qui sera également retournée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| admin_consent |Est défini sur **true**. |

#### <a name="error-response"></a>Réponse d’erreur
Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec ressemble à ce qui suit :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| --- | --- | --- |
| error |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur. |

Une fois que vous avez reçu une réponse correcte du point de terminaison de consentement de l’administrateur, votre application a acquis les autorisations qu’elle avait demandées. Vous pouvez alors demander un jeton pour la ressource souhaitée.

## <a name="using-permissions"></a>Utilisation des autorisations
Une fois que l’utilisateur accepte les autorisations pour votre application, cette dernière peut acquérir des jetons d’accès représentant l’autorisation de votre application à accéder, dans une certaine capacité, à une ressource. Un jeton d’accès peut être utilisé pour une ressource uniquement, mais l’encodage de ce jeton comporte les informations relatives à toutes les autorisations octroyées pour cette ressource à votre application. Pour acquérir un jeton d’accès, votre application peut transmettre une requête au point de terminaison de jeton v2.0 :

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Vous pouvez utiliser le jeton d’accès obtenu dans les requêtes HTTP transmises à la ressource. Il indique de façon fiable à la ressource que votre application dispose de l’autorisation appropriée pour effectuer une tâche spécifique.  

Pour en savoir plus sur le protocole OAuth 2.0 et sur le mode d’obtention des jetons d’accès, consultez la page de [référence sur les protocoles du point de terminaison v2.0](active-directory-v2-protocols.md).
