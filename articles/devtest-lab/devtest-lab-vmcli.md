---
title: "Créer et gérer des machines virtuelles dans DevTest Labs avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Apprenez à utiliser Azure DevTest Labs pour créer et gérer des machines virtuelles avec Azure CLI 2.0"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: v-craic
ms.openlocfilehash: e73ddeba56c779d9fb1be77a50cbae5111de03c4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Créer et gérer des machines virtuelles avec DevTest Labs à l’aide de l’interface de ligne de commande Azure
Ce guide de démarrage rapide vous assistera dans la création, le démarrage, la connexion, la mise à jour et le nettoyage d’un ordinateur de développement dans votre laboratoire. 

Avant de commencer :

* Si aucun laboratoire n’a été créé, la procédure à suivre se trouve [ici](devtest-lab-create-lab.md).

* [Installer CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Pour démarrer, exécutez az login pour créer une connexion avec Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Créer et vérifier la machine virtuelle 
Créez une machine virtuelle à partir d’une image de la place du marché avec authentification SSH.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Placez le nom du **groupe de ressources du laboratoire** dans le paramètre --resource-group.
>

Si vous voulez créer une machine virtuelle à l’aide d’une formule, utilisez le paramètre --formula dans [az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create) (créer vm labo az).


Vérifiez la disponibilité de la machine virtuelle.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Démarrer et se connecter à la machine virtuelle
Démarrez une machine virtuelle.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Placez le nom du **groupe de ressources du laboratoire** dans le paramètre --resource-group.
>

Se connecter à une machine virtuelle : [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Bureau à distance](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Mettre à jour la machine virtuelle
Appliquez des artefacts à une machine virtuelle.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Listez les artefacts disponibles dans le laboratoire.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Arrêter et supprimer la machine virtuelle    
Arrêtez une machine virtuelle.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Supprimez une machine virtuelle.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]