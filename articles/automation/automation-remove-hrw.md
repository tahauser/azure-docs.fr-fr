---
title: Supprimer des runbooks Workers hybrides Azure Automation | Microsoft Docs
description: "Cet article fournit des informations sur la gestion de Runbooks Worker hybrides Azure Automation qui vous permettent d’exécuter des Runbooks sur les machines de votre centre de données local ou de votre environnement cloud."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: a897a97b9a1e259f5994a27b974eb5183fd1194b
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Supprimer des runbooks Workers hybrides Azure Automation

Vous pouvez : 

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
1. Sélectionnez la vignette **Groupes de Workers hybrides** et sélectionnez le groupe que vous souhaitez supprimer dans le panneau **Groupes de Workers hybrides**.  Une fois que vous avez sélectionné le groupe, le panneau de propriétés du **groupe de Workers hybrides** s’affiche.<br> ![Panneau Groupes de Runbook Workers hybrides](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
1. Dans le panneau des propriétés du groupe sélectionné, cliquez sur **Supprimer**.  Un message apparaît, vous invitant à confirmer cette action : sélectionnez **Oui** si vous êtes sûr de vouloir continuer.<br> ![Boîte de dialogue de confirmation de suppression du groupe](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="next-steps"></a>Étapes suivantes

* Consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.
* Pour plus d’informations sur l’installation de Runbooks Worker hybrides Windows, consultez [Runbook Worker hybride Windows Azure Automation](automation-windows-hrw-install.md).
* Pour plus d’informations sur l’installation de Runbooks Worker hybrides Linux, consultez [Runbook Worker hybride Linux Azure Automation](automation-linux-hrw-install.md).