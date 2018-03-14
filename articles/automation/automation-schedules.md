---
title: "Planifications dans Azure Automation | Microsoft Docs"
description: "Les planifications Automation permettent de planifier les Runbooks dans Azure pour qu'ils démarrent automatiquement. Décrit comment créer et gérer une planification afin de pouvoir démarrer automatiquement un Runbook à un instant donné ou selon une planification périodique."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 6ad70d736cd0a267ace3ade0a1ecfea38128ac72
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planification d'un Runbook dans Azure Automation
Pour planifier le démarrage d'un Runbook dans Azure Automation à une heure spécifiée, liez-le à une ou plusieurs planifications. Une planification peut être configurée pour une exécution des runbooks ponctuelle ou régulière à des heures et jours précis dans le portail Azure. Vous pouvez également planifier une exécution hebdomadaire ou mensuelle, ou des jours spécifiques de la semaine ou du mois, ou encore un jour particulier du mois.  Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés.

> [!NOTE]
> Les planifications ne prennent pas en charge les configurations Azure Automation DSC pour le moment.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Applets de commande Windows PowerShell
Les applets de commande du tableau suivant permettent de créer et de gérer les planifications avec Windows PowerShell dans Azure Automation. Elles sont fournies dans le cadre du [module Azure PowerShell](/powershell/azure/overview).

