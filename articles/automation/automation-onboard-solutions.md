---
title: "Intégrer les solutions de mises à jour et Change Tracking pour Azure Automation | Microsoft Docs"
description: "En savoir plus sur l’intégration des solutions de mises à jour et Change Tracking pour Azure Automation."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2018
ms.author: eamono
ms.custom: mvc
ms.openlocfilehash: 4c97cda2f16c769d0510b6a661bd03b20f488b62
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Intégrer les solutions de mises à jour et Change Tracking pour Azure Automation

Dans ce didacticiel, vous découvrez comment intégrer automatiquement à Azure Automation des solutions de mise à jour, Change Tracking et d’inventaire pour des machines virtuelles :

> [!div class="checklist"]
> * Intégrer une machine virtuelle Azure
> * Activer des solutions
> * Installer et mettre à jour des modules
> * Importer le runbook d’intégration
> * Démarrer le runbook

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](automation-offering-get-started.md) pour gérer les machines.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="onboard-an-azure-vm"></a>Intégrer une machine virtuelle Azure

Vous pouvez intégrer des machines de plusieurs façons : vous pouvez intégrer la solution [à partir d’une machine virtuelle](automation-onboard-solutions-from-vm.md), [à partir de votre compte Automation](automation-onboard-solutions-from-automation-account.md) ou via un runbook. Ce didacticiel vous guide dans l’activation de la solution Update Management via un runbook. Pour intégrer à grande échelle Azure Virtual Machines, une machine virtuelle existante doit être intégrée à la solution Change Tracking ou Update Management. Dans cette étape, vous intégrez une machine virtuelle aux solutions Change Tracking et Update Management.

### <a name="enable-change-tracking-and-inventory"></a>Activer Change Tracking et Inventory

La solution Change Tracking et Inventory fournit la possibilité de [suivre les changements](automation-vm-change-tracking.md) et de [faire l’inventaire](automation-vm-inventory.md) de vos machines virtuelles. Dans cette étape, vous activez la solution sur une machine virtuelle.

1. Dans le menu de gauche, sélectionnez **Comptes Automation**, puis sélectionnez votre compte Automation dans la liste.
1. Sélectionnez **Inventory** sous **GESTION DE LA CONFIGURATION**.
1. Sélectionnez un espace de travail Log Analytics existant ou créez-en un. Cliquez sur le bouton **Activer**.

![Intégrer la solution Update](media/automation-onboard-solutions/inventory-onboard.png)

Quand vous recevez la notification d’intégration de la solution Change Tracking et Inventory, cliquez sur **Update Management** sous **GESTION DE LA CONFIGURATION**.

### <a name="enable-update-management"></a>Activer Update Management

La solution Update Management vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles Microsoft Azure. Vous pouvez évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour obligatoires et examiner les résultats des déploiements pour vérifier que les mises à jour ont été appliquées à la machine virtuelle. Dans cette étape, vous activez la solution pour votre machine virtuelle.

1. Dans votre compte Automation, sélectionnez **Update Management** sous **GESTION DES MISES À JOUR**.
1. L’espace de travail Log Analytics sélectionné est le même que celui utilisé à l’étape précédente. Cliquez sur **Activer** pour intégrer la solution Update Management.

![Intégrer la solution Update](media/automation-onboard-solutions/update-onboard.png)

Pendant l’installation de la solution Update Management, une bannière bleue s’affiche. Quand la solution est activée, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION** pour accéder à l’étape suivante.

### <a name="select-azure-vm-to-be-managed"></a>Sélectionnez la machine virtuelle Azure à gérer

Maintenant que les solutions sont activées, vous pouvez ajouter une machine virtuelle Azure à intégrer à ces solutions.

1. Dans votre compte Automation, dans la page **Change Tracking**, sélectionnez **+ Ajouter une machine virtuelle Azure** pour ajouter votre machine virtuelle.

1. Sélectionnez votre machine virtuelle dans la liste et sélectionnez **Activer**. Cette action active la solution Change Tracking et Inventory pour la machine virtuelle.

   ![Activer la solution Update pour la machine virtuelle](media/automation-onboard-solutions/enable-change-tracking.png)

1. Quand vous recevez la notification d’intégration de la machine virtuelle, dans votre compte Automation, sélectionnez **Update Management** sous **GESTION DES MISES À JOUR**.

1. Sélectionnez **+ Ajouter une machine virtuelle Azure** pour ajouter votre machine virtuelle.

