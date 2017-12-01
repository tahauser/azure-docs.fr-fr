---
title: "Créer une tâche d'observateur dans le compte Azure Automation | Microsoft Docs"
description: "Découvrez comment créer une tâche d'observateur dans le compte Azure Automation afin de surveiller les fichiers créés dans un dossier."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Les tâches d'observateur Azure Automation vous permettent de répondre aux événements qui se produisent dans votre centre de données local

Dans ce didacticiel, vous apprendrez à créer une tâche d'observateur pour :

> [!div class="checklist"]
> * Créer un runbook Watcher qui recherche les nouveaux fichiers dans un répertoire ;
> * Créer une variable Automation qui consigne la dernière fois où un fichier a été traité par l'observateur ;
> * Créer un runbook d'action appelé lorsque le runbook Watcher trouve un nouveau fichier ;
> * Créer une tâche d'observateur qui sélectionne le runbook Watcher et le runbook d'action ;
> * Déclencher un Watcher en ajoutant un nouveau fichier à un répertoire ;
> * Inspecter la sortie du runbook d'action qui affiche des informations sur le nouveau fichier.  

## <a name="prerequisites"></a>Composants requis

Pour compléter ce didacticiel, les éléments suivants sont requis.
+ Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d'actions ainsi que la tâche d'observateur.
+ Un [Runbook Worker hybride](automation-hybrid-runbook-worker.md) où la tâche d'observateur est exécutée.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Créer un runbook Watcher qui recherche les nouveaux fichiers
1.  Ouvrez le compte Automation puis cliquez sur la page Runbooks.
2.  Cliquez sur le bouton Parcourir la galerie.
![Liste des runbooks à partir de l'interface utilisateur](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Recherchez « Watch-NewFile » et importez le runbook dans le compte Automation.
![Publier un runbook à partir de l'interface utilisateur](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Cliquez sur le bouton Modifier pour afficher la source du Runbook, puis sur le bouton Publier.

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Créer une variable Automation qui consigne la dernière fois où un fichier a été traité par l'observateur
1.  Ouvrez la page des variables sous SHARED RESOURCES puis cliquez sur « Ajouter une variable » ![Répertorier les variables à partir de l'interface utilisateur](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Entrer le nom « Watch-NewFileTimestamp »
3.  Sélectionnez le type DateTime puis cliquez sur le bouton « Créer ».
![Créer une variable en filigrane à partir de l'interface utilisateur](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Créer un runbook d'action appelé lorsque le runbook Watcher trouve un nouveau fichier
1.  Cliquez sur le page Runbooks dans la catégorie PROCESS AUTOMATION.
2.  Cliquez sur le bouton Parcourir la galerie.
3.  Recherchez « Process-NewFile » et importez le runbook dans le compte Automation.
4.  Cliquez sur le bouton Modifier pour afficher la source du Runbook, puis sur le bouton Publier.
![Traiter une tâche d'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Créer une tâche d'observateur qui sélectionne le runbook Watcher et le runbook d'action
1.  Ouvrez la page des tâches d'observateur puis cliquez sur le bouton « Ajouter une tâche d'observateur ».
![Liste des observateurs à partir de l'interface utilisateur](media/automation-watchers-tutorial/WatchersList.png)
2.  Entrez le nom « WatchMyFolder ».
3.  Sélectionnez « Configurer l'observateur » puis choisissez le runbook « Watch-NewFile ».
![Configurer l'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Entrez les valeurs suivantes pour les paramètres :
    *   FOLDERPATH. Un dossier sur le worker hybride où les fichiers sont créés
    *   EXTENSION. Laisser vide pour traiter toutes les extensions de fichier.
    *   RECURSE. Conservez la valeur par défaut.
    *   RUN SETTINGS. Sélectionnez le worker hybride.
5.  Cliquez sur OK, puis sur Sélectionner pour revenir à la page de l'observateur.
6.  Sélectionnez « Configurer l'action » puis choisissez le runbook « Process-NewFile ».
![Configurer l'action de l'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Entrez les valeurs suivantes pour les paramètres :
    *   EVENTDATA. Laisser vide. Les données sont transmises à partir du runbook Watcher.
    *   Paramètres d'exécution. Conservez la valeur Azure car ce runbook s'exécute dans le service Automation.
8.  Cliquez sur OK, puis sur Sélectionner pour revenir à la page de l'observateur.
9.  Cliquez sur OK pour créer la tâche d'observateur.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Déclencher un Watcher en ajoutant un nouveau fichier à un répertoire
1.  Accès à distance au worker hybride
2.  Ajoutez un nouveau fichier texte au dossier surveillé par la tâche d'observateur.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Inspecter la sortie du runbook d'action qui affiche des informations sur le nouveau fichier
1.  Cliquez sur la tâche d'observateur pour « WatchMyFolder »
2.  Cliquez sur « Afficher les flux d'observateurs » pour voir que l'observateur a trouvé le nouveau fichier et lancé le runbook d'actions.
3.  Cliquez sur « Afficher les tâches d'action d'observateur » pour voir la tâche du runbook d'actions.
![Tâches d'action de l'observateur à partir de l'interface utilisateur](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Étapes suivantes :

Pour plus d'informations, voir [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).








