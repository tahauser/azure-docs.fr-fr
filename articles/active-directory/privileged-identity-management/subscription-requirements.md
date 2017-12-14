---
title: Abonnements Privileged Identity Management - Azure| Microsoft Docs
description: "Décrit les exigences en matières d’abonnement et de licences pour gérer utiliser Azure AD Privileged Identity Management dans votre locataire"
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: b04544c6a1d288524783ed6d323146c0ef7bfe95
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Exigences en matière d’abonnement d’Azure AD Privileged Identity Management

Azure AD Privileged Identity Management est disponible avec l’édition Premium P2 d’Azure AD. Pour plus d’informations sur les autres fonctionnalités de P2 et le comparer à Premium P1, consultez [Éditions d’Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Dans la version préliminaire d’Azure Active Directory (Azure AD) Privileged Identity Management, aucune vérification de la licence ne permettait à un locataire de tester le service.  Maintenant qu’Azure AD Privileged Identity Management est disponible de manière générale, le locataire doit disposer d’un abonnement d’évaluation ou payant pour continuer à utiliser Privileged Identity Management après décembre 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Confirmer votre abonnement d’évaluation ou payant

Si vous ne savez pas si votre organisation dispose d’un abonnement d’évaluation ou payant, vous pouvez vérifier s’il existe un abonnement dans votre locataire à l’aide des commandes incluses dans le module Azure Active Directory pour Windows PowerShell V1. 
1. Ouvrez une fenêtre PowerShell.
2. Entrez `Connect-MsolService` pour vous authentifier en tant qu’utilisateur de votre locataire.
3. Entrez `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Cette commande récupère la liste des abonnements dans votre locataire. Si aucune ligne n’est renvoyée, vous devez obtenir une version d’évaluation d’Azure AD Premium P2, acheter un abonnement Azure AD Premium P2 ou un abonnement ESM E5 pour pouvoir utiliser Azure AD Privileged Identity Management.  Pour obtenir une version d’évaluation et commencer à utiliser Azure AD Privileged Identity Management, lisez [Prise en main d’Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Si cette commande renvoie une ligne dans laquelle le SkuPartNumber est « AAD_PREMIUM_P2 » ou « EMSPREMIUM » et IsTrial est « True », cela signifie qu’une version d’évaluation d’Azure AD Premium P2 est présente dans le locataire.  Si l’état de l’abonnement n’est pas activé et si vous n’avez pas acheté d’abonnement Azure AD Premium P2 ou EMS E5, vous devez acheter un abonnement Azure AD Premium P2 ou EMS E5 pour continuer à utiliser Azure AD Privileged Identity Management.

Azure AD Premium P2 est disponible via un [Contrat Entreprise Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), le [programme de licence en volume Open](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) et le programme [Cloud Solution Provider](https://partner.microsoft.com/en-US/cloud-solution-provider). Les abonnés Azure et Office 365 peuvent également acheter Azure AD Premium P2 en ligne.  Vous trouverez plus d’informations sur la tarification d’Azure AD Premium et comment commander en ligne dans [Tarification Azure Active Directory](https://azure.microsoft.com/en-us/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management n’est pas disponible dans le locataire

Azure AD Privileged Identity Management ne sera plus disponible dans votre locataire si :
- Votre organisation utilisait la version préliminaire d’Azure AD Privileged Identity Management et n’a pas acheté d’abonnement Azure AD Premium P2 ou EMS E5.
- Votre organisation disposait d’une version d’évaluation d’Azure AD Premium P2 ou EMS E5 qui a expiré.
- Votre organisation a acheté un abonnement qui a expiré.

Lorsqu’un abonnement Azure AD Premium P2 ou EMS E5 arrive à expiration, ou qu’une organisation qui utilisait la version préliminaire d’Azure AD Privileged Identity Management n’achète pas d’abonnement Azure AD Premium P2 ou EMS E5 :

- Les affectations de rôle permanentes à des rôles Azure AD sont annulées.
- L’extension Azure AD Privileged Identity Management dans le portail Azure, ainsi que les applets de commande API Graph et les interfaces PowerShell d’Azure AD Privileged Identity Management, ne sont plus disponibles pour les utilisateurs afin d’activer des rôles privilégiés, de gérer l’accès privilégié ou de procéder à des vérifications d’accès des rôles privilégiés.
- Les affectations de rôle éligibles de rôles Azure AD sont supprimées, car les utilisateurs ne sont plus en mesure d’activer des rôles privilégiés.
- Les vérifications d’accès en cours de rôles sont terminées, et les paramètres de configuration d’Azure AD Privileged Identity Management sont supprimés.
- Azure AD Privileged Identity Management n’envoie plus de messages électroniques sur les modifications apportées aux affectations de rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main d’Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Rôles dans Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
