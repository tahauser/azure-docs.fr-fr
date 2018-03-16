---
title: "Accès aux machines virtuelles Azure à partir de l’Explorateur de serveurs | Microsoft Docs"
description: "Obtenez une présentation de l’affichage, de la création et de la gestion des machines virtuelles Azure dans l’Explorateur de serveurs de Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/31/2017
ms.author: kraigb
ms.openlocfilehash: fbd11777c86a19d2b6b8e5125e467d2413b5d736
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Accès aux machines virtuelles Azure à partir de l’Explorateur de serveurs

Si vous avez des machines virtuelles hébergées par Azure, vous pouvez y accéder depuis l’Explorateur de serveurs. Vous devez d’abord vous connecter à votre abonnement Azure pour afficher vos services mobiles. Pour vous connecter, ouvrez le menu contextuel du nœud Azure dans l’Explorateur de serveurs, puis choisissez **Se connecter à Microsoft Azure**.

1. Dans Cloud Explorer, choisissez une machine virtuelle, puis appuyez sur la touche F4 pour afficher sa fenêtre de propriétés.

    Le tableau suivant indique les propriétés disponibles. Toutes les propriétés sont en lecture seule. Utilisez le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) pour les changer.

   | Propriété | Description |
   | --- | --- |
   | Nom DNS |URL comportant l’adresse Internet de la machine virtuelle. |
   | Environnement |Pour une machine virtuelle, la valeur de cette propriété est toujours Production. |
   | NOM |Nom de la machine virtuelle. |
   | Taille |Taille de la machine virtuelle, qui reflète la quantité de mémoire et d’espace disque disponibles. Pour plus d’informations, consultez [Tailles de machines virtuelles](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Statut |Les valeurs incluent : Démarrage en cours, Démarré, En cours d’arrêt, Arrêté et Extraction de l’état. Si Extraction de l’état s’affiche, l’état actuel est inconnu. Les valeurs de cette propriété ne sont pas les mêmes que celles qui sont utilisées dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | SubscriptionID |ID d’abonnement de votre compte Azure. Vous pouvez obtenir cette information sur le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) en affichant les propriétés de l’abonnement. |
2. Sélectionnez un nœud de point de terminaison, puis ouvrez la fenêtre **Propriétés** .
3. Le tableau suivant décrit les propriétés des points de terminaison disponibles. Toutes ces propriétés sont en lecture seule. Pour ajouter ou modifier les points de terminaison d’une machine virtuelle, utilisez le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Propriété | Description |
   | --- | --- |
   | NOM |Identificateur du point de terminaison. |
   | Port privé |Port d’accès réseau interne à votre application. |
   | Protocole |Protocole utilisé par la couche de transport du point de terminaison (TCP ou UDP). |
   | Port public |Port utilisé pour l’accès public à votre application. |
