---
title: "Durées de vie des jetons configurables dans Azure Active Directory | Microsoft Docs"
description: "Découvrez comment définir les durées de vie des jetons émis par Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: billmath
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: eaf9e7088c8c88140ea690c13ff7e0c7026b8f86
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durées de vie des jetons configurables dans Azure Active Directory (version préliminaire publique)
Vous pouvez spécifier la durée de vie d’un jeton émis par Azure Active Directory (Azure AD). Vous pouvez définir les durées de vie des jetons pour toutes les applications de votre organisation, pour une application mutualisée (plusieurs organisations) ou pour un principal de service spécifique de votre organisation.

> [!NOTE]
> Pour le moment, cette fonctionnalité est disponible dans la version préliminaire publique. Soyez prêt à rétablir ou à supprimer les modifications. La fonctionnalité est disponible dans n’importe quel abonnement Azure Active Directory tant que la version préliminaire publique est utilisée. Toutefois, lorsque la fonctionnalité est mise à la disposition générale, certains aspects de la fonctionnalité peuvent nécessiter un abonnement [Azure Active Directory Premium](active-directory-get-started-premium.md).
>
>

Dans Azure AD, un objet de stratégie représente un ensemble de règles appliquées sur des applications individuelles ou sur toutes les applications d’une organisation. Chaque type de stratégie comporte une structure unique avec un ensemble de propriétés qui sont ensuite appliquées aux objets auxquels elles sont affectées.

Vous pouvez désigner une stratégie comme stratégie par défaut pour votre organisation. La stratégie est appliquée à toutes les applications de l’organisation tant qu’elle n’est pas remplacée par une stratégie pourvue d’une priorité plus élevée. Vous pouvez également affecter une stratégie à des applications spécifiques. L’ordre de priorité varie par type de stratégie.


## <a name="token-types"></a>Types de jetons

Vous pouvez définir les stratégies de durée de vie des jetons pour les jetons d’actualisation, les jetons d’accès, les jetons de session et les jetons d’ID.

### <a name="access-tokens"></a>Jetons d’accès
Les clients utilisent des jetons d’accès pour accéder à une ressource protégée. Un jeton d’accès peut uniquement être utilisé pour une combinaison spécifique d’utilisateur, de client et de ressource. Les jetons d’accès ne peuvent pas être révoqués et sont valides jusqu’à leur expiration. Un acteur malveillant qui a obtenu un jeton d’accès peut l’utiliser pour prolonger sa durée de vie. L’ajustement de la durée de vie des jetons d’accès représente un compromis entre l’amélioration des performances du système et l’augmentation de la durée pendant laquelle le client conserve un accès une fois son compte désactivé. Les performances du système sont améliorées en réduisant le nombre de fois où un client doit acquérir un nouveau jeton d’accès.

### <a name="refresh-tokens"></a>Jetons d’actualisation
Lorsqu’un client acquiert un jeton d’accès pour accéder à une ressource protégée, il reçoit un jeton d’actualisation et un jeton d’accès. Le jeton d’actualisation permet d’obtenir de nouvelles paires de jetons d’accès/actualisation à l’expiration du jeton d’accès actuel. Un jeton d’actualisation est lié à une combinaison d’utilisateur et de client. Un jeton d’actualisation peut être révoqué, et la validité du jeton est vérifié à chaque fois qu’il est utilisé.

