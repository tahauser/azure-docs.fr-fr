---
title: "Démarrage d’une révision d’accès | Microsoft Docs"
description: "Découvrez comment créer une révision d'accès pour les identités privilégiées avec l’application Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 3e52b731-55f4-4c8a-ba87-9fd34033f52f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f57a32ca1914d18540289ebb05421a7ae9618094
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Comment démarrer une révision de l’accès dans Azure AD Privileged Identity Management ?
Les attributions de rôles deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès privilégié dont ils n’ont plus besoin. Pour réduire les risques associés à ces affectations de rôles « obsolètes », les administrateurs de rôle privilégié doivent régulièrement réviser les rôles qui ont été donnés aux utilisateurs. Ce document décrit les étapes de démarrage d’une révision d’accès dans Azure AD Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Démarrage d’une révision d’accès
> [!NOTE]
> Si vous n’avez pas ajouté l’application PIM à votre tableau de bord dans le Portail Azure, consultez les étapes dans [Prise en main d’Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)
> 
> 

Dans la page principale de l’application PIM, vous pouvez démarrer une révision d’accès de trois façons différentes :

* **Révisions d’accès** > **Ajouter**
* **Rôles** > bouton **Révision**
* Sélectionnez le rôle spécifique à réviser dans la liste des rôles > bouton **Révision**.

Lorsque vous cliquez sur le bouton **Révision**, le panneau **Démarrer une vérification d’accès** s’affiche. Dans ce panneau, vous allez configurer la révision avec un nom et une limite de temps, choisir un rôle à réviser, et nommer la personne qui effectuera la révision.

![Démarrage d’une vérification d’accès - capture d’écran][1]

### <a name="configure-the-review"></a>Configuration de la révision
Pour créer une révision d’accès, vous devez la nommer et définir une date de début et de fin.

![Configuration d’une révision - capture d’écran][2]

Prévoyez une période suffisamment longue pour permettre aux utilisateurs de terminer la révision. Si vous avez terminé avant la date de fin, vous pouvez toujours arrêter la révision plus tôt.

### <a name="choose-a-role-to-review"></a>Sélection d’un rôle à réviser
Chaque révision se concentre sur un seul rôle. À moins d’avoir démarré la révision d’accès à partir d’un panneau de rôle spécifique, vous devez maintenant choisir un rôle.

1. Navigation vers **Réviser une appartenance à un rôle**
   
    ![Réviser une appartenance à un rôle - capture d’écran][3]
2. Choisissez un rôle dans la liste.

### <a name="decide-who-will-perform-the-review"></a>Désignez la personne qui effectuera la révision
Il existe trois options pour effectuer une révision. Vous pouvez affecter la révision à quelqu’un d’autre, vous pouvez la faire vous-même ou vous pouvez demander à chaque utilisateur de réviser son propre accès.

1. Navigation vers **Sélectionner des réviseurs**
   
    ![Sélection des réviseurs - capture d’écran][4]
2. Choisissez l'une des options :
   
   * **Sélectionnez le réviseur**: utilisez cette option lorsque vous ne savez pas qui a besoin de l’accès. Avec cette option, vous pouvez affecter la révision à un propriétaire de ressource ou un responsable de groupe.
   * **Moi**: utile si vous souhaitez un aperçu du fonctionnement des révisions d’accès ou effectuer une révision à la place de personnes qui ne peuvent pas le faire.
   * **Auto-révision par les membres du rôle**: utilisez cette option pour demander aux utilisateurs de réviser leurs propres affectations de rôles.

### <a name="start-the-review"></a>Démarrage d’une révision
Enfin, vous pouvez obliger les utilisateurs à indiquer le motif pour lequel ils approuvent leur accès. Ajoutez une description de la révision si vous le souhaitez, puis sélectionnez **Démarrer**.

Assurez-vous d’informer vos utilisateurs qu’une révision d’accès les attend, puis montrez-leur [comment exécuter une révision d’accès](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès
Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD PIM, dans la section des révisions d'accès. Aucun droit d’accès ne sera modifié dans le répertoire avant que [la révision ne soit terminée](active-directory-privileged-identity-management-how-to-complete-review.md).

Tant que la période de révision n’est pas terminée, vous pouvez rappeler aux utilisateurs d’effectuer leur révision, ou arrêter la révision au début de la section des révisions d’accès.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>Sommaire PIM
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
