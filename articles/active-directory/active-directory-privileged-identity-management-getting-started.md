---
title: "Prise en main d’Azure AD Privileged Identity Management | Microsoft Docs"
description: "Découvrez comment gérer des identités privilégiées avec l’application Azure Active Directory Privileged Identity Management dans le portail Azure."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9f51daabef7d1e02917869e4e6943b8ea28b56f5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Commencer à utiliser Azure AD Privileged Identity Management

Avec Azure Active Directory (AD) Privileged Identity Management, vous pouvez gérer, contrôler et surveiller l’accès au sein de votre organisation. Cette étendue inclut l’accès aux ressources Azure, à Azure AD et à d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

Cet article vous indique comment ajouter l'application Azure AD PIM à votre tableau de bord du portail Azure.

## <a name="add-the-privileged-identity-management-application"></a>Ajout de l’application Privileged Identity Management

Avant d'utiliser Azure AD Privileged Identity Management, vous devez ajouter l'application à votre tableau de bord du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure. Sélectionnez l’annuaire dans lequel vous souhaitez utiliser PIM.
3. Sélectionnez **Tous les services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

Si vous êtes la première personne à utiliser Azure AD Privileged Identity Management dans votre annuaire, les rôles **Administrateur de sécurité** et **Administrateur de rôle privilégié** vous sont automatiquement attribués pour cet annuaire. Seuls les administrateurs de rôle privilégié peuvent gérer les attributions de rôles d’annuaire Azure AD d’utilisateurs. En outre, vous pouvez choisir d’exécuter [l’Assistant Sécurité.](active-directory-privileged-identity-management-security-wizard.md) qui vous guide tout au long du processus de découverte et d’attribution.

## <a name="navigate-to-your-tasks"></a>Accédez à vos tâches

Une fois Azure AD Privileged Identity Management configuré, le panneau de navigation s’affiche chaque fois que vous ouvrez l’application. Utilisez ce panneau pour accomplir vos tâches de gestion des identités.

![Tâches de niveau supérieur pour PIM - capture d’écran](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- **Mes rôles** affiche la liste des rôles éligibles et actifs qui vous sont attribués. C’est là où vous pouvez activer tout rôle éligible attribué.
- **Approuver les demandes (préversion)** affiche la liste des demandes d’activation des rôles d’annuaire Azure AD éligibles par utilisateur dans votre annuaire. Vous avez été désigné pour approuver ces demandes. [En savoir plus.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- **Demandes en attente	 (préversion)** affiche les demandes d’activation en attente des attributions de rôles éligibles.
- **Revoir l’accès** répertorie les révisions d’accès actives (pour vous-même ou quelqu’un d’autre) qui vous ont été attribuées.
- **Rôles d’annuaire Azure AD**, situé sous la section Gérer du menu de navigation gauche, affiche le tableau de bord utilisé par les administrateurs de rôle privilégié pour gérer les attributions de rôle, changer les paramètres d’activation de rôle, démarrer des révisions d’accès, etc. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire).
- Les **rôles de ressources Azure (préversion)** situés sous la section Gérer du menu de navigation gauche affichent la liste de ressources d’abonnement pour lesquelles vous avez des attributions de rôles. 

## <a name="next-steps"></a>Étapes suivantes
La [vue d’ensemble Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclut plus de détails sur la manière de gérer un accès administrateur dans votre organisation.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