Il est important de distinguer les clients confidentiels des clients publics. Pour plus d’informations sur les différents types de client, consultez [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durées de vie des jetons avec des jetons d’actualisation de client confidentiel
Les clients confidentiels sont des applications qui peuvent stocker un mot de passe client (secret). Ils peuvent prouver que les demandes proviennent de l’application cliente et non d’un acteur malveillant. Par exemple, une application web est un client confidentiel, car elle peut stocker un secret de client sur le serveur web. Elle n’est pas exposée. Comme ces flux sont plus sécurisés, les durées de vie par défaut des jetons d’actualisation émis en direction de ces flux sont de `until-revoked`, ne peuvent pas être modifiées à l’aide de la stratégie et ne peuvent pas être révoquées sur les réinitialisations de mot de passe volontaires.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durées de vie des jetons avec des jetons d’actualisation de client public

Les clients publics ne peuvent pas stocker en toute sécurité un mot de passe client (secret). Par exemple, une application iOS/Android ne peut pas masquer un secret au propriétaire de la ressource et est donc considérée comme un client public. Vous pouvez définir des stratégies sur des ressources pour empêcher les jetons d’actualisation des clients publics antérieurs à une période spécifiée d’obtenir une nouvelle paire de jetons d’accès/actualisation. (Pour ce faire, utilisez la propriété Délai d’inactivité maximale de jeton d’actualisation). Vous pouvez également utiliser des stratégies pour définir un délai au-delà duquel les jetons d’actualisation ne sont plus acceptés. (Pour ce faire, utilisez la propriété Âge maximal de jeton d’actualisation). Vous pouvez ajuster la durée de vie des jetons d’actualisation pour contrôler le moment et la fréquence auxquels l’utilisateur doit entrer de nouveau les informations d’identification au lieu d’être authentifié de nouveau en mode silencieux lorsqu’il utilise une application cliente publique.

### <a name="id-tokens"></a>Jetons d’ID
Les jetons d’ID sont transmis aux sites web et clients natifs. Les jetons d’ID contiennent des informations de profil sur un utilisateur. Un jeton d’ID est lié à une combinaison spécifique d’utilisateur et de client. Les jetons d’ID sont considérés comme valides jusqu’à leur expiration. En règle générale, une application web fait correspondre la durée de vie de session d’un utilisateur de l’application à la durée de vie du jeton d’ID émis pour l’utilisateur. Vous pouvez ajuster la durée de vie des jetons d’ID pour contrôler la fréquence à laquelle l’application web arrête la session de l’application et demande à l’utilisateur de s’authentifier à nouveau auprès d’Azure AD (en mode silencieux ou interactif).

### <a name="single-sign-on-session-tokens"></a>Jetons de session d’authentification unique
Lorsqu’un utilisateur s’authentifie auprès d’Azure AD, une session d’authentification unique (SSO) est établie avec le navigateur de l’utilisateur et Azure AD. Le jeton SSO représente cette session sous la forme d’un cookie. Notez que le jeton de session SSO n’est pas lié à une application cliente/ressource spécifique. Les jetons de session SSO peuvent être révoqués, et leur validité est vérifiée à chaque fois qu’ils sont utilisés.

Azure AD utilise deux types de jetons de session SSO : persistant et non persistant. Les jetons de session persistants sont stockés en tant que cookies persistants par le navigateur. Les jetons de session non persistants sont stockés en tant que cookies de session. (Les cookies de session sont détruits lors de la fermeture du navigateur.) En règle générale, un jeton de session non persistant est stocké. Cependant, quand l’utilisateur sélectionne la case à cocher **Maintenir la connexion** lors de l’authentification, un jeton de session persistant est stocké.

Les jetons de session non persistants ont une durée de vie de 24 heures. Les jetons persistants ont une durée de vie de 180 jours. À chaque fois qu’un jeton de session SSO est utilisé au cours de sa période de validité, celle-ci est prolongée à nouveau de 24 heures ou de 180 jours, en fonction du type de jeton. Si un jeton de session SSO n’est pas utilisé au cours de sa période de validité, il est considéré comme arrivé à expiration et n’est plus accepté.

Vous pouvez utiliser une stratégie pour définir la durée après laquelle le premier jeton de session a été émis au-delà de celle pour laquelle le jeton de session n’est plus accepté. (Pour ce faire, utilisez la propriété Âge maximal de jeton de session). Vous pouvez ajuster la durée de vie des jetons de session pour contrôler le moment et la fréquence auxquels l’utilisateur doit entrer de nouveau les informations d’identification au lieu d’être authentifié de nouveau en mode silencieux lorsqu’il utilise une application web.

### <a name="token-lifetime-policy-properties"></a>Propriétés des stratégies de durée de vie des jetons
Une stratégie de durée de vie des jetons est un type d’objet de stratégie qui contient des règles de durée de vie des jetons. Utilisez les propriétés de la stratégie pour contrôler les durées de vie des jetons spécifiés. Si aucune stratégie n’est définie, le système applique la valeur de durée de vie par défaut.

### <a name="configurable-token-lifetime-properties"></a>Propriétés des durées de vie des jetons configurables
| Propriété | Chaîne de propriété de stratégie | Éléments affectés | Default | Minimale | Maximale |
| --- | --- | --- | --- | --- | --- |
| Durée de vie de jeton d’accès |AccessTokenLifetime |Jetons d’accès, jetons d’ID, jetons SAML2 |1 heure |10 minutes |1 jour |
| Délai d’inactivité maximale de jeton d’actualisation |MaxInactiveTime |Jetons d’actualisation |90 jours |10 minutes |90 jours |
| Âge maximal de jeton d’actualisation à facteur unique |MaxAgeSingleFactor |Jetons d’actualisation (pour tous les utilisateurs) |Jusqu’à révocation |10 minutes |Jusqu’à révocation<sup>1</sup> |
| Âge maximal de jeton d’actualisation multifacteur |MaxAgeMultiFactor |Jetons d’actualisation (pour tous les utilisateurs) |Jusqu’à révocation |10 minutes |Jusqu’à révocation<sup>1</sup> |
| Âge maximal de jeton de session à facteur unique |MaxAgeSessionSingleFactor<sup>2</sup> |Jetons de session (persistants et non persistants) |Jusqu’à révocation |10 minutes |Jusqu’à révocation<sup>1</sup> |
| Âge maximal de jeton de session multifacteur |MaxAgeSessionMultiFactor<sup>3</sup> |Jetons de session (persistants et non persistants) |Jusqu’à révocation |10 minutes |Jusqu’à révocation<sup>1</sup> |

* <sup>1</sup>Une durée explicite maximale de 365 jours peut être définie pour ces attributs.
* <sup>2</sup>Si **MaxAgeSessionSingleFactor** n’est pas défini, cette valeur prend la valeur **MaxAgeSingleFactor**. Si aucun de ces paramètres n’est défini, la propriété prend la valeur par défaut (Jusqu’à révocation).
* <sup>3</sup>Si **MaxAgeSessionMultiFactor** n’est pas défini, cette valeur prend la valeur **MaxAgeMultiFactor**. Si aucun de ces paramètres n’est défini, la propriété prend la valeur par défaut (Jusqu’à révocation).

### <a name="exceptions"></a>Exceptions
| Propriété | Éléments affectés | Default |
| --- | --- | --- |
| Âge maximal de jeton d’actualisation (émis pour les utilisateurs fédérés disposant d’informations de révocation insuffisantes<sup>1</sup>) |Jetons d’actualisation (émis pour les utilisateurs fédérés disposant d’informations de révocation insuffisantes<sup>1</sup>) |12 heures |
| Délai d’inactivité maximale de jeton d’actualisation (émis pour les clients confidentiels) |Jetons d’actualisation (émis pour les clients confidentiels) |90 jours |
| Âge maximal de jeton d’actualisation (émis pour les clients confidentiels) |Jetons d’actualisation (émis pour les clients confidentiels) |Jusqu’à révocation |

* <sup>1</sup>Les utilisateurs fédérés qui disposent d’informations de révocation insuffisantes incluent tous les utilisateurs qui n’ont pas l’attribut « LastPasswordChangeTimestamp » synchronisé. Cette valeur Âge maximal courte est affectée à ces utilisateurs car AAD est incapable de vérifier quand révoquer les jetons qui sont liés à d’anciennes informations d’identification (par exemple un mot de passe qui a été changé) et doit vérifier plus fréquemment pour s’assurer que les jetons associés sont toujours conformes. Pour améliorer cette expérience, les administrateurs de locataires doivent s’assurer qu’ils synchronisent l’attribut « LastPasswordChangeTimestamp » (cela peut être défini sur l’objet utilisateur à l’aide de Powershell ou d’AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Définition des priorités et évaluation de la stratégie
Vous pouvez créer, puis affecter une stratégie de durée de vie à une application spécifique, à votre organisation et à vos principaux de service. Plusieurs stratégies peuvent s’appliquer à une application spécifique. La stratégie de durée de vie du jeton appliquée suit les règles ci-dessous :

* Si une stratégie est explicitement affectée au principal de service, elle est appliquée.
* Si aucune stratégie n’est explicitement affectée au principal de service, une stratégie explicitement affectée à l’organisation parente du principal de service est appliquée.
* Si aucune stratégie n’est explicitement affectée au principal de service ou à l’organisation, la stratégie affectée à l’application est appliquée.
* Si aucune stratégie n’a été affectée au principal de service, à l’organisation ou à l’objet d’application, les valeurs par défaut sont appliquées. (Consultez le tableau dans la section [Propriétés des durées de vie des jetons configurables](#configurable-token-lifetime-properties).)

Pour plus d’informations sur la relation existant entre les objets de principal de service et d’application, consultez [Objets application et principal du service dans Azure Active Directory](active-directory-application-objects.md).

La validité d’un jeton est évaluée lors de son utilisation. C’est la stratégie pourvue de la priorité la plus élevée sur l’application ouverte qui est appliquée.

> [!NOTE]
> Voici un scénario d’exemple.
>
> Un utilisateur souhaite accéder à deux applications web : l’application web A et l’application web B.
> 
> Facteurs :
> * Les deux applications web résident dans la même organisation parente.
> * La stratégie 1 de durée de vie des jetons pourvue d’une propriété Âge maximal de jeton de session de 8 heures est définie comme valeur par défaut de l’organisation parente.
> * L’application web A est une application web d’utilisation régulière, qui n’est liée à aucune stratégie.
> * L’application web B est utilisée pour les processus très sensibles. Son principal de service est lié à la stratégie 2 de durée de vie des jetons pourvue d’une propriété Âge maximal de jeton de session de 30 minutes.
>
> À 12 h 00, l’utilisateur démarre une nouvelle session de navigateur et tente d’accéder à l’application web A. Il est redirigé vers Azure AD et doit se connecter. Cette action crée un cookie avec un jeton de session dans le navigateur. L’utilisateur est redirigé vers l’application web A avec un jeton d’ID qui lui permet d’accéder à l’application.
>
> À 12 h 15, l’utilisateur essaie alors d’accéder à l’application web B. Le navigateur le redirige vers Azure AD qui détecte le cookie de session. Le principal de service de l’application web B est lié à la stratégie 2 de durée de vie des jetons, mais fait également partie de l’organisation parente avec la stratégie 1 de durée de vie des jetons par défaut. La stratégie 2 de durée de vie des jetons est appliquée puisque les stratégies liées aux principaux de service ont une priorité supérieure à celle des stratégies par défaut de l’organisation. Comme le jeton de session a été initialement émis au cours des 30 dernières minutes, il est considéré comme valide. L’utilisateur est redirigé vers l’application web B avec un jeton d’ID qui lui octroie un accès.
>
> À 13 h 00, l’utilisateur essaie d’accéder à l’application web A. Il est redirigé vers Azure AD. L’application web A n’est liée à aucune stratégie, mais comme elle réside dans une organisation avec la stratégie 1 de durée de vie des jetons par défaut, cette stratégie est appliquée. Le cookie de session qui a initialement été émis au cours des huit dernières heures est détecté. L’utilisateur est redirigé en mode silencieux vers l’application web A avec un nouveau jeton d’ID. Aucune authentification de l’utilisateur n’est nécessaire.
>
> L’utilisateur essaie immédiatement d’accéder à l’application web B. Il est redirigé vers Azure AD. Comme avant, la stratégie 2 de durée de vie des jetons est appliquée. Étant donné que le jeton a été émis il y a plus de 30 minutes, l’utilisateur est invité à entrer de nouveau ses informations d’identification. Un nouveau jeton de session et un jeton d’ID sont émis. L’utilisateur peut alors accéder à l’application web B.
>
>

## <a name="configurable-policy-property-details"></a>Présentation des propriétés de stratégie configurables
### <a name="access-token-lifetime"></a>Durée de vie de jeton d’accès
**Chaîne :** AccessTokenLifetime

**Éléments affectés :** jetons d’accès, jetons d’ID

**Résumé :** cette stratégie détermine la durée pendant laquelle les jetons d’accès et d’ID sont considérés comme valides. Réduire la propriété Durée de vie de jeton d’accès atténue le risque qu’un jeton d’accès ou jeton d’ID soit utilisé par un acteur malveillant pour une période prolongée. (Ces jetons ne peuvent pas être révoqués.) L’inconvénient est que les performances sont affectées, car les jetons sont remplacés plus souvent.

### <a name="refresh-token-max-inactive-time"></a>Délai d’inactivité maximale de jeton d’actualisation
**Chaîne :** MaxInactiveTime

**Éléments affectés :** jetons d’actualisation

**Résumé :** cette stratégie détermine l’âge qu’un jeton d’actualisation peut avoir avant qu’un client ne puisse plus l’utiliser pour récupérer une nouvelle paire de jetons d’actualisation/d’accès lors de la tentative d’accès à cette ressource. Comme un nouveau jeton d’actualisation est généralement renvoyé lorsqu’un jeton d’actualisation est utilisé, la stratégie bloque l’accès si le client tente d’accéder à une ressource à l’aide du jeton d’actualisation en cours pour la période spécifiée.

Cette stratégie force les utilisateurs qui n’ont pas été actifs sur leur client à s’authentifier de nouveau pour récupérer un nouveau jeton d’actualisation.

La propriété Délai d’inactivité maximale de jeton d’actualisation doit être définie sur une valeur inférieure à celles des propriétés Âge maximal de jeton d’actualisation à facteur unique et Âge maximal de jeton d’actualisation multifacteur.

### <a name="single-factor-refresh-token-max-age"></a>Âge maximal de jeton d’actualisation à facteur unique
**Chaîne :** MaxAgeSingleFactor

**Éléments affectés :** jetons d’actualisation

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut utiliser des jetons d’actualisation pour obtenir de nouvelles paires de jetons d’accès/actualisation après sa dernière authentification réussie avec un seul facteur. Une fois que l’utilisateur est authentifié et qu’il reçoit un nouveau jeton d’actualisation, il peut utiliser le flux de jeton d’actualisation pour la période spécifiée. (Cela est vrai tant que le jeton d’actualisation actuel n’a pas été révoqué et qu’il ne reste pas inutilisé plus longtemps que la durée d’inactivité.) À ce stade, l’utilisateur est contraint de s’authentifier de nouveau pour recevoir un nouveau jeton d’actualisation.

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Comme l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, nous vous recommandons de définir cette propriété sur une valeur inférieure ou égale à celle de la propriété Âge maximal de jeton d’actualisation multifacteur.

### <a name="multi-factor-refresh-token-max-age"></a>Âge maximal de jeton d’actualisation multifacteur
**Chaîne :** MaxAgeMultiFactor

**Éléments affectés :** jetons d’actualisation

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut utiliser des jetons d’actualisation pour obtenir de nouvelles paires de jetons d’accès/actualisation après sa dernière authentification réussie avec plusieurs facteurs. Une fois que l’utilisateur est authentifié et qu’il reçoit un nouveau jeton d’actualisation, il peut utiliser le flux de jeton d’actualisation pour la période spécifiée. (Cela est vrai tant que le jeton d’actualisation actuel n’a pas été révoqué et qu’il ne reste pas inutilisé plus longtemps que la durée d’inactivité.) À ce stade, les utilisateurs sont contraints de s’authentifier de nouveau pour recevoir un nouveau jeton d’actualisation.

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Comme l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, nous vous recommandons de définir cette propriété sur une valeur supérieure ou égale à celle de la propriété Âge maximal de jeton d’actualisation à facteur unique.

### <a name="single-factor-session-token-max-age"></a>Âge maximal de jeton de session à facteur unique
**Chaîne :** MaxAgeSessionSingleFactor

**Éléments affectés :** jetons de session (persistants et non persistants)

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut utiliser des jetons de session pour obtenir un nouvel ID et un nouveau jeton de session après sa dernière authentification réussie avec un seul facteur. Une fois que l’utilisateur est authentifié et qu’il reçoit un nouveau jeton de session, il peut utiliser le flux de jeton de session pour la période spécifiée. (Cela est vrai tant que le jeton de session actuel n’est pas révoqué et qu’il n’a pas expiré.) Après la période de temps spécifié, l’utilisateur est forcé à s’authentifier de nouveau pour recevoir un nouveau jeton de session.

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Comme l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, nous vous recommandons de définir cette propriété sur une valeur inférieure ou égale à celle de la propriété Âge maximal de jeton de session multifacteur.

### <a name="multi-factor-session-token-max-age"></a>Âge maximal de jeton de session multifacteur
**Chaîne :** MaxAgeSessionMultiFactor

**Éléments affectés :** jetons de session (persistants et non persistants)

**Résumé :** cette stratégie détermine la durée pendant laquelle un utilisateur peut utiliser des jetons de session pour obtenir un nouvel ID et un nouveau jeton de session après sa dernière authentification réussie avec plusieurs facteurs. Une fois que l’utilisateur est authentifié et qu’il reçoit un nouveau jeton de session, il peut utiliser le flux de jeton de session pour la période spécifiée. (Cela est vrai tant que le jeton de session actuel n’est pas révoqué et qu’il n’a pas expiré.) Après la période de temps spécifié, l’utilisateur est forcé à s’authentifier de nouveau pour recevoir un nouveau jeton de session.

En réduisant l’âge maximal, vous obligez les utilisateurs à s’authentifier plus souvent. Comme l’authentification à facteur unique est considérée comme moins sécurisée qu’une authentification multifacteur, nous vous recommandons de définir cette propriété sur une valeur supérieure ou égale à celle de la propriété Âge maximal de jeton de session à facteur unique.

## <a name="example-token-lifetime-policies"></a>Exemples de stratégie de durée de vie des jetons
De nombreux scénarios sont possibles dans Azure AD lorsque vous créez et gérez les durées de vie des jetons pour les applications, les principaux du service et votre organisation globale. Dans cette section, nous allons vous guider dans quelques scénarios courants de stratégie qui peuvent vous aider à imposer de nouvelles règles pour les éléments suivants :

* Durée de vie du jeton
* Délai d’inactivité maximale des jetons
* Âge maximal des jetons

Dans les exemples, vous pouvez apprendre à :

* Gérer la stratégie par défaut d’une organisation
* Créer une stratégie de connexion web
* Créer une stratégie pour une application native qui appelle une API web
* Gérer une stratégie avancée

### <a name="prerequisites"></a>configuration requise
Dans les exemples suivants, vous allez créer, mettre à jour, lier et supprimer des stratégies pour les applications, les principaux de service et votre organisation globale. Si vous débutez avec Azure AD, nous vous recommandons de vous documenter sur [l’obtention d’un client Azure Active Directory](active-directory-howto-tenant.md) avant de continuer avec ces exemples.  

Pour commencer, suivez les étapes ci-dessous :

1. Téléchargez la dernière [version préliminaire publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Exécutez la commande `Connect` pour vous connecter à votre compte Administrateur Azure AD. Exécutez cette commande chaque fois que vous démarrez une nouvelle session.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Pour afficher toutes les stratégies qui ont été créées dans votre organisation, exécutez la commande suivante. Exécutez cette commande après la plupart des opérations dans les scénarios suivants. L’exécution de la commande vous aide également à obtenir le ** ** de vos stratégies.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemple : Gérer la stratégie par défaut d’une organisation
Dans cet exemple, vous allez créer une stratégie qui permet à vos utilisateurs de se connecter moins fréquemment dans votre organisation entière. Pour ce faire, créez une stratégie de durée de vie des jetons pour les jetons d’actualisation à facteur unique, qui est appliquée dans toute votre organisation. Cette stratégie est appliquée à toutes les applications de votre organisation et à chaque principal de service pour lequel aucune stratégie n’est déjà définie.

1. Créez une stratégie de durée de vie des jetons.

    1.  Définissez le jeton d’actualisation à facteur unique sur « Jusqu’à révocation ». Le jeton n’expire pas tant que l’accès n’est pas révoqué. Créez la définition de stratégie suivante :

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Pour créer la stratégie, exécutez la commande suivante :

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Pour afficher votre nouvelle stratégie et obtenir son **ID d’objet**, exécutez la commande ci-après :

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Mettez à jour la stratégie.

    Vous pouvez décider que la première stratégie que vous définissez dans cet exemple n’est pas aussi stricte que ce que votre service requiert. Pour définir votre jeton d’actualisation à facteur unique de façon qu’il expire dans deux jours, exécutez la commande suivante :

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemple : Créer une stratégie de connexion web

Dans cet exemple, vous créez une stratégie qui nécessite que vos utilisateurs s’authentifient plus fréquemment dans votre application web. Cette stratégie définit la durée de vie des jetons d’accès/ID et l’âge maximal d’un jeton de session multifacteur pour le principal de service de votre application web.

1. Créez une stratégie de durée de vie des jetons.

    Cette stratégie de connexion web définit la durée de vie des jetons d’accès/ID et l’âge maximal de jeton de session à facteur unique sur 2 heures.

    1.  Pour créer la stratégie, exécutez la commande suivante :

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Pour afficher votre nouvelle stratégie et obtenir son **ID d’objet**, exécutez la commande ci-après :

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Affectez la stratégie au principal de service. Vous devez également obtenir **l’ID d’objet** de votre principal de service. 

    1.  Pour afficher tous les principaux de service de votre organisation, vous pouvez interroger [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ou, dans [l’explorateur Azure AD Graph](https://graphexplorer.cloudapp.net/), connectez-vous à votre compte Azure AD.

    2.  Une fois que vous disposez de **l’ID d’objet**  de votre principal de service, exécutez la commande suivante :

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemple : Créer une stratégie pour une application native qui appelle une API web
Dans cet exemple, vous créez une stratégie qui nécessite que vos utilisateurs s’authentifient moins fréquemment. La stratégie augmente également la durée pendant laquelle un utilisateur peut être inactif avant d’avoir à s’authentifier de nouveau. La stratégie est appliquée à l’API web. Lorsque l’application native demande l’API web en tant que ressource, cette stratégie est appliquée.

1. Créez une stratégie de durée de vie des jetons.

    1.  Pour créer une stratégie stricte pour une API web, exécutez la commande suivante :

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Pour afficher votre nouvelle stratégie et obtenir son **ID d’objet**, exécutez la commande ci-après :

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Affectez la stratégie à votre API web. Vous devez également obtenir **l’ID d’objet** de votre application. La meilleure façon de trouver **l’ID d’objet** de votre application consiste à utiliser le [portail Azure](https://portal.azure.com/).

   Une fois que vous disposez de **l’ID d’objet**  de votre application, exécutez la commande suivante :

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Exemple : Gérer une stratégie avancée
Dans cet exemple, vous créez quelques stratégies, pour savoir comment fonctionne le système de priorité. Vous pouvez également apprendre à gérer plusieurs stratégies qui sont appliquées à divers objets.

1. Créez une stratégie de durée de vie des jetons.

    1.  Pour créer une stratégie par défaut d’organisation qui définit la durée de vie des jetons d’actualisation à facteur unique sur 30 jours, exécutez la commande suivante :

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Pour afficher votre nouvelle stratégie et obtenir son **ID d’objet**, exécutez la commande ci-après :

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Affectez la stratégie à un principal de service.

    À présent, vous avez une stratégie qui s’applique à toute l’organisation. Vous souhaitez peut-être conserver cette stratégie de 30 jours pour un principal de service spécifique, mais changer la stratégie par défaut d’organisation pour qu’elle soit la limite supérieure du paramètre « Jusqu’à révocation ».

    1.  Pour afficher tous les principaux de service de votre organisation, vous pouvez interroger [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ou, dans [l’explorateur Azure AD Graph](https://graphexplorer.cloudapp.net/), connectez-vous à l’aide de votre compte Azure AD.

    2.  Une fois que vous disposez de **l’ID d’objet**  de votre principal de service, exécutez la commande suivante :

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Définissez l’indicateur `IsOrganizationDefault` sur false :

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Créez une stratégie par défaut d’organisation :

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    À présent, la stratégie d’origine est liée à votre principal de service, et la nouvelle stratégie est définie comme stratégie par défaut de votre organisation. Il est important de se rappeler que les stratégies appliquées aux principaux de service ont priorité sur les stratégies par défaut d’organisation.

## <a name="cmdlet-reference"></a>Référence sur les applets de commande

### <a name="manage-policies"></a>Gérer les stratégies

Vous pouvez utiliser les applets de commande suivantes pour gérer les stratégies.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Permet de créer une stratégie.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Tableau de champs de chaîne JSON qui contient toutes les règles de la stratégie. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Chaîne du nom de la stratégie. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Si la valeur est true, elle définit la stratégie comme stratégie par défaut de l’organisation. Si la valeur est false, rien ne se produit. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Type de stratégie. Pour les durées de vie des jetons, utilisez toujours « TokenLifetimePolicy ». | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Facultatif] |Définit un autre ID pour la stratégie. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Permet d’obtenir toutes les stratégies d’Azure AD ou une stratégie spécifiée.

```PowerShell
Get-AzureADPolicy
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> [Facultatif] |**ObjectId (ID)** de la stratégie souhaitée. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Permet d’obtenir toutes les applications et tous les principaux de service liés à une stratégie.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** de la stratégie souhaitée. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Met à jour une stratégie existante.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** de la stratégie souhaitée. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Chaîne du nom de la stratégie. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Facultatif] |Tableau de champs de chaîne JSON qui contient toutes les règles de la stratégie. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Facultatif] |Si la valeur est true, elle définit la stratégie comme stratégie par défaut de l’organisation. Si la valeur est false, rien ne se produit. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Facultatif] |Type de stratégie. Pour les durées de vie des jetons, utilisez toujours « TokenLifetimePolicy ». |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Facultatif] |Définit un autre ID pour la stratégie. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Supprime la stratégie spécifiée.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** de la stratégie souhaitée. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Stratégies d’application
Vous pouvez utiliser les applets de commande suivantes pour les stratégies d’application.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Lie la stratégie spécifiée à une application.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** de l’application. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ID d’objet** de la stratégie. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Permet d’obtenir la stratégie affectée à une application.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** de l’application. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Supprime une stratégie d’une application.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** de l’application. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ID d’objet** de la stratégie. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Stratégies de principal du service
Vous pouvez utiliser les applets de commande suivantes pour les stratégies de principal de service.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Lie la stratégie spécifiée à un principal de service.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** de l’application. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ID d’objet** de la stratégie. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Permet d’obtenir une stratégie liée au principal de service spécifié.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** de l’application. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Supprime la stratégie du principal de service spécifié.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| parameters | Description | exemples |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** de l’application. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ID d’objet** de la stratégie. | `-PolicyId <ObjectId of Policy>` |
