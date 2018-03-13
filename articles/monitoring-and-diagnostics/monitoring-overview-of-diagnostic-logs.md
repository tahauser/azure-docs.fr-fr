---
title: "Présentation des journaux de diagnostic Azure | Microsoft Docs"
description: "Découvrez les journaux de diagnostic Azure et comment les utiliser pour comprendre les événements qui se produisent au sein d’une ressource Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: df20e174abb9960ad378221008ac7261fd0582f1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Collecter et utiliser des données de journaux à partir de vos ressources Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Présentation des journaux de diagnostic des ressources Azure
Les **journaux de diagnostic au niveau des ressources Azure** sont des journaux émis par une ressource, qui fournissent des informations détaillées et riches sur l’utilisation de celle-ci, à intervalles réguliers. Le contenu de ces journaux varie en fonction du type de ressource. Les compteurs de règles du groupe de sécurité réseau et les audits de coffres de clés sont deux exemples de catégories de journaux de ressource.

Les journaux de diagnostic des ressources ne fournissent pas les mêmes informations que le [Journal d’activité](monitoring-overview-activity-logs.md). Le journal d’activité fournit un aperçu des opérations qui ont été effectuées sur les ressources de votre abonnement à l’aide de Resource Manager (par exemple, la création d’une machine virtuelle ou la suppression d’une application logique). Il s’intéresse aux opérations effectuées au niveau de l’abonnement. Les journaux de diagnostic des ressources, quant à eux, donnent un aperçu des opérations qui ont été effectuées au sein d’une ressource (par exemple, l’obtention d’un secret à partir d’un coffre de clés).

Les journaux de diagnostic des ressources diffèrent également des journaux de diagnostic de système d’exploitation invité. Les journaux de diagnostic de système d’exploitation invité sont collectés par un agent exécuté sur une machine virtuelle ou un autre type de ressource pris en charge. Les journaux de diagnostic des ressources ne nécessitent aucun agent et capturent les données relatives à la ressource à partir de la plateforme Azure, alors que les journaux de diagnostic du système d’exploitation invité capturent les données provenant du système d’exploitation et des applications exécutées sur la machine virtuelle.

Le nouveau type de journal de diagnostic décrit ici n’est cependant pas pris en charge par toutes les ressources. Cet article comprend une section répertoriant les types de ressources qui prennent en charge les nouveaux journaux de diagnostic des ressources.

