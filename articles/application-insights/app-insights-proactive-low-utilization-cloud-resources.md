---
title: "Détection intelligente - Faible utilisation des ressources cloud détectée par Azure Application Insights | Microsoft Docs"
description: "Surveiller les applications avec Azure Application Insights pour détecter une faible utilisation des ressources cloud."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 8382f6047ae222a01cc0e8d6ca9dcf5593d0dff6
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="low-utilization-of-cloud-resources-preview"></a>Faible utilisation des ressources cloud (version préliminaire)

Application Insights analyse automatiquement la consommation du processeur de chaque instance de rôle dans votre application et détecte les instances qui utilisent peu le processeur. Cette détection permet de réduire les ressources Azure et les coûts, en diminuant le nombre d’instances de rôle utilisées par chaque rôle ou en diminuant le nombre de rôles.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la [configuration des compteurs de performances](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) pour votre application. Elle est active lorsque votre application génère suffisamment de télémétrie de compteur de performances du processeur (% de temps processeur).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand pouvez-vous recevoir ce type de notification de détection intelligente ?
Une notification type se produit lorsqu’un grand nombre de vos instances de rôle web/de travail présentent une faible utilisation du processeur.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Mon application consomme-t-elle vraiment trop de ressources ?
Non, une notification ne signifie pas que votre application consomme vraiment trop de ressources. Bien que ces modèles de faible utilisation du processeur indiquent généralement que la consommation de ressources pourrait être diminuée, ce comportement peut être typique de votre rôle spécifique ou peut se justifier sur le plan commercial et peut être ignoré. Par exemple, peut être que plusieurs instances sont nécessaires pour d’autres ressources, telles que la mémoire/le réseau et pas le processeur.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus de diagnostic :
1. **Tri.** La notification affiche les rôles de votre application qui présentent une faible utilisation du processeur. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Combien de rôles présentent une faible utilisation du processeur et combien d’instances de chaque rôle utilisent peu le processeur ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection contient le pourcentage d’utilisation du processeur, indiquant l’utilisation du processeur par chaque instance au fil du temps. Pour mieux diagnostiquer le problème, vous pouvez également utiliser les éléments liés et les rapports pointant vers des informations de prise en charge, comme les centiles d’utilisation du processeur.
