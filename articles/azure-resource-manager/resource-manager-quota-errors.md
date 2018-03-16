---
title: "Erreurs liées aux quotas Azure | Microsoft Docs"
description: "Explique comment résoudre les erreurs liées aux quotas de ressources."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: d2cc760bf516e8ee96629886120a1bb092932a82
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="resolve-errors-for-resource-quotas"></a>Résoudre les erreurs liées aux quotas de ressources

Cet article décrit les erreurs liées aux quotas que vous risquez de rencontrer lorsque vous déployez des ressources.

## <a name="symptom"></a>Symptôme

Si vous déployez un modèle qui crée des ressources ne respectant pas les quotas Azure, vous obtenez une erreur de déploiement similaire à ce qui suit :

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Ou bien, vous pouvez obtenir ce qui suit :

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Cause :

Les quotas sont appliqués par groupe de ressources, abonnement, compte ou autre étendue. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que la quantité autorisée, vous recevez une erreur indiquant que le quota a été dépassé.
Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="azure-cli"></a>Azure CLI

Dans le cas d’Azure CLI, utilisez la commande `az vm list-usage` pour rechercher des quotas de machine virtuelle.

```azurecli
az vm list-usage --location "South Central US"
```

Résultat :

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Pour PowerShell, utilisez la commande **Get-AzureRmVMUsage** pour rechercher des quotas de machine virtuelle.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

Résultat :

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Solution

Pour demander une augmentation du quota, accédez au portail et soumettez une requête de support. Dans la requête de support, demandez une augmentation de votre quota pour la région dans laquelle vous souhaitez effectuer le déploiement.

> [!NOTE]
> N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs Resource Manager dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Resource Manager dans toutes les régions.
>
>

1. Sélectionnez **Abonnements**.

   ![Abonnements](./media/resource-manager-quota-errors/subscriptions.png)

2. Sélectionnez l’abonnement nécessitant une augmentation du quota.

   ![Sélectionnez un abonnement](./media/resource-manager-quota-errors/select-subscription.png)

3. Sélectionnez **Utilisation + quotas**.

   ![Sélectionnez Utilisation + quotas](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. Dans l’angle supérieur droit, cliquez sur **Demander une augmentation**.

   ![Demander une augmentation](./media/resource-manager-quota-errors/request-increase.png)

5. Renseignez les formulaires pour le type de quota que vous avez besoin d’augmenter.

   ![Renseignez le formulaire](./media/resource-manager-quota-errors/forms.png)