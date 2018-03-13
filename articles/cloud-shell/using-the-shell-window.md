---
title: "Utilisation de la fenêtre Azure Cloud Shell | Microsoft Docs"
description: "Vue d’ensemble de l’utilisation de la fenêtre Azure Cloud Shell."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 43da2bf5b66ff7db03a6fb5c2e1ceaebe322bcbb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="using-the-azure-cloud-shell-window"></a>Utilisation de la fenêtre Azure Cloud Shell

Ce document explique comment utiliser la fenêtre Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Basculer entre les environnements Bash et PowerShell
![](media/using-the-shell-window/env-selector.png)

Utilisez le sélecteur d’environnement de la barre d’outils Cloud Shell pour basculer de Bash à PowerShell.

## <a name="restart-cloud-shell"></a>Redémarrer Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Le redémarrage de Cloud Shell réinitialise l’état de la machine et tous les fichiers non conservés par votre partage de fichiers Azure sont perdus.

* Cliquez sur l’icône de redémarrage de la barre d’outils Cloud Shell pour réinitialiser l’état de la machine.

## <a name="minimize--maximize-cloud-shell-window"></a>Réduire et agrandir la fenêtre Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Cliquez sur l’icône de réduction sur la partie supérieure droite de la fenêtre pour la masquer. Cliquez de nouveau sur l’icône Cloud Shell pour la réafficher.
* Cliquez sur l’icône d’agrandissement pour agrandir la fenêtre à sa hauteur maximale. Pour revenir à la taille de fenêtre précédente, cliquez sur Restaurer.

## <a name="concurrent-sessions"></a>Sessions simultanées
Cloud Shell permet plusieurs sessions simultanées dans les onglets du navigateur en permettant à chaque session d’exister sous la forme d’un processus Bash distinct.
Lorsque vous quittez une session, veillez à fermer chaque fenêtre de session dans la mesure où chaque processus s’exécute indépendamment sur la même machine.

## <a name="copy-and-paste"></a>Copier et coller
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionner la fenêtre Cloud Shell
* Cliquez et faites glisser le bord supérieur de la barre d’outils vers haut ou le bas pour redimensionner la fenêtre Cloud Shell.

## <a name="scrolling-text-display"></a>Défilement du texte
* Faites défiler le texte du terminal avec la souris ou le pavé tactile.

## <a name="changing-the-text-size"></a>Modification de la taille du texte
![](media/using-the-shell-window/text-size.png)
* Cliquez sur l’icône des paramètres en haut à gauche de la fenêtre, puis pointez sur l’option « Taille du texte » et sélectionnez la taille de texte voulue. Votre sélection sera conservée d’une session à l’autre.

## <a name="exit-command"></a>Commande Quitter
Cliquez sur `exit` pour mettre un terme à la session active. Ce comportement se produit par défaut au bout de 20 minutes en l’absence d’interaction.

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide de Bash dans Cloud Shell](quickstart.md)
[Démarrage rapide de PowerShell dans Cloud Shell](quickstart-powershell.md)