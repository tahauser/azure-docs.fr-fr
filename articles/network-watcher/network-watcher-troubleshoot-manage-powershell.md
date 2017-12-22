---
title: "Résoudre les problèmes liés à la passerelle de réseau virtuel et aux connexions Azure - PowerShell | Microsoft Docs"
description: "Cette page explique comment utiliser Azure Network Watcher pour résoudre les problèmes liés à l’applet de commande PowerShell"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: d7ae5599b3fa1876e2b5af79f56548cd17c1c8ed
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Résoudre les problèmes liés à la passerelle de réseau virtuel et aux connexions par le biais de PowerShell d’Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portail](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Le service Network Watcher offre de nombreuses fonctionnalités en lien avec la bonne compréhension de vos ressources réseau dans Azure. Il permet notamment de résoudre les problèmes liés aux ressources. Vous pouvez appeler la solution de résolution des problèmes de ressources par le biais du portail, de PowerShell, de l’interface de ligne de commande ou de l’API REST. Lorsque cette fonctionnalité est appelée, Network Watcher inspecte l’intégrité d’une passerelle de réseau virtuel ou d’une connexion et renvoie ses résultats.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher.

Vous trouverez la liste des types de passerelles pris en charge sur la page [Types de passerelles pris en charge](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Vue d'ensemble

La résolution des problèmes liés aux ressources offre la possibilité de résoudre les problèmes qui surviennent avec les passerelles de réseau virtuel et les connexions. Lorsqu’une demande de résolution des problèmes liés aux ressources est faite, les journaux sont interrogés et inspectés. Lorsque l’inspection est terminée, les résultats sont renvoyés. Les demandes de résolution des problèmes liés aux ressources sont longues : vous devrez peut-être patienter plusieurs minutes avant d’obtenir un résultat. Les journaux de dépannage sont stockés dans un conteneur sur un compte de stockage spécifié.

## <a name="retrieve-network-watcher"></a>Récupérer Network Watcher

La première étape consiste à récupérer l’instance Network Watcher. La variable `$networkWatcher` est transmise à l’applet de commande `Start-AzureRmNetworkWatcherResourceTroubleshooting` lors de l’étape 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Récupérer une connexion de passerelle de réseau virtuel

Dans cet exemple, la résolution des problèmes liés aux ressources s’effectue sur une connexion. Vous pouvez également la transférer à une passerelle de réseau virtuel.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

La résolution des problèmes liés aux ressources renvoie des données concernant l’intégrité de la ressource, et elle enregistre les journaux dans un compte de stockage qui fera l’objet d’une révision. À cette étape, nous créons un compte de stockage. Si vous disposez déjà d’un compte de stockage existant, vous pouvez l’utiliser.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Procéder à la résolution des problèmes liés aux ressources Network Watcher

Résolvez les problèmes liés aux ressources avec l’applet de commande `Start-AzureRmNetworkWatcherResourceTroubleshooting`. Nous transmettons à l’applet de commande l’objet Network Watcher, l’ID de la connexion ou la passerelle de réseau virtuel, l’ID de compte de stockage et le chemin d’accès pour stocker les résultats.

> [!NOTE]
> L’exécution de l’applet de commande `Start-AzureRmNetworkWatcherResourceTroubleshooting` est longue et peut prendre quelques minutes.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Une fois l’applet de commande exécutée, Network Watcher passe en revue la ressource pour vérifier son intégrité. Les résultats sont renvoyés à l’interpréteur de commandes, et les journaux des résultats sont stockés dans le compte de stockage spécifié.

## <a name="understanding-the-results"></a>Compréhension des résultats

Le texte d’action fournit des indications générales sur la façon de résoudre le problème. Si une action peut être entreprise pour résoudre le problème, un lien est fourni avec des indications supplémentaires. En l’absence d’indications supplémentaires, la réponse fournit l’URL permettant d’ouvrir un dossier de support.  Pour plus d’informations sur les propriétés de la réponse et sur ce qu’elle contient, consultez [Network Watcher Troubleshoot overview (Vue d’ensemble de la résolution des problèmes Network Watcher)](network-watcher-troubleshoot-overview.md)

Pour obtenir des instructions de téléchargement des fichiers à partir des comptes de stockage Azure, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). L’explorateur de stockage peut aussi être utilisé. Pour en savoir plus sur l’explorateur de stockage, cliquez sur le lien suivant : [Explorateur de stockage](http://storageexplorer.com/)

## <a name="next-steps"></a>Étapes suivantes

Si les paramètres ont été modifiés et arrêtent la connectivité VPN, consultez la page [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) afin d’effectuer le suivi du groupe de sécurité réseau et des règles de sécurité pouvant être concernés.
