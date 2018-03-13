---
title: "Exemples et scénarios courants - Azure Logic Apps | Microsoft Docs"
description: "Découvrez les applications logiques grâce à des exemples, des scénarios, des didacticiels et des procédures pas à pas."
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 09/13/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: b88d0c1ccb7a729c95299bcdc3cba5fd73fcdeac
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Scénarios, exemples, didacticiels et procédures pas à pas courants relatifs à Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous permet d’orchestrer et d’intégrer différents services en fournissant [plus de 100 connecteurs prêts à l’emploi](../connectors/apis-list.md), depuis des connecteurs SQL Server ou SAP locaux jusqu’à Microsoft Cognitive Services. Le service Logic Apps est un service « sans serveur ». Ainsi, vous n’avez pas à vous soucier de la mise à l’échelle ni des instances. Il vous suffit de définir le workflow avec un déclencheur et les actions exécutées par ce workflow. La plateforme sous-jacente gère la mise à l’échelle, la disponibilité et les performances. Le service Logic Apps se révèle d’une grande utilité pour les cas d’usage et les scénarios impliquant la coordination de plusieurs actions dans différents systèmes.

Voici quelques exemples et scénarios courants qui vous aideront à en savoir plus sur les nombreux modèles et fonctionnalités pris en charge par [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="popular-starting-points-for-logic-app-workflows"></a>Points de départ courants pour les workflows d’application logique

Chaque application logique commence avec un seul et unique [*déclencheur*](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui démarre votre workflow d’application logique et transmet des données. Certains connecteurs fournissent des déclencheurs, qui présentent les types suivants :

* *Déclencheurs d’interrogation* : vérifient régulièrement l’existence de nouvelles données dans un point de terminaison de service. Lorsque de nouvelles données existent, le déclencheur crée et exécute une nouvelle instance de workflow en utilisant ces données en guise d’entrée.

* *Déclencheurs d’émission* : écoutent les données au niveau d’un point de terminaison de service et attendent qu’un événement spécifique se produise. Lorsque cet événement se produit, le déclencheur s’active aussitôt en créant et exécutant une instance de workflow qui utilise toutes les données disponibles en tant qu’entrée.

Voici quelques exemples de déclencheurs courants :

* Interrogation : 

  * Le [déclencheur **Planification - Périodicité**](../connectors/connectors-native-recurrence.md) vous permet de définir la date et l’heure de début et la périodicité du déclenchement de votre application logique. 
  Par exemple, vous pouvez sélectionner les jours de la semaine et les heures de la journée auxquels votre application logique doit se déclencher.

  * Le déclencheur « Lors de la réception d’un message électronique » permet à votre application logique de vérifier l’existence de nouveaux messages électroniques à partir de n’importe quel fournisseur de messagerie pris en charge par Logic Apps, par exemple, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/), etc.

  * Le [déclencheur **HTTP**](../connectors/connectors-native-http.md) permet à votre application logique de vérifier un point de terminaison de service spécifié en communiquant par le biais de HTTP.
  
* Émission :

  * Le [déclencheur **Requête/réponse - Requête**](../connectors/connectors-native-reqres.md) permet à votre application logique de recevoir des requêtes HTTP et de répondre d’une certaine manière aux événements en temps réel.

  * Le [déclencheur **HTTP Webhook**](../connectors/connectors-native-webhook.md) s’abonne au point de terminaison d’un service en inscrivant une *URL de rappel* auprès de ce service. 
  De cette façon, le service peut tout simplement informer le déclencheur lorsque l’événement spécifié se produit, ce qui évite au déclencheur d’avoir à interroger le service.

Après avoir reçu une notification concernant de nouvelles données ou un événement, le déclencheur s’active, crée une instance de workflow d’application logique et exécute les actions dans le workflow. Vous pouvez accéder à toutes les données du déclencheur dans l’ensemble du workflow. Par exemple, le déclencheur « On a new tweet » (En cas de nouveau tweet) transmet le contenu du tweet dans l’exécution de l’application logique. 

## <a name="respond-to-triggers-and-extend-actions"></a>Répondre aux déclencheurs et étendre des actions

Pour les systèmes et services pour lesquels aucun connecteur n’a été publié, vous pouvez également étendre les applications logiques.

* [Créer des actions ou des déclencheurs personnalisés](../logic-apps/logic-apps-create-api-app.md)
* [Configurer des actions de longue durée pour les exécutions de flux de travail](../logic-apps/logic-apps-create-api-app.md)
* [Répondre aux événements et actions externes à l’aide de webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Appeler, déclencher ou imbriquer des flux de travail avec des réponses synchrones aux requêtes HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Didacticiel expliquant comment créer un tableau de bord social reposant sur l’intelligence artificielle en quelques minutes avec Logic Apps et Power BI](http://aka.ms/logicappsdemo)
* [Didacticiel expliquant comment répondre aux webhooks SMS Twilio et envoyer une réponse textuelle](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Fonctionnalités de flux de contrôle, de gestion des erreurs et de journalisation

Les applications logiques incluent de puissantes fonctionnalités de flux de contrôle avancé, notamment des conditions, des commutateurs, des boucles et des étendues. Pour garantir la résilience de vos solutions, vous pouvez également implémenter la gestion des erreurs et des exceptions dans vos flux de travail. Pour la notification et les journaux de diagnostic relatifs à l’état d’exécution des flux de travail, Azure Logic Apps fournit également la surveillance et des alertes.

* [Traiter les éléments dans des tableaux et collections avec des boucles et des lots dans les applications logiques](../logic-apps/logic-apps-loops-and-scopes.md)
* [Effectuer différentes actions avec les instructions switch](../logic-apps/logic-apps-switch-case.md)
* [Gestion des erreurs de création et des exceptions dans un flux de travail](../logic-apps/logic-apps-exception-handling.md)
* [Cas d’utilisation : comment un prestataire de soins de santé utilise la gestion des exceptions d’application logique pour les flux de travail HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Activer la surveillance, la journalisation et les alertes pour les applications logiques existantes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Activer la surveillance et la journalisation des diagnostics lors de la création d’applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Déployer et gérer des applications logiques

Vous pouvez développer et déployer des applications logiques entièrement avec Visual Studio, Visual Studio Team Services ou tout autre outil de compilation automatisée et contrôle de code source. Pour prendre en charge le déploiement de flux de travail et de connexions dépendantes d’un modèle de ressource, les applications logiques utilisent des modèles de déploiement de ressources Azure. Les outils Visual Studio génèrent automatiquement ces modèles, que vous pouvez archiver dans le contrôle de code source à des fins de contrôle de version.

* [Créer et déployer des applications logiques à partir de Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Activer la surveillance, la journalisation et les alertes pour les applications logiques existantes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Créer un modèle de déploiement automatisé](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Types de contenu, conversions et transformations d’une exécution

Vous pouvez accéder à plusieurs types de contenu, les convertir et les transformer à l’aide des nombreuses fonctions du [langage de définition de flux de travail](http://aka.ms/logicappsdocs) d’Azure Logic Apps. Par exemple, vous pouvez effectuer des conversions entre une chaîne, JSON et XML avec les expressions de flux de travail `@json()` et `@xml()`. Le moteur Logic Apps préserve les types de contenu pour prendre en charge le transfert de contenu entre les services sans perte.

* [Fonctionnement des expressions de flux de travail dans les applications logiques](../logic-apps/logic-apps-author-definitions.md)
* [Gérer les types de contenu non-JSON](../logic-apps/logic-apps-content-type.md), comme `application/xml`, `application/octet-stream` et`multipart/formdata`
* [Référence : Langage de définition de flux de travail Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Autres intégrations et fonctionnalités

Les applications logiques offrent également une intégration avec de nombreux services comme Azure Functions, la Gestion des API Azure, Azure App Services et les points de terminaison HTTP personnalisés, par exemple, REST et SOAP.

* [Créer un tableau de bord social en temps réel avec Azure Serverless](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Appeler Azure Functions à partir d’applications logiques](../logic-apps/logic-apps-azure-functions.md)
* [Scénario : Déclencher des applications logiques avec Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Blog : Appeler des points de terminaison SOAP à partir d’applications logiques](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Scénarios de bout en bout

* [Livre blanc : Gestion de cas de bout en bout d’intégration d’entreprise avec des services Azure, tels que des applications logiques](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>Étapes suivantes

* [Créer des définitions de flux de travail avec le langage de définition de flux de travail](../logic-apps/logic-apps-author-definitions.md)
* [Gérer les erreurs et les exceptions dans Azure Logic Apps](../logic-apps/logic-apps-exception-handling.md)
* [Envoyer vos questions, commentaires ou suggestions concernant les améliorations que nous pourrions apporter à Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)