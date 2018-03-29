---
title: Créer un groupe identique Azure utilisant des machines virtuelles basse priorité (préversion) | Microsoft Docs
description: Découvrez comment créer des groupes identiques Azure qui utilisent des machines virtuelles basse priorité pour réduire vos coûts
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: f25e4d1e3906a610e7c60e348f872a78d7db8fd3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Machines virtuelles basse priorité dans des groupes identiques (préversion)

L’utilisation de machines virtuelles basse priorité dans des groupes identiques vous permet de disposer de notre capacité inutilisée et ainsi de réduire nettement vos coûts. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure évince les machines virtuelles basse priorité. Les machines virtuelles basse priorité sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les grosses charges de calcul, entre autres.

La capacité inutilisée disponible dépend de divers facteurs, tels que la taille, la région, l’heure, etc. Quand des machines virtuelles basse priorité sont déployées dans des groupes identiques, Azure alloue la capacité disponible aux machines virtuelles, le cas échéant. Sachez toutefois qu’il n’y a pas de contrat SLA pour ces machines virtuelles. Un groupe identique basse priorité est déployé dans un domaine d’erreur unique. Il n’offre pas de garantie de haute disponibilité.

> [!NOTE]
> Les groupes identiques basse priorité sont disponibles en préversion pour les scénarios de développement et de test. 

## <a name="eviction-policy"></a>Stratégie d’éviction

Quand les machines virtuelles basse priorité d’un groupe identique sont évincées, elles sont mises à l’état Arrêté (libéré) par défaut. Cette stratégie d’éviction vous permet de redéployer des instances évincées, mais sans garantie de réussite de l’allocation. Les machines virtuelles arrêtées sont comptabilisées dans votre quota d’instances de groupe identique, et vos disques sous-jacents vous sont facturés. 

Si vous souhaitez que les machines virtuelles dans votre groupe identique basse priorité soient supprimées après avoir été évincées, configurez la stratégie d’éviction avec suppression dans votre [modèle Azure Resource Manager](#use-azure-resource-manager-templates). Avec cette configuration, vous pouvez créer d’autres machines virtuelles en définissant la propriété du nombre d’instances du groupe identique à une valeur plus grande. Les machines virtuelles évincées sont supprimées en même temps que leurs disques sous-jacents. Vous n’êtes donc pas facturé pour le stockage. Vous pouvez également utiliser la fonctionnalité de mise à l’échelle automatique des groupes identiques pour essayer et compenser automatiquement les machines virtuelles évincées, mais sans garantie de réussite de l’allocation. Nous vous recommandons d’utiliser uniquement la mise à l’échelle automatique des groupes identiques basse priorité quand vous définissez la stratégie d’éviction avec suppression pour éviter la facturation de vos disques et le dépassement de vos limites de quota. 

> [!NOTE]
> Dans la préversion, vous pouvez définir la stratégie d’éviction à l’aide des [modèles Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Déployer des machines virtuelles basse priorité dans des groupes identiques

Pour déployer des machines virtuelles basse priorité dans des groupes identiques, définissez le nouvel indicateur *Priority* sur *Low*. Toutes les machines virtuelles dans votre groupe identique sont alors configurées en basse priorité. Pour créer un groupe identique avec des machines virtuelles basse priorité, utilisez l’une des méthodes suivantes :
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modèles Microsoft Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>Utiliser Azure CLI 2.0

Le processus de création d’un groupe identique avec des machines virtuelles basse priorité est identique à celui décrit dans [l’article de démarrage rapide](quick-create-cli.md). Ajoutez simplement le paramètre '--Priority' à l’appel CLI et définissez-le sur *Low*, comme indiqué dans l’exemple ci-dessous :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Le processus de création d’un groupe identique avec des machines virtuelles basse priorité est identique à celui décrit dans [l’article de démarrage rapide](quick-create-powershell.md).
Ajoutez simplement le paramètre '-Priority' à [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) et définissez-le sur *Low*, comme indiqué dans l’exemple ci-dessous :

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Utiliser les modèles Azure Resource Manager

Le processus de création d’un groupe identique avec des machines virtuelles basse priorité est identique à celui décrit dans l’article de démarrage rapide pour [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Ajoutez la propriété 'priority' au type de ressource *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* dans votre modèle et définissez la propriété sur *Low*. Vérifiez que vous utilisez l’API *2017-10-30-preview* ou version ultérieure. 

Pour configurer la stratégie d’éviction avec suppression, ajoutez le paramètre 'evictionPolicy' défini sur *delete*.

L’exemple suivant crée un groupe identique basse priorité pour Linux, nommé *myScaleSet*, dans la région *West Central US*, avec *suppression* des machines virtuelles du groupe identique qui sont évincées :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="next-steps"></a>Étapes suivantes
Vous avez créé un groupe identique avec des machines virtuelles basse priorité. Essayez maintenant de déployer notre [modèle de mise à l’échelle automatique des machines virtuelles basse priorité](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Consultez la page [Tarification des groupes identiques de machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) pour connaître les tarifs appliqués.