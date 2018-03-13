---
title: "Vue d’ensemble des diagnostics Azure | Microsoft Docs"
description: "Utilisation des diagnostics Azure pour le débogage, la mesure des performances, la surveillance, l’analyse du trafic dans Cloud Services, Virtual Machines et Service Fabric"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: fbeacd2acfd2fba9d26e7cbc94d8660d15c75642
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="what-is-azure-diagnostics"></a>Que sont les diagnostics Azure ?
Azure Diagnostics est la fonctionnalité Azure qui active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l'extension de diagnostic à partir de plusieurs sources différentes. Les sources actuellement prises en charge sont les rôles Web et les rôles de travail Azure Cloud Service, Azure Virtual Machines sous Microsoft Windows et Service Fabric. Les autres services Azure ont leurs propres diagnostics distincts.

## <a name="data-you-can-collect"></a>Données que vous pouvez collecter
Les diagnostics Azure peuvent collecter les types de données suivants :

| source de données | Description |
| --- | --- |
| Compteurs de performances |Système d’exploitation et compteurs de performances personnalisés |
| Journaux d’application |Messages de trace écrits par votre application |
| Journaux d’événements Windows |Informations envoyées au système de journalisation des événements Windows |
| .NET EventSource |Événements d’écriture de code à l'aide de la classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| Journaux IIS |Informations au sujet des sites Web IIS |
| ETW basé sur les manifestes |Événements de suivi d’événements pour Windows générés par n’importe quel processus |
| Vidages sur incident |Informations sur l’état du processus en cas d’incident d’application |
| Journaux d'erreurs personnalisés |Journaux créés par votre application ou service |
| Journaux d’infrastructure Azure Diagnostic |Informations au sujet des diagnostics eux-mêmes |

L’extension des diagnostics Azure peut transférer ces données vers un compte de stockage Azure ou l’envoyer à des services tels qu’[Application Insights](../application-insights/app-insights-cloudservices.md). Ces données peuvent servir au débogage et à la résolution des problèmes, à mesurer les performances, à suivre l'utilisation des ressources, à analyser le trafic et à prévoir et analyser la capacité.

## <a name="versioning"></a>Contrôle de version
Voir [Historique de contrôle de version des diagnostics Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>étapes suivantes
Choisissez le service sur lequel vous voulez collecter des diagnostics et utilisez les articles suivants pour commencer. Utilisez les liens des diagnostics Azure généraux comme référence pour des tâches spécifiques.

## <a name="web-apps"></a>Web Apps
Notez que Web Apps n'utilise pas les diagnostics Azure. Trouver les informations équivalentes sur [Web Apps](../app-service/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services avec les diagnostics Azure
* Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio pour suivre une application Cloud Services](../vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Sinon, voir
* [Surveillance de Cloud Services à l’aide des diagnostics Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configuration des diagnostics Azure dans une application Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

* [Utilisation des diagnostics avec Application Insights pour Cloud Services](../application-insights/app-insights-cloudservices.md)
* [Assurer le suivi du flux dans une application Cloud Services avec les diagnostics Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Utilisation de PowerShell pour configurer les diagnostics sur Cloud Services](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Virtual Machines avec les diagnostics Azure
* Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio pour suivre les machines virtuelles Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Sinon, voir
* [Configurer les diagnostics Azure sur une machine virtuelle Azure](../virtual-machines-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

* [Utilisation de PowerShell pour configurer les diagnostics sur les machines virtuelles Azure](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric avec les diagnostics Azure
Commencez avec [Surveillance d’une application Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). De nombreux autres articles sur les diagnostics Service Fabric sont disponibles dans l'arborescence de navigation sur la gauche, à partir de cet article.

## <a name="general-azure-diagnostics-articles"></a>Articles généraux sur les diagnostics Azure
* [Configuration du schéma des diagnostics Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) - Apprenez à modifier le fichier de schéma pour collecter et acheminer les données des diagnostics. Vous pouvez également utiliser Visual Studio pour modifier le fichier de schéma.
* [Mode de stockage des données des diagnostics Azure dans le stockage Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) : identifiez les noms des tables et des objets blob dans lesquels les données des diagnostics sont écrites.
* Apprenez à [utiliser les compteurs de performances dans les diagnostics Azure](../cloud-services/diagnostics-performance-counters.md).
* Apprenez à [acheminer les informations des diagnostics Azure vers Application Insights](azure-diagnostics-configure-application-insights.md)
* Si vous rencontrez des difficultés avec le lancement des diagnostics ou la recherche des données dans les tables de stockage Azure, consultez [Résolution des problèmes des diagnostics Azure](azure-diagnostics-troubleshooting.md)
