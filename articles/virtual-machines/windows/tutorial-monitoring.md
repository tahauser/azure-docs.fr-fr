---
title: Surveillance Azure, mise à jour Azure et machines virtuelles Windows | Microsoft Docs
description: Didacticiel - Surveiller et mettre à jour une machine virtuelle Windows avec Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fdb8009e3dbca1037cae61ec8627f73190a8263d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Surveiller et mettre à jour une machine virtuelle Windows avec Azure PowerShell

Surveillance Azure utilise des agents pour collecter des données de performances et de démarrage à partir des machines virtuelles Azure, stocker ces données dans le stockage Azure et les rendre accessibles via le portail, le module Azure PowerShell et l’interface CLI Azure. La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur une machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte de machine virtuelle
> * Installer l’extension de diagnostics
> * Afficher les métriques de la machine virtuelle
> * Créer une alerte
> * Gérer les mises à jour Windows
> * Configurer la surveillance avancée

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) peut en créer une pour vous. Au cours du didacticiel, remplacez le groupe de ressources, le nom de la machine virtuelle et l’emplacement, si nécessaire.

## <a name="view-boot-diagnostics"></a>Afficher les diagnostics de démarrage

Au démarrage des machines virtuelles Windows, l’agent de diagnostic de démarrage capture le résultat à l’écran. Celui-ci peut être utilisé pour résoudre certains problèmes. Cette fonctionnalité est activée par défaut. Les captures d’écran sont stockées dans un compte de stockage Azure, qui est également créé par défaut.

Vous pouvez obtenir les données de diagnostic de démarrage avec la commande [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). Dans l’exemple suivant, les diagnostics de démarrage sont téléchargés à la racine du lecteur *c:\*.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Afficher les métriques de l’hôte

