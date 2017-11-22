---
title: "Configurer des notifications sur l’intégrité pour les systèmes de gestion des problèmes existants à l’aide d’un Webhook | Microsoft Docs"
description: "Obtenir des notifications personnalisées sur les événements d’intégrité du service sur votre système de gestion des problèmes existants."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Configurer des notifications sur l’intégrité pour les systèmes de gestion des problèmes existants à l’aide d’un Webhook

Cet article montre comment configurer vos alertes d’intégrité du service pour envoyer des données via des Webhooks à votre système de notification existant.

Aujourd’hui, vous pouvez configurer des alertes d’intégrité du service de sorte que lorsqu’un incident lié à un service Azure vous concerne, vous soyez averti par SMS ou e-mail.
Toutefois, vous disposez peut-être déjà d’un système de notification externe que vous souhaitez utiliser.
Ce document vous montre les parties les plus importantes de la charge utile du Webhook, et comment il est possible de créer des alertes personnalisées pour être averti lorsque vous êtes confronté à des problèmes de service.

Si vous souhaitez utiliser une intégration préconfigurée, découvrez comment :
* [Configurer des alertes avec ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurer des alertes avec PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurer des alertes avec OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Configuration d’une notification personnalisée à l’aide de la charge utile du Webhook de l’intégrité du service
Si vous souhaitez configurer l’intégration de votre propre Webhook personnalisé, vous devez analyser la charge utile JSON envoyée au cours des notifications sur l’intégrité du service.

Consultez [ici un exemple](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) de ce à quoi la charge utile du Webhook `Service Health` ressemble.

Il est possible de déterminer qu’il s’agit d’alerte d’intégrité du service en regardant `context.eventSource == "ServiceHealth"`. À partir de là, les propriétés qui sont les plus importantes à ingérer sont :
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>Création d’un lien direct vers l’intégrité du service Azure pour un incident
Vous pouvez créer un lien direct vers l’incident d’intégrité du service Azure personnalisé sur un PC de bureau ou un appareil mobile en générant une URL spécialisée. Utilisez `trackingId`, ainsi que le premier et les trois derniers chiffres de votre `subscriptionId`, pour former le lien :
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Par exemple, si votre `subscriptionId` est `bba14129-e895-429b-8809-278e836ecdb3` et votre `trackingId` est `0DET-URB`, votre URL d’intégrité du service Azure personnalisé sera :

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Utilisation du niveau pour détecter la gravité du problème
Du niveau de gravité le plus faible au plus élevé, la propriété `level` de la charge utile peut être `Informational`, `Warning`, `Error`, et `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Analyse des services concernés pour comprendre l’étendue de l’incident
Les alertes d’intégrité du service peuvent vous informer sur les problèmes survenant entre plusieurs régions et services. Pour obtenir tous les détails, vous devez analyser la valeur de `impactedServices`.
Le contenu est une chaîne [placée dans une séquence d’échappement JSON](http://json.org/). Sans séquence d’échappement, il contient un autre objet JSON pouvant être analysé régulièrement.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Cela devient :

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Cela montre qu’il existe des problèmes avec les alertes et les mesures dans l’est et le Sud-Est de l’Australie, ainsi que des problèmes avec App Service dans le Sud-Est de l’Australie.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Tester l’intégration à Webhook via une demande HTTP POST
1. Créez la charge utile d’intégrité du service que vous souhaitez envoyer. Vous trouverez un exemple de charge utile du Webhook d’intégrité du service sur la page [Webhook des alertes du journal d’activité Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Créez une requête HTTP POST comme suit :

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Vous devez recevoir une réponse `2XX - Successful`.

4. Accédez à [PagerDuty](https://www.pagerduty.com/) pour confirmer que votre intégration a été définie avec succès.

## <a name="next-steps"></a>Étapes suivantes
- Consultez le [schéma webhook des alertes de journal d’activité](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- En savoir plus sur les [notifications sur l’intégrité du service](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- En savoir plus sur les [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md).