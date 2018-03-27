---
title: Gérer la capacité de mémoire physique pour Azure Stack | Microsoft Docs
description: Surveillez et gérez l’espace de stockage disponible pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/13/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: b922790d51c7028c37bb5863d43e99e19790488c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Gérer la capacité de mémoire physique pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Pour augmenter la capacité de mémoire totale disponible pour Azure Stack, vous pouvez ajouter plus de mémoire. Dans Azure Stack, votre serveur physique est également appelé un *nœud d’unité d’échelle*. Tous les nœuds d’unité d’échelle qui sont membres d’une seule unité d’échelle doivent avoir la même quantité de mémoire.

> [!note]  
> Avant de continuer, consultez la documentation de votre fabricant de matériel pour voir s’il prend en charge une mise à niveau de la mémoire physique. Votre contrat de support avec votre fabricant de matériel OEM peut exiger que le fabricant effectue le positionnement du rack de serveurs physiques et la mise à jour du microprogramme de l’appareil.

L’organigramme suivant illustre la procédure générale d’ajout de mémoire à chaque nœud d’unité d’échelle.

![Ajouter de la mémoire dans chaque nœud d’unité d’échelle](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Ajouter de la mémoire à un nœud existant
Les étapes suivantes fournissent une vue d’ensemble globale du processus d’ajout de mémoire. 

> [!Warning]  
Ne suivez pas ces étapes sans consulter la documentation fournie par votre fournisseur OEM.

> [!Warning]  
L’unité d’échelle doit être arrêtée dans son ensemble, car la mise à niveau propagée de la mémoire n’est pas prise en charge.

1. Arrêtez Azure Stack en suivant la procédure décrite dans l’article [Démarrer et arrêter Azure Stack](azure-stack-start-and-stop.md).
2. Mettez la mémoire à niveau sur chaque ordinateur physique à l’aide de la documentation de votre fabricant de matériel.
3. Démarrez Azure Stack en suivant la procédure décrite dans l’article [Démarrer et arrêter Azure Stack](azure-stack-start-and-stop.md).

## <a name="next-steps"></a>Étapes suivantes

 - Pour découvrir comment gérer les comptes de stockage dans Azure Stack pour rechercher, restaurer et récupérer de la capacité de stockage en fonction des besoins de l’entreprise, consultez [Gérer les comptes de stockage dans Azure Stack](azure-stack-manage-storage-accounts.md).
 - Pour découvrir comment l’opérateur de cloud d’Azure Stack surveille et gère la capacité de stockage de son déploiement Azure Stack, consultez [Gérer la capacité de stockage d’Azure Stack](azure-stack-manage-storage-shares.md). 