1. Sélectionnez votre machine virtuelle dans la liste et sélectionnez **Activer**. Cette action active la solution Update Management pour la machine virtuelle.

   ![Activer la solution Update pour la machine virtuelle](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Si vous n’attendez pas la fin de l’autre solution, quand vous activez la solution suivante, vous recevez un message indiquant : *Une autre solution est en cours d’installation sur cette machine virtuelle ou sur une autre. Au terme de l’opération, le bouton Activer est disponible et vous pouvez demander l’installation de la solution sur cette machine virtuelle.*

## <a name="install-and-update-modules"></a>Installer et mettre à jour les modules

Vous devez effectuer la mise à jour vers les derniers modules Azure et importer `AzureRM.OperationalInsights` pour pouvoir automatiser l’intégration de la solution.

## <a name="update-azure-modules"></a>Mettre à jour les modules Azure

Dans votre compte Automation, sélectionnez **Modules** sous **RESSOURCES PARTAGÉES**. Sélectionnez **Mettre à jour les modules Azure** pour mettre à jour les modules Azure vers la dernière version. Sélectionnez **Oui** à l’invite pour mettre à jour tous les modules Azure existants vers la dernière version.

![Mettre à jour les modules](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Installer le module AzureRM.OperationalInsights

Dans la page **Modules**, sélectionnez **Parcourir la galerie** pour ouvrir la galerie des modules. Recherchez AzureRM.OperationalInsights et importez ce module dans le compte Automation.

![Importer le module OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importer le runbook d’intégration

1. Dans votre compte Automation, sélectionnez **Runbooks** sous **AUTOMATISATION DES PROCESSUS**.
1. Sélectionnez **Parcourir la galerie**.
1. Recherchez **Update et Change Tracking**, cliquez sur le runbook et sélectionnez **Importer** dans la page **Afficher la source**. Sélectionnez **OK** pour importer le runbook dans le compte Automation.

  ![Importer le runbook d’intégration](media/automation-onboard-solutions/import-from-gallery.png)

1. Dans la page **Runbook**, sélectionnez **Modifier**, puis **Publier**. Dans la boîte de dialogue **Publier le runbook**, sélectionnez **Oui** pour le publier.

## <a name="start-the-runbook"></a>Démarrer le runbook

Vous devez avoir intégré la solution de mise à jour ou Change Tracking à une machine virtuelle Azure pour pouvoir démarrer ce runbook. Il nécessite une machine virtuelle et un groupe de ressources existants avec la solution embarquée pour les paramètres.

1. Ouvrez le runbook Enable-MultipleSolution.

   ![Runbooks avec plusieurs solutions](media/automation-onboard-solutions/runbook-overview.png)

1. Cliquez sur le bouton démarrer et saisissez les valeurs suivantes pour les paramètres.

   * **VMNAME** : Laissez vide. Le nom d’une machine virtuelle existante à intégrer dans la solution Change Tracking ou de mise à jour. Laissez cet espace vide pour intégrer toutes les machines virtuelles du groupe de ressources.
   * **VMRESOURCEGROUP** : Nom du groupe de ressources des machines virtuelles à intégrer.
   * **SUBSCRIPTIONID** : Laissez vide. ID d’abonnement de la nouvelle machine virtuelle à intégrer. Laissez cet espace vide pour utiliser l’abonnement de l’espace de travail. Lorsqu’un ID d’abonnement différent est fourni, le compte RunAs pour ce compte Automation devrait également être ajouté comme contributeur pour cet abonnement.
   * **ALREADYONBOARDEDVM** : Nom de la machine virtuelle manuellement intégrée à la solution Updates ou Change Tracking.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** : Nom du groupe de ressources auquel appartient la machine virtuelle.
   * **SOLUTIONTYPE** : Entrez **Updates** ou **Change Tracking**

   ![Paramètres du runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Sélectionnez **OK** pour démarrer le travail du runbook.
1. Surveillez la progression et la présence d’erreurs depuis la page de travail du runbook.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Intégrez manuellement une machine virtuelle Azure.
> * Installez et mettez à jour les modules Azure requis.
> * Importez un runbook qui intègre des machines virtuelles Azure.
> * Démarrez le runbook qui intègre automatiquement des machines virtuelles Azure.

Suivez ce lien pour en savoir plus sur la planification des runbooks.

> [!div class="nextstepaction"]
> [Planification des runbooks](automation-schedules.md).
