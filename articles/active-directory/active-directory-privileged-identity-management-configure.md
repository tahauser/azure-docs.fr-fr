---
title: Configurer Azure AD Privileged Identity Management | Microsoft Docs
description: "Une rubrique qui explique ce qu’est Azure AD Privileged Identity Management et comment utiliser PIM pour renforcer la sécurité de votre cloud."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 94855cf102764532fa0a6718541beffe93230fd1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Qu’est-ce qu’Azure AD Privileged Identity Management ?

Avec Azure Active Directory (AD) Privileged Identity Management, vous pouvez gérer, contrôler et surveiller l’accès au sein de votre organisation. Cela inclut l’accès aux ressources d’Azure AD, aux ressources Azure (préversion) et à d’autres services Microsoft Online Services comme Office 365 ou Microsoft Intune.

> [!NOTE]
> Privileged Identity Management est disponible dans toute l’organisation quand vous accordez à vos administrateurs une licence pour l’édition Premium P2 d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Il est souhaitable pour les organisations de limiter le nombre de personnes qui ont accès aux informations ou aux ressources sécurisées afin de réduire le risque qu’un utilisateur malveillant accède à ces données ou qu’une ressource sensible soit accidentellement affectée par un utilisateur autorisé.  Cependant, les utilisateurs doivent pouvoir continuer à effectuer des opérations privilégiées dans les applications Azure AD, Azure, Office 365 ou SaaS. Les organisations peuvent donner aux utilisateurs un accès privilégié aux ressources Azure, telles que les abonnements, et à Azure AD. Elles doivent alors pouvoir surveiller ce que ces utilisateurs font avec leurs privilèges administratifs. Azure AD Privileged Identity Management permet de réduire le risque d’utilisation excessive, injustifiée ou malveillante des droits d’accès.

Grâce à Azure AD Privileged Identity Management, votre organisation peut :

- Voir quels utilisateurs disposent de rôles privilégiés pour gérer les ressources Azure (préversion) et quels utilisateurs disposent de rôles d’administrateur dans Azure AD
- Activer à la demande un accès administratif « juste-à-temps » aux services Microsoft Online Services comme Office 365 et Intune, et aux ressources Azure (préversion) des abonnements, aux groupes de ressources et aux ressources individuelles telles que des machines virtuelles 
-   Consulter un historique de l’activation d’administrateurs, y compris les modifications apportées par les administrateurs aux ressources Azure (préversion)
- Recevoir des alertes sur les modifications apportées aux affectations des administrateurs
- Exiger une approbation pour activer les rôles d’administrateur privilégiés Azure AD (préversion) 
- Vérifier l’appartenance aux rôles d’administrateur et demander aux utilisateurs de fournir une justification pour que l’appartenance à leur rôle soit maintenue

Dans Azure AD, Azure AD Privileged Identity Management peut gérer les utilisateurs affectés aux rôles organisationnels intégrés dans Azure AD, tels que celui d’administrateur général. Dans Azure, Azure AD Privileged Identity Management peut gérer les utilisateurs et les groupes affectés via les rôles RBAC Azure, y compris ceux de propriétaire et de contributeur.

## <a name="just-in-time-administrator-access"></a>Administrateur des accès immédiats

Jusqu’ici, vous pouviez affecter un utilisateur à un rôle d’administrateur via le portail Azure, les autres portails Microsoft Online Services ou les cmdlets Azure AD dans Windows PowerShell. Cet utilisateur devient ainsi un **administrateur permanent**, toujours actif dans le rôle qui lui a été affecté. Azure AD Privileged Identity Management introduit le concept d **’administrateur éligible**. Les administrateurs éligibles doivent être des utilisateurs qui ont besoin d’un accès privilégié de temps à autre, et non pas en permanence. Ce rôle reste inactif jusqu’à ce l’utilisateur ait besoin d’un tel accès ; dans ce cas, il complète un processus d’activation et devient administrateur actif pour une durée prédéterminée. De plus en plus d’organisations choisissent d’utiliser cette approche pour limiter ou éliminer l’« accès administrateur permanent » aux rôles privilégiés.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Activer Privileged Identity Management pour votre répertoire

