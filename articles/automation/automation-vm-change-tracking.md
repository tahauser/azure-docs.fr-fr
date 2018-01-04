---
title: Suivi les modifications sur des machines virtuelles Azure | Microsoft Docs
description: Utilisez le suivi des modifications pour suivre les modifications dans les fichiers et le Registre des machines virtuelles.
services: automation
documentationcenter: automation
author: georgewallace
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: 3a661fada2e768c2206183c125593d019b557c1d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Suivi les modifications sur des machines virtuelles Azure

L’activation du suivi des modifications permet de suivre les modifications apportées aux fichiers et clés de Registre Windows des machines virtuelles. Identifier les modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Vous pouvez activer le suivi des modifications directement à partir de votre machine virtuelle Azure.

Si vous ne disposez pas d’une machine virtuelle Azure, vous pouvez en créer une en suivant les instructions du [guide de démarrage rapide Windows](../virtual-machines/windows/quick-create-portal.md) ou du [guide de démarrage rapide Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Activer le suivi des modifications pour une machine virtuelle Azure

1. Dans le volet de gauche du portail Azure, sélectionnez **Machines virtuelles**.
2. Dans la liste, sélectionnez une machine virtuelle.
3. Dans la fenêtre de la machine virtuelle, sous **Opérations**, sélectionnez **Suivi des modifications**. 

   ![Suivi des modifications sur une machine virtuelle intégrée](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    La fenêtre **Activer la gestion des mises à jour** s’ouvre.

    Une validation est effectuée pour déterminer si le suivi des modifications est activé pour cette machine virtuelle. S’il n’est pas activé, une bannière apparaît et vous donne la possibilité d’activer la solution.

   ![Bannière de configuration intégrée du suivi des modifications](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. Pour activer la solution, cliquez sur la bannière. Si vous ne disposez pas des éléments suivants, ils sont ajoutés automatiquement :

   * Espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md)
   * Compte [Automation](../automation/automation-offering-get-started.md)

5. Sélectionnez un espace de travail Log Analytics pour stocker les journaux de données de suivi des modifications et un compte Automation pour suivre les modifications, puis cliquez sur **Activer**.  
    Une barre d’état vous informe que la solution est en cours d’activation. Ce processus peut prendre jusqu’à 15 minutes.

## <a name="configure-change-tracking"></a>Configurer le suivi des modifications

Une fois activé le suivi des modifications, la fenêtre **Suivi des modifications** s’affiche. 

Pour choisir les fichiers et les clés de Registre à suivre, cliquez sur **Modifier les paramètres**.

   ![Modification des paramètres de suivi des modifications](./media/automation-vm-change-tracking/change-edit-settings.png)

   La fenêtre **Configuration de l’espace de travail** s’ouvre. 

Dans la fenêtre **Configuration de l’espace de travail**, ajoutez les clés de Registre Windows, puis les fichiers Windows ou Linux à suivre, comme décrit dans les trois sections suivantes.

### <a name="add-a-windows-registry-key"></a>Ajouter une clé de Registre Windows

1. Dans l’onglet **Registre Windows**, sélectionnez **Ajouter**.  
    La fenêtre **Ajouter le Registre Windows pour le suivi des modifications**.

   ![Ajout d’un Registre pour le suivi des modifications](./media/automation-vm-change-tracking/change-add-registry.png)

2. Sous **Activé**, sélectionnez **True**.
3. Dans la zone **Nom de l’élément**, entrez un nom convivial.
4. (Facultatif) Dans la zone **Groupe**, entrez un nom de groupe.
5. Dans la zone **Clé de Registre Windows**, entrez le nom de la clé de Registre que vous souhaitez suivre.
6. Sélectionnez **Enregistrer**.

### <a name="add-a-windows-file"></a>Ajouter un fichier Windows

1. Dans l’onglet **Fichiers Windows**, sélectionnez **Ajouter**.  
    La fenêtre **Ajouter le fichier Windows pour le suivi des modifications**.

   ![Ajout d’un fichier Windows pour le suivi des modifications](./media/automation-vm-change-tracking/change-add-win-file.png)

2. Sous **Activé**, sélectionnez **True**.
3. Dans la zone **Nom de l’élément**, entrez un nom convivial.
4. (Facultatif) Dans la zone **Groupe**, entrez un nom de groupe.
5. Dans la zone **Entrer le chemin**, entrez le chemin d’accès complet et le nom du fichier que vous souhaitez suivre.
6. Sélectionnez **Enregistrer**.

### <a name="add-a-linux-file"></a>Ajouter un fichier Linux

1. Dans l’onglet **Fichiers Linux**, sélectionnez **Ajouter**.  
    La fenêtre **Ajouter le fichier Linux pour le suivi des modifications**.

   ![Ajout d’un fichier Linux pour le suivi des modifications](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. Sous **Activé**, sélectionnez **True**.
3. Dans la zone **Nom de l’élément**, entrez un nom convivial.
4. (Facultatif) Dans la zone **Groupe**, entrez un nom de groupe.
5. Dans la zone **Entrer le chemin**, entrez le chemin d’accès complet et le nom du fichier que vous souhaitez suivre.
6. Dans la zone **Type de chemin**, sélectionnez **Fichier** ou **Répertoire**.
7. Sous **Récursivité**, sélectionnez **Activé** pour suivre les modifications apportées au chemin d’accès spécifié et à tous les fichiers et chemins d’accès qui se trouvent sous ce dernier. Pour suivre uniquement le chemin d’accès ou fichier sélectionné, sélectionnez **Désactivé**.
8. Sous **Utiliser sudo**, sélectionnez **Activé** pour suivre les fichiers qui nécessitent la commande `sudo` pour y accéder. Dans le cas contraire, sélectionnez **Désactivé**.
9. Sélectionnez **Enregistrer**.

## <a name="view-changes"></a>Afficher les modifications

La fenêtre **Suivi des modifications** affiche les modifications dans chacune des catégories de la machine virtuelle au fil du temps.

   ![Affichage du suivi des modifications](./media/automation-vm-change-tracking/change-view-changes.png)

Vous pouvez sélectionner les catégories et l’intervalle de temps des modifications à afficher. En haut de la fenêtre, vous pouvez voir une représentation graphique des modifications au fil du temps. En bas de la fenêtre s’affiche la liste des modifications récentes.

## <a name="view-change-tracking-log-data"></a>Afficher les données du journal de suivi des modifications

Le suivi des modifications génère des données de journal qui sont envoyées à Log Analytics. Pour rechercher les journaux en exécutant des requêtes, sélectionnez **Log Analytics** en haut de la fenêtre **Suivi des modifications**.

   ![Log Analytics pour le suivi des modifications](./media/automation-vm-change-tracking/change-log-analytics.png)

Pour en savoir plus sur l’affichage et la recherche de fichiers journaux dans Log Analytics, consultez [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>étapes suivantes

* Pour en savoir plus sur le suivi des modifications, consultez [Suivi des modifications apportées aux logiciels dans votre environnement grâce à la solution de suivi des modifications](../log-analytics/log-analytics-change-tracking.md).
* Pour en savoir plus sur la gestion des mises à jour pour les machines virtuelles, consultez [Solution de gestion des mises à jour dans OMS](../operations-management-suite/oms-solution-update-management.md).
