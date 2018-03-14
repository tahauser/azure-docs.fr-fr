---
title: "Guide pratique pour configurer une identité MSI affectée par l’utilisateur pour une machine virtuelle Azure à l’aide d’un modèle Azure"
description: "Instructions pas à pas expliquant comment configurer une identité du service administré (MSI) affectée par l’utilisateur pour une machine virtuelle Azure, à l’aide d’un modèle Azure Resource Manager."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Configurer une identité MSI (Managed Service Identity) affectée par l’utilisateur pour une machine virtuelle, à l’aide d’un modèle Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Managed Service Identity fournit aux services Azure une identité administrée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment activer et supprimer une identité du service administré affectée par l’utilisateur pour une machine virtuelle Azure, à l’aide d’un modèle de déploiement Azure Resource Manager.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure, ou sur une machine virtuelle existante

Comme pour le portail Azure et le script, les modèles Azure Resource Manager offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

   - Utiliser un [modèle personnalisé à partir de Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utilisation d’un [éditeur local JSON (VS Code, par exemple)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
   - Utilisez le [projet de groupe de ressources Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quelle que soit l’option choisie, la syntaxe de modèle est identique lors du déploiement initial et lors du redéploiement. La création et l’affectation d’une identité MSI affectée par l’utilisateur à une machine virtuelle nouvelle ou existante s’effectuent de la même manière. En outre, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) au niveau des déploiements :

1. Si vous vous connectez à Azure localement ou par le biais du portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’identité MSI et la machine virtuelle. Vérifiez également que votre compte appartient à un rôle vous donnant des autorisations en écriture sur l’abonnement ou les ressources (par exemple le rôle « Propriétaire »).

2. Après le chargement du modèle dans un éditeur, localisez la ressource d’intérêt `Microsoft.Compute/virtualMachines` dans la section `resources`. La vôtre peut différer légèrement de la capture d’écran suivante, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant.

   >[!NOTE] 
   > Cet exemple suppose que des variables telles que `vmName`, `storageAccountName` et `nicName` ont été définies dans le modèle.
   >

   ![Capture d’écran de modèle - localiser une machine virtuelle](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachines"`. Utilisez la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Puis ajoutez l’extension de l’identité du service administré de la machine virtuelle en tant qu’élément `resources`. Utilisez la syntaxe suivante :

   >[!NOTE] 
   > L’exemple suivant suppose qu’une extension de la machine virtuelle Windows (`ManagedIdentityExtensionForWindows`) est en cours de déploiement. À la place, vous pouvez également configurer pour Linux à l’aide de `ManagedIdentityExtensionForLinux`, pour les éléments `"name"` et `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

   ![Capture d’écran de modèle après mise à jour](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite plus d’identité du service administré :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel. Vérifiez également que votre compte appartient à un rôle vous donnant des autorisations en écriture sur la machine virtuelle (par exemple, le rôle « Contributeur de machines virtuelles »).

2. Supprimez les deux éléments qui ont été ajoutés dans la section précédente : la propriété `"identity"` de la machine virtuelle et la ressource `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Contenu connexe

- Pour une perspective plus large concernant sur l’identité du service administré, lisez la [Vue d’ensemble de l’identité du service administré](msi-overview.md).

