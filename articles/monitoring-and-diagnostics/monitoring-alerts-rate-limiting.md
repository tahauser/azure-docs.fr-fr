---
title: Restriction des SMS, e-mails, Webhooks et notifications Push Azure App | Microsoft Docs
description: "Découvrez comment Azure limite le nombre de notifications possibles par SMS, e-mail, Webhook ou Push Azure App à partir d’un groupe d’actions."
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitation de la fréquence des SMS, e-mails, notifications push Azure App et publications Webhook
La limitation de la fréquence est une suspension des notifications qui se produit lorsque trop de notifications sont envoyées à une adresse e-mail, un numéro de téléphone donné ou un appareil. Elle garantit que les alertes sont faciles à gérer et exploitables.

Voici les seuils de limitation de la fréquence :

 - **SMS** : pas plus de 1 SMS toutes les 5  minutes.
 - **E-mail** : 100 messages en une heure.
 - **Notifications Push Azure App** : il n’existe pas de restriction pour les notifications Push.
 - **Webhooks** : aucune restriction n’est appliquée aux Webhooks.

## <a name="rate-limit-rules"></a>Règles de limitation de la fréquence
- Un numéro de téléphone ou une adresse e-mail donné est soumis à une limitation de la fréquence lorsqu’il reçoit plus de messages que ne l’autorise le seuil.
- Un numéro de téléphone ou une adresse de messagerie peut faire partie de groupes d’actions sur plusieurs abonnements. La limitation de la fréquence s’applique à tous les abonnements, dès lors que le seuil est atteint, même si les messages sont envoyés à partir de plusieurs abonnements.  
- Lorsqu’une adresse e-mail fait l’objet d’une restriction, une notification supplémentaire est envoyée à ce sujet. Cette notification indique la fin de la restriction.

## <a name="next-steps"></a>Étapes suivantes ##
* En savoir plus sur le [comportement des alertes SMS](monitoring-sms-alert-behavior.md).
* Obtenir une [vue d’ensemble des alertes du journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md).
