---
title: Étendre les alertes d’OMS à Azure - Vue d’ensemble | Microsoft Docs
description: Vue d’ensemble du processus d’extension des alertes d’OMS à Azure Alerts, détails relatifs aux problèmes courants des clients.
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
ms.openlocfilehash: 045a7f97d9c4d380e83325c04c209a6afcc761a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="extend-alerts-from-oms-into-azure"></a>Étendre les alertes d’OMS à Azure
La nouvelle expérience d’alertes intègre désormais l’expérience d’alerte entre les différents services et parties dans Microsoft Azure. La nouvelle expérience, disponible sous forme d’**alertes** sous Azure Monitor dans le portail, réunit dans un même emplacement les alertes de journal d’activité, les alertes de métriques et les alertes de journal sur Log Analytics ainsi qu’Application Insights. 

Pour certains utilisateurs toutefois, l’utilisation de Log Analytics et de fonctionnalités associées comme les alertes, se fait via [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Par conséquent, pour leur permettre de gérer facilement leurs autres ressources Azure tout en utilisant Log Analytics, Microsoft garantit systématiquement que les fonctionnalités d’OMS sont également disponibles dans le portail Azure. Azure Alerts permet déjà ainsi aux utilisateurs de gérer les alertes basées sur une requête pour Log Analytics. Pour plus d’informations, consultez les [alertes de journal sur Azure Alerts](monitor-alerts-unified-log.md). Sous Azure Monitor, dans Alerts, les alertes créées dans OMS sont déjà répertoriés sous l’espace de travail Log Analytics approprié. Toutefois, toute modification ou changement apporté aux alertes créées dans OMS oblige l’utilisateur à quitter Azure et à utiliser OMS, puis à revenir dans Azure s’il doit gérer un autre service. Pour remédier à cela, Microsoft permet désormais aux utilisateurs d’étendre leurs alertes d’OMS à Azure.

## <a name="benefits-of-extending-your-alerts"></a>Avantages de l’extension de vos alertes
Outre le fait de ne pas devoir sortir du portail Azure, l’extension des alertes d’OMS à Azure présente d’autres avantages marquants

- Contrairement à OMS, où seules 250 alertes peuvent être créées et affichées, cette limitation ne s’applique pas aux alertes Azure
- Dans les alertes Azure, tous vos types d’alertes peuvent être gérés, répertoriés et affichés, et pas seulement les alertes Log Analytics comme c’est le cas avec OMS
- Les alertes Azure utilisent des [groupes d’actions](monitoring-action-groups.md) qui vous permettent de disposer pour chaque alerte de plusieurs actions, notamment les SMS, appels vocaux, le runbook Automation, Webhook, connecteur ITSM et d’autres encore. Dans OMS, les alertes sont limitées tant en nombre qu’en type d’actions possibles

## <a name="process-of-extending-your-alerts"></a>Processus d’extension de vos alertes
Le processus d’extension des alertes d’OMS à Azure, n’implique **pas** une modification, de quelque nature que ce soit, de votre définition d’alerte, requête ou configuration. Le seul changement nécessaire est tel que, dans Azure, toutes les actions comme les notifications par courrier électronique, l’appel Webhook, le runbook Automation ou la connexion à l’outil ITSM, se font via le groupe d’actions. Par conséquent, si le groupe d’actions approprié est associé à votre alerte, il sera étendu à Azure.

Le processus d’extension étant non destructif et sans interruption, Microsoft étendra automatiquement les alertes créées dans Operations Management Suite (OMS) aux alertes Azure à compter du **23 avril 2018**. À partir de cette date, Microsoft commencera à planifier l’extension des alertes à Azure et mettra progressivement à disposition toutes les alertes dans OMS pour qu’elles soient gérées à partir du portail Azure. 

Lorsque des alertes dans un espace de travail OMS sont planifiées pour être étendues à Azure, elles continuent à fonctionner et n’affectent **en aucun cas** votre capacité de surveillance. Lorsqu’elles sont planifiées, vos alertes peuvent ne pas être disponibles temporairement pour modification/édition ; de nouvelles alertes Azure peuvent toutefois être créées pendant cette courte période. Pendant cette courte période, si vous modifiez ou créez une alerte dans OMS, vous pourrez continuer dans Azure Log Analytics ou Azure Alerts.

 ![Pendant la période planifiée, une action de l’utilisateur sur des alertes est redirigée vers Azure](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> L’extension des alertes d’Operations Management Suite (OMS) à Azure est gratuite et l’utilisation d’alertes Azure pour des alertes Log Analytics basées sur une requête ne sera pas facturée dans les limites et les conditions stipulées dans la [stratégie de tarification d’Azure Monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Les utilisateurs peuvent tirer profit des avantages de l’extension des alertes avant cette date en optant volontairement pour la gestion de leurs alertes dans Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Comment étendre volontairement vos alertes
Pour permettre aux utilisateurs d’OMS de passer en toute simplicité aux alertes Azure, Microsoft a créé des outils permettant d’étendre les alertes. Les clients Microsoft Operations Management Suite (OMS) peuvent étendre leurs alertes à Azure à partir d’un Assistant dans le portail OMS (ou) selon une approche par programmation à l’aide d’une nouvelle API. Pour plus d’informations, consultez [Extending alerts into Azure using OMS portal and API](monitoring-alerts-extend-tool.md) (Étendre les alertes à Azure à l’aide du portail OMS et d’une API).


## <a name="usage-after-extending-your-alerts"></a>Utilisation après l’extension de vos alertes
Comme indiqué, les alertes créées dans Microsoft Operation Management Suite seront étendues à Azure Alerts. Vous pourrez alors les gérer à partir d’Azure. Les alertes continueront à être répertoriées dans le portail OMS, dans la section de paramétrage des alertes. En voici une illustration :

 ![Portail OMS répertoriant les alertes après une extension à Azure](./media/monitor-alerts-extend/PostExtendList.png)

Pour toute opération sur les alertes, comme la modification ou la création effectuée dans le portail OMS, les utilisateurs seront dirigés de manière transparente vers Azure Alerts. La création d’alertes se fera toujours via l’[API Log Analytics](../log-analytics/log-analytics-api-alerts.md) comme auparavant, la seule différence mineure étant qu’une fois les alertes étendues à Azure, les groupes d’actions doivent être inclus dans la planification.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les outils de [lancement de l’extension des alertes d’OMS à Azure](monitoring-alerts-extend-tool.md)
* En savoir plus sur la nouvelle [expérience d’alertes Azure](monitoring-overview-unified-alerts.md).
* En savoir plus sur les [alertes de journal dans Azure Alerts](monitor-alerts-unified-log.md).
