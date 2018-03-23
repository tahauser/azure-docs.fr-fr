---
title: Gestion des régions dans Azure Stack | Microsoft Docs
description: Vue d’ensemble de la gestion des régions dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0a19490ce276eec9a46bc0ea4343e3449a9dc93c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="region-management-in-azure-stack"></a>Gestion des régions dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack comprend le concept des régions, qui sont des entités logiques composées des ressources matérielles qui constituent l’infrastructure Azure Stack. Dans la gestion des régions se trouvent toutes les ressources nécessaires pour utiliser correctement le cycle de vie de l’infrastructure Azure Stack.

Un déploiement de système intégré (appelé *cloud Azure Stack*) constitue une région unique. Chaque Kit de développement Azure Stack a une région, nommée **local**. Si vous déployez un deuxième système intégré Azure Stack, ou si vous configurez une autre instance du Kit de développement sur un ordinateur distinct, ce cloud Azure Stack est une région différente.

## <a name="information-available-through-the-region-management-tile"></a>Informations disponibles par le biais de la vignette Gestion des régions
Azure Stack dispose d’un ensemble de fonctionnalités de gestion des régions disponibles dans la vignette **Gestion des régions**. Cette vignette est accessible à un opérateur Azure Stack sur le tableau de bord par défaut du portail administrateur. Elle vous permet de surveiller et de mettre à jour votre région Azure Stack et ses composants, qui sont propres à la région.

 ![Vignette de gestion des régions](media/azure-stack-manage-region/image1.png)

 Si vous cliquez sur une région dans la vignette Gestion des régions, vous pouvez accéder aux informations suivantes :

  ![Description des volets du panneau Gestion des régions](media/azure-stack-manage-region/image2.png)

1. **Le menu de ressources**. Ici, vous pouvez accéder à des zones de gestion de l’infrastructure spécifiques, ainsi qu’afficher et gérer des ressources utilisateur telles que des comptes de stockage et des réseaux virtuels.

2. **Alertes**. Cette vignette répertorie les alertes à l’échelle du système et fournit des détails sur chacune de ces alertes.

3. **Mises à jour**. Dans cette vignette, vous pouvez consulter la version actuelle de votre infrastructure Azure Stack, les mises à jour disponibles et l’historique des mises à jour. Vous pouvez également mettre à jour votre système intégré.

4. **Fournisseurs de ressources**. La zone Fournisseurs de ressources est l’endroit où gérer les fonctionnalités d’utilisateur offertes par les composants nécessaires pour exécuter Azure Stack. Chaque fournisseur de ressources est fourni avec une expérience d’administration. Cette expérience peut inclure des alertes relatives au fournisseur spécifique, aux mesures et à d’autres fonctionnalités de gestion propres au fournisseur de ressources.

5. **Rôles d’infrastructure**. Les rôles d’infrastructure sont les composants nécessaires à l’exécution d’Azure Stack. Seuls les rôles d’infrastructure qui signalent des alertes sont répertoriés. En cliquant sur un rôle, vous pouvez afficher les alertes qui sont associées à ce rôle spécifique et aux instances de rôle où il est en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes
[Surveiller l’intégrité et les alertes dans Azure Stack](azure-stack-monitor-health.md)

[Gérer les mises à jour dans Azure Stack](azure-stack-updates.md)
