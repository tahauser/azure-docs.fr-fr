---
title: Quotas de processeurs virtuels pour Azure | Microsoft Docs
description: "Découvrez plus en détail les quotas de processeurs virtuels pour Azure."
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: b481299b62d452bc306c1f9c1fa2cdccd49b818e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de processeurs virtuels pour les machines virtuelles

Les quotas de processeurs virtuels pour les machines virtuelles et les groupes de machines virtuelles identiques sont organisés en deux niveaux pour chaque abonnement, dans chaque région. Le premier niveau est le total des processeurs virtuels régionaux. Le deuxième niveau se compose des cœurs des différentes familles de tailles de machine virtuelle, comme les processeurs virtuels de la Famille D Standard. Chaque fois qu’une nouvelle machine virtuelle est déployée, le nombre de processeurs virtuels de cette machine virtuelle ne doit pas dépasser le quota de processeurs virtuels pour la famille de tailles de machine virtuelle spécifique ou le quota du total des processeurs virtuels régionaux. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé. Il existe également un quota pour le nombre total de machines virtuelles dans la région. Pour plus d’informations sur chacun de ces quotas, consultez la section **Utilisation + quotas** de la page **Abonnement** dans le [portail Azure](https://portal.azure.com) ou interrogez les valeurs à l’aide de PowerShell.

 
## <a name="check-usage"></a>Vérifier l’utilisation

Vous pouvez utiliser l’applet de commande [Get-AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) pour vérifier votre utilisation du quota.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

Le résultat ressemble à ce qui suit :

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count

```


## <a name="reserved-vm-instances"></a>Instances de machines virtuelles réservées
Les instances de machines virtuelles réservées, dont l’étendue se limite à un seul abonnement, ajoutent un nouvel aspect aux quotas de processeurs virtuels. Ces valeurs décrivent le nombre d’instances de la taille indiquée qui doivent être déployables dans l’abonnement. Elles jouent le rôle d’espace réservé dans le système de quota pour garantir que le quota est réservé et que les instances réservées sont déployables dans l’abonnement. Par exemple, si un abonnement spécifique a 10 instances réservées Standard_D1, la limite d’utilisation des instances réservées Standard_D1 est de 10. De cette façon, Azure veille à ce qu’il y ait toujours au moins 10 processeurs virtuels disponibles dans le quota Total des processeurs virtuels régionaux pour les instances Standard_D1 et au moins 10 processeurs virtuels disponibles dans le quota de processeurs virtuels Famille D Standard pour les instances Standard_D1.

Si une augmentation de quota est nécessaire pour acheter une instance réservée à abonnement unique, vous pouvez [demander une augmentation de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) sur votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la facturation et les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).