---
title: "Exportation de données Azure Security vers SIEM - Configuration du pipeline [Préversion] | Microsoft Docs"
description: "Cet article décrit comment envoyer des journaux Azure Security Center vers SIEM"
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: barclayn
ms.openlocfilehash: aef623f047bd7e14cb5bd17fb2a2c18e3c5d42b9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Exportation de données Azure Security vers SIEM - Configuration du pipeline [Préversion]

Ce document décrit en détail la procédure d’exportation de données de sécurité Azure Security Center vers un système SIEM.

Les événements traités produits par Azure Security Center sont publiés dans le [journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Azure, l’un des types de journaux disponibles avec Azure Monitor. Azure Monitor offre un pipeline centralisé pour router les données de monitoring dans un outil SIEM. Ces données sont acheminées vers un hub d’événements, d’où elles peuvent ensuite être extraites dans un outil partenaire.

Pour ce faire, est utilisé le [seul pipeline de monitoring Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) permettant d’accéder aux données de monitoring à partir de votre environnement Azure. Cela vous permet de configurer facilement des systèmes SIEM et des outils de monitoring pour consommer les données.

Les sections suivantes expliquent comment configurer les données à diffuser vers un hub d’événements. Les étapes partent du principe qu’Azure Security Center est déjà configuré dans votre abonnement Azure.

Vue d’ensemble globale

![Vue d’ensemble globale](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quelles sont les données de sécurité Azure exposées à SIEM ?

Dans cette préversion, nous exposons les [alertes de sécurité](../security-center/security-center-managing-and-responding-alerts.md). Dans les versions à venir, nous enrichirons le jeu de données avec des recommandations de sécurité.

## <a name="how-to-setup-the-pipeline"></a>Comment configurer le pipeline ? 

### <a name="create-an-event-hub"></a>Créer un hub d’événements 

Avant de commencer, vous devez [créer un espace de noms Event Hubs](../event-hubs/event-hubs-create.md). Cet espace de noms et cet hub d’événements sont la destination de toutes vos données de monitoring.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Acheminer le journal des activités Azure sur Event Hubs

Consultez l’article [Acheminer le journal des activités Azure vers Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).

### <a name="install-a-partner-siem-connector"></a>Installer un connecteur SIEM partenaire 

Le routage de vos données de monitoring vers un hub d’événement avec Azure Monitor vous permet d’intégrer facilement des systèmes SIEM et des outils de monitoring partenaires.

Consultez le lien suivant pour afficher la liste des [systèmes SIEM pris en charge](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

## <a name="example-for-querying-data"></a>Exemple d’interrogation de données 

Voici quelques exemples de requêtes Splunk que vous pouvez utiliser pour extraire des données d’alerte :

| **Description de la requête**                                | **Requête**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les alertes                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Résumer le nombre d’opérations par leur nom             | **Alerts** index=main sourcetype="amal:security" \| table operationName \| stats count by operationName                                |
| Obtenir des informations d’alertes : heure, nom, état, ID et abonnement | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Étapes suivantes

- [Systèmes SIEM pris en charge](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Acheminer le journal d’activité vers Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Alertes de sécurité](../security-center/security-center-managing-and-responding-alerts.md)