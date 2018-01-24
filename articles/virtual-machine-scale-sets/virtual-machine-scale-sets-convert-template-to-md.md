---
title: "Convertir un modèle de groupe identique Azure Resource Manager pour utiliser un disque managé | Microsoft Docs"
description: "Convertissez un modèle de groupe identique pour utiliser modèle de groupe identique de disque managé."
keywords: groupes de machines virtuelles identiques
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Convertir un modèle de groupe identique pour utiliser modèle de groupe identique de disque managé

Les clients utilisant un modèle Resource Manager pour créer un groupe identique sans utiliser de disque managé devront le modifier pour utiliser un disque managé. Cet article explique comment utiliser des disques managés en prenant l’exemple d’une demande de tirage (pull request) dans les [Modèles de démarrage rapide Microsoft Azure](https://github.com/Azure/azure-quickstart-templates), dépôt géré par la communauté contenant des exemples de modèles Resource Manager. Vous pouvez voir la demande de tirage complète ici : [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), et les parties pertinentes de la comparaison, ainsi que des explications :

## <a name="making-the-os-disks-managed"></a>Création des disques managés de système d’exploitation

Dans la comparaison suivante, plusieurs variables liées aux propriétés de disque et au compte de stockage sont supprimées. Le type de compte de stockage n’est plus nécessaire (Standard_LRS est la valeur par défaut), mais vous pouvez toujours le spécifier si vous le souhaitez. Seuls Standard_LRS et Premium_LRS sont pris en charge pour les disques managés. Un nouveau suffixe de compte de stockage, un tableau de chaînes uniques et le nombre d’associations de sécurité étaient utilisés dans l’ancien modèle pour générer des noms de compte de stockage. Ces variables ne sont plus nécessaires dans le nouveau modèle, car le disque managé crée automatiquement des comptes de stockage au nom du client. De même, le nom de conteneur de disque dur virtuel et le nom du disque du système d’exploitation ne sont plus nécessaires, car le disque managé nomme automatiquement les conteneurs d’objets blob de stockage et les disques sous-jacents.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Dans la comparaison suivante, votre API de calcul est mise à jour vers la version 2016-04-30-preview, qui est la version minimale requise pour la prise en charge des disques managés avec des groupes identiques. Vous pouvez utiliser des disques non managés dans la nouvelle version de l’API avec l’ancienne syntaxe si vous le souhaitez. Si vous mettez seulement à jour la version de l’API de calcul et ne modifiez rien d’autre, le modèle devrait continuer à fonctionner comme avant.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Dans la comparaison suivante, la ressource de compte de stockage est complètement supprimée du tableau des ressources. La ressource n’est plus nécessaire, car le disque managé les crée automatiquement.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Dans la comparaison suivante, nous pouvons voir que ce que nous supprimons dépend de la clause faisant référence, depuis le groupe identique, à la boucle qui créait des comptes de stockage. Dans l’ancien modèle, cela garantissait que les comptes de stockage étaient créés avant le début de la création du groupe identique, mais cette clause n’est plus nécessaire avec un disque managé. La propriété de conteneurs de disque dur virtuel est également supprimée ainsi que la propriété de nom du disque du système d’exploitation, car elles sont gérées automatiquement par le disque managé en arrière-plan. Vous pouviez ajouter `"managedDisk": { "storageAccountType": "Premium_LRS" }` dans la configuration « osDisk » si vous vouliez des disques de système d’exploitation premium. Seules les machines virtuelles avec une un « s » minuscule ou majuscule dans la référence de machine virtuelle (SKU) peuvent utiliser des disques premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Il n’existe aucune propriété explicite dans la configuration du jeu de mise à l’échelle pour indiquer s’il faut utiliser un disque managé ou non managé. Le groupe identique sait lequel utiliser sur la base des propriétés qui sont présentes dans le profil de stockage. Par conséquent, il est important, lorsque vous modifiez le modèle, de garantir que les bonnes propriétés sont dans le profil de stockage du groupe identique.


## <a name="data-disks"></a>Disques de données

Avec les modifications ci-dessus, le groupe identique utilise des disques managés pour le disque de système d’exploitation, mais qu’en est-il des disques de données ? Pour ajouter des disques de données, ajoutez la propriété « dataDisks » sous « storageProfile » au même niveau que « osDisk ». La valeur de la propriété est une liste JSON d’objets, chacun d'entre eux possédant les propriétés « lun » (qui doit être unique pour chaque disque de données sur une machine virtuelle), « createOption » (« empty » est actuellement la seule option prise en charge) et « diskSizeGB » (la taille du disque en gigaoctets ; doit être supérieure à 0 et inférieure à 1024) comme dans l’exemple suivant : 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Si vous spécifiez `n` disques dans ce tableau, chaque machine virtuelle du groupe identique obtient `n` disques de données. Notez, cependant, que ces disques de données sont des appareils bruts. Ils ne sont pas formatés. Il incombe au client de joindre, partitionner et formater les disques avant de les utiliser. Vous pouvez également spécifier `"managedDisk": { "storageAccountType": "Premium_LRS" }` dans chaque objet de disque de données pour indiquer que ce doit être un disque de données premium. Seules les machines virtuelles avec une un « s » minuscule ou majuscule dans la référence de machine virtuelle (SKU) peuvent utiliser des disques premium.

Pour en savoir plus sur l’utilisation de disques de données avec des groupes identiques, consultez [cet article](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Étapes suivantes
Pour les modèles Resource Manager utilisant des groupes identiques, recherchez « vmss » dans le [dépôt Github de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

Pour plus d’informations, consultez la [page d’accueil principale des groupes identiques](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

