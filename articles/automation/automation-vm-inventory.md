---
title: "Gérer une machine virtuelle Azure avec la collection d’inventaire | Microsoft Docs"
description: "Gérer une machine virtuelle avec la collection d’inventaire"
services: automation
keywords: inventaire, automatisation, suivi des modifications
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7b0e39e98a81231b68414f36ac5c1fc0897304a1
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gérer une machine virtuelle Azure avec la collecte d’inventaire

Vous pouvez activer le suivi d’inventaire pour une machine virtuelle Azure à partir de la page de ressources de cette machine. Cette méthode fournit une interface utilisateur basée sur un navigateur pour la configuration de la collecte d’inventaire.

## <a name="before-you-begin"></a>Avant de commencer
Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).
Si vous n’avez pas de machine virtuelle Azure, [créez-en une](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Activer la collecte d’inventaire à partir de la page de ressources de la machine virtuelle

1. Dans le volet de gauche du portail Azure, sélectionnez **Machines virtuelles**.
2. Dans la liste des machines virtuelles, sélectionnez-en une.
3. Dans le menu **Ressource**, sous **Opérations**, sélectionnez **Inventory (préversion)**.  
    Une bannière apparaît en haut de la fenêtre, afin de vous informer que la solution n’est pas activée. 
4. Pour activer la solution, cliquez sur la bannière.
5. Sélectionnez un espace de travail Log Analytics pour stocker vos journaux de données.  
    Si aucun espace de travail n’est disponible pour cette région, vous êtes invité à créer un compte Automation et un espace de travail par défaut. 
6. Cliquez sur **Activer** pour démarrer l’intégration de votre ordinateur.

   ![Afficher les options d’intégration](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Une barre d’état vous informe que la solution est en cours d’activation. Ce processus peut prendre jusqu’à 15 minutes. Pendant ce temps, vous pouvez fermer la fenêtre ou la laisser ouverte, afin qu’elle vous tienne informé de l’activation de la solution. Vous pouvez surveiller l’état du déploiement dans le volet des notifications.

   ![Afficher la solution d’inventaire immédiatement après l’intégration](./media/automation-vm-inventory/inventory-onboarded.png)

Une fois le déploiement terminé, la barre d’état disparaît. Le système est toujours en cours de collection des données d’inventaire ; ces données peuvent ne pas être encore visibles. Une collecte complète des données peut prendre 24 heures.

## <a name="configure-your-inventory-settings"></a>Configurer les paramètres d’inventaire

Par défaut, les logiciels, les services Windows et les démons Linux sont configurés pour la collection. Pour collecter l’inventaire des fichiers et registres Windows, configurez les paramètres de collection d’inventaire.

1. Dans la vue **Inventory (préversion)**, sélectionnez le bouton **Modifier les paramètres** situé en haut de la fenêtre.
2. Pour ajouter un nouveau paramètre de collection, accédez à la catégorie de paramètres que vous souhaitez ajouter en sélectionnant les onglets **Registre Windows**, **Fichiers Windows** et **Fichiers Linux**. 
3. Sélectionnez **Ajouter** sur la partie supérieure de la fenêtre.
4. Pour afficher les détails et les descriptions de chaque propriété de paramètre, consultez la [page de documentation Inventory](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>Déconnecter la machine virtuelle de la fonction de gestion

Pour supprimer votre machine virtuelle de la gestion de l’inventaire, procédez comme suit :

1. Dans le volet de gauche du portail Azure, cliquez sur **Log Analytics**, puis sélectionnez l’espace de travail que vous avez utilisé lors de l’intégration de votre machine virtuelle.
2. Dans la fenêtre **Log Analytics**, dans le menu **Ressource**, sous la catégorie **Sources de données de l’espace de travail**, sélectionnez **Machines virtuelles**. 
3. Dans la liste, sélectionnez la machine virtuelle que vous souhaitez déconnecter. Une coche verte s’affiche en regard du texte indiquant **Cet espace de travail** dans la colonne **Connexion OMS**, sur la machine virtuelle. 
4. Sur la partie supérieure de la page suivante, sélectionnez **Déconnecter**.
5. Dans la fenêtre de confirmation, cliquez sur **Oui**.  
    Cette action déconnecte la machine de la fonction de gestion.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la gestion des modifications dans les paramètres de registre et les fichiers sur vos machines virtuelles, consultez l’article relatif au [suivi des modifications apportées à votre environnement logiciel avec la solution Change Tracking](../log-analytics/log-analytics-change-tracking.md).
* Pour en savoir plus sur la gestion des mises à jour de package et Windows sur vos machines virtuelles, consultez la section relative à la [solution Update Management dans OMS](../operations-management-suite/oms-solution-update-management.md).
