---
title: "Détecter les logiciels installés sur vos machines avec Azure Automation | Microsoft Docs"
description: "Utilisez l’inventaire pour connaître les logiciels installés sur les machines dans votre environnement."
services: automation
keywords: inventaire, automatisation, suivi des modifications
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bdd638d0612a8ddee1a0ddb4fd4579f8da14b887
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Détecter les logiciels installés sur vos machines Azure et non-Azure

Dans ce didacticiel, vous allez apprendre à détecter les logiciels installés dans votre environnement. Vous pouvez collecter et afficher l’inventaire des logiciels, fichiers, démons Linux, services Windows et des clés de registre Windows présents sur vos ordinateurs. Le suivi des configurations de vos machines peut vous aider à identifier les problèmes opérationnels au sein de votre environnement et à mieux comprendre l’état de vos machines.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour le suivi des modifications et l’inventaire
> * Afficher les logiciels installés
> * Rechercher les journaux d’inventaire des logiciels installés

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d’action, ainsi que la tâche d’observateur.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Activer le suivi des modifications et l’inventaire

Pour ce didacticiel, vous devez d’abord activer le suivi des modifications et l’inventaire pour votre machine virtuelle. Si vous avez déjà activé une autre solution d’automatisation pour une machine virtuelle, cette étape n’est pas nécessaire.

1. Dans le menu de gauche, sélectionnez **Machines virtuelles** et sélectionnez une machine virtuelle dans la liste.
2. Dans le menu de gauche, sous la section **Opérations**, cliquez sur **Inventaire**. La page **Activer le suivi des modifications et l’inventaire** s’ouvre.

Une validation est effectuée pour déterminer si l’inventaire est activé pour cette machine virtuelle.
La validation inclut la vérification de l’existence d’un espace de travail Log Analytics et d’un compte Automation lié, et si la solution est dans l’espace de travail.

Un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme l’inventaire.
L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un worker hybride.
Cet agent sert à communiquer avec la machine virtuelle et à obtenir des informations sur les logiciels installés.
Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un Automation hybrid runbook worker.

Si ces conditions préalables ne sont pas remplies, une bannière apparaît et vous donne la possibilité d’activer la solution.

![Bannière de configuration intégrée d’inventaire](./media/automation-tutorial-installed-software/enableinventory.png)

Pour activer la solution, cliquez sur la bannière.
Si la validation n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automation](./automation-offering-get-started.md)
* Un [worker runbook hybride](./automation-hybrid-runbook-worker.md) est activé sur la machine virtuelle

L’écran **Change Tracking and Inventory** (Suivi des modifications et inventaire) s’ouvre. Configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

![Fenêtre de la solution d’activation du suivi des modifications](./media/automation-tutorial-installed-software/installed-software-enable.png)

L’activation de la solution peut prendre jusqu'à 15 minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur.
Une fois la solution activée, des informations sur les logiciels installés et les modifications apportées à la machine virtuelle sont envoyées à Log Analytics.
Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

## <a name="view-installed-software"></a>Afficher les logiciels installés

Lorsque la solution de suivi des modifications et d’inventaire est activée, vous pouvez voir les résultats sur la page **Inventaire**.

À partir de votre machine virtuelle, sélectionnez **Inventaire** sous **OPÉRATIONS**.

Sur la page **Inventaire**, cliquez sur l’onglet **Logiciels**.

Dans l’onglet **Logiciels**, un tableau dresse la liste des logiciels détectés. Les logiciels sont regroupés par nom et par version.

Les informations détaillées sur chaque enregistrement de logiciel sont visibles dans le tableau. Ces détails comprennent le nom du logiciel, la version, l’éditeur, l’heure de la dernière actualisation (heure de l’actualisation la plus récente signalée par une machine du groupe) et les machines (nombre de machines possédant ce logiciel).

![Inventaires des logiciels](./media/automation-tutorial-installed-software/inventory-software.png)

Cliquez sur une ligne pour afficher les propriétés de l’enregistrement du logiciel et le nom des machines qui le possèdent.

Pour rechercher un logiciel ou un groupe de logiciels spécifique, vous pouvez faire une recherche dans la zone de texte située juste au-dessus de la liste des logiciels.
Le filtre vous permet de faire une recherche à partir du nom du logiciel, de la version ou de l’éditeur.

Par exemple, la recherche de « Contoso » renvoie tous les logiciels dont le nom, l’éditeur ou la version contient « Contoso ».

## <a name="search-inventory-logs-for-installed-software"></a>Rechercher les journaux d’inventaire des logiciels installés

L’inventaire génère des données de journal qui sont envoyées à Log Analytics. Pour rechercher les journaux en exécutant des requêtes, sélectionnez **Log Analytics** en haut de la fenêtre **Inventaire**.

Les données d’inventaire sont stockées sous le type **ConfigurationData**.
L’exemple suivant de requête Log Analytics retourne les éditeurs contenant « Microsoft » et le nombre d’enregistrements de logiciels (regroupés par SoftwareName et Computer) pour chaque éditeur.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Pour en savoir plus sur l’affichage et la recherche de fichiers journaux dans Log Analytics, consultez [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventaire d’une machine unique

Pour afficher l’inventaire des logiciels d’une seule machine, vous pouvez accéder à l’inventaire à partir de la page de ressources de la machine virtuelle Azure ou utiliser Log Analytics pour filtrer jusqu'à la machine correspondante. L’exemple suivant de requête Log Analytics retourne la liste des logiciels pour une machine nommée ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>étapes suivantes

Ce didacticiel vous a appris à afficher l’inventaire des logiciels et notamment comment :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour le suivi des modifications et l’inventaire
> * Afficher les logiciels installés
> * Rechercher les journaux d’inventaire des logiciels installés

Passez à la vue d’ensemble de la solution de suivi des modifications et d’inventaire pour en savoir plus.

> [!div class="nextstepaction"]
> [Solution de gestion des changements et d’inventaire](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)