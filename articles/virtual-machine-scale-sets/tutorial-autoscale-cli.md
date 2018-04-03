---
title: Didacticiel - Mettre automatiquement un groupe identique avec Azure CLI 2.0 | Microsoft Docs
description: Découvrez comment utiliser Azure CLI 2.0 pour mettre automatiquement à l’échelle un groupe identique de machines virtuelles en fonction de l’augmentation et de la diminution des demandes du processeur
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
ms.openlocfilehash: 10e5b1a261f28391bed8cf3f111b1124b52d7816
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Didacticiel : Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques avec Azure CLI 2.0
Lorsque vous créez un groupe identique, vous définissez le nombre d’instances de machine virtuelle que vous souhaitez exécuter. À mesure que la demande de votre application change, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machine virtuelle. La capacité de mise à l’échelle automatique vous permet de suivre la demande du client ou de répondre aux changements de performances de votre application tout au long de son cycle de vie. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser la mise à l’échelle automatique avec un groupe identique
> * Créer et utiliser des règles de mise à l’échelle automatique
> * Tests de contraintes des instances de machine virtuelle et déclenchement des règles de mise à l’échelle
> * Remise à l’échelle en cas de réduction de la demande

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.29 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Créer un groupe identique
Pour aider à créer des règles de mise à l’échelle automatique, définissez des paramètres pour votre ID d’abonnement, groupe de ressources, nom de groupe identique, et emplacement :

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create) comme suit :

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

