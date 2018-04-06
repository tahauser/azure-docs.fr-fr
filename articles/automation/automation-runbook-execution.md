---
title: Exécution d'un Runbook dans Azure Automation
description: Décrit les détails du traitement d'un Runbook dans Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 18059ef1e0efba4f030a6e99198f0b7c72b7daf3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="runbook-execution-in-azure-automation"></a>Exécution d'un Runbook dans Azure Automation
Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Une tâche est une instance d'exécution unique d'un Runbook. Un travail Azure Automation est assigné pour exécuter chaque tâche. Même si les travaux sont partagés par plusieurs comptes Azure, les tâches des différents comptes Automation sont isolées les unes des autres. Vous n'avez pas le contrôle du travail qui traite la demande de votre tâche. Un même Runbook peut avoir plusieurs tâches s'exécutant à la fois.  L’environnement d’exécution pour les travaux du même compte Automation peut être réutilisé. Lorsque vous affichez la liste des Runbooks du portail Azure, vous voyez l'état de toutes les tâches démarrées pour chaque Runbook. Vous pouvez afficher la liste des tâches de chaque Runbook pour en assurer le suivi de l'état. Pour obtenir une description des différents états des tâches, consultez [États des tâches](#job-statuses).

Le diagramme suivant illustre le cycle de vie d’une tâche de Runbook pour des [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) et des [Runbooks de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![États des tâches - Workflow PowerShell](./media/automation-runbook-execution/job-statuses.png)

Le diagramme suivant illustre le cycle de vie d'une tâche de Runbook pour des [Runbooks PowerShell](automation-runbook-types.md#powershell-runbooks).

![États des tâches - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Vos tâches auront accès à vos ressources Azure en créant une connexion à votre abonnement Azure. Ils auront uniquement accès aux ressources de votre centre de données si ces ressources sont accessibles depuis le cloud public.

## <a name="job-statuses"></a>États des tâches
Le tableau suivant décrit les différents statuts possibles pour une tâche.

| Statut | Description |
|:--- |:--- |
| Completed |La tâche s'est terminée avec succès. |
| Échec |Pour des [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md), le Runbook n’a pas pu être compilé.  Pour des [Runbooks de script PowerShell](automation-runbook-types.md), le Runbook n'a pas pu démarrer ou la tâche a rencontré une exception. |
| Échec, en attente de ressources |La tâche a échoué, car elle a atteint la limite de [répartition de charge équilibrée](#fair-share) trois fois et a démarré à partir du même point de contrôle ou à partir du début du Runbook à chaque fois. |
| Mis en file d'attente. |La tâche attend que les ressources d'un travail Automation deviennent disponibles afin de pouvoir être démarrée. |
| Démarrage en cours |La tâche a été attribuée à un travail et le système est en train de le démarrer. |
| Reprise |Le système est en cours de reprise de la tâche après qu'elle a été suspendue. |
| Exécution |La tâche est en cours d'exécution. |
| En cours d'exécution, en attente de ressources |La tâche a été déchargée, car elle a atteint la limite de [répartition de charge équilibrée](#fair-share) . Elle reprend bientôt depuis son dernier point de contrôle. |
| Arrêté |La tâche a été arrêtée par l'utilisateur avant qu'elle n'ait été terminée. |
| En cours d’arrêt |Le système est en cours d'arrêt de la tâche. |
| Interrompu |La tâche a été suspendue par l'utilisateur, le système ou une commande du Runbook. Une tâche qui est interrompue peut être démarrée à nouveau et reprend à partir de son dernier point de contrôle ou à partir du début du Runbook s'il n'a aucun point de contrôle. Le Runbook est uniquement interrompu par le système en cas d'exception. Par défaut, ErrorActionPreference est définie sur **Continuer**, ce qui signifie que la tâche se poursuit en cas d'erreur. Si cette préférence est définie sur **Arrêter**, la tâche s'interrompt en cas d'erreur.  S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement. |
| Suspension |Le système tente de suspendre la tâche à la demande de l'utilisateur. Le Runbook doit atteindre son prochain point de contrôle avant de pouvoir être suspendu. S'il a déjà passé le dernier point de contrôle, il se termine avant d'être suspendu.  S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Affichage de l’état du travail à partir du portail Azure
Vous pouvez afficher une synthèse de l’état de toutes les tâches du Runbook ou explorer les détails d’une tâche spécifique du Runbook dans le portail Azure ou en configurant l’intégration à votre espace de travail Log Analytics pour transférer l’état des tâches et les flux de tâches de Runbook.  Pour plus d’informations sur l’intégration avec Log Analytics, voir [Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics](automation-manage-send-joblogs-log-analytics.md).  

### <a name="automation-runbook-jobs-summary"></a>Résumé des tâches de Runbook Automation
À droite de votre compte Automation, vous pouvez observer un résumé de toutes les tâches de Runbook pour un compte Automation sélectionné sous la vignette **Statistiques des tâches**.<br><br> ![Vignette Statistiques des tâches](./media/automation-runbook-execution/automation-account-job-status-summary.png).<br> Cette vignette affiche un nombre et une représentation graphique de l’état de toutes les tâches exécutées.  

Cliquez sur la vignette pour afficher le panneau **Tâches**, qui comprend une liste récapitulative de toutes les tâches exécutées avec leur état ainsi que les heures de début et de fin de leur exécution.<br><br> ![Panneau Tâches de compte Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  Pour filtrer la liste des tâches, sélectionnez **Filtrer les tâches**, puis filtrez sur un Runbook spécifique, un état de tâche ou dans la liste déroulante, la plage de dates/heures pour y effectuer une recherche.<br><br> ![Filtrer l’état des tâches](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Vous pouvez également afficher les détails d’un résumé de tâche d’un Runbook spécifique en sélectionnant celui-ci dans le panneau **Runbooks** de votre compte Automation, puis sélectionner la vignette **Tâches**.  Dans le panneau **Tâches** qui s’ouvre, vous pouvez cliquer sur l’enregistrement d’une tâche pour en afficher les détails et la sortie.<br><br> ![Panneau Tâches de compte Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>Résumé des tâches
Vous pouvez afficher la liste de toutes les tâches qui ont été créées pour un Runbook donné et leur état le plus récent. Vous pouvez filtrer cette liste par état de la tâche et par plage de dates de la dernière modification de la tâche. Pour afficher des informations détaillées et la sortie, cliquez sur le nom d’une tâche. La vue détaillée de la tâche inclut les valeurs des paramètres du Runbook qui ont été fournies à cette tâche.

Vous pouvez utiliser les étapes suivantes pour afficher les tâches d'un Runbook.

1. Dans le portail Azure, sélectionnez **Automation**, puis le nom d’un compte Automation.
2. À partir du concentrateur, sélectionnez **Runbooks** puis, dans le panneau **Runbooks**, sélectionnez un Runbook dans la liste.
3. Dans le panneau du Runbook sélectionné, cliquez sur la vignette **Tâches**.
4. Cliquez sur une des tâches de la liste et dans le panneau de détails des tâches Runbook, vous pouvez afficher ses détails et sa sortie.

## <a name="retrieving-job-status-using-windows-powershell"></a>Récupération de l'état d'une tâche à l'aide de Windows PowerShell
Vous pouvez utiliser [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) pour récupérer les tâches créées pour un Runbook et les détails d’une tâche particulière. Si vous démarrez un Runbook avec Windows PowerShell à l’aide de [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), il retourne la tâche résultante. Utilisez [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt619440.aspx) pour obtenir la sortie d’une tâche.

Les exemples de commandes suivants récupèrent la dernière tâche d’un exemple de Runbook et affichent son état, les valeurs fournies aux paramètres du Runbook et la sortie de la tâche.

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>répartition de charge équilibrée
Afin de partager les ressources entre tous les Runbooks du cloud, Azure Automation décharge temporairement toute tâche après leur exécution pendant 3 heures.  Pendant ce temps, les tâches pour [Runbooks basés sur PowerShell](automation-runbook-types.md#powershell-runbooks) sont arrêtées et non redémarrées.  L’état de la tâche indique **Arrêté**.  Ce type de Runbook est toujours redémarré depuis le début puisqu’il ne prend pas en charge les points de contrôle.  

[Les Runbooks basés sur le flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) reprennent depuis leur dernier [point de contrôle](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints).  Après avoir été exécutée pendant trois heures, la tâche Runbook est interrompue par le service et son état indique **En cours d’exécution, en attente de ressources**.  Lorsqu’un bac à sable devient disponible, le Runbook est automatiquement redémarré par le service Automation et reprend à partir du dernier point de contrôle.  Ce comportement de flux de travail PowerShell est normal pour la suspension/reprise.  Si le Runbook dépasse à nouveau trois heures d’exécution, le processus se répète, jusqu'à trois fois.  Après le troisième redémarrage, si le Runbook n’est toujours pas terminé en trois heures, la tâche du Runbook échoue et l’état de la tâche affiche **Échec, en attente de ressources**.  Dans ce cas, vous recevez l'exception suivante avec l'échec.

*La tâche ne peut pas continuer, car elle a été exclue à plusieurs reprises du même point de contrôle. Assurez-vous que votre Runbook n’effectue pas des opérations de longue durée sans conserver son état.*

L'objectif est d'empêcher que les Runbooks ne s'exécutent indéfiniment sans s'achever, pour protéger le service, car ils ne sont pas en mesure d'accéder au point de contrôle suivant sans être à nouveau déchargés.

Si le Runbook n'a aucun point de contrôle ou si la tâche n'a pas atteint le premier point de contrôle avant d'être déchargée, il redémarre depuis le début.  

Lorsque vous créez un Runbook, vous devez vous assurer que la durée d'exécution de toute activité entre deux points de contrôle ne dépasse pas 3 heures. Vous devrez peut-être ajouter des points de contrôle à votre Runbook pour vous assurer qu'il n'excède pas cette limite de trois heures ou n’interrompt pas les opérations de longue durée. Par exemple, votre Runbook peut effectuer une réindexation sur une base de données SQL volumineuse. Si cette opération unique ne se termine pas dans la limite de la répartition de charge équilibrée, la tâche est déchargée et redémarrée depuis le début. Dans ce cas, vous devez décomposer l'opération de réindexation en plusieurs étapes, comme la réindexation d'une table à la fois, puis insérer un point de contrôle après chaque opération, afin que la tâche puisse reprendre après que la dernière opération s'est terminée.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook dans Azure Automation, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md)

