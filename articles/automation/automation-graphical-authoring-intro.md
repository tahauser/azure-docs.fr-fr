---
title: Création de graphiques dans Azure Automation
description: La création de graphiques vous permet de créer des Runbooks pour Azure Automation sans utiliser de code. Cet article propose une introduction à la création de graphiques et tous les détails nécessaires pour commencer à créer Runbook graphique.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c67be9fc2fbeefd47b70f48885d641eebfbf2521
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="graphical-authoring-in-azure-automation"></a>Création de graphiques dans Azure Automation
## <a name="introduction"></a>Introduction
La création de graphiques vous permet de créer des Runbooks pour Azure Automation sans les complexités du code Windows PowerShell ou du code de workflow PowerShell sous-jacent. Vous pouvez ajouter des activités au canevas à partir d’une bibliothèque d’applets de commande et de Runbooks, puis les lier et les configurer pour former un workflow. Si vous avez déjà travaillé avec System Center Orchestrator ou Service Management Automation (SMA), cette procédure vous semblera certainement familière

Cet article propose une introduction à la création de graphiques et aux concepts dont vous avez besoin pour prendre en main la création d'un Runbook graphique.

## <a name="graphical-runbooks"></a>Runbooks graphiques
Dans Azure Automation, tous les Runbooks sont des workflows Windows PowerShell. Les Runbooks graphiques et les Runbooks de workflow PowerShell graphique génèrent du code PowerShell qui est exécuté par les Workers Automation, mais vous ne pouvez pas l’afficher ou le modifier directement. Un Runbook graphique peut être converti en Runbook de workflow PowerShell graphique et inversement, mais les deux ne peuvent pas être convertis en Runbooks textuels. Un Runbook textuel existant ne peut pas être importé dans l’éditeur graphique.

## <a name="overview-of-graphical-editor"></a>Vue d'ensemble de l'éditeur graphique
Vous pouvez ouvrir l’éditeur graphique dans le portail Azure en créant ou en modifiant un Runbook graphique.

