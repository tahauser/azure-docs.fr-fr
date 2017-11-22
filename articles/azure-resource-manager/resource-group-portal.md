---
title: "Utiliser le portail Azure pour gérer des ressources Azure | Microsoft Docs"
description: "Utilisez le Portail Azure et Azure Resource Manager pour gérer vos ressources. Montre comment utiliser des tableaux de bord pour surveiller les ressources."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: 27213482c3ef6b35e1e3f887c9a336b946850802
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-resources-through-portal"></a>Gérer les ressources Azure sur le portail

Cet article montre comment utiliser le [Portail Azure](https://portal.azure.com) avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos ressources Azure. Pour en savoir plus sur le déploiement des ressources avec le portail, voir [Déployer des ressources avec des modèles Resource Manager et le portail Azure](resource-group-template-deploy-portal.md).

## <a name="manage-resource-groups"></a>Gérer des groupes de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe. 

Le groupe de ressources stocke des métadonnées sur les ressources. Par conséquent, lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

1. Pour afficher tous les groupes de ressources de votre abonnement, sélectionnez **Groupes de ressources**.
   
    ![parcourir les groupes de ressources](./media/resource-group-portal/browse-groups.png)
2. Pour créer un groupe de ressources vide, sélectionnez **Ajouter**.
   
    ![ajouter un groupe de ressources](./media/resource-group-portal/add-resource-group.png)
3. Fournissez un nom et un emplacement pour le nouveau groupe de ressources. Sélectionnez **Créer**.
   
    ![Créer un groupe de ressources](./media/resource-group-portal/create-empty-group.png)
4. Vous pourrez avoir besoin de sélectionner **Actualiser** pour afficher le groupe de ressources que vous venez de créer.
   
    ![actualiser un groupe de ressources](./media/resource-group-portal/refresh-resource-groups.png)
5. Pour personnaliser les informations affichées pour vos groupes de ressources, sélectionnez **Colonnes**.
   
    ![personnaliser des colonnes](./media/resource-group-portal/select-columns.png)
6. Sélectionnez les colonnes à ajouter, puis sélectionnez **Mettre à jour**.
   
    ![ajouter des colonnes](./media/resource-group-portal/add-columns.png)
7. Pour plus d’informations sur le déploiement de ressources dans votre nouveau groupe de ressources, voir [Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure](resource-group-template-deploy-portal.md).
8. Pour accéder rapidement à un groupe de ressources, vous pouvez épingler ce dernier à votre tableau de bord.
   
    ![épingler un groupe de ressources](./media/resource-group-portal/pin-group.png)
9. Le tableau de bord affiche le groupe de ressources et ses ressources. Vous pouvez sélectionner les groupes de ressources ou l’une de leurs ressources pour accéder à l’élément correspondant.
   
    ![épingler un groupe de ressources](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Baliser des ressources
Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations sur l’utilisation des balises, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Surveiller les ressources
Lorsque vous sélectionnez une ressource, le portail présente les graphiques et tables par défaut pour la surveillance de ce type de ressource.

1. Sélectionnez une ressource, puis observez la section **Surveillance** . Elle contient des graphiques pertinents pour le type de ressource. L’illustration suivante montre les données de surveillance par défaut pour un compte de stockage.
   
    ![afficher la surveillance](./media/resource-group-portal/show-monitoring.png)
2. Vous pouvez épingler une section à votre tableau de bord en sélectionnant les points de suspension (...) situés au-dessus de la section. Vous pouvez aussi personnaliser la taille de la section ou supprimer complètement cette section. L’illustration suivante montre comment épingler, personnaliser ou supprimer la section Processeur et mémoire.
   
    ![épingler une section](./media/resource-group-portal/pin-cpu-section.png)
3. Après avoir épinglé la section au tableau de bord, vous obtenez le résumé sur le tableau de bord. Vous pouvez le sélectionner immédiatement pour obtenir plus de détails sur les données.
   
    ![afficher le tableau de bord](./media/resource-group-portal/view-startboard.png)
4. Pour personnaliser entièrement les données que vous surveillez via le portail, accédez à votre tableau de bord par défaut, puis sélectionnez **Nouveau tableau de bord**.
   
    ![dashboard](./media/resource-group-portal/dashboard.png)
5. Donnez un nom à votre tableau de bord et faites glisser les mosaïques dessus. Les mosaïques sont filtrées par différentes options.
   
    ![dashboard](./media/resource-group-portal/create-dashboard.png)
   
     Pour plus d’informations sur l’utilisation des tableaux de bord, consultez [Création et partage de tableaux de bord sur le Portail Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Gestion des ressources
Lorsque vous visualisez une ressource dans le portail, les options de gestion de cette ressource particulière s’affichent.

![Gestion des ressources](./media/resource-group-portal/manage-resources.png)

À partir de ces options, vous pouvez effectuer des opérations telles que le démarrage et l’arrêt d’une machine virtuelle, ou la reconfiguration des propriétés de la machine virtuelle.

## <a name="move-resources"></a>Déplacer des ressources
Si vous souhaitez déplacer des ressources vers un autre groupe de ressources ou un autre abonnement, voir [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

## <a name="lock-resources"></a>Verrouiller des ressources
Vous pouvez verrouiller un abonnement, un groupe de ressources ou une ressource afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier accidentellement des ressources cruciales. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Afficher votre abonnement et vos coûts
Vous pouvez afficher des informations sur votre abonnement et les coûts cumulés de toutes vos ressources. Sélectionnez **Abonnements** , puis l’abonnement de votre choix. Il se peut que vous n’ayez qu’un seul abonnement à sélectionner.

![abonnement](./media/resource-group-portal/select-subscription.png)

Le taux d’avancement s’affiche.

![taux d’avancement](./media/resource-group-portal/burn-rate.png)

Ainsi qu’une répartition des coûts par type de ressource.

![coût des ressources](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Exportation du modèle
Après avoir configuré votre groupe de ressources, vous pouvez éventuellement afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser les futurs déploiements de la solution, car le modèle contient la totalité de l’infrastructure.
2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

Pour obtenir des instructions détaillées, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Supprimer des ressources ou un groupe de ressources
La suppression d’un groupe de ressources supprime toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lors de la suppression d’un groupe de ressources. Ce dernier peut contenir des ressources dont des ressources d’autres groupes de ressources dépendent.

![supprimer un groupe](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour visualiser les journaux d’activité, consultez la rubrique [Opérations d’audit avec Resource Manager](resource-group-audit.md).
* Pour plus d’informations sur le déploiement, consultez la page [Voir les opérations de déploiement](resource-manager-deployment-operations.md).
* Pour déployer des ressources via le portail, voir [Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure](resource-group-template-deploy-portal.md).
* Pour gérer l’accès aux ressources, voir [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).

