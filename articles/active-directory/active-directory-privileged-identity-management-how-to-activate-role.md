---
title: "Comment activer ou désactiver un rôle | Microsoft Docs"
description: "Découvrez comment activer des rôles pour des identités privilégiées avec l’application Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a524e1f9d739b42cd63de82291d98ff8c59f2f5f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Comment activer ou désactiver des rôles dans Azure AD Privileged Identity Management
Le service Azure Active Directory (AD) Privileged Identity Management simplifie la gestion par les entreprises de l’accès privilégié aux ressources dans Azure AD et d’autres services en ligne Microsoft tels qu’Office 365 ou que Microsoft Intune.  

Si vous êtes éligible à un rôle d’administrateur, vous pouvez activer ce rôle quand vous devez effectuer des actions privilégiées. Par exemple, si vous gérez occasionnellement des fonctionnalités d’Office 365, les administrateurs de rôle privilégié de votre organisation peuvent ne pas vous attribuer le rôle d’administrateur général permanent, étant donné que ce rôle affecte également les autres services. Au lieu de cela, ils peuvent vous attribuer des rôles Azure AD tels qu’administrateur Exchange Online. Vous pouvez demander d’activer ce rôle lorsque vous avez besoin de ces privilèges, et par conséquent, vous aurez le contrôle d’administration pendant une période prédéterminée.

Cet article est destiné aux administrateurs qui doivent activer leur rôle dans Azure AD Privileged Identity Management (PIM). Il vous guidera à travers les étapes pour activer un rôle lorsque vous avez besoin des autorisations et pour désactiver le rôle lorsque vous avez terminé. En outre, les administrateurs de rôle privilégié peuvent demander l’approbation pour activer un rôle (préversion). Pour en savoir plus sur les flux de travail d’approbation PIM, consultez [cet article](./privileged-identity-management/azure-ad-pim-approval-workflow.md).

## <a name="add-the-privileged-identity-management-application"></a>Ajout de l’application Privileged Identity Management
Utilisez l’application Azure AD Privileged Identity Management dans le [portail Azure](https://portal.azure.com/) pour demander une activation de rôle, même si vous allez utiliser un autre portail ou PowerShell. Si vous n'avez pas l'application Azure AD Privileged Identity Management sur votre portail Azure, procédez comme suit pour commencer.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure, puis choisissez le répertoire que vous allez utiliser.
3. Sélectionnez **Tous les services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

## <a name="activate-a-role"></a>Activer un rôle
Quand vous avez besoin d’assumer un rôle, vous pouvez demander une activation en sélectionnant l’option de navigation **Mes rôles** dans la colonne de navigation de gauche de l’application Azure AD Privileged Identity Management.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez la mosaïque Azure AD Privileged Identity Management.
2. Sélectionnez **Mes rôles**. Une liste de vos rôles éligibles affectés apparaît dans le regroupement en haut de la page.
3. Sélectionnez un rôle à activer.
4. Sélectionnez **Activer**. Le panneau **Demande d’activation du rôle** s’affiche.
5. Certains rôles requièrent l’authentification multifacteur (MFA) avant de pouvoir activer le rôle. Vous ne devez vous authentifier qu’une seule fois par session.
   
    ![Vérification multifacteur avant activation du rôle : capture d’écran][2]
6. Entrez le motif de la demande d’activation dans le champ de texte.  Certains rôles vous obligent à fournir un numéro de ticket d’incident.
7. Sélectionnez **OK**.  Si le rôle ne nécessite pas d’approbation, il est activé et apparaît dans la liste des rôles actifs (juste sous la liste des affectations de rôles éligibles). Si [l’activation du rôle nécessite une approbation](./privileged-identity-management/azure-ad-pim-approval-workflow.md), une notification toast apparaît brièvement dans le coin supérieur droit de votre navigateur pour vous informer que la demande est en attente d’approbation.

    ![Notification de demande en attente - capture d’écran][3]

## <a name="deactivate-a-role"></a>Désactiver un rôle
Une fois qu’un rôle a été activé, il se désactive automatiquement quand sa limite de temps (durée éligible) est atteinte.

Si vous terminez vos tâches d’administration plus tôt que prévu, vous pouvez également désactiver un rôle manuellement dans l’application Azure AD Privileged Identity Management.  Sélectionnez **Mes rôles**, choisissez le rôle avec lequel vous en avez terminé dans le regroupement **Attributions de rôles actives**, puis sélectionnez **Désactiver**.  

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente
Au cas où vous n’auriez pas besoin de l’activation d’un rôle qui nécessite l’approbation, vous pouvez annuler une demande en attente à tout moment. Il vous suffit de sélectionner l’option de navigation **Mes rôles** dans la colonne de navigation de gauche de l’application Azure AD Privileged Identity Management.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez la mosaïque Azure AD Privileged Identity Management.
2. Sélectionnez **Mes rôles**. Une liste de vos rôles éligibles affectés apparaît dans le regroupement en haut de la page.
3. Sélectionnez un rôle
4. Sélectionnez la bannière **Activation en attente d’approbation** dans le panneau de détails de l’activation de rôle.
5. Sélectionnez **Annuler** en haut du panneau **En attente d’approbation**.

   ![Capture d’écran d’annulation de demande en attente][4]

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez en savoir plus sur Azure AD Privileged Identity Management, les liens suivants contiennent d'autres informations.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
