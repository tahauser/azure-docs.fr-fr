---
title: "Présentation des paramètres de mise à l’échelle automatique | Microsoft Docs"
description: "Il s’agit d’une description détaillée des paramètres de mise à l’échelle automatique et de leur fonctionnement."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: cff2be1818417a19f36da08d8c2eaa227bb945ec
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="understand-autoscale-settings"></a>Comprendre les paramètres de mise à l’échelle automatique
Les paramètres de mise à l’échelle automatique permettent de s’assurer qu’un nombre approprié de ressources s’exécute pour gérer la charge fluctuante de votre application. Vous pouvez configurer les paramètres de mise à l’échelle automatique de sorte qu’ils soient déclenchés en fonction de mesures indiquant la charge ou les performances, ou un déclenchement à une date et une heure planifiées. Cet article examine de manière détaillée l’anatomie d’un paramètre de mise à l’échelle automatique. L’article commence par décrire le schéma et les propriétés d’un paramètre, puis vous guide à travers les différents types de profil qui peuvent être configurés et enfin explique comment la mise à l’échelle automatique évalue le profil à exécuter à un moment donné.

## <a name="autoscale-setting-schema"></a>Schéma du paramètre de mise à l'échelle automatique
Pour illustrer le schéma du paramètre de mise à l’échelle automatique, le paramètre de mise à l’échelle automatique suivant est utilisé. Il est important de noter que ce paramètre de mise à l’échelle automatique comporte les éléments suivants :
- Un profil 
- Il dispose de deux règles de mesure dans ce profil. Une pour augmenter la taille des instances et l’autre pour la diminuer.
- La règle d’augmentation de la taille des instances est déclenchée lorsque la mesure de pourcentage moyen d’utilisation du processeur du groupe de machines virtuelles identiques est supérieur à 85 % pendant les 10 dernières minutes.
- La règle de diminution de la taille des instances est déclenchée lorsque la moyenne du groupe de machines virtuelles identiques est inférieur à 60 % pendant la dernière minute.