![Espace de travail graphique](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Les sections suivantes décrivent les contrôles de l'éditeur graphique.

### <a name="canvas"></a>Canevas
Le canevas est l'emplacement où vous concevez votre Runbook. Vous pouvez ajouter au Runbook des activités des nœuds se trouvant dans le contrôle Bibliothèque et les connecter à l'aide de liens pour définir la logique du Runbook.

Vous pouvez utiliser les commandes en bas de la zone de dessin pour effectuer un zoom avant ou arrière.

### <a name="library-control"></a>Contrôle Bibliothèque
Le contrôle Bibliothèque est l'emplacement où vous sélectionnez les [activités](#activities) à ajouter à votre Runbook. Vous les ajoutez au canevas où vous les connectez à d'autres activités. Il comprend quatre sections décrites dans le tableau suivant :

| Section | Description |
|:--- |:--- |
| Applets de commande |Inclut toutes les applets de commande qui peuvent être utilisées dans votre Runbook. Les applets de commande sont organisées par module. Tous les modules que vous avez installés dans votre compte Automation sont disponibles. |
| Runbooks |Inclut les Runbooks de votre compte Automation. Ces Runbooks peuvent être ajoutés au canevas pour être utilisés en tant que Runbooks enfants. Seuls les Runbooks du même type de base que le Runbook en cours de modification sont affichés. Dans le cas des Runbooks graphiques, seuls les Runbooks basés sur PowerShell sont indiqués ; pour les Runbooks de workflow PowerShell graphique, seuls les Runbooks basés sur un workflow PowerShell s’affichent. |
| Éléments multimédias |Inclut les [ressources Automation](http://msdn.microsoft.com/library/dn939988.aspx) de votre compte Automation qui peuvent être utilisées dans votre Runbook. Lorsque vous ajoutez une ressource à un Runbook, cela ajoute une activité de workflow qui obtient la ressource sélectionnée. Pour les ressources de type Variable, vous pouvez choisir d'ajouter une activité pour obtenir la variable ou de définir la variable. |
| Contrôle de Runbook |Inclut des activités de contrôle de Runbook qui peuvent être utilisées dans votre Runbook actuel. Une *jonction* prend plusieurs entrées et attend que tout soit terminé pour poursuivre le workflow. Une activité *Code* exécute une ou plusieurs lignes de code PowerShell ou de workflow PowerShell en fonction du type de Runbook graphique. Vous pouvez utiliser cette activité pour du code personnalisé ou pour une fonctionnalité difficile à obtenir avec d’autres activités. |

### <a name="configuration-control"></a>Contrôle Configuration
Le contrôle Configuration vous permet de renseigner les détails concernant un objet sélectionné dans le canevas. Les propriétés disponibles dans ce contrôle dépendent du type d’objet sélectionné. Lorsque vous sélectionnez une option dans le contrôle Configuration, des panneaux supplémentaires s'ouvrent afin de fournir des informations supplémentaires.

### <a name="test-control"></a>Contrôle Test
Le contrôle Test n'est pas affiché lors du premier démarrage de l'éditeur graphique. Il s'ouvre lorsque vous [testez un Runbook graphique](#graphical-runbook-procedures)de manière interactive. 

## <a name="graphical-runbook-procedures"></a>Procédures relatives aux Runbooks graphiques
### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportation et importation d'un Runbook graphique
Vous pouvez exporter uniquement la version publiée d'un Runbook graphique. Si le Runbook n'a pas encore été publié, le bouton **Exportation** est désactivé. Lorsque vous cliquez sur le bouton **Exportation**, le Runbook est téléchargé sur votre ordinateur local. Le nom du fichier correspond au nom du Runbook avec une extension *graphrunbook* .

Vous pouvez importer un fichier de Runbook graphique ou de workflow PowerShell graphique en sélectionnant l’option **Importer** lors de l’ajout d’un Runbook. Lorsque vous sélectionnez le fichier à importer, vous pouvez conserver le même **nom** ou en fournir un nouveau. Le champ Type de runbook affiche le type de Runbook auquel est comparé le fichier sélectionné. Si vous tentez de sélectionner un autre type incorrect, un message vous indique que vous risquez de rencontrer des conflits et des erreurs de syntaxe au cours de la conversion. 

![Importer un Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Test d'un Runbook graphique
Vous pouvez tester le brouillon d’un Runbook dans le portail Azure tout en laissant la version publiée du Runbook inchangée, ou vous pouvez tester un Runbook avant sa publication. Cela vous permet de vérifier que le Runbook fonctionne correctement avant de remplacer la version publiée. Lorsque vous testez un Runbook, le Runbook brouillon est exécuté et toutes les actions qu'il effectue sont finalisées. Aucun historique des tâches n'est créé, mais la sortie est affichée dans le volet de sortie du test. 

Ouvrez le contrôle Test pour un Runbook en ouvrant le Runbook en vue de sa modification, puis cliquez sur le bouton **Volet de test** .

Le contrôle Test demande tous les paramètres d'entrée, et vous pouvez démarrer le Runbook en cliquant sur le bouton **Démarrer**.

### <a name="publishing-a-graphical-runbook"></a>Publication d'un Runbook graphique
Dans Azure Automation, chaque Runbook a un brouillon et une version publiée. Seule la version publiée est disponible à l'exécution, et seul le brouillon peut être modifié. La version publiée n'est pas affectée par les modifications apportées à la version Bouillon. Lorsque le brouillon est prêt à être rendu disponible, vous le publiez, ce qui remplace la version publiée par le brouillon.

Vous pouvez publier un Runbook graphique en ouvrant le Runbook en vue de sa modification, puis en cliquant sur le bouton **Publier** .

Lorsqu'un Runbook n'a pas encore été publié, il a le statut **Nouveau**. Une fois publié, il a le statut **Publié**. Si vous modifiez le Runbook après qu'il a été publié, et que la version publiée et le brouillon sont différents, le Runbook a le statut **En mode Édition**.

![États du Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png) 

Vous avez également la possibilité de revenir à la version publiée d'un Runbook. Cela supprime toutes les modifications apportées depuis la dernière publication du Runbook et remplace le brouillon du Runbook par la version publiée.

## <a name="activities"></a>Activités
Les activités sont les éléments essentiels d'un Runbook. Une activité peut être une applet de commande PowerShell, un Runbook enfant ou une activité de workflow. Vous ajoutez une activité au Runbook en cliquant avec le bouton droit dans le contrôle Bibliothèque, puis en sélectionnant **Ajouter au canevas**. Vous pouvez ensuite cliquer sur l'activité et la faire glisser pour la placer à l'emplacement de votre choix sur le canevas. L'emplacement de l'activité sur le canevas n'affecte en aucune façon le fonctionnement du Runbook. Vous pouvez disposer votre Runbook de la façon qui vous convient le mieux pour visualiser son fonctionnement. 

![Ajouter au canevas](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Sélectionnez l'activité sur le canevas pour configurer ses propriétés et ses paramètres dans le panneau Configuration. Vous pouvez remplacer **l’étiquette** de l’activité par une chaîne descriptive. L'applet de commande d'origine est toujours en cours d'exécution. Vous modifiez simplement son nom d'affichage qui est utilisé dans l'éditeur graphique. L'étiquette doit être unique dans le Runbook. 

### <a name="parameter-sets"></a>Jeux de paramètres
Un jeu de paramètres définit les paramètres obligatoires et facultatifs qui acceptent des valeurs pour une applet de commande particulière. Toutes les applets de commande ont au moins un jeu de paramètres ; certaines en ont plusieurs. Si une applet de commande a plusieurs jeux de paramètres, vous devez sélectionner celui que vous utilisez avant de pouvoir configurer les paramètres. Les paramètres que vous pouvez configurer dépendent de l'ensemble de paramètres que vous choisissez. Vous pouvez changer le jeu de paramètres utilisé par une activité en sélectionnant **Jeu de paramètres** , puis en sélectionnant un autre jeu. Dans ce cas, toutes les valeurs de paramètres que vous avez configurées sont perdues.

Dans l’exemple suivant, l’applet de commande Get-AzureRmVM contient trois jeux de paramètres. Vous ne pouvez pas configurer les valeurs de paramètres tant que vous n'avez pas sélectionné l'un des jeux de paramètres. Le jeu de paramètres ListVirtualMachineInResourceGroupParamSet permet de retourner toutes les machines virtuelles d’un groupe de ressources. Il ne comporte qu’un seul paramètre facultatif. Le jeu de paramètres **GetVirtualMachineInResourceGroupParamSet** sert à spécifier la machine virtuelle. Il comporte deux paramètres obligatoires et un paramètre facultatif.

![Jeu de paramètres](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valeurs de paramètres
Lorsque vous spécifiez une valeur pour un paramètre, vous sélectionnez une source de données pour déterminer la façon dont la valeur est spécifiée. Les sources de données disponibles pour un paramètre particulier dépendent des valeurs valides pour ce paramètre. Par exemple, Null n’est pas une option disponible pour un paramètre qui n'autorise pas les valeurs null.

| source de données | Description |
|:--- |:--- |
| Valeur constante |Entrez une valeur pour le paramètre. Cette option est disponible uniquement pour les types de données suivants : Int32, Int64, String, Boolean, DateTime, Switch. |
| Sortie d'activité |Sortie d'une activité qui précède l'activité actuelle dans le workflow. Toutes les activités valides sont répertoriées. Sélectionnez simplement l'activité pour utiliser sa sortie comme valeur de paramètre. Si l'activité génère un objet ayant plusieurs propriétés, vous pouvez taper le nom de la propriété après avoir sélectionné l'activité. |
| Entrée de Runbook |Sélectionnez un paramètre d'entrée de Runbook en tant qu'entrée pour le paramètre de l'activité. |
| Ressource de variable |Sélectionnez une variable Automation comme entrée. |
| Ressource d’informations d’identification |Sélectionnez les informations d'identification Automation comme entrée. |
| Ressource de certificat |Sélectionnez un certificat Automation comme entrée. |
| Ressource de connexion |Sélectionnez une connexion Automation comme entrée. |
| Expression PowerShell |Spécifiez une [expression PowerShell](#powershell-expressions)simple. L'expression est évaluée avant l'activité, et le résultat utilisé pour la valeur du paramètre. Vous pouvez utiliser des variables pour faire référence à la sortie d'une activité ou à un paramètre d'entrée de Runbook. |
| Non configuré |Efface toute valeur qui a été précédemment configurée. |

#### <a name="optional-additional-parameters"></a>Autres paramètres facultatifs
Toutes les applets de commande peuvent fournir des paramètres supplémentaires. Il s'agit de paramètres communs PowerShell ou d'autres paramètres personnalisés. Une zone de texte dans laquelle vous pouvez fournir des paramètres en utilisant la syntaxe PowerShell s'affiche. Par exemple, pour utiliser le paramètre commun **Verbose**, vous devez spécifier **« -Verbose:$True »**.

### <a name="retry-activity"></a>Nouvelles tentatives d’activité
La fonction **Comportement des nouvelles tentatives** permet à une activité de s’exécuter plusieurs fois jusqu’à ce qu’une condition particulière soit remplie. Elle agit à la manière d’une boucle. Vous pouvez utiliser cette fonctionnalité pour les activités qui doivent s’exécuter plusieurs fois, qui sont sujettes à des erreurs et qui peuvent nécessiter plusieurs tentatives avant d’aboutir. Vous pouvez aussi tester les informations de sortie de l’activité pour vérifier la validité des données. 

Lorsque vous activez les nouvelles tentatives pour une activité, vous pouvez définir un délai et une condition. Le délai correspond au temps (exprimé en secondes ou minutes) d’attente du Runbook avant la nouvelle exécution de l’activité. Si aucun délai n’est spécifié, l’activité est immédiatement réexécutée après son exécution. 

![Délai de nouvelle tentative d’activité](media/automation-graphical-authoring-intro/retry-delay.png)

La condition de nouvelle tentative est une expression PowerShell qui est évaluée après chaque exécution de l’activité. Si l’expression correspond à True, l’activité s’exécute à nouveau. Si l’expression correspond à False, l’activité n’est pas réexécutée et le Runbook passe à l’activité suivante. 

![Délai de nouvelle tentative d’activité](media/automation-graphical-authoring-intro/retry-condition.png)

La condition de nouvelle tentative peut utiliser une variable appelée $RetryData qui fournit un accès aux informations sur les nouvelles tentatives d’activité. Cette variable possède les propriétés indiquées dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| NumberOfAttempts |Nombre d’exécutions de l’activité. |
| Sortie |Sortie de la dernière exécution de l’activité. |
| TotalDuration |Délai écoulé depuis la première exécution de l’activité. |
| StartedAt |Heure au format UTC à laquelle l’activité a été exécutée pour la première fois. |

Voici des exemples de conditions de nouvelles tentatives d’activité.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10 

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1 

    # Run the activity repeatedly until 2 minutes has elapsed. 
    $RetryData.TotalDuration.TotalMinutes -ge 2

Après avoir configuré une condition de nouvelle tentative pour une activité, l’activité émet un rappel à l’aide de deux signaux visuels. Le premier est présenté dans l’activité et le second apparaît lorsque vous passez en revue la configuration de l’activité.

![Indicateurs visuels de nouvelle tentative d’activité](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Contrôle Script de workflow
Un contrôle Code est une activité spéciale qui accepte un script PowerShell ou un script de workflow PowerShell selon le type de Runbook graphique créé pour fournir des fonctionnalités qui, sans cela, ne seraient peut-être pas disponibles. Il n'accepte pas de paramètres, mais il peut utiliser des variables pour les paramètres de sortie d'activité et d'entrée de Runbook. Toute sortie de l'activité est ajoutée au bus de données, sauf si elle n'a aucun lien sortant, auquel cas elle est ajoutée à la sortie du Runbook.

Par exemple, le code suivant effectue des calculs de date à l'aide d'une variable d'entrée de Runbook appelée $NumberOfDays. Il envoie ensuite la valeur date-heure calculée en tant que sortie qui doit être utilisée par les activités suivantes dans le Runbook.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>Liens et workflow
Dans un Runbook graphique, un **lien** connecte deux activités. Il est affiché sur le canevas sous la forme d'une flèche pointant de l'activité source vers l'activité de destination. Les activités s'exécutent dans le sens de la flèche, l'activité de destination commençant une fois l'activité source terminée. 

### <a name="create-a-link"></a>Créer un lien
Créez un lien entre deux activités en sélectionnant l'activité source, puis en cliquant sur le cercle situé en bas de la forme. Faites glisser la flèche vers l'activité de destination, puis relâchez-la.

![Créer un lien](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Sélectionnez le lien pour configurer ses propriétés dans le panneau Configuration. Cela inclut le type du lien, qui est décrit dans le tableau suivant :

| Type de lien | Description |
|:--- |:--- |
| Pipeline |L'activité de destination est exécutée une fois pour chaque sortie d'objet de l'activité source. L'activité de destination n'est pas exécutée si l'activité source ne génère aucune sortie. La sortie de l'activité source est disponible sous la forme d'un objet. |
| Séquence |L'activité de destination s'exécute une seule fois. Elle reçoit un tableau d'objets de l'activité source. La sortie de l'activité source est disponible sous la forme d'un tableau d'objets. |

### <a name="starting-activity"></a>Activité de départ
Un Runbook graphique commence par toute activité qui n'a pas un lien entrant. Il s'agit souvent d'une seule activité qui fait office d'activité de départ du Runbook. Si plusieurs activités n'ont pas de lien entrant, le Runbook démarre en les exécutant en parallèle. Lorsque chaque activité est terminée, il suit les liens pour exécuter les autres activités.

### <a name="conditions"></a>Conditions
Lorsque vous spécifiez une condition sur un lien, l'activité de destination est exécutée uniquement si la condition a pour résultat la valeur true. Vous utilisez généralement une variable $ActivityOutput dans une condition pour récupérer la sortie de l'activité source. 

Pour un lien de type pipeline, vous spécifiez une condition pour un seul objet, et cette condition est évaluée pour chaque sortie d'objet par l'activité source. L'activité de destination est ensuite exécutée pour chaque objet qui remplit la condition. Par exemple, avec une activité source Get-AzureRmVm, la syntaxe suivante peut être utilisée pour qu’un lien pipeline conditionnel récupère uniquement les machines virtuelles se trouvant dans le groupe de ressources nommé *Group1*. 

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

Pour un lien de type séquence, la condition est évaluée une seule fois, car un seul tableau contenant la sortie de tous les objets à partir de l'activité source est retourné. C'est pour cette raison qu'un lien séquence ne peut pas être utilisé pour filtrer comme un lien pipeline ; il détermine simplement si l'activité suivante est exécutée ou non. Prenez par exemple l’ensemble des activités suivantes dans le Runbook de démarrage de machine virtuelle.<br> ![Lien conditionnel avec séquences](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
Trois liens séquentiels différents vérifient que des valeurs ont bien été fournies aux deux paramètres d’entrée de Runbook représentant le nom de la machine virtuelle et le nom du groupe de ressources afin de déterminer l’action à entreprendre, à savoir démarrer une seule machine virtuelle, démarrer toutes les machines virtuelles du groupe de ressources ou démarrer toutes les machines virtuelles d’un abonnement. Pour la liaison séquentielle entre Connect to Azure et Get single VM, voici la logique de la condition :

    <# 
    Both VMName and ResourceGroupName runbook input parameters have values 
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

Lorsque vous utilisez un lien conditionnel, les données disponibles de l'activité source vers d'autres activités dans cette branche sont filtrées par la condition. Si une activité est la source vers plusieurs liens, les données disponibles pour les activités de chaque branche dépendent de la condition spécifiée dans le lien de connexion à cette branche.

Par exemple, l’activité **Start-AzureRmVm** du Runbook ci-dessous démarre toutes les machines virtuelles. Elle comporte deux liens conditionnels. Le premier lien conditionnel utilise l’expression *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true* pour appliquer un filtre si l’activité Start-AzureRmVm s’est correctement exécutée. Le second lien conditionnel utilise l’expression *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true* pour appliquer un filtre si l’activité Start-AzureRmVm n’est pas parvenue à démarrer la machine virtuelle. 

![Exemple de lien conditionnel](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Toute activité qui suit le premier lien et qui utilise la sortie d'activité de Get-AzureVM obtient uniquement les machines virtuelles qui étaient démarrées au moment de l'exécution de Get-AzureVM. Toute activité qui suit le deuxième lien obtient uniquement les machines virtuelles qui étaient arrêtées au moment de l'exécution de Get-AzureVM. Toute activité suivant le troisième lien obtient toutes les machines virtuelles, quel que soit leur état d'exécution.

### <a name="junctions"></a>Jonctions
Une jonction est une activité spéciale qui attend que toutes les branches entrantes aient terminé. Cela vous permet d'exécuter plusieurs activités en parallèle et de vous assurer qu'elles ont toutes terminé avant de continuer.

Alors qu'une jonction peut avoir un nombre illimité de liens entrants, un seul de ces liens peut être un pipeline. Le nombre de liens de type séquence entrants n'est pas limité. Vous êtes autorisé à créer la jonction avec plusieurs liens de type pipeline entrants et à enregistrer le Runbook, mais il échoue lorsqu'il est exécuté.

L'exemple ci-dessous représente une partie d'un Runbook qui démarre un ensemble de machines virtuelles tout en téléchargeant simultanément des correctifs à appliquer à ces machines. Une jonction permet de garantir que les deux processus sont terminés avant que le Runbook poursuive.

![jonction](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles
Un cycle se produit lorsqu'une activité de destination a un lien qui revient vers son activité source ou vers une autre activité qui, au final, revient à sa source. Les cycles ne sont actuellement pas autorisés dans la création de graphiques. Si votre Runbook a un cycle, il s'enregistre correctement, mais il reçoit une erreur lorsqu'il s'exécute.

![Cycle](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Partage de données entre des activités
Toutes les données qui sont générées par une activité avec un lien sortant sont écrites dans le *bus de données* du Runbook. Toute activité du Runbook peut utiliser les données présentes dans le bus de données pour renseigner les valeurs de paramètres ou les inclure dans le code de script. Une activité peut accéder à la sortie de toute activité précédente du workflow. 

La façon dont les données sont écrites dans le bus de données varie en fonction du type de lien figurant sur l'activité. Pour un **pipeline**, les données sont sorties sous la forme de plusieurs objets. Pour un lien de type **séquence** , les données sont sorties sous la forme d'un tableau. S'il n'y a qu'une seule valeur, elle sort sous la forme d'un tableau à un seul élément.

Vous avez le choix entre deux méthodes pour accéder aux données présentes dans le bus de données. La première consiste à utiliser une source de données **Sortie d'activité** pour renseigner un paramètre d'une autre activité. Si la sortie est un objet, vous ne pouvez spécifier qu'une seule propriété.

![Sortie d'activité](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Vous pouvez également récupérer la sortie d’une activité dans une source de données **Expression PowerShell** ou à partir d’une activité **Script de workflow** avec une variable ActivityOutput. Si la sortie est un objet, vous ne pouvez spécifier qu'une seule propriété. Les variables ActivityOutput utilisent la syntaxe suivante.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName 

### <a name="checkpoints"></a>Points de contrôle
Vous pouvez définir des [points de contrôle](automation-powershell-workflow.md#checkpoints) dans un Runbook de workflow PowerShell graphique en sélectionnant *Runbook de point de contrôle* sur n’importe quelle activité. Cette option permet de définir un point de contrôle après l’exécution de l’activité.

![Point de contrôle](media/automation-graphical-authoring-intro/set-checkpoint.png)

Les points de contrôle sont activés uniquement dans les Runbooks de workflow PowerShell graphique ; ils ne sont pas disponibles dans les Runbooks graphiques. Si le Runbook utilise les applets de commande Azure, vous devez suivre toute activité soumise à des points de contrôle avec l’applet de commande Add-AzureRMAccount dans l’éventualité où le Runbook serait interrompu et redémarré sur un autre Worker à partir de ce point de contrôle. 

## <a name="authenticating-to-azure-resources"></a>Authentification auprès de ressources Azure
Dans Azure Automation, les Runbooks qui gèrent des ressources Azure doivent s’authentifier auprès d’Azure. La fonctionnalité [Compte d’identification](automation-offering-get-started.md#creating-an-automation-account) (également appelée principal de service) est la méthode utilisée par défaut pour accéder aux ressources Azure Resource Manager dans votre abonnement à l’aide des runbooks Automation. Vous pouvez ajouter cette fonctionnalité à un runbook graphique en ajoutant la ressource de connexion **AzureRunAsConnection**, qui utilise l’applet de commande PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) et l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) sur le canevas. Cette opération est illustrée dans l’exemple suivant :<br>![Activités d’authentification de l’identification](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
L’activité Get Run As Connection (par exemple, Get-AutomationConnection), est configurée avec une source de données de valeur constante nommée AzureRunAsConnection.<br>![Configuration de la connexion d’identification](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
L’activité suivante, Add-AzureRmAccount, ajoute le compte d’identification authentifié pour l’utiliser dans le runbook.<br>
![Jeu de paramètres Add-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
Pour les paramètres **APPLICATIONID**, **CERTIFICATETHUMBPRINT** et **TENANTID**, vous devez spécifier le nom de la propriété du chemin d’accès du champ car l’activité génère un objet comportant plusieurs propriétés. À défaut, la tentative d’authentification échoue lors de l’exécution du runbook. Vous devez au minimum renseigner ces informations pour authentifier votre runbook auprès du compte d’identification.

Pour garantir une compatibilité descendante pour les abonnés qui ont créé un compte Automation à l’aide d’un [compte d’utilisateur Azure AD](automation-create-aduser-account.md) afin de gérer le déploiement Azure Classic ou pour les ressources Azure Resource Manager, le mode d’authentification est l’applet de commande Add-AzureAccount avec une [ressource d’informations d’identification](automation-credentials.md) qui représente un utilisateur Active Directory ayant accès au compte Azure.

Vous pouvez ajouter cette fonctionnalité à un Runbook graphique en ajoutant une ressource d'informations d'identification au canevas, suivie d'une activité Add-AzureAccount. Add-AzureAccount utilise l'activité d'informations d'identification pour son entrée. Cette opération est illustrée dans l’exemple suivant :

![Activités d'authentification](media/automation-graphical-authoring-intro/authentication-activities.png)

Vous devez effectuer une authentification au début du Runbook et après chaque point de contrôle. Cela signifie qu'une activité Add-AzureAccount doit être ajoutée après chaque activité Checkpoint-Workflow. Vous n'avez pas besoin d'ajouter une activité d'informations d'identification dans la mesure où vous pouvez utiliser la même. 

![Sortie d'activité](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Entrée et sortie de Runbook
### <a name="runbook-input"></a>Entrée de Runbook
Un Runbook peut nécessiter une entrée d’un utilisateur lorsqu’il démarre le Runbook via le portail Azure ou à partir d’un autre Runbook si celui actuel est utilisé en tant qu’enfant.
Par exemple, si vous avez un Runbook qui crée une machine virtuelle, vous devrez peut-être fournir des informations telles que le nom de la machine virtuelle et d'autres propriétés chaque fois que vous démarrez le Runbook. 

Vous acceptez une entrée pour un Runbook en définissant un ou plusieurs paramètres d'entrée. Vous fournissez des valeurs pour ces paramètres chaque fois à chaque démarrage du Runbook. Lorsque vous démarrez un Runbook avec le portail Azure, il vous invite à fournir des valeurs pour chacun des paramètres d’entrée du Runbook.

Vous pouvez accéder aux paramètres d'entrée d'un Runbook en cliquant sur le bouton **Entrée et sortie** dans la barre d'outils du Runbook. 

Cela ouvre le contrôle **Entrée et sortie** où vous pouvez modifier un paramètre d’entrée existant ou en créer un nouveau en cliquant sur **Ajouter une entrée**. 

![Ajouter une entrée](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Chaque paramètre d'entrée est défini par les propriétés figurant dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| NOM |Nom unique du paramètre. Il ne peut contenir que des caractères alphanumériques et ne peut pas contenir d'espace. |
| Description |Description facultative du paramètre d'entrée. |
| type |Type de données attendu pour la valeur de paramètre. Le portail Azure fournit un contrôle approprié pour le type de données de chaque paramètre lorsque vous êtes invité à indiquer une entrée. |
| Obligatoire |Spécifie si une valeur doit être fournie pour le paramètre. Le Runbook ne peut pas être démarré si vous ne fournissez pas une valeur pour chaque paramètre obligatoire pour lequel aucune valeur par défaut n'est définie. |
| Valeur par défaut |Spécifie quelle valeur est utilisée pour le paramètre si aucune n'est pas fournie. Cela peut être Null ou une valeur spécifique. |

### <a name="runbook-output"></a>Sortie de Runbook
Les données créées par toute activité qui ne dispose pas d'un lien sortant sont ajoutées à la [sortie du Runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). La sortie est enregistrée avec la tâche du Runbook et est disponible pour un Runbook parent lorsque le Runbook est utilisé en tant qu'enfant. 

## <a name="powershell-expressions"></a>Expressions PowerShell
Un des avantages de la création de graphiques est qu'elle vous donne la possibilité de créer un Runbook avec une connaissance de PowerShell minimale. Pour le moment, vous devez avoir quelques connaissances de base sur PowerShell pour remplir certaines [valeurs de paramètres](#activities) et définir des [conditions de lien](#links-and-workflow). Cette section propose une brève introduction aux utilisateurs qui ne connaissent pas les expressions PowerShell. La totalité des informations sur PowerShell est disponible dans [Écriture de scripts avec Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). 

### <a name="powershell-expression-data-source"></a>Source de données d’expressions PowerShell
Vous pouvez utiliser une expression PowerShell comme source de données pour remplir la valeur d'un [paramètre d'activité](#activities) avec les résultats d'un code PowerShell. Il peut s'agir d'une seule ligne de code qui exécute une fonction simple ou de plusieurs lignes qui suivent une logique complexe. Toute sortie de commande non affectée à une variable correspond à la sortie de la valeur du paramètre. 

Par exemple, la commande suivante affiche la date actuelle. 

    Get-Date

Les commandes suivantes construisent une chaîne à partir de la date actuelle et l'affectent à une variable. Le contenu de la variable est ensuite envoyé à la sortie 

    $string = "The current date is " + (Get-Date)
    $string

Les commandes suivantes évaluent la date actuelle et renvoient une chaîne qui indique si la date du jour est un jour de la semaine ou du week-end. 

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>Sortie d'activité
Pour utiliser la sortie d'une activité précédente dans le Runbook, utilisez la variable $ActivityOutput avec la syntaxe suivante.

    $ActivityOutput['Activity Label'].PropertyName

Par exemple, si la propriété d'une activité requiert le nom d'une machine virtuelle, vous pouvez utiliser l'expression suivante :

    $ActivityOutput['Get-AzureVm'].Name

Si la propriété requiert l'objet machine virtuelle au lieu d'une simple propriété, vous devez renvoyer l'objet entier avec la syntaxe suivante.

    $ActivityOutput['Get-AzureVm']

Vous pouvez également utiliser la sortie d'une activité dans une expression plus complexe comme celle-ci, qui concatène le texte au nom de la machine virtuelle.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>Conditions
Utilisez les [opérateurs de comparaison](https://technet.microsoft.com/library/hh847759.aspx) pour comparer des valeurs ou déterminer si une valeur correspond à un modèle spécifié. Une comparaison renvoie la valeur $true ou $false.

Par exemple, la condition suivante détermine si la machine virtuelle d’une activité nommée *Get-AzureVM* est actuellement *arrêtée*. 

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

La condition suivante détermine si la même machine virtuelle est dans un état autre qu' *arrêté*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Vous pouvez joindre plusieurs conditions en utilisant un [opérateur logique](https://technet.microsoft.com/library/hh847789.aspx) comme **-and** ou **-or**. Par exemple, la condition suivante détermine si la machine virtuelle de l’exemple précédent est dans un état *arrêté* ou *en cours d’arrêt*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### <a name="hashtables"></a>Tables de hachage
[Tables de hachage](http://technet.microsoft.com/library/hh847780.aspx) sont des paires nom/valeur servant à renvoyer un ensemble de valeurs. Les propriétés de certaines activités peuvent attendre une table de hachage plutôt qu'une valeur simple. Vous pouvez également voir une table de hachage appelée dictionnaire. 

Une table de hachage se crée avec la syntaxe suivante. Une table de hachage peut contenir un nombre quelconque d'entrées, chacune étant définie par un nom et une valeur.

    @{ <name> = <value>; [<name> = <value> ] ...}

Par exemple, l'expression suivante crée une table de hachage à utiliser dans la source de données pour un paramètre d'activité qui attend une table de hachage avec des valeurs pour une recherche sur Internet.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

L'exemple suivant utilise la sortie d'une activité nommée *Obtenir la connexion Twitter* pour remplir une table de hachage.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>Étapes suivantes
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md) 
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour comprendre les mécanismes d’authentification à l’aide du compte d’identification Automation, consultez [Configurer un compte d’identification Azure](automation-sec-configure-azure-runas-account.md)

