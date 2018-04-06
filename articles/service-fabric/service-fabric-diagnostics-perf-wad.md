---
title: Azure Service Fabric - Analyse des performances avec l’extension Microsoft Azure Diagnostics | Microsoft Docs
description: Utilisez Microsoft Azure Diagnostics pour collecter les compteurs de performances de vos clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: b2b740c2ececba2c3f95f8fbfbfb55e7f4811112
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Analyse des performances avec l’extension Microsoft Azure Diagnostics

Ce document explique comment configurer la collecte des compteurs de performances via Microsoft Azure Diagnostics pour les clusters Windows. Pour les clusters Linux, configurez [l’agent OMS](service-fabric-diagnostics-oms-agent.md) afin de collecter les compteurs de performances de vos nœuds. 

 > [!NOTE]
> Pour cette procédure, l’extension Microsoft Azure Diagnostics doit être déployée sur votre cluster. S’il n’est pas configuré, consultez [Agrégation et collecte d’événements à l’aide des diagnostics Windows Azure](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

## <a name="collect-performance-counters-via-the-wadcfg"></a>Collecter des compteurs de performances via le WadCfg

Pour collecter les compteurs de performances via Microsoft Azure Diagnostics, vous devez modifier la configuration dans le modèle Resource Manager de votre cluster. Suivez ces étapes pour ajouter un compteur de performances à collecter dans votre modèle, puis exécutez une mise à niveau des ressources Resource Manager.

1. Recherchez la configuration Microsoft Azure Diagnostics dans les modèles de votre cluster (recherchez `WadCfg`). Vous allez ajouter des compteurs de performances à collecter sous `DiagnosticMonitorConfiguration`.

2. Définissez votre configuration pour collecter les compteurs de performances en ajoutant la section suivante à votre `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` définit à quelle fréquence les valeurs des compteurs collectés sont transférées vers votre table de stockage Azure et tous les récepteurs configurés. 

3. Ajoutez les compteurs de performances à collecter au `PerformanceCounterConfiguration` qui a été déclaré à l’étape précédente. Chaque compteur à collecter est défini avec un `counterSpecifier`, un `sampleRate`, un `unit` et un `annotation`, ainsi qu’avec tous les `sinks` applicables. Voici un exemple de configuration avec le compteur pour le *Temps total du processeur* (la quantité de temps d’utilisation de l’UC pour les opérations de traitement) et les *appels de la Méthode Service Fabric Actor par seconde*, un des compteurs des performances personnalisés Service Fabric. Reportez-vous à l’article [Compteurs de performances de l’acteur fiable](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) et [Compteurs de Performances du service fiable](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) pour obtenir une liste complète des compteurs de performances personnalisés Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Vous pouvez modifier le taux d’échantillonnage du compteur selon vos besoins. Le format utilisé est le suivant : `PT<time><unit>`. Par conséquent, si vous souhaitez que le compteur soit collecté à chaque seconde, vous devez définir le taux d’échantillonnage ainsi : `"sampleRate": "PT15S"`.

 >[!NOTE]
 >Même si vous pouvez utiliser `*` pour spécifier des groupes de compteurs de performances portant un nom similaire, l’envoi de tous les compteurs via un récepteur (vers Application Insights) nécessite de les déclarer séparément. 

4. Une fois que vous avez ajouté les compteurs de performances à collecter appropriés, vous devez mettre à niveau votre ressource de cluster afin que ces modifications soient répercutées dans le cluster en cours d’exécution. Enregistrez le `template.json` modifié, puis ouvrez PowerShell. Vous pouvez mettre à niveau votre cluster à l’aide de `New-AzureRmResourceGroupDeployment`. L’appel nécessite le nom du groupe de ressources, le fichier de modèle mis à jour et le fichier de paramètres. Ensuite, il demande à Resource Manager d’apporter les modifications nécessaires aux ressources que vous avez mises à jour. Une fois connecté à votre compte et au bon abonnement, utilisez la commande suivante pour exécuter la mise à niveau :

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Une fois la mise à niveau terminée (au bout de 15 à 45 minutes), WAD doit collecter les compteurs de performances et les envoyer à la table nommée WADPerformanceCountersTable dans le compte de stockage associé à votre cluster.

## <a name="next-steps"></a>Étapes suivantes
* Pour voir vos compteurs de performances dans Application Insights, [ajoutez le Récepteur IA au modèle Resource Manager](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
* Collectez d’autres compteurs de performances pour votre cluster. Consultez [Mesures de performances](service-fabric-diagnostics-event-generation-perf.md) pour obtenir la liste des compteurs à collecter.
* [Utilisez les fonctionnalités de surveillance et de diagnostics avec une machine virtuelle Windows et des modèles Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) pour apporter des modifications à votre `WadCfg` (y compris pour la configuration de comptes de stockage supplémentaires vers lesquels envoyer des données de diagnostics).
