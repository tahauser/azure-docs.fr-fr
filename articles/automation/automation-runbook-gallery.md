---
title: Galeries de runbooks et de modules pour Azure Automation
description: Des runbooks et des modules de Microsoft et de la communauté sont disponibles pour l’installation et l’utilisation dans votre environnement Azure Automation.  Cet article décrit comment accéder à ces ressources et comment partager vos runbooks dans la galerie.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6a9298c0b7331bfa8af76eb904d256f6302816bf
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galeries de runbooks et de modules pour Azure Automation
Au lieu de créer vos propres runbooks et modules dans Azure Automation, vous pouvez accéder à un large éventail de scénarios déjà générés par Microsoft et la communauté.  Vous pouvez utiliser ces scénarios sans les modifier ou les utiliser comme point de départ, puis les modifier selon vos besoins spécifiques.

Vous pouvez obtenir des runbooks à partir de la [galerie de runbooks](#runbooks-in-runbook-gallery) et des modules à partir de la [PowerShell Gallery](#modules-in-powerShell-gallery).  Vous pouvez également contribuer à la communauté en partageant les scénarios que vous développez.

## <a name="runbooks-in-runbook-gallery"></a>Runbooks dans la galerie de runbooks
La [galerie de runbooks](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) fournit un éventail de runbooks de Microsoft et de la communauté que vous pouvez importer dans Azure Automation. Vous pouvez télécharger un runbook à partir de la galerie qui est hébergée dans le [Centre de scripts TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), ou vous pouvez importer directement les runbooks depuis la galerie dans le portail Azure.

Vous pouvez uniquement importer directement à partir de la galerie de runbooks à l’aide du portail Azure. Vous ne pouvez pas exécuter cette fonction à l’aide de Windows PowerShell.

> [!NOTE]
> Vous devez valider le contenu de tous les runbooks que vous obtenez de la galerie de runbooks et vous montrer très vigilant lors de leur installation et de leur exécution dans un environnement de production. |
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Pour importer un runbook à partir de la galerie de runbooks avec le portail Azure
1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sous **Automatisation de processus**, cliquez sur **Galerie de runbooks**
3. Recherchez l’élément de la galerie qui vous intéresse et sélectionnez-le pour afficher les informations le concernant. Sur la gauche, vous pouvez entrer des paramètres de recherche supplémentaires pour l’éditeur et le type.
   
    ![Parcourir la galerie](media/automation-runbook-gallery/browse-gallery.png)
5. Cliquez sur **Afficher le projet source** pour afficher l’élément dans le [Centre de scripts TechNet](http://gallery.technet.microsoft.com/).
6. Pour importer un élément, cliquez dessus pour afficher les informations le concernant, puis cliquez sur le bouton **Importer** .
   
    ![Bouton Importer](media/automation-runbook-gallery/gallery-item-detail.png)
7. Vous pouvez également modifier le nom du runbook, puis cliquer sur **OK** pour importer le runbook.
8. Ce runbook apparaît sur l’onglet **Runbooks** du compte Automation.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Ajout d’un runbook à la galerie de runbooks
Microsoft vous invite à ajouter à la galerie de runbooks des runbooks dont vous pensez qu’ils pourraient être utiles à d’autres utilisateurs.  Vous pouvez ajouter un runbook en [le téléchargeant vers le Centre de scripts](http://gallery.technet.microsoft.com/site/upload) en tenant compte des informations suivantes.

* Vous devez spécifier *Windows Azure* comme **Catégorie** et *Automation* comme **Sous-catégorie** pour le runbook à afficher dans l’Assistant.  
* Le téléchargement doit être un seul fichier .ps1 ou .graphrunbook.  Si le runbook nécessite des modules, des runbooks enfants ou des ressources, vous devez répertorier ceux figurant dans la description de l’envoi et dans la section commentaires du runbook.  Si vous disposez d’un scénario nécessitant plusieurs runbooks, téléchargez-les séparément et répertoriez les noms des runbooks associés dans chacune de leurs descriptions. Assurez-vous que vous utilisez les mêmes balises afin qu’elles s’affichent dans la même catégorie. Un utilisateur doit lire la description pour savoir que les autres runbooks sont requis pour que le scénario fonctionne.
* Ajoutez la balise « GraphicalPS » si vous publiez un **Runbook graphique** (et non un workflow graphique). 
* Insérer un extrait de code PowerShell ou PowerShell Workflow dans la description à l’aide de l’icône **Insérer la section de code** .
* Le résumé du chargement s’affiche dans les résultats de la galerie de runbooks. Vous devez donc fournir des informations détaillées qui aideront un utilisateur à identifier la fonctionnalité du runbook.
* Vous devez attribuer une à trois des balises suivantes pour le téléchargement.  Le runbook est répertorié dans l’Assistant sous les catégories qui correspondent à ses balises.  Toutes les balises ne figurant pas sur cette liste sont ignorées par l’Assistant. Si vous ne spécifiez pas les balises correspondantes, le runbook est répertorié dans la catégorie Autre.
  
  * Sauvegarde
  * Gestion de la capacité
  * Contrôle des modifications
  * Conformité
  * Environnements de développement / test
  * Récupération d’urgence
  * Surveillance
  * Application de correctifs
  * Approvisionnement
  * Correction
  * Gestion du cycle de vie des machines virtuelles
* Automation met à jour la galerie toutes les heures, donc vous ne verrez pas vos contributions immédiatement.

## <a name="modules-in-powershell-gallery"></a>Modules dans PowerShell Gallery
Les modules PowerShell contiennent des applets de commande que vous pouvez utiliser dans vos runbooks ; des modules existants que vous pouvez installer dans Azure Automation sont disponibles dans [PowerShell Gallery](http://www.powershellgallery.com).  Vous pouvez lancer cette galerie à partir du portail Azure et les installer directement dans Azure Automation, ou vous pouvez les télécharger et les installer manuellement.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Pour importer un module à partir d’Automation Module Gallery avec le portail Azure
1. Dans le portail Azure, ouvrez votre compte Automation.
2. Sélectionnez **Modules** sous **Ressources partagées** pour ouvrir la liste des modules.
4. Cliquez sur **Parcourir la galerie**, en haut de la page.
   
    ![Galerie du module](media/automation-runbook-gallery/modules-blade.png) <br>
5. Sur la page **Parcourir la galerie**, vous pouvez effectuer des recherches en fonction des champs suivants :
   
   * Nom du module
   * Tags
   * Auteur
   * Nom Applet de commande/Ressource DSC
6. Recherchez un module qui vous intéresse et sélectionnez-le pour en afficher les détails.  
   Lorsque vous explorez un module spécifique, vous pouvez afficher plus d’informations le concernant, y compris un lien vers PowerShell Gallery, les dépendances requises et toutes les applets de commande et/ou les ressources DSC que contient le module.
   
    ![Détails du module PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Pour installer le module directement dans Azure Automation, cliquez sur le bouton **Importer** .
8. Lorsque vous cliquez sur le bouton Importer du volet **Importer**, le nom du module que vous êtes sur le point d’importer s’affiche. Si toutes les dépendances sont installées, le bouton **OK** est activé. S'il manque des dépendances, vous devrez les importer avant de pouvoir importer ce module.
9. Dans la page **Importer**, cliquez sur **OK** pour importer le module. Quand il importe un module dans votre compte, le logiciel Azure Automation extrait les métadonnées sur le module et les cmdlets. Cette opération peut prendre quelques minutes, car chaque activité doit être extraite.
10. Vous recevez une première notification indiquant que le module est en cours de déploiement, puis une autre, signalant que le processus est terminé.
11. Une fois le module importé, les activités disponibles s’affichent ; vous pouvez en utiliser les ressources dans vos runbooks et la Configuration d’état souhaité.

## <a name="requesting-a-runbook-or-module"></a>Demande d’un runbook ou d’un module
Vous pouvez envoyer des demandes à [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Si vous avez besoin d’aide pour écrire un runbook ou si vous avez une question à propos de PowerShell, publiez une question sur notre [forum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Étapes suivantes
* Pour vous familiariser avec les runbooks, consultez [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md)
* Pour comprendre les différences entre PowerShell et un workflow PowerShell avec les Runbooks, consultez [Apprentissage du workflow PowerShell](automation-powershell-workflow.md)

