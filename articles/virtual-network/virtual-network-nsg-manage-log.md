---
title: "Surveiller les opérations, les événements et les compteurs pour les groupes de sécurité réseau | Microsoft Docs"
description: "Découvrez comment activer la journalisation des compteurs, des événements et des opérations pour les groupes de sécurité réseau"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 6beb9ae1b64e27df0a4eefefd592c7850efc7d2d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Analyse de journaux pour les groupes de sécurité réseau (NSG)

Vous pouvez activer les catégories de journaux de diagnostic suivantes pour les groupes de sécurité réseau :

* **Événements :** contient les entrées pour lesquelles des règles NSG sont appliquées aux machines virtuelles et aux rôles d’instance en fonction de l’adresse MAC. L’état de ces règles est collecté toutes les 60 secondes.
* **Journaux des compteurs :** contient les entrées pour le nombre de fois où chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

> [!NOTE]
> Les journaux de diagnostic ne sont disponibles que pour les groupes de sécurité réseau déployés avec le modèle de déploiement Azure Resource Manager. Vous ne pouvez pas activer la journalisation des diagnostics pour les groupes de sécurité réseau déployés via le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, reportez-vous à l’article [Présentation des modèles de déploiement Azure](../resource-manager-deployment-model.md).

La journalisation des activités (anciennement appelée audit ou journaux des opérations) est activé par défaut pour les groupes de sécurité réseau créés via un modèle de déploiement d’Azure. Pour déterminer les opérations qui ont été effectuées sur les groupes de sécurité réseau dans le journal d’activité, recherchez les entrées qui contiennent les types de ressources suivants : 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Lisez l’article [Vue d’ensemble du journal d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) pour en savoir plus sur les journaux d’activité. 

## <a name="enable-diagnostic-logging"></a>Activer la journalisation des diagnostics

La journalisation des diagnostics doit être activée pour *chaque* groupe de sécurité réseau dont vous souhaitez collecter des données. L’article [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) explique où les journaux de diagnostic peuvent être envoyés. Si vous n’avez pas de groupe de sécurité réseau, effectuez les étapes de l’article [Créer un groupe de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md) pour en créer un. Vous pouvez activer la journalisation des diagnostics des groupes de sécurité réseau à l’aide d’une des méthodes suivantes :

### <a name="azure-portal"></a>Portail Azure

Pour utiliser le portail pour activer la journalisation, connectez-vous au [portail](https://portal.azure.com). Cliquez sur **Tous les services**, puis tapez *groupes de sécurité réseau*. Sélectionnez le groupe de sécurité réseau pour lequel vous souhaitez activer la journalisation. Suivez les instructions pour les ressources hors calcul dans l’article [Activation des journaux de diagnostic dans le portail](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Sélectionnez **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter**, ou les deux catégories de journaux.

### <a name="powershell"></a>PowerShell

Pour utiliser PowerShell pour activer la journalisation, suivez les instructions de l’article [Activation des journaux de diagnostic via PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Évaluez les informations suivantes avant d’entrer une commande de l’article :

- Vous pouvez déterminer la valeur à utiliser pour le paramètre `-ResourceId` en remplaçant le texte suivant si nécessaire, puis en entrant la commande `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. La sortie de l’ID de la commande ressemble à */subscriptions/[ID d’abonnement]/resourceGroups/[groupe de ressources]/providers/Microsoft.Network/networkSecurityGroups/[nom du groupe]*.
- Si vous souhaitez uniquement collecter les données de la catégorie journal, ajoutez `-Categories [category]` à la fin de la commande dans l’article, où la catégorie est soit *NetworkSecurityGroupEvent*, soit *NetworkSecurityGroupRuleCounter*. Si vous n’utilisez pas le paramètre `-Categories`, la collecte des données est activée pour les deux catégories de journaux.

### <a name="azure-command-line-interface-cli"></a>Interface de ligne de commande Microsoft Azure

Pour utiliser l’interface CLI pour activer la journalisation, suivez les instructions de l’article [Activation des journaux de diagnostic via l’interface de ligne de commande](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Évaluez les informations suivantes avant d’entrer une commande de l’article :

- Vous pouvez déterminer la valeur à utiliser pour le paramètre `-ResourceId` en remplaçant le texte suivant si nécessaire, puis en entrant la commande `azure network nsg show [resource-group-name] [nsg-name]`. La sortie de l’ID de la commande ressemble à */subscriptions/[ID d’abonnement]/resourceGroups/[groupe de ressources]/providers/Microsoft.Network/networkSecurityGroups/[nom du groupe]*.
- Si vous souhaitez uniquement collecter les données de la catégorie journal, ajoutez `-Categories [category]` à la fin de la commande dans l’article, où la catégorie est soit *NetworkSecurityGroupEvent*, soit *NetworkSecurityGroupRuleCounter*. Si vous n’utilisez pas le paramètre `-Categories`, la collecte des données est activée pour les deux catégories de journaux.

## <a name="logged-data"></a>Données enregistrées

Les données sont écrites au format JSON pour les deux journaux. Les données spécifiques à chaque type de journal sont répertoriées dans les sections suivantes :

### <a name="event-log"></a>Journal des événements
Ce journal contient des informations sur les règles de groupe de sécurité réseau appliquées aux machines virtuelles et aux instances de rôle de service cloud, en fonction de l’adresse MAC. Les exemples de données suivants sont enregistrés pour chaque événement :

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Journal des compteurs de règle

Ce fichier journal contient des informations sur chaque règle appliquée aux ressources. L’exemple de données suivant est enregistré chaque fois qu’une règle est appliquée :

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Afficher et analyser les journaux

Pour savoir comment voir les données du journal d’activité, lisez l’article [Vue d’ensemble du journal d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Pour savoir comment voir les données des journaux de diagnostic, lisez l’article [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Si vous envoyez des données de diagnostic à Log Analytics, vous pouvez utiliser la solution de gestion [Azure Network Security Group Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) (version préliminaire) pour obtenir des données de meilleure qualité. 
