---
title: "Créer une instance Gestion des API Azure à l’aide de PowerShell | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour créer une instance Gestion des API Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: d33a37002f1e3a2684d4b93682dbd48327f3312b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Créer une instance du service Gestion des API Azure

Le service Gestion des API Azure (API Management, APIM) permet aux organisations de publier des API pour des développeurs externes, partenaires et internes afin de libérer le potentiel de leurs données et de leurs services. Il offre les compétences essentielles qui garantissent un programme d’API réussi au travers de l’engagement des développeurs, des perspectives commerciales, de l’analytique, de la sécurité et de la protection. APIM vous permet de créer et de gérer des passerelles d’API modernes pour des services backend existants, où qu’ils soient hébergés. Pour plus d’informations, consultez la rubrique [Vue d’ensemble](api-management-key-concepts.md).

Ce guide de démarrage rapide décrit les étapes permettant de créer une instance Gestion des API à l’aide de scripts PowerShell. Il vous montre comment utiliser **Azure Cloud Shell**, que vous pouvez exécuter à partir du portail Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Créer un service Gestion des API

L’exécution de cette opération peut prendre jusqu’à 15 minutes.

```azurepowershell-interactive
New-AzureRmApiManagement -ResourceGroupName "myResourceGroup" -Location "West US" -Name "apim-name" -Organization "myOrganization" -AdminEmail "myEmail" -Sku "Developer"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Importer et publier votre première API](import-and-publish.md)