> [!NOTE]
> Un paramètre peut avoir plusieurs profils, consultez la section [profils](#autoscale-profiles) pour en savoir plus.
> Un profil peut également avoir plusieurs règles d’augmentation ou de diminution de taille des instances, consultez la [section relative à l’évaluation](#autoscale-evaluation) pour voir comment celles-ci sont évaluées

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
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
    ]
  }
}
```

| Section | Nom de l'élément | DESCRIPTION |
| --- | --- | --- |
| Paramètre | ID | ID de ressource du paramètre de mise à l’échelle automatique. Les paramètres de mise à l’échelle automatique sont une ressource Azure Resource Manager. |
| Paramètre | Nom | Nom du paramètre de mise à l'échelle automatique. |
| Paramètre | location | Emplacement du paramètre de mise à l’échelle automatique. Cet emplacement peut être différent de celui de la ressource en cours de mise à l’échelle. |
| properties | targetResourceUri | ID de la ressource mise à l’échelle. Vous ne pouvez avoir qu’un seul paramètre de mise à l’échelle automatique par ressource. |
| properties | profils | Un paramètre de mise à l’échelle automatique se compose d’un ou plusieurs profils. Chaque fois que le moteur de mise à l’échelle automatique s’exécute, un profil est exécuté. |
| Profil | Nom | Nom du profil. Vous pouvez choisir n’importe quel nom vous permettant d’identifier le profil. |
| Profil | Capacity.maximum | Capacité maximale autorisée. Elle garantit que la mise à l’échelle automatique, lors de l’exécution de ce profil, ne met pas votre ressource à l’échelle au-dessus de ce nombre. |
| Profil | Capacity.minimum | Capacité minimale autorisée. Elle garantit que la mise à l’échelle automatique, lors de l’exécution de ce profil, ne met pas votre ressource à l’échelle au-dessous de ce nombre. |
| Profil | Capacity.default | S’il existe un problème de lecture des mesures de ressource (dans ce cas l’UC de « vmss1 ») et que la capacité actuelle est inférieure à la capacité par défaut, pour garantir la disponibilité de la ressource, la mise à l’échelle automatique sera modifiée sur la valeur par défaut. Si la capacité actuelle est déjà supérieure à la capacité par défaut, la mise à l’échelle automatique n’est pas réduite. |
| Profil | règles | La mise à l’échelle s’ajuste automatiquement entre les capacités maximales et minimales en utilisant les règles du profil. Un profil peut contenir plusieurs règles. Le scénario de base consiste à avoir deux règles, une pour déterminer quand procéder à l’augmentation de la taille des instances et une autre pour déterminer quand procéder à la diminution de la taille des instances. |
| Règle | metricTrigger | Définit la condition de mesure de la règle. |
| metricTrigger | metricName | Nom de la mesure. |
| metricTrigger |  metricResourceUri | ID de la ressource qui a généré la mesure. Dans la plupart des cas, il est identique à la ressource mise à l’échelle. Dans certains cas, il peut être différent. Par exemple, vous pouvez faire évoluer un groupe de machines virtuelles identiques en fonction du nombre de messages figurant dans la file d’attente de stockage. |
| metricTrigger | timeGrain | Durée d’échantillonnage de la mesure. Par exemple, TimeGrain = "PT1M" signifie qui les mesures sont agrégées chaque minute à l'aide de la méthode d'agrégation spécifiée dans « statistic ». |
| metricTrigger | statistic | Méthode d’agrégation au cours de la période timeGrain. Par exemple, statistic = “Average” et timeGrain = “PT1M” signifient que les mesures sont agrégées chaque minute en prenant la valeur moyenne. Cette propriété détermine la façon dont la mesure est échantillonnée. |
| metricTrigger | timeWindow | Durée nécessaire à l’examen des mesures. Par exemple, timeWindow = “PT10M” signifie qu’à chaque exécution d’une mise à l’échelle automatique, les mesures sont interrogées sur les 10 dernières minutes. La fenêtre de temps permet la normalisation de vos mesures et évite de réagir aux pics temporaires. |
| metricTrigger | timeAggregation | Méthode d’agrégation utilisée pour agréger les mesures échantillonnées. Par exemple, TimeAggregation = “Average” doit agréger les mesures échantillonnées en prenant la moyenne. Dans le cas ci-dessus, les dix échantillons de 1 minute et leur moyenne. |
| Règle | scaleAction | Action à entreprendre lors du déclenchement du paramètre metricTrigger de la règle. |
| scaleAction | direction | "Increase" pour augmenter la taille des instances et "Decrease" pour la diminuer|
| scaleAction | value | Indique dans quelle mesure augmenter ou diminuer la capacité de la ressource |
| scaleAction | cooldown | Temps d’attente entre deux opérations de mise à l’échelle. Par exemple, si cooldown = “PT10M”, cela signifie qu’après une opération de mise à l’échelle, aucune tentative de mise à l’échelle automatique ne pourra survenir dans les 10 minutes. Le cooldown permet la stabilisation des mesures après l’ajout ou la suppression d’instances. |

## <a name="autoscale-profiles"></a>Profils de mise à l’échelle automatique

Il existe trois types de profils de mise à l’échelle automatique :

1. **Profil régulier :** profil le plus courant. Si vous n’avez pas besoin de mettre à l’échelle votre ressource de manière différente selon le jour de la semaine ou un jour donné, il vous suffit de définir un profil régulier dans le paramètre de mise à l’échelle automatique. Ce profil peut ensuite être configuré à l’aide de règles de mesures qui déterminent l’augmentation et la diminution de la taille des instances. Un seul profil régulier doit être défini.

    L’exemple de profil utilisé précédemment dans cet article est un exemple de profil régulier. Il est également possible de définir un profil à mettre à l’échelle sur un certain nombre d’instances statiques pour votre ressource.

2. **Profil à date fixe :** lorsque le profil standard est défini, supposons qu’un événement important est à venir le 26 décembre 2017 (PST) et que vous souhaitiez que les capacités minimales/maximales de votre ressource soient différentes ce jour-là tout en effectuant la mise à l’échelle sur les mêmes mesures. Dans ce cas, vous devez ajouter un profil de date fixe à la liste des profils de votre paramètre. Le profil est configuré de manière à s’exécuter uniquement le jour de l’événement. Tous les autres jours, le profil régulier est exécuté.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
3. **Profil de périodicité :** ce type de profil permet de s’assurer que ce profil est toujours utilisé un jour spécifique de la semaine. Les profils de périodicité disposent uniquement d’une heure de début. Par conséquent, ils s’exécutent jusqu'à ce que le profil de périodicité suivant ou le profil de date fixe soit configuré de manière à démarrer. Un paramètre de mise à l’échelle automatique doté d’un seul profil de périodicité, exécute ce profil, même s’il existe un profil régulier défini dans le même paramètre. Les deux exemples ci-dessous illustrent l’utilisation de ce profil :

    **Exemple 1 - Jour de la semaine et week-ends** Supposons que les week-ends, vous souhaitiez porter votre capacité maximale à 4, et que les jours de semaine, vous souhaitiez plus de charge et porter votre capacité maximale à 10. Dans ce cas, le paramètre contiendrait deux profils de périodicité, un à exécuter les week-ends et l’autre les jours de la semaine.
    Le paramètre pourrait ressemble à ceci :

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    En examinant le paramètre précédent, vous remarquerez que chaque profil de périodicité comporte une planification, celle-ci détermine le début de l’exécution du profil. L’exécution du profil s’interrompt lorsqu’il est temps d’exécuter un autre profil.

    Par exemple, dans le paramètre précédent, « weekdayProfile » est défini pour démarrer le lundi à midi, ce qui signifie que ce profil commence à s’exécuter le lundi à midi. Il s’exécute jusqu'au samedi midi, moment auquel « weekendProfile » doit démarrer.

    **Exemple 2 - Heures de bureau** Prenons un autre exemple. Vous voudrez peut-être définir un seuil de mesure = « x » pendant les heures de bureau, soit de 9h00. à 17h00, puis de 17h00 à 9 h 00 le jour suivant, vous voudrez peut-être définir ce seuil de sorte qu’il soit égal à 'y'.
    Le paramètre pourrait ressemble à ceci :
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
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
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Le paramètre précédent, « businessHoursProfile », s’exécute du lundi 9h00 jusqu’à 17h00 , moment où « nonBusinessHoursProfile » commence à s’exécuter. Le paramètre « nonBusinessHoursProfile » s’exécute jusqu'au mardi 9h00 , puis « BusinessHoursProfile » prend le relais. Ce processus se répète jusqu'au vendredi 17h00, moment auquel « nonBusinessHoursProfile » s’exécute jusqu’au lundi 9 h 00 , étant donné que « businessHoursProfile » ne commence pas à s’exécuter avant le lundi 9h00.
    
> [!Note]
> L’expérience utilisateur de mise à l’échelle automatique dans le portail Azure applique les heures de fin pour les profils de périodicité et commence à exécuter le profil par défaut du paramètre de mise à l’échelle entre les profils de périodicité.
    
## <a name="autoscale-evaluation"></a>Évaluation de mise à l'échelle automatique
Dans la mesure où les paramètres de mise à l’échelle automatique peuvent comporter plusieurs profils de mises à l’échelle automatique et que chaque profil peut comporter plusieurs règles de mesure, il est important de comprendre comment un tel paramètre est évalué. Chaque fois que le travail de mise à l’échelle automatique s’exécute, il commence par choisir le profil applicable. Une fois le choix effectué, la mise à l’échelle automatique du profil évalue les valeurs minimales et maximales et les règles de mesure du profil et décide si une action de mise à l’échelle est nécessaire.

### <a name="which-profile-will-autoscale-pick"></a>Quel profil est choisi par la mise à l’échelle automatique ?
- La mise à l’échelle automatique recherche d’abord les profils de date fixe configurés pour s’exécuter maintenant. Une fois le profil trouvé, la mise à l’échelle l’exécute. Si plusieurs profils de date fixe sont supposés s’exécuter, la mise à l’échelle automatique sélectionne le premier.
- En l’absence de profil de date fixe, la mise à l’échelle recherche un profil de périodicité et l’exécute.
- En l’absence de ces deux types de profils, la mise à l’échelle automatique exécute le profil régulier.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Comment la mise à l’échelle automatique évalue plusieurs règles ?

Dès que la mise à l’échelle automatique a déterminé le profil censé s’exécuter, elle commence par évaluer toutes les règles d’augmentation de la taille des instances du profil (règles dans lesquelles direction = “Increase”).
- Si une ou plusieurs règles d’augmentation de la taille des instances sont déclenchées, la mise à l’échelle automatique calcule la nouvelle capacité déterminée par scaleAction de chacune de ces règles. Elle augmente ensuite la taille des instances de manière à atteindre les capacités maximales pour garantir la disponibilité du service.
- Par exemple : s’il existe un groupe de machines virtuelles identiques dont la capacité actuelle est 10 et qu’il existe deux règles d’augmentation de la taille des instances (une augmentant la capacité de 10 % et une l’augmentant de 3 %). La première règle entraînerait une nouvelle capacité de 11, et la deuxième règle une capacité de 13. Pour garantir la disponibilité du service la mise à l’échelle automatique choisit l’action qui résulte dans la capacité maximale, par conséquent, la deuxième règle.

Si aucune règle d’augmentation de la taille des instances n’est déclenchée, la mise à l’échelle automatique évalue toutes les règles de diminution de la taille des instances (règles dans lesquelles direction = “Decrease”). La mise à l’échelle automatique n’accepte une action de diminution de la taille des instances que si toutes les règles correspondantes sont déclenchées.
- La mise à l’échelle automatique calcule la nouvelle capacité déterminée par scaleAction de chacune de ces règles. Elle choisit ensuite l’action de mise à l’échelle offrant le maximum de capacité pour garantir la disponibilité du service.
- Par exemple : s’il existe un groupe de machines virtuelles identiques dont la capacité actuelle est 10 et qu’il existe deux règles de diminution de la taille des instances (une réduisant la capacité de 50 % et une la réduisant de 3 %). La première règle entraînerait une nouvelle capacité de 5, et la deuxième règle une capacité de 7. Pour garantir la disponibilité du service la mise à l’échelle automatique choisit l’action qui résulte dans la capacité maximale, par conséquent, la deuxième règle.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la mise à l’échelle automatique, consultez les ressources suivantes :

* [Vue d’ensemble de la mise à l’échelle automatique](monitoring-overview-autoscale.md)
* [Mesures courantes pour la mise à l’échelle automatique dans Azure Monitor](insights-autoscale-common-metrics.md)
* [Meilleures pratiques pour la mise à l’échelle automatique d’Azure Insights](insights-autoscale-best-practices.md)
* [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique](insights-autoscale-to-webhook-email.md)
* [Paramètres de mise à l’échelle automatique](https://msdn.microsoft.com/library/dn931953.aspx)