Vous pouvez commencer à utiliser Azure AD Privileged Identity Management dans le [portail Azure](https://portal.azure.com/).

> [!NOTE]
> Vous devez être un administrateur général avec un compte d’organisation (par exemple, @yourdomain.com) et non un compte Microsoft (par exemple, @outlook.com), pour activer Azure AD Privileged Identity Management sur un annuaire.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure. Sélectionnez le répertoire où vous allez utiliser Azure AD Privileged Identity Management.
3. Sélectionnez **Tous les services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

Si vous êtes la première personne à utiliser Azure AD Privileged Identity Management dans votre répertoire et que vous accédez aux rôles d’annuaire Azure AD, un [Assistant Sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guide tout au long de la procédure d’affectation initiale. Vous devenez alors automatiquement le premier **administrateur de la sécurité** et le premier **administrateur de rôle privilégié** de l’annuaire.

Pour les rôles Azure AD, seul un utilisateur qui possède le rôle d’administrateur de rôle privilégié peut gérer les affectations des autres administrateurs dans Azure AD PIM. Vous pouvez [permettre aux autres utilisateurs de gérer les rôles d’annuaire dans PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Les administrateurs généraux, les administrateurs de la sécurité et les lecteurs Sécurité peuvent consulter les affectations aux rôles Azure AD dans Azure AD PIM.
Pour les rôles RBAC Azure, seul un administrateur d’abonnement, un propriétaire de ressource ou un administrateur de l’accès utilisateur aux ressources peut gérer les affectations des autres administrateurs dans Azure AD PIM.  Par défaut, les utilisateurs qui possèdent un rôle d’administrateur de rôle privilégié, d’administrateur de la sécurité ou de lecteur Sécurité ne peuvent pas consulter les affectations aux rôles RBAC Azure dans Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Vue d’ensemble de Privileged Identity Management (point d’entrée)

Azure AD Privileged Identity Management prend en charge l’administration des rôles d’annuaire Azure AD et des rôles de ressources Azure (préversion). Le fonctionnement des rôles pour les ressources Azure diffère de celui des rôles d’administrateur dans Azure AD. Les rôles de ressources Azure fournissent des autorisations granulaires pour la ressource à laquelle ils sont affectés, ainsi que pour toutes les ressources subordonnées dans la hiérarchie des ressources (selon un principe appelé héritage). Vous trouverez plus d’informations sur RBAC, la hiérarchie des ressources et l’héritage [ici](role-based-access-control-configure.md). Pour les rôles d’annuaire Azure AD et les ressources Azure (préversion), PIM peut être géré en accédant au lien approprié sous la section Gérer du menu de navigation gauche du point d’entrée Vue d’ensemble de PIM.

PIM permet d’activer des rôles et d’afficher les demandes/activations en attente, les approbations en attente (pour les rôles d’annuaire Azure AD), ainsi que les révisions attendant une réponse de votre part en toute simplicité à partir de la section Tâches du menu de navigation gauche.

Lorsque vous accédez à l’un des éléments du menu Tâches à partir du point d’entrée Vue d’ensemble, la vue correspondante contient les résultats pour les rôles d’annuaire Azure AD et les rôles de ressources Azure (version préliminaire).

![Démarrage rapide](./media/active-directory-privileged-identity-management-configure/quick-start.png)

La section Mes rôles contient une liste de vos affectations de rôles actifs et éligibles pour les rôles d’annuaire Azure AD et les rôles de ressources Azure (préversion). Vous trouverez plus d’informations sur l’activation des affectations de rôles éligibles [ici](active-directory-privileged-identity-management-how-to-activate-role.md).

L’activation de rôles de ressources Azure (préversion) introduit une nouvelle expérience permettant aux membres éligibles d’un rôle de planifier une activation à une date/heure future et de sélectionner une durée d’activation spécifique dans la limite autorisée par les administrateurs.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

Si une activation planifiée n’est plus nécessaire, les utilisateurs peuvent annuler leur demande en accédant aux demandes en attente dans le menu de navigation gauche, puis en cliquant sur le bouton Annuler en regard de la demande correspondante.

![Demandes en attente](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Tableau de bord d’administration de Privileged Identity Management

Azure AD Privileged Identity Manager fournit un tableau de bord d’administration qui contient des informations importantes telles que :

* Alertes indiquant des possibilités d’amélioration de la sécurité
* Le nombre d'utilisateurs affectés à chaque rôle privilégié  
* Le nombre d'administrateurs éligibles et permanents
* Un graphique des activations de rôle privilégié dans votre annuaire
*   Le nombre d’affectations juste-à-temps, limitées dans le temps et permanentes pour les rôles de ressources Azure (préversion)
*   Les utilisateurs et les groupes auxquels de nouveaux rôles ont été affectés au cours des 30 derniers jours (rôles de ressources Azure)


![Capture d’écran du tableau de bord PIM][2]

## <a name="privileged-role-management"></a>Gestion des rôles privilégiés

Avec Azure AD Privileged Identity Management, vous pouvez gérer les administrateurs en ajoutant ou en supprimant des administrateurs permanents ou éligibles pour chaque rôle d’annuaire Azure AD. Avec PIM pour les ressources Azure (préversion), les propriétaires, les administrateurs de l’accès utilisateur et les administrateurs généraux qui activent la gestion des abonnements dans leur client peuvent affecter des utilisateurs ou des groupes aux rôles de ressources Azure en leur octroyant un accès juste-à-temps (rôle éligible), limité dans le temps (activation non nécessaire) avec une date/heure de début et de fin ou permanent (si cette option est activée dans les paramètres de rôle).

![Capture d’écran d’ajout et de suppression d’administrateurs dans PIM][3]

## <a name="configure-the-role-activation-settings"></a>Configurer les paramètres d'activation de rôle

À l’aide des [paramètres de rôle](active-directory-privileged-identity-management-how-to-change-default-settings.md), vous pouvez configurer les propriétés d’activation de rôle éligible pour les rôles d’annuaire Azure AD, notamment :

* La durée de la période d’activation d’un rôle
* La notification d'activation d’un rôle
* Les informations qu'un utilisateur doit fournir au cours du processus d'activation du rôle
* Un numéro d’incident ou ticket de service
* [Exigences relatives au flux de travail d’approbation - préversion](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Capture d’écran de l’activation d’administrateur dans les paramètres PIM][4]

Notez que dans l’image, les boutons de **l’authentification multifacteur** sont désactivés. Avec certains rôles dotés de privilèges élevés, l’authentification multifacteur est requise pour garantir une protection renforcée.

Les paramètres de rôle pour les rôles de ressources Azure (préversion) permettent aux administrateurs de configurer les paramètres d’affectation juste-à-temps et directe, notamment :

- La possibilité d’affecter des utilisateurs ou des groupes à des rôles sans date/heure de fin (affectation permanente)
- La durée par défaut d’une affectation (lorsqu’elle n’est pas permanente)
- La durée d’activation maximale (lorsqu’un membre active un rôle éligible)
- Les informations qu’un utilisateur doit fournir lors de l’activation du rôle (affectations juste-à-temps) ou du processus d’affectation (affectations directes)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Activation d’un rôle

Pour [activer un rôle](active-directory-privileged-identity-management-how-to-activate-role.md), un administrateur éligible demande une « activation » limitée dans le temps concernant ce rôle. L'activation peut être demandée à l'aide de l’option **Activate my role** dans Azure AD Privileged Identity Management.

Un administrateur qui souhaite activer un rôle doit initialiser Azure AD Privileged Identity Management sur le portail Azure.

L’activation de rôles est personnalisable. Dans les paramètres de PIM, vous pouvez définir la durée de l’activation, ainsi que les informations que l’administrateur doit fournir pour activer le rôle.

![Capture d’écran de demande d’activation de rôle d’administrateur dans PIM][5]

## <a name="review-role-activity"></a>Passer en revue les activités de rôle

Il existe deux méthodes pour suivre la façon dont vos employés et les administrateurs utilisent les rôles privilégiés. La première option consiste à utiliser l’[historique d’audit des rôles d’annuaire](active-directory-privileged-identity-management-how-to-use-audit-log.md). L’historique d’audit consigne le suivi des modifications des affectations de rôles privilégiés, l’historique de l’activation de rôles, ainsi que les modifications apportées aux paramètres pour les rôles de ressources Azure (préversion). 

![Capture d’écran de l’historique d’activation dans PIM][6]

La deuxième option consiste à configurer des [révisions d’accès](active-directory-privileged-identity-management-how-to-start-security-review.md)régulières. Ces révisions d’accès peuvent être effectuées par un réviseur affecté (par exemple, un gestionnaire d’équipe) ou par les employés eux-mêmes. C’est la méthode la plus adaptée pour déterminer les utilisateurs qui nécessitent l’accès et ceux qui n’en ont plus besoin.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM à l’expiration de l’abonnement

Votre organisation doit disposer d’un abonnement Azure AD Premium P2 (ou EMS E5) d’évaluation ou payant dans son client pour pouvoir utiliser Azure AD PIM.  En outre, les licences doivent être affectées aux administrateurs du client.  Plus précisément, les licences doivent être affectées aux administrateurs dans les rôles Azure AD gérés via Azure AD PIM, aux administrateurs dans les rôles RBAC Azure gérés via Azure AD PIM et à tous les utilisateurs non administrateurs qui effectuent des révisions d’accès.
Si votre organisation ne renouvelle pas son abonnement Azure AD Premium P2 ou si votre période d’évaluation expire, les fonctionnalités d’Azure AD PIM ne seront plus disponibles dans votre client, les affectations de rôles éligibles seront supprimées et les utilisateurs ne pourront plus activer de rôles. Vous trouverez plus d’informations à ce sujet dans les [spécifications de l’abonnement à Azure AD PIM](./privileged-identity-management/subscription-requirements.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
