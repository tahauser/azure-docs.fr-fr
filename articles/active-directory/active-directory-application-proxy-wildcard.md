---
title: Applications génériques dans le proxy d’application Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser des applications génériques dans le proxy d’application Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: f97b2541bb755a9b7ab8e3602dfad90f50ada740
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Applications génériques dans le proxy d’application Azure Active Directory 

Dans Azure Active Directory (Azure AD), la configuration d’un grand nombre d’applications locales peut rapidement devenir ingérable et introduire des risques inutiles d’erreurs de configuration si la plupart d'entre elles utilisent les mêmes paramètres. Avec le [proxy d’application Azure AD](active-directory-application-proxy-get-started.md), vous pouvez résoudre ce problème à l’aide de la publication d’applications génériques qui permet de publier et de gérer plusieurs applications à la fois. Cette solution vous permet de :

-   Simplifier votre surcharge administrative
-   Réduire le nombre d’erreurs de configuration potentielles
-   Autoriser vos utilisateurs à accéder à davantage de ressources en toute sécurité

Cet article vous fournit les informations nécessaires pour configurer la publication d’applications génériques dans votre environnement.


## <a name="create-a-wildcard-application"></a>Créer une application générique 

Vous pouvez créer une application générique (*) si vous avez un groupe d’applications avec la même configuration. Les candidats potentiels à une application générique sont les applications qui partagent les paramètres suivants :

- Le groupe d’utilisateurs ayant accès à ces applications
- La méthode d’authentification unique
- Le protocole d’accès (http, https)

Vous pouvez publier des applications avec des caractères génériques si les URL internes et externes ont le format suivant :

> http(s)://*.\<domaine\>

Par exemple : `http(s)://*.adventure-works.com`. Alors que les URL internes et externes peuvent utiliser des domaines différents, une bonne pratique consiste à utiliser des domaines identiques. Quand vous publiez l’application, vous voyez une erreur si une des URL n’a pas de caractère générique.

Si vous avez d’autres applications avec différents paramètres de configuration, vous devez publier ces exceptions en tant qu’applications distinctes pour remplacer les valeurs par défaut définies pour le caractère générique. Les applications sans caractère générique ont toujours priorité sur les applications génériques. Du point de vue de la configuration, ce sont « juste » des applications normales.

La création d’une application générique repose sur le même [flux de publication d’application](application-proxy-publish-azure-portal.md) que celui de toutes les autres applications. La seule différence est que vous ajoutez un caractère générique dans les URL et éventuellement la configuration de l’authentification unique.


## <a name="prerequisites"></a>Prérequis


### <a name="custom-domains"></a>Domaines personnalisés

Bien que les [domaines personnalisés](active-directory-application-proxy-custom-domains.md) soient facultatifs pour toutes les autres applications, ils sont obligatoires pour les applications génériques. Pour créer des domaines personnalisés, vous devez :

1. Créer un domaine vérifié dans Azure 
2. Charger un certificat SSL au format PFX dans votre proxy d’application.

Vous devez utiliser un certificat générique correspondant à l’application que vous voulez créer. Sinon, vous pouvez utiliser un certificat qui liste uniquement des applications spécifiques. Dans ce cas, seules les applications listées dans le certificat sont accessibles via cette application générique.

Pour des raisons de sécurité, il s’agit d’une règle stricte. Nous ne prenons pas en charge les caractères génériques pour les applications qui ne peuvent pas utiliser de domaine personnalisé pour l’URL externe.

### <a name="dns-updates"></a>Mises à jour de DNS

