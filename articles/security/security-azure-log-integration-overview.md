---
title: Intégrer des journaux à partir de ressources Azure à vos systèmes SIEM | Microsoft Docs
description: Découvrez Azure Log Integration, ses fonctionnalités principales et son fonctionnement.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Présentation d’Azure Log Integration

Vous pouvez utiliser Azure Log Integration pour intégrer des journaux bruts de vos ressources Azure à vos systèmes SIEM (Security Information and Event Management) locaux. Utilisez Azure Log Integration uniquement s’il n’y a pas de connecteur pour [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) disponible depuis votre fournisseur SIEM.

La méthode recommandée pour intégrer les journaux Azure consiste à utiliser le connecteur Azure Monitor de votre fournisseur SIEM. Pour utiliser le connecteur, suivez les instructions données dans [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). 

Toutefois, si votre fournisseur SIEM ne propose pas de connecteur pour Azure Monitor, vous pouvez utiliser Azure Log Integration de façon temporaire jusqu’à ce qu’un tel connecteur soit disponible. Azure Log Integration est une option uniquement si Azure Log Integration prend en charge votre SIEM.

> [!IMPORTANT]
> Si vous êtes principalement intéressé par la collecte des journaux de machine virtuelle, la plupart des fournisseurs SIEM intègrent cette option à leur solution. L’utilisation du connecteur du fournisseur SIEM doit toujours être l’alternative privilégiée.

Azure Log Integration collecte les événements Windows à partir des journaux de l’Observateur d’événements, des [journaux d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), des [alertes Azure Security Center](../security-center/security-center-intro.md) et des [journaux Azure Diagnostics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) des ressources Azure. Azure Log Integration permet à votre solution SIEM d’offrir un tableau de bord unifié pour toutes vos ressources, qu’elles soient locales ou dans le cloud. Vous pouvez utiliser un tableau de bord pour recevoir, agréger, mettre en corrélation et analyser des alertes pour des événements de sécurité.

> [!NOTE]
> Actuellement, Azure Log Integration prend uniquement en charge les clouds Azure Government et commerciaux Azure. Les autres clouds ne sont pas pris en charge.

![Schéma illustrant le processus d’Azure Log Integration][1]

## <a name="what-logs-can-i-integrate"></a>Journaux pouvant être intégrés

Azure génère une journalisation complète pour chaque service Azure. Les journaux sont de trois types :

* **Journaux de contrôle/gestion** : permettent de consulter les opérations CREATE, UPDATE et DELETE [d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Un journal d’activité Azure est un exemple de ce type de journal.
* **Journaux de plan de données** : permettent de visualiser les événements qui sont déclenchés lorsque vous utilisez une ressource Azure. Les chaînes **Système**, **Sécurité** et **Application** de l’Observateur d’événements Windows sont des exemples de ce type de journal, dans une machine virtuelle Windows. La journalisation Azure Diagnostics, configurée via Azure Monitor, en est un autre exemple.
* **Événements traités** : fournissent des informations sur les alertes et les événements traités en votre nom. Les alertes Azure Security Center sont un exemple de ce type d’événement. Azure Security Center traite et analyse votre abonnement, afin de proposer des alertes pertinentes pour votre niveau de sécurité actuel.

Azure Log Integration prend en charge ArcSight, QRadar et Splunk. Renseignez-vous auprès de votre fournisseur SIEM pour déterminer s’il dispose d’un connecteur natif. N’utilisez pas Azure Log Integration si un connecteur natif est disponible.

Si aucune autre option n’est disponible, envisagez d’utiliser Azure Log Integration. Le tableau ci-après inclut nos recommandations :

|SIEM | Le client utilise déjà un intégrateur de journaux Azure | Le client examine les options d’intégration SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Commencez à effectuer une migration vers le [module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/). | Utilisez le [connecteur Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Effectuez une migration vers le connecteur QRadar documenté dans la dernière section de [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) ou commencez à l’utiliser. | Utilisez le connecteur QRadar documenté dans la dernière section de [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Continuez à utiliser l’intégrateur de journaux Azure jusqu’à ce qu’un connecteur soit disponible. Ensuite, effectuez la migration vers la solution basée sur le connecteur.  | Envisagez d’utiliser Azure Log Analytics en guise de solution de rechange. Ne recourez à Azure Log Integration que si vous êtes disposé à exécuter le processus de migration une fois que le connecteur deviendra disponible. |

> [!NOTE]
> Bien qu’Azure Log Integration soit une solution gratuite, le stockage des informations des fichiers de journaux est facturé, via Stockage Azure.

Si vous avez besoin d’une assistance, vous pouvez ouvrir une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour le service, sélectionnez **Intégration du journal**.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous présente Azure Log Integration. Pour plus d’informations sur Azure Log Integration et les types de journaux pris en charge, consultez les articles suivants :

* [Intégration des journaux Azure avec Azure Diagnostics Logging et Windows Event Forwarding](security-azure-log-integration-get-started.md). Ce didacticiel vous guide tout au long de l’installation d’Azure Log Integration. Il décrit également comment intégrer des journaux du stockage Windows Azure Diagnostics, des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure Active Directory.
* [Forum aux questions sur l’intégration des journaux Azure](security-azure-log-integration-faq.md). Ce FAQ répond aux questions courantes sur Azure Log Integration.
* Découvrez comment [diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
