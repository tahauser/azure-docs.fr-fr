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
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 62cb96075deed6c252ae44c8a000e820cb691a90
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Mon premier runbook PowerShell Workflow

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ce didacticiel décrit la création d’un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) dans Azure Automation. Vous commencez avec un simple runbook que vous testez et publiez tout en expliquant comment suivre l’état du travail du runbook. Vous modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, vous le rendrez plus robuste en lui ajoutant des paramètres.

## <a name="prerequisites"></a>configuration requise
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Vous arrêtez et démarrez cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="step-1---create-new-runbook"></a>Étape 1 - Création d’un Runbook
Commençons par créer un runbook simple qui retourne le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.

   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).

1. Cliquez sur **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
2. Créez un runbook en cliquant sur le bouton **+ Ajouter un Runbook**, puis sur **Créer un Runbook**.
3. Nommez le runbook *MyFirstRunbook-Workflow*.
4. Dans ce cas précis, vous allez créer un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Par conséquent, sélectionnez **Powershell Workflow** dans le champ **Type de Runbook**.
5. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="step-2---add-code-to-the-runbook"></a>Étape 2 - Ajouter du code au Runbook
Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, vous tapez directement le code dans le runbook.

1. Pour l’instant, votre runbook est vide, à l’exception du mot-clé requis *workflow*, du nom de votre runbook et des accolades qui entourent la totalité du workflow.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Tapez *Write-Output « Hello World. »* entre les accolades.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="step-3---test-the-runbook"></a>Étape 3 : Test du Runbook
Avant de publier le runbook pour le rendre disponible en production, vous voulez le tester pour vous assurer qu’il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.

   L’état initial de la tâche est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. Il passe à *En cours de démarrage* lorsqu’un Worker sélectionne la tâche, puis à *En cours d’exécution* lorsque le runbook se lance.  

1. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans votre cas, *Hello World* devrait apparaître.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 : Publication et démarrage du Runbook
Le runbook que vous avez créé est toujours en mode brouillon. Il faut le publier pour pouvoir l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans votre cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
2. Si vous faites maintenant défiler la page vers la gauche pour visualiser le runbook sur la page **Runbooks**, celle-ci affiche **l’État de création** **Publié**.
3. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-Workflow**.  
   Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4. Comme vous voulez simplement démarrer le runbook, cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité.<br><br> ![Démarrer un Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Un volet s’ouvre pour la tâche du runbook qui vient d’être créée. Vous pouvez le fermer, mais, dans ce cas précis, laissez-le ouvert pour pouvoir suivre la progression du travail.
6. L’état de la tâche, indiqué dans le champ **Résumé de la tâche**, correspond aux états constatés lors du test du runbook.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Fermez le volet Sortie.
9. Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Vous devez uniquement voir le message *Hello World* dans le flux de sortie, mais d’autres flux peuvent s’afficher pour un travail de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook.
11. Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Vous devez voir un seul travail, car vous n’avez exécuté le travail qu’une seule fois.<br><br> ![Tâches](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que vous avez consulté au démarrage du runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Étape 5 : Ajout d’une authentification pour gérer les ressources Azure
Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Il ne peut le faire que si vous le configurez pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Vous utilisez pour cela la cmdlet **Add-AzureRMAccount**.

1. Ouvrez l’éditeur textuel en cliquant sur **Modifier** dans le volet MyFirstRunbook-Workflow.
2. La ligne **Write-Output** ne vous est plus utile. Vous pouvez donc la supprimer.
3. Positionnez le curseur sur une ligne vide entre les accolades.
4. Tapez ou copiez-collez le code suivant qui gérera l’authentification avec votre compte d’authentification Automation :

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Cliquez sur le volet de **Test** afin de tester le runbook.
6. Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée semblable à celle illustrée ci-dessous devrait afficher les informations de base sur votre compte. Cette sortie confirme la validité des informations d’identification.<br><br> ![Authentifier](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Étape 6 - Ajouter du code pour démarrer une machine virtuelle
À présent que votre runbook s’authentifie auprès de votre abonnement Azure, vous pouvez gérer les ressources. Vous ajoutez une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, vous allez coder ce nom en dur dans le runbook.

1. Après *Add-AzureRmAccount*, tapez *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Enregistrez le runbook, puis cliquez sur le volet de **Test** pour le tester.
3. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Étape 7 : Ajout d’un paramètre d'entrée au Runbook
Pour l’instant, votre runbook démarre la machine virtuelle que vous avez codée en dur dans le runbook, mais ce dernier serait plus utile si vous pouviez spécifier la machine virtuelle lorsque le runbook est démarré. Vous ajoutez des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ajoutez des paramètres au Runbook pour *VMName* et *ResourceGroupName* et utilisez ces variables avec l’applet de commande **Start-AzureRmVM** comme dans l’exemple ci-dessous.

   ```powershell-interactive
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
2. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée se trouvant dans le test.
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
