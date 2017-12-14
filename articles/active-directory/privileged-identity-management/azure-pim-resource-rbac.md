---
title: "Vue d’ensemble de la fonctionnalité RBAC dans PIM pour les ressources Azure | Documents Microsoft"
description: "Obtenir une vue d’ensemble de la fonctionnalité RBAC dans PIM, y compris la terminologie et les notifications"
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.openlocfilehash: 19715f800e7d8d40336d8e9fa3bf8073795dce5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="pim-for-azure-resources-preview"></a>PIM pour les ressources Azure (préversion)

Avec Azure Active Directory Privileged Identity Management (PIM), vous pouvez maintenant gérer, contrôler et surveiller l’accès aux ressources Azure (préversion) au sein de votre organisation. Cela inclut les abonnements, les groupes de ressources et même les machines virtuelles. N’importe quelle ressource dans le portail Azure tirant parti de la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) de Azure peut utiliser toutes les capacités de gestion de la sécurité et des cycles de vie offertes par Azure AD PIM, ainsi que certaines nouvelles fonctionnalités que nous prévoyons d’ajouter bientôt aux rôles de Azure AD. 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>PIM pour les ressources Azure (préversion) vient en aide aux administrateurs de ressources

- Vérifier quels utilisateurs et groupes se sont vus assigner des rôles pour les ressources Azure que vous administrez
- Activer l’accès sur demande, « à la volée », pour gérer des ressources telles que les abonnements, les groupes de ressources, etc.
- Faire expirer automatiquement les accès assignés aux ressources d’utilisateurs/groupes grâce aux nouveaux paramètres d’affectation temporaire
- Affecter un accès temporaire aux ressources pour des tâches rapides ou des planifications d’appel
- Application de l’authentification multifacteur via Azure Multi-Factor Authentication pour l’accès aux ressources sur n’importe quel rôle intégré ou personnalisé 
- Obtenir des rapports sur l’activité des ressources liées aux accès pendant une session active d’un utilisateur
- Obtenir des alertes lorsque de nouveaux utilisateurs ou groupes se voient assigner un accès à des ressources, et lorsqu’ils activent des attributions éligibles

Azure AD PIM peut gérer les rôles intégrés de Azure Resource, ainsi que les rôles personnalisés (RBAC), notamment (liste non exhaustive) :

- Propriétaire
- Administrateur de l'accès utilisateur
- Collaborateur
- Administrateur de la sécurité
- Gestionnaire de sécurité, et plus

>[!NOTE]
Les utilisateurs ou les membres d’un groupe affectés aux rôles de propriétaire ou d’administrateur de l’accès utilisateur, de même que les administrateurs globaux qui permettent la gestion des abonnements dans Azure AD, sont des administrateurs de ressources. Ces administrateurs peuvent assigner des rôles, configurer des paramètres de rôle et revoir les accès à l’aide de PIM pour Azure Resources. Affichez la liste des [rôles intégrés pour les ressources Azure](../role-based-access-built-in-roles.md).

## <a name="tasks"></a>Tâches

PIM offre un accès aisé pour activer des rôles, afficher les demandes/activations en attente ainsi que les approbations en attente (pour [les rôles d’annuaire Azure AD](azure-ad-pim-approval-workflow.md)) et les revus attendant votre réponse à partir de la section Tâches du menu de navigation gauche.

Lorsque vous accédez à l’un des éléments du menu Tâches à partir du point d’entrée Vue d’ensemble, la vue correspondante contient les résultats pour les rôles d’annuaire Azure AD et les rôles de ressources Azure (version préliminaire). 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Mes rôles contiennent une liste de vos attributions de rôles actives et éligibles pour les rôles d’annuaire Azure AD et les rôles de ressources de Azure (préversion).

## <a name="activate-roles"></a>Activer des rôles

