---
title: Quotas de processeurs virtuels pour Azure | Microsoft Docs
description: "Découvrez plus en détail les quotas de processeurs virtuels pour Azure."
keywords: 
services: virtual-machines-linux
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: 8b99fd92d9031b7172e698cf5db1f776387bdfb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de processeurs virtuels pour les machines virtuelles

Les quotas de processeurs virtuels pour les machines virtuelles et les groupes de machines virtuelles identiques sont organisés en deux niveaux pour chaque abonnement, dans chaque région. Le premier niveau est le total des processeurs virtuels régionaux. Le deuxième niveau se compose des cœurs des différentes familles de tailles de machine virtuelle, comme les processeurs virtuels de la Famille D Standard. Chaque fois qu’une nouvelle machine virtuelle est déployée, le nombre de processeurs virtuels de cette machine virtuelle ne doit pas dépasser le quota de processeurs virtuels pour la famille de tailles de machine virtuelle spécifique ou le quota du total des processeurs virtuels régionaux. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé. Il existe également un quota pour le nombre total de machines virtuelles dans la région. Pour plus d’informations sur chacun de ces quotas, consultez la section **Utilisation + quotas** de la page **Abonnement** dans le [portail Azure](https://portal.azure.com) ou lancez une requête pour obtenir les valeurs à l’aide d’Azure CLI.


## <a name="check-usage"></a>Vérifier l’utilisation

Vous pouvez vérifier l’utilisation de votre quota à l’aide de la commande [az vm list-usage](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Instances de machines virtuelles réservées
Les instances de machines virtuelles réservées, dont l’étendue se limite à un seul abonnement, ajoutent un nouvel aspect aux quotas de processeurs virtuels. Ces valeurs décrivent le nombre d’instances de la taille indiquée qui doivent être déployables dans l’abonnement. Elles jouent le rôle d’espace réservé dans le système de quota pour garantir que le quota est réservé et que les instances réservées sont déployables dans l’abonnement. Par exemple, si un abonnement spécifique a 10 instances réservées Standard_D1, la limite d’utilisation des instances réservées Standard_D1 est de 10. De cette façon, Azure veille à ce qu’il y ait toujours au moins 10 processeurs virtuels disponibles dans le quota Total des processeurs virtuels régionaux pour les instances Standard_D1 et au moins 10 processeurs virtuels disponibles dans le quota de processeurs virtuels Famille D Standard pour les instances Standard_D1.

Si une augmentation de quota est nécessaire pour acheter une instance réservée à abonnement unique, vous pouvez [demander une augmentation de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) sur votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la facturation et les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
