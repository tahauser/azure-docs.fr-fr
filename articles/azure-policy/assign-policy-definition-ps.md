---
title: "Utiliser PowerShell pour créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure | Microsoft Docs"
description: "Utilisez PowerShell pour créer une affectation de stratégie Azure pour identifier les ressources non conforme."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6a9b7cff1341bd898b76a226ca413b8135eec408
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant PowerShell

La première étape pour comprendre la conformité dans Azure est de savoir où vous en êtes avec vos propres ressources actuelles. Ce démarrage rapide vous guide pas à pas dans le processus de création d’une affectation de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques gérés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques gérés et qui sont par conséquent *non conformes*.


PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide décrit en détail l’utilisation de PowerShell pour créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure.

Il nécessite le module Azure PowerShell version 4.0 ou ultérieure. Exécutez  ```Get-Module -ListAvailable AzureRM``` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Avant de commencer, assurez-vous que la dernière version de PowerShell est installée. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.


## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, nous créons une affectation de stratégie et attribuons la définition *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*. Cette définition de stratégie identifie les ressources qui ne sont pas conformes aux conditions définies dans la définition de stratégie.

Suivez cette procédure pour créer une affectation de stratégie.

Exécutez la commande suivante pour afficher toutes les définitions de stratégie et trouver celle que vous voulez affecter :

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Azure Policy est fourni avec des définitions de stratégie intégrées que vous pouvez utiliser. Vous voyez des définitions de stratégie intégrées comme :

- Imposer une étiquette et sa valeur
- Apply tag and its value
- Nécessitent SQL Server version 12.0

Appliquez ensuite la définition de stratégie à l’étendue souhaitée à l’aide de l’applet de commande `New-AzureRmPolicyAssignment`.

Pour ce didacticiel, nous fournissons les informations suivantes pour la commande :
- **Nom** d’affichage pour l’affectation de stratégie. Dans ce cas, nous allons utiliser Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés).
- **Stratégie** : c’est la définition de stratégie, que vous utilisez comme base pour créer l’affectation. Dans ce cas, il s’agit de la définition de stratégie *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*.
- Une **étendue** : une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Dans cet exemple, nous affectons la définition de stratégie au groupe de ressources **FabrikamOMS**.
- **$definition** : vous devez fournir l’ID de ressource de la définition de stratégie. Dans ce cas, nous utilisons l’ID de la définition de stratégie : *Audit Virtual Machines without Managed Disks (Auditer des machines virtuelles sans disques gérés)*.

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

   Si des ressources existantes ne sont pas compatibles avec cette nouvelle affectation, elles apparaissent sous l’onglet **Ressources non compatibles**, comme illustré ci-dessus.

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
