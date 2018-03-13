---
title: "Mettre à l’échelle automatiquement des groupes de machines virtuelles identiques à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Comment créer des règles de mise à l’échelle automatique pour des groupes de machines virtuelles identiques avec Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques avec Azure PowerShell
Lorsque vous créez un groupe identique, vous définissez le nombre d’instances de machine virtuelle que vous souhaitez exécuter. À mesure que la demande de votre application change, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machine virtuelle. La capacité de mise à l’échelle automatique vous permet de suivre la demande du client ou de répondre aux changements de performances de votre application tout au long de son cycle de vie.

Cet article explique comment créer avec Azure PowerShell des règles de mise à l’échelle automatique qui analysent les performances des instances de machine virtuelle dans votre groupe identique. Ces règles de mise à l’échelle augmentent ou réduisent le nombre d’instances de machine virtuelle en réponse à ces métriques de performances. Vous pouvez également accomplir ces étapes avec [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) ou dans le [portail Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>configuration requise
Pour créer des règles de mise à l’échelle, vous avez besoin d’un groupe de machines virtuelles identiques. Vous pouvez créer un groupe identique avec le [portail Azure](virtual-machine-scale-sets-create-portal.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) ou [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md).

Pour faciliter la création des règles de mise à l’échelle, définissez des variables pour votre groupe identique. L’exemple suivant définit des variables pour le groupe identique nommé *myScaleSet* dans le groupe de ressources nommé *myResourceGroup* et dans la région *East US*. Votre abonnement ID est obtenu avec l’applet de commande [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Si plusieurs abonnements sont associés à votre compte, seul le premier est retourné. Ajustez les noms et l’ID d’abonnement comme suit :

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Créer une règle d’augmentation automatique du nombre d’instances
Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans votre groupe identique augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Vous contrôlez les métriques à surveiller, telles que l’usage du processeur ou du disque, la durée pendant laquelle la charge de l’application doit respecter un seuil donné, et le nombre d’instances de machine virtuelle à ajouter au groupe identique.

Créons avec l’applet de commande [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) une règle qui augmente le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est supérieure à 70 % pendant 5 minutes. Lorsque la règle déclenche l’opération, le nombre d’instances de machine virtuelle est augmenté de 20 %. Dans des groupes identiques comportant un petit nombre d’instances de machine virtuelle, vous pouvez ignorer `-ScaleActionScaleType` et spécifier uniquement `-ScaleActionValue` pour augmenter de *1* ou *2* le nombre d’instances. Dans des groupes identiques comportant un grand nombre d’instances de machine virtuelle, une augmentation de 10 ou 20 % d’instances de machine virtuelle peut être plus appropriée.

Les paramètres suivants sont utilisés pour cette règle :

| Paramètre               | Explication                                                                                                         | Valeur          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Métrique de performances à surveiller et sur laquelle appliquer des actions de groupe identique.                                                   | Percentage CPU |
| *-TimeGrain*            | Fréquence à laquelle les métriques sont collectées à des fins d’analyse.                                                                   | 1 minute       |
| *-MetricStatistic*      | Définit la manière dont les métriques collectées doivent être agrégées à des fins d’analyse.                                                | Moyenne        |
| *-TimeWindow*           | Temps de surveillance avant que les valeurs de métrique et de seuil soient comparées.                                   | 10 minutes      |
| *-Operator*             | Opérateur utilisé pour comparer les données de métrique au seuil.                                                     | Supérieur à   |
| *-Threshold*            | Valeur qui amène la règle de mise à l’échelle automatique à déclencher une action.                                                      | 70 %            |
| *-ScaleActionDirection* | Définit si le groupe identique doit augmenter ou réduire l’échelle lorsque la règle s’applique.                                             | Augmenter       |
| *–ScaleActionScaleType* | Indique que le nombre d’instances de machine virtuelle doit être modifié d’un certain pourcentage.                                 | Pourcentage de modification |
| *-ScaleActionValue*     | Le pourcentage d’instances de machine virtuelle doit être modifié lorsque la règle se déclenche.                                            | 20             |
| *-ScaleActionCooldown*  | Temps d’attente avant que la règle soit appliquée à nouveau afin que les actions de mise à l’échelle automatique aient le temps de porter effet. | 5 minutes      |

L’exemple suivant crée un objet nommé *myRuleScaleOut* qui contient cette règle d’augmentation du nombre d’instances. Le paramètre *-MetricResourceId* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Créer une règle pour diminuer automatiquement le nombre d’instances
Au cours d’une soirée ou d’un week-end, la demande de votre application peut diminuer. Si cette charge réduite est constante pendant un certain temps, vous pouvez configurer des règles de mise à l’échelle automatique pour réduire le nombre d’instances de machine virtuelle dans le groupe identique. Cette action de diminution du nombre d’instances a pour effet de réduire le coût d’exécution de votre groupe identique, car vous seul exécutez le nombre d’instances requis pour répondre à la demande en cours.

Créez avec l’applet de commande [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) une règle qui diminue le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est inférieure à 30 % pendant 10 minutes. Lorsque la règle déclenche, le nombre d’instances de machine virtuelle est réduit de 20 %. L’exemple suivant crée un objet nommé *myRuleScaleDown* qui contient cette règle de diminution du nombre d’instances. Le paramètre *-MetricResourceId* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Définir un profil de mise à l’échelle automatique
Pour associer vos règles de mise à l’échelle automatique avec un groupe identique, vous créez un profil. Le profil de mise à l’échelle automatique définit les capacités par défaut, minimale et maximale de groupe identique, et associe vos règles de mise à l’échelle automatique. Créez un profil de mise à l’échelle automatique avec l’applet de commande [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). L’exemple suivant définit les capacités par défaut et minimale de *2* instances de machine virtuelle, et la capacité maximale de *10*. Les règles d’augmentation et de diminution du nombre d’instances créées dans les étapes précédentes sont ensuite associées :

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Appliquer des règles de mise à l’échelle automatique à un groupe identique
L’étape finale consiste à appliquer le profil de mise à l’échelle automatique à votre groupe identique. L’échelle peut ensuite augmenter ou diminuer automatiquement en fonction de la demande de l’application. Appliquez le profil de mise à l’échelle automatique avec l’applet de commande [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) comme suit :

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Surveiller le nombre d’instances dans un groupe identique
Pour voir le nombre et l’état des instances de machine virtuelle, affichez la liste des instances dans votre groupe identique en utilisant l’applet d commande [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). L’état indique si l’instance de la machine virtuelle approvisionne à mesure que le groupe identique augmente automatiquement le nombre d’instances, ou déprovisionne à mesure que le groupe identique diminue automatiquement le nombre d’instances. L’exemple suivant présente l’état d’instance de machine virtuelle pour le groupe identique nommé *myScaleSet* dans le groupe de ressources nommé *myResourceGroup* :

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Mise à l’échelle automatique en fonction d’une planification
Les exemples précédents ont automatiquement mis à l’échelle un groupe identique en fonction de métriques de base de l’ordinateur hôte, telles que l’utilisation du processeur. Vous pouvez également créer des règles de mise à l’échelle automatique basées sur des planifications. Ces règles basées sur une planification vous permettent d’augmenter automatiquement le nombre d’instances de machine virtuelle avant une augmentation anticipée de la demande d’une application, par exemple, aux heures de travail habituelles, et diminuer automatiquement le nombre d’instances aux heures auxquelles vous anticipez moins de demande, par exemple, durant le weekend.

Pour créer des règles de mise à l’échelle automatique basées sur une planification plutôt que sur des métriques de l’hôte, utilisez le portail Azure. Il n’est pas possible actuellement de créer des règles basées sur une planification avec Azure PowerShell.


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser des règles de mise à l’échelle automatique pour mettre à l’échelle horizontalement, et augmenter ou diminuer le *nombre* d’instances de machine virtuelle dans votre groupe identique. Vous pouvez également mettre à l’échelle verticalement pour augmenter ou diminuer la *taille* d’instance de machine virtuelle. Pour plus d’informations, voir [Mise à l’échelle verticale avec des groupes de machines virtuelles identiques](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Pour plus d’informations sur la gestion de vos instances de machine virtuelle, voir [Gérer les groupes de machines virtuelles identiques avec Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Pour découvrir comment générer des alertes lorsque vos règles de mise à l’échelle automatique déclenchent, voir [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Vous pouvez également [Utiliser des journaux d’audit pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
