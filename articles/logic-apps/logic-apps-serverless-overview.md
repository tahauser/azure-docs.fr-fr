---
title: "Vue d’ensemble d’une application sans serveur dans Azure | Documents Microsoft"
description: "Créez des solutions puissantes dans le cloud sans avoir à penser à l’infrastructure."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5cc6837ed0b0f4467e48c736f5d596a51a799fae
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Vue d’ensemble d’une application sans serveur dans Azure avec Functions et Logic Apps

Les applications sans serveur offrent les avantages suivants : augmentation de la vitesse de développement, réduction de code requis et simplicité de mise à l’échelle.  Cet article porte sur les différents attributs des solutions sans serveur et des offres sans serveur dans Azure.

## <a name="what-is-serverless"></a>Que sont les environnements sans serveur ?

« Sans serveur » ne signifie pas qu’il n’y a pas de serveurs, mais simplement que le développeur n’a pas à se soucier des serveurs.  Une grande partie du développement traditionnel d’application traite de la mise à l’échelle, l’hébergement et la surveillance des solutions pour répondre aux besoins de l’application.  Avec les offres sans serveur, ces questions sont prises en charge dans le cadre de la solution.  En outre, les applications sans serveur sont facturées en fonction de la consommation.  Si l’application n’est jamais utilisée, aucun frais n’est engagé.  Ces fonctionnalités permettent aux développeurs de se concentrer exclusivement sur la logique métier de la solution.

Les principaux services sans serveur dans Azure sont [Azure Functions](https://azure.microsoft.com/services/functions/) et [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Ces deux solutions respectent les principes ci-dessus et permettent aux développeurs de créer des applications cloud robustes avec un minimum de code.

## <a name="what-are-azure-functions"></a>Qu’est-ce qu’Azure Functions ?

Azure Functions est une solution conçue pour exécuter facilement des petits morceaux de code, ou « fonctions », dans le cloud. Vous pouvez simplement écrire le code dont vous avez besoin pour le problème, sans vous soucier d’une application dans sa globalité ou de l’infrastructure pour l’exécuter. Grâce à Functions, le développement gagne en productivité et vous pouvez utiliser votre langage de développement préféré, tel que C#, F#, Node.js, Python ou PHP. Payez uniquement pour le temps d’exécution de votre code et Azure évolue en fonction des besoins.

Si vous souhaitez vous familiariser avec Azure Functions, commencez par [créer votre première fonction Azure](../azure-functions/functions-create-first-azure-function.md). Si vous recherchez des informations plus techniques sur Azure Functions, consultez les [informations de référence pour les développeurs](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Qu’est-ce qu’Azure Logic Apps ?

Azure Logic Apps permet de simplifier et d’implémenter des intégrations et des flux de travail évolutifs dans le cloud. Son concepteur visuel modélise et automatise votre processus sous la forme d’une série d’étapes appelée flux de travail.  Il existe de [nombreux connecteurs](../connectors/apis-list.md) dans les services cloud et locaux pour connecter rapidement une application sans serveur à d’autres API.  Une application logique commence par un déclencheur (tel que « Lorsqu’un compte est ajouté à Dynamics CRM »), et après le déclenchement, peut initialiser de nombreuses actions de combinaison, des conversions et une logique de condition.  Logic Apps est un choix parfait lorsque vous orchestrez différentes fonctions Azure dans un processus, en particulier lorsque le processus doit interagir avec un système externe ou une API.

Pour commencer avec Logic Apps, [créez votre première application logique](quickstart-create-first-logic-app-workflow.md).  Si vous recherchez des informations plus techniques sur Logic Apps, consultez les [références du développeur](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Comment puis-je créer et déployer des applications sans serveur dans Azure ?

Azure fournit un ensemble complet d’outils pour le développement, le déploiement et la gestion des applications sans serveur.  Les applications peuvent être créées directement dans le portail Azure ou avec les [outils de Visual Studio](logic-apps-serverless-get-started-vs.md).  Une fois qu’une application a été développée, elle peut être [déployée immédiatement](logic-apps-create-deploy-template.md).  Azure fournit également une surveillance pour les applications sans serveur.  Vous pouvez accéder à cette surveillance à partir du portail Azure, via l’API ou les kits de développement logiciel, ou avec des outils intégrés à OMS et Application Insights.

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de la création d’une application sans serveur dans Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Création d’un tableau de bord des insights client sans serveur](logic-apps-scenario-social-serverless.md)
* [Création d’un modèle de déploiement pour une application logique](logic-apps-create-deploy-template.md)