---
title: "Configurer des serveurs à l’état souhaité et gérer la dérive avec Azure Automation | Microsoft Docs"
description: "Didacticiel - Gérer les configurations de serveur avec Azure Automation DSC"
services: automation
documentationcenter: automation
author: georgewallace
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: c510b2244dfa85b12ed08cad9dbab75067ebe41a
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurer les serveurs à l’état souhaité et gérer la dérive

Azure Automation DSC (Configuration de l’état souhaité) vous permet de spécifier des configurations pour vos serveurs, et de vous assurer que leur état ainsi défini perdure dans le temps.



> [!div class="checklist"]
> * Intégrer une machine virtuelle à gérer par Azure Automation DSC
> * Charger une configuration dans Azure Automation
> * Compiler une configuration dans une configuration de nœud
> * Attribuer une configuration de nœud à un nœud géré
> * Vérifier l’état de conformité d’un nœud géré

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure Automation. Pour obtenir des instructions sur la création d’un compte d’identification Azure Automation, consultez [Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md).
* Une machine virtuelle Azure Resource Manager (non classique) exécutant Windows Server 2008 R2 ou version ultérieure. Pour obtenir des instructions sur la création d’une machine virtuelle, consultez [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Module Azure PowerShell, version 3.6 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Vous êtes familiarisé avec DSC. Pour en savoir plus sur DSC, consultez [Vue d’ensemble de la fonctionnalité Desired State Configuration de Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Créer et charger une configuration dans Azure Automation

Pour ce didacticiel, nous allons utiliser une configuration DSC simple qui permet de s’assurer qu’IIS est installé sur la machine virtuelle.

Pour obtenir des informations sur les configurations DSC, consultez [Configurations DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Dans un éditeur de texte, tapez la commande suivante et enregistrez-la localement sous `TestConfig.ps1`.

```powershell
configuration TestConfig {

   Node WebServer {

      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Appelez l’applet de commande `Import-AzureRmAutomationDscConfiguration` pour charger la configuration dans votre compte Automation :

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compiler une configuration dans une configuration de nœud

Une configuration DSC doit être compilée dans une configuration de nœud avant de pouvoir être affectée à un nœud.

Pour obtenir des informations sur la compilation des configurations, consultez [Configurations DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Appelez l’applet de commande `Start-AzureRmAutomationDscCompilationJob` pour compiler la configuration `TestConfig` dans une configuration de nœud :

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Une configuration de nœud nommée `TestConfig.WebServer` est créée dans votre compte Automation.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Inscrire une machine virtuelle devant être gérée par DSC

Azure Automation DSC vous permet de gérer vos machines virtuelles Azure (via les modèles de déploiement classique et Resource Manager), vos machines virtuelles locales, vos machines virtuelles Linux, vos machines virtuelles AWS et vos ordinateurs physiques en local. Dans cette rubrique, nous allons voir comment inscrire uniquement des machines virtuelles Azure Resource Manager.
Pour plus d’informations sur l’inscription d’autres types de machines, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).

Appelez l’applet de commande `Register-AzureRmAutomationDscNode` pour inscrire votre machine virtuelle auprès d’Azure Automation DSC.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

La machine virtuelle spécifiée est inscrite comme nœud DSC dans votre compte Azure Automation.

### <a name="specify-configuration-mode-settings"></a>Spécifier les paramètres du mode de configuration

Lorsque vous inscrivez une machine virtuelle comme nœud géré, vous pouvez également indiquer les propriétés de la configuration.
Par exemple, vous pouvez spécifier que l’état de la machine ne doit être appliqué qu’une seule fois (DSC n’essaie pas d’appliquer la configuration après la vérification initiale) en indiquant `ApplyOnly` comme valeur de la propriété **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Vous pouvez également préciser la fréquence à laquelle DSC vérifie l’état de configuration à l’aide de la propriété **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Pour plus d’informations sur la définition des propriétés de configuration d’un nœud géré, consultez [Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Pour plus d’informations sur les paramètres de configuration DSC, consultez [Configuration du Gestionnaire de configuration local](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Attribuer une configuration de nœud à un nœud géré

Nous pouvons désormais attribuer la configuration de nœud compilé à la machine virtuelle que nous souhaitons configurer.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Cette opération attribue la configuration de nœud nommée `TestConfig.WebServer` au nœud DSC inscrit nommé `DscVm`.
Par défaut, le nœud DSC est vérifié pour la conformité avec la configuration de nœud toutes les 30 minutes.
Pour obtenir plus d’informations sur la modification de l’intervalle de vérification de conformité, consultez [Configuration du Gestionnaire de configuration local](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Vérifier l’état de conformité d’un nœud géré

Vous pouvez obtenir des rapports sur l’état de conformité d’un nœud DSC en appelant l’applet de commande `Get-AzureRmAutomationDscNodeReport` :

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>étapes suivantes

* Pour savoir comment intégrer des nœuds devant être gérés avec Azure Automation DSC, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).
* Pour apprendre à utiliser le portail Azure et vous servir d’Automation DSC, consultez [Bien démarrer avec Azure Automation DSC](automation-dsc-getting-started.md).
* Pour en savoir plus sur la compilation des configurations DSC pour les assigner à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation DSC](automation-dsc-compile.md).
* Pour la référence de cmdlet PowerShell pour Azure Automation DSC, consultez [AzureRM.Automation](/powershell/module/azurerm.automation/#automation).
* Pour plus d’informations sur la tarification, consultez [Tarification de Automation](https://azure.microsoft.com/pricing/details/automation/).
* Pour voir un exemple d’utilisation d’Azure Automation DSC dans un pipeline de déploiement continu, consultez [Exemple d’utilisation : Déploiement continu sur des machines virtuelles à l’aide d’Automation DSC et Chocolatey](automation-dsc-cd-chocolatey.md).
