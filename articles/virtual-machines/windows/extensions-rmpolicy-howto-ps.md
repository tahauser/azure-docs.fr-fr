---
title: Utiliser Azure Policy pour restreindre l’installation d’extensions de machines virtuelles | Microsoft Docs
description: Découvrez comment utiliser Azure Policy pour restreindre les déploiements d’extension.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: da5b0db997ba1aa0e998f6fe2645e955b490951d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Utiliser Azure Policy pour restreindre l’installation d’extensions sur les machines virtuelles Windows

Si vous souhaitez empêcher l’utilisation ou l’installation de certaines extensions sur vos machines virtuelles Windows, vous pouvez créer une stratégie Azure à l’aide de PowerShell afin de restreindre les extensions pour les machines virtuelles d’un groupe de ressources. 

Ce didacticiel utilise Azure PowerShell dans Cloud Shell, qui est constamment mise à jour vers la dernière version. Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Créer un fichier de règles

Pour restreindre les extensions qui peuvent être installées, vous devez disposer d’une [règle](/azure/azure-policy/policy-definition#policy-rule) qui fournit la logique permettant d’identifier l’extension.

Cet exemple montre comment interdire l’installation des extensions publiées par « Microsoft.Compute » en créant un fichier de règles dans Azure Cloud Shell. Cependant, si vous utilisez PowerShell localement, vous pouvez également créer un fichier local et remplacer le chemin d’accès ($home/clouddrive) par le chemin d’accès au fichier local sur votre ordinateur.

Dans un [interpréteur de commandes Cloud Shell](https://shell.azure.com/powershell) :

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Copiez et collez le code .json suivant dans le fichier.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Lorsque vous avez terminé, appuyez sur **Ctrl + O**, puis sur **Entrée** pour enregistrer le fichier. Accès **Ctrl + X** pour fermer le fichier et quittez.

## <a name="create-a-parameters-file"></a>Créer un fichier de paramètres

Vous avez également besoin d’un fichier de [paramètres](/azure/azure-policy/policy-definition#parameters) qui crée une structure que vous pourrez utiliser pour transmettre la liste des extensions à bloquer. 

Cet exemple montre comment créer un fichier de paramètres pour les machines virtuelles dans Cloud Shell. Cependant, si vous utilisez PowerShell localement, vous pouvez également créer un fichier local et remplacer le chemin d’accès ($home/clouddrive) par le chemin d’accès au fichier local sur votre ordinateur.

Dans un [interpréteur de commandes Cloud Shell](https://shell.azure.com/powershell) :

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Copiez et collez le code .json suivant dans le fichier.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Lorsque vous avez terminé, appuyez sur **Ctrl + O**, puis sur **Entrée** pour enregistrer le fichier. Accès **Ctrl + X** pour fermer le fichier et quittez.

## <a name="create-the-policy"></a>Création de la stratégie

Une définition de stratégie est un objet servant à stocker la configuration que vous souhaitez utiliser. La définition de stratégie fait appel aux fichiers de règles et de paramètres pour définir la stratégie. Créez une définition de stratégie à l’aide de l’applet de commande [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

 Les règles de stratégie et les paramètres correspondent aux fichiers que vous avez créés et stockés en tant que fichiers .json dans votre Cloud Shell.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Affecter la stratégie

Dans cet exemple, la stratégie est affectée à un groupe de ressources à l’aide de la commande [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). N’importe quelle machine virtuelle créée dans le groupe de ressources **myResourceGroup** ne pourra pas installer les extensions VM Access Agent ou Custom Script. 

Utilisez le cmdlet [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) pour obtenir votre ID d’abonnement à utiliser à la place de celui de l’exemple.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Tester la stratégie

Pour tester la stratégie, essayez d’utiliser l’extension d’accès de la machine virtuelle. La suite doit échouer avec le message « Set-AzureRmVMAccessExtension : ressource 'myVMAccess' a été interdite par la stratégie. »

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Dans le portail, la modification de mot de passe doit échouer avec le message « Le déploiement de modèle a échoué en raison de la violation de stratégie. » .

## <a name="remove-the-assignment"></a>Supprimer l’affectation

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Supprimer la stratégie

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Présentation d’Azure Policy](../../azure-policy/azure-policy-introduction.md).
