---
title: "Utiliser PowerShell pour créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure | Microsoft Docs"
description: "Utilisez PowerShell pour créer une affectation de stratégie Azure pour identifier les ressources non conforme."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 1/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 67c779b96dab088d810d22ad3053ade106aec56a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant PowerShell

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources. Ce démarrage rapide vous guide pas à pas dans le processus de création d’une affectation de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques gérés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques gérés. Elles sont *non conformes* avec l’affectation de stratégie.

PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide décrit en détail l’utilisation de PowerShell pour créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure.

Il nécessite le module Azure PowerShell version 4.0 ou ultérieure. Exécutez  `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Avant de commencer, assurez-vous que la dernière version de PowerShell est installée. Consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) pour plus de détails.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.


## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une affectation de stratégie et attribuons la définition *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*. Cette définition de stratégie identifie les ressources qui ne sont pas conformes aux conditions définies dans la définition de stratégie.

Suivez cette procédure pour créer une affectation de stratégie.

1. Pour vous assurer que votre abonnement fonctionne avec le fournisseur de ressources, inscrivez le fournisseur de ressources Policy Insights. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer l’opération d’inscription pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire.

    Exécutez la commande suivante pour enregistrer le fournisseur de ressources :

    ```
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
```

    Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources en ayant des types de ressources de ce fournisseur de ressources dans votre abonnement.

    Pour plus de détails sur l’inscription et l’affichage des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](../azure-resource-manager/resource-manager-supported-services.md).

2. Après avoir inscrit le fournisseur de ressources, exécutez la commande suivante pour afficher toutes les définitions de stratégie et trouver celle que vous voulez affecter :

    ```powershell
$definition = Get-AzureRmPolicyDefinition
```

    Azure Policy est fourni avec des définitions de stratégie intégrées que vous pouvez utiliser. Vous voyez des définitions de stratégie intégrées comme :

    - Imposer une étiquette et sa valeur
    - Apply tag and its value
    - Require SQL Server version 12.0

3. Appliquez ensuite la définition de stratégie à l’étendue souhaitée à l’aide de l’applet de commande `New-AzureRmPolicyAssignment`.

Pour ce didacticiel, nous utilisons les informations suivantes pour la commande :

- **Nom** d’affichage pour l’affectation de stratégie. Dans ce cas, nous allons utiliser Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés).
- **Stratégie** : c’est la définition de stratégie, que vous utilisez comme base pour créer l’affectation. Dans ce cas, il s’agit de la définition de stratégie *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*.
- Une **étendue** : une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Dans cet exemple, nous assignons la définition de stratégie au groupe de ressources **FabrikamOMS**.
- **$definition** : vous devez fournir l’ID de ressource de la définition de stratégie. Dans ce cas, vous utilisez l’ID de la définition de stratégie : *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

1. Revenez à la page d’accueil d’Azure Policy.
2. Sélectionnez **Conformité** dans le volet gauche, puis recherchez **l’affectation de stratégie** que vous avez créée.

   ![Conformité à la stratégie](media/assign-policy-definition/policy-compliance.png)

   Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous l’onglet **Ressources non conformes**.

## <a name="clean-up-resources"></a>Supprimer des ressources

D’autres guides de cette collection sont basés sur ce démarrage rapide. Si vous prévoyez de continuer avec les didacticiels suivants, ne nettoyez pas les ressources créées dans ce démarrage rapide. Dans le cas contraire, supprimez l’affectation que vous avez créée en exécutant cette commande :

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie pour identifier les ressources non conformes de votre environnement Azure.

Pour plus d’informations sur l’affectation de stratégies et garantir que les ressources **futures** qui sont créées sont conformes, continuez avec le didacticiel pour :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./create-manage-policy.md)
