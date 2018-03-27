---
title: "Utiliser des disques gérés avec des groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: "Découvrez pourquoi et comment utiliser des disques gérés avec des groupes de machines virtuelles identiques"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Groupes de machines virtuelles identiques Azure et disques gérés

Les [groupes de machines virtuelles identiques](/azure/virtual-machine-scale-sets/) Azure prennent en charge les machines virtuelles avec disques gérés. Les disques gérés avec groupes identiques présentent plusieurs avantages, notamment :

* Vous n’avez plus besoin de créer au préalable et de gérer des comptes de stockage pour stocker les disques du système d’exploitation pour les machines virtuelles de groupes identiques.

* Vous pouvez attacher des disques de données gérés au groupe identique.

* Avec un disque géré, un groupe identique peut atteindre une capacité de 1 000 machines virtuelles s’il est basé sur une image de plateforme, ou de 300 machines virtuelles s’il est basé sur une image personnalisée.

## <a name="get-started"></a>Prise en main

Un moyen simple de se familiariser avec les groupes de disques gérés identiques consiste à en déployer un à partir du Portail Azure. Pour plus d’informations, consultez [cet article](./virtual-machine-scale-sets-portal-create.md). Il est aussi possible, pour commencer, d’utiliser [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) afin de déployer un groupe identique. L’exemple suivant montre comment créer un groupe identique sous Ubuntu avec 10 machines virtuelles, comprenant chacune un disque de données de 50 Go et de 100 Go :

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Vous pouvez également consulter le [Référentiel GitHub de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates), dans les dossiers qui contiennent `vmss`, pour voir des exemples prédéfinis de modèles qui déploient des groupes identiques. Pour savoir quels modèles utilisent déjà des disques gérés, vous pouvez consulter [cette liste](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les disques gérés en général, consultez [cet article](../virtual-machines/windows/managed-disks-overview.md).

Pour savoir comment convertir un modèle Resource Manager afin d’approvisionner des groupes identiques avec disques gérés, consultez [cet article](./virtual-machine-scale-sets-convert-template-to-md.md). Les modifications apportées aux modèles Resource Manager s’appliquent également à l’API REST Azure.

Pour en savoir plus sur l’utilisation de disques de données gérés avec des groupes identiques, consultez [cet article](./virtual-machine-scale-sets-attached-disks.md).

Pour commencer à travailler avec des groupes identiques, consultez [cet article](./virtual-machine-scale-sets-placement-groups.md).


