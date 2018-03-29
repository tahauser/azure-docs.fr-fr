---
title: Surveiller et mettre à jour des machines virtuelles Linux dans Azure | Microsoft Docs
description: Découvrez comment surveiller les diagnostics de démarrage et les métriques de performances, et gérer les mises à jour de package sur une machine virtuelle Linux dans Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9ffd36da535a2e5ac4a355f429394dc4209348b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Comment surveiller et mettre à jour une machine virtuelle Linux dans Azure

Pour vérifier que vos machines virtuelles dans Azure fonctionnent correctement, vous pouvez consulter les diagnostics de démarrage et les métriques de performances, et gérer les mises à jour de package. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Activer l’extension Diagnostics sur la machine virtuelle
> * Afficher les métriques de la machine virtuelle
> * Créer des alertes en fonction des métriques des diagnostics
> * Gérer les mises à jour de package
> * Configurer la surveillance avancée


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Créer une machine virtuelle

Pour voir les diagnostics et les métriques en action, vous avez besoin d’une machine virtuelle. Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupMonitor* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Créez maintenant une machine virtuelle avec la commande [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Activer les diagnostics de démarrage

Au démarrage des machines virtuelles Linux, l’extension Diagnostics de démarrage capture la sortie du démarrage et la stocke dans le stockage Azure. Ces données peuvent être utilisées pour résoudre les problèmes de démarrage des machines virtuelles. Les diagnostics de démarrage ne sont pas activés automatiquement quand vous créez une machine virtuelle Linux à l’aide d’Azure CLI.

Avant d’activer les diagnostics de démarrage, vous devez créer un compte de stockage pour stocker les journaux de démarrage. Les comptes de stockage doivent avoir un nom global unique, comprenant entre 3 et 24 caractères, et contenant seulement des chiffres et des lettres minuscules. Créez un compte de stockage avec la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). Dans cet exemple, une chaîne aléatoire est utilisée pour créer un nom de compte de stockage unique. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Lors de l’activation des diagnostics de démarrage, l’URI vers le conteneur de stockage d’objets blob est nécessaire. La commande suivante interroge le compte de stockage et retourne cet URI. La valeur de l’URI est stockée dans une variable nommée *bloburi*, qui est utilisé à l’étape suivante.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Activez maintenant les diagnostics de démarrage avec la commande [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). La valeur de `--storage` est l’URI de l’objet blob collecté à l’étape précédente.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Afficher les diagnostics de démarrage

