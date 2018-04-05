---
title: Activer la journalisation des événements de lot - Azure | Microsoft Docs
description: Enregistrez et analysez les événements du journal de diagnostic pour des ressources de compte Azure Batch telles que des pools et des tâches.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41c8c9f8fd9302c610ce356b0485e33ea3c967d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Consigner des événements pour l’analyse et l’évaluation de diagnostic des solutions Batch

Comme de nombreux services Azure, le service Batch génère des événements de journal pour certaines ressources pendant la durée de vie de celles-ci. Vous pouvez activer les journaux de diagnostic Azure Batch afin d’enregistrer des événements pour des ressources telles que des pools et des tâches, puis utiliser les journaux à des fins d’évaluation et d’analyse des diagnostics. Des événements tels qu’une création de pool, une suppression de pool, un démarrage de tâche ou un achèvement de tâche sont consignés dans les journaux de diagnostic de Batch.

> [!NOTE]
> Cet article décrit la journalisation d’événements pour les ressources de compte Batch, pas les données de sortie de travail et de tâche. Pour plus d’informations sur le stockage des données de sortie des travaux et des tâches, voir [Conserver une sortie de tâche et de travail Azure Batch](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Prérequis

* [Compte Azure Batch](batch-account-create-portal.md)
* [Compte Stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Pour conserver les journaux de diagnostic de Batch, vous devez créer un compte Stockage Azure dans lequel Azure stocke les journaux. Vous spécifiez ce compte de stockage lorsque vous [activez la journalisation des diagnostics](#enable-diagnostic-logging) pour votre compte Batch. Le compte de stockage que vous spécifiez lorsque vous activez la collecte de journaux n’est pas identique à un compte de stockage lié tel que décrit dans les articles relatifs aux [packages d’application](batch-application-packages.md) et à la [persistance en sortie des tâches](batch-task-output.md).
  
  > [!WARNING]
  > Vous êtes **facturé** pour les données stockées dans votre compte de Stockage Azure. Cela inclut les journaux de diagnostic abordés dans cet article. Gardez cela à l’esprit lorsque vous élaborez votre [stratégie de rétention des journaux](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Activer la journalisation des diagnostics
La journalisation des diagnostics n’est pas activée par défaut pour votre compte Batch. Vous devez activer explicitement la journalisation des diagnostics pour chaque compte Batch à analyser :

[Comment activer la collecte des journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Nous vous recommandons de lire entièrement l’article [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pour comprendre non seulement comment activer la journalisation, mais aussi les catégories de journal prises en charge par les divers services Azure. Par exemple, Azure Batch prend actuellement en charge une seule catégorie de journal, celle des **journaux de service**.

## <a name="service-logs"></a>Journaux de service
Les journaux de service Azure Batch contiennent des événements signalés par le service Azure Batch pendant la durée de vie d’une ressource Batch telle qu’un pool ou une tâche. Chaque événement émis par Batch est stocké dans le compte de stockage spécifié au format JSON. Par exemple, ceci est le corps d’un exemple d’**événement de création de pool** :

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Chaque corps d’événement réside dans un fichier .json dans le compte de Stockage Azure spécifié. Si vous souhaitez accéder directement aux journaux, vous pouvez consulter le [Schéma des journaux de diagnostic dans le compte de stockage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Événements du journal de service
Actuellement, le service Batch émet les événements du journal de service suivants. Cette liste n’est peut-être pas exhaustive, car des événements supplémentaires peuvent avoir été ajoutés depuis la dernière mise à jour de cet article.

| **Événements du journal de service** |
| --- |
| [Création de pool][pool_create] |
| [Démarrage de suppression de pool][pool_delete_start] |
| [Fin de suppression de pool][pool_delete_complete] |
| [Démarrage de redimensionnement de pool][pool_resize_start] |
| [Fin de redimensionnement de pool][pool_resize_complete] |
| [Démarrage de tâche][task_start] |
| [Fin de tâche][task_complete] |
| [Échec de la tâche][task_fail] |

## <a name="next-steps"></a>Étapes suivantes
Outre le stockage d’événements du journal de diagnostic dans un compte de Stockage Azure, vous pouvez diffuser des événements de journal de service Batch sur un [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md), puis les envoyer à [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Stream Azure Diagnostic Logs to Event Hubs (Diffuser en continu les journaux de diagnostic Azure vers Event Hubs)](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Diffusez les événements de diagnostic de Batch vers le service d’entrée de données hautement extensible Event Hubs. Le service Event Hubs peut traiter à chaque seconde des millions d’événements que vous pouvez transformer et stocker à l’aide de tout fournisseur d’analyses en temps réel.
* [Analyser les journaux de diagnostic Azure à l’aide de Log Analytics](../log-analytics/log-analytics-azure-storage.md)
  
  Envoyez vos journaux de diagnostic à Log Analytics où vous pouvez les analyser via le portail Operations Management Suite (OMS), ou les exporter à des fins d’analyse vers Power BI ou Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