Quand vous utilisez des domaines personnalisés, vous devez créer une entrée DNS avec un enregistrement CNAME pour l’URL externe (par exemple, `*.adventure-works.com`) pointant vers l’URL externe du point de terminaison du proxy d’application. Pour les applications génériques, l’enregistrement CNAME doit pointer vers les URL externes appropriées :

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Pour vérifier que vous avez correctement configuré votre enregistrement CNAME, vous pouvez utiliser [nslookup](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup) sur l’un des points de terminaison cibles, par exemple, `expenses.adventure-works.com`.  Votre réponse doit inclure l’alias déjà mentionné (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Considérations


### <a name="accepted-formats"></a>Formats acceptés

Pour les applications génériques, **l’URL interne** doit être au format `http(s)://*.<domain>`. 

![AppId](./media/active-directory-application-proxy-wildcard\22.png)


Quand vous configurez une **URL externe**, vous devez utiliser le format suivant : `https://*.<custom domain>` 

![AppId](./media/active-directory-application-proxy-wildcard\21.png)

Les autres positions du caractère générique, les caractères génériques multiples ou les autres chaînes d’expression régulière ne sont pas pris en charge et entraînent des erreurs.


### <a name="excluding-applications-from-the-wildcard"></a>Exclure des applications du caractère générique

Vous pouvez exclure une application de l’application générique de la façon suivante :

- Publier l’application d’exception en tant qu’application normale 
- Activer le caractère générique uniquement pour des applications spécifiques par le biais de vos paramètres DNS  


La publication d’une application en tant qu’application normale est la méthode recommandée pour exclure une application d’un caractère générique. Vous devez publier les applications exclues avant les applications génériques pour vérifier que vos exceptions sont appliquées depuis le début. L’application la plus spécifique est toujours prioritaire : une application publiée sous `budgets.finance.adventure-works.com` est prioritaire sur l’application `*.finance.adventure-works.com`, qui à son tour est prioritaire sur l’application `*.adventure-works.com`. 

Vous pouvez également limiter le caractère générique pour qu’il fonctionne uniquement pour des applications spécifiques dans votre gestion DNS. Une bonne pratique consiste à créer une entrée CNAME avec un caractère générique et correspondant au format de l’URL externe que vous avez configurée. Toutefois, vous pouvez faire pointer à la place les URL des applications spécifiques vers les caractères génériques. Par exemple, au lieu de `*.adventure-works.com`, faites pointer `hr.adventure-works.com`, `expenses.adventure-works.com` et `travel.adventure-works.com individually` vers `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Si vous utilisez cette option, vous avez aussi besoin d’une autre entrée CNAME pour la valeur `AppId.domain`, par exemple, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, pointant également vers le même emplacement. **L’AppId** se trouve dans la page de propriétés de l’application générique :

![AppId](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Définition de l’URL de la page d’accueil pour le panneau MyApps

L’application générique est représentée par une seule vignette dans le [panneau MyApps](https://myapps.microsoft.com). Par défaut, cette vignette est masquée. Pour afficher la vignette et diriger les utilisateurs vers une page d’accueil spécifique :

1. Suivez les instructions permettant de [définir une URL de page d’accueil](application-proxy-office365-app-launcher.md).
2. Définissez **Afficher l’application** avec la valeur **true** dans la page de propriétés d’application.

### <a name="kerberos-constrained-delegation"></a>Délégation Kerberos contrainte

Pour les applications qui utilisent [la délégation Kerberos contrainte (KCD) comme méthode d’authentification unique](active-directory-application-proxy-sso-using-kcd.md), le SPN répertorié pour la méthode d’authentification unique peut aussi nécessiter un caractère générique. Par exemple, le SPN peut être : `HTTP/*.adventure-works.com`. Vous devez toujours configurer les SPN individuels sur vos serveurs backend (par exemple, `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scénario 1 : Application générique générale

Dans ce scénario, vous avez trois applications différentes à publier :

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Les trois applications :

- Sont utilisées par tous les utilisateurs
- Utilisent *l’authentification Windows intégrée* 
- Ont les mêmes propriétés


Vous pouvez publier l’application générique en suivant les étapes décrites dans [Publier des applications à l’aide du proxy d’application Azure AD](application-proxy-publish-azure-portal.md). Ce scénario part du principe que :

- Un locataire a l’ID suivant : `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Un domaine vérifié appelé `adventure-works.com` a été configuré.

- Une entrée **CNAME** qui fait pointer `*.adventure-works.com` vers `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` a été créée.

En suivant les [étapes documentées](application-proxy-publish-azure-portal.md), vous créez une application de proxy d’application dans votre locataire. Dans cet exemple, le caractère générique est dans les champs suivants :

- URL interne :

    ![URL interne](./media/active-directory-application-proxy-wildcard\42.png)


- URL externe :

    ![URL externe](./media/active-directory-application-proxy-wildcard\43.png)

 
- SPN d’application interne : 

    ![Configuration SPN](./media/active-directory-application-proxy-wildcard\44.png)


La publication de l’application générique vous permet désormais d’accéder à vos trois applications via les URL auxquelles vous êtes habitué (par exemple, `travel.adventure-works.com`).

La configuration implémente la structure suivante :

![AppId](./media/active-directory-application-proxy-wildcard\05.png)

| Couleur | Description |
| ---   | ---         |
| Bleu  | Applications explicitement publiées et visibles dans le portail Azure. |
| Gris  | Applications accessibles via l’application parente. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scénario 2 : Application générique générale avec exception

Dans ce scénario, en plus des trois applications générales, vous avez une autre application, `finance.adventure-works.com`, qui doit être accessible uniquement par le service Finance. Avec la structure d’application actuelle, votre application Finance est accessible via l’application générique par tous les employés. Pour changer ce paramètre, vous excluez votre application de l’application générique en configurant Finance en tant qu’application distincte avec des autorisations plus restrictives.



Vous devez vérifier qu’un enregistrement CNAME fait pointer `finance.adventure-works.com` vers le point de terminaison propre à l’application, spécifié dans la page Proxy d’application de l’application. Pour ce scénario, `finance.adventure-works.com` pointe vers `https://finance-awcycles.msappproxy.net/`. 

D’après les [étapes documentées](application-proxy-publish-azure-portal.md), ce scénario nécessite les paramètres suivants :


- Dans **l’URL interne**, vous définissez **finance** au lieu d’un caractère générique. 

    ![URL interne](./media/active-directory-application-proxy-wildcard\52.png)

- Dans **l’URL externe**, vous définissez **finance** au lieu d’un caractère générique. 

    ![URL externe](./media/active-directory-application-proxy-wildcard\53.png)

- Pour le SPN d’application interne, vous définissez **finance** au lieu d’un caractère générique.

    ![Configuration SPN](./media/active-directory-application-proxy-wildcard\54.png)


Cette configuration implémente le scénario suivant :

![Scénario](./media/active-directory-application-proxy-wildcard\09.png)

Comme `finance.adventure-works.com` est une URL plus spécifique que `*.adventure-works.com`, elle est prioritaire. Les utilisateurs qui accèdent à `finance.adventure-works.com` reçoivent l’expérience spécifiée dans l’application Ressources financières. Dans ce cas, seuls les employés du service Finance peuvent accéder à `finance.adventure-works.com`.

Si vous avez plusieurs applications publiées pour la finance et que `finance.adventure-works.com` est un domaine vérifié, vous pouvez publier une autre application générique `*.finance.adventure-works.com`. Comme ce domaine est plus spécifique que le domaine `*.adventure-works.com` générique, il est prioritaire si un utilisateur accède à une application dans le domaine finance.


## <a name="next-steps"></a>Étapes suivantes

Le cas échéant, consultez les références suivantes :

- **Domaines personnalisés**, consultez [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](active-directory-application-proxy-custom-domains.md).

- **Publication d’applications**, consultez [Publication d’applications à l’aide du proxy d’application Azure AD](application-proxy-publish-azure-portal.md)