Quand les diagnostics de démarrage sont activés, chaque fois que vous arrêtez et que vous démarrez la machine virtuelle, des informations sur le processus de démarrage sont écrites dans un fichier journal. Pour cet exemple, désallouez d’abord la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#az_vm_deallocate) comme suit :

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Démarrez maintenant la machine virtuelle avec la commande [az vm start]( /cli/azure/vm#az_vm_stop) comme suit :

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Vous pouvez obtenir les données des diagnostics de démarrage pour *myVM* avec la commande [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) comme suit :

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Afficher les métriques de l’hôte

Une machine virtuelle Linux a un hôte dédié dans Azure qui interagit avec elle. Les métriques sont automatiquement collectées pour l’hôte et peuvent être visualisées dans le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroupMonitor** puis sélectionnez **myVM** dans la liste des ressources.
1. Pour voir comment la machine virtuelle hôte fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle, puis sélectionnez une des métriques de *[hôte]* sous **Métriques disponibles**.

    ![Afficher les métriques de l’hôte](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Installer l’extension Diagnostics

> [!IMPORTANT]
> Ce document décrit la version 2.3 de l’extension de diagnostic Linux, qui est déconseillée. La version 2.3 sera prise en charge jusqu’au 30 juin 2018.
>
> La version 3.0 de l’extension de diagnostic Linux peut être activée à la place. Pour plus d’informations, consultez [la documentation](./diagnostic-extension.md).

Les métriques de base de l’hôte sont disponibles, mais pour voir des métriques plus précises et spécifiques à la machine virtuelle, vous devez installer l’extension Diagnostics Azure sur la machine virtuelle. L’extension Diagnostics Azure permet de récupérer des données supplémentaires de surveillance et de diagnostic auprès de la machine virtuelle. Vous pouvez voir ces métriques de performances et créer des alertes basées sur le fonctionnement de la machine virtuelle. L’extension Diagnostics est installée via le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
1. Cliquez sur **Paramètres de diagnostic**. La liste montre que les *Diagnostics de démarrage* sont déjà activés depuis la section précédente. Cliquez sur la case à cocher *Métriques de base*.
1. Dans la section *Compte de stockage*, recherchez et sélectionnez le compte *mydiagdata[1234]* créé dans la section précédente.
1. Cliquez sur le bouton **Enregistrer** .

    ![Afficher les métriques de diagnostic](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Afficher les métriques de la machine virtuelle

Vous pouvez afficher les métriques de la machine virtuelle de la même façon que vous avez affiché le mesures de la machine virtuelle hôte :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
1. Pour voir comment la machine virtuelle fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle puis sélectionnez une des métriques de diagnostic sous **Métriques disponibles**.

    ![Afficher les métriques de la machine virtuelle](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>Créez des alertes

Vous pouvez créer des alertes en fonction de métriques de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. Les alertes sont affichées dans le portail Azure ou peuvent être envoyées par e-mail. Vous pouvez également déclencher des runbooks Azure Automation ou Azure Logic Apps en réponse aux alertes générées.

L’exemple suivant crée une alerte pour l’utilisation moyenne de l’UC.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Cliquez sur **Règles d’alerte** dans le panneau de la machine virtuelle puis cliquez sur **Ajouter une alerte Métrique** dans la partie supérieure du panneau des alertes.
4. Spécifiez un **Nom** pour votre alerte, comme *myAlertRule*
5. Pour déclencher une alerte quand le pourcentage d’UC dépasse 1,0 pendant cinq minutes, laissez toutes les autres valeurs par défaut sélectionnées.
6. Si vous le souhaitez, cochez la case *Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs* pour envoyer la notification par e-mail. L’action par défaut est de présenter une notification dans le portail.
7. Cliquez sur le bouton **OK**.

## <a name="manage-package-updates"></a>Gérer les mises à jour de package

Avec la gestion des mises à jour, vous pouvez gérer les mises à jour et les correctifs de package pour vos machines virtuelles Linux Azure. Directement à partir de votre machine virtuelle, vous pouvez rapidement évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour nécessaires et passer en revue les résultats des déploiements pour vérifier que les mises à jour ont été appliquées correctement à la machine virtuelle.

Pour plus d’informations sur les prix, consultez [Tarification Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>Activer la gestion des mises à jour (préversion)

Activer la gestion des mises à jour pour votre machine virtuelle

1. Sur le côté gauche de l’écran, sélectionnez **Machines virtuelles**.
1. Sélectionnez une machine virtuelle dans la liste.
1. Dans l’écran de la machine virtuelle, dans la section **Opérations**, cliquez sur **Gestion des mises à jour**. L’écran **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle. La validation inclut la vérification de l’existence d’un espace de travail Log Analytics et d’un compte Automation lié, et si la solution est dans l’espace de travail.

Un espace de travail Log Analytics est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour. L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources. Pour effectuer une action supplémentaire sur les machines virtuelles qui nécessitent des mises à jour, Azure Automation vous permet d’exécuter des scripts sur les machines virtuelles, par exemple pour télécharger et appliquer des mises à jour.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un worker hybride. Cet agent est utilisé pour communiquer avec la machine virtuelle et pour obtenir des informations sur l’état des mises à jour. 

Si ces prérequis ne sont pas satisfaits, une bannière apparaît et vous donne la possibilité d’activer la solution.

![Bannière de configuration intégrée de la gestion des mises à jour](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Cliquez sur la bannière pour activer la solution. Si un des prérequis suivants est trouvé non satisfait après la validation, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* Un [worker runbook hybride](../../automation/automation-hybrid-runbook-worker.md) est activé sur la machine virtuelle

L’écran **Activer la gestion des mises à jour** s’ouvre. Configurez les paramètres, puis cliquez sur **Activer**.

![Activer la solution de gestion des mises à jour](./media/tutorial-monitoring/manage-updates-update-enable.png)

L’activation de la solution peut prendre jusqu’à 15 minutes : pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur. Une fois la solution activée, des informations sur les mises à jour manquantes provenant du gestionnaire de package sur la machine virtuelle sont envoyées à Log Analytics.
Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

### <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la solution de **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît. Vous pouvez voir une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

![Afficher l’état des mises à jour](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Pour installer les mises à jour, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions.

Planifier un nouveau déploiement de mises à jour pour la machine virtuelle en cliquant sur **Planifier le déploiement de la mise à jour** en haut de l’écran **Gestion des mises à jour**. Dans l’écran **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : spécifiez un nom unique pour identifier le déploiement de mises à jour.
* **Mises à jour à exclure** : sélectionnez cette option pour entrer les noms des packages à exclure de la mise à jour.
* **Paramètres de planification** : vous pouvez accepter la date et l’heure par défaut, qui est de 30 minutes après l’heure actuelle, ou spécifier un moment différent. Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique. Cliquez sur l’option Périodique sous Périodicité pour définir une planification périodique.

  ![Écran Paramètres de planification des mises à jour](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Fenêtre de maintenance (en minutes)** : spécifiez la période de temps pendant laquelle le déploiement des mises à jour doit se produire.  Cela permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies. 

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer** ; vous revenez ensuite au tableau de bord des états.
Notez que le tableau **Planifié** montre la planification de déploiement que vous avez créée.

> [!WARNING]
> La machine virtuelle redémarre automatiquement après l’installation des mises à jour s’il reste suffisamment de temps dans la fenêtre de maintenance.

La gestion des mises à jour utilise le gestionnaire de package existant sur votre machine virtuelle pour installer les packages.

### <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié est démarré, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans l’écran **Gestion des mises à jour**.
S’il est en cours d’exécution, son état est **En cours d’exécution**. Une fois le déploiement terminé, s’il est réussi, l’état passe à **Réussi**.
S’il existe un échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec**.
Cliquez sur le déploiement des mise à jour terminé pour voir le tableau de bord pour ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/tutorial-monitoring/manage-updates-view-results.png)

Dans la vignette **Résultats des mises à jour**, vous pouvez voir un récapitulatif du nombre total de mises à jour et les résultats du déploiement sur la machine virtuelle.
Dans le tableau de droite se trouve une répartition détaillée de chaque mise à jour et les résultats de l’installation, qui peut être une des valeurs suivantes :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussite** : la mise à jour a été correctement téléchargée et installée sur la machine virtuelle.
* **Échec** : le téléchargement ou l’installation de la mise à jour a échoué sur la machine virtuelle.

Cliquez sur **Tous les journaux** pour afficher toutes les entrées de journal créées par le déploiement.

Cliquez sur la vignette **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Cliquez sur **Erreurs** pour afficher les informations détaillées sur les erreurs du déploiement.

## <a name="advanced-monitoring"></a>Surveillance avancée 

Vous pouvez faire une surveillance plus avancée de votre machine virtuelle en utilisant [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Si vous ne l’avez pas déjà fait, vous pouvez vous inscrire pour une [version d’évaluation gratuite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) d’Operations Management Suite.

Quand vous avez accès au portail OMS, vous pouvez trouver la clé de l’espace de travail et l’identificateur de l’espace de travail dans le panneau Paramètres. Remplacez <workspace-key> et <workspace-id> par les valeurs correspondant à votre espace de travail OMS et utilisez ensuite **az vm extension set** pour ajouter l’extension OMS à la machine virtuelle :

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Dans le panneau Recherche dans les journaux du portail OMS, vous devez normalement voir *myVM*, comme illustré dans l’image suivante :

![Panneau OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré, examiné et géré les mises à jour pour une machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Activer l’extension Diagnostics sur la machine virtuelle
> * Afficher les métriques de la machine virtuelle
> * Créer des alertes en fonction des métriques des diagnostics
> * Gérer les mises à jour de package
> * Configurer la surveillance avancée

Passez au didacticiel suivant pour découvrir plus d’informations sur Azure Security Center.

> [!div class="nextstepaction"]
> [Gérer la sécurité des machines virtuelles](./tutorial-azure-security.md)
