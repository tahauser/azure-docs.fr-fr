---
title: "Détection intelligente - Élévation anormale du volume des exceptions dans Azure Application Insights | Microsoft Docs"
description: "Surveillez les exceptions d’application avec Azure Application Insights afin de déterminer si le volume des exceptions présente des anomalies."
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
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 8030f3331a03170bb265c417a57725544bdc7d3f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Élévation anormale du volume des exceptions (préversion)

Application Insights analyse automatiquement les exceptions levées dans votre application et peut vous avertir si vos données de télémétrie pour les exceptions présentent des anomalies.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la [configuration du rapport des exceptions](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) pour votre application. Elle est active quand votre application génère suffisamment de données de télémétrie pour les exceptions.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand pouvez-vous recevoir ce type de notification de détection intelligente ?
Vous pouvez recevoir ce type de notification si votre application présente une élévation anormale du nombre d’exceptions d’un type spécifique pendant une journée, par rapport à une ligne de base calculée sur les sept jours précédents.
Des algorithmes d’apprentissage automatique sont utilisés pour détecter l’augmentation du nombre d’exceptions, tout en tenant compte d’une croissance naturelle de l’utilisation de votre application.

## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?
Non, une notification ne signifie pas que votre application rencontre réellement un problème. Bien qu’un nombre excessif d’exceptions indique généralement un problème lié à l’application, ces exceptions peuvent être bénignes et être correctement traitées par votre application.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus de diagnostic :
1. **Tri.** La notification vous indique le nombre d’utilisateurs ou le nombre de demandes affectés. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Le problème affecte-t-il tout le trafic ou une opération seulement ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic.** La détection contient des informations sur la méthode à partir de laquelle l’exception a été levée, ainsi que le type d’exception. Pour mieux diagnostiquer le problème, vous pouvez également utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.