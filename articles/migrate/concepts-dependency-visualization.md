---
title: "Visualisation des dépendances dans Azure Migrate | Microsoft Docs"
description: "Offre une vue d’ensemble des calculs d’évaluation dans le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: bcbb2ace6686e4052149a5dde1ed837a16c36bad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="dependency-visualization"></a>Visualisation de dépendance

Le service [Azure Migrate](migrate-overview.md) évalue les groupes de machines locales pour la migration vers Azure. Pour regrouper des machines, vous pouvez utiliser la visualisation des dépendances. Cet article fournit des informations sur cette fonctionnalité.


## <a name="overview"></a>Vue d'ensemble

La visualisation des dépendances dans Azure Migrate permet de créer des groupes pour évaluer la migration de manière plus fiable. Elle vous permet d’afficher les dépendances réseau de machines spécifiques ou d’un groupe de machines. Cette fonctionnalité est utile pour éviter tout problème de fonctionnalité ou toute perte (ou machines oubliées) durant le processus de migration, quand les applications et les charges de travail sont exécutées sur plusieurs machines.  

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Azure Migrate utilise la solution [Service Map](../operations-management-suite/operations-management-suite-service-map.md) dans [Log Analytics](../log-analytics/log-analytics-overview.md) pour la visualisation des dépendances.
- Quand vous créez un projet de migration Azure, un espace de travail OMS Log Analytics est créé dans votre abonnement.
- Le nom de l’espace de travail est le nom que vous spécifiez pour le projet de migration, auquel est ajouté le préfixe **migrate-**, et éventuellement un numéro en suffixe. 
- Accédez à l’espace de travail Log Analytics à partir de la section **Essentials** de la page **Vue d’ensemble** du projet.
- L’espace de travail créé est marqué à l’aide de la clé **MigrateProject** et de la valeur **project name**. Vous pouvez utiliser ces informations pour effectuer des recherches dans le portail Azure.  

    ![Espace de travail Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez analyser.  

## <a name="do-i-need-to-pay-for-it"></a>Est-ce une fonctionnalité payante ?

Azure Migrate est disponible sans coût supplémentaire. L’utilisation des fonctionnalités de visualisation des dépendances dans Azure Migrate nécessite Service Map. Lors de la création d’un projet Azure Migrate, Azure Migrate crée automatiquement un nouvel espace de travail Log Analytics à votre place.

> [!NOTE]
> La fonctionnalité de visualisation des dépendances utilise Service Map via un espace de travail Log Analytics. Depuis le 28 février 2018, avec l’annonce de la disponibilité générale d’Azure Migrate, la fonctionnalité est désormais disponible sans frais supplémentaires. Vous devez créer un nouveau projet pour utiliser l’espace de travail gratuit. Les espaces de travail existants avant la disponibilité générale sont toujours facturés, nous vous recommandons donc d’utiliser un nouveau projet.

1. L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics entraîne des frais Log Analytics standard. 
2. Pour prendre en charge des scénarios de migration sans frais supplémentaires, la solution Service Map n’entraîne pas de frais pendant 180 jours à compter de la création du projet Azure Migrate, après quoi des frais standard s’appliquent.
3. Seul l’espace de travail créé dans le cadre de la création du projet pourra être utilisé.

Lorsque vous inscrivez des agents auprès de l’espace de travail, utilisez l’ID et la clé fournis par le projet sur la page des étapes de l’agent d’installation. Vous ne pouvez pas utiliser un espace de travail existant et l’associer au projet Azure Migrate.

Lorsque le projet Azure Migrate est supprimé, l’espace de travail ne l’est pas. Après la suppression du projet, l’utilisation de Service Map ne sera pas gratuite et chaque nœud sera facturé en fonction du niveau payant de l’espace de travail Log Analytics.

En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/pricing/details/azure-migrate/). 

## <a name="how-do-i-manage-the-workspace"></a>Comment gérer l’espace de travail ?

Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate. Il n’est pas supprimé si vous supprimez le projet de migration dans lequel il a été créé. Si vous n’avez plus besoin de l’espace de travail, [supprimez-le](../log-analytics/log-analytics-manage-access.md) manuellement.

Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet de migration. Si vous le faites, les dépendances ne fonctionneront pas comme prévu.

## <a name="next-steps"></a>étapes suivantes

[Regrouper des machines à l’aide de dépendances de machines](how-to-create-group-machine-dependencies.md)