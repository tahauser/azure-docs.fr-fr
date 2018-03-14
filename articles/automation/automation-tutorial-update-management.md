---
title: "Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de l’utilisation d’Azure Automation - Gestion des mises à jour pour gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure."
services: automation
documentationcenter: 
author: zjalexander
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 614b5bd7a2663c3b61f511dcc6b6a49218ac439a
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Gérer les mises à jour Windows avec Azure Automation

La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles.
Dans ce didacticiel, vous allez découvrir comment évaluer rapidement l’état des mises à jour disponibles, planifier l’installation des mises à jour nécessaires et passer en revue les résultats des déploiements pour vérifier que les mises à jour sont appliquées correctement.

Pour plus d’informations sur les prix, consultez [Tarification Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour la gestion des mises à jour
> * Afficher une évaluation des mises à jour
> * Planifier un déploiement de mises à jour
> * Afficher les résultats d’un déploiement

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d’action, ainsi que la tâche d’observateur.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="enable-update-management"></a>Activer la gestion des mises à jour

Pour ce didacticiel, vous devez d’abord activer la gestion des mises à jour pour votre machine virtuelle. Si vous avez déjà activé une autre solution d’automatisation pour une machine virtuelle, cette étape n’est pas nécessaire.

1. Dans le menu de gauche, sélectionnez **Machines virtuelles**, puis choisissez une machine virtuelle dans la liste
2. Dans le menu de gauche, sous la section **Opérations**, cliquez sur **Gestion des mises à jour**. La page **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle.
La validation inclut la vérification de l’existence d’un espace de travail Log Analytics et d’un compte Automation lié, et si la solution est dans l’espace de travail.

Un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour.
L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.
Pour effectuer une action supplémentaire sur les machines virtuelles qui nécessitent des mises à jour, Azure Automation vous permet d’exécuter des runbooks sur les machines virtuelles, par exemple télécharger et appliquer des mises à jour.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un runbook Worker hybride Automation.
Cet agent est utilisé pour communiquer avec la machine virtuelle et pour obtenir des informations sur l’état des mises à jour.

Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

Si l’intégration n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automation](./automation-offering-get-started.md)
* Un [worker runbook hybride](./automation-hybrid-runbook-worker.md) est activé sur la machine virtuelle

L’écran **Gestion des mises à jour** s’ouvre. Configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

![Fenêtre Activer la solution de gestion des mises à jour](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

L’activation de la solution peut prendre jusqu’à 15 minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur.
Une fois la solution activée, des informations sur les mises à jour manquantes sur la machine virtuelle sont envoyées à Log Analytics.
Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît.
Si des mises à jour sont manquantes, vous pouvez afficher une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

Sélectionnez le **LIEN INFORMATIONS** sur la mise à jour pour ouvrir l’article de la mise à jour dans une nouvelle fenêtre. Vous y trouverez des informations importantes sur la mise à jour.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Cliquez n’importe où sur la mise à jour pour ouvrir la fenêtre **Recherche dans les journaux** pour la mise à jour sélectionnée. La requête pour la recherche dans les journaux est prédéfinie pour cette mise à jour particulière. Vous pouvez modifier cette requête ou créer votre propre requête afin d’afficher des informations détaillées sur les mises à jour déployées ou manquantes dans votre environnement.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/logsearch.png)

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Vous savez maintenant que des mises à jour sont manquantes pour votre machine virtuelle. Pour installer les mises à jour, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions.
Vous pouvez choisir les types de mises à jour à inclure dans le déploiement.
Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

> [!WARNING]
> Lorsque les mises à jour nécessitent un redémarrage, la machine virtuelle est automatiquement redémarrée.

Planifiez un nouveau déploiement de mises à jour pour la machine virtuelle en revenant à **Gestion des mises à jour** et en sélectionnant **Planifier le déploiement de la mise à jour** en haut de l’écran.

Dans l’écran **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : spécifiez un nom unique pour le déploiement de mises à jour.
* **Classification de mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour incluait dans le déploiement. Pour ce didacticiel, conservez tous les types sélectionnés.

  Les types de classification sont les suivants :

  * Mises à jour critiques
  * Mises à jour de sécurité
  * Correctifs cumulatifs
  * Packs de fonctionnalités
  * Service Packs
  * Mises à jour de définitions
  * Outils
  * Mises à jour

* **Paramètres de planification** : définissez l’heure sur 5 minutes à l’avenir. Vous pouvez également accepter la valeur par défaut, qui est de 30 minutes après l’heure actuelle.
Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique.
Sélectionnez **Récurrent** sous **Récurrence**. Conservez la valeur par défaut de 1 jour, cliquez sur **OK**. Cela configure une planification récurrente.

* **Fenêtre de maintenance (en minutes)** : conservez cette valeur par défaut. Vous pouvez spécifier la période de temps pendant laquelle le déploiement des mises à jour doit se produire. Ce paramètre permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

![Écran Paramètres de planification des mises à jour](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer**. Vous revenez au tableau de bord d’état. Sélectionnez **Déploiements des mises à jour planifiés** pour afficher la planification de déploiement que vous avez créée.

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans l’écran **Gestion des mises à jour**.
L’état est **En cours d’exécution** lorsqu’il est en cours d’exécution.
Une fois le déploiement terminé, s’il est réussi, l’état passe à **Réussi**.
En cas d’échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.
Cliquez sur le déploiement des mises à jour terminé pour voir le tableau de bord dédié à ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Dans la vignette **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur la machine virtuelle.
Le tableau de droite montre une répartition détaillée de chaque mise à jour et les résultats de l’installation.
La liste suivante présente les valeurs disponibles :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussi** : la mise à jour a réussi
* **Échec** : la mise à jour a échoué

Cliquez sur **Tous les journaux** pour voir toutes les entrées de journal créées par le déploiement.

Cliquez sur la vignette **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Cliquez sur **Erreurs** pour afficher les informations détaillées sur les erreurs du déploiement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour la gestion des mises à jour
> * Afficher une évaluation des mises à jour
> * Planifier un déploiement de mises à jour
> * Afficher les résultats d’un déploiement

Passez à la vue d’ensemble de la solution de gestion des mises à jour.

> [!div class="nextstepaction"]
> [Solution de gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
