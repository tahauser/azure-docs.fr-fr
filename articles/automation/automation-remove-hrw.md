---
title: Supprimer des runbooks Workers hybrides Azure Automation
description: Cet article fournit des informations sur la gestion de Runbooks Worker hybrides Azure Automation qui vous permettent d’exécuter des Runbooks sur les machines de votre centre de données local ou de votre environnement cloud.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0b8f951bbd9712e3d20c3286ef3571e287499c68
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Supprimer des runbooks Workers hybrides Azure Automation

La fonctionnalité de Runbook Worker hybride d’Azure Automation vous permet d’exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Cet article vous guide tout au long de la procédure de suppression d’un Worker hybride d’un ordinateur local.

## <a name="removing-hybrid-runbook-worker"></a>Suppression de la fonctionnalité Runbook Worker hybride

Vous pouvez supprimer un ou plusieurs Runbook Workers hybrides d’un groupe ou supprimer le groupe, selon vos besoins.  Pour supprimer un Runbook Worker hybride à partir d’un ordinateur local, procédez comme suit.

1. Sur le Portail Azure, accédez à votre compte Automation.  
2. À partir du panneau **Paramètres**, sélectionnez **Clés** et notez les valeurs des champs **URL** et **Clé d’accès primaire**.  Vous aurez besoin de ces informations dans l’étape suivante.
3. Ouvrez une session PowerShell en mode administrateur et exécutez la commande suivante - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Utilisez le commutateur **-Verbose** pour afficher un journal détaillé du processus de suppression.

> [!NOTE]
> Cela ne supprime pas Microsoft Monitoring Agent de l’ordinateur, mais seulement les fonctionnalités et la configuration du rôle Runbook Worker hybride.  

## <a name="remove-hybrid-worker-groups"></a>Suppression de groupes de Workers hybrides

Pour supprimer un groupe, vous devez tout d’abord supprimer les Runbook Workers hybrides de chaque ordinateur membre du groupe à l’aide de la procédure indiquée plus haut, puis effectuer les opérations suivantes pour supprimer le groupe.  

1. Dans le portail Azure, ouvrez le compte Automation.
1. Sous **Automatisation de processus**, sélectionnez **Groupes de Workers hybrides**. Sélectionnez le groupe que vous souhaitez supprimer.  Une fois que vous avez sélectionné le groupe, le panneau de propriétés du **groupe de Workers hybrides** s’affiche.<br> ![Panneau Groupes de Runbook Workers hybrides](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
2. Dans le panneau des propriétés du groupe sélectionné, cliquez sur **Supprimer**.  Un message apparaît, vous invitant à confirmer cette action : sélectionnez **Oui** si vous êtes sûr de vouloir continuer.<br> ![Boîte de dialogue de confirmation de suppression du groupe](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="next-steps"></a>Étapes suivantes

* Consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.
* Pour plus d’informations sur l’installation de Runbooks Worker hybrides Windows, consultez [Runbook Worker hybride Windows Azure Automation](automation-windows-hrw-install.md).
* Pour plus d’informations sur l’installation de Runbooks Worker hybrides Linux, consultez [Runbook Worker hybride Linux Azure Automation](automation-linux-hrw-install.md).