---
title: Configuration des jetons, des sessions et de l’authentification unique - Azure AD B2C | Microsoft Docs
description: Configuration du jeton, de la session et de l’authentification unique dans Azure Active Directory B2C
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
ms.openlocfilehash: 925313b6f2a00826f2ec8086457315c60f70b007
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C : configuration du jeton, de la session et de l’authentification unique

Cette fonctionnalité vous donne un contrôle précis, [par stratégie](active-directory-b2c-reference-policies.md), de ce qui suit :

1. Durées de vie des jetons de sécurité émis par Azure Active Directory (Azure AD) B2C.
2. Durées de vie des sessions d’applications web gérées par Azure AD B2C.
3. Formats des revendications importantes dans les jetons de sécurité émis par Azure AD B2C.
4. Comportement de l’authentification unique (SSO) entre plusieurs applications et stratégies dans votre client B2C.

Pour les stratégies intégrées, vous pouvez utiliser cette fonctionnalité dans votre annuaire Azure AD B2C de la façon suivante :

1. Suivez ces étapes pour [accéder au menu des fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le Portail Azure.
2. Cliquez sur **Stratégies d’inscription ou de connexion**. *Remarque : vous pouvez utiliser cette fonctionnalité sur tous types de stratégies, pas seulement les **Stratégies d’inscription ou de connexion***.
3. Ouvrez une stratégie en cliquant dessus. Par exemple, cliquez sur **B2C_1_SiUpIn**.
4. Cliquez sur **Modifier** en haut du menu.
5. Cliquez sur **Configuration du jeton, de la session et de l’authentification unique**.
6. Apportez les modifications voulues. Apprenez-en plus sur les propriétés disponibles dans les sections suivantes.
7. Cliquez sur **OK**.
8. Cliquez sur **Enregistrer** en haut du menu.

## <a name="token-lifetimes-configuration"></a>Configuration de la durée de vie des jetons

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md) pour activer l’accès sécurisé aux ressources protégées. Pour implémenter cette prise en charge, Azure AD B2C émet divers [jetons de sécurité](active-directory-b2c-reference-tokens.md). Ce sont les propriétés que vous pouvez utiliser pour gérer les durées de vie des jetons de sécurité émis par Azure AD B2C :

* **Durées de vie du jeton d’accès et d’ID (minutes)** : la durée de vie du jeton du porteur OAuth 2.0 utilisé pour accéder à une ressource protégée.
  * Par défaut : 60 minutes.
  * Valeur minimale (inclusive) : 5 minutes.
  * Valeur maximale (inclusive) : 1 440 minutes.
* **Durée de vie du jeton d’actualisation (jours)** : la durée maximale avant laquelle un jeton d’actualisation peut être utilisé pour acquérir un nouveau jeton d’accès ou d’ID (et éventuellement, un nouveau jeton d’actualisation, si l’étendue `offline_access` a été accordée à votre application).
  * Par défaut : 14 jours.
  * Valeur minimale (inclusive) : 1 jour.
  * Valeur maximale (inclusive) : 90 jours.
* **Durée de vie fenêtre glissante du jeton d’actualisation (jours)**: une fois cette période écoulée, l’utilisateur est obligé de s’authentifier de nouveau, quelle que soit la période de validité de dernier jeton d’actualisation obtenu par l’application. Cette valeur ne peut être fournie que si le commutateur est défini sur **Délimité**. Elle doit être supérieure ou égale à la valeur **Durée de vie du jeton d’actualisation (jours)** . Si le commutateur est défini sur **Non délimité**, vous ne pouvez pas fournir une valeur spécifique.
  * Par défaut : 90 jours.
  * Valeur minimale (inclusive) : 1 jour.
  * Valeur maximale (inclusive) : 365 jours.

Voici quelques cas d’usage que vous pouvez activer à l’aide de ces propriétés :

* Autoriser un utilisateur à rester connecté à une application mobile indéfiniment, tant qu’il est actif en permanence sur l’application. Vous pouvez configurer cela en définissant le commutateur **Durée de vie fenêtre glissante du jeton d’actualisation (jours)** sur **Non délimité** dans votre stratégie d’authentification.
* Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes du jeton d’accès.

    > [!NOTE]
    > Ces paramètres ne sont pas disponibles pour les stratégies de réinitialisation de mot de passe.
    > 
    > 

## <a name="token-compatibility-settings"></a>Paramètres de compatibilité de jeton

