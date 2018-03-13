---
title: "Vérifier l’état, configurer la journalisation et recevoir des alertes - Azure Logic Apps | Microsoft Docs"
description: "Surveiller l’état et les performances des applications logiques, journaliser les données de diagnostic et configurer les alertes"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 0dc8bc81ca6125d40d1784ce39fd0facaf9e736a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Surveiller l’état, configurer la journalisation des diagnostics et activer les alertes pour Azure Logic Apps

Après avoir [créé et exécuté une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md), vous pouvez vérifier son historique d’exécutions, son historique du déclencheur, son état et ses performances. Pour une surveillance des événements en temps réel et un débogage enrichi, configurez une [journalisation des diagnostics](#azure-diagnostics) pour votre application logique. De cette façon, vous pouvez [rechercher et consulter des événements](#find-events), tels que des événements de déclencheur, des événements d’exécution et des événements d’action. Vous pouvez également utiliser ces [données de diagnostic avec d’autres services](#extend-diagnostic-data), tels que Stockage Azure et Azure Event Hubs. 

Pour obtenir des notifications concernant des échecs ou d’autres problèmes éventuels, configurez des [alertes](#add-azure-alerts). Par exemple, vous pouvez créer une alerte qui détecte quand plus de cinq exécutions échouent en une heure. Vous pouvez également configurer la surveillance, le suivi et journalisation par programme à l’aide des [Paramètres et propriétés d’événements de Diagnostics Azure](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Afficher l’historique des exécutions et du déclencheur pour votre application logique

1. Pour trouver votre application logique dans le [portail Azure](https://portal.azure.com), dans le menu principal Azure, choisissez **Tous les services**. Dans la zone de recherche, tapez « logic apps », puis choisissez **Logic apps**.

   ![Trouver votre application logique](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Le portail Azure affiche toutes les applications logiques associées à votre abonnement Azure. 

2. Sélectionnez votre application logique, puis choisissez **Vue d’ensemble**.

   Le portail Azure affiche l’historique des exécutions et l’historique du déclencheur pour votre application logique. Par exemple : 

   ![Historique des exécutions et l’historique du déclencheur de l’application logique](media/logic-apps-monitor-your-logic-apps/overview.png)

   * L’**Historique des exécutions** montre toutes les exécutions de votre application logique. 
   * L’**Historique du déclencheur** montre toutes les activités de déclencheur de votre application logique.

   Pour des descriptions d’état, voir [Dépanner votre application logique](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Si vous ne trouvez pas les données que vous attendez, dans la barre d’outils, choisissez **Actualiser**.

3. Pour afficher les étapes d’une exécution spécifique, sous **Historique des exécutions**, sélectionnez cette exécution. 

   La vue de moniteur affiche chaque étape de cette exécution. Par exemple : 

   ![Actions d’une exécution spécifique](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Pour obtenir plus d’informations sur l’exécution, choisissez **Détails de l’exécution**. Ces informations résument les étapes, l’état, les entrées et les sorties de l’exécution. 

   ![Choisir « Détails de l’exécution »](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Par exemple, vous pouvez obtenir l’**ID de corrélation** de l’exécution, dont vous pouvez avoir besoin lorsque vous utilisez l’[API REST pour Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Pour obtenir plus d’informations sur une étape spécifique, choisissez celle-ci. Vous pouvez maintenant examiner des détails tels que les entrées, les sorties et les erreurs qui se sont produites pour cette étape. Par exemple : 

   ![Détails de l’étape](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Tous les détails et événements d’exécution sont chiffrés dans le service Logic Apps. Il sont déchiffrés uniquement quand un utilisateur demande à consulter ces données. Vous pouvez également contrôler l’accès à ces événements avec le [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-what-is.md).

6. Pour obtenir des détails sur un événement déclencheur spécifique, revenez au volet **Vue d’ensemble**. Sous **Historique du déclencheur**, sélectionnez l’événement déclencheur. Vous pouvez maintenant examiner des détails tels que les entrées et sorties, par exemple :

   ![Détails de sortie d’événement déclencheur](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Activer la journalisation des diagnostics pour votre application logique

Pour un débogage enrichi avec des détails et événements d’exécution, vous pouvez configurer la journalisation des diagnostics avec [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics est un service d’[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) qui surveille vos environnements cloud et locaux pour vous aider à maintenir leur disponibilité et leurs performances. 

Avant de commencer, vous devez disposer d’un espace de travail OMS. Découvrez comment [créer un espace de travail OMS](../log-analytics/log-analytics-get-started.md).

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre application logique. 

2. Dans le menu du panneau d’application logique, sous **Surveillance**, choisissez **Diagnostics** > **Paramètres de diagnostic**.

   ![Accéder à Surveillance, Diagnostics, Paramètres de diagnostic](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Sous **Paramètres de diagnostic**, choisissez **Activé**.

   ![Activer les journaux de diagnostic](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Sélectionnez à présent l’espace de travail OMS et la catégorie d'événement pour la journalisation comme illustré :

   1. Sélectionnez **Envoyer à Log Analytics**. 
   2. Sous **Log Analytics**, choisissez **Configurer**. 
   3. Sous **Espaces de travail OMS**, sélectionnez l’espace de travail OMS à utiliser pour la journalisation.
   4. Sous **Journal**, sélectionnez la catégorie **WorkflowRuntime**.
   5. Choisissez l’intervalle de mesure.
   6. Une fois ces opérations effectuées, sélectionnez **Enregistrer**.

   ![Sélectionner l’espace de travail OMS et les données à journaliser](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

À présent, vous pouvez trouver des événements et d’autres données pour des événements déclencheurs, des événements d’exécution et des événements d’action.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Rechercher des événements et des données pour votre application logique

Pour rechercher et afficher dans votre application logique des événements tels que des événements de déclencheur, des événements d’exécution et des événements d’action, procédez comme suit.

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Tous les services**. Recherchez « log analytics », puis choisissez **Log Analytics** comme illustré ici :

   ![Choisir « Log Analytics »](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail OMS. 

   ![Sélectionner votre espace de travail OMS](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Sous **Gestion**, choisissez **Portail OMS**.

   ![Choisir « Portail OMS »](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. Dans votre page d’accueil d’OMS, choisissez **Recherche dans les journaux**.

   ![Dans votre page d’accueil d’OMS, choisissez « Recherche dans les journaux »](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -ou-

   ![Dans le menu OMS, choisir « Recherche dans les journaux »](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. Dans la zone de recherche, spécifiez un champ que vous souhaitez trouver, puis appuyez sur **Entrée**. Lorsque vous commencez à taper, OMS affiche les correspondances possibles et les opérations que vous pouvez utiliser. 

   Par exemple, pour rechercher les 10 principaux événements qui se sont produits, entrez et sélectionnez la requête de recherche suivante : **search Category == WorkflowRuntime | limit 10**

   ![Entrer une chaîne de recherche](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Pour en savoir plus, voir [Recherche de données à l’aide de recherches de journal](../log-analytics/log-analytics-log-searches.md).

6. Dans la page de résultats, dans la barre de gauche, choisissez la plage de temps que vous souhaitez afficher.
Pour affiner votre requête en ajoutant un filtre, choisissez **+Ajouter**.

   ![Choisir un plage de temps pour les résultats de la requête](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. Sous **Ajouter des filtres**, entrez le nom du filtre afin de trouver le filtre souhaité. Sélectionnez le filtre, puis choisissez **+Ajouter**.

   Cet exemple utilise le mot « status » pour rechercher les échecs d’événements sous **AzureDiagnostics**.
   Ici, le filtre pour **status_s** est déjà sélectionné.

   ![Sélectionner le filtre](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. Dans la barre de gauche, sélectionnez la valeur de filtre que vous souhaitez utiliser, puis choisissez **Appliquer**.

   ![Sélectionner la valeur de filtre, choisir « Appliquer »](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Revenez à présent à la requête que vous créez. Votre requête est mis à jour avec le filtre et la valeur sélectionnés. Vos résultats précédents sont à présent également filtrés.

   ![Revenir à votre requête avec les résultats filtrés](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Pour enregistrer votre requête en vue d’une utilisation ultérieure, choisissez **Enregistrer**. Découvrez comment [Enregistrer votre requête](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Étendre la manière dont vous utilisez les données de diagnostic et l’emplacement où vous les utilisez avec d’autres services

Avec Azure Log Analytics, vous pouvez étendre le mode d’utilisation des données de diagnostic de votre application logique avec d’autres services Azure, par exemple : 

* [Archivage des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Diffuser en continu les journaux de diagnostic vers Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Vous pouvez ensuite obtenir une surveillance en temps réel en utilisant les ressources de télémétrie et d’analyse d’autres services, tels que [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) et [Power BI](../log-analytics/log-analytics-powerbi.md). Par exemple : 

* [Diffuser les données d’Event Hubs vers Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyser les données de diffusion avec Stream Analytics et créer un tableau de bord analytique en temps réel dans Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Selon les options que vous souhaitez configurer, veillez au préalable à [créer un compte de stockage Azure](../storage/common/storage-create-storage-account.md) ou à [créer un hub d’événements Azure](../event-hubs/event-hubs-create.md). Sélectionnez ensuite les options concernant l’emplacement où vous souhaitez envoyer les données de diagnostic :

![Envoyer des données à un compte de stockage ou à un hub d’événements Azure](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Les périodes de rétention s’appliquent uniquement lorsque vous choisissez d’utiliser un compte de stockage.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configurer des alertes pour votre application logique

Pour surveiller des métriques spécifiques ou des dépassements de seuils de votre application logique, configurez des [alertes dans Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). En savoir plus sur les [métriques dans Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Pour configurer des alertes sans [Azure Log Analytics](../log-analytics/log-analytics-overview.md), procédez comme suit. Pour des critères et actions d’alertes plus avancés, [configurez également Log Analytics](#azure-diagnostics).

1. Dans le menu du panneau de l’application logique, sous **Surveillance**, choisissez **Diagnostics** > **Règles d’alerte** > **ajouter une alerte** comme illustré ici :

   ![Ajouter une alerte pour votre application logique](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Dans le panneau **Ajouter une règle d’alerte**, créer votre alerte comme illustré :

   1. Sous **Ressource**, sélectionnez votre application logique si ce n’est déjà fait. 
   2. Donnez un nom et une description à votre alerte.
   3. Sélectionnez une **Métrique** ou l’événement que vous souhaitez suivre.
   4. Sélectionnez une **Condition**, spécifiez un **Seuil** pour la métrique, puis sélectionnez la **Période** de surveillance de cette mesure.
   5. Spécifiez s’il faut envoyer un e-mail pour l’alerte. 
   6. Spécifiez d’autres adresses e-mail éventuelles pour l’envoi de l’alerte. 
   Vous pouvez également spécifier une URL de webhook à laquelle envoyer l’alerte.

   Par exemple, cette règle envoie une alerte quand cinq exécutions ou plus échouent par heure :

   ![Créer une règle d’alerte de métrique](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Pour exécuter une application logique à partir d’une alerte, vous pouvez inclure le [déclencheur de demande](../connectors/connectors-native-reqres.md) dans votre flux de travail, ce qui vous permet d’effectuer des tâches telles que les exemples suivants :
> 
> * [Publier sur Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Envoyer un texte](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Ajouter un message à une file d'attente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Paramètres et détails d’événements Azure Diagnostics

Chaque événement de diagnostic comprend des détails sur votre application logique et l’événement, par exemple, l’état, l’heure de début, l’heure de fin, etc. Pour configurer par programmation la surveillance, le suivi et la journalisation, vous pouvez utiliser ces informations avec l’[API REST pour Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) et l’[API REST pour Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Par exemple, l’événement `ActionCompleted` a les propriétés `clientTrackingId` et `trackedProperties` que vous pouvez utiliser pour le suivi et la surveillance :

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId` : si cet ID n’est pas fourni, Azure le génère automatiquement et met en corrélation les événements de l’exécution d’une application logique, y compris les flux de travail imbriqués appelés par l’application logique. Vous pouvez spécifier manuellement cet ID à partir d’un déclencheur en passant un en-tête `x-ms-client-tracking-id` avec votre valeur d’ID personnalisé dans la demande de déclencheur. Vous pouvez utiliser un déclencheur de demande, un déclencheur HTTP ou un déclencheur de webhook.

* `trackedProperties` : pour effectuer le suivi des entrées ou sorties dans les données de diagnostic, vous pouvez ajouter des propriétés suivies à des actions dans la définition JSON de votre application logique. Les propriétés suivies ne peuvent suivre les entrées ou sorties que d’une action unique, mais vous pouvez utiliser les propriétés `correlation` d’événements pour mettre en corrélation les actions d’une exécution.

  Pour effectuer le suivi d’une ou plusieurs propriétés, ajoutez la section `trackedProperties` et les propriétés souhaitées à la définition de l’action. Par exemple, supposons que vous souhaitez effectuer le suivi de données telles qu’un « ID de commande » dans votre télémétrie :

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>Étapes suivantes

* [Création de modèles pour le déploiement d’applications logiques et la gestion des versions](../logic-apps/logic-apps-create-deploy-template.md)
* [Scénarios B2B et communication avec Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Surveiller les messages B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
