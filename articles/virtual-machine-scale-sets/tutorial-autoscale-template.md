---
title: 'Didacticiel : mettre automatiquement à l’échelle un groupe identique avec des modèles Azure | Microsoft Docs'
description: Découvrez comment utiliser les modèles Azure Resource Manager pour mettre automatiquement à l’échelle un groupe de machines virtuelles identiques en fonction de l’augmentation et de la baisse des demandes de l’UC
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
ms.openlocfilehash: 97c92a68009a378d13daed35520c7d338c5b932a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Didacticiel : mettre automatiquement à l’échelle un groupe de machines virtuelles identiques avec un modèle Azure
Lorsque vous créez un groupe identique, vous définissez le nombre d’instances de machine virtuelle que vous souhaitez exécuter. À mesure que la demande de votre application change, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machine virtuelle. La capacité de mise à l’échelle automatique vous permet de suivre la demande du client ou de répondre aux changements de performances de votre application tout au long de son cycle de vie. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser la mise à l’échelle automatique avec un groupe identique
> * Créer et utiliser des règles de mise à l’échelle automatique
> * Effectuer un test de contrainte sur les instances de machine virtuelle et déclencher des règles de mise à l’échelle automatique
> * Remettre à l’échelle automatiquement en cas de baisse de la demande

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.29 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="define-an-autoscale-profile"></a>Définir un profil de mise à l’échelle automatique
Vous définissez un profil de mise à l’échelle automatique dans un modèle Azure avec le fournisseur de ressources *Microsoft.insights/autoscalesettings*. Un *profil* détaille la capacité du groupe identique et toutes les règles associées. L’exemple suivant définit un profil nommé *Autoscale by percentage based on CPU usage* et définit la capacité par défaut de *2* instances de machine virtuelle minimum et de *10* instances de machine virtuelle maximum :

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2014-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Définir une règle pour l’augmentation automatique
Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans votre groupe identique augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Vous contrôlez les métriques à surveiller, telles que l’usage du processeur ou du disque, la durée pendant laquelle la charge de l’application doit respecter un seuil donné, et le nombre d’instances de machine virtuelle à ajouter au groupe identique.

Dans l’exemple suivant, une règle est définie afin d’augmenter le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge d’UC moyenne est supérieure à 70 % pendant 5 minutes. Lorsque la règle se déclenche, le nombre d’instances de machine virtuelle est majoré de trois unités.

Les paramètres suivants sont utilisés pour cette règle :

| Paramètre         | Explication                                                                                                         | Valeur           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Métrique de performances à surveiller et sur laquelle appliquer des actions de groupe identique.                                                   | Percentage CPU  |
| *timeGrain*       | Fréquence à laquelle les métriques sont collectées à des fins d’analyse.                                                                   | 1 minute        |
| *timeAggregation* | Définit la manière dont les métriques collectées doivent être agrégées à des fins d’analyse.                                                | Moyenne         |
| *timeWindow*      | Temps de surveillance avant que les valeurs de métrique et de seuil soient comparées.                                   | 5 minutes       |
| *operator*        | Opérateur utilisé pour comparer les données de métrique au seuil.                                                     | Supérieur à    |
| *threshold*       | Valeur qui amène la règle de mise à l’échelle automatique à déclencher une action.                                                      | 70 %             |
| *direction*       | Définit si le groupe identique doit être augmenté ou réduit lorsque la règle s’applique.                                              | Augmenter        |
| *type*            | Indique que le nombre d’instances de machine virtuelle doit être modifié par une certaine valeur.                                    | Nombre de modifications    |
| *value*           | Nombre d’instances de machine virtuelle à ajouter ou à supprimer lorsque la règle s’applique.                                             | 3               |
| *cooldown*        | Temps d’attente avant que la règle soit appliquée à nouveau afin que les actions de mise à l’échelle automatique aient le temps de porter effet. | 5 minutes       |

La règle suivante est ajoutée dans la section de profil du fournisseur de ressources *Microsoft.insights/autoscalesettings* à partir de la section précédente :

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Définir une règle pour la diminution automatique
Au cours d’une soirée ou d’un week-end, la demande de votre application peut diminuer. Si cette charge réduite est constante pendant un certain temps, vous pouvez configurer des règles de mise à l’échelle automatique pour réduire le nombre d’instances de machine virtuelle dans le groupe identique. Cette action de diminution du nombre d’instances a pour effet de réduire le coût d’exécution de votre groupe identique, car vous seul exécutez le nombre d’instances requis pour répondre à la demande en cours.

