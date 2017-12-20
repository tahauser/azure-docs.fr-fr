---
title: Mon premier Runbook PowerShell Workflow dans Azure Automation | Microsoft Docs
description: "Ce didacticiel présente les procédures de création, de test et de publication d’un runbook textuel simple à l’aide de PowerShell Workflow."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: powershell workflow, exemples de workflows powershell, workflows powershell
ms.assetid: 0002d7f7-e2b5-46e3-b5eb-4596b84fd526
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 214163dd0f63bb8e08afdf94dcbfcc93d0279ad1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="my-first-powershell-workflow-runbook"></a>Mon premier runbook PowerShell Workflow

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ce didacticiel décrit la création d’un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) dans Azure Automation. Nous commençons par un simple runbook que nous testons et publions tout en expliquant comment suivre l’état du travail du runbook. Nous modifions ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, nous renforçons le runbook en ajoutant des paramètres de runbook.

## <a name="prerequisites"></a>Composants requis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Nous arrêtons et démarrons cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="step-1---create-new-runbook"></a>Étape 1 - Création d’un Runbook
Nous allons commencer par créer un Runbook simple qui renvoie le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.  
   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.<br><br> ![Contrôle des Runbooks](media/automation-first-runbook-textual/runbooks-control-tiles.png)
3. Créez un Runbook en cliquant sur le bouton **Ajouter un Runbook**, puis **Créer un Runbook**.
4. Nommez le runbook *MyFirstRunbook-Workflow*.
5. Dans ce cas précis, nous allons créer un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Par conséquent, sélectionnez **Powershell Workflow** dans le champ **Type de Runbook**.<br><br> ![Nouveau Runbook](media/automation-first-runbook-textual/new-runbook-properties.png)
6. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="step-2---add-code-to-the-runbook"></a>Étape 2 - Ajouter du code au Runbook
Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, nous taperons directement le code dans le Runbook.

1. Pour l’instant, notre Runbook est vide, à l’exception du mot-clé requis *workflow* , du nom de notre Runbook et des accolades qui entoureront la totalité du workflow.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Tapez *Write-Output « Hello World. »* entre les accolades.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Enregistrez le Runbook en cliquant sur **Enregistrer**.<br><br> ![Enregistrer un Runbook](media/automation-first-runbook-textual/automation-runbook-edit-controls-save.png)

## <a name="step-3---test-the-runbook"></a>Étape 3 : Test du Runbook
Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.<br><br> ![Volet de test](media/automation-first-runbook-textual/automation-runbook-edit-controls-test.png)
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.  
   L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
4. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 : Publication et démarrage du Runbook
Le Runbook que nous venons de créer est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.<br><br> ![Publier](media/automation-first-runbook-textual/automation-runbook-edit-controls-publish.png)
2. Si vous faites défiler la page vers la gauche pour afficher le Runbook dans le volet **Runbooks**, **l’État de création** **Publié** est affiché.
3. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-Workflow**.  
   Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4. Comme nous voulons simplement démarrer le Runbook, cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité.<br><br> ![Démarrer un Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Un volet de tâche est ouvert pour la tâche du Runbook que nous venons de créer. Nous pouvons fermer ce volet mais, dans ce cas, nous le laisserons ouvert afin de suivre la progression de la tâche.
6. L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Fermez le volet Sortie.
9. Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de Runbook, notamment Mode détaillé et Erreur si le Runbook consigne ces informations.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook.
11. Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois.<br><br> ![Tâches](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Vous pouvez cliquer sur cette tâche pour ouvrir le volet de la tâche que nous avons consulté au démarrage du Runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Étape 5 : Ajout d’une authentification pour gérer les ressources Azure
Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Il ne peut le faire que si nous le configurons pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Nous utilisons pour cela l’applet de commande **Add-AzureRmAccount** .

1. Ouvrez l’éditeur textuel en cliquant sur **Modifier** dans le volet MyFirstRunbook-Workflow.<br><br> ![Modifier un Runbook](media/automation-first-runbook-textual/automation-runbook-controls-edit.png)
2. Nous n’avons plus besoin de la ligne **Write-Output**. Vous pouvez donc la supprimer.
3. Positionnez le curseur sur une ligne vide entre les accolades.
4. Tapez ou copiez-collez le code suivant qui gérera l’authentification avec votre compte d’authentification Automation :

   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Cliquez sur **Volet de test** afin de tester le Runbook.
6. Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée semblable à celle illustrée ci-dessous devrait afficher les informations de base sur votre compte. Cette sortie confirme la validité des informations d’identification.<br><br> ![Authentifier](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Étape 6 - Ajouter du code pour démarrer une machine virtuelle
À présent que notre Runbook s’authentifie auprès de notre abonnement Azure, nous pouvons gérer les ressources. Nous ajoutons une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, nous allons coder ce nom en dur dans le runbook.

1. Après *Add-AzureRmAccount*, tapez *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.  

   ```
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Enregistrez le runbook, puis cliquez sur **Volet de test** pour le tester.
3. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Étape 7 : Ajout d’un paramètre d'entrée au Runbook
Pour l’instant, notre Runbook démarre la machine virtuelle que nous avons codée en dur dans le Runbook, mais ce dernier serait plus utile si nous pouvions spécifier la machine virtuelle lorsque le Runbook est démarré. Nous allons à présent ajouter des paramètres d’entrée au Runbook pour fournir cette fonctionnalité.

1. Ajoutez des paramètres au Runbook pour *VMName* et *ResourceGroupName* et utilisez ces variables avec l’applet de commande **Start-AzureRmVM** comme dans l’exemple ci-dessous.

   ```
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Enregistrez le Runbook et ouvrez le volet de test. Notez que vous pouvez désormais fournir des valeurs pour les deux variables d'entrée qui seront utilisées dans le test.
3. Fermez le volet de test.
4. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
5. Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
6. Cliquez sur **Démarrer** pour démarrer le Runbook. Tapez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.<br><br> ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.  

## <a name="next-steps"></a>Étapes suivantes
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez le billet [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
