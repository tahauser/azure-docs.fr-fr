---
title: "Grouper des machines pour l’évaluation avec Azure Migrate | Microsoft Docs"
description: "Explique comment grouper des machines avant d’exécuter une évaluation avec le service Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Grouper des machines pour l’évaluation

Cet article explique comment créer un groupe de machines pour l’évaluation par [Azure Migrate](migrate-overview.md). Azure Migrate évalue les machines du groupe pour vérifier si elles sont adaptées à la migration vers Azure, et fournit des estimations de dimensionnement et de coût d’exécution de chaque machine dans Azure.


## <a name="create-a-group"></a>Créer un groupe

1. Dans le **Tableau de bord** du projet Azure Migrate, cliquez sur  **Groupes** > **+ Groupe** et spécifiez un nom de groupe.
2. Ajoutez une ou plusieurs machines au groupe, puis cliquez sur  **Créer**. 
3. Vous pouvez éventuellement choisir d’exécuter une nouvelle évaluation pour le groupe. 

    ![Créer un groupe](./media/how-to-create-a-group/create-group.png)

Une fois le groupe créé, vous pouvez le modifier en le sélectionnant sur la page **Groupes** et en ajoutant ou en supprimant des machines.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser le [mappage de dépendances de machine](how-to-create-group-machine-dependencies.md) pour créer des groupes plus détaillés.
- [Découvrez plus en détails](concepts-assessment-calculation.md) le mode de calcul des évaluations.
