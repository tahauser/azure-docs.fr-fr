---
title: "Considérations relatives à la conception des groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: "En savoir plus sur les considérations relatives à la conception des groupes de machines virtuelles identiques Azure"
keywords: machine virtuelle linux, groupes de machines virtuelles identiques
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: efb9f7f7daa5dbb8cd3120b21ef812106fdc7fb9
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="design-considerations-for-scale-sets"></a>Considérations relatives à la conception des groupes de machines virtuelles identiques
Cet article présente les considérations à prendre en compte pour créer des groupes de machines virtuelles identiques. Pour plus d'informations sur les groupes de machines virtuelles identiques, reportez-vous à la rubrique [Présentation des groupes de machines virtuelles identiques](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quand utiliser des groupes identiques plutôt que des machines virtuelles individuelles ?
En règle générale, les groupes identiques sont utiles pour le déploiement d’infrastructures hautement disponibles où un ensemble de machines a une configuration similaire. Toutefois, certaines fonctionnalités sont uniquement disponibles dans les groupes identiques, tandis que d’autres le sont seulement dans les machines virtuelles individuelles. Afin de prendre une décision avisée et déterminer dans quel cas utiliser l’une ou l’autre de ces technologies, vous devez tout d’abord examiner certaines des fonctionnalités couramment utilisées disponibles avec les groupes identiques, mais pas avec les machines virtuelles individuelles :

### <a name="scale-set-specific-features"></a>Fonctionnalités spécifiques des groupes identiques

