---
title: "Gérer les mises à jour dans Azure Stack - Vue d’ensemble | Microsoft Docs"
description: "Découvrez la gestion des mises à jour pour les systèmes intégrés Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 23b05909bda7785b45aeaeed0bd75a90de9ffe50
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gérer les mises à jour dans Azure Stack - Vue d’ensemble

*S’applique à : systèmes intégrés Azure Stack*

Microsoft publie des packages de mises à jour pour les systèmes intégrés Azure Stack de manière régulière, généralement le quatrième mardi de chaque mois à compter de la disponibilité générale. Demandez à votre fabricant OEM quel est son processus de notification, afin d’être sûr que votre organisation reçoit les notifications de mise à jour, ou consultez Vue d’ensemble\Notes de mise à jour\Notes de mise à jour de systèmes intégrés pour obtenir plus d’informations sur les versions spécifiques.

Chaque version des mises à jour logicielles Microsoft est fournie sous forme de package de mise à jour unique. En tant qu’opérateur Azure Stack, vous pouvez facilement importer, installer et surveiller la progression de l’installation de ces packages de mise à jour à partir du portail administrateur. 

Votre fabricant de matériel OEM publie également des mises à jour, par exemple pour les pilotes et le microprogramme. Ces mises à jour sont fournies sous forme de packages distincts par votre fabricant de matériel OEM, et sont gérées séparément des mises à jour Microsoft.

Pour conserver votre système dans un état de prise en charge, vous devez maintenir Azure Stack à jour à un niveau de version spécifique. Pensez à passer en revue la [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Vous ne pouvez pas appliquer des packages de mise à jour Azure Stack au Kit de développement Azure Stack. Les packages de mise à jour sont conçues pour les systèmes intégrés.

## <a name="the-update-resource-provider"></a>Le fournisseur de ressources de mise à jour

Azure Stack inclut un fournisseur de ressources de mise à jour qui orchestre l’application des mises à jour logicielles Microsoft. Ce fournisseur de ressources garantit que les mises à jour sont appliquées sur tous les hôtes physiques, les applications et runtimes Service Fabric, et toutes les machines virtuelles d’infrastructure et leurs services associés.

Lors de l’installation des mises à jour, vous pouvez facilement afficher l’état à mesure que le processus de mise à jour cible les différents sous-systèmes dans Azure Stack (par exemple les hôtes physiques et les machines virtuelles d’infrastructure).

## <a name="plan-for-updates"></a>Planifier les mises à jour

Nous vous recommandons vivement d’informer les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations de portail et les charges de travail de locataire.

## <a name="using-the-update-tile-to-manage-updates"></a>Utilisation de la vignette Mise à jour pour gérer les mises à jour
La gestion des mises à jour à partir du portail administrateur est un processus simple. Un opérateur Azure Stack peut accéder à la vignette Mise à jour dans le tableau de bord pour :

- Afficher des informations importantes telles que la version actuelle
- Installer des mises à jour et surveiller la progression
- Examiner l’historique des mises à jour pour les mises à jour précédemment installées
 
## <a name="determine-the-current-version"></a>Déterminer la version actuelle

La vignette Mise à jour affiche la version actuelle d’Azure Stack. Vous pouvez accéder à la vignette Mise à jour en appliquant l’une des méthodes suivantes dans le portail administrateur :

- Dans le tableau de bord, consultez la version actuelle dans la vignette **Mise à jour**.
 
   ![Vignette Mises à jour sur le tableau de bord par défaut](./media/azure-stack-updates/image1.png)
 
- Dans la vignette **Gestion des régions**, cliquez sur le nom de la région. Consultez la version actuelle dans la vignette **Mise à jour**.

## <a name="next-steps"></a>Étapes suivantes

- [Stratégie de maintenance d’Azure Stack](azure-stack-servicing-policy.md) 
- [Gestion des régions dans Azure Stack](azure-stack-region-management.md)     


