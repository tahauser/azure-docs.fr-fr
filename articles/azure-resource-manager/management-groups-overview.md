---
title: Organiser vos ressources avec des groupes d’administration Azure | Microsoft Docs
description: Découvrez les groupes d’administration et comment les utiliser.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: db472345bacda916f1b1664ed7803978ab235a2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organiser vos ressources avec des groupes d’administration Azure 

Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-dessus des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos conditions de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des conditions appliquées à ce groupe d’administration. Les groupes d’administration vous permettent une gestion de qualité professionnelle à grande échelle, quel que soit le type de vos abonnements.

La fonctionnalité de groupe d’administration est disponible dans une préversion publique. Pour commencer à utiliser des groupes d’administration, connectez-vous au [portail Azure](https://portal.azure.com) et recherchez **Groupes d’administration** dans la section **Tous les services**. 

Par exemple, vous pouvez appliquer des stratégies à un groupe d’administration pour limiter les régions disponibles pour la création de machines virtuelles. Une telle stratégie s’appliquerait alors à tous les groupes d’administration, abonnements et ressources sous ce groupe d’administration en autorisant uniquement la création de machines virtuelles dans une région donnée.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hiérarchie des groupes d’administration et des abonnements 

Vous pouvez créer une structure flexible de groupes d’administration et d’abonnements pour organiser vos ressources dans une hiérarchie à des fins de stratégie unifiée et de gestion de l’accès. Le diagramme suivant illustre un exemple de hiérarchie qui comprend des groupes d’administration et des abonnements organisés en services.    

![arborescence](media/management-groups/MG_overview.png)

En créant une hiérarchie regroupée par services, vous pouvez affecter des rôles de [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-what-is.md) dont *héritent* les services sous ce groupe d’administration. En utilisant des groupes d’administration, vous pouvez réduire votre charge de travail et le risque d’erreur en n’ayant à attribuer le rôle qu’une seule fois. 

### <a name="important-facts-about-management-groups"></a>Faits importants sur les groupes d’administration
- 10 000 groupes d’administration peuvent être pris en charge dans un seul annuaire. 
- Une arborescence de groupes d’administration peut prendre en charge jusqu’à six niveaux de profondeur.
    - Cette limite n’inclut pas le niveau racine ni le niveau d’abonnement.
- Chaque groupe d’administration peut uniquement prendre en charge un seul parent.
- Chaque groupe d’administration peut avoir plusieurs enfants. 

### <a name="preview-subscription-visibility-limitation"></a>Limitation de visibilité des abonnements aux préversions 
Il existe actuellement une limitation dans la préversion ne vous permettant pas d’afficher les abonnements auxquels vous avez hérité l’accès. L’accès à l’abonnement est hérité, mais Azure Resource Manager n’est pas encore en mesure d’honorer l’accès hérité.  

L’utilisation de l’API REST pour obtenir des informations sur l’abonnement renvoie les détails puisque vous y avez accès, mais les abonnements n’apparaissent pas dans le portail Azure et Azure Powershell. 

Cet élément est en cours de traitement et sera résolu avant que les groupes d’administration soient annoncés en tant que « Disponibilité générale ».  


## <a name="root-management-group-for-each-directory"></a>Groupe d’administration racine pour chaque annuaire

Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé groupe d’administration « racine ». Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des affectations RBAC au niveau de l’annuaire. L’[administrateur de l’annuaire doit élever ses privilèges](../active-directory/role-based-access-control-tenant-admin-access.md) pour être propriétaire de ce groupe racine à l’initiale. Une fois que l’administrateur est propriétaire du groupe, il peut affecter un rôle RBAC à d’autres utilisateurs ou groupes de l’annuaire pour qu’ils gèrent la hiérarchie.  

### <a name="important-facts-about-the-root-management-group"></a>Faits importants sur le groupe d’administration racine
- Le nom et l’ID du groupe d’administration racine reçoivent l’ID Azure Active Directory par défaut. Le nom d’affichage peut être mis à jour à tout moment au sein du portail Azure. 
- Tous les abonnements et groupes d’administration sont contenus dans un seul groupe d’administration racine au sein de l’annuaire.  
    - Il est recommandé de pouvoir développer tous les éléments de l’annuaire à partir du groupe d’administration racine à des fins de gestion globale.  
    - Avec la préversion publique, tous les abonnements au sein de l’annuaire ne sont pas automatiquement enfants de la racine.   
    - Avec la préversion publique, les nouveaux abonnements ne sont pas automatiquement définis par défaut dans le groupe d’administration racine. 
- Le groupe d’administration racine ne peut pas être déplacé ni supprimé, contrairement aux autres groupes d’administration. 
  
## <a name="management-group-access"></a>Accès aux groupes d’administration

Les groupes d’administration Azure prennent en charge le [contrôle d’accès en fonction du rôle (RBAC) Azure](../active-directory/role-based-access-control-what-is.md) pour tous les accès aux ressources et toutes les définitions de rôles. Les ressources enfants qui existent dans la hiérarchie héritent de ces autorisations.   

Tout [rôle RBAC intégré](../active-directory/role-based-access-control-what-is.md#built-in-roles) peut être affecté à un groupe d’administration. Il en existe quatre couramment utilisés : 
- **Propriétaire** dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. 
- **Contributeur** peut créer et gérer tous les types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes.
- **Collaborateur de stratégie de ressource** peut créer et gérer des stratégies dans l’annuaire sur les ressources.     
- **Lecteur** peut consulter les ressources Azure existantes. 


## <a name="next-steps"></a>Étapes suivantes 
Pour en savoir plus sur les groupes d’administration, consultez : 
- [Créer des groupes d’administration pour organiser les ressources Azure](management-groups-create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](management-groups-manage.md)
- [Installer le module Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Passer en revue les spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installer l’extension Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

