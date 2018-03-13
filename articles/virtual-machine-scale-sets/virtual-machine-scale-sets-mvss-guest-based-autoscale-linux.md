---
title: "Utiliser la mise à l’échelle automatique Azure avec des mesures invitées dans un modèle de groupe identique Linux | Microsoft Docs"
description: "Découvrez comment effectuer la mise à l’échelle automatique en utilisant des mesures invitées dans un modèle de groupe de machines virtuelles identiques Linux"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Mise à l’échelle automatique en utilisant des mesures invitées dans un modèle de groupe identique Linux

Dans Azure, il existe deux types de mesures collectées sur les machines virtuelles et les groupes identiques : certaines proviennent de la machine virtuelle hôte et d’autres de la machine virtuelles invitée. En général, si vous utilisez des mesures de processeur, de disque et de réseau standard, les mesures d’hôte sont probablement bien adaptées. Si, en revanche, vous avez besoin d’un éventail plus large de mesures, alors les mesures invitées conviennent probablement mieux. Examinons les différences entre les deux :

Les mesures d’hôte sont plus simples et plus fiables. Elles ne nécessitent aucune configuration supplémentaire, car elles sont collectées par la machine virtuelle hôte, tandis que les métriques invitées nécessitent d’installer [l’extension Microsoft Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) ou [l’extension Linux Azure Diagnostics](../virtual-machines/linux/diagnostic-extension.md) sur la machine virtuelle invitée. L’utilisation des mesures invitées à la place des mesures hôtes est courante, car les premières sont plus variées que les dernières. Les mesures de consommation de la mémoire, notamment, ne sont disponibles que via les mesures invitées. Les mesures hôtes prises en charge sont répertoriées [ici](../monitoring-and-diagnostics/monitoring-supported-metrics.md), et les mesures invitées couramment utilisées [ici](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Cet article explique comment modifier le [modèle de groupe identique viable minimal](./virtual-machine-scale-sets-mvss-start.md) pour qu’il utilise des règles de mise à l’échelle automatique en fonction des mesures invitées des groupes identiques Linux.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Le modèle de groupe identique minimum viable peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) et le modèle de déploiement de groupe identique Linux avec mise à l’échelle automatique basée sur des invités peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Examinons le différentiel utilisé pour créer ce modèle (`git diff minimum-viable-scale-set existing-vnet`), élément par élément :

Ajoutez tout d’abord les paramètres de `storageAccountName` et `storageAccountSasToken`. L’agent de diagnostics stocke les données métriques dans une [table](../cosmos-db/table-storage-how-to-use-dotnet.md) de ce compte de stockage. À compter de la version 3.0 de l’agent de diagnostic Linux, l’utilisation d’une clé d’accès de stockage n’est plus prise en charge. Utilisez plutôt un [jeton SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ensuite, modifiez le groupe identique `extensionProfile` pour y inclure l’extension Diagnostics. Dans cette configuration, spécifiez l’ID de ressource du groupe identique à partir duquel collecter les métriques, ainsi que le compte de stockage et le jeton SAP à utiliser pour les stocker. Indiquez la fréquence d’agrégation des métriques (dans ce cas, chaque minute) et les métriques à suivre (dans ce cas, le pourcentage de mémoire utilisée). Pour plus d’informations sur cette configuration et les mesures autres que le pourcentage de mémoire utilisée, consultez [cette documentation](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Enfin, ajoutez une ressource `autoscaleSettings` pour configurer la mise à l’échelle automatique en fonction de ces métriques. Cette ressource contient une clause `dependsOn` qui fait référence au groupe identique pour s’assurer que ce groupe existe avant d’essayer de le soumettre à une mise à l’échelle automatique. Pour baser la mise à l’échelle automatique sur une autre métrique, utilisez la propriété `counterSpecifier` de la configuration de l’extension Diagnostics en tant que propriété `metricName` dans la configuration de la mise à l’échelle automatique. Pour plus d’informations sur la configuration de la mise à l’échelle automatique, consultez les [meilleures pratiques en matière de mise à l’échelle automatique](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) et la [documentation de référence de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
