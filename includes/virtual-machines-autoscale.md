Vous pouvez facilement [mettre automatiquement à l’échelle](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) vos [machines virtuelles (VM)](../articles/virtual-machines/windows/overview.md) lorsque vous utilisez [les groupes de machine virtuelles identiques](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) et [la fonctionnalité de mise à l’échelle automatique de Azure Monitor](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Vos machines virtuelles doivent être membres d’un groupe identique pour pouvoir être mises à l’échelle automatiquement. Cet article fournit des informations vous permettant de mieux comprendre comment mettre vos machines virtuelles à l’échelle, verticalement et horizontalement, à l’aide de méthodes automatique et manuelle.

## <a name="horizontal-or-vertical-scaling"></a>Mise à l’échelle horizontale ou verticale

La fonctionnalité de mise à l’échelle automatique de Azure Monitor s’effectue uniquement horizontalement, ce qui correspond à une augmentation (« out ») ou à une diminution (« in ») du nombre de machine virtuelle. La mise à l’échelle horizontale est plus flexible dans un environnement cloud, car elle vous permet d’exécuter des milliers de machines virtuelles pour gérer la charge. Vous mettez à l’échelle horizontalement en modifiant automatiquement ou manuellement la capacité (ou le nombre d’instances) du groupe identique. 

La mise à l’échelle verticale conserve le même nombre de machines virtuelles, mais rend la machine virtuelle plus (« up ») ou moins (« down ») puissante. La puissance est mesurée par des attributs tels que la mémoire, la vitesse du processeur ou l’espace disque. La mise à l’échelle verticale dépend de la disponibilité d’un matériel plus puissant, qui peut atteindre rapidement sa limite et varier d’une région à l’autre. La mise à l’échelle verticale nécessite généralement un arrêt et un redémarrage de la machine virtuelle. Vous faites une mise à l’échelle verticale en définissant une nouvelle taille dans la configuration des machines virtuelles au sein du groupe identique.

À l’aide de Runbooks au sein de [Azure Automation](../articles/automation/automation-intro.md), vous pouvez facilement [mettre à l’échelle des machines virtuelles dans un groupe identique](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) vers le haut ou vers le bas.

## <a name="create-a-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Les jeux de mise à l’échelle facilitent le déploiement et la gestion de machines virtuelles identiques en tant qu’ensemble. Vous pouvez créer des jeux de mise à l’échelle Linux ou Windows à l’aide du [portail Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), de [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), ou de [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md). Vous pouvez également créer et gérer des jeux de mise à l’échelle avec des SDK tels que [Python](/develop/python) ou [Node.js](/nodejs/azure), ou bien directement avec des [API REST](/rest/api/compute/virtualmachinescalesets). La mise à l’échelle automatique des machines virtuelles s’effectue en appliquant des mesures et des règles au jeu de mise à l’échelle.

## <a name="configure-autoscale-for-a-scale-set"></a>Configurer la mise à l’échelle automatique pour un jeu de mise à l’échelle

La mise à l’échelle automatique fournit le nombre approprié de machines virtuelles pour gérer la charge sur votre application. Elle vous permet d’ajouter des machines virtuelles pour gérer les augmentations de charge et économiser de l’argent en supprimant les machines virtuelles qui sont inactives. Vous spécifiez un nombre minimal et maximal de machines virtuelles à exploiter sur un ensemble de règles. Avoir un nombre minimal d’instances permet de vous assurer que votre application est toujours en cours d’exécution même en l’absence de charge. Avoir une valeur maximale d’instances limite votre coût total possible par heure.

Vous pouvez activer la mise à l’échelle automatique lorsque vous créez un jeu de mise à l’échelle à l’aide de [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) ou de [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Vous pouvez également l’activer après avoir créé le jeu de mise à d’échelle. Vous pouvez créer un jeu de mise à l’échelle, installer l’extension et configurer la mise à l’échelle automatique à l’aide d’un [modèle Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Dans le portail Azure, activez la mise à l’échelle automatique depuis Azure Monitor, ou bien à partir des paramètres du jeu de mise à l’échelle.

![Activer la mise à l’échelle automatique](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Mesures

La fonctionnalité de mise à l’échelle automatique d’Azure Monitor vous permet de mettre à l’échelle le nombre de machines virtuelles en cours d’exécution vers le haut ou vers le bas en fonction des [mesures](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Par défaut, les machines virtuelles fournissent des mesures de base au niveau hôte pour l’utilisation du disque dur, du réseau et du processeur. Lorsque vous configurez la collecte de données de diagnostic à l’aide de l’extension de diagnostic, des compteurs de performances supplémentaires du système d’exploitation invité sont disponibles pour le disque dur, le processeur et la mémoire.

![Critères de mesures](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Si votre application doit effectuer une mise à l’échelle en fonction de mesures qui ne sont pas disponibles via l’hôte, [l’extension de diagnostics Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou [l’extension de diagnostics Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) doit être installée sur les machines virtuelles du jeu de mise à l’échelle. Si vous créez un jeu de mise à l’échelle à l’aide du portail Azure, vous devez également utiliser Azure PowerShell ou Azure CLI pour installer l’extension avec la configuration de diagnostics dont vous avez besoin.
 
### <a name="rules"></a>Règles

Les [règles](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) combinent une mesure avec une action à effectuer. Lorsque les conditions relatives aux règles sont remplies, une ou plusieurs actions de mise à l’échelle automatique sont déclenchées. Par exemple, vous pouvez définir une règle augmentant le nombre de machines virtuelles de 1 si l’utilisation moyenne du processeur dépasse 85 pour cent.

![Actions de mise à l’échelle automatique](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Notifications

Vous pouvez [définir des déclencheurs](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) afin d’appeler des URL web spécifiques ou pour envoyer des messages électroniques en fonction des règles de mise à l’échelle que vous créez. Les webhooks vous permettent d’acheminer les notifications d’alerte Azure vers d’autres systèmes afin qu’elles soient post-traitées ou personnalisées.

## <a name="manually-scale-vms-in-a-scale-set"></a>Mettre manuellement à l’échelle des machines virtuelles dans un jeu de mise à l’échelle

### <a name="horizontal"></a>Horizontal

Vous pouvez ajouter ou supprimer des machines virtuelles en modifiant la capacité du jeu de mise à l’échelle. Dans le portail Azure, vous pouvez réduire ou augmenter le nombre de machines virtuelles (indiqué en tant que **nombre d’instances**) dans le jeu de mise à l’échelle en faisant glisser la barre de condition de remplacement sur l’écran de mise à l’échelle gauche ou droit.

À l’aide d’Azure PowerShell, vous devez obtenir l’objet du jeu de mise à l’échelle en utilisant [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Vous définissez ensuite la propriété **sku.capacity** pour le nombre de machines virtuelles que vous souhaitez et vous mettez à jour le jeu de mise à l’échelle avec [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). À l’aide d’Azure CLI, vous modifiez la capacité avec le paramètre**--new-capacity** pour la commande [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale).

### <a name="vertical"></a>Vertical

Vous pouvez modifier manuellement la taille des machines virtuelles dans le portail Azure depuis l’écran Taille du jeu de mise à l’échelle. Vous pouvez utiliser Azure PowerShell avec Get-AzureRmVmss, définissant la propriété SKU de référence d’image, puis utilisant [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) et [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les jeux de mise à l’échelle dans [Considérations relatives à la conception des jeux de mise à l’échelle](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

