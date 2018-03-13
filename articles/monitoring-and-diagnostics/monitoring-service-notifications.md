---
title: "Qu’est-ce qu’une notification Azure Service Health ? | Microsoft Docs"
description: "Les notifications Service Health vous permettent de voir les messages relatifs à l’intégrité du service publiés par Microsoft Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 4a95e9882515e6a2861292829a44847e11f39063
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Afficher les notifications Service Health dans le portail Azure

Les notifications Service Health sont publiées par Azure et contiennent des informations sur les ressources de votre abonnement. Ces notifications constituent une sous-classe des événements du journal d’activité, et figurent également dans le journal d’activité. Selon leur classe, les notifications Service Health peuvent être fournies à titre d’information ou nécessiter une action de votre part.

Il existe plusieurs classes de notifications Service Health :  

- **Action nécessaire :** Azure peut remarquer un événement inhabituel dans votre compte et vous aider à résoudre le problème. Azure vous envoie une notification contenant des informations sur les actions à effectuer, ou les coordonnées des ingénieurs ou du support Azure.  
- **Récupération assistée :** un événement s’est produit et les ingénieurs ont confirmé que vous en subissez toujours l’impact. Les ingénieurs Azure doivent travailler directement avec vous pour restaurer vos services intégralement.  
- **Incident :** un événement impactant le service affecte actuellement une ou plusieurs des ressources de votre abonnement.  
- **Maintenance :** une activité de maintenance planifiée peut impacter une ou plusieurs ressources de votre abonnement.  
- **Information :** suggestions d’optimisations pouvant aider à améliorer l’utilisation de vos ressources. 
- **Sécurité :** informations de sécurité urgentes concernant vos solutions exécutées dans Azure.

Chaque notification d’intégrité de service contient des détails sur l’étendue et l’impact sur vos ressources. Les détails sont les suivants :

Nom de la propriété | Description
-------- | -----------
channels | L’une des valeurs suivantes : **Admin** ou **Operation**.
correlationId | Généralement un GUID au format chaîne. Les événements qui appartiennent à la même action partagent généralement le même correlationId.
eventDataId | L’identificateur unique d’un événement.
eventName | Titre de l’événement.
level | Niveau de l’événement. L’une des valeurs suivantes : **Critical** (Critique), **Error** (Erreur), **Warning** (Avertissement) ou **Informational** (Information).
resourceProviderName | Nom du fournisseur de ressources de la ressource impactée.
resourceType| Le type de ressource de la ressource affectée.
subStatus | En général, le code d’état HTTP de l’appel REST correspondant. Peut également inclure d’autres chaînes décrivant un sous-état. Par exemple : OK (code d’état HTTP : 200), Created (code d’état HTTP : 201), Accepted (code d’état HTTP : 202), No Content (code d’état HTTP : 204), Bad Request (code d’état HTTP : 400), Not Found (code d’état HTTP : 404), Conflict (code d’état HTTP : 409), Internal Server Error (code d’état HTTP : 500), Service Unavailable (code d’état HTTP : 503) et Gateway Timeout (code d’état HTTP : 504).
eventTimestamp | Horodatage de la génération de l’événement effectuée par le service Azure lors du traitement de la requête correspondant à l’événement.
submissionTimestamp | Horodatage lorsque l’événement est devenu disponible pour l’interrogation.
subscriptionId | L’abonnement Azure dans lequel l’événement est enregistré.
status | Chaîne décrivant l’état de l’opération. Voici certaines valeurs courantes : **Started** (Démarré), **In Progress** (En cours), **Succeeded** (Réussi), **Failed** (Échec), **Active** (Actif) et **Resolved** (Résolu).
operationName | Nom de l’opération.
category | Cette propriété est toujours **ServiceHealth**.
ResourceId | ID de ressource de la ressource impactée.
Properties.title | Le titre localisé pour cette communication. L’anglais est la langue par défaut.
Properties.communication | Les détails localisés de la communication avec le balisage HTML. L’anglais est la langue par défaut.
Properties.incidentType | L’une des valeurs suivantes : **AssistedRecovery** (Récupération assistée), **ActionRequired** (Action nécessaire), **Information**, **Incident**, **Maintenance** ou **Security** (Sécurité).
Properties.trackingId | Incident associé à cet événement. Cela permet de mettre en corrélation les événements liés à un incident.
Properties.impactedServices | Objet blob JSON placé dans une séquence d’échappement, qui décrit les services et régions impactés par l’incident. La propriété inclut une liste de services, ayant chacun un **ServiceName** et une liste de régions impactées, ayant chacune un **RegionName**.
Properties.defaultLanguageTitle | Communication dans la langue par défaut.
Properties.defaultLanguageContent | Communication dans la langue par défaut, au format HTML ou texte brut.
Properties.stage | Les valeurs possibles pour **AssistedRecovery**, **ActionRequired**, **Information**, **Incident** et **Security** sont **Active** (Actif) ou **Resolved** (Résolu). Pour **Maintenance**, les valeurs possibles sont **Active**, **Planned** (Planifiée), **InProgress** (En cours), **Canceled** (Annulée), **Rescheduled** (Reportée), **Resolved** (Résolue) et **Complete** (Terminée).
Properties.communicationId | Communication associée à cet événement.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Afficher vos notifications Service Health dans le portail Azure
1.  Dans le [portail Azure](https://portal.azure.com), sélectionnez **Moniteur**.

    ![Capture d’écran du menu du portail Azure, avec l’option Surveiller sélectionnée](./media/monitoring-service-notifications/home-monitor.png)

    Azure Monitor rassemble tous vos paramètres de surveillance et données dans une vue centralisée. Il ouvre d’abord la section **Journal d’activité** .

3.  Sélectionnez **Alertes**.

    ![Capture d’écran du journal Suivi de l’activité, avec l’option Alertes sélectionnée](./media/monitoring-service-notifications/service-health-summary.png)
4. Sélectionnez **+ Ajouter une alerte de journal d’activité** pour configurer une alerte qui vous permettra d’être informé des futures notifications de service. Pour plus d’informations, consultez [Créer des alertes de journal d’activité sur les notifications de service](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Étapes suivantes
Recevez des [notifications d’alerte lorsqu’une notification Service Health](monitoring-activity-log-alerts-on-service-notifications.md) est publiée.  
Découvrez plus en détail les [alertes du journal d’activité](monitoring-activity-log-alerts.md).
