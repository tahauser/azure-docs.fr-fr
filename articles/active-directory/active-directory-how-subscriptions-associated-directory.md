---
title: "Comment ajouter un abonnement Azure existant à votre répertoire Azure AD | Microsoft Docs"
description: "Comment ajouter un abonnement existant à votre répertoire Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e063e6a46b6b99c4bbe749347e6887a930adb882
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Comment associer ou ajouter un abonnement Azure à Azure Active Directory

Cet article vous fournit des informations sur la relation entre un abonnement Azure et Azure Active Directory (Azure AD), et vous explique comment ajouter un abonnement existant à votre répertoire Azure AD. Votre abonnement Azure possède une relation d’approbation avec Azure AD, ce qui signifie qu’il fait confiance à l’annuaire pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent faire confiance au même répertoire, mais un abonnement ne fait confiance qu’à un seul répertoire. 

La relation d’approbation dont dispose un abonnement avec un répertoire diffère de la relation qu’il possède avec d’autres ressources dans Azure (sites web, bases de données etc.). En cas d’expiration d’un abonnement, l’accès aux autres ressources associées à cet abonnement s’arrête également. En revanche, un répertoire Azure AD reste dans Azure. Vous pouvez associer un autre abonnement à ce répertoire et le gérer à l’aide d’un autre abonnement.

Tous les utilisateurs disposent d'un répertoire de base unique qui les authentifie, mais ils peuvent également être invités dans d'autres répertoires. Dans Azure AD, vous pouvez voir les répertoires dont votre compte d’utilisateur est membre ou invité.

## <a name="before-you-begin"></a>Avant de commencer

* Vous devez vous connecter avec un compte disposant de l’accès Propriétaire RBAC à l’abonnement.
* Vous devez vous connecter avec un compte qui existe à la fois dans le répertoire actif auquel l’abonnement est associé et dans le répertoire dans lequel vous souhaitez l’ajouter. Pour plus d’informations sur l’accès à un autre répertoire, consultez [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Pour associer un abonnement existant à votre annuaire Azure AD

1. Connectez-vous et sélectionnez un abonnement dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Cliquez sur **Modifier le répertoire**.

    ![Capture d’écran affichant le bouton Modifier le répertoire](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Passez en revue les avertissements. Tous les utilisateurs [RBAC (contrôle d’accès en fonction du rôle)](role-based-access-control-configure.md) disposant d’un accès et tous les administrateurs d’abonnement perdent leur accès si le répertoire d’abonnement est modifié.
4. Sélectionnez un répertoire.

    ![Capture d’écran affichant l’interface utilisateur Modifier le répertoire](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Cliquez sur **Modifier**.
6. Vous avez réussi ! Utilisez le sélecteur de répertoire pour passer au nouveau répertoire. 10 minutes peuvent être nécessaire pour tout afficher correctement.

    ![Capture d’écran affichant la notification de réussite de modification du répertoire](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Capture d’écran affichant le sélecteur](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


La modification du répertoire d’abonnement est une opération qui s’effectue au niveau du service. Elle n’affecte pas la propriété de facturation de l’abonnement, et l’administrateur de compte peut toujours changer l’administrateur du service dans le [Centre des comptes](https://account.azure.com/subscriptions). Si vous souhaitez supprimer le répertoire d’origine, vous devez transférer la propriété de facturation de l’abonnement à un nouvel administrateur du compte. Pour en savoir plus sur le transfert de la propriété de facturation, consultez [Transfert de la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création d’un annuaire Azure AD gratuit, consultez [Obtention d’un client Azure Active Directory](develop/active-directory-howto-tenant.md)
* Pour en savoir plus sur le transfert de la propriété de facturation d’un abonnement Azure, consultez [Transfert de la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md).
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
