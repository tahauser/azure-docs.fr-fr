---
title: Diffuser des données de surveillance Azure vers Event Hubs | Microsoft Docs
description: Découvrez comment diffuser toutes vos données de surveillance Azure vers un hub d’événements afin de les intégrer à un système SIEM ou à un outil d’analytique partenaire.
author: johnkemnetz
manager: robb
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/05/2018
ms.author: johnkem
ms.openlocfilehash: 1b1c50f106be8848fb1f32deefa6cb9acb7a298a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe

Azure Monitor fournit un pipeline pour accéder à l’ensemble des données de surveillance de votre environnement Azure. Vous pouvez ainsi configurer facilement des systèmes SIEM et des outils de surveillance partenaires de manière à ce qu’ils utilisent ces données. Cet article vous aide à configurer différentes couches à partir des données de votre environnement Azure, en vue de les envoyer vers un espace de noms ou un hub d’événements Event Hubs, où elles pourront être collectées par un outil externe.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Quelles données puis-je envoyer vers un hub d’événements ? 

Au sein de votre environnement Azure, il existe plusieurs « couches » de données de surveillance. La méthode pour y accéder varie légèrement selon les couches. En règle générale, ces couches peuvent être décrites ainsi :

- **Données de surveillance de l’application :** données concernant les performances et la fonctionnalité du code que vous avez écrit et qui est exécuté dans Azure. Il peut s’agir, par exemple, de traces de performances, de journaux d’applications ou de télémétrie utilisateur. Les données de surveillance de l’application sont généralement regroupées de l’une des manières suivantes :
  - En instrumentant votre code avec un SDK tel que le [SDK Application Insights](../application-insights/app-insights-overview.md)
  - En exécutant un agent de surveillance qui écoute les nouveaux journaux d’application sur la machine qui exécute votre application, tel que [l’agent de diagnostic Azure pour Windows](./azure-diagnostics.md) ou [l’agent de diagnostic Azure pour Linux](../virtual-machines/linux/diagnostic-extension.md)
