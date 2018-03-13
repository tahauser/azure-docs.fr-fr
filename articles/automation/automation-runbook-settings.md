---
title: "Paramètres du Runbook | Microsoft Docs"
description: "Décrit les paramètres de configuration d'un Runbook dans Azure Automation et explique comment les modifier à l'aide du portail Azure et de Windows PowerShell."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
ms.openlocfilehash: 527c40c4a0c5c9a0eb4745f542a8afcc63281416
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="runbook-settings"></a>Paramètres du Runbook
Chaque Runbook dans Azure Automation a plusieurs paramètres qui lui permettent d'être identifié et de modifier son comportement de journalisation. Chacun de ces paramètres est décrit ci-dessous, suivi des procédures sur la façon de les modifier.

## <a name="settings"></a>Paramètres
### <a name="name-and-description"></a>Nom et description
Vous ne pouvez pas modifier le nom d'un Runbook une fois qu'il a été créé. La description est facultative et peut comporter jusqu'à 512 caractères.

### <a name="tags"></a>Balises
Les balises permettent d'attribuer des mots et des expressions distincts pour permettre d'identifier un Runbook. Par exemple, quand vous envoyez un Runbook à [PowerShell Gallery](https://www.powershellgallery.com/), vous spécifiez certaines balises pour identifier les catégories dans lesquelles le Runbook doit être répertorié. Vous pouvez spécifier plusieurs balises pour un Runbook en les séparant par des virgules.

### <a name="logging"></a>Journalisation
Par défaut, les informations de commentaires et de progression ne sont pas écrites dans l'historique des tâches. Vous pouvez modifier les paramètres d'un Runbook donné pour enregistrer ces informations. Pour en savoir plus sur ces informations, consultez [Sortie et messages de runbook](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Modification des paramètres du Runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Modification des paramètres du Runbook avec le portail Azure
Vous pouvez modifier les paramètres d’un Runbook dans le portail Azure à partir du panneau **Paramètres** du Runbook.

1. Dans le portail Azure, sélectionnez **Automation** , puis cliquez sur le nom d'un compte Automation.
2. Sélectionnez l'onglet **Runbooks** .
3. Cliquez sur le nom d’un Runbook pour accéder à son panneau Paramètres. De là, vous pouvez spécifier ou modifier des balises, la description du Runbook, configurer la journalisation et les paramètres de traçage, et accéder aux outils de prise en charge pour vous aider à résoudre les problèmes.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Modification des paramètres du Runbook avec Windows PowerShell
Vous pouvez utiliser l’applet de commande [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) pour modifier les paramètres d’un Runbook. Si vous souhaitez spécifier plusieurs balises, vous pouvez fournir au paramètre Balises un tableau ou une chaîne unique avec les valeurs délimitées par des virgules. Vous pouvez obtenir les balises actives avec [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Les exemples de commandes suivants montrent comment définir les propriétés d'un Runbook. Cet exemple ajoute trois balises aux balises existantes et spécifie que les informations de commentaires doivent être enregistrées.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Étapes suivantes
* Pour découvrir comment créer et récupérer la sortie et les messages d’erreur à partir des Runbooks, consultez [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md) 
* Pour découvrir comment ajouter un Runbook qui a déjà été développé par la Communauté ou une autre source, ou pour créer votre propre Runbook, consultez [Création ou importation d’un runbook](automation-creating-importing-runbook.md) 

