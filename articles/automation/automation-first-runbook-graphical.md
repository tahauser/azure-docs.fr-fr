---
title: Mon premier runbook graphique dans Azure Automation
description: Ce didacticiel vous familiarise avec la création, le test et la publication d'un Runbook graphique simple.
keywords: runbook, modèle de runbook, automatisation des runbooks, runbook azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 85902975d6871eccc69ffd441aec509f9f63ec2f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="my-first-graphical-runbook"></a>Mon premier Runbook graphique

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Ce didacticiel vous familiarise avec la création d’un [Runbook graphique](automation-runbook-types.md#graphical-runbooks) dans Azure Automation. Vous commencez avec un simple runbook qui teste et publie tout en expliquant comment suivre l’état du travail du runbook. Vous modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, vous terminez ce didacticiel en renforçant le runbook grâce à l’ajout des paramètres de runbook et des liens conditionnels.

## <a name="prerequisites"></a>Prérequis


Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Vous arrêtez et démarrez cette machine afin qu’elle ne soit pas une machine virtuelle de production.

## <a name="create-runbook"></a>Créer un runbook

Commencez par créer un runbook simple qui renvoie le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.

   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments multimédias. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).

2. Sélectionnez **Runbooks** sous **GESTION DE PROCESSUS** pour ouvrir la liste des runbooks.
3. Créez un runbook en sélectionnant l’option **+ Ajouter un Runbook**, puis en cliquant sur **Créer un Runbook**.
4. Nommez le Runbook *MyFirstRunbook-Graphical*.
5. Dans ce cas précis, vous allez créer un [runbook graphique](automation-graphical-authoring-intro.md) ; vous devez donc sélectionner **Graphique** dans le champ **Type de runbook**.<br> ![Nouveau Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur graphique.

## <a name="add-activities"></a>Ajouter des activités

Le contrôle Bibliothèque à gauche de l’éditeur vous permet de sélectionner des activités à ajouter à votre Runbook. Nous allons ajouter une cmdlet **Write-Output** pour extraire du texte du runbook.

1. Dans le contrôle Bibliothèque, cliquez dans la zone de recherche et tapez **Write-Output**. Les résultats de la recherche sont affichés dans l’image suivante : <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Faites défiler jusqu'au bas de la liste. Vous pouvez cliquer avec le bouton droit sur **Write-Output** et sélectionner **Ajouter au canevas** ou cliquer sur les points de suspension en regard de la cmdlet, puis sélectionner **Ajouter au canevas**.
1. Cliquez sur l’activité **Write-Output** sur le canevas. La page du contrôle Configuration qui vous permet de configurer l’activité s’ouvre.
1. Par défaut, le champ **Étiquette** affiche le nom de la cmdlet, mais vous pouvez le modifier pour le rendre plus convivial. Remplacez-le par *Write Hello World to output*.
1. Cliquez sur **Paramètres** pour renseigner les paramètres de l’applet de commande.

   Certaines applets de commande comportent plusieurs jeux de paramètres, et vous devez choisir lequel utiliser. Dans ce cas précis, **Write-Output** ne comporte qu’un seul paramètre défini ; vous n’avez donc pas besoin d’en sélectionner un.

1. Sélectionnez le paramètre **InputObject** . Il s’agit du paramètre où vous spécifiez le texte à envoyer au flux de sortie.
1. Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**. Le menu déroulant **Source de données** fournit différentes sources que vous utilisez pour renseigner un paramètre.

   Vous pouvez utiliser la sortie de ces sources, par exemple une autre activité, un élément multimédia Automation ou une expression PowerShell. Dans ce cas, la sortie est simplement *Hello World*. Vous pouvez utiliser une expression PowerShell et spécifier une chaîne.<br>

1. Dans le champ **Expression**, tapez *« Hello World »*, puis cliquez deux fois sur **OK** pour revenir au canevas.
1. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="test-the-runbook"></a>Tester le Runbook

Avant de publier le runbook pour le rendre disponible en production, vous voulez le tester pour vous assurer qu’il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Sélectionnez l’option **Volet de test** pour ouvrir la page Test.
1. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
1. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît dans le volet.

   L’état initial du travail est *Mis en file d’attente* pour indiquer que la tâche attend qu’un Runbook Worker du cloud devienne disponible. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.

1. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans ce cas, vous voyez *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Fermez la page Test pour revenir au canevas.

## <a name="publish-and-start-the-runbook"></a>Publier et démarrer le Runbook

Le runbook que vous avez créé est toujours en mode brouillon. Il doit être publié avant de pouvoir l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans ce cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Sélectionnez l’option **Publier** pour publier le runbook, puis cliquez sur **Oui** quand vous y êtes invité.
1. Si vous faites défiler la page vers la gauche pour visualiser la page **Runbooks**, celle-ci affiche l’**État de création** **Publié**.
1. Faites défiler la page vers la droite pour visualiser la page **MyFirstRunbook**.

   Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.

1. Cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité pour démarrer le runbook.
1. Une page de travail est ouverte pour le travail du runbook qui a été créé. Vérifiez que **État du travail** est défini sur **Terminé**.
1. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. La page **Sortie** est ouverte et vous pouvez voir le message *Hello World* affiché dans le volet.
1. Fermez la page Sortie.
1. Cliquez sur **Tous les journaux** pour ouvrir la page Flux de la tâche du runbook. Vous devez uniquement voir le message *Hello World* dans le flux de sortie, mais d’autres flux peuvent s’afficher pour un travail de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.
1. Fermez la page Tous les journaux et la page Tâche pour revenir à la page MyFirstRunbook.
1. Pour afficher tous les travaux du runbook, fermez la page **Travail** et sélectionnez **Travaux** sous **RESSOURCES**. Il répertorie toutes les tâches créées par ce Runbook. Vous devez voir un seul travail, car vous n’avez exécuté le travail qu’une seule fois.
1. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que vous avez consulté au démarrage du runbook. Cela vous permet de revenir en arrière et d’afficher les détails de toute tâche créée pour un Runbook donné.

## <a name="create-variable-assets"></a>Créer des ressources de variables

Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Avant de configurer le runbook pour l’authentification, vous créez une variable qui contiendra l’ID d’abonnement. Vous le référencerez après avoir configuré l’authentification à l’étape 6 ci-dessous. L’ajout d’une référence au contexte de l’abonnement vous permet de gérer facilement plusieurs abonnements. Avant de poursuivre, copiez votre ID d’abonnement depuis l’option Abonnements du panneau de navigation.

1. Dans la page Comptes Automation, sélectionnez **Variables** sous **RESSOURCES PARTAGÉES**.
1. Sélectionnez **Ajouter une variable**.
1. Dans la zone **Nom** de la page Nouvelle variable, entrez **AzureSubscriptionId**, puis dans la zone **Valeur**, entrez votre ID d’abonnement. Conservez la sélection *chaîne* pour **Type** et la valeur par défaut pour le **chiffrement**.
1. Cliquez sur **Créer** pour créer la variable.

## <a name="add-authentication"></a>Ajouter une authentification

Maintenant que vous disposez d’une variable pour contenir l’ID d’abonnement, vous pouvez configurer le runbook pour l’authentification à l’aide des informations d’identification mentionnées dans les [conditions préalables](#prerequisites). Pour ce faire, ajoutez au canevas la **Ressource** de connexion Azure et la cmdlet **Add-AzureRMAccount**.

1. Revenez à votre runbook, puis sélectionnez **Modifier** dans la page MyFirstRunbook.
1. Étant donné que **Write Hello World to output** ne vous est plus utile, cliquez sur les points de suspension (...) et sélectionnez **Supprimer**.
1. Dans la bibliothèque, développez **RESSOURCES**, **Connexions** et ajoutez **AzureRunAsConnection** au canevas en sélectionnant **Ajouter au canevas**.
1. Dans la commande Bibliothèque, tapez **Add-AzureRmAccount** dans la zone de recherche.
1. Ajoutez **Ajoutez-AzureRmAccount** au canevas.
1. Pointez sur **Get Run As Connection** (Obtenir une connexion d’identification) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Add-AzureRmAccount**. La flèche que vous avez créée est un *lien*. Le runbook démarre avec **Get Run As Connection**. Exécutez **Add-AzureRmAccount**.<br> ![Créer un lien entre des activités](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Dans le canevas, sélectionnez **Add-AzureRmAccount** et le type de panneau de contrôle Configuration **Login to Azure** (Connexion à Azure) dans la zone de texte **Étiquette**.
1. Cliquez sur **Paramètres** pour afficher la page Configuration des paramètres d’activité.
1. **Add-AzureRmAccount** comporte plusieurs ensembles de paramètres et vous devez donc en sélectionner un pour fournir les valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **ServicePrincipalCertificate**.
1. Une fois que vous sélectionnez le jeu de paramètres, les paramètres apparaissent dans la page Configuration des paramètres d’activité. Cliquez sur **APPLICATIONID**.<br> ![Ajouter des paramètres de compte Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
1. Dans la page Valeur du paramètre, sélectionnez **Sortie d’activité** pour **Source de données** et sélectionnez **Get Run As Connection** (Obtenir une connexion d’identification) dans la liste. Puis, dans la zone de texte **Chemin du champ**, saisissez **ApplicationId** et cliquez sur **OK**. Vous spécifiez le nom de la propriété du chemin du champ car l’activité génère un objet contenant plusieurs propriétés.
1. Cliquez sur **CERTIFICATETHUMBPRINT** puis, dans la page Valeur du paramètre, sélectionnez **Sortie de l’activité** comme **Source de données**. Sélectionnez **Get Run As Connection** (Obtenir une connexion d’identification) dans la liste, puis, dans la zone de texte **Chemin du champ**, entrez **CertificateThumbprint** et cliquez sur **OK**.
1. Cliquez sur **SERVICEPRINCIPAL** puis, dans la page Valeur du paramètre, sélectionnez **ConstantValue** comme **source de données**, cliquez sur l’option **True**, puis cliquez sur **OK**.
1. Cliquez sur **TENANTID** puis, dans la page Valeur du paramètre, sélectionnez **Sortie de l’activité** comme **Source de données**. Sélectionnez **Get Run As Connection** (Obtenir une connexion d’identification) dans la liste, puis, dans la zone de texte **Chemin du champ**, entrez **TenantId** et cliquez deux fois sur **OK**.
1. Dans la commande Bibliothèque, tapez **Set-AzureRmContext** dans la zone de recherche.
1. Ajoutez **Set-AzureRmContext** au canevas.
1. Dans le canevas, sélectionnez **Set-AzureRmContext**, puis dans le panneau de contrôle Configuration, tapez **Specify Subscription Id** (Spécifier un ID d’abonnement) dans la zone de texte **Étiquette**.
1. Cliquez sur **Paramètres** pour afficher la page Configuration des paramètres d’activité.
1. **Set-AzureRmContext** comporte plusieurs ensembles de paramètres et vous devez donc en sélectionner un pour fournir les valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **SubscriptionId**.
1. Une fois que vous sélectionnez le jeu de paramètres, les paramètres apparaissent dans la page Configuration des paramètres d’activité. Cliquez sur **SubscriptionID**
1. Dans la page Valeur du paramètre, sélectionnez **Ressource de variable** comme **Source de données** et sélectionnez **AzureSubscriptionId** dans la liste, puis cliquez deux fois sur **OK**.
1. Pointez sur **Login to Azure** (Connexion à Azure) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Specify Subscription Id** (Spécifier un ID d’abonnement).

À ce stade, votre Runbook doit ressembler à ce qui suit :  <br>![Configuration de l’authentification de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Ajouter une activité pour démarrer une machine virtuelle

Vous ajoutez maintenant une activité **Start-AzureRmVM** pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, coder ce nom dans l’applet de commande.

1. Dans la commande Bibliothèque, tapez **Start-AzureRm** dans la zone de recherche.
2. Ajoutez **Start-AzureRmVM** au canevas, puis faites-le glisser sous **Specify Subscription Id** (Spécifier un ID d’abonnement).
1. Pointez sur **Specify Subscription Id** (Spécifier un ID d’abonnement) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Start-AzureRmVM**.
1. Sélectionnez **Start-AzureRmVM**. Cliquez sur **Paramètres**, puis sur **Jeu de paramètres** afin d’afficher les jeux pour **Start-AzureRmVM**. Sélectionnez le jeu de paramètres **ResourceGroupNameParameterSetName** . Des points d’exclamation apparaissent en regard des paramètres **ResourceGroupName** et **Name**. Ils indiquent que ces paramètres sont obligatoires. Notez également que les deux attendent des valeurs de chaîne.
1. Sélectionnez **Name**. Sélectionnez **Expression PowerShell** pour **Source de données** et tapez entre guillemets doubles le nom de la machine virtuelle que vous démarrez avec ce runbook. Cliquez sur **OK**.
1. Sélectionnez **ResourceGroupName**. Utilisez **Expression PowerShell** pour **Source de données** et tapez le nom du groupe de ressources entre guillemets doubles. Cliquez sur **OK**.
1. Cliquez sur le volet de Test afin de tester le runbook.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

À ce stade, votre Runbook doit ressembler à ce qui suit :  <br>![Configuration de l’authentification de runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Ajouter des paramètres d’entrée supplémentaires

Notre runbook démarre actuellement la machine virtuelle dans le groupe de ressources que vous avez spécifié dans la cmdlet **Start-AzureRmVM**. Le runbook serait plus utile si nous pouvions spécifier ces deux éléments au démarrage du runbook. Vous ajoutez à présent des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ouvrez l’éditeur graphique en cliquant sur **Modifier** dans le volet **MyFirstRunbook**.
1. Cliquez sur **Entrée et sortie**, puis sur **Ajouter une entrée** pour ouvrir le volet Paramètre d’entrée de Runbook.
1. Spécifiez *VMName* dans le champ **Nom**. Conservez la sélection *chaîne* pour le champ **Type**, mais définissez la valeur **Obligatoire** sur *Oui*. Cliquez sur **OK**.
1. Créez un second paramètre d’entrée obligatoire appelé *ResourceGroupName*, puis cliquez sur **OK** pour fermer le panneau **Entrée et sortie**.<br> ![Paramètres d'entrée de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Sélectionnez l’activité **Start-AzureRmVM**, puis cliquez sur **Paramètres**.
1. Changez la **Source de données** de **Name** en **Entrée de Runbook**, puis sélectionnez **VMName**.
1. Changez la **Source de données** de **ResourceGroupName** en **Entrée de Runbook**, puis sélectionnez **ResourceGroupName**.<br> ![Paramètres Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée que vous utilisez dans le test.
1. Fermez le volet de test.
1. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
1. Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
1. Cliquez sur **Démarrer** pour démarrer le Runbook. Tapez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.
1. Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## <a name="create-a-conditional-link"></a>Créer un lien conditionnel

Vous modifiez maintenant le runbook afin qu’il tente de démarrer la machine virtuelle uniquement si elle n’est pas déjà démarrée. Pour cela, vous ajoutez au Runbook une applet de commande **Get-AzureRmVM** qui obtient l’état du niveau d’instance de la machine virtuelle. Ensuite, vous ajoutez un module de code Workflow PowerShell appelé **Obtenir l’état** à un extrait de code PowerShell pour déterminer si l’état de la machine virtuelle est en cours d’exécution ou arrêté. Un lien conditionnel à partir du module **Obtenir l’état** exécute **Start-AzureRmVM** seulement si l’état d’exécution en cours est arrêté. Enfin, vous générez un message vous informant si la machine virtuelle a été démarrée avec succès ou non à l’aide de la cmdlet PowerShell Write-Output.

1. Ouvrez **MyFirstRunbook** dans l’éditeur graphique.
1. Supprimez le lien entre **Specify Subscription Id** (Spécifier un ID d’abonnement) et **Start-AzureRmVM** en cliquant dessus, puis en appuyant sur la touche *Supprimer*.
1. Dans la commande Bibliothèque, tapez **Get-AzureRm** dans la zone de recherche.
1. Ajoutez **Get-AzureRmVM** au canevas.
1. Sélectionnez **Get-AzureRmVM** puis **Jeu de paramètres** afin d’afficher les jeux pour **Get-AzureRmVM**. Sélectionnez le jeu de paramètres **GetVirtualMachineInResourceGroupNameParamSet** . Des points d’exclamation apparaissent en regard des paramètres **ResourceGroupName** et **Name**. Ils indiquent que ces paramètres sont obligatoires. Notez également que les deux attendent des valeurs de chaîne.
1. Pour le paramètre **Name** sous **Source de données**, sélectionnez **Entrée du Runbook** puis **VMName**. Cliquez sur **OK**.
1. Pour le paramètre **ResourceGroupName** sous **Source de données**, sélectionnez **Entrée de Runbook**, puis **ResourceGroupName**. Cliquez sur **OK**.
1. Pour le paramètre **Status** sous **Source de données**, sélectionnez **Valeur constante**, puis cliquez sur **True**. Cliquez sur **OK**.
1. Créez un lien entre **Specify Subscription Id** (Spécifier un ID d’abonnement) et **Get-AzureRmVM**.
1. Dans la commande Bibliothèque, développez **Contrôle de Runbook** et ajoutez **Code** au canevas.  
1. Créez un lien entre **Get-AzureRmVM** et **Code**.  
1. Cliquez sur **Code** et dans le panneau Configuration, changez l’étiquette en **Obtenir l’état**.
1. Sélectionnez le paramètre **Code** pour afficher la page **Éditeur de code**.  
1. Dans l’éditeur de code, collez l’extrait de code suivant :

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Créez un lien entre **Obtenir l’état** et **Start-AzureRmVM**.<br> ![Runbook avec module de code](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Sélectionnez le lien puis dans le panneau Configuration, changez **Appliquer la condition** en **Oui**. Notez que le lien se transforme en une ligne pointillée indiquant que l’activité cible s’exécute uniquement si la condition produit la valeur true.  
1. Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Get Status’] -eq "Stopped"*. **Start-AzureRmVM** s’exécute uniquement si la machine virtuelle est arrêtée.
1. Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Microsoft.PowerShell.Utility**.
1. Ajoutez **Write-Output** au canevas à deux reprises.
1. Sur le premier contrôle **Write-Output**, cliquez sur **Paramètres** et changez la valeur de **Étiquette** en *Notify VM Started*.
1. Pour **InputObject**, définissez le champ **Source de données** sur **Expression PowerShell**, puis tapez l’expression *"$VMName démarrée."*.
1. Sur le second contrôle **Write-Output**, cliquez sur **Paramètres** et changez la valeur de **Étiquette** en *Notify VM Start Failed*
1. Pour **InputObject**, changez **Source de données** en **Expression PowerShell** puis tapez l’expression *"$VMName n’a pas pu démarrer."*.
1. Créez un lien entre **Start-AzureRmVM** et **Notify VM Started** ainsi que **Notify VM Start Failed**.
1. Sélectionnez le lien vers **Notify VM Started** et changez **Appliquer la condition** en **True**.
1. Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Start-AzureRmVM’].IsSuccessStatusCode -eq $true*. Ce contrôle Write-Output s’exécute désormais uniquement si la machine virtuelle a démarré correctement.
1. Sélectionnez le lien vers **Notify VM Start Failed** et changez **Appliquer la condition** en **True**.
1. Dans la zone **Expression de condition**, tapez *$ActivityOutput[’Start-AzureRmVM’].IsSuccessStatusCode -ne $true*. Ce contrôle Write-Output s’exécute désormais uniquement si la machine virtuelle n’a pas démarré correctement. À ce stade, votre runbook doit ressembler à l’image suivante : <br> ![Runbook avec Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Enregistrez le Runbook et ouvrez le volet de test.
1. Démarrez le Runbook avec la machine virtuelle arrêtée, et il devrait démarrer.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md)

