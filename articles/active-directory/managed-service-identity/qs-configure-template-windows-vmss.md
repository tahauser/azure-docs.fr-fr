---
title: "Configurer l’identité du service administré sur un groupe de machines virtuelles identiques Azure à l’aide d’un modèle"
description: "Instructions détaillées sur la configuration d’une identité du service administré (MSI) sur un groupe de machine virtuelle identique Azure, à l’aide d’un modèle Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurer une identité du service administré de machine virtuelle à l’aide d’un modèle

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré (MSI) fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprenez à activer et supprimer l’identité du service administré pour un groupe de machines virtuelles identiques Azure, à l’aide d’un modèle de déploiement Azure Resource Manager.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Activer l’identité du service administré lors de la création d’un groupe de machines virtuelles identiques Azure, ou pour un groupe de machines virtuelles identiques Azure existant

Comme pour le portail Azure et le script, les modèles Azure Resource Manager offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

   - Utiliser un [modèle personnalisé à partir de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utilisation d’un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
   - Utilisez le [projet de groupe de ressources Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quelle que soit l’option choisie, la syntaxe de modèle est identique lors du déploiement initial et lors du redéploiement. L’activation du MSI est effectuée de la même manière sur un groupe de machines virtuelles identiques Azure, nouveau ou non. En outre, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) au niveau des déploiements :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Après le chargement du modèle dans un éditeur, localisez la ressource d’intérêt `Microsoft.Compute/virtualMachineScaleSets` dans la section `resources`. La vôtre peut différer légèrement de la capture d’écran suivante, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant.
   
   ![Capture d’écran de modèle - localiser une machine virtuelle](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Utilisez la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Ajoutez ensuite l’extension de l’identité du service administré du groupe de machines virtuelles identiques en tant qu’élément `extensionsProfile`. Utilisez la syntaxe suivante :

   >[!NOTE] 
   > L’exemple suivant suppose qu’une extension du groupe de machines virtuelles identiques Windows (`ManagedIdentityExtensionForWindows`) est en cours de déploiement. À la place, vous pouvez également configurer pour Linux à l’aide de `ManagedIdentityExtensionForLinux`, pour les éléments `"name"` et `"type"`.
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

5. Lorsque vous avez terminé, votre modèle doit ressembler au suivant :

   ![Capture d’écran de modèle après mise à jour](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Supprimer l’identité du service administré d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui ne nécessite plus d’identité du service administré :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Supprimez les deux éléments qui ont été ajoutés dans la section précédente : les propriétés `"identity"` et `"extensionsProfile"`.du groupe de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes

- Pour une perspective plus large concernant sur l’identité du service administré, lisez la [Vue d’ensemble de l’identité du service administré](overview.md).

