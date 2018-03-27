---
title: "Mettre à l’échelle automatiquement des groupes de machines virtuelles identiques avec Azure CLI | Microsoft Docs"
description: "Comment créer des règles de mise à l’échelle automatique pour des groupes de machines virtuelles identiques avec Azure CLI 2.0"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques avec Azure CLI 2.0
Lorsque vous créez un groupe identique, vous définissez le nombre d’instances de machine virtuelle que vous souhaitez exécuter. À mesure que la demande de votre application change, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machine virtuelle. La capacité de mise à l’échelle automatique vous permet de suivre la demande du client ou de répondre aux changements de performances de votre application tout au long de son cycle de vie.

Cet article explique comment créer avec Azure CLI 2.0 des règles de mise à l’échelle automatique qui analysent les performances des instances de machine virtuelle dans votre groupe identique. Ces règles de mise à l’échelle augmentent ou réduisent le nombre d’instances de machine virtuelle en réponse à ces métriques de performances. Vous pouvez également accomplir ces étapes avec [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou dans le [portail Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Composants requis
Pour créer des règles de mise à l’échelle, vous avez besoin d’un groupe de machines virtuelles identiques. Vous pouvez créer un groupe identique avec le [portail Azure](virtual-machine-scale-sets-create-portal.md), [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), ou [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Pour faciliter la création des règles de mise à l’échelle, définissez des variables pour votre groupe identique. L’exemple suivant définit des variables pour le groupe identique nommé *myScaleSet* dans le groupe de ressources nommé *myResourceGroup* et dans la région *eastus*. Votre ID d’abonnement est obtenu avec la commande [az account show](/cli/azure/account#az_account_show). Si plusieurs abonnements sont associés à votre compte, seul le premier est retourné. Ajustez les noms et l’ID d’abonnement comme suit :

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Définir un profil de mise à l’échelle automatique
Les règles de mise à l’échelle automatique sont déployées en tant que JSON (JavaScript Object Notation) avec Azure CLI 2.0. Vous trouverez le JSON complet qui définit et déploie les règles de mise à l’échelle automatique plus loin dans l’article. 

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


## <a name="create-a-rule-to-automatically-scale-out"></a>Créer une règle d’augmentation automatique du nombre d’instances
Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans votre groupe identique augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Vous contrôlez les métriques à surveiller, telles que l’usage du processeur ou du disque, la durée pendant laquelle la charge de l’application doit respecter un seuil donné, et le nombre d’instances de machine virtuelle à ajouter au groupe identique.

Créons une règle qui augmente le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est supérieure à 70 % pendant 10 minutes. Lorsque la règle déclenche l’opération, le nombre d’instances de machine virtuelle est augmenté de 20 %. Dans des groupes identiques comportant un petit nombre d’instances de machine virtuelle, vous pouvez définir le `type` sur *ChangeCount* et augmenter la `value` de *1* ou *2* instances. Dans des groupes identiques comportant un grand nombre d’instances de machine virtuelle, une augmentation de 10 ou 20 % d’instances de machine virtuelle peut être plus appropriée.

Les paramètres suivants sont utilisés pour cette règle :

| Paramètre         | Explication                                                                                                         | Valeur           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Métrique de performances à surveiller et sur laquelle appliquer des actions de groupe identique.                                                   | Percentage CPU  |
| *timeGrain*       | Fréquence à laquelle les métriques sont collectées à des fins d’analyse.                                                                   | 1 minute        |
| *timeAggregation* | Définit la manière dont les métriques collectées doivent être agrégées à des fins d’analyse.                                                | Moyenne         |
| *timeWindow*      | Temps de surveillance avant que les valeurs de métrique et de seuil soient comparées.                                   | 10 minutes      |
| *operator*        | Opérateur utilisé pour comparer les données de métrique au seuil.                                                     | Supérieur à    |
| *threshold*       | Valeur qui amène la règle de mise à l’échelle automatique à déclencher une action.                                                      | 70 %             |
| *direction*       | Définit si le groupe identique doit augmenter ou réduire l’échelle lorsque la règle s’applique.                                             | Augmenter        |
| *type*            | Indique que le nombre d’instances de machine virtuelle doit être modifié d’un certain pourcentage.                                 | Pourcentage de modification  |
| *value*           | Nombre d’instances de machine virtuelle à ajouter ou à supprimer lorsque la règle s’applique.                                            | 20              |
| *cooldown*        | Temps d’attente avant que la règle soit appliquée à nouveau afin que les actions de mise à l’échelle automatique aient le temps de porter effet. | 5 minutes       |

L’exemple suivant définit la règle pour augmenter le nombre d’instances de machine virtuelle. La valeur *metricResourceUri* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Créer une règle pour diminuer automatiquement le nombre d’instances
Au cours d’une soirée ou d’un week-end, la demande de votre application peut diminuer. Si cette charge réduite est constante pendant un certain temps, vous pouvez configurer des règles de mise à l’échelle automatique pour réduire le nombre d’instances de machine virtuelle dans le groupe identique. Cette action de diminution du nombre d’instances a pour effet de réduire le coût d’exécution de votre groupe identique, car vous seul exécutez le nombre d’instances requis pour répondre à la demande en cours.

Créez une autre règle qui diminue le nombre d’instances de machine virtuelle dans un groupe identique lorsque la charge moyenne du processeur est inférieure à 30 % pendant 10 minutes. L’exemple suivant définit la règle pour augmenter le nombre d’instances de machine virtuelle. La valeur *metricResourceUri* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Appliquer des règles de mise à l’échelle automatique à un groupe identique
L’étape finale consiste à appliquer le profil et les règles de mise à l’échelle automatique à votre groupe identique. L’échelle peut ensuite augmenter ou diminuer automatiquement en fonction de la demande de l’application. Appliquez le profil de mise à l’échelle automatique à l’aide de la commande [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) comme suit. Le JSON complet utilise le profil et les règles indiquées dans les sections précédentes.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
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
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
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
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
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


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Surveiller le nombre d’instances dans un groupe identique
Pour voir le nombre et l’état des instances de machine virtuelle, affichez la liste des instances dans votre groupe identique en utilisant la commande [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances). L’état indique si l’instance de la machine virtuelle approvisionne à mesure que le groupe identique augmente automatiquement le nombre d’instances, ou déprovisionne à mesure que le groupe identique diminue automatiquement le nombre d’instances. L’exemple suivant présente l’état d’instance de machine virtuelle pour le groupe identique nommé *myScaleSet* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Mise à l’échelle automatique en fonction d’une planification
Les exemples précédents ont automatiquement mis à l’échelle un groupe identique en fonction de métriques de base de l’ordinateur hôte, telles que l’utilisation du processeur. Vous pouvez également créer des règles de mise à l’échelle automatique basées sur des planifications. Ces règles basées sur une planification vous permettent d’augmenter automatiquement le nombre d’instances de machine virtuelle avant une augmentation anticipée de la demande d’une application, par exemple, aux heures de travail habituelles, et diminuer automatiquement le nombre d’instances aux heures auxquelles vous anticipez moins de demande, par exemple, durant le weekend.

Pour utiliser des règles de mise à l’échelle automatique basées sur une planification, créez un profil JSON qui définit le nombre d’instances de machine virtuelle à exécuter pour une fenêtre déterminée d’heures de début et de fin. L’exemple suivant définit une règle pour augmenter le nombre d’instances à *10* à *9* heures chaque jour de la semaine de travail (du lundi au vendredi).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Pour diminuer le nombre d’instances en soirée, créez une autre règle qui spécifie un nombre inférieur d’instances de machine virtuelle et une heure de début appropriée.

L’exemple complet suivant définit les règles pour augmenter et diminuer le nombre d’instances, puis applique le profil de mise à l’échelle avec la commande [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Cet exemple remplace les règles de mise à l’échelle automatique basées sur des mesures créées dans les exemples précédents. Le paramètre *metricResourceUri* utilise les variables précédemment définies pour l’ID d’abonnement, le nom du groupe de ressources et le nom du groupe identique :

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser des règles de mise à l’échelle automatique pour mettre à l’échelle horizontalement, et augmenter ou diminuer le *nombre* d’instances de machine virtuelle dans votre groupe identique. Vous pouvez également mettre à l’échelle verticalement pour augmenter ou diminuer la *taille* d’instance de machine virtuelle. Pour plus d’informations, voir [Mise à l’échelle verticale avec des groupes de machines virtuelles identiques](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Pour plus d’informations sur la gestion de vos instances de machine virtuelle, voir [Gérer les groupes de machines virtuelles identiques avec Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Pour découvrir comment générer des alertes lorsque vos règles de mise à l’échelle automatique déclenchent, voir [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Vous pouvez également [Utiliser des journaux d’audit pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
