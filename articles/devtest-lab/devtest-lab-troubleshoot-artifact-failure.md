---
title: "Diagnostiquer les échecs d’artefacts dans une machine virtuelle Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment résoudre les échecs des artefacts dans Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: v-craic
ms.openlocfilehash: 6c03ce8f91ec688e32e379f1284767db9a45920c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostiquer les échecs d’artefacts dans le labo 
Une fois que vous avez créé un artefact, vous pouvez vérifier si la procédure a réussi ou échoué. Les journaux d’artefact d’Azure DevTest Labs fournissent des informations qui permettent de diagnostiquer un échec d’artefact. Vous disposez de deux options pour afficher les informations des journaux d’artefact relatives à une machine virtuelle Windows :

* Dans le portail Azure
* Dans la machine virtuelle

> [!NOTE]
> Pour que les échecs soient correctement identifiés et expliqués, il est important que la structure de l’artefact soit adéquate. Pour plus d’informations sur la bonne façon de créer un artefact, consultez la page [Créer des artefacts personnalisés](devtest-lab-artifact-author.md). L’artefact [Types de paramètres de test](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) est un exemple d’artefact structuré correctement.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Résoudre les échecs des artefacts à l’aide du portail Azure

1. Dans le portail Azure, dans la liste des ressources, sélectionnez votre laboratoire.
2. Choisissez la machine virtuelle Windows qui inclut l’artefact à analyser.
3. Dans le volet gauche, sous **GÉNÉRAL**, sélectionnez **Artefacts**. La liste des artefacts associés à cette machine virtuelle s’affiche. Le nom et l’état de l’artefact sont indiqués.

   ![État de l’artefact](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Sélectionnez un artefact qui présente l’état **Échec**. L’artefact s’ouvre. Un message d’extension comportant des détails sur l’échec de l’artefact s’affiche.

   ![Message d’erreur sur l’artefact](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Résoudre les échecs des artefacts au sein de la machine virtuelle

1. Connectez-vous à la machine virtuelle qui contient l’artefact à diagnostiquer.
2. Accédez à C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, *1.9* étant le numéro de version de l’extension de script personnalisé Azure.

   ![Fichier d’état](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Ouvrez le fichier **status**.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Joindre une machine virtuelle à un domaine Active Directory existant à l’aide d’un modèle Resource Manager dans DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [ajouter un référentiel Git à un labo](devtest-lab-add-artifact-repo.md).

