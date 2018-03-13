---
title: "Scenario : créer un tableau de bord Insights client avec Azure Serverless | Microsoft Docs"
description: "Exemple de création d’un tableau de bord pour gérer des commentaires client, des données sociales, etc. avec Azure Logic Apps et Azure Functions."
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
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: d3e07b8d7194d83e3ba3986177170edff21e1d7a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Créer un tableau de bord Insights client en temps réel avec Azure Logic Apps et Azure Functions

Les outils Azure Serverless fournissent de puissantes fonctionnalités pour générer rapidement et héberger des applications dans le cloud, sans avoir à réfléchir à l’infrastructure.  Dans ce scénario, nous allons créer un tableau de bord qui se déclenchera lors de commentaires client, pour analyser les commentaires liés à Machine Learning et publier une source telle que Power BI ou Azure Data Lake.

## <a name="overview-of-the-scenario-and-tools-used"></a>Vue d’ensemble du scénario et des outils utilisés

Pour implémenter cette solution, nous allons tirer parti des deux principaux composants des applications sans serveur dans Azure : [Azure Functions](https://azure.microsoft.com/services/functions/) et [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).

Logic Apps est un moteur de flux de travail sans serveur dans le cloud.  Il permet d’orchestrer les composants sans serveur, et se connecte également à plus de 100 services et API.  Pour ce scénario, nous allons créer une application logique qui se déclenchera lors des commentaires des clients.  Voici certains des connecteurs qui peuvent aider à réagir aux commentaires client : Outlook.com, Office 365, Survey Monkey, Twitter et une requête HTTP [à partir d’un formulaire web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  Pour le flux de travail ci-dessous, nous allons surveiller un mot-dièse sur Twitter.

Les fonctions fournissent le mode de calcul sans serveur dans le cloud.  Dans ce scénario, nous allons utiliser Azure Functions pour marquer les tweets de clients en fonction d’une série de mots-clés prédéfinis.

La solution complète peut être [générée dans Visual Studio](logic-apps-deploy-from-vs.md) et [déployée dans le cadre d’un modèle de ressource](logic-apps-create-deploy-template.md).  Une vidéo de procédure pas à pas du scénario existe également [sur Channel 9](http://aka.ms/logicappsdemo).

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Générer l’application logique à déclencher sur des données du client

Après avoir [créé une application logique](quickstart-create-first-logic-app-workflow.md) dans Visual Studio ou le portail Azure :

1. Ajoutez un déclencheur pour **On New Tweets (Aux nouveaux tweets)** de Twitter.
2. Configurez le déclencheur pour qu’il écoute les tweets sur un mot-clé ou un mot-dièse.

   > [!NOTE]
   > La propriété de périodicité du déclencheur détermine la fréquence à laquelle l’application logique recherche de nouveaux éléments dans les déclencheurs d’interrogation.

   ![Exemple de déclencheur Twitter][1]

Cette application se déclenche sur tous les nouveaux tweets.  Nous pouvons ensuite prendre ces données de tweet et comprendre davantage les opinions exprimées.  Pour ce faire, nous utilisons [Azure Cognitive Service](https://azure.microsoft.com/services/cognitive-services/).

1. Cliquez sur **Nouvelle étape**.
1. Sélectionnez ou recherchez le connecteur **Analyse de texte**.
1. Sélectionnez l’opération **Detect Sentiment (Détecter le sentiment)**.
1. Si vous y êtes invité, indiquez une clé Cognitive Services valide pour le service d’analyse de texte.
1. Ajouter le **Tweet text (Texte du tweet)** à analyser.

Maintenant que nous disposons des données du tweet et d’informations sur celui-ci, plusieurs autres connecteurs peuvent être pertinents :
* Power BI - Ajouter des lignes au jeu de données de streaming : afficher des tweets dans un tableau de bord Power BI en temps réel.
* Azure Data Lake - Ajouter un fichier : ajouter des données client à un jeu de données Azure Data Lake à inclure dans des travaux analytiques.
* SQL - Ajouter des lignes : stocker des données dans une base de données pour les récupérer ultérieurement.
* Slack - Envoyer un message : alerter un canal Slack en cas de commentaires négatifs nécessitant des actions.

Une fonction Azure permet également d’effectuer des calculs plus personnalisés des données.

## <a name="enriching-the-data-with-an-azure-function"></a>Enrichissement des données avec une fonction Azure

Avant de pouvoir créer une fonction, nous devons disposer d’une application de fonction dans notre abonnement Azure.  Des informations détaillées sur la création d’une fonction Azure dans le portail [se trouvent ici](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Pour qu’une fonction soit appelée directement à partir d’une application logique, elle doit posséder une liaison de déclencheur HTTP.  Nous vous recommandons d’utiliser le modèle **HttpTrigger**.

Dans ce scénario, le corps de la requête de la fonction Azure est le texte de tweet.  Dans le code de fonction, définissez simplement une logique si le texte de tweet contient un mot-clé ou une expression.  La fonction proprement dite peut rester simple ou être plus complexe selon les besoins du scénario.

À la fin de la fonction, retournez simplement une réponse à l’application logique avec certaines données.  Il peut s’agir d’une valeur booléenne simple (par exemple, `containsKeyword`) ou d’un objet complexe.

![Étape de configuration d’une fonction Azure][2]

> [!TIP]
> Lorsque vous accédez à une réponse complexe d’une fonction dans une application logique, utilisez l’action Analyser JSON.

Une fois la fonction enregistrée, elle peut être ajoutée dans l’application logique créée ci-dessus.  Dans l’application logique :

1. Cliquez pour ajouter une **Nouvelle étape**.
1. Sélectionnez le connecteur **Azure Functions**.
1. Sélectionnez une fonction existante, puis accédez à la fonction créée.
1. Envoyez le **Texte du tweet** comme **Corps de la requête**.

## <a name="running-and-monitoring-the-solution"></a>Exécution et surveillance de la solution

L’un des avantages de la création d’orchestrations sans serveur dans Logic Apps est la fonctionnalité enrichie de débogage et de surveillance.  Toute exécution (actuelle ou historique) peut être affichée dans Visual Studio, le portail Azure ou via l’API REST et les Kits de développement logiciel (SDK).

L’une des méthodes les plus simples de tester une application logique consiste à utiliser le bouton **Exécuter** dans le concepteur.  Si vous cliquez sur **Exécuter**, vous continuez d’interroger le déclencheur toutes les 5 secondes jusqu’à ce qu’un événement soit détecté, et fournissez un affichage en direct à mesure de la progression de l’exécution.

Les historiques d’exécutions précédentes peuvent être affichés dans le panneau Vue d’ensemble du portail Azure ou à l’aide de Visual Studio Cloud Explorer.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Création d’un modèle de déploiement pour les déploiements automatisés

Une fois qu’une solution a été développée, elle peut être capturée et déployée via un modèle de déploiement Azure vers toute région Azure du monde.  Cela est utile pour modifier les paramètres des différentes versions de ce flux de travail, mais également pour intégrer cette solution à un pipeline de génération et de mise en production.  Les détails sur la création d’un modèle de déploiement se trouvent [dans cet article](logic-apps-create-deploy-template.md).

La solution Azure Functions peut aussi être intégrée au modèle de déploiement. De cette façon, la solution complète avec toutes les dépendances peut être gérée comme un seul et même modèle.  Vous trouverez un exemple de modèle de déploiement de fonction dans le [référentiel de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Étapes suivantes

* [Voir d’autres exemples et scénarios relatifs à Azure Logic Apps](logic-apps-examples-and-scenarios.md)
* [Regarder une vidéo de procédure pas à pas sur la création de cette solution de bout en bout](http://aka.ms/logicappsdemo)
* [Découvrir comment gérer et intercepter les exceptions dans une application logique](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png