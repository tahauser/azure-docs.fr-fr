---
title: "Détection intelligente - Pack de détection de la sécurité avec Azure Application Insights | Microsoft Docs"
description: "Effectuez le monitoring des applications avec Azure Application Insights pour détecter les éventuels problèmes de sécurité."
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
ms.openlocfilehash: 837525d70f28c0710b8a8b86d411a7854402207f
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="application-security-detection-pack-preview"></a>Pack de détection de la sécurité des applications (préversion)

Application Insights analyse automatiquement les données de télémétrie générées par votre application et détecte les problèmes potentiels de sécurité. Cette fonctionnalité permet d’identifier les problèmes potentiels de sécurité et de les traiter en corrigeant l’application ou en prenant les mesures de sécurité nécessaires.

Cette fonctionnalité ne requiert aucune autre configuration spéciale que la [configuration de l’envoi des données de télémétrie par l’application](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Trois types de problèmes de sécurité sont détectés :
1. Accès à l’URL non sécurisé : une URL de l’application est accessible à la fois suivant le protocole HTTP et suivant le protocole HTTPS. En règle générale, une URL qui accepte les requêtes HTTPS ne doit pas autoriser les requêtes HTTP. C’est peut-être le signe d’un problème de sécurité ou d’un bogue dans l’application.
2. Formulaire non sécurisé : un formulaire (ou une autre requête « POST ») de l’application utilise le protocole HTTP au lieu de HTTPS. Cela peut compromettre les données utilisateur envoyées par le biais du formulaire.
3. Activités utilisateur suspectes : un même utilisateur accède à l’application à partir de différents pays à peu près au même moment, par exemple, de l’Espagne et des États-Unis au cours de la même heure. Cette détection indique une tentative potentiellement malveillante d’accès à l’application.

## <a name="does-my-app-definitely-have-a-security-issue"></a>L’application présente-t-elle nécessairement un problème de sécurité ?
Non, une notification ne signifie pas nécessairement que l’application présente réellement un problème de sécurité. Dans de nombreux cas, la détection d’un des scénarios ci-dessus peut indiquer un problème de sécurité. Toutefois, il se peut qu’elle ait une justification naturelle pour l’entreprise, et qu’elle puisse être ignorée.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Comment corriger la détection « Accès à l’URL non sécurisé » ?
1. **Triage.** La notification indique le nombre d’utilisateurs ayant accédé à des URL non sécurisées, ainsi que l’URL la plus affectée par des accès non sécurisés. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Quelle est la proportion d’utilisateurs ayant accédé à des URL non sécurisées ? Combien d’URL ont été affectées ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection donne la liste des requêtes non sécurisées, ainsi que des URL et des utilisateurs affectés, pour permettre de diagnostiquer le problème.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Comment corriger la détection « Formulaire non sécurisé » ?
1. **Triage.** La notification indique le nombre de formulaires non sécurisés et le nombre d’utilisateurs dont les données sont potentiellement compromises. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Quel est le formulaire qui a été impliqué dans le plus grand nombre de transmissions non sécurisées, et quelle est la distribution des transmissions non sécurisées au fil du temps ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection donne la liste des formulaires non sécurisés, ainsi que la répartition du nombre de transmissions non sécurisées pour chaque formulaire, pour permettre de diagnostiquer le problème.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Comment corriger la détection « Activité utilisateur suspecte » ?
1. **Triage.** La notification indique le nombre d’utilisateurs différents qui ont présenté le comportement suspect. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** De quels pays les requêtes suspectes provenaient-elles ? Quel est l’utilisateur le plus suspect ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection donne la liste des utilisateurs suspects et des pays de chacun, pour permettre de diagnostiquer le problème.