Nous avons apporté des modifications de mise en forme aux revendications importantes dans les jetons de sécurité émis par Azure AD B2C. Cela a été fait pour améliorer la prise en charge du protocole standard et pour une meilleure interopérabilité avec les bibliothèques d’identité tierces. Toutefois, pour éviter d’interrompre les applications existantes, nous avons créé les propriétés suivantes pour permettre aux clients de participer en fonction de leurs besoins :

* **Revendication de l’émetteur (iss)** : identifie le client Azure AD B2C qui a émis le jeton.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/` : il s’agit de la valeur par défaut.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` : cette valeur inclut l’ID pour le client B2C et la stratégie utilisée dans la demande de jeton. Si votre application ou bibliothèque a besoin qu’Azure AD B2C soit conforme à [la spécification OpenId Connect Discovery 1.0](http://openid.net/specs/openid-connect-discovery-1_0.html), utilisez cette valeur.
* **Revendication d’objet (obj)** : identifie l’entité, par exemple, l’utilisateur, pour laquelle le jeton indique des informations.
  * **ObjectID** : il s’agit de la valeur par défaut. Elle renseigne l’ID d’objet de l’utilisateur du répertoire dans la revendication `sub` du jeton.
  * **Non pris en charge** : uniquement fourni pour la compatibilité descendante, nous vous recommandons de passer à **ObjectID** dès que vous le pouvez.
* **ID de stratégie représentant la revendication** : identifie le type de revendication dans lequel l’ID de stratégie utilisé dans la demande de jeton est renseigné.
  * **tfp** : il s’agit de la valeur par défaut.
  * **acr** : uniquement fourni pour la compatibilité descendante, nous vous recommandons de passer à `tfp` dès que vous le pouvez.

## <a name="session-behavior"></a>Comportement de la session

Azure AD B2C prend en charge le [protocole d’authentification OpenID Connect](active-directory-b2c-reference-oidc.md) pour activer l’authentification sécurisée dans les applications web. Voici les propriétés que vous pouvez utiliser pour gérer les sessions d’application web :

* **Durée de vie de session d’application web (minutes)**: la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
  * Par défaut : 1 440 minutes.
  * Valeur minimale (inclusive) : 15 minutes.
  * Valeur maximale (inclusive) : 1 440 minutes.
* **Expiration de la session d’application web** : si ce commutateur est défini sur **Absolu**, l’utilisateur est obligé de s’authentifier de nouveau lorsque le délai spécifié dans **Durée de vie de la session d’application web (minutes)** est écoulé. Si ce commutateur est défini sur **Cumulé** (paramètre par défaut), l’utilisateur reste connecté tant qu’il est actif en permanence dans votre application web.

Voici quelques cas d’usage que vous pouvez activer à l’aide de ces propriétés :

* Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes de la session d’application web.
* Forcez la réauthentification après une période donnée pendant une interaction utilisateur avec une partie haute sécurité de votre application web. 

    > [!NOTE]
    > Ces paramètres ne sont pas disponibles pour les stratégies de réinitialisation de mot de passe.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Configuration de l’authentification unique
Si vous avez plusieurs applications et stratégies dans votre client B2C, vous pouvez gérer les interactions utilisateur à l’aide de la propriété **Configuration de l’authentification unique** . Vous pouvez définir la propriété sur l’un des paramètres suivants :

* **Client**: il s’agit du paramètre par défaut. L’utilisation de ce paramètre permet à plusieurs applications et stratégies dans votre client B2C de partager la même session utilisateur. Par exemple, lorsqu’un utilisateur se connecte à une application, Contoso Shopping, il peut également se connecter de façon transparente à une autre application, Contoso Pharmacy, lorsqu’il y accède.
* **Application**: ceci vous permet de maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, si vous souhaitez que l’utilisateur se connecte à Contoso Pharmacy (avec les mêmes informations d’identification), même s’il est déjà connecté à Contoso Shopping, une autre application sur le même client B2C. 
* **Stratégie**: ceci vous permet de maintenir une session utilisateur exclusivement pour une stratégie, indépendamment des applications qui l’utilisent. Par exemple, si l’utilisateur s’est déjà connecté et a effectué une étape d’authentification multifacteur, il peut obtenir l’accès à des parties plus sécurisées de plusieurs applications tant que la session liée à la stratégie n’expire pas.
* **Désactivé**: ceci oblige l’utilisateur à réexécuter toute la procédure pour chaque exécution de la stratégie. Par exemple, cela permet à plusieurs utilisateurs de s’inscrire à votre application (dans un scénario de type bureau partagé), même si un seul utilisateur reste connecté pendant toute la durée.

    > [!NOTE]
    > Ces paramètres ne sont pas disponibles pour les stratégies de réinitialisation de mot de passe.
    > 
    > 

