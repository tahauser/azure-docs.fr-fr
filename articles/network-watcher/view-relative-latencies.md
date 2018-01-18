---
title: "Afficher les latences relatives aux régions Azure à partir d’emplacements spécifiques | Microsoft Docs"
description: "Découvrez comment afficher les latences relatives sur des fournisseurs Internet pour les régions Azure à partir d’emplacements spécifiques."
services: network-watcher
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Afficher la latence relative aux régions Azure à partir d’emplacements spécifiques

Dans ce didacticiel, vous apprenez à utiliser le service Azure [Network Watcher](network-watcher-monitoring-overview.md) pour vous aider à déterminer dans quelle région Azure déployer votre application ou votre service, en fonction des données démographiques de vos utilisateurs. En outre, vous pouvez l’utiliser pour évaluer les connexions des fournisseurs de services à Azure.  
        
## <a name="create-a-network-watcher"></a>Créer un observateur réseau (network watcher)

Si vous disposez déjà d’un observateur réseau dans au moins une [région](https://azure.microsoft.com/regions) Azure, vous pouvez ignorer les tâches de cette section. Créez un groupe de ressources pour l’observateur réseau. Dans cet exemple, le groupe de ressources est créé dans la région Est des États-Unis, mais vous pouvez créer le groupe de ressources dans n’importe quelle région Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Créez un observateur réseau. Vous devez disposer d’un observateur réseau créé dans au moins une région Azure. Dans cet exemple, un observateur de réseau est créé dans la région Azure Est des États-Unis.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Comparer les latences réseau relatives à une seule région Azure à partir d’un emplacement spécifique

Évaluez les fournisseurs de services, ou aidez un utilisateur à résoudre un problème de type « le site était lent », à partir d’un emplacement spécifique à la région Azure où un service est déployé. Par exemple, la commande suivante retourne les latences relatives moyennes des fournisseurs de services Internet entre l’état de Washington aux États-Unis et la région Azure Ouest des États-Unis 2 entre le 13 et le 15 décembre 2017 :

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> La région que vous spécifiez dans la commande précédente n’a pas besoin de correspondre à la région que vous avez spécifiée lorsque vous avez extrait l’observateur réseau. La commande précédente nécessite seulement qu’un observateur réseau existant soit spécifié. L’observateur réseau peut se situer dans n’importe quelle région. Si vous spécifiez des valeurs pour `-Country` et `-State`, elles doivent être valides. Les valeurs respectent la casse. Les données sont disponibles pour un nombre limité de pays, d’états et de villes. Exécutez les commandes dans [Afficher les pays, les états, les villes et les fournisseurs disponibles](#view-available) pour afficher la liste des pays, villes et états disponibles à utiliser avec la commande précédente. 

> [!WARNING]
> Vous devez spécifier une date postérieure au 14 novembre 2017 pour `-StartTime` et `-EndTime`. En spécifiant une date avant le 14 novembre 2017, aucune donnée n’est retournée. 

Voici la sortie de la commande précédente :

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

Dans la sortie retournée, la valeur de **Score** est la latence relative entre les régions et les fournisseurs. Un score de 1 correspond à la pire latence (la plus élevée), tandis que 100 correspond à la latence la plus faible. Les latences relatives moyennes sont calculées sur la journée. Dans l’exemple précédent, alors qu’il est clair que les latences étaient les mêmes au cours des deux jours et qu’il existe une petite différence entre la latence des deux fournisseurs, il est également clair que les latences des deux fournisseurs sont faibles sur l’échelle allant de 1 à 100. Bien que ce comportement soit attendu, étant donné que l’état de Washington aux États-Unis est physiquement proche de la région Azure Ouest des États-Unis 2, parfois les résultats sont imprévisibles. Plus la plage de dates que vous spécifiez est étendue, plus vous pouvez calculer la latence moyenne dans le temps.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Comparer les latences réseau relatives entre des régions Azure à partir d’un emplacement spécifique

Si, au lieu de spécifier les latences relatives entre un emplacement spécifique et une région Azure spécifique à l’aide de `-Location`, vous souhaitez déterminer les latences relatives à toutes les régions Azure à partir d’un emplacement physique spécifique, vous pouvez le faire. Par exemple, la commande suivante vous permet d’évaluer dans quelle région Azure déployer un service si vos utilisateurs principaux sont des utilisateurs Comcast situés dans l’état de Washington :

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
Contrairement au fait de spécifier un emplacement unique, si vous ne spécifiez aucun emplacement ou si vous spécifiez plusieurs emplacements, par exemple « Ouest des États-Unis 2 », « Ouest des États-Unis », vous devez spécifier un fournisseur de services Internet lorsque vous exécutez la commande. 

## <a name="view-available"></a>Afficher les pays, les états, les villes et les fournisseurs disponibles

Les données sont disponibles pour des fournisseurs de services Internet, des pays, des états et des villes spécifiques. Pour afficher la liste de tous les fournisseurs de services Internet, les pays, les états et les villes disponibles, pour lequels vous pouvez afficher des données, entrez la commande suivante :

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Les données ne sont disponibles que pour les pays, les états et les villes retournés par la commande précédente. La commande précédente nécessite qu’un observateur réseau existant soit spécifié. L’exemple spécifiait l’observateur réseau *NetworkWatcher_eastus* dans un groupe de ressources nommé *NetworkWatcherRG*, mais vous pouvez spécifier n’importe quel observateur réseau existant. Si vous n’avez pas d’observateur réseau existant, créez-en un en effectuant les tâches indiquées sous [Créer un observateur réseau](#create-a-network-watcher). 

Après avoir exécuté la commande précédente, vous pouvez filtrer la sortie retournée en spécifiant des valeurs valides pour **Pays**, **État** et **Ville**, si vous le souhaitez.  Par exemple, pour afficher la liste des fournisseurs de services Internet disponibles à Seattle, Washington, aux États-Unis, entrez la commande suivante :

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> La valeur spécifiée pour **Pays** doit être en majuscules et en minuscules. Les valeurs spécifiées pour **État** et **Ville** doivent être en minuscules. Les valeurs doivent être répertoriées dans la sortie retournée après l’exécution de la commande sans valeurs pour **Pays**, **État** et **Ville**. Si la casse est incorrecte ou si vous entrez une valeur pour **Pays**, **État** ou **Ville** qui ne figure pas dans la sortie retournée après l’exécution de la commande sans valeurs pour ces propriétés, la sortie retournée est vide.