- **Données de surveillance du système d’exploitation invité :** données concernant le système d’exploitation sur lequel votre application est exécutée. Il peut s’agir, par exemple, de journaux système Linux ou d’événements système Windows. Pour collecter ce type de données, vous devez installer un agent tel que [l’agent de diagnostic Azure pour Windows](./azure-diagnostics.md) ou [l’agent de diagnostic Azure pour Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Données de surveillance des ressources Azure :** données concernant le fonctionnement d’une ressource Azure. Pour certains types de ressources Azure, telles que les machines virtuelles, il existe un système d’exploitation invité et des applications qui permettent de surveiller ce qui se passe dans le service Azure. Pour d’autres ressources Azure, telles que les groupes de sécurité réseau, les données de surveillance des ressources constituent la couche de données la plus élevée (dans la mesure où aucun système d’exploitation invité ni aucune application ne sont exécutés sur ces ressources). Ces données peuvent être collectées à l’aide des [paramètres de diagnostic des ressources](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Données de surveillance de la plateforme Azure :** données concernant le fonctionnement et la gestion d’un abonnement ou d’un locataire Azure, mais aussi données concernant l’intégrité et le fonctionnement d’Azure. Le [journal d’activité](./monitoring-overview-activity-logs.md), y compris les données d’intégrité du service, et les audits Active Directory, sont des exemples de données de surveillance de la plateforme. Ces données peuvent également être collectées à l’aide des paramètres de diagnostic.

Vous pouvez envoyer les données de toutes les couches vers un hub d’événements, duquel elles pourront être extraites par un outil partenaire. Les sections suivantes expliquent comment configurer les données de chaque couche de sorte qu’elles soient diffusées vers un hub d’événements. Ces étapes supposent que cette couche contient déjà des ressources à surveiller.

## <a name="set-up-an-event-hubs-namespace"></a>Configurer un espace de noms Event Hubs

Avant de commencer, vous devez [créer un espace de noms Event Hubs et un hub d’événements](../event-hubs/event-hubs-create.md). Cet espace de noms et ce hub d’événements constituent la destination de toutes vos données de surveillance. Un espace de noms Event Hubs est un regroupement logique de hubs d’événements qui partagent une même stratégie d’accès, tout comme un compte de stockage contient des objets blob. Notez les points suivants concernant l’espace de noms Event Hubs et les hubs d’événements que vous créez :
* Nous recommandons d’utiliser un espace de noms Event Hubs standard.
* En règle générale, une seule unité de débit est nécessaire. Si vous avez besoin de plus pour répondre à l’augmentation de l’utilisation de votre journal, vous pouvez augmenter manuellement le nombre d’unités de débit pour l’espace de noms ou activer l’inflation automatique.
* Le nombre d’unités de débit vous permet d’augmenter l’échelle de débit de vos hubs d’événements. Le nombre de partitions vous permet de paralléliser la consommation sur un grand nombre de consommateurs. Une seule partition peut atteindre jusqu’à 20 Mbits/s, soit environ 20 000 messages par seconde. En fonction de l’outil qui consomme les données, la consommation simultanée de plusieurs partitions risque de ne pas être prise en charge. Si vous n’êtes pas sûr du nombre de partitions à définir, il est recommandé de commencer avec quatre partitions.
* Il est également recommandé de définir la conservation des messages de votre hub d’événements sur 7 jours. Si l’outil consommateur est en panne pendant plus d’un jour, cela garantit qu’il pourra reprendre là où il s’est arrêté (pour les événements des 7 derniers jours).
* Il est recommandé d’utiliser le groupe de consommateurs par défaut pour votre hub d’événements. Il n’est pas nécessaire de créer d’autres groupes de consommateurs ou d’utiliser un groupe de consommateurs distinct, sauf si deux outils doivent utiliser les mêmes données d’un même hub d’événements.
* Pour le journal des activités Azure, vous pouvez choisir un espace de noms Event Hubs pour qu’Azure Monitor y crée un hub d’événements appelé « insights-logs-operationallogs ». Pour les autres types de journaux, vous pouvez soit choisir un hub d’événements existant (ce qui vous permet de réutiliser le hub d’événements insights-logs-operationallogs), soit créer un hub d’événements par catégorie de journal à l’aide d’Azure Monitor.
* En règle générale, les ports 5671 à 5672 doivent être ouverts sur l’ordinateur qui consomme les données du hub d’événements.

Consultez également le [Forum aux questions (FAQ) sur Azure Event Hubs](../event-hubs/event-hubs-faq.md).

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Comment configurer les données de surveillance de la plateforme Azure pour qu’elles soient diffusées vers un hub d’événements ?

Les données de surveillance de la plateforme Azure proviennent principalement de deux sources :
1. Le [journal d’activité Azure](./monitoring-overview-activity-logs.md), qui contient les opérations de création, de mise à jour et de suppression du Gestionnaire de ressources, les changements [d’intégrité du service Azure](../service-health/service-health-overview.md) qui peuvent impacter les ressources de votre abonnement, les transitions d’état [d’intégrité des ressources](../service-health/resource-health-overview.md), et plusieurs autres types d’événements au niveau de l’abonnement. [Cet article décrit toutes les catégories d’événements qui s’affichent dans le journal d’activité Azure](./monitoring-activity-log-schema.md).
2. Les [rapports Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md), qui contiennent l’historique des connexions et la piste d’audit des modifications apportées à un locataire particulier. Il n’est pas encore possible de diffuser des données Azure Active Directory vers un hub d’événements.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Diffuser les données du journal d’activité Azure vers un hub d’événements

Pour envoyer les données du journal d’activité Azure vers un espace de noms Event Hubs, vous devez configurer un profil de journal dans votre abonnement. [Suivez ce guide](./monitoring-stream-activity-logs-event-hubs.md) pour configurer un profil de journal dans votre abonnement. Vous devez effectuer cette configuration pour chaque abonnement que vous souhaitez surveiller.

> [!TIP]
> Un profil de journal permet uniquement de sélectionner un espace de noms Event Hubs, dans lequel un hub d’événements est créé puis nommé « insights-operational-logs ». Il n’est pas encore possible de spécifier un nom personnalisé pour un hub d’événements dans un profil de journal.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Comment configurer les données de surveillance des ressources Azure pour qu’elles soient diffusées vers un hub d’événements ?

Les ressources Azure émettent deux types de données de surveillance :
1. [Journaux de diagnostic des ressources](./monitoring-overview-of-diagnostic-logs.md)
2. [Métriques](monitoring-overview-metrics.md)

Ces deux types de données sont envoyés à un hub d’événements à l’aide d’un paramètre de diagnostic des ressources. [Suivez ce guide](./monitoring-stream-diagnostic-logs-to-event-hubs.md) pour définir un paramètre de diagnostic dans une ressource particulière. Définissez un paramètre de diagnostic des ressources pour chacune des ressources dont vous voulez collecter les journaux.

> [!TIP]
> Vous pouvez utiliser Azure Policy pour faire en sorte que toutes les ressources de l’étendue spécifiée soient toujours configurées avec un paramètre de diagnostic [à l’aide de l’effet DeployIfNotExists de la règle de stratégie](../azure-policy/policy-definition.md#policy-rule). À l’heure actuelle, DeployIfNotExists est uniquement pris en charge dans les stratégies prédéfinies.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Comment configurer les données de surveillance des systèmes d’exploitation invités pour qu’elles soient diffusée vers un hub d’événements ?

Pour envoyer les données de surveillance des systèmes d’exploitation invités vers un hub d’événements, vous devez installer un agent. Que ce soit pour Windows ou pour Linux, vous devez spécifier dans un fichier config les données à envoyer au hub d’événements, ainsi que le hub d’événements vers lequel envoyer les données. Ensuite, vous devez passer ce fichier config à l’agent qui est exécuté sur la machine virtuelle.

### <a name="stream-linux-data-to-an-event-hub"></a>Diffuser les données Linux vers un hub d’événements

[L’agent de diagnostic Azure pour Linux](../virtual-machines/linux/diagnostic-extension.md) peut être utilisé pour envoyer les données de surveillance d’une machine Linux vers un hub d’événements. Pour cela, ajoutez le hub d’événements comme récepteur dans les paramètres protégés de votre fichier config LAD. [Consultez cet article pour en savoir plus sur l’ajout du récepteur de hub d’événements à l’agent de diagnostic Azure pour Linux](../virtual-machines/linux/diagnostic-extension.md#protected-settings).

> [!NOTE]
> Il n’est pas possible de configurer le streaming des données de surveillance des systèmes d’exploitation invités vers un hub d’événements dans le portail. Au lieu de cela, vous devez modifier manuellement le fichier config.

### <a name="stream-windows-data-to-an-event-hub"></a>Diffuser les données Windows vers un hub d’événements

[L’agent de diagnostic Azure pour Windows](./azure-diagnostics.md) peut être utilisé pour envoyer les données de surveillance d’une machine Windows vers un hub d’événements. Pour cela, ajoutez le hub d’événements comme récepteur dans la section privateConfig du fichier config WAD. [Consultez cet article pour en savoir plus sur l’ajout du récepteur de hub d’événements à l’agent de diagnostic Azure pour Windows](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Il n’est pas possible de configurer le streaming des données de surveillance des systèmes d’exploitation invités vers un hub d’événements dans le portail. Au lieu de cela, vous devez modifier manuellement le fichier config.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Comment configurer les données de surveillance de l’application pour qu’elles soient diffusées vers un hub d’événements ?

Les données de surveillance de l’application nécessitent que votre code soit instrumenté avec un SDK. Il n’existe donc pas de solution toute faite pour router les données de surveillance de l’application vers un hub d’événements dans Azure. Toutefois, [Azure Application Insights](../application-insights/app-insights-overview.md) est un service qui peut être utilisé pour collecter des données Azure au niveau de l’application. Si vous utilisez Application Insights, vous pouvez diffuser des données de surveillance vers un hub d’événements en procédant de la manière suivante :

1. [Configurez l’exportation continue](../application-insights/app-insights-export-telemetry.md) des données Application Insights vers un compte de stockage.

2. Configurez une application logique déclenchée par minuteur qui [tire (pull) les données du stockage d’objets blob](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) et [les envoie (push) en tant que message au hub d’événements](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Que puis-je faire avec les données de surveillance qui sont envoyées à mon hub d’événements ?

Le routage de vos données de surveillance vers un hub d’événements avec Azure Monitor vous permet d’intégrer facilement des systèmes SIEM ou des outils de surveillance partenaires. La plupart des outils nécessitent la chaîne de connexion du hub d’événements, ainsi que certaines autorisations de votre abonnement Azure, pour lire les données du hub d’événements. Voici une liste non exhaustive d’outils avec intégration Azure Monitor :

* **IBM QRadar** : le module DSM Microsoft Azure et le protocole Microsoft Azure Event Hubs sont disponibles au téléchargement sur le [site web du support IBM](http://www.ibm.com/support). Pour plus d’informations sur l’intégration à Azure [cliquez ici](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - En fonction de votre configuration Splunk, deux approches sont possibles :
    1. [Le module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/) est un projet open source disponible dans Splunkbase. [La documentation est disponible ici](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Si vous ne pouvez pas installer de module complémentaire dans votre instance Splunk (par exemple, si vous utilisez un proxy ou exécutez sur un cloud Splunk), vous pouvez transférer ces événements au collecteur d’événements HTTP Splunk en utilisant [cette fonction qui est déclenchée par les nouveaux messages dans le hub d’événements](https://github.com/sebastus/AzureFunctionForSplunkVS).
* **SumoLogic** : les instructions pour configurer SumoLogic de manière à utiliser les données d’un hub d’événements sont [disponibles ici](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>Étapes suivantes
* [Archiver le journal d’activité dans un compte de stockage](monitoring-archive-activity-log.md)
* [Lire la présentation du journal d’activité Azure](monitoring-overview-activity-logs.md)
* [Définir une alerte basée sur un événement de journal d’activité](insights-auditlog-to-webhook-email.md)

