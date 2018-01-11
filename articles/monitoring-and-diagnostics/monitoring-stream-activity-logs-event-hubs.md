---
title: "Diffuser en continu le journal d’activité Azure sur les Event Hubs | Microsoft Docs"
description: "Apprenez comment diffuser en continu le journal d’activité Azure sur les Event Hubs."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/06/2017
ms.author: johnkem
ms.openlocfilehash: f0e507cf2804edbcdd6c87f47b30defbc6a5eb94
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Diffuser en continu le journal des activités Azure sur les Event Hubs
Le [**journal d’activité Azure**](monitoring-overview-activity-logs.md) peut être diffusé en quasi-temps réel sur n’importe quelle application à l’aide de l’option « Export » intégrée au portail, ou en activant l’identifiant de règle Service Bus dans un profil de journal via les applets de commande PowerShell Azure ou l’interface de ligne de commande Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Ce que vous pouvez faire avec le journal d’activité et Event Hubs
Voici quelques façons d’utiliser la fonctionnalité de diffusion en continu pour le journal d’activité :

* **Diffuser en continu sur des systèmes de journalisation et de télémétrie tiers** : au fil du temps, la diffusion en continu sur Event Hubs deviendra le mécanisme de diffusion de votre journal d’activité vers les SIEM et les solutions d’analyse de journaux tiers.
* **Créer une plateforme de journalisation et de télémétrie personnalisée** : si vous disposez déjà d’une plate-forme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse le journal d’activité. [Consultez ici le guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plate-forme de télémétrie à échelle mondiale.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Activer la diffusion en continu du journal d’activité
Vous pouvez activer la diffusion en continu du journal d’activité soit par programmation, soit via le portail. Dans les deux cas, vous choisissez un espace de noms Service Bus et une stratégie d’accès partagé pour cet espace de noms, et un Event Hub est créé dans cet espace de noms lorsque le premier nouvel événement de journal d’activité se produit. Si vous n’avez pas d’espace de noms Service Bus, vous devez d’abord en créer un. Si vous avez précédemment diffusé en continu des événements du journal d’activité vers cet espace de noms Service Bus, l’Event Hub créé précédemment sera réutilisé. La stratégie d’accès partagé définit les autorisations dont dispose le mécanisme de diffusion en continu. À l’heure actuelle, la diffusion vers les clients Event Hubs requiert des autorisations de **gestion**, d’**envoi** et d’**écoute**. Vous pouvez créer ou modifier les stratégies d’accès partagé de l’espace de noms Service Bus dans le portail Azure sous l’onglet Configurer pour votre espace de noms Service Bus. Pour mettre à jour le profil de journal d’activité afin d’inclure la diffusion en continu, l’utilisateur qui apporte la modification doit avoir l’autorisation ListKey sur la règle d’autorisation Service Bus.

Il n’est pas nécessaire que l’espace de noms de Service Bus ou du hub d’événements se trouve dans le même abonnement que la ressource générant des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

### <a name="via-azure-portal"></a>Via le portail Azure
1. Accédez au panneau **Journal d’activité** à l’aide du menu sur le côté gauche du portail.
   
    ![Accéder au journal d’activité dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Cliquez sur le bouton **Exporter** en haut du panneau.
   
    ![Bouton Exporter dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Dans le panneau qui s’affiche, vous pouvez sélectionner les régions pour lesquelles vous voulez diffuser des événements, et l’espace de nom Service Bus dans lequel vous voulez créer un Event Hub pour la diffusion en continu de ces événements.
   
    ![Panneau Exporter le journal d’activité](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliqués à votre abonnement.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Si un profil de journal existe déjà, vous devez d’abord le supprimer.

1. Utiliser `Get-AzureRmLogProfile` pour déterminer s’il existe un profil de journal
2. S’il y en a un, utilisez `Remove-AzureRmLogProfile` pour le supprimer.
3. Utilisez `Set-AzureRmLogProfile` pour créer un profil :

```
Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

L’identifiant de règle Service Bus est une chaîne au format : {identifiant de ressource Service Bus}/authorizationrules/{nom de clé}, par exemple 

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure
Si un profil de journal existe déjà, vous devez d’abord le supprimer.

1. Utiliser `azure insights logprofile list` pour déterminer s’il existe un profil de journal
2. S’il y en a un, utilisez `azure insights logprofile delete` pour le supprimer.
3. Utilisez `azure insights logprofile add` pour créer un profil :

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

L’identifiant de règle Service Bus est une chaîne au format : `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment utiliser les données de journal d’Event Hubs ?
[Le schéma pour le journal d’activité est disponible ici](monitoring-overview-activity-logs.md). Chaque événement est dans un tableau d’objets blob JSON appelé « enregistrements ».

## <a name="next-steps"></a>Étapes suivantes
* [Archiver le journal d’activité dans un compte de stockage](monitoring-archive-activity-log.md)
* [Lire la présentation du journal d’activité Azure](monitoring-overview-activity-logs.md)
* [Définir une alerte basée sur un événement de journal d’activité](insights-auditlog-to-webhook-email.md)

