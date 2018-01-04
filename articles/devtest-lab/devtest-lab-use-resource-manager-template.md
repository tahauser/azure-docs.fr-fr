---
title: "Afficher et utiliser un modèle Azure Resource Manager de machine virtuelle | Microsoft Docs"
description: "Découvrez comment utiliser le modèle Azure Resource Manager à partir d’une machine virtuelle pour créer d’autres machines virtuelles"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: v-craic
ms.openlocfilehash: d3800fe360a2451bdc39644e713b82ab0608ef12
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="use-a-virtual-machines-azure-resource-manager-template"></a>Utiliser un modèle Azure Resource Manager de machine virtuelle

Lorsque vous créez une machine virtuelle dans DevTest Labs via le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), vous pouvez voir le modèle Azure Resource Manager avant d’enregistrer la machine virtuelle. Le modèle peut alors être utilisé comme base pour créer d’autres machines virtuelles lab avec les mêmes paramètres.

Cet article décrit comment afficher le modèle Resource Manager lors de la création d’une machine virtuelle et comment le déployer ultérieurement pour automatiser la création de la même machine virtuelle.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modèles Resource Manager à plusieurs machines virtuelles ou à machine virtuelle unique
Il existe deux façons de créer des machines virtuelles dans DevTest Labs à l’aide d’un modèle Resource Manager : configurer la ressource Microsoft.DevTestLab/labs/virtualmachines ou configurer la ressource Microsoft.Commpute/virtualmachines. Chaque méthode est utilisée dans des scénarios différents et nécessite des autorisations différentes.

- Les modèles Resource Manager qui utilisent un type de ressource Microsoft.DevTestLab/labs/virtualmachines (tel que déclaré dans la propriété « ressource » du modèle) peuvent configurer des machines virtuelles lab individuelles. Chaque machine virtuelle apparaît alors sous la forme d’un seul élément dans la liste de machines virtuelles DevTest Labs :

   ![Liste des machines virtuelles qui apparaissent comme un seul élément dans la liste de machines virtuelles DevTest Lab](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ce type de modèle Resource Manager peut être configuré via la commande Azure PowerShell **New-AzureRmResourceGroupDeployment** ou via la commande Azure CLI **az group deployment create**. Il requiert des autorisations d’administrateur. Les utilisateurs assignés à un rôle d’utilisateur DevTest Labs ne peuvent donc pas effectuer le déploiement. 

- Les modèles Resource Manager qui utilisent un type de ressource Microsoft.Compute/virtualmachines peuvent configurer plusieurs machines virtuelles en tant qu’environnement unique dans la liste des machines virtuelles DevTest Labs :

   ![Liste des machines virtuelles qui apparaissent comme un seul élément dans la liste de machines virtuelles DevTest Lab](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Les machines virtuelles qui se trouvent dans le même environnement peuvent être gérées ensemble et partagent le même cycle de vie. Les utilisateurs affectés à un rôle d’utilisateur DevTest Labs peuvent créer des environnements à l’aide de ces modèles, si l’administrateur a configuré le laboratoire de cette façon.

Le reste de cet article présente les modèles Resource Manager qui utilisent Mirosoft.DevTestLab/labs/virtualmachines. Ils sont utilisés par les administrateurs de laboratoire pour automatiser la création de machines virtuelles lab (par exemple, des machines virtuelles revendicables) ou la génération de l’image de référence (par exemple, Image Factory).

Les [Bonnes pratiques de création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) contiennent de nombreuses directives et suggestions pour vous aider à créer des modèles Azure Resource Manager fiables et faciles à utiliser.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Afficher et enregistrer un modèle Resource Manager de machine virtuelle
1. Suivez les étapes de la section [Créer votre première machine virtuelle dans un laboratoire](devtest-lab-create-first-vm.md) pour commencer à créer une machine virtuelle.
1. Entrez les informations requises pour votre machine virtuelle et ajoutez les artefacts que vous souhaitez pour cette machine virtuelle.
1. Au bas de la fenêtre Configurer les paramètres, choisissez **Afficher le modèle ARM**.

   ![Bouton Afficher le modèle ARM](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Copiez et enregistrez le modèle Resource Manager à utiliser ultérieurement pour créer une autre machine virtuelle.

   ![Modèle Resource Manager à enregistrer pour une utilisation ultérieure](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Après avoir enregistré le modèle Resource Manager, vous devez mettre à jour la section Paramètres du modèle avant de pouvoir l’utiliser. Vous pouvez créer un paramètre .json qui personnalise simplement les paramètres en dehors du modèle Resource Manager. 

![Personnaliser les paramètres à l’aide d’un fichier JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Déployer un modèle Resource Manager pour créer une machine virtuelle
Après avoir enregistré un modèle Resource Manager et l’avoir personnalisé selon vos besoins, vous pouvez l’utiliser pour automatiser la création de machines virtuelles. La rubrique [Déployer des ressources avec des modèles Resource Manager et Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) explique comment utiliser Azure PowerShell avec des modèles Resource Manager pour déployer vos ressources sur Azure. La rubrique [Déployer des ressources avec des modèles Resource Manager et Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) explique comment utiliser Azure CLI avec des modèles Resource Manager pour déployer vos ressources sur Azure.

> [!NOTE]
> Seul un utilisateur disposant des autorisations de propriétaire de laboratoire peut créer des machines virtuelles à partir d’un modèle Resource Manager avec Azure PowerShell. Si vous souhaitez automatiser la création de machines virtuelles à l’aide d’un modèle Resource Manager et que vous disposez uniquement d’autorisations utilisateur, vous pouvez utiliser la commande [ **az lab vm create** dans l’interface CLI](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).

### <a name="next-steps"></a>étapes suivantes
* Découvrez comment [Créer des environnements à plusieurs machines virtuelles avec les modèles Resource Manager](devtest-lab-create-environment-from-arm.md).
* Découvrez les autres modèles Resource Manager à démarrage rapide pour l’automatisation de DevTest Labs à partir du [référentiel DevTest Labs GitHub public](https://github.com/Azure/azure-quickstart-templates).
