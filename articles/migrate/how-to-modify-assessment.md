---
title: "Personnaliser les paramètres d’évaluation Azure Migrate | Microsoft Docs"
description: "Décrit comment configurer et exécuter une évaluation de la migration de machines virtuelles VMware vers Azure en utilisant Azure Migrate"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>Personnaliser une évaluation

[Azure Migrate](migrate-overview.md) crée des évaluations avec des paramètres par défaut. Après avoir créé une évaluation, vous pouvez modifier ces paramètres par défaut en suivant les instructions indiquées dans cet article.


## <a name="edit-assessment-values"></a>Modifier les valeurs d’évaluation

1. Dans la page **Évaluations** du projet Azure Migrate, sélectionnez l’évaluation et cliquez sur **Modifier des propriétés**.
2. Modifiez les paramètres en fonction du tableau ci-dessous.

    **Paramètre** | **Détails** | **Par défaut**
    --- | --- | ---
    **Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer. |  Ouest des États-Unis 2 est l’emplacement par défaut.
    **Redondance du stockage** | Type de stockage que les machines virtuelles Azure utilisent après la migration. | Seule la réplication [Stockage localement redondant (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) est prise en charge.
    **Facteur de confort** | Le facteur de confort est une mémoire tampon qui est utilisée pendant l’évaluation. Il permet de prendre en compte des éléments tels que l’utilisation occasionnelle, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future. | Le paramètre par défaut est 1.3x.
    **Historique des performances** | Durée passée à l’évaluation de l’historique des performances. | La valeur par défaut est un mois.
    **Utilisation en centile** | Valeur de centile à prendre en compte pour l’historique des performances. | La valeur par défaut est 95 %.
    **Niveau tarifaire** | Vous pouvez spécifier le [niveau tarifaire](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) d’une machine virtuelle.  | Par défaut le niveau [Standard](../virtual-machines/windows/sizes-general.md) est utilisé.
    **Offer** | [Offres Azure](https://azure.microsoft.com/support/legal/offer-details/) qui s’appliquent. | [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) est la valeur par défaut.
    **Devise** | Devise de facturation. | La valeur par défaut est le dollar américain.
    **Remise (%)** | Remise propre à un abonnement cumulable avec toute autre offre. | Le paramètre par défaut est 0 %.
    **Azure Hybrid Use Benefit** | Indique si vous êtes inscrit à [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. | La valeur par défaut est Oui.

3. Cliquez sur **Enregistrer** pour mettre à jour l’évaluation.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
