---
title: "Agrégation d’événements Azure Service Fabric avec Linux Azure Diagnostics | Microsoft Docs"
description: "Découvrez l’agrégation et la collecte d’événements à l’aide de Linux Azure Diagnostics (LAD) pour la surveillance et le diagnostic de clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b70780b2e1d169aced3412f6fe6d13ad4dab82be
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agrégation et collection d’événements à l’aide de Linux Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux vous permet d’analyser et résoudre les problèmes que vous pourriez rencontrer dans votre cluster ou dans les applications et services exécutés dans ce cluster.

L’une des façons de charger et de collecter des journaux consiste à utiliser l’extension Linux Azure Diagnostics (LAD), qui assure le chargement des journaux dans le Stockage Azure, et qui a également la possibilité d’envoyer des journaux à Azure Application Insights ou Event Hubs. Vous pouvez également utiliser un processus externe pour lire les événements à partir du stockage et les placer dans un produit de plateforme d’analyse tel que [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou une autre solution d’analyse de journaux.

## <a name="log-and-event-sources"></a>Sources de journaux et d’événements

### <a name="service-fabric-platform-events"></a>Événements de la plateforme Service Fabric
Service Fabric émet quelques journaux prêts à l’emploi via [LTTng](http://lttng.org), notamment les événements opérationnels ou des événements du runtime. Ces journaux sont stockés dans l’emplacement spécifié par le modèle Resource Manager du cluster. Pour obtenir ou définir les informations relatives au compte de stockage, recherchez l’étiquette **AzureTableWinFabETWQueryable**, puis recherchez **StoreConnectionString**.

### <a name="application-events"></a>Événements liés aux applications
 Les événements émis à partir du code de vos applications et services tel que spécifié par vous-même lors de l’instrumentation de votre logiciel. Vous pouvez utiliser une solution de journalisation qui écrit des fichiers journaux textuels, par exemple LTTng. Pour plus d’informations, consultez la documentation de LTTng sur le traçage de votre application.

[Surveillance et diagnostic des services dans une configuration de développement d’ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Déployer l’extension Diagnostics
La première étape de la collecte de journaux consiste à déployer l’extension Diagnostics sur chaque machine virtuelle du cluster Service Fabric. Cette extension collecte les journaux sur chaque machine virtuelle et les charge dans le compte de stockage que vous spécifiez. 

Pour déployer l’extension Diagnostics sur les machines virtuelles du cluster lors de la création de ce dernier, définissez **Diagnostics** sur **Activé**. Une fois le cluster créé, vous ne pouvez plus modifier ce paramètre à l’aide du portail et vous devez donc apporter les modifications appropriées dans le modèle Resource Manager.

Vous configurez ainsi l’agent LAD pour surveiller les fichiers journaux indiqués. Dès qu’une nouvelle ligne est ajoutée au fichier, elle crée une entrée dans le journal syslog, qui est envoyée au stockage (à la table) que vous avez spécifié.


## <a name="next-steps"></a>Étapes suivantes

1. Pour identifier plus précisément les événements à consulter lors de la résolution des problèmes, consultez la [documentation de LTTng](http://lttng.org/docs) et la section [Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
2. [Configurez l’agent OMS](service-fabric-diagnostics-event-analysis-oms.md) pour rassembler les métriques, surveiller les conteneurs déployés sur votre cluster et visualiser vos journaux 