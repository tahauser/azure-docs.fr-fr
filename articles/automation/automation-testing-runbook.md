---
title: Test d’un runbook dans Azure Automation
description: Avant de publier un runbook dans Azure Automation, vous pouvez le tester pour vous assurer qu’il fonctionne comme prévu.  Cet article décrit la procédure de test d’un runbook et de l’affichage de son résultat.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 531fffe1ed24016d47708a729a3ee7642a1db64a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="testing-a-runbook-in-azure-automation"></a>Test d’un runbook dans Azure Automation
Lorsque vous testez un runbook, la [version Brouillon](automation-creating-importing-runbook.md#publishing-a-runbook) est exécutée et toutes les actions qu’il effectue sont finalisées. Aucun historique des tâches n’est créé, mais les flux [Résultat](automation-runbook-output-and-messages.md#output-stream) et [Avertissement et Erreur](automation-runbook-output-and-messages.md#message-streams) s’affichent dans le panneau de sortie du test. Les messages dirigés vers le [flux de messages](automation-runbook-output-and-messages.md#message-streams) s’affiche dans le panneau de résultat uniquement si la variable [$VerbosePreference variable](automation-runbook-output-and-messages.md#preference-variables) a pour valeur Continue.

Bien que la version brouillon soit exécutée, le runbook exécute toujours le flux de travail normalement et effectue des actions sur des ressources dans l’environnement. Pour cette raison, vous devez tester les runbooks uniquement sur des ressources hors production.

La procédure de chaque [type de runbook](automation-runbook-types.md) est identique et il n’y a aucune différence de test entre l’éditeur de texte et l’éditeur graphique dans le portail Azure.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Pour tester le runbook dans le portail Azure
Vous pouvez utiliser n’importe quel [type de runbook](automation-runbook-types.md) dans le portail Azure.

1. Ouvrez la version Brouillon du runbook dans l’[éditeur de texte](automation-edit-textual-runbook.md) ou l’[éditeur graphique](automation-graphical-authoring-intro.md).
2. Cliquez sur le bouton **Tester** pour ouvrir le panneau de test.
3. Si le runbook possède des paramètres, ils figureront dans le volet gauche dans lequel vous pourrez fournir des valeurs à utiliser pour le test.
4. Si vous souhaitez exécuter le test sur [Runbook Worker hybride](automation-hybrid-runbook-worker.md), définissez les **Paramètres d’exécution** sur **Worker hybride** et sélectionnez le nom du groupe cible.  Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le cloud.
5. Cliquez sur le bouton **Démarrer** pour démarrer le test.
6. Si le runbook est [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou [Graphique](automation-runbook-types.md#graphical-runbooks), vous pouvez l’arrêter ou le suspendre pendant qu’il est en cours de test en vous aidant des boutons situés sous le panneau de résultat. Lorsque vous interrompez le runbook, il termine l’activité en cours avant de s’interrompre. Lorsque le runbook est suspendu, vous pouvez l’arrêter ou le redémarrer.
7. Inspectez la sortie du runbook dans le panneau de résultat.

## <a name="next-steps"></a>Étapes suivantes
* Pour savoir comment créer ou importer un Runbook, consultez [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md)
* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur la configuration de runbooks pour retourner des messages d’état et des erreurs, y compris sur les pratiques recommandées, consultez [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)