| Applets de commande | Description |
|:--- |:--- |
| **Applets de commande Azure Resource Manager** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Récupère une planification. |
| [Nouvelle AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crée une planification. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Supprime une planification. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Définit les propriétés d'une planification existante. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Récupère les Runbooks planifiés. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associe un Runbook à une planification. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocie un Runbook d'une planification. |
| **Applets de commande de la gestion des services Azure** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Récupère une planification. |
| [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Crée une planification. |
| [Remove-AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Supprime une planification. |
| [Set-AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Définit les propriétés d'une planification existante. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Récupère les Runbooks planifiés. |
| [Register-AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Associe un Runbook à une planification. |
| [Unregister-AzureAutomationScheduledRunbook](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Dissocie un Runbook d'une planification. |

## <a name="creating-a-schedule"></a>Création d'une planification
Vous pouvez utiliser le portail Azure ou Windows PowerShell pour créer une planification de runbooks. Vous avez également la possibilité de créer une planification lorsque vous liez un Runbook à une planification à l’aide du portail Azure Classic ou du portail Azure.

> [!NOTE]
> Azure Automation utilise les modules les plus récents de votre compte Automation durant l’exécution d’un nouveau travail planifié.  Pour éviter d’affecter vos runbooks et les processus qu’ils automatisent, vous devez tout d’abord tester les runbooks ayant des planifications associées avec un compte Automation dédié au test.  Cela permet de vérifier que vos runbooks planifiés continuent de fonctionner correctement et, dans le cas contraire, vous pouvez résoudre les problèmes et appliquer les modifications nécessaires avant de migrer la version mise à jour du runbook en production.  
>  Votre compte Automation n’obtient pas automatiquement les nouvelles versions des modules, sauf si vous les avez mis à jour manuellement en sélectionnant l’option [Mettre à jour les modules Azure](automation-update-azure-modules.md) du panneau **Modules**. 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Pour créer une planification à l’aide du portail Azure
1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Planifications** dans la section **Ressources partagées** à gauche. 
2. Cliquez sur **Ajouter une planification** en haut de la page.
4. Dans le volet **Nouvelle planification**, tapez un **Nom** et éventuellement une **Description** pour la nouvelle planification.
5. Indiquez si la planification doit s’exécuter une seule fois ou selon un calendrier récurrent en sélectionnant **Une fois** ou **Périodicité**.  Si vous sélectionnez **Une fois**, indiquez une **Heure de début**, puis cliquez sur **Créer**.  Si vous sélectionnez **Périodicité**, spécifiez une **Heure de début** et indiquez la fréquence à laquelle vous souhaitez répéter le Runbook : par **heure**, par **jour**, par **semaine** ou par **mois**.  Si vous sélectionnez **semaine** ou **mois** dans la liste déroulante, l’option **Périodicité** apparaît dans le volet. Sélectionnez cette option pour accéder au volet d’options **Périodicité** et sélectionner le jour de la semaine si vous avez sélectionné **semaine**.  Si vous avez sélectionné **mois**, vous pouvez choisir par **jours de la semaine** ou spécifier des jours précis du mois dans le calendrier. Pour finir, indiquez si vous souhaitez ou non exécuter le runbook le dernier jour du mois, puis cliquez sur **OK**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Pour créer une planification avec Windows PowerShell
Vous pouvez utiliser l’applet de commande [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) pour créer une planification dans Azure Automation pour les Runbooks classiques, ou utiliser l’applet de commande [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) pour les Runbooks exécutés dans le portail Azure. Vous devez spécifier l’heure de début de la planification et indiquer sa fréquence d’exécution.

Les exemples de commandes suivants montrent comment créer une planification le 15 et le 30 de chaque mois à l’aide d’une cmdlet Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Les exemples de commandes suivants montrent comment créer une planification qui s'exécute chaque jour à 15h30 à partir du 20 janvier 2015, à l’aide d’une applet de commande Azure Service Management.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Liaison d'une planification à un Runbook
Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés. Si un Runbook possède des paramètres, vous pouvez leur fournir des valeurs. Vous devez fournir des valeurs pour tous les paramètres obligatoires et vous pouvez fournir des valeurs pour tous les paramètres facultatifs.  Ces valeurs sont utilisées à chaque démarrage du Runbook par cette planification.  Vous pouvez attacher le même Runbook à une autre planification et spécifier différentes valeurs de paramètre.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Pour lier une planification à un Runbook avec le portail Azure
1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Runbooks** dans la section **Automatisation de processus** à gauche.
2. Cliquez sur le nom du Runbook à planifier.
3. Si le Runbook n'est pas actuellement lié à une planification, vous avez la possibilité de créer une planification ou de le lier à une planification existante.  
4. Si le Runbook possède des paramètres, vous pouvez sélectionner l’option **Modifier les paramètres d’exécution (par défaut : Azure)** pour accéder au volet **Paramètres**, dans lequel vous pouvez saisir les informations correspondantes.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Pour lier une planification à un Runbook avec Windows PowerShell
Vous pouvez utiliser l’applet de commande [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) pour lier une planification à un Runbook classique ou l’applet de commande [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) dans le cas des Runbooks exécutés dans le portail Azure.  Vous pouvez spécifier les valeurs des paramètres du Runbook avec le paramètre Parameters. Pour plus d'informations sur la spécification des valeurs des paramètres, consultez [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) .

Les exemples de commandes suivants montrent comment lier une planification à un Runbook à l’aide d’une applet de commande Azure Resource Manager avec des paramètres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Les exemples de commandes suivants montrent comment lier une planification à l’aide d’une applet de commande Azure Service Management avec des paramètres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Désactivation d'une planification
Lorsque vous désactivez une planification, les Runbooks qui y sont liés ne s'exécutent plus sur cette planification. Vous pouvez désactiver manuellement une planification ou affecter un délai d’expiration aux planifications selon une certaine fréquence lors de leur création. Lorsque le délai d'expiration est atteint, la planification est désactivée.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Pour désactiver une planification à partir du portail Azure
1. Dans le portail Azure, accédez à votre compte Automation et sélectionnez **Planifications** dans la section **Ressources partagées** à gauche.
2. Cliquez sur le nom d’une planification pour ouvrir le volet Détails.
3. Remplacez **Activé** par **Non**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Pour désactiver une planification avec Windows PowerShell
Vous pouvez utiliser l’applet de commande [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) pour modifier les propriétés d’une planification existante pour un Runbook classique, ou utiliser l’applet de commande [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) pour les Runbooks exécutés dans le portail Azure. Pour désactiver la planification, spécifiez la valeur **false** pour le paramètre **IsEnabled**.

Les exemples de commandes suivants montrent comment désactiver une planification pour un Runbook à l’aide d’une applet de commande Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Les exemples de commandes suivants montrent comment désactiver une planification à l’aide de l’applet de commande Azure Service Management.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Étapes suivantes
* Pour vous familiariser avec les Runbooks dans Azure Automation, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md) 

