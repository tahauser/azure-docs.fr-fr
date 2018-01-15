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
ms.openlocfilehash: 0349b8aae880c39677e05d8ba8ddd6eadd13eea1
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="my-first-powershell-runbook"></a>Mon premier Runbook PowerShell

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Ce didacticiel vous guide dans la création d’un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) dans Azure Automation. Nous commençons par un simple runbook que nous testons et publions tout en expliquant comment suivre l’état du travail du runbook. Nous modifions ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, nous renforçons le runbook en ajoutant des paramètres de runbook.

## <a name="prerequisites"></a>Conditions préalables
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Nous arrêtons et démarrons cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="step-1---create-new-runbook"></a>Étape 1 - Création d’un Runbook
Nous allons commencer par créer un Runbook simple qui renvoie le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.  
   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.<br><br> ![RunbooksControl](media/automation-first-runbook-textual-powershell/runbooks-control-tiles.png)  
3. Créez un runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
4. Nommez le Runbook *MyFirstRunbook-PowerShell*.
5. Dans ce cas précis, nous allons créer un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks). Par conséquent, sélectionnez **Powershell** comme **Type de Runbook**.<br><br> ![Types de Runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="step-2---add-code-to-the-runbook"></a>Étape 2 - Ajouter du code au Runbook
Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, nous tapons directement le code dans le runbook.

1. Notre runbook est actuellement vide. Saisissez *Write-Output « Hello World ».* dans le corps du script.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Enregistrez le Runbook en cliquant sur **Enregistrer**.<br><br> ![Bouton Enregistrer](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-save.png)  

## <a name="step-3---test-the-runbook"></a>Étape 3 : Test du Runbook
Avant publier le Runbook pour le rendre disponible en production, nous voulons le tester pour s’assurer qu'il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.<br><br> ![Test Pane](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-test.png)  
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.  
   L’état initial du travail est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
4. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans notre cas, nous devrions voir *Hello World*.<br><br> ![Résultat du volet de test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 : Publication et démarrage du Runbook
Le runbook que nous avons créé est toujours en mode brouillon. Nous devons le publier pour pouvoir l'exécuter en production.  Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon.  Dans notre cas, nous n'avons pas encore de version publiée car nous venons de créer le Runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** lorsque vous y êtes invité.<br><br> ![Bouton Publier](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-publish.png)  
2. Si vous faites défiler la page vers la gauche pour visualiser le volet **Runbooks**, celui-ci affichera **l’État de création** **Publié**.
3. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-PowerShell**.  
   Les options de la partie supérieure nous permettent de démarrer le runbook, de l’afficher, de planifier son démarrage à un moment ultérieur ou de créer un [webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
4. Notre objectif étant de démarrer le runbook, cliquons sur **Démarrer**, puis sur **OK** à l’ouverture du panneau Démarrer le Runbook.<br><br> ![Bouton Démarrer](media/automation-first-runbook-textual-powershell/automation-runbook-controls-start.png)<br>    
5. Un volet de travail est ouvert pour le travail du runbook que nous avons créé. Nous pouvons fermer ce volet mais, dans ce cas, nous le laissons ouvert afin de suivre la progression du travail.
6. L’état de la tâche est indiqué dans le champ **Résumé de la tâche** et correspond aux états que nous avons constatés lors du test du Runbook.<br><br> ![Résumé des tâches](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.<br><br> ![Sortie de travail](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Fermez le volet Sortie.
9. Cliquez sur **Tous les journaux** pour ouvrir le volet Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de Runbook, notamment Mode détaillé et Erreur si le Runbook consigne ces informations.<br><br> ![Tous les journaux](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Fermez les volets du flux et de la tâche pour revenir au volet MyFirstRunbook PowerShell.
11. Cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Il répertorie toutes les tâches créées par ce Runbook. Une seule tâche doit apparaître, car nous n’avons exécuté la tâche qu’une seule fois.<br><br> ![Liste des postes](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que nous avons consulté au démarrage du runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Étape 5 : Ajout d’une authentification pour gérer les ressources Azure
Nous avons testé et publié notre Runbook, mais jusqu'à présent, il ne fait rien d'utile. Nous voulons qu’il gère les ressources Azure. Il ne peut le faire que si nous le configurons pour qu’il s’authentifie à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Nous utilisons pour cela l’applet de commande **Add-AzureRmAccount** .

1. Ouvrez l’éditeur de texte en cliquant sur **Modifier** dans le volet MyFirstRunbook PowerShell.<br><br> ![Modifier un Runbook](media/automation-first-runbook-textual-powershell/automation-runbook-controls-edit.png)<br>   
2. La ligne **Write-Output** ne nous est plus utile. Vous pouvez donc la supprimer.
3. Tapez ou copiez-collez le code suivant qui gère l’authentification avec votre compte d’authentification Automation :
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Cliquez sur **Volet de test** afin de tester le Runbook.
5. Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée semblable à celle illustrée ci-dessous devrait afficher les informations de base sur votre compte. Cette sortie confirme la validité des informations d’identification.<br><br> ![Authentifier](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Étape 6 - Ajouter du code pour démarrer une machine virtuelle
À présent que notre Runbook s’authentifie auprès de notre abonnement Azure, nous pouvons gérer les ressources. Nous ajoutons une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, nous allons coder ce nom en dur dans le runbook.

1. Après *Add-AzureRmAccount*, tapez *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Enregistrez le runbook, puis cliquez sur **Volet de test** pour le tester.
3. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Étape 7 : Ajout d’un paramètre d'entrée au Runbook
Pour l’instant, notre runbook démarre la machine virtuelle que nous avons codée en dur dans le runbook, mais ce dernier serait plus utile si nous spécifiions la machine virtuelle lorsque le runbook est démarré. Nous allons à présent ajouter des paramètres d’entrée au Runbook pour fournir cette fonctionnalité.

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
2. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée utilisées dans le test.
3. Fermez le volet de test.
4. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
5. Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
6. Cliquez sur **Démarrer** pour démarrer le Runbook. Entrez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.<br><br> ![Paramètre de réussite](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
7. Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## <a name="differences-from-powershell-workflow"></a>Différences par rapport à PowerShell Workflow
Les runbooks PowerShell ont les mêmes cycle de vie, fonctionnalités et mode de gestion que ceux de PowerShell Workflow, avec toutefois quelques différences et limitations :

1. Les Runbooks PowerShell s’exécutent rapidement par rapport aux Runbooks PowerShell Workflow, car ils n’ont pas à subir l’étape de compilation.
2. Les Runbooks PowerShell Workflow prennent en charge les points de contrôle. Avec les points de contrôle, les Runbooks PowerShell Workflow peuvent reprendre à n’importe quel point du Runbook, tandis que les Runbooks PowerShell ne peuvent recommencer qu’à partir du début.
3. Les Runbooks PowerShell Workflow prennent en charge l’exécution parallèle et série, alors que les Runbooks PowerShell peuvent exécuter des commandes en série uniquement.
4. Dans un runbook PowerShell Workflow, une activité, une commande ou un bloc de script peut avoir sa propre instance d’exécution, alors que dans un Runbook PowerShell, tous les éléments du script s’exécutent dans une instance d’exécution unique. Il existe également certaines [différences syntaxiques](https://technet.microsoft.com/magazine/dn151046.aspx) entre un Runbook PowerShell natif et un Runbook PowerShell Workflow.

## <a name="next-steps"></a>étapes suivantes
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez [Prise en charge de script PowerShell natif dans Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

