---
title: "Intégrer des journaux à partir de ressources Azure dans vos systèmes SIEM | Microsoft Docs"
description: "Découvrez l’intégration des journaux Azure, ses fonctionnalités principales et son fonctionnement."
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
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Présentation de l’intégration des journaux Microsoft Azure

Azure Log Integration, le service d’intégration des journaux Azure, vous permet d’intégrer les journaux bruts de vos ressources Azure à vos systèmes SIEM (Security Information and Event Management) locaux dans les cas où un connecteur pour [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) n’est pas encore disponible auprès de votre fournisseur SIEM.

La méthode recommandée en matière d’intégration des journaux Azure consiste à utiliser le connecteur Azure Monitor de votre fournisseur SIEM et à suivre les [instructions](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) ci-après. Toutefois, si votre fournisseur SIEM ne propose pas de connecteur pour Azure Monitor, vous pouvez utiliser le service Azure Log Integration de façon temporaire (s’il prend en charge votre système SIEM) jusqu’à ce qu’un tel connecteur soit disponible.

>[!IMPORTANT]
>Si vous êtes principalement intéressé par la collecte des journaux des machines virtuelles, la plupart des fournisseurs SIEM intègrent cette fonctionnalité à leur solution. L’utilisation du connecteur du fournisseur SIEM doit toujours constituer la solution privilégiée.

Le service Azure Log Integration collecte les événements Windows à partir des journaux de l’Observateur d’événements, des [journaux d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), des [alertes Azure Security Center](../security-center/security-center-intro.md) et des [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) des ressources Azure. Cette intégration aide votre solution SIEM à offrir un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.

>[!NOTE]
À l’heure actuelle, seuls les clouds Azure Commercial et Azure Government sont pris en charge. Les autres clouds ne sont pas pris en charge.

![Intégration des journaux Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?

Azure génère une journalisation complète pour chaque service Azure. Ces journaux représentent trois types de journaux :

* **Journaux de contrôle / gestion** permet de consulter les opérations CREATE, UPDATE et DELETE d’[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Les journaux d’activité Azure sont un exemple de ce type de journal.
* **Journaux des plans de données** permet de consulter les événements déclenchés lors de l’utilisation d’une ressource Azure. Les chaînes **Système**, **Sécurité** et **Application** de l’Observateur d’événements Windows sont des exemples de ce type de journal, dans une machine virtuelle Windows. Les journaux de diagnostics, configurés via Azure Monitor, en sont un autre exemple.
* Les **Événements traités** fournissent des informations sur les alertes et les événements analysés en votre nom. Les alertes Azure Security Center sont un exemple de ce type d’événement. Azure Security Center traite et analyse votre abonnement pour vous envoyer des alertes utiles pour votre dispositif de sécurité en cours.

Azure Log Integration prend en charge ArcSight, QRadar et Splunk. Dans tous les cas, renseignez-vous auprès de votre fournisseur SIEM pour déterminer s’il dispose d’un connecteur natif. Vous ne devez pas utiliser Azure Log Integration lorsque des connecteurs natifs sont disponibles.

Si aucune autre option n’est utilisable, vous pouvez envisager de recourir à Azure Log Integration. Le tableau ci-après inclut nos recommandations.

|**SIEM** | **Client utilisant déjà un intégrateur de journaux** | **Client examinant les options d’intégration aux systèmes SIEM**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | Commencez à effectuer une migration vers le [module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/). | Utilisez le [connecteur SPLUNK](https://splunkbase.splunk.com/app/3534/). |
|**IBM QRADAR** | Passez ou recourez au connecteur QRadar documenté à la fin de la page http://aka.ms/azmoneventhub. | Utilisez le connecteur QRadar documenté à la fin de la page http://aka.ms/azmoneventhub.  |
|**ARCSIGHT** | Continuez à utiliser l’intégrateur de journaux jusqu’à ce qu’un connecteur soit disponible, puis effectuez la migration vers la solution basée sur le connecteur.  | Envisagez d’utiliser Azure Log Analytics en guise de solution de rechange. Ne recourez à Azure Log Integration que si vous êtes disposé à exécuter le processus de migration une fois que le connecteur deviendra disponible. |

>[!NOTE]
>Bien que l’intégration des journaux Azure soit une solution gratuite, le stockage des informations des fichiers de journaux est facturé, via Stockage Azure.

Si vous avez besoin d’une assistance, vous pouvez ouvrir une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour ce faire, sélectionnez **intégration du journal** comme service pour lequel vous demandez de l’aide.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a présenté une vue d’ensemble de l’intégration des journaux Azure. Pour plus d’informations sur l’intégration des journaux Azure et les types de journaux pris en charge, consultez les rubriques suivantes :

* [Prise en main de l’intégration des journaux Azure](security-azure-log-integration-get-started.md) : ce didacticiel vous guide dans la procédure d’installation de l’intégration des journaux Azure et d’intégration des journaux du stockage Azure WAD, des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure Active Directory.
* [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
* [Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