![Comparaison entre les journaux de diagnostic des ressources et les autres types de journaux ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Comment utiliser les journaux de diagnostic au niveau des ressources
Voici ce que vous pouvez faire avec les journaux de diagnostic des ressources :

![Positionnement logique des journaux de diagnostic des ressources](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Enregistrez-les dans un [**compte de stockage**](monitoring-archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **paramètres de diagnostic des ressources**.
* [Diffusez-les en streaming sur **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (comme PowerBI) les ingère.
* Analysez-les avec [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hubs qui n’est pas dans le même abonnement que celui générant des journaux. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements.

## <a name="resource-diagnostic-settings"></a>Paramètres de diagnostic des ressources
Les journaux de diagnostic des ressources non liées au calcul sont configurés à l’aide des paramètres de diagnostic des ressources. **Paramètres de diagnostic des ressources** pour un contrôle des ressources :

* Où les journaux de diagnostic des ressources et les mesures sont envoyés (compte de stockage, Concentrateurs d’événements et/ou Analyse des journaux OMS).
* Les catégories de journal envoyées et les données de mesure également envoyées.
* La durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage
    - Une durée de rétention de zéro jour signifie que les journaux sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647.
    - Si des stratégies de rétention sont définies, mais que le stockage des journaux dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.
    - Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’avant-hier seront supprimés.

Ces paramètres peuvent être facilement configurés via les paramètres de diagnostics pour une ressource dans le portail Azure, via les commandes Azure PowerShell et de l’interface CLI ou via [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Mesures et journaux de diagnostic depuis la couche de système d’exploitation invité des ressources Compute (comme les machines virtuelles ou Service Fabric) utilisent [un mécanisme distinct pour la configuration et la sélection des sorties](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Comment activer la collecte des journaux de diagnostic des ressources
La collecte des journaux de diagnostic des ressources peut être activée [dans le cadre de la création d’une ressource dans un modèle Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) ou après la création d’une ressource via la page d’une ressource dans le portail. Vous pouvez également activer la collecte à tout moment via les commandes de l’interface de ligne de commande ou d’Azure PowerShell, ou via l’API REST Azure Monitor.

> [!TIP]
> Ces instructions peuvent ne pas s’appliquer directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les étapes spécifiques qui peuvent concerner certains types de ressources.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Activer la collecte des journaux de diagnostic des ressources dans le portail
Vous pouvez activer la collecte des journaux de diagnostic des ressources dans le portail Azure après la création d’une ressource, en allant vers une ressource spécifique ou en navigant vers Azure Monitor. Pour activer cette option via Azure Monitor :

1. Dans le [portail Azure](http://portal.azure.com), naviguez jusqu’à Azure Monitor, puis cliquez sur **Paramètres de diagnostic**

    ![Section Surveillance d’Azure Monitor](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Vous pouvez également filtrer la liste par type ou groupe de ressources, puis cliquez sur la ressource pour laquelle vous souhaitez définir un paramètre de diagnostic.

3. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur « Activer les diagnostics ».

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   S’il existe des paramètres existants sur la ressource, vous verrez une liste de paramètres déjà configurés sur cette ressource. Cliquez sur « Ajouter le paramètre de diagnostic ».

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Donnez un nom à votre définition, cochez les cases pour chaque destination vers laquelle vous souhaitez envoyer des données et configurez la ressource utilisée pour chaque destination. Si vous le souhaitez, vous pouvez définir un nombre de jours pour conserver ces journaux à l’aide des curseurs **Rétention (jours)** (uniquement applicable à la destination du compte de stockage). Si la valeur zéro est appliquée à la rétention, les journaux sont stockés pour une durée indéfinie.
   
   ![Ajouter le paramètre de diagnostic - paramètres existants](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource et les journaux de diagnostic sont envoyés vers les destinations spécifiées dès la génération de nouvelles données d’événements.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Activer la collecte des journaux de diagnostic des ressources via PowerShell
Pour activer la collecte des journaux de diagnostic des ressources via Azure PowerShell, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L’ID de compte de stockage est l’ID de ressource du compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

L’ID de règle Service Bus est une chaîne au format suivant : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Activer la collecte des journaux de diagnostic des ressources via l’interface de ligne de commande (CLI)
Pour activer la collecte des journaux de diagnostic des ressources via Azure CLI, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

L’ID de compte de stockage est l’ID de ressource du compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

L’ID de règle Service Bus est une chaîne au format suivant : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Activer la collecte des journaux de diagnostic des ressources via l’API REST
Pour modifier les paramètres de diagnostic à l’aide de Azure Monitor REST API, consultez [ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gérer les paramètres de diagnostic des ressources dans le portail
Assurez-vous que toutes vos ressources sont configurées avec des paramètres de diagnostic. Accédez à **Moniteur** dans le portail et ouvrez **Paramètres de diagnostic**.

![Panneau Journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Vous devrez peut-être cliquer sur « Tous les services » pour trouver la section Monitor.

Dans cette section, vous pouvez afficher et filtrer toutes les ressources qui prennent en charge les paramètres de diagnostic pour voir si les diagnostics y sont activés. Vous pouvez également explorer pour voir si plusieurs paramètres sont définis sur une ressource et vérifier quel compte de stockage, espace de noms des concentrateurs d’événements et/ou espace de travail Log Analytics vers lesquels sont diffusées les données.

![Résultats des Journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

L’ajout d’un paramètre de diagnostic permet d’afficher le panneau Paramètres de diagnostic, où vous pouvez activer, désactiver ou modifier vos paramètres de diagnostic pour la ressource sélectionnée.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Services, catégories et schémas pris en charge pour les journaux de diagnostic de ressources
[Consultez cet article](monitoring-diagnostic-logs-schema.md) pour obtenir la liste complète des services pris en charge et des catégories de journaux et des schémas utilisés par ces services.

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)
