---
title: "Mon premier Runbook PowerShell dans Azure Automation | Microsoft Docs"
description: "Ce didacticiel vous familiarise avec la création, le test et la publication d’un Runbook Powershell simple."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: azure powershell, didacticiel sur le script powershell, automatisation powershell
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte;sngun
ms.openlocfilehash: 8db2cc1c8759fd08129f7794a8675d006998cc55
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-powershell-runbook"></a>Mon premier Runbook PowerShell

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ce didacticiel vous guide dans la création d’un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) dans Azure Automation. Vous commencez avec un simple runbook que vous testez et publiez tout en décourant comment suivre l’état de la tâche du runbook. Vous modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, vous le rendrez plus robuste en lui ajoutant des paramètres.

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Vous arrêtez et démarrez cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="step-1---create-new-runbook"></a>Étape 1 - Création d’un Runbook
Commençons par créer un runbook simple qui retourne le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.

   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).

1. Cliquez sur **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
2. Créez un runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
3. Nommez le Runbook *MyFirstRunbook-PowerShell*.
4. Dans ce cas précis, nous allons créer un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks). Par conséquent, sélectionnez **Powershell** comme **Type de runbook**.
5. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="step-2---add-code-to-the-runbook"></a>Étape 2 - Ajouter du code au Runbook
Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, vous tapez directement le code dans le runbook.

1. Votre runbook est actuellement vide. Saisissez *Write-Output « Hello World ».* dans le corps du script.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="step-3---test-the-runbook"></a>Étape 3 : Test du Runbook
Avant de publier le runbook pour le rendre disponible en production, vous voulez le tester pour vous assurer qu’il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.

   L’état initial du travail est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. Il passe à *En cours de démarrage* lorsqu’un Worker sélectionne la tâche, puis à *En cours d’exécution* lorsque le runbook se lance.  

1. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans votre cas, *Hello World* devrait apparaître.<br><br> ![Résultat du volet de test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 : Publication et démarrage du Runbook
Le runbook que vous avez créé est toujours en mode brouillon. Il faut le publier pour pouvoir l’exécuter en production.  Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans votre cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
2. Si vous faites maintenant défiler la page vers la gauche pour visualiser le runbook sur la page **Runbooks**, celle-ci affiche **l’État de création** **Publié**.
3. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-PowerShell**.  
   Les options de la partie supérieure nous permettent de démarrer le runbook, de l’afficher, de planifier son démarrage à un moment ultérieur ou de créer un [webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4. L’objectif étant de démarrer le runbook, cliquez sur **Démarrer**, puis sur **Ok** à l’ouverture de la page Démarrer le runbook.
5. Un volet s’ouvre pour la tâche du runbook qui vient d’être créée. Vous pouvez le fermer, mais, dans ce cas précis, laissez-le ouvert pour pouvoir suivre la progression de la tâche.
6. L’état de la tâche, indiqué dans le champ **Résumé de la tâche**, correspond aux états constatés lors du test du runbook.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Lorsque le Runbook prend l’état *Terminé*, sous **Vue d’ensemble**, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.<br><br> ![Sortie de travail](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Fermez la page Sortie.
9. Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Vous devez uniquement voir le message *Hello World* dans le flux de sortie, mais d’autres flux peuvent s’afficher pour un travail de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.<br><br> ![Tous les journaux](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Fermez les page du flux et de la tâche pour revenir à la page MyFirstRunbook-PowerShell.
11. Sous **Détails**,c liquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Vous devez voir un seul travail, car vous n’avez exécuté le travail qu’une seule fois.<br><br> ![Liste des postes](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que vous avez consulté au démarrage du runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Étape 5 : Ajout d’une authentification pour gérer les ressources Azure
Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Il ne peut le faire que si vous le configurez pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Vous utilisez pour cela la cmdlet **Add-AzureRmAccount**.

1. Ouvrez l’éditeur de texte en cliquant sur **Modifier** dans la page MyFirstRunbook-PowerShell.
2. La ligne **Write-Output** ne vous est plus utile. Vous pouvez donc la supprimer.
3. Tapez ou copiez-collez le code suivant qui gère l’authentification avec votre compte d’authentification Automation :
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Cliquez sur le volet de **Test** afin de tester le runbook.
5. Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée semblable à celle illustrée ci-dessous devrait afficher les informations de base sur votre compte. Cette sortie confirme la validité des informations d’identification.<br><br> ![Authentifier](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Étape 6 - Ajouter du code pour démarrer une machine virtuelle
À présent que votre runbook s’authentifie auprès de votre abonnement Azure, vous pouvez gérer les ressources. Vous ajoutez une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, nous allons coder ce nom en dur dans le runbook.

1. Après *Add-AzureRmAccount*, tapez *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Enregistrez le runbook, puis cliquez sur le volet de **Test** pour le tester.
3. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Étape 7 : Ajout d’un paramètre d'entrée au Runbook
Pour l’instant, votre runbook démarre la machine virtuelle que vous avez codée en dur dans le runbook, mais ce dernier serait plus utile si vous pouviez spécifier la machine virtuelle lorsque le runbook est démarré. Vous ajoutez des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ajoutez des paramètres au Runbook pour *VMName* et *ResourceGroupName* et utilisez ces variables avec l’applet de commande **Start-AzureRmVM** comme dans l’exemple ci-dessous.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée utilisées dans le test.
2. Fermez le volet de test.
3. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
4. Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
5. Cliquez sur **OK** pour démarrer le Runbook. Tapez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.<br><br> ![Paramètre de réussite](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## <a name="differences-from-powershell-workflow"></a>Différences par rapport à PowerShell Workflow
Les runbooks PowerShell ont les mêmes cycle de vie, fonctionnalités et mode de gestion que ceux de PowerShell Workflow, avec toutefois quelques différences et limitations :

1. Les Runbooks PowerShell s’exécutent rapidement par rapport aux Runbooks PowerShell Workflow, car ils n’ont pas à subir l’étape de compilation.
2. Les Runbooks PowerShell Workflow prennent en charge les points de contrôle. Avec les points de contrôle, les Runbooks PowerShell Workflow peuvent reprendre à n’importe quel point du Runbook, tandis que les Runbooks PowerShell ne peuvent recommencer qu’à partir du début.
3. Les Runbooks PowerShell Workflow prennent en charge l’exécution parallèle et série, alors que les Runbooks PowerShell peuvent exécuter des commandes en série uniquement.
4. Dans un runbook PowerShell Workflow, une activité, une commande ou un bloc de script peut avoir sa propre instance d’exécution, alors que dans un Runbook PowerShell, tous les éléments du script s’exécutent dans une instance d’exécution unique. Il existe également certaines [différences syntaxiques](https://technet.microsoft.com/magazine/dn151046.aspx) entre un Runbook PowerShell natif et un Runbook PowerShell Workflow.

## <a name="next-steps"></a>Étapes suivantes
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez [Prise en charge de script PowerShell natif dans Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