- Une fois la configuration du groupe identique spécifiée, vous pouvez mettre à jour la propriété « capacity » pour déployer davantage de machines virtuelles en parallèle. Cette approche est beaucoup plus simple que d’écrire un script pour orchestrer le déploiement d’un grand nombre de machines virtuelles individuelles en parallèle.
- Vous pouvez [utiliser la mise à l’échelle automatique d’Azure pour dimensionner automatiquement un groupe identique](./virtual-machine-scale-sets-autoscale-overview.md), mais pas les machines virtuelles individuelles.
- Vous pouvez [réinitialiser les machines virtuelles des groupes identiques](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm), mais [pas les machines virtuelles individuelles](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Vous pouvez [sur-approvisionner](./virtual-machine-scale-sets-design-overview.md) les machines virtuelles d’un groupe identique pour une fiabilité accrue et un déploiement plus rapide. Vous ne pouvez pas adopter cette approche avec les machines virtuelles individuelles à moins d’écrire un code personnalisé à cette fin.
- Vous pouvez spécifier une [stratégie de mise à niveau](./virtual-machine-scale-sets-upgrade-scale-set.md) pour faciliter le déploiement des mises à niveau sur les machines virtuelles de votre groupe identique. Avec des machines virtuelles individuelles, vous devez orchestrer les mises à jour vous-même.

### <a name="vm-specific-features"></a>Fonctionnalités spécifiques des machines virtuelles

Certaines fonctionnalités sont actuellement disponibles uniquement dans les machines virtuelles :

- Vous pouvez attacher des disques de données à des machines virtuelles individuelles spécifiques, mais ces derniers sont configurés pour toutes les machines virtuelles dans un groupe identique.
- Vous pouvez attacher des disques de données non vides à des machines virtuelles individuelles, mais pas aux machines virtuelles d’un groupe identique.
- Vous pouvez prendre un instantané d’une machine virtuelle individuelle, mais cela n’est pas possible dans un groupe identique.
- Vous pouvez capturer une image à partir d’une machine virtuelle individuelle, mais cela n’est pas possible dans un groupe identique.
- Vous pouvez migrer une machine virtuelle individuelle à partir de disques natifs vers des disques gérés, mais cela n’est pas possible dans un groupe identique.
- Vous pouvez assigner des adresses IP publiques IPv6 aux cartes d’interface réseau des machines virtuelles individuelles, mais cela n’est pas possible dans un groupe identique. Vous pouvez assigner des adresses IP publiques IPv6 aux équilibreurs de charge des machines virtuelles individuelles ou des machines de groupes identiques.

## <a name="storage"></a>Stockage

### <a name="scale-sets-with-azure-managed-disks"></a>Groupes identiques avec Azure Managed Disks
Les groupes identiques peuvent être créés avec [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) plutôt qu’à partir des comptes de stockage Azure traditionnels. Managed Disks permet de bénéficier des avantages suivants :
- Vous n’êtes pas obligé de créer au préalable un ensemble de comptes de stockage Azure pour les machines virtuelles du groupe identique.
- Vous pouvez définir des [disques de données associés](virtual-machine-scale-sets-attached-disks.md) pour les machines virtuelles de votre groupe identique.
- Les groupes identiques peuvent être configurés pour [prendre en charge jusqu’à 1 000 machines virtuelles par groupe](virtual-machine-scale-sets-placement-groups.md). 

Si vous avez déjà un modèle, vous pouvez également [le mettre à jour pour qu’il utilise Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Stockage géré par l’utilisateur
Un groupe identique qui n’est pas défini avec Azure Managed Disks repose sur les comptes de stockage créés par l’utilisateur pour stocker les disques du système d’exploitation des machines virtuelles dans le groupe. Un rapport de 20 machines virtuelles maximum par compte de stockage est recommandé pour atteindre la valeur d’E/S maximale, mais aussi pour tirer parti du _sur-approvisionnement_ (voir ci-dessous). Il est également recommandé de répartir dans l’alphabet les premiers caractères des noms de comptes de stockage. Cette méthode améliore la répartition sur les différents systèmes internes. 


## <a name="overprovisioning"></a>Sur-approvisionnement
Par défaut, les groupes identiques activent « le sur-approvisionnement » de machines virtuelles. Avec le sur-approvisionnement activé, le groupe identique crée en réalité plus de machines virtuelles par rapport au nombre demandé, puis supprime les machines virtuelles supplémentaires une fois ce nombre de machines virtuelles approvisionné. Le sur-approvisionnement améliore les taux de réussite de l’approvisionnement et réduit la durée du déploiement. Vous n’êtes pas facturé pour les machines virtuelles supplémentaires et elles ne seront pas comptabilisées dans vos limites de quotas.

Bien que le sur-approvisionnement améliore les taux de réussite de l’approvisionnement, il peut provoquer un comportement déroutant si une application n’est pas conçue pour gérer les machines virtuelles qui disparaissent, puis réapparaissent. Pour désactiver le sur-approvisionnement, vérifiez que votre modèle contient la chaîne suivante : `"overprovision": "false"`. Vous trouverez plus de détails dans la [documentation de l’API REST de groupes identiques](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Si votre groupe identique utilise le stockage géré par l’utilisateur et que vous désactivez le sur-approvisionnement, vous pouvez avoir plus de 20 machines virtuelles par compte de stockage, mais il n’est pas recommandé de dépasser 40 pour des raisons de performances d’E/S. 

## <a name="limits"></a>limites
Un groupe identique, basé sur une image de la Place de marché (également appelée image de plateforme) et configuré pour utiliser Azure Managed Disks, peut prendre en charge jusqu’à 1 000 machines virtuelles. Si vous configurez votre groupe identique pour prendre en charge plus de 100 machines virtuelles, tous les scénarios ne fonctionnent pas de la même manière (par exemple l’équilibrage de charge). Pour plus d’informations, consultez [Utilisation de grands groupes de machines virtuelles identiques](virtual-machine-scale-sets-placement-groups.md). 

Un groupe identique configuré avec des comptes de stockage gérés par l’utilisateur est actuellement limité à 100 machines virtuelles (et 5 comptes de stockage sont recommandés pour cette échelle).

Un groupe identique basé sur une image personnalisée (créée par vous-même) peut prendre en charge jusqu’à 300 machines virtuelles s’il est configuré avec Azure Managed Disks. Si le groupe identique est configuré avec des comptes de stockage gérés par l’utilisateur, il doit créer tous les disques durs virtuels du disque du système d’exploitation dans un même compte de stockage. Par conséquent, le nombre maximal recommandé de machines virtuelles dans un groupe identique basé sur une image personnalisée et dont le stockage est géré par l’utilisateur est de 20. Si vous désactivez le sur-approvisionnement, vous pouvez aller jusqu’à 40.

Si vous souhaitez avoir plus de machines virtuelles que ne l’autorisent ces limites, vous devez déployer plusieurs groupes identiques, comme indiqué dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

