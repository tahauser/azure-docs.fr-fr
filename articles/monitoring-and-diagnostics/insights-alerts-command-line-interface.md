---
title: Créer des alertes pour les services Azure - Interface de ligne de commande multiplateforme | Microsoft Docs
description: Déclenchez des e-mails et des notifications, appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions spécifiées sont remplies.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: cf93c95a37c9c32333727059317e05cfcc252905
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Créer des alertes de métriques classiques dans Azure Monitor pour les services Azure - Interface de ligne de commande multiplateforme
> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Vue d'ensemble
> [!NOTE]
> Cet article décrit comment créer des alertes de métriques plus anciennes. Désormais, Azure Monitor prend en charge des [alertes de métriques plus récentes et plus performantes](monitoring-near-real-time-metric-alerts.md). Ces alertes peuvent surveiller plusieurs métriques, et permettent d’obtenir des alertes sur des métriques dimensionnelles. L’interface de ligne de commande sera bientôt prise en charge pour des alertes de métriques plus récentes.
>
>

Cet article montre comment configurer des alertes de métrique Azure avec l’interface de ligne de commande (CLI) multiplateforme.

> [!NOTE]
> Azure Monitor est le nouveau nom de ce qui était appelé « Azure Insights » jusqu’au 25 septembre 2016. Toutefois, les espaces de noms et, par conséquent, les commandes ci-dessous contiennent toujours « insights ».
>
>

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.    
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou seulement quand un certain événement se produit. Pour plus d’informations sur les alertes du journal d’activité, [cliquez ici](monitoring-activity-log-alerts.md)

Vous pouvez configurer une alerte de métrique pour effectuer les opérations suivantes lors de son déclenchement :

* envoyer des notifications par courrier électronique à l’administrateur du service et aux coadministrateurs
* envoyer un courrier électronique à d’autres adresses que vous spécifiez.
* appeler un webhook
* démarrer l’exécution d’un runbook Azure (uniquement à partir du Portail Azure pour l’instant)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte de métrique via

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Vous pouvez toujours obtenir une aide sur les commandes en tapant une commande et en ajoutant -help à la fin. Par exemple : 

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Créer des règles d’alerte à l’aide de l’interface CLI
1. Suivez les conditions préalables et connectez-vous à Azure. Consultez les [exemples d’interface de ligne de commande Azure Monitor](insights-cli-samples.md). En bref, installez l’interface CLI et exécutez ces commandes. Elles vous connectent, indiquent quel abonnement que vous utilisez et préparent l’exécution des commandes Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Pour répertorier les règles existantes dans un groupe de ressources, utilisez le format suivant **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Pour créer une règle, vous avez d’abord besoin de différentes informations importantes.
  * **L’ID de la ressource** pour laquelle vous souhaitez définir une alerte
  * Les **définitions de métriques** disponibles pour cette ressource

     Pour obtenir l’ID de la ressource, vous pouvez utiliser le Portail Azure. À supposer que la ressource soit déjà créée, sélectionnez-la dans le portail. Puis, dans le panneau suivant, sélectionnez *Propriétés* sous la section *Paramètres*. *L’ID DE RESSOURCE* est un champ du panneau suivant. Une autre méthode consiste à utiliser [l’Explorateur de ressources Azure](https://resources.azure.com/).

     Voici un exemple d’ID de ressource d’une application web

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Pour obtenir la liste des métriques disponibles et de leurs unités pour l’exemple de ressource précédent, utilisez la commande CLI suivante :  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* est la précision de la mesure disponible (intervalles d’une minute). L’utilisation de différentes précisions vous offre différentes options de métriques.
4. Pour créer une règle d’alerte basée sur une métrique, utilisez une commande de la forme suivante :

    **azure insights alerts rule metric set***[options] &lt;ruleName&gt;&lt;location&gt;&lt;resourceGroup&gt;&lt;windowSize&gt;&lt;operator&gt;&lt;threshold&gt;&lt;targetResourceId&gt;&lt;metricName&gt;&lt;timeAggregationOperator&gt;*

    L’exemple suivant configure une alerte sur une ressource de site web. L’alerte se déclenche chaque fois qu’il reçoit constamment du trafic pendant cinq minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant cinq minutes.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Pour créer un webhook ou envoyer un e-mail quand une alerte se déclenche, commencez par créer l’e-mail et/ou les webhooks. Puis créez la règle immédiatement après. Vous ne pouvez pas associer un webhook ou des messages électroniques à des règles déjà créées à l’aide de l’interface CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Vous pouvez vérifier que vos alertes ont été correctement créées en examinant une règle en particulier.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Pour supprimer des règles, utilisez une commande de la forme :

    **insights alerts rule delete** [options] &lt;resourceGroup&gt;&lt;ruleName&gt;

    Ces commandes suppriment les règles créées précédemment dans cet article.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md) , notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez plus en détail la [configuration des webhooks dans les alertes](insights-webhooks-alerts.md).
* Découvrez plus d’informations sur la [configuration des alertes sur les événements de journal d’activité](monitoring-activity-log-alerts.md).
* Découvrez plus en détails les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble de la collecte des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) pour collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
