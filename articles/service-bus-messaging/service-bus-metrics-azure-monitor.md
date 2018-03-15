---
title: "Métriques d’Azure Service Bus dans Azure Monitor (préversion) | Microsoft Docs"
description: "Utiliser la surveillance Azure pour surveiller des entités Service Bus"
services: service-bus-messaging
documentationcenter: .NET
author: christianwolf42
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 20115897bb5ae2638588e79d80700fa8ece06104
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Métriques Azure Service Bus dans Azure Monitor (préversion)

Les métriques de Service Bus vous donnent l’état des ressources dans votre abonnement Azure. Avec un ensemble complet de données de métriques, vous pouvez évaluer l’intégrité globale des ressources de Service Bus, non seulement au niveau de l’espace de noms, mais également au niveau de l’entité. Ces statistiques peuvent être importantes, car elles vous aident à surveiller l’état de Service Bus. Les métriques peuvent également vous aider à résoudre les problèmes constituant des causes premières sans avoir à contacter le support technique Azure.

Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre divers services Azure. Pour plus d’informations, consultez [Analyse dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) et [Récupérer des métriques Azure Monitor avec .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) sur GitHub.

## <a name="access-metrics"></a>Accéder aux mesures

Azure Monitor propose plusieurs méthodes d’accès aux mesures. Vous pouvez accéder à ces métriques à partir du [portail Azure](https://portal.azure.com), ou utiliser les API Azure Monitor (REST et .Net) et des solutions d’analyse telles qu’Operation Management Suite (OMS) et Event Hubs. Pour plus d’informations, voir [Mesures Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Les métriques sont activées par défaut, et vous pouvez accéder aux 30 derniers jours de données. Si vous souhaitez conserver des données sur une période plus longue, vous pouvez archiver les données de mesures dans un compte de stockage Azure. Celui-ci est configuré dans les [paramètres de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) dans Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Accéder aux métriques dans le portail

Vous pouvez surveiller les mesures au fil du temps dans le [portail Azure](https://portal.azure.com). L’exemple suivant montre comment afficher les demandes réussies et les demandes entrantes au niveau du compte :

![][1]

Vous pouvez également accéder aux métriques directement via l’espace de noms. Pour ce faire, sélectionnez votre espace de noms, puis **Métriques (préversion)**. Pour afficher les métriques filtrées à l’étendue de l’entité, sélectionnez l’entité, puis cliquez sur **Métriques (préversion)**.

![][2]

Pour les mesures prenant en charge des dimensions, vous devez filtrer avec la valeur de dimension souhaitée.

## <a name="billing"></a>Facturation

L’utilisation de mesures dans Azure Monitor est gratuite pendant la période de préversion. Toutefois, si vous utilisez des solutions supplémentaires absorbant des données de métriques, vous pouvez être facturé par ces solutions. Par exemple, vous êtes facturé par Stockage Azure si vous archivez des données de mesures pour un compte de stockage Azure. Vous êtes également facturé par Operation Management Suite (OMS) si vous diffusez en continu des données de métriques vers OMS pour une analyse avancée.

Les métriques suivantes vous donnent une vue d’ensemble de l’intégrité de votre service. 

> [!NOTE]
> Nous déprécions plusieurs métriques lors de leur déplacement sous un autre nom. Vous devrez peut-être mettre à jour vos références. Les métriques marquées avec le mot-clé « dépréciée » ne seront pas gérées à l’avenir.

Toutes les valeurs de métriques sont envoyées à Azure Monitor toutes les minutes. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de métriques sont présentées. L’intervalle de temps pris en charge pour toutes les métriques de Service Bus est de 1 minute.

## <a name="request-metrics"></a>Métriques de demande

Compte le nombre de requêtes d’opérations de données et de gestion.

| Nom de métrique | Description |
| ------------------- | ----------------- |
| Demandes entrantes (préversion) | Le nombre de requêtes effectuées auprès de Service Bus sur une période spécifiée. <br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Requêtes ayant réussi (préversion)|Le nombre de requêtes réussies effectuées auprès de Service Bus sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Erreurs du serveur (préversion)|Le nombre de requêtes non traitées en raison d’une erreur sur Service Bus sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Erreurs utilisateur (préversion)|Le nombre de demandes non traitées en raison d’erreurs utilisateur sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Demandes limitées (préversion)|Le nombre de demandes qui ont été limitées car l’utilisation a été dépassée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="message-metrics"></a>Métriques de message

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Messages entrants (préversion)|Le nombre d’événements ou de messages envoyés à Service Bus sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Messages sortants (préversion)|Le nombre d’événements ou de messages reçus à partir de Service Bus sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="connection-metrics"></a>Métriques de connexion

| Nom de métrique | Description |
| ------------------- | ----------------- |
|ActiveConnections (préversion)|Le nombre de connexions actives sur un espace de noms ainsi que sur une entité.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Ouvertures de connexion (préversion)|Le nombre de connexions ouvertes.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Connexions fermées (préversion)|Le nombre de connexions fermées.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName |

## <a name="resource-usage-metrics"></a>Métriques d’utilisation des ressources

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Utilisation du processeur par espace de noms (préversion)|Le pourcentage d’utilisation du processeur de l’espace de noms.<br/><br/> Unité : pourcentage <br/> Type d’agrégation : maximale <br/> Dimension : EntityName|
|Utilisation de la taille mémoire par espace de noms (préversion)|Le pourcentage d’utilisation de mémoire de l’espace de noms.<br/><br/> Unité : pourcentage <br/> Type d’agrégation : maximale <br/> Dimension : EntityName|

## <a name="metrics-dimensions"></a>Dimensions de mesures

Azure Service Bus prend en charge les dimensions suivantes pour les mesures dans Azure Monitor. L’ajout de dimensions à vos métriques est facultatif. Si vous n’ajoutez pas de dimensions, les métriques sont spécifiées au niveau de l’espace de noms. 

|Nom de la dimension|Description|
| ------------------- | ----------------- |
|EntityName| Service Bus prend en charge les entités de messagerie sous l’espace de noms.|

## <a name="next-steps"></a>Étapes suivantes

Consultez la [Vue d’ensemble de surveillance Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


