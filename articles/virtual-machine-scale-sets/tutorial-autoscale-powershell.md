---
title: 'Didacticiel : mettre automatiquement à l’échelle un groupe identique avec Azure PowerShell | Microsoft Docs'
description: Découvrez comment mettre automatiquement à l’échelle un groupe de machines virtuelles identiques avec Azure PowerShell en fonction de l’augmentation et de la baisse des demandes d’UC
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 128f3d366dc7de0870bc6f52ae6d0bbaf3a0fcb3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Didacticiel : mettre à l’échelle automatiquement un groupe de machines virtuelles identiques avec Azure PowerShell
Lorsque vous créez un groupe identique, vous définissez le nombre d’instances de machine virtuelle que vous souhaitez exécuter. À mesure que la demande de votre application change, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machine virtuelle. La capacité de mise à l’échelle automatique vous permet de suivre la demande du client ou de répondre aux changements de performances de votre application tout au long de son cycle de vie. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser la mise à l’échelle automatique avec un groupe identique
> * Créer et utiliser des règles de mise à l’échelle automatique
> * Effectuer un test de contrainte sur les instances de machine virtuelle et déclencher des règles de mise à l’échelle automatique
> * Remettre à l’échelle automatiquement en cas de baisse de la demande

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.6.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="create-a-scale-set"></a>Créer un groupe identique
Pour faciliter la création des règles de mise à l’échelle, définissez des variables pour votre groupe identique. L’exemple suivant définit des variables pour le groupe identique nommé *myScaleSet* dans le groupe de ressources nommé *myResourceGroup* et dans la région *East US*. Votre abonnement ID est obtenu avec l’applet de commande [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Si plusieurs abonnements sont associés à votre compte, seul le premier est retourné. Ajustez les noms et l’ID d’abonnement comme suit :

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Définissez un nom d’utilisateur administrateur et un mot de passe pour les instances de machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

À présent, créez un groupe de machines virtuelles identiques avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé. L’équilibreur de charge inclut des règles pour distribuer le trafic sur le port TCP 80, ainsi que pour autoriser le trafic Bureau à distance sur le port TCP 3389 et le trafic Accès distant PowerShell sur le port TCP 5985 :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.

## <a name="create-a-rule-to-autoscale-out"></a>Créer une règle pour l’augmentation automatique
Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans votre groupe identique augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Vous contrôlez les métriques à surveiller, telles que l’usage du processeur ou du disque, la durée pendant laquelle la charge de l’application doit respecter un seuil donné, et le nombre d’instances de machine virtuelle à ajouter au groupe identique.

Créons avec l’applet de commande [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) une règle qui augmente le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est supérieure à 70 % pendant 5 minutes. Lorsque la règle se déclenche, le nombre d’instances de machine virtuelle est majoré de trois unités.

Les paramètres suivants sont utilisés pour cette règle :

| Paramètre               | Explication                                                                                                         | Valeur          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Métrique de performances à surveiller et sur laquelle appliquer des actions de groupe identique.                                                   | Percentage CPU |
| *-TimeGrain*            | Fréquence à laquelle les métriques sont collectées à des fins d’analyse.                                                                   | 1 minute       |
| *-MetricStatistic*      | Définit la manière dont les métriques collectées doivent être agrégées à des fins d’analyse.                                                | Moyenne        |
| *-TimeWindow*           | Temps de surveillance avant que les valeurs de métrique et de seuil soient comparées.                                   | 5 minutes      |
| *-Operator*             | Opérateur utilisé pour comparer les données de métrique au seuil.                                                     | Supérieur à   |
| *-Threshold*            | Valeur qui amène la règle de mise à l’échelle automatique à déclencher une action.                                                      | 70 %            |
| *-ScaleActionDirection* | Définit si le groupe identique doit augmenter ou réduire l’échelle lorsque la règle s’applique.                                             | Augmenter       |
| *–ScaleActionScaleType* | Indique que le nombre d’instances de machine virtuelle doit être modifié par une certaine valeur.                                    | Nombre de modifications   |
| *-ScaleActionValue*     | Le pourcentage d’instances de machine virtuelle doit être modifié lorsque la règle se déclenche.                                            | 3              |
| *-ScaleActionCooldown*  | Temps d’attente avant que la règle soit appliquée à nouveau afin que les actions de mise à l’échelle automatique aient le temps de porter effet. | 5 minutes      |

L’exemple suivant crée un objet nommé *myRuleScaleOut* qui contient cette règle d’augmentation du nombre d’instances. Le paramètre *-MetricResourceId* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Créer une règle pour la diminution automatique
Au cours d’une soirée ou d’un week-end, la demande de votre application peut diminuer. Si cette charge réduite est constante pendant un certain temps, vous pouvez configurer des règles de mise à l’échelle automatique pour réduire le nombre d’instances de machine virtuelle dans le groupe identique. Cette action de diminution du nombre d’instances a pour effet de réduire le coût d’exécution de votre groupe identique, car vous seul exécutez le nombre d’instances requis pour répondre à la demande en cours.

Utilisez [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) pour créer une autre règle qui diminue le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge d’UC moyenne est inférieure à 30 % pendant 5 minutes. Lorsque la règle se déclenche, le nombre d’instances de machine virtuelle est réduit d’une unité. L’exemple suivant montre la création d’un objet nommé *myRuleScaleDown* qui contient cette règle de montée en puissance. Le paramètre *-MetricResourceId* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Définir un profil de mise à l’échelle automatique
Pour associer vos règles de mise à l’échelle automatique avec un groupe identique, créez un profil. Le profil de mise à l’échelle automatique définit les capacités par défaut, minimale et maximale de groupe identique, et associe vos règles de mise à l’échelle automatique. Créez un profil de mise à l’échelle automatique avec l’applet de commande [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). L’exemple suivant définit les capacités par défaut et minimale de *2* instances de machine virtuelle, et la capacité maximale de *10*. Les règles d’augmentation et de diminution du nombre d’instances créées dans les étapes précédentes sont ensuite associées :

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Appliquer des règles de mise à l’échelle automatique à un groupe identique
L’étape finale consiste à appliquer le profil de mise à l’échelle automatique à votre groupe identique. Votre groupe identique peut ensuite augmenter ou diminuer automatiquement en fonction de la demande de l’application. Appliquez le profil de mise à l’échelle automatique avec l’applet de commande [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) comme suit :

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Générer une charge d’UC sur un groupe identique
Pour tester les règles de mise à l’échelle automatique, générez des charges d’UC sur les instances de machine virtuelle dans le groupe identique. Cette charge d’UC simulée provoque la mise à l’échelle automatique et donc l’augmentation du nombre d’instances de machine virtuelle. Comme la charge d’UC simulée diminue ensuite, les règles de mise à l’échelle automatique réduisent le nombre d’instances de machine virtuelle.

Pour répertorier les ports NAT permettant une connexion à des instances de machine virtuelle dans un groupe identique, obtenez tout d’abord l’objet d’équilibrage de charge avec [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Affichez ensuite les règles NAT entrantes avec [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) :

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

L’exemple de sortie suivant affiche le nom de l’instance, l’adresse IP publique de l’équilibreur de charge et le numéro de port où les règles NAT transfèrent le trafic :

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Le *nom* de la règle s’aligne avec le nom de l’instance de machine virtuelle indiqué dans une commande [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) précédente. Par exemple, pour vous connecter à une instance de machine virtuelle *0*, vous utilisez *myRDPRule.0* et vous vous connectez au port *50001*. Pour vous connecter à une instance de machine virtuelle *1*, utilisez la valeur tirée de *myRDPRule.1* et connectez-vous au port *50002*.

Affichez l’adresse IP publique de l’équilibreur de charge avec [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Exemple de sortie :

```powershell
IpAddress
---------
52.168.121.216
```

Créez une connexion distante vers votre première instance de machine virtuelle. Spécifiez votre propre adresse IP publique et le numéro de port de l’instance de machine virtuelle requise, comme indiqué dans les commandes précédentes. Lorsque vous y êtes invité, entrez les informations d’identification utilisées lors de la création du groupe identique (par défaut dans les exemples de commandes, il s’agit de *azureuser* et *P@ssw0rd!*). Si vous utilisez Azure Cloud Shell, effectuez cette étape à partir d’une invite de commandes PowerShell locale ou du client Bureau à distance. L’exemple suivant établit une connexion à une instance de machine virtuelle *0* :

```powershell
mstsc /v 52.168.121.216:50001
```

Une fois que vous êtes connecté, ouvrez Internet Explorer à partir de la barre des tâches.

- Sélectionner **OK** pour accepter l’option *Utiliser les paramètres de sécurité, de confidentialité et de compatibilité recommandés*
- Entrez *http://download.sysinternals.com/files/CPUSTRES.zip* dans la barre d’adresses.
- Lorsque la configuration de sécurité renforcée d’Internet Explorer est activée, choisissez d’**ajouter** le domaine *http://download.sysinternals.com* à votre liste de sites de confiance.
- Lorsque vous êtes invité à télécharger le fichier, sélectionnez **Ouvrir**, puis sélectionnez et **exécutez** l’outil *CPUSTRES. EXE*.

Pour générer une charge d’UC, activez les deux cases à cocher des threads **Actif**. Dans le menu déroulant **Activité** des deux threads, sélectionnez *Maximum*. Vous pouvez ouvrir le gestionnaire des tâches pour vérifier que la charge d’UC sur la machine virtuelle est à 100 %.

![L’utilitaire CPU Stress génère une charge sur l’instance de machine virtuelle](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Laissez la session de connexion au Bureau à distance ouverte et ouvrez une autre session de connexion au Bureau à distance. Connectez-vous à la deuxième instance de machine virtuelle avec le numéro de port indiqué par le biais de la cmdlet [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) précédente :

```powershell
mstsc /v 52.168.121.216:50002
```

Une fois que vous êtes connecté à la deuxième instance de machine virtuelle, répétez les étapes précédentes pour télécharger et exécuter *CPUSTRES.EXE*. Une fois encore, démarrez deux threads **Actif** et définissez l’activité sur *Maximum*.

Pour permettre à l’outil **CPU Stress** de continuer à s’exécuter, laissez les deux sessions de connexion au Bureau à distance ouvertes.


## <a name="monitor-the-active-autoscale-rules"></a>Surveiller les règles de mise à l’échelle automatique actives
Pour surveiller le nombre d’instances de machine virtuelle dans votre groupe identique, utilisez **while**. Les règles de mise à l’échelle automatique prennent 5 minutes pour commencer le processus de montée en puissance en réponse à la charge d’UC générée par **CPUStress* sur chacune des instances de machine virtuelle :

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Une fois que le seuil de l’UC est atteint, les règles de mise à l’échelle automatique augmentent le nombre d’instances de machine virtuelle au sein du groupe identique. La sortie suivante montre trois machines virtuelles créées au moment de la montée en puissance automatique du groupe identique :

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Dans votre session de connexion au Bureau à distance pour chacune de vos instances de machine virtuelle, fermez l’outil **CPU Stress**. La charge d’UC moyenne du groupe identique revient à la normale. Lorsque 5 autres minutes ont passé, les règles de mise à l’échelle automatique diminuent le nombre d’instances de machine virtuelle. La diminution commence par la suppression des instances de machine virtuelle disposant des ID les plus élevés. La sortie d’exemple suivante montre une instance de machine virtuelle supprimée lors de la diminution automatique :

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Quittez *while* avec `Ctrl-c`. Le groupe identique continue à diminuer toutes les 5 minutes et supprime une instance de machine virtuelle jusqu’à ce que la quantité minimale d’instances (2) soit atteinte.


## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer votre groupe identique et les ressources supplémentaires, supprimez le groupe de ressources et toutes ses ressources avec [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Le paramètre `-Force` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin. Le paramètre `-AsJob` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à mettre automatiquement à l’échelle un groupe identique avec Azure PowerShell :

> [!div class="checklist"]
> * Utiliser la mise à l’échelle automatique avec un groupe identique
> * Créer et utiliser des règles de mise à l’échelle automatique
> * Effectuer un test de contrainte sur les instances de machine virtuelle et déclencher des règles de mise à l’échelle automatique
> * Remettre à l’échelle automatiquement en cas de baisse de la demande

Pour plus d’exemples de groupes identiques de machines virtuelles en action, consultez les exemples suivants de scripts Azure PowerShell :

> [!div class="nextstepaction"]
> [Exemples de scripts de groupe identique pour Azure PowerShell](powershell-samples.md)