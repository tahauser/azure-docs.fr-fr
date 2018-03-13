---
title: "Personnaliser les paramètres d’évaluation Azure Migrate | Microsoft Docs"
description: "Décrit comment configurer et exécuter une évaluation de la migration de machines virtuelles VMware vers Azure en utilisant Azure Migrate"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 02/26/2018
ms.author: raynew
ms.openlocfilehash: efb4ad59d25a0c1209e4f0f6cd406c2f0d48159c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="customize-an-assessment"></a>Personnaliser une évaluation

[Azure Migrate](migrate-overview.md) crée des évaluations avec des propriétés par défaut. Après avoir créé une évaluation, vous pouvez modifier les propriétés par défaut en suivant les instructions indiquées dans cet article.


## <a name="edit-assessment-properties"></a>Modifier les propriétés d’évaluation

1. Dans la page **Évaluations** du projet de migration, sélectionnez l’évaluation et cliquez sur **Modifier des propriétés**.
2. Modifiez les propriétés conformément au tableau suivant :

    **Paramètre** | **Détails** | **Par défaut**
    --- | --- | ---
    **Emplacement cible** | Emplacement Azure vers lequel vous souhaitez migrer.<br/><br/> À l’heure actuelle, Azure Migrate prend en charge les 30 régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre de l’Inde, Centre des États-Unis, Est de la Chine, Nord de la Chine, Asie-Pacifique, Est des États-Unis, Centre de l’Allemagne, Nord-Ouest de l’Allemagne, Est des États-Unis 2, Est du Japon, Ouest du Japon, Corée Centre, Sud de la Corée, Nord-Centre des États-Unis, Europe du Nord, Sud-Centre des États-Unis, Asie du Sud-Est, Inde du Sud, Royaume-Uni Sud, Royaume-Uni Ouest, Centre-Ouest des États-Unis, Europe de l’Ouest, Inde de l’Ouest, Ouest des États-Unis et Ouest des États-Unis 2. |  Ouest des États-Unis 2 est l’emplacement par défaut.
    **Redondance du stockage** | Type de redondance du stockage que les machines virtuelles Azure utiliseront après la migration. | La valeur par défaut est [Stockage localement redondant (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage). Azure Migrate prend uniquement en charge les évaluations basées sur des disques managés, et les disques managés prennent uniquement en charge le stockage LRS. Par conséquent, la propriété ne comporte pour le moment que l’option LRS. 
    **Critère de dimensionnement** | Critère utilisé par Azure Migrate pour dimensionner correctement les machines virtuelles pour Azure. Vous pouvez effectuer un dimensionnement *en fonction des performances* ou dimensionner les machines virtuelles *comme localement*, sans tenir compte de l’historique des performances. | Le dimensionnement en fonction des performances est l’option par défaut.
    **Historique des performances** | Durée à prendre en compte pour évaluer les performances des machines virtuelles. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*. | La valeur par défaut est une journée.
    **Utilisation en centile** | Valeur de centile du jeu d’échantillons de performances devant être pris en compte pour le dimensionnement adéquat. Cette propriété s’applique uniquement quand le critère de dimensionnement est le *dimensionnement en fonction des performances*.  | La valeur par défaut est le 95e centile.
    **Niveau tarifaire** | Vous pouvez spécifier le [niveau tarifaire (de base/standard)](../virtual-machines/windows/sizes-general.md) des machines virtuelles Azure cibles. Par exemple, si vous envisagez de migrer un environnement de production, vous pouvez prendre en compte le niveau Standard, qui fournit des machines virtuelles avec une faible latence, mais est sans doute plus coûteux. En revanche, dans un environnement de développement et de test, vous pouvez prendre en compte le niveau de base, qui fournit des machines virtuelles avec une latence plus élevée, moins coûteuses. | Par défaut le niveau [Standard](../virtual-machines/windows/sizes-general.md) est utilisé.
    **Facteur de confort** | Azure Migrate considère une mémoire tampon (facteur de confort) au cours de l’évaluation. Cette mémoire tampon est appliquée sur des données d’utilisation de l’ordinateur pour les machines virtuelles (processeur, mémoire, disque et réseau). Le facteur de confort prend en compte les problèmes, tels que l’utilisation saisonnière, l’historique des performances de courte durée et l’augmentation probable de l’utilisation future.<br/><br/> Par exemple, une machine virtuelle de 10 cœurs avec 20 % d’utilisation correspond normalement à une machine virtuelle à 2 cœurs. Toutefois, avec un facteur de confort de 2.0x, le résultat est une machine virtuelle de 4 cœurs. | Le paramètre par défaut est 1.3x.
    **Offer** | [Offre Azure](https://azure.microsoft.com/support/legal/offer-details/) pour laquelle vous êtes inscrit. | [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) est la valeur par défaut.
    **Devise** | Devise de facturation. | La valeur par défaut est le dollar américain.
    **Remise (%)** | Remise propre à un abonnement cumulable avec l’offre Azure. | Le paramètre par défaut est 0 %.
    **Azure Hybrid Benefit** | Spécifiez si vous disposez de Software Assurance et que vous êtes éligible à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Si la valeur est Oui, les prix non-Microsoft Azure sont envisagés pour les machines virtuelles Windows. | La valeur par défaut est Oui.

3. Cliquez sur **Enregistrer** pour mettre à jour l’évaluation.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