Créez à présent un groupe de machines virtuelles identiques avec [az vmss create](/cli/azure/vmss#create). L’exemple suivant crée un groupe identique avec *2* instances, et génère des clés SSH si elles n’existent pas :

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>Définir un profil de mise à l’échelle automatique
Les règles de mise à l’échelle automatique sont déployées en tant que JSON (JavaScript Object Notation) avec Azure CLI 2.0. Nous allons examiner chaque partie de ce profil de mise à l’échelle automatique, puis créer un exemple complet.

Le début du profil de mise à l’échelle automatique définit les capacités par défaut, minimale et maximale du groupe identique. L’exemple suivant définit les capacités par défaut et minimale de *2* instances de machine virtuelle, et la capacité maximale de *10* :

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-autoscale-out"></a>Créer une règle pour la mise à l’échelle automatique
Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans votre groupe identique augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Vous contrôlez les métriques à surveiller, telles que l’usage du processeur ou du disque, la durée pendant laquelle la charge de l’application doit respecter un seuil donné, et le nombre d’instances de machine virtuelle à ajouter au groupe identique.

Créons une règle qui augmente le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est supérieure à 70 % pendant 5 minutes. Lorsque la règle déclenche l’opération, le nombre d’instances de machine virtuelle est augmenté de trois.

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

L’exemple suivant définit la règle pour augmenter le nombre d’instances de machine virtuelle. Le paramètre *metricResourceUri* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
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
```


## <a name="create-a-rule-to-autoscale-in"></a>Créer une règle pour la mise à l’échelle automatique
Au cours d’une soirée ou d’un week-end, la demande de votre application peut diminuer. Si cette charge réduite est constante pendant un certain temps, vous pouvez configurer des règles de mise à l’échelle automatique pour réduire le nombre d’instances de machine virtuelle dans le groupe identique. Cette action de diminution du nombre d’instances a pour effet de réduire le coût d’exécution de votre groupe identique, car vous seul exécutez le nombre d’instances requis pour répondre à la demande en cours.

Créez une autre règle qui diminue le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est inférieure à 30 % pendant 5 minutes. L’exemple suivant définit la règle pour diminuer de une unité le nombre d’instances de machine virtuelle. Le paramètre *metricResourceUri* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
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


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Appliquer des règles de mise à l’échelle automatique à un groupe identique
L’étape finale consiste à appliquer le profil et les règles de mise à l’échelle automatique à votre groupe identique. Votre groupe identique peut ensuite augmenter ou diminuer automatiquement en fonction de la demande de l’application. Appliquez le profil de mise à l’échelle automatique à l’aide de la commande [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) comme suit. Le JSON complet suivant utilise le profil et les règles indiquées dans les sections précédentes :

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
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
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
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
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>Générer une charge du processeur sur un groupe identique
Pour tester les règles de mise à l’échelle automatique, générez des charges du processeur sur les instances de machine virtuelle dans le groupe identique. Cette charge du processeur simulée provoque la mise à l’échelle automatique afin d’entraîner une montée en puissance et l’augmentation du nombre d’instances de machine virtuelle. Comme la charge du processeur simulée est ensuite diminuée, les règles de mise à l’échelle automatique réduisent le nombre d’instances de machine virtuelle.

Tout d’abord, affichez l’adresse et les ports à connecter à des instances de machine virtuelle dans un groupe identique avec la commande [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) :

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

L’exemple de sortie suivant affiche le nom de l’instance, l’adresse IP publique de l’équilibreur de charge et le numéro de port vers lequel les règles NAT (traduction d’adresse réseau) transfèrent le trafic :

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

Une fois connecté, installez l’utilitaire **stress**. Démarrez *10* rôles de travail **stress** qui génèrent une charge du processeur. Ces rôles de travail sont exécutés pendant *420* secondes, ce qui est suffisant pour que les règles de mise à l’échelle automatique implémentent l’action souhaitée.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Lorsque **stress** affiche une sortie similaire à *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, appuyez sur la touche *Entrée* pour revenir à l’invite de commandes.

Pour vérifier que **stress** génère du processeur de charge, examinez la charge du système active avec l’utilitaire **top** :

```azuecli-interactive
top
```

Quittez **top**, puis fermez votre connexion à l’instance de machine virtuelle. **stress** continue de s’exécuter sur l’instance de machine virtuelle.

```azurecli-interactive
Ctrl-c
exit
```

Connectez-vous à la deuxième instance de machine virtuelle avec le numéro de port indiqué dans le [az mise liste-instance-connexion-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) précédent :

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Installez et exécutez **stress**, puis démarrez dix rôles de travail dans cette deuxième instance de machine virtuelle.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

De nouveau, lorsque **stress** affiche une sortie similaire à *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, appuyez sur la touche *Entrée* pour revenir à l’invite de commandes.

Fermez votre connexion à la deuxième instance de machine virtuelle. **stress** continue de s’exécuter sur l’instance de machine virtuelle.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Surveiller les règles actives de mise à l’échelle automatique
Pour surveiller le nombre d’instances de machine virtuelle dans votre groupe identique, utilisez **watch**. Les règles de mise à l’échelle automatique prennent 5 minutes pour commencer le processus de montée en charge en réponse à la charge du processeur générée par **stress** sur chacune des instances de machine virtuelle :

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

Une fois que le seuil du processeur a été atteint, les règles de mise à l’échelle automatique augmentent le nombre d’instances de machine virtuelle au sein du groupe identique. La sortie suivante montre trois machines virtuelles créées par la montée en puissance automatique du groupe identique :

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

Une fois que **stress** s’arrête sur les instances initiales de machine virtuelle, la charge moyenne du processeur retourne à la normale. Lorsque 5 autres minutes ont passé, les règles de mise à l’échelle automatique diminuent le nombre d’instances de machine virtuelle. Les actions de diminution commencent par supprimer les instances de machine virtuelle disposant des ID les plus élevés. La sortie d’exemple suivante montre une instance de machine virtuelle supprimée lors de la diminution automatique :

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Fermez *watch* avec `Ctrl-c`. Le groupe identique continue à diminuer toutes les 5 minutes et supprime une instance de machine virtuelle jusqu'à atteindre la quantité minimale (2).


## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer votre groupe identique et les ressources supplémentaires, supprimez le groupe de ressources et toutes ses ressources avec [az group delete](/cli/azure/group#az_group_delete). Le paramètre `--no-wait` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine. Le paramètre `--yes` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin.

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à mettre automatiquement l’échelle un groupe identique avec Azure CLI 2.0 :

> [!div class="checklist"]
> * Utiliser la mise à l’échelle automatique avec un groupe identique
> * Créer et utiliser des règles de mise à l’échelle automatique
> * Tests de contraintes des instances de machine virtuelle et déclenchement des règles de mise à l’échelle
> * Remise à l’échelle en cas de réduction de la demande

Pour plus d’exemples de groupes identiques de machines virtuelles en action, consultez l’exemple suivant de scripts Azure CLI 2.0 :

> [!div class="nextstepaction"]
> [Exemples de scripts de groupe identique pour Azure CLI 2.0](cli-samples.md)