---
title: "Comment ajouter un abonnement Azure existant à votre répertoire Azure AD | Microsoft Docs"
description: "Comment ajouter un abonnement existant à votre répertoire Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 67df6d893c0770b9210fc73e96865d5c6396796c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Comment ajouter un abonnement Azure à Azure Active Directory
Cet article vous fournit des informations sur la relation entre un abonnement Azure et Azure Active Directory (Azure AD), et vous explique comment ajouter un abonnement existant à votre répertoire Azure AD. Votre abonnement Azure possède une relation d’approbation avec Azure AD, ce qui signifie qu’il fait confiance à l’annuaire pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent faire confiance au même répertoire, mais un abonnement ne fait confiance qu’à un seul répertoire. 

La relation d’approbation dont dispose un abonnement avec un répertoire diffère de la relation qu’il possède avec d’autres ressources dans Azure (sites web, bases de données etc.). En cas d’expiration d’un abonnement, l’accès aux autres ressources associées à cet abonnement s’arrête également. En revanche, un répertoire Azure AD reste dans Azure. Vous pouvez associer un autre abonnement à ce répertoire et le gérer à l’aide d’un autre abonnement.

Tous les utilisateurs disposent d'un répertoire de base unique qui les authentifie, mais ils peuvent également être invités dans d'autres répertoires. Dans Azure AD, vous pouvez voir les répertoires dont votre compte d’utilisateur est membre ou invité.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Pour ajouter un abonnement existant à votre répertoire Azure AD
Vous devez vous connecter avec un compte qui existe à la fois dans le répertoire actif auquel l’abonnement est associé et dans le répertoire dans lequel vous souhaitez l’ajouter. 

1. Connectez-vous au [Centre des comptes Azure](https://account.azure.com/Subscriptions) avec un compte qui est l’administrateur du compte de l’abonnement et dont vous souhaitez transférer la propriété de l’abonnement.
2. Vérifiez que l’utilisateur dont vous voulez être le propriétaire de l’abonnement est dans le répertoire cible.
3. Cliquez sur **Transférer la propriété de l’abonnement**.
4. Spécifiez le destinataire. Le destinataire reçoit automatiquement un e-mail contenant un lien d’acceptation.
5. Le destinataire clique sur le lien et suit les instructions, notamment pour la saisie des informations de paiement. Lorsque le destinataire a terminé, l’abonnement est transféré. 
6. Le répertoire par défaut de l’abonnement est modifié pour être remplacé par le répertoire contenant l’utilisateur.


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la façon de modifier les administrateurs d’un abonnement Azure, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
