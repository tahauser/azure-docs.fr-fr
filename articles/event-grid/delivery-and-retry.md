---
title: Distribution et nouvelle tentative de distribution avec Azure Event Grid
description: "Décrit comment Azure Event Grid distribue des événements et gère les messages qui n’ont pas été distribués."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: cdf6a4e999d55196e8f4eac5695163a7e5a933de
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Distribution et nouvelle tentative de distribution de messages avec Azure Grid 

Cet article décrit comment Azure Event Grid gère les événements en l’absence d’accusé de réception d’une distribution.

Event Grid assure une distribution fiable. Il distribue chaque message au moins une fois pour chaque abonnement. Les événements sont envoyés immédiatement au webhook inscrit de chaque abonnement. Si un webhook n’accuse pas réception d’un événement dans les 60 secondes suivant la première tentative de distribution, Event Grid effectue une nouvelle tentative de distribution de l’événement.

## <a name="message-delivery-status"></a>État de distribution du message

Event Grid utilise les codes de réponse HTTP pour accuser réception des événements. 

### <a name="success-codes"></a>Codes de réussite

Les codes de réponse HTTP suivants indiquent qu’un événement a bien été distribué à votre webhook. Event Grid considère que la distribution est effectuée.

- 200 OK
- 202 Accepté

### <a name="failure-codes"></a>Codes d’échec

Les codes de réponse HTTP suivants indiquent un échec de la tentative de distribution d’un événement. Event Grid tente à nouveau d’envoyer l’événement. 

- 400 Demande incorrecte
- 401 Non autorisé
- 404 Introuvable
- 408 Délai d’expiration de la demande
- 414 URI trop long
- 500 Erreur interne du serveur
- 503 Service indisponible
- 504 Dépassement du délai de la passerelle

Tout autre code de réponse ou manque de réponse indique un échec. Event Grid effectue une nouvelle tentative de distribution. 

## <a name="retry-intervals"></a>Intervalles avant nouvelle tentative

Event Grid utilise une stratégie de nouvelle tentative d’interruption exponentielle pour la distribution des événements. Si votre webhook ne répond pas ou s’il retourne un code d’échec, Event Grid effectue une nouvelle tentative de distribution aux intervalles suivants :

1. 10 secondes
2. 30 secondes
3. 1 minute
4. 5 minutes
5. 10 minutes
6. 30 minutes
7. 1 heure

Event Grid ajoute une petite randomisation à tous les intervalles de nouvelle tentative. La remise des événements est renouvelée après une heure, une fois par heure.

## <a name="retry-duration"></a>Durée des nouvelles tentatives

Azure Event Grid fait expirer tous les événements qui ne sont pas distribués dans les 24 heures.

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher l’état des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).