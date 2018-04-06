---
title: Créer des alertes pour les services Azure - Portail Azure | Microsoft Docs
description: Déclenchez des e-mails et des notifications, appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions spécifiées sont remplies.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.openlocfilehash: 4acf1f549a6c901fb0b772c4591f1f35d61365ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Créer des alertes de métriques classiques dans Azure Monitor pour les services Azure - Portail Azure
> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Vue d'ensemble

> [!NOTE]
> Cet article décrit comment créer des alertes de métriques plus anciennes. Désormais, Azure Monitor prend en charge des alertes de métriques plus récentes et plus performantes. Ces alertes peuvent surveiller plusieurs métriques et permettent d’obtenir des alertes sur des métriques dimensionnelles. Découvrez plus en détail les [alertes de métriques en quasi temps réel](monitoring-near-real-time-metric-alerts.md).
>
>

Cet article vous montre comment configurer des alertes de métrique Azure avec le portail Azure. 

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.    
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou seulement quand un événement particulier se produit. Découvrez plus en détail les [alertes du journal d’activité](monitoring-activity-log-alerts.md).

Vous pouvez configurer une alerte de métrique pour effectuer les opérations suivantes lors de son déclenchement :

* envoyer des notifications par courrier électronique à l’administrateur du service et aux coadministrateurs
* envoyer un courrier électronique à d’autres adresses que vous spécifiez.
* appeler un webhook
* démarrer l’exécution d’un runbook Azure (uniquement à partir du Portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte de métrique via

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une métrique avec le Portail Azure
1. Sur le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller et sélectionnez-la.

2. Sélectionnez **Alertes (classiques)** dans la section SURVEILLANCE. Le texte et l’icône peuvent varier légèrement pour les différentes ressources. Si vous ne trouvez pas **Alertes (classiques)** à cet endroit, vous les trouverez peut-être sous **Alertes** ou **Règles d’alerte**

    ![Surveillance](./media/insights-alerts-portal/AlertRulesButton.png)

3. Sélectionnez la commande **Ajouter une alerte de métrique** et renseignez les champs.

    ![Ajouter une alerte](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nommez** votre règle d’alerte, puis choisissez une **Description** qui indique également les adresses électroniques de notification.

5. Sélectionnez la **Métrique** que vous souhaitez surveiller, puis choisissez une **Condition** et une valeur de **Seuil** pour la métrique. Choisissez également la **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche. Par exemple, si vous utilisez la période « Au cours des 5 dernières minutes » et que votre alerte recherche une utilisation de l’UC supérieure à 80 %, l’alerte se déclenche quand l’utilisation de l’UC dépasse 80 % depuis cinq minutes. Après le premier déclenchement, elle se déclenche à nouveau lorsque l’UC reste au-dessous de 80 % pendant cinq minutes. La mesure de la métrique de l’UC est effectuée toutes les minutes.

6. Cochez **Propriétaires de messagerie...** si vous souhaitez que les administrateurs et les coadministrateurs reçoivent un courrier électronique lorsque l’alerte se déclenche.

7. Si vous souhaitez que d’autres adresses électroniques reçoivent une notification lorsque l’alerte se déclenche, ajoutez-les dans le champ **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)** . Séparez les adresses e-mails par des points-virgules : *email@contoso.com;email2@contoso.com*

8. Insérez un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.

9. Si vous utilisez Azure Automation, vous pouvez sélectionner un Runbook à exécuter lorsque l’alerte se déclenche.

10. Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.   

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent.
* La modifier ou la supprimer.
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre l’envoi de notifications pour cette alerte.

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md) , notamment les types d’informations que vous pouvez collecter et surveiller.
* En savoir plus sur les [nouvelles alertes de métriques](monitoring-near-real-time-metric-alerts.md)
* Découvrez plus en détail la [configuration des webhooks dans les alertes](insights-webhooks-alerts.md).
* Découvrez plus d’informations sur la [configuration des alertes sur les événements de journal d’activité](monitoring-activity-log-alerts.md).
* Découvrez plus en détails les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) et collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
