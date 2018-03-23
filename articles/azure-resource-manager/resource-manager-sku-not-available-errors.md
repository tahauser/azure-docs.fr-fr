---
title: Erreurs de référence SKU Azure non disponible | Microsoft Docs
description: Décrit comment résoudre les erreurs de référence SKU non disponible au cours du déploiement.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: b0cbd3c232e5df831031cc8e436f8dbb24b0e72c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="resolve-errors-for-sku-not-available"></a>Résoudre les erreurs de référence SKU non disponible

Cet article décrit comment résoudre les erreurs **SkuNotAvailable**.

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource (généralement une machine virtuelle), vous pouvez recevoir le message et le code d’erreur suivants :

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Cause :

Vous recevez cette erreur lorsque la ressource de référence (SKU) que vous avez sélectionnée (par exemple, la taille de la machine virtuelle) n’est pas disponible pour l’emplacement que vous avez sélectionné.

## <a name="solution-1---powershell"></a>Solution 1 : PowerShell

Pour déterminer les références SKU qui sont disponibles dans une région, utilisez la commande [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku). Filtrez les résultats par emplacement. Pour cette commande, vous devez disposer de la version la plus récente de PowerShell.

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations -icontains "southcentralus"}
```

Les résultats incluent une liste de références pour l’emplacement et toutes les restrictions éventuellement liées aux références concernées.

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

## <a name="solution-2---azure-cli"></a>Solution 2 : Azure CLI

Pour déterminer les références SKU qui sont disponibles dans une région, utilisez la commande `az vm list-skus`. Vous pouvez ensuite utiliser `grep` ou un utilitaire similaire pour filtrer la sortie.

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

## <a name="solution-3---azure-portal"></a>Solution 3 : Portail Azure

Pour déterminer les références SKU qui sont disponibles dans une région, consultez le [portail](https://portal.azure.com). Connectez-vous au portail et ajoutez une ressource à travers l’interface. Lorsque vous définissez les valeurs, vous voyez les références (SKU) disponibles pour cette ressource. Vous n’avez pas besoin de terminer le déploiement.

![Références disponibles](./media/resource-manager-sku-not-available-errors/view-sku.png)

## <a name="solution-4---rest"></a>Solution 4 : REST

Pour déterminer les références SKU qui sont disponibles dans une région, utilisez l’API REST pour les machines virtuelles. Envoyez la requête suivante :

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

Elle renvoie les références et les régions disponibles dans le format suivant :

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

Si vous ne parvenez pas à trouver une référence appropriée dans cette région ou une autre qui réponde aux besoins de votre entreprise, envoyez une [demande de référence SKU](https://aka.ms/skurestriction) au support Azure.