L’exemple suivant définit une règle qui diminue le nombre d’instances de machine virtuelle d’une unité lorsque la charge d’UC moyenne est inférieure à 30 % pendant 5 minutes. Cette règle s’ajoute au profil de mise à l’échelle automatique après la règle précédente d’augmentation de la taille des instances :

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Créer un groupe identique avec mise à l’échelle automatique
Nous allons utiliser un exemple de modèle pour créer un groupe identique et appliquer des règles de mise à l’échelle automatique. Vous pouvez [examiner le modèle complet](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) ou [consulter la section dédiée au fournisseur de ressources *Microsoft.insights/autoscalesettings* ](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) du modèle.

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Créez à présent un groupe de machines virtuelles identiques avec [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Lorsque vous y êtes invité, fournissez votre propre nom d’utilisateur, comme *azureuser* et le mot de passe que vous avez utilisés comme informations d’identification pour chaque instance de machine virtuelle :

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="generate-cpu-load-on-scale-set"></a>Générer une charge d’UC sur un groupe identique
Pour tester les règles de mise à l’échelle automatique, générez des charges d’UC sur les instances de machine virtuelle dans le groupe identique. Cette charge d’UC simulée provoque la mise à l’échelle automatique et donc l’augmentation du nombre d’instances de machine virtuelle. Comme la charge d’UC simulée diminue ensuite, les règles de mise à l’échelle automatique réduisent le nombre d’instances de machine virtuelle.

Tout d’abord, affichez l’adresse et les ports à connecter aux instances de machine virtuelle d’un groupe identique avec la commande [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) :

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

L’exemple de sortie suivant affiche le nom de l’instance, l’adresse IP publique de l’équilibreur de charge et le numéro de port où les règles de traduction d’adresse réseau (NAT) transfèrent le trafic :

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH vers votre première instance de machine virtuelle. Spécifiez votre propre adresse IP publique et le numéro de port avec le paramètre `-p`, comme indiqué dans la commande précédente :

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Une fois connecté, installez l’utilitaire **stress**. Démarrez *10* rôles de travail **stress** qui génèrent une charge d’UC. Ces rôles de travail sont exécutés pendant *420* secondes, ce qui est suffisant pour que les règles de mise à l’échelle automatique implémentent l’action souhaitée.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Lorsque **stress** affiche une sortie semblable à *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, appuyez sur la touche *Entrée* pour revenir à l’invite de commandes.

Pour vérifier que **stress** génère une charge d’UC, examinez la charge du système actif avec l’utilitaire **top** :

```azuecli-interactive
top
```

Quittez **top**, puis fermez votre connexion à l’instance de machine virtuelle. **stress** continue de s’exécuter sur l’instance de machine virtuelle.

```azurecli-interactive
Ctrl-c
exit
```

Connectez-vous à la deuxième instance de machine virtuelle avec le numéro de port indiqué à partir de la commande [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) précédente :

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Installez et exécutez **stress**, puis démarrez dix rôles de travail dans cette deuxième instance de machine virtuelle.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

De nouveau, lorsque **stress** affiche une sortie semblable à *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, appuyez sur la touche *Entrée* pour revenir à l’invite de commandes.

Fermez votre connexion à la deuxième instance de machine virtuelle. **stress** continue de s’exécuter sur l’instance de machine virtuelle.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Surveiller les règles de mise à l’échelle automatique actives
Pour surveiller le nombre d’instances de machine virtuelle dans votre groupe identique, utilisez **watch**. Les règles de mise à l’échelle automatique prennent 5 minutes pour commencer le processus de montée en puissance en réponse à la charge d’UC générée par **stress** sur chacune des instances de machine virtuelle :

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Une fois que le seuil de l’UC est atteint, les règles de mise à l’échelle automatique augmentent le nombre d’instances de machine virtuelle au sein du groupe identique. La sortie suivante montre trois machines virtuelles créées au moment de la montée en puissance automatique du groupe identique :

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Une fois que **stress** s’arrête sur les instances initiales de machine virtuelle, la charge d’UC moyenne retourne à la normale. Lorsque 5 autres minutes ont passé, les règles de mise à l’échelle automatique diminuent le nombre d’instances de machine virtuelle. La diminution commence par la suppression des instances de machine virtuelle disposant des ID les plus élevés. La sortie d’exemple suivante montre une instance de machine virtuelle supprimée lors de la diminution automatique :

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Fermez *watch* avec `Ctrl-c`. Le groupe identique continue à diminuer toutes les 5 minutes et supprime une instance de machine virtuelle jusqu’à ce que la quantité minimale d’instances (2) soit atteinte.


## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer votre groupe identique et les ressources supplémentaires, supprimez le groupe de ressources et toutes ses ressources avec [az group delete](/cli/azure/group#az_group_delete) :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à mettre automatiquement à l’échelle un groupe identique avec Azure CLI 2.0 :

> [!div class="checklist"]
> * Utiliser la mise à l’échelle automatique avec un groupe identique
> * Créer et utiliser des règles de mise à l’échelle automatique
> * Effectuer un test de contrainte sur les instances de machine virtuelle et déclencher des règles de mise à l’échelle automatique
> * Remettre à l’échelle automatiquement en cas de baisse de la demande

Pour plus d’exemples de groupes identiques de machines virtuelles en action, consultez les exemples suivants de scripts Azure CLI 2.0 :

> [!div class="nextstepaction"]
> [Exemples de scripts de groupe identique pour Azure CLI 2.0](cli-samples.md)