L’activation de rôles pour les ressources Azure (préversion) introduit une nouvelle expérience permettant aux membres du rôle éligible de planifier une activation à une date/heure future et de sélectionner une durée d’activation spécifique au sein d’un délai maximal (configuré par les administrateurs). En savoir plus sur [l’activation de rôles de Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Dans le menu Activations, entrez la date et l’heure de début souhaitées pour l’activation du rôle. Si vous le souhaitez, réduisez la durée d’activation (durée pendant laquelle le rôle est actif), puis indiquez une justification si nécessaire ; cliquez sur Activer.

Si la date et l’heure de début ne sont pas modifiées, le rôle sera activé dans les secondes qui suivent. Vous verrez un message en bannière indiquant un rôle en attente d’activation dans la page Mes rôles. Cliquez sur le bouton de rafraîchissement pour effacer ce message.

![](media/azure-pim-resource-rbac/my-roles.png)

Si l’activation est planifiée pendant une date future, la demande en attente s’affichera dans l’onglet Demandes en attente du menu de navigation gauche. Dans le cas où l’activation du rôle ne serait plus nécessaire, l’utilisateur peut annuler la demande en cliquant sur le bouton Annuler sur le côté droit de la page.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Découvrir et gérer des ressources Azure

Pour rechercher et gérer les rôles pour une ressource Azure, sélectionnez Ressources de Azure (préversion) sous l’onglet Gérer dans le menu de navigation gauche. Utilisez les filtres ou la barre de recherche en haut de la page pour rechercher une ressource.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Tableaux de bord de ressources

Le tableau de bord de vue d’administrateur possède quatre composants principaux. Une représentation graphique des activations de rôle de ressource au cours des sept derniers jours. Ces données sont limitées à la ressource sélectionnée et affichent les activations pour les principaux rôles (propriétaire, collaborateur, administrateur de l’accès utilisateur) et pour tous les rôles combinés.

Deux graphiques se trouvent à droite du graphique des activations, affichant la distribution des attributions de rôle par type d’attribution, pour les utilisateurs et les groupes. La sélection d’une section du graphique indique la valeur sous forme d’un pourcentage (ou vice versa).

![](media/azure-pim-resource-rbac/admin-view.png)

Sous les graphiques, vous voyez le nombre d’utilisateurs et de groupes ayant reçu de nouvelles attributions de rôle au cours des 30 derniers jours (à gauche) ainsi qu’une liste des rôles, triée par les attributions totales (ordre décroissant).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Gérer les attributions de rôles

Les administrateurs peuvent gérer des attributions de rôle en sélectionnant des rôles ou des membres dans le volet de navigation gauche. La sélection des rôles permet aux administrateurs de centrer leurs tâches de gestion sur un rôle spécifique, tandis que la section Membres affiche toutes les attributions de rôles des utilisateurs et de groupe pour la ressource.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Si vous avez un rôle en attente d’activation, une bannière de notification s’affiche en haut de la page lors de l’affichage de l’appartenance.

## <a name="assign-roles"></a>Attribuer des rôles

Pour affecter un utilisateur ou un groupe à un rôle, sélectionnez le rôle (si vous affichez les rôles), ou cliquez sur Ajouter à partir de la barre d’action (si vous utilisez la vue des membres).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Si vous ajoutez un utilisateur ou un groupe à partir de l’onglet des membres, vous devez sélectionner un rôle depuis le menu Ajouter avant de pouvoir sélectionner un utilisateur ou un groupe.

![](media/azure-pim-resource-rbac/select-role.png)

Choisissez un utilisateur ou un groupe à partir de l’annuaire.

![](media/azure-pim-resource-rbac/choose.png)

Dans le menu déroulant, choisissez le type d’attribution approprié. 

**Attribution à la volée :** elle fournit aux utilisateurs ou membres du groupe un accès au rôle éligible mais pas persistant pendant une période de temps spécifiée ou bien indéfiniment (selon la configuration des paramètres de rôle). 

**Attribution directe :** elle ne nécessite pas l’activation de l’attribution du rôle par l’utilisateur ou les membres du groupe (connu comme un accès permanent). Microsoft recommande l’utilisation d’attributions directes pour une utilisation à court terme, par exemple pour des équipes d’appel ou des activités à durée limitée, où l’accès ne sera pas nécessaire une fois la tâche terminée.

![](media/azure-pim-resource-rbac/membership-settings.png)

Une case à cocher située sous la liste déroulante du type d’attribution vous permet de spécifier le caractère permanent de l’attribution (éligible de façon permanente pour activer une attribution à la volée/active de façon permanente pour l’attribution directe). Pour spécifier une durée d’attribution spécifique, décochez la case et modifiez les champs des heures et dates de début et/ou de fin.

>[!NOTE]
La case à cocher peut être non modifiable si un autre administrateur a spécifié la durée maximale d’attribution pour chaque type d’attribution dans les paramètres de rôle.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Voir les activations et les activités des ressources Azure

Si vous avez besoin de connaître les actions d’un utilisateur spécifique effectuées sur les différentes ressources, vous pouvez consulter l’activité de la ressource Azure sur une période d’activation donnée (pour les utilisateurs éligibles). Commencez par sélectionner un utilisateur à partir de la vue des membres ou à partir de la liste des membres dans un rôle spécifique. Le résultat affiche une vue graphique des actions de l’utilisateur sur les ressources Azure classées par date ainsi que les activations de rôle récentes sur la même période de temps.

![](media/azure-pim-resource-rbac/user-details.png)

La sélection d’une activation de rôle affichera les détails de l’activation de rôle ainsi que l’activité de la ressource Azure correspondante survenue lorsque l’utilisateur était actif.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Modifier des attributions existantes

Pour modifier des attributions existantes à partir de la vue des détails du groupe/utilisateur, sélectionnez Modifier les paramètres à partir de la barre d’action en haut de la page. Configurez le type d’attribution sur Attribution à la volée ou Attribution directe.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Revoir les accès à un abonnement

Pour revoir les attributions de rôle dans votre abonnement, sélectionnez l’onglet Membres dans le volet de navigation gauche, ou bien sélectionner des rôles et choisissez un rôle spécifique pour passer en revue les membres. 

Sélectionnez Revoir à partir de la barre d’action pour afficher les révisions d’accès existantes et sélectionnez Ajouter pour en créer une nouvelle.

![](media/azure-pim-resource-rbac/owner.png)

[En savoir plus sur les révisions d’accès](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Les révisions sont uniquement prises en charge pour les types de ressource d’abonnement à un instant donné.

## <a name="configure-role-settings"></a>Configurer les paramètres de rôle

La configuration des paramètres de rôle permet de définir les valeurs par défaut appliqués aux attributions dans l’environnement PIM. Pour définir ces valeurs pour votre ressource, sélectionnez l’onglet Paramètres de rôle dans le volet de navigation gauche, ou bien le bouton Paramètres de rôle sur la barre d’action dans n’importe quel rôle afin d’afficher les options actuelles.

Cliquer sur Modifier dans la barre d’action en haut de la page vous permet de modifier chaque paramètre.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Les modifications apportées aux paramètres sont enregistrées dans la page des paramètres de rôle en indiquant la date et l’heure de la dernière mise à jour ainsi que l’administrateur ayant modifié les paramètres.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Audit d’une ressource

L’audit d’une ressource vous donne une vue d’ensemble de l’activité des rôles pour la ressource. Vous pouvez filtrer les informations en choisissant une date prédéfinie ou une plage personnalisée.
![](media/azure-pim-resource-rbac/last-day.png) L’audit d’une ressource fournit également un accès rapide aux détails de l’activité d’un utilisateur. Dans la vue, toutes les actions « Activer le rôle » sont des liens vers l’activité des ressources spécifiques du demandeur.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Just Enough Administration (JEA)

L’utilisation des meilleures pratiques de JEA pour vos attributions de rôles de ressource est simple avec PIM pour les ressources Azure. Les utilisateurs et les membres de groupe disposant d’attributions dans des abonnements Azure ou des groupes de ressources peuvent activer leur attribution de rôle existante en lui donnant une portée limitée. 

À partir de la page de recherche, recherchez la ressource secondaire que vous devez gérer.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Sélectionnez Mes rôles à partir du menu de navigation de gauche et choisissez le rôle approprié à activer. Notez que le type d’affectation est Héritée, étant donné que le rôle a été attribué à l’abonnement plutôt qu’au groupe de ressources, comme indiqué ci-dessous.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Étapes suivantes

- [Rôles intégrés pour les ressources Azure](../role-based-access-built-in-roles.md)
- En savoir plus sur [l’activation de rôles de Azure AD](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [Flux de travail d’approbation PIM](azure-ad-pim-approval-workflow.md)
