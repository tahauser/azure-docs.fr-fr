---
title: "Détection intelligente - Fuite de mémoire potentielle détectée par Azure Application Insights | Microsoft Docs"
description: "Surveiller les applications avec Azure Application Insights pour détecter les fuites de mémoire potentielles."
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
ms.openlocfilehash: 452d0a9d0231e54df2a7f1df76c3c2c0fcd94d87
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="memory-leak-detection-preview"></a>Détection des fuites de mémoire (version préliminaire)

Application Insights analyse automatiquement la consommation de mémoire des processus de votre application et peut vous avertir des fuites de mémoire potentielles ou d’une consommation accrue de la mémoire.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la [configuration des compteurs de performances](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) pour votre application. Elle est active lorsque votre application génère suffisamment de données de télémétrie sur les compteurs de performances mémoire (par exemple, les octets privés).


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand pouvez-vous recevoir ce type de notification de détection intelligente ?
Une notification typique suivra une augmentation constante de la consommation de mémoire sur une longue période de temps (quelques heures), pour un ou plusieurs processus et/ou une ou plusieurs machines, faisant partie de votre application.
Les algorithmes Machine Learning sont utilisés pour la détection d’une augmentation de la consommation de mémoire correspondant à un modèle de fuite de mémoire, contrairement à l’augmentation de la consommation de mémoire engendrée par une augmentation naturelle de l’utilisation des applications.

## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?
Non, une notification ne signifie pas que votre application rencontre réellement un problème. Bien que les modèles de fuites de mémoire indiquent généralement un problème au niveau de l’application, ces modèles peuvent être liés à votre processus spécifique, ou peuvent avoir une justification naturelle, et peuvent être ignorés.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus d’analyse de diagnostic :
1. **Tri.** La notification vous montre la quantité d’augmentation de mémoire (en Go) et l’intervalle de temps au cours duquel celle-ci s’est produite. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Combien de machines présentent un modèle de fuite de mémoire ? Combien d’exceptions ont été déclenchées au cours de la fuite de mémoire potentielle ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic.** La détection contient le modèle de fuite de mémoire, montrant la consommation de mémoire du processus au fil du temps. Pour mieux diagnostiquer le problème, vous pouvez également utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.
