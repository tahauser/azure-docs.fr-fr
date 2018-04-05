---
title: API du SDK web Azure Mobile Engagement | Microsoft Docs
description: Dernières mises à jour et procédures du Kit de développement logiciel (SDK) web pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 6d2ae75b384b60d0383c1682a00a4fc0d19d0f43
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Utiliser l’API Azure Mobile Engagement dans une application web
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Ce document vient compléter celui vous décrivant comment [intégrer Mobile Engagement dans une application web](mobile-engagement-web-integrate-engagement.md). Il fournit des informations détaillées sur l’utilisation de l’API Azure Mobile Engagement pour signaler les statistiques de votre application.

L’API Mobile Engagement est fournie par l’objet `engagement.agent` . L’alias du Kit de développement logiciel (SDK) web d’Azure Mobile Engagement par défaut est `engagement`. Vous pouvez redéfinir cet alias dans la configuration du Kit de développement logiciel (SDK).

## <a name="mobile-engagement-concepts"></a>Concepts de Mobile Engagement
Les sections qui suivent affinent les [concepts Mobile Engagement](mobile-engagement-concepts.md) courants pour la plateforme web.

### <a name="session-and-activity"></a>`Session` et `Activity`
Si l’utilisateur reste inactif pendant plus de quelques secondes entre deux activités, sa séquence d’activités est fractionnée en deux sessions distinctes. Ces quelques secondes constituent le délai d’expiration de session.

Si votre application web ne déclare pas la fin des activités de l’utilisateur par elle-même (en appelant la fonction `engagement.agent.endActivity` ), le serveur Mobile Engagement termine automatiquement la session de l’utilisateur dans les trois minutes qui suivent la fermeture de la page d’application. C’est ce que l’on appelle le délai d’expiration de session du serveur.

### `Crash`
Les rapports automatisés d’exceptions JavaScript non interceptées ne sont pas créés par défaut. Toutefois, vous pouvez signaler les incidents manuellement à l’aide de la fonction `sendCrash` (voir la section sur les rapports d’incidents).

## <a name="reporting-activities"></a>Rapports d’activités
Les rapports d’activités de l’utilisateur incluent le moment où l’utilisateur démarre une nouvelle activité et où l’utilisateur met fin à l’activité en cours.

### <a name="user-starts-a-new-activity"></a>L’utilisateur démarre une nouvelle activité
    engagement.agent.startActivity("MyUserActivity");

Vous devez appeler `startActivity()` chaque fois que l’activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

### <a name="user-ends-the-current-activity"></a>L’utilisateur met fin à l’activité en cours
    engagement.agent.endActivity();

Vous devez appeler `endActivity()` au moins une fois lorsque l’utilisateur termine sa dernière activité. Le Kit de développement logiciel (SDK) web Mobile Engagement est ainsi informé que l’utilisateur est inactif et que la session utilisateur doit être fermée après le délai d’expiration de session. Si vous appelez `startActivity()` avant la fin du délai d’expiration de session, la session reprend simplement.

Étant donné qu’il n’existe aucun appel fiable lors de la fermeture de la fenêtre du navigateur, il est souvent difficile, voire impossible, d’intercepter la fin des activités de l’utilisateur dans un environnement web. C’est pourquoi le serveur Mobile Engagement termine automatiquement la session de l’utilisateur dans un délai de trois minutes après la fermeture de la page de l’application.

## <a name="reporting-events"></a>Rapports d’événements
Les rapports d’événements couvrent les événements de session et les événements autonomes.

### <a name="session-events"></a>Événements de session
Les événements de session servent généralement à signaler les actions effectuées par un utilisateur lors la session de l’utilisateur.

**Exemple sans données supplémentaires :**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exemple avec des données supplémentaires :**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Événements autonomes
Contrairement aux événements de session, les événements autonomes peuvent se produire en dehors du contexte d’une session.

Pour cela, utilisez ``engagement.agent.sendEvent`` au lieu de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Rapports d’erreurs
Les rapports d’erreurs couvrent les erreurs de session et les erreurs autonomes.

### <a name="session-errors"></a>Erreurs de session
Les erreurs de session servent généralement à signaler les erreurs qui ont un impact sur l’utilisateur pendant la session de l’utilisateur.

**Exemple sans données supplémentaires :**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exemple avec des données supplémentaires :**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Erreurs autonomes
Contrairement aux erreurs de session, les erreurs autonomes peuvent se produire en dehors du contexte d’une session.

Pour cela, utilisez `engagement.agent.sendError` au lieu de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Rapports de travaux
Les rapports de travaux couvrent les erreurs et les événements qui se produisent lors d’un travail, ainsi que les rapports d’incidents.

**Exemple :**

Pour surveiller une requête AJAX, vous utilisez les éléments suivants :

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Signaler les erreurs lors d’un travail
Les erreurs peuvent être associées à un travail en cours d’exécution plutôt qu’à la session utilisateur en cours.

**Exemple :**

Pour signaler une erreur si une requête AJAX échoue :

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Rapports d’événements pendant un travail
Les événements peuvent être associés à un travail en cours d’exécution plutôt qu’à la session utilisateur en cours grâce à la fonction `engagement.agent.sendJobEvent` .

Cette fonction fonctionne exactement comme `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Rapports d’incidents
Utilisez la fonction `sendCrash` pour signaler des incidents manuellement.

L’argument `crashid` est une chaîne qui identifie le type d’incident.
L’argument `crash` correspond généralement à l’arborescence des appels de procédure de l’incident sous forme de chaîne.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Paramètres supplémentaires
Vous pouvez joindre des données arbitraires à un événement, une erreur, une activité ou un travail.

Les données peuvent être n’importe quel objet JSON (mais pas un tableau ou un type primitif).

**Exemple :**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>limites
Les limites qui s’appliquent aux paramètres supplémentaires se situent dans les zones des expressions régulières pour les clés, les types de valeur et la taille.

#### <a name="keys"></a>Clés
Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

    ^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="values"></a>Valeurs
Les valeurs sont limitées aux types chaîne, nombre et Booléen.

#### <a name="size"></a>Taille
Les paramètres supplémentaires sont limités à 1 024 caractères par appel (une fois que le Kit de développement logiciel (SDK) Mobile Engagement Web l’encode dans JSON).

## <a name="reporting-application-information"></a>Rapports d’informations sur l’application
Vous pouvez signaler manuellement les informations de suivi (ou toutes autres informations spécifiques aux applications) à l’aide de la fonction `sendAppInfo()` .

Notez que ces informations peuvent être envoyées de façon incrémentielle. Seule la dernière valeur d’une clé spécifique est conservée pour un appareil donné.

Comme pour les paramètres supplémentaires d’événement, vous pouvez utiliser n’importe quel objet JSON pour extraire des informations sur l’application. Notez que les tableaux ou les sous-objets sont traités comme des chaînes plates (à l’aide de la sérialisation JSON).

**Exemple :**

Voici un exemple de code pour l’envoi du sexe et de la date de naissance de l’utilisateur :

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>limites
Les limites qui s’appliquent aux informations sur l’application se situent dans les zones des expressions régulières pour les clés et la taille.

#### <a name="keys"></a>Clés
Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

    ^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### <a name="size"></a>Taille
Les informations sur l’application sont limitées à 1 024 caractères par appel (une fois que le Kit de développement logiciel (SDK) Mobile Engagement Web l’encode dans JSON).

Dans l’exemple précédent, le JSON envoyé au serveur fait 44 caractères :

    {"birthdate":"1983-12-07","gender":"female"}