Une machine virtuelle Windows possède une machine virtuelle hôte dédiée dans Azure, avec qui elle interagit. Les métriques sont automatiquement collectées pour l’hôte et peuvent être visualisées dans le portail Azure.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Pour voir comment la machine virtuelle hôte fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle, puis sélectionnez une des métriques de l’hôte sous **Métriques disponibles**.

    ![Afficher les métriques de l’hôte](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installer l’extension Diagnostics

Les métriques de base de l’hôte sont disponibles, mais pour voir des métriques plus précises et spécifiques à la machine virtuelle, vous devez installer l’extension Diagnostics Azure sur la machine virtuelle. L’extension Diagnostics Azure permet de récupérer des données supplémentaires de surveillance et de diagnostic auprès de la machine virtuelle. Vous pouvez voir ces métriques de performances et créer des alertes basées sur le fonctionnement de la machine virtuelle. L’extension Diagnostics est installée via le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Cliquez sur **Paramètres de diagnostic**. La liste montre que les *Diagnostics de démarrage* sont déjà activés depuis la section précédente. Cliquez sur la case à cocher *Métriques de base*.
3. Cliquez sur le bouton **Activer la surveillance au niveau invité**.

    ![Afficher les métriques de diagnostic](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Afficher les métriques de la machine virtuelle

Vous pouvez afficher les métriques de la machine virtuelle de la même façon que vous avez affiché le mesures de la machine virtuelle hôte :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Pour voir comment la machine virtuelle fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle puis sélectionnez une des métriques de diagnostic sous **Métriques disponibles**.

    ![Afficher les métriques de la machine virtuelle](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Créez des alertes

Vous pouvez créer des alertes en fonction de métriques de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. Les alertes sont affichées dans le portail Azure ou peuvent être envoyées par e-mail. Vous pouvez également déclencher des runbooks Azure Automation ou Azure Logic Apps en réponse aux alertes générées.

L’exemple suivant crée une alerte pour l’utilisation moyenne de l’UC.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Cliquez sur **Règles d’alerte** dans le panneau de la machine virtuelle puis cliquez sur **Ajouter une alerte Métrique** dans la partie supérieure du panneau des alertes.
3. Spécifiez un **Nom** pour votre alerte, comme *myAlertRule*
4. Pour déclencher une alerte quand le pourcentage d’UC dépasse 1,0 pendant cinq minutes, laissez toutes les autres valeurs par défaut sélectionnées.
5. Si vous le souhaitez, cochez la case *Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs* pour envoyer la notification par e-mail. L’action par défaut est de présenter une notification dans le portail.
6. Cliquez sur le bouton **OK**.

## <a name="manage-windows-updates"></a>Gérer les mises à jour Windows

La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure.
Directement à partir de votre machine virtuelle, vous pouvez rapidement évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour nécessaires et passer en revue les résultats des déploiements pour vérifier que les mises à jour ont été appliquées correctement à la machine virtuelle.

Pour plus d’informations sur les prix, consultez [Tarification Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Activer la gestion des mises à jour

Activer la gestion des mises à jour pour votre machine virtuelle :

1. Sur le côté gauche de l’écran, sélectionnez **Machines virtuelles**.
2. Sélectionnez une machine virtuelle dans la liste.
3. Dans l’écran de la machine virtuelle, dans la section **Opérations**, cliquez sur **Gestion des mises à jour**. L’écran **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle.
La validation inclut la vérification de l’existence d’un espace de travail Log Analytics et d’un compte Automation lié, et si la solution est dans l’espace de travail.

Un espace de travail [Log Analytics](../../log-analytics/log-analytics-overview.md) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour.
L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.
Pour effectuer une action supplémentaire sur les machines virtuelles qui nécessitent des mises à jour, Azure Automation vous permet d’exécuter des runbooks sur les machines virtuelles, par exemple télécharger et appliquer des mises à jour.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un runbook Worker hybride Automation.
Cet agent est utilisé pour communiquer avec la machine virtuelle et pour obtenir des informations sur l’état des mises à jour.

Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

Si l’intégration n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* Un [worker runbook hybride](../../automation/automation-hybrid-runbook-worker.md) est activé sur la machine virtuelle

L’écran **Gestion des mises à jour** s’ouvre. Configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

![Activer la solution de gestion des mises à jour](./media/tutorial-monitoring/manageupdates-update-enable.png)

L’activation de la solution peut prendre jusqu’à 15 minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur. Une fois la solution activée, des informations sur les mises à jour manquantes sur la machine virtuelle sont envoyées à Log Analytics. Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

### <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît. Une fois l’évaluation des mises à jour terminée, vous pouvez afficher une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

 ![Afficher l’état des mises à jour](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Pour installer les mises à jour, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

Planifier un nouveau déploiement de mises à jour pour la machine virtuelle en cliquant sur **Planifier le déploiement de la mise à jour** en haut de l’écran **Gestion des mises à jour**. Dans l’écran **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : spécifiez un nom unique pour identifier le déploiement de mises à jour.
* **Classification de mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour incluait dans le déploiement. Les types de classification sont :
  * Mises à jour critiques
  * Mises à jour de sécurité
  * Correctifs cumulatifs
  * Packs de fonctionnalités
  * Service Packs
  * Mises à jour de définitions
  * Outils
  * Mises à jour

* **Paramètres de planification** : vous pouvez accepter la date et l’heure par défaut, qui est de 30 minutes après l’heure actuelle, ou spécifier un moment différent.
  Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique. Cliquez sur l’option Périodique sous Périodicité pour définir une planification périodique.

  ![Écran Paramètres de planification des mises à jour](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Fenêtre de maintenance (en minutes)** : spécifiez la période de temps pendant laquelle le déploiement des mises à jour doit se produire.  Cela permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer** ; vous revenez ensuite au tableau de bord des états.
Notez que le tableau **Planifié** montre la planification de déploiement que vous avez créée.

> [!WARNING]
> Pour les mises à jour nécessitant un redémarrage, la machine virtuelle est automatiquement redémarrée.

### <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans l’écran **Gestion des mises à jour**.
S’il est en cours d’exécution, son état est **En cours d’exécution**. Une fois le déploiement terminé, s’il est réussi, l’état passe à **Réussi**.
S’il existe un échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.
Cliquez sur le déploiement des mises à jour terminé pour voir le tableau de bord pour ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/tutorial-monitoring/manageupdates-view-results.png)

Dans la vignette **Résultats des mises à jour**, vous pouvez voir un récapitulatif du nombre total de mises à jour et les résultats du déploiement sur la machine virtuelle.
Dans le tableau de droite se trouve une répartition détaillée de chaque mise à jour et les résultats de l’installation, qui peut être une des valeurs suivantes :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussi** : la mise à jour a réussi
* **Échec** : la mise à jour a échoué

Cliquez sur **Tous les journaux** pour voir toutes les entrées de journal créées par le déploiement.

Cliquez sur la vignette **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Cliquez sur **Erreurs** pour afficher les informations détaillées sur les erreurs du déploiement.

## <a name="monitor-changes-and-inventory"></a>Surveiller les modifications et l’inventaire

Vous pouvez collecter et afficher l’inventaire des logiciels, fichiers, démons Linux, services Windows et des clés de registre Windows présents sur vos ordinateurs. Le suivi des configurations de vos machines peut vous aider à identifier les problèmes opérationnels au sein de votre environnement et à mieux comprendre l’état de vos machines.

### <a name="enable-change-and-inventory-management"></a>Activer la gestion des modifications et de l’inventaire

Activez la gestion des modifications et de l’inventaire pour votre machine virtuelle :

1. Sur le côté gauche de l’écran, sélectionnez **Machines virtuelles**.
2. Sélectionnez une machine virtuelle dans la liste.
3. Dans l’écran de la machine virtuelle, dans la section **Operations**, cliquez sur **Inventaire** ou **Suivi des modifications**. L’écran **Enable Change Tracking and Inventory** (Activer le suivi des modifications et inventaire) s’ouvre.

Configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés. Même si les solutions sont distinctes dans le menu, elles forment une même solution. Le fait d’en activer une active les deux pour votre machine virtuelle.

![Activer le suivi des modifications et de l’inventaire](./media/tutorial-monitoring/manage-inventory-enable.png)

Une fois la solution activée, un délai peut être nécessaire pour la collecte de l’inventaire sur la machine virtuelle avant l’affichage de données.

### <a name="track-changes"></a>Suivi des modifications

Sur votre machine virtuelle, sélectionnez **Suivi des modifications** sous **OPÉRATIONS**. Cliquez sur **Modifier les paramètres**, la page **Suivi des modifications** s’affiche. Sélectionnez le type de paramètre dont vous souhaitez effectuer le suivi, puis cliquez sur **+ Ajouter** pour configurer les paramètres. Les options disponibles pour Windows sont les suivantes :

* Registre Windows
* Fichiers Windows

Pour plus d’informations sur Change Tracking, consultez [Résoudre les modifications sur une machine virtuelle](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Afficher l’inventaire

Sur votre machine virtuelle, sélectionnez **Inventaire** sous **OPÉRATIONS**. Dans l’onglet **Logiciels**, un tableau dresse la liste des logiciels détectés. Les informations détaillées sur chaque enregistrement de logiciel sont visibles dans le tableau. Ces détails incluent le nom du logiciel, la version, l’éditeur, l’heure de la dernière actualisation.

![Afficher l’inventaire](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Surveiller les journaux d’activité et les modifications

À partir de la page **Suivi des modifications** sur votre machine virtuelle, sélectionnez **Manage Activity Log Connection** (Gérer la connexion du journal d’activité). Cette tâche ouvre la page **Journal des activités Azure**. Sélectionnez **Connexion** pour connecter le suivi des modifications au journal des activités Azure pour votre machine virtuelle.

Une fois ce paramètre activé, accédez à la page **Vue d’ensemble** de votre machine virtuelle, puis sélectionnez **Arrêter** pour l’arrêter. Lorsque vous y êtes invité, sélectionnez **Oui** pour arrêter la machine virtuelle. Celle-ci étant libérée, sélectionnez **Démarrer** pour redémarrer votre machine virtuelle.

Le fait d’arrêter et de démarrer une machine virtuelle enregistre un événement dans son journal d’activité. Revenez à la page **Suivi des modifications**. Sélectionnez l’onglet **Événements** au bas de la page. Après un certain temps, les événements apparaissent dans le graphique et le tableau. Chaque événement peut être sélectionné pour en afficher le détail.

![Afficher les modifications dans le journal d’activité](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Le graphique affiche les modifications qui se sont produites au fil du temps. Après avoir ajouté une connexion au journal d’activité, le graphique linéaire dans la partie supérieure affiche les événements du journal des activités Azure. Chacune des lignes des graphiques à barres représente un autre type de modification dont il est possible d’effectuer le suivi. Ces types sont les démons Linux, les fichiers, les clés de Registre Windows, les logiciels et les services Windows. L’onglet Modification présente le détail des modifications indiquées dans la visualisation, par ordre décroissant, c’est-à-dire le plus récent en premier.

## <a name="advanced-monitoring"></a>Surveillance avancée

Vous pouvez effectuer un suivi plus avancé de votre machine virtuelle à l’aide de solutions telles que Update Management et Change and Inventory fournies par Azure Automation. [Operations Management Suite](../../automation/automation-intro.md).

Lorsque vous avez accès à l’espace de travail Log Analytics, vous pouvez trouver la clé de l’espace de travail et l’identificateur de l’espace de travail en sélectionnant **Paramètres avancés** sous **PARAMÈTRES**. Utilisez la commande [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour ajouter l’extension de l’agent Microsoft Monitoring à la machine virtuelle. Mettez à jour les valeurs des variables dans l’exemple ci-dessous de manière à refléter la clé et l’ID de l’espace de travail Log Analytics.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location eastus
```

Après quelques minutes, la nouvelle machine virtuelle s’affiche dans l’espace de travail Log Anaytics.

![Panneau OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré et examiné des machines virtuelles avec Azure Security Center. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer un groupe de ressources et une machine virtuelle
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Installer l’extension de diagnostics
> * Afficher les métriques de la machine virtuelle
> * Créer une alerte
> * Gérer les mises à jour Windows
> * Configurer la surveillance avancée

Passez au didacticiel suivant pour découvrir plus d’informations sur Azure Security Center.

> [!div class="nextstepaction"]
> [Gérer la sécurité des machines virtuelles](./tutorial-azure-security.md)