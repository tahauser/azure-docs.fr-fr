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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Intégrer les solutions de mises à jour et Change Tracking pour Azure Automation

Dans ce didacticiel, vous découvrez comment intégrer automatiquement à Azure Automation des solutions de mise à jour, Change Tracking et d’inventaire pour des machines virtuelles :

> [!div class="checklist"]
> * Intégrez manuellement une machine virtuelle Azure.
> * Installez et mettez à jour les modules Azure requis.
> * Importez un runbook qui intègre des machines virtuelles Azure.
> * Démarrez le runbook qui intègre automatiquement des machines virtuelles Azure.

## <a name="prerequisites"></a>Composants requis

Pour compléter ce didacticiel, les éléments suivants sont requis.
+ Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Compte Automation](automation-offering-get-started.md) pour gérer les machines.

## <a name="onboard-an-azure-virtual-machine-manually"></a>Intégrer manuellement une machine virtuelle Azure

1.  Ouvrez le compte Automation.
2.  Cliquez sur la page Inventaire.
![Intégrer la solution d’inventaire](media/automation-onboard-solutions/inventory-onboard.png)
3.  Sélectionnez un espace de travail Log Analytics existant ou créez-en un. Cliquez sur le bouton Activer.
4.  Lorsque la notification d’intégration des solutions Change Tracking et d’inventaire est terminée, cliquez sur la page Update Management.
![Intégrer la solution de mise à jour](media/automation-onboard-solutions/update-onboard.png)
4.  Cliquez sur le bouton Activer pour intégrer la solution de mise à jour.
5.  Lorsque la notification d’intégration de la solution de mise à jour est terminée, cliquez sur la page Change Tracking.
![Intégrer Change Tracking](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Cliquez sur le bouton Ajouter une machine virtuelle Azure.
![Sélectionner la machine virtuelle pour Change Tracking](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Sélectionnez une machine virtuelle Azure et cliquez sur le bouton Activer pour intégrer la solution Change Tracking et d’inventaire.
8.  Lorsque la notification d’intégration de machine virtuelle est terminée, cliquez sur la page Update Management.
![Intégrer la machine virtuelle pour la gestion des mises à jour](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Cliquez sur le bouton Ajouter une machine virtuelle Azure.
![Sélectionner la machine virtuelle pour la gestion des mises à jour](media/automation-onboard-solutions/enable-update.png)
10.  Sélectionnez une machine virtuelle Azure et cliquez sur le bouton Activer pour intégrer la solution de gestion de mise à jour.

## <a name="install-and-update-required-azure-modules"></a>Installer et mettre à jour les modules Azure requis

Il est nécessaire d’effectuer la mise à jour avec les derniers modules Azure et d’importer AzureRM.OperationalInsights pour automatiser l’intégration de la solution avec succès.
1.  Cliquez sur la page Modules.
![Mettre à jour des modules](media/automation-onboard-solutions/update-modules.png)
2.  Cliquez sur le bouton Mettre à jour les modules Azure pour effectuer la mise à jour vers la dernière version. Attendez la fin des mises à jour.
3.  Cliquez sur le bouton Parcourir la galerie pour ouvrir la galerie des modules.
![Importer le module OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  Recherchez AzureRM.OperationalInsights et importez ce module dans le compte Automation.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Importer un runbook qui intègre des solutions aux machines virtuelles Azure

1.  Cliquez sur le page Runbooks dans la catégorie PROCESS AUTOMATION.
2.  Cliquez sur le bouton Parcourir la galerie.
3.  Recherchez « update and change tracking » et importez le runbook dans le compte Automation.
![Importer le runbook d’intégration](media/automation-onboard-solutions/import-from-gallery.png)
4.  Cliquez sur le bouton Modifier pour afficher la source du Runbook, puis sur le bouton Publier.
![Importer le runbook d’intégration](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Démarrer le runbook qui intègre automatiquement des machines virtuelles Azure

Vous devez avoir intégré la solution de mise à jour ou Change Tracking à une machine virtuelle Azure pour pouvoir démarrer ce runbook. Il nécessite une machine virtuelle et un groupe de ressources existants avec la solution embarquée pour les paramètres.
1.  Ouvrez le runbook Enable-MultipleSolution.
![Runbooks comportant plusieurs solutions](media/automation-onboard-solutions/runbook-overview.png)
2.  Cliquez sur le bouton démarrer et saisissez les valeurs suivantes pour les paramètres.
    *   VMNAME. Le nom d’une machine virtuelle existante à intégrer dans la solution Change Tracking ou de mise à jour. Laissez cet espace vide pour intégrer toutes les machines virtuelles du groupe de ressources.
    *   VMRESOURCEGROUP. Le nom du groupe de ressources dont la machine virtuelle est membre.
    *   SUBSCRIPTIONID. L’ID d’abonnement de la nouvelle machine virtuelle à intégrer. Laissez cet espace vide pour que l’abonnement de l’espace de travail soit utilisé. Lorsqu’un ID d’abonnement différent est fourni, le compte RunAs pour ce compte Automation devrait également être ajouté comme contributeur pour cet abonnement.
    *   ALREADYONBOARDEDVM. Le nom de la machine virtuelle manuellement intégrée à la solution de mises à jour ou Change Tracking.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. Le nom du groupe de ressources dont la machine virtuelle est membre.
    *   SOLUTIONTYPE. Saisissez « Updates » ou « Change Tracking »
    
    ![Paramètres de runbook comportant plusieurs solutions](media/automation-onboard-solutions/runbook-parameters.png)
3.  Cliquez sur OK pour démarrer le travail du runbook.
4.  Surveillez la progression et la présence d’erreurs depuis la page de travail du runbook.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Planification de runbooks](automation-schedules.md).