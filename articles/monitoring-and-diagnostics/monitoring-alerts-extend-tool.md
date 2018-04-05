---
title: Lancer l’extension des alertes d’OMS à Azure | Microsoft Docs
description: Outils et API d’extension des alertes d’OMS à Azure Alerts que les clients peuvent utiliser volontairement.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 76b7481223566f16a5da8c08d9d76f2bdb6b542a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="initiate-extending-alerts-from-oms-into-azure"></a>Lancer l’extension des alertes d’OMS à Azure
À compter du **23 avril 2018**, les alertes configurées dans [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) seront étendues à Azure pour tous les clients les utilisant. Les alertes étendues à Azure se comportent de la même façon que dans OMS. Les fonctionnalités de surveillance restent intactes. Étendre les alertes créées dans OMS à Azure offre de nombreux avantages. Pour plus d’informations sur les avantages et la procédure d’extension des alertes d’OMS à Azure, consultez la page [Étendre les alertes d’OMS à Azure](monitoring-alerts-extend.md).

Les clients qui souhaitent déplacer immédiatement leurs alertes d’OMS vers Azure peuvent le faire à l’aide de l’une des options indiquées.

## <a name="option-1---using-oms-portal"></a>Option 1 : dans le portail OMS
Pour lancer volontairement l’extension des alertes d’OMS à Azure, suivez les étapes ci-dessous.

1. Dans la page de présentation d’Operations Management Suite (OMS), accédez aux Paramètres, puis à la section Alertes. Cliquez sur le bouton « Étendre à Azure », comme le montre l’illustration ci-dessous.

    ![Page Paramètres d’alerte d’OMS avec l’option d’extension](./media/monitor-alerts-extend/ExtendInto.png)

2. Une fois le bouton sélectionné, un Assistant en 3 étapes s’affiche. La première étape détaille le processus. Cliquez sur Suivant pour continuer.

    ![Étendre les alertes d’OMS à Azure : étape 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. Dans la deuxième étape, le système affiche un résumé de la modification proposée, en répertoriant les [Groupes d’actions](monitoring-action-groups.md) appropriés pour les alertes d’OMS. Si des actions similaires concernent plusieurs alertes, le système propose de toutes les associer dans un groupe d’action unique.  Les groupes d’actions proposés suivent cette convention d’affectation de noms : *Nom_espace_travail_GA_#Numéro*. Pour continuer, cliquez sur Suivant.
Voici un exemple d’écran.

    ![Étendre les alertes d’OMS à Azure : étape 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. Dans la dernière étape de l’Assistant, vous pouvez demander à OMS d’étendre toutes vos alertes à Azure en créant des groupes d’actions et en les associant avec des alertes, comme illustré dans l’écran précédent. Pour continuer, cliquez sur Terminer et à l’invite, confirmez le lancement du processus. S’ils le souhaitent, les clients peuvent également fournir des adresses e-mail auxquelles ils aimeraient qu’OMS leur envoie un rapport sur l’exécution du processus.

    ![Étendre les alertes d’OMS à Azure : étape 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Une fois l’Assistant terminé, vous revenez à la page Paramètres d’alerte où l’option « Étendre à Azure » a disparu. En arrière-plan, OMS planifie l’extension de ses alertes à Azure. Cela peut prendre un certain temps. Au début de l’opération et pendant une courte période, les alertes d’OMS ne peuvent pas être modifiées. L’état actuel s’affiche dans une bannière. Si des adresses e-mail ont été fournies à l’étape 4, les clients sont informés lorsque le processus en arrière-plan a terminé l’extension de toutes les alertes à Azure. 

6. Les alertes sont toujours répertoriées dans OMS, même après leur extension à Azure.

    ![Après l’extension des alertes d’OMS à Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Option 2 : à l’aide d’une API
Pour les clients qui souhaitent contrôler par programme ou automatiser le processus d’extension des alertes d’OMS à Azure, Microsoft propose une nouvelle API AlertsVersion sous Log Analytics.

L’API AlertsVersion Log Analytics est un service RESTful qui est accessible par le biais de l’API REST Azure Resource Manager. Ce document présente des exemples montrant comment accéder à l’API à partir d’une ligne de commande PowerShell en utilisant [ARMClient](https://github.com/projectkudu/ARMClient), outil en ligne de commande open source qui simplifie l’appel de l’API Azure Resource Manager. L’utilisation d’ARMClient et de PowerShell est une des nombreuses options vous permettant d’accéder à l’API. L'API produit des résultats au format JSON, qui vous permet d'utiliser ces résultats, par programme, de différentes manières.

En utilisant GET sur l’API, il est possible d’obtenir comme résultat le résumé de la modification proposée,sous forme de liste de [Groupes d’actions](monitoring-action-groups.md) appropriés pour les alertes d’OMS, au format JSON. Si des actions similaires concernent plusieurs alertes, le système propose de toutes les associer dans un groupe d’action unique.  Les groupes d’actions proposés suivent cette convention d’affectation de noms : *Nom_espace_travail_GA_#Numéro*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> L’appel GET à l’API n’entraîne pas l’extension des alertes d’OMS à Azure. Il fournit simplement en réponse le résumé des modifications proposées. Pour confirmer ces modifications et étendre les alertes à Azure, un appel POST doit être passé à l’API.

Si l’appel GET à l’API réussit et qu’il reçoit la réponse 200 OK, une liste JSON d’alertes et des propositions de groupes d’actions s’affichent. Voici un exemple de réponse :

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Dans ce cas, il n’y a aucune alerte dans l’espace de travail spécifié, et pas de réponse 200 OK pour l’opération GET, la sortie JSON serait la suivante :

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Si toutes les alertes de l’espace de travail spécifié ont déjà été étendues à Azure, la réponse à l’appel GET serait la suivante :
```json
{
    "version": 2
}
```

Pour lancer la planification de l’extension des alertes d’OMS à Azure, lancez un appel POST sur l’API. Cet appel/cette commande confirme l’intention de l’utilisateur d’étendre les alertes d’OMS à Azure et son approbation, et effectue les modifications indiquées dans la réponse de l’appel GET à l’API. S’il le souhaite, l’utilisateur peut fournir une liste d’adresses e-mail auxquelles OMS enverra un rapport à la fin de l’exécution planifiée du processus en arrière-plan d’extension des alertes d’OMS à Azure.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Les résultats de l’extension des alertes d’OMS à Azure peuvent être différents du résumé fourni en réponse à l’appel GET en cas de modification apportée dans le système. Une fois l’extension planifiée, les alertes d’OMS ne pourront plus être modifiées temporairement, pendant la création des nouvelles alertes. 

Si l’appel POST réussit, il doit retourner la réponse 200 OK, avec :
```json
{
    "version": 2
}
```
Ce qui indique que les alertes ont été étendues à Azure, comme indiqué par la version 2. Cette version sert uniquement à vérifier que les alertes ont bien été étendues à Azure et que cela n’a aucune incidence sur l’utilisation avec l’[API de recherche Log Analytics](../log-analytics/log-analytics-api-alerts.md). Une fois les alertes bien étendues à Azure, tous les utilisateurs ayant des rôles d’administrateur et de contributeur dans l’espace de travail reçoivent un e-mail contenant les détails des modifications apportées.


Enfin, si l’extension à Azure de toutes les alertes de l’espace de travail spécifié a déjà été planifiée, la réponse à l’appel POST sera 403 Forbidden.


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la nouvelle [expérience Azure Alerts](monitoring-overview-unified-alerts.md).
* En savoir plus sur les [alertes de journal dans Azure Alerts](monitor-alerts-unified-log.md).
