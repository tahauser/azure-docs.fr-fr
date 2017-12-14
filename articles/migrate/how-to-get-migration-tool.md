---
title: "Migrer des machines après l’évaluation avec Azure Migrate | Microsoft Docs"
description: "Explique comment obtenir des recommandations pour migrer des machines après l’exécution d’une évaluation avec le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: e6e32e9bd2384987a1d0315bfbef913c46fc5dbb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-machines-after-assessment"></a>Migrer des machines après évaluation


[Azure Migrate](migrate-overview.md) évalue les machines locales pour vérifier si elles sont adaptées à la migration vers Azure, puis fournit des estimations de dimensionnement et de coût d’exécution de chaque machine dans Azure. Dans le cadre d’une migration, Azure Migrate se contente d’évaluer les machines. La migration proprement dite est effectuée à l’aide d’autres services Azure.

Cet article décrit comment obtenir des suggestions d’outil de migration, une fois que vous avez exécuté une évaluation de la migration.

## <a name="migration-tool-suggestion"></a>Suggestion d’outil de migration

Pour obtenir des suggestions d’outil de migration, vous devez effectuer une découverte approfondie de l’environnement local. Pour ce faire, vous devez installer des agents sur les machines locales.  

1. Créez un projet Azure Migrate, découvrez les machines locales et créez une évaluation de la migration. [En savoir plus](tutorial-assessment-vmware.md).
2. Téléchargez et installez les agents Azure Migrate sur chaque machine locale pour laquelle vous souhaitez afficher une méthode de migration recommandée. [Suivez cette procédure](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) pour installer les agents.
2. Identifiez les machines locales adaptées à la migration lift-and-shift. Il s’agit des machines virtuelles dont les applications n’ont pas besoin de subir de modifications et qui peuvent être migrées en l’état.
3. Pour la migration lift-and-shift, nous vous suggérons d’utiliser Azure Site Recovery. [En savoir plus](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Vous pouvez également utiliser des outils tiers qui prennent en charge la migration vers Azure.
4. Si des machines locales ne sont pas adaptées à la migration lift-and-shift, autrement dit, si vous souhaitez migrer une application spécifique plutôt qu’une machine virtuelle entière, vous pouvez utiliser d’autres outils de migration. Par exemple, nous vous suggérons le [service Azure Database Migration](https://azure.microsoft.com/campaigns/database-migration/) si vous souhaitez migrer vers Azure des bases de données locales telles que SQL Server, MySQL ou Oracle.


## <a name="review-suggested-migration-methods"></a>Passer en revue les méthodes de migration suggérées

1. Pour obtenir une suggestion de méthode de migration, vous devez créer un projet Azure Migrate, découvrir les machines locales et exécuter une évaluation de la migration. [En savoir plus](tutorial-assessment-vmware.md).
2. Une fois l’évaluation créée, affichez-la dans le projet > **Vue d’ensemble** > **Tableau de bord**. Cliquez sur **Préparation de l’évaluation**.

    ![Préparation de l’évaluation](./media/tutorial-assessment-vmware/assessment-report.png)  

3. Dans **Outil suggéré**, examinez les suggestions d’outils pour la migration.

    ![Outil suggéré](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Étapes suivantes

[Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
