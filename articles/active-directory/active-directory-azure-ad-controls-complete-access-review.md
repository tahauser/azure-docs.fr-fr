---
title: "Réviser les accès à une application des membres d’un groupe ou des utilisateurs avec Azure AD| Microsoft Docs"
description: "Découvrez comment réviser les accès des membres d’un groupe ou des utilisateurs ayant accès à une application dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: de853d633aa65c9f08f5e28088d5240c2e4d7fa6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Effectuer une révision d’accès des membres d’un groupe ou des accès des utilisateurs à une application dans Azure AD

Les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour [créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md) des membres du groupe ou des utilisateurs affectés à une application. Azure AD envoie automatiquement aux réviseurs un e-mail les invitant à réviser l’accès. Si un utilisateur n’a pas reçu d’e-mail, vous pouvez lui envoyer les instructions décrites dans l’article [Effectuer votre propre révision d’accès](active-directory-azure-ad-controls-perform-access-review.md). Si la période de révision d’accès est terminée ou que l’administrateur a arrêté la révision d’accès, suivez les étapes décrites dans cet article pour voir et appliquer les résultats.

## <a name="view-an-access-review-in-the-azure-portal"></a>Afficher une révision d’accès dans le portail Azure

1. Accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), sélectionnez **Programmes**, puis choisissez le programme qui contient le contrôle de révision d’accès.

2. Cliquez sur **Gérer** et sélectionnez le contrôle de révision d’accès. S’il existe de nombreux contrôles dans le programme, vous pouvez appliquer un filtre pour n’afficher que les contrôles d’un type spécifique et les trier selon leur état. Vous pouvez également effectuer une recherche en saisissant le nom du contrôle de révision d’accès ou le nom complet de son auteur. 

## <a name="stop-a-review-that-hasnt-finished"></a>Arrêter une révision qui n’est pas terminée

Si la révision n’a pas encore atteint sa date de fin planifiée, un administrateur peut l’arrêter plus tôt en sélectionnant **Arrêter**. Une fois la révision arrêtée, il n’est plus possible de vérifier l’accès des utilisateurs. Vous ne pouvez pas redémarrer une révision ayant été arrêtée.

## <a name="apply-the-changes"></a>Appliquer les modifications 

Une fois la révision d’accès terminée, c’est-à-dire lorsque celle-ci a atteint sa date de fin ou a été manuellement arrêtée par un administrateur, sélectionnez **Appliquer**. Le résultat de la révision est implémenté via une mise à jour du groupe ou de l’application. Si l’accès d’un utilisateur a été refusé lors de la révision, lorsque l’administrateur sélectionne cette option, Azure AD supprime l’appartenance de cet utilisateur ou son assignation à l’application. 

Sélectionner l’option **Appliquer** n’aura pas d’effet pour un groupe qui provient d’un répertoire local ou d’un groupe dynamique. Si vous souhaitez modifier un groupe qui provient d’un répertoire local, téléchargez les résultats et appliquez ces modifications à la représentation du groupe dans ce répertoire.

## <a name="download-the-results-of-the-review"></a>Télécharger les résultats de la révision

Pour récupérer les résultats de la révision, sélectionnez **Approbations**, puis cliquez sur **Télécharger**. Le fichier CSV généré peut être affiché dans Excel ou dans d’autres programmes qui permettent d’ouvrir les fichiers CSV.

## <a name="optional-delete-a-review"></a>Facultatif : supprimer une révision
Si la révision ne vous intéresse plus, vous pouvez la supprimer. Sélectionnez **Supprimer** pour supprimer la révision d’Azure AD.

> [!IMPORTANT]
> Il n’y a pas d’avertissement avant la suppression. Par conséquent, assurez-vous de bien vouloir supprimer cette révision.
> 
> 

## <a name="next-steps"></a>Étapes suivantes

- [Manage user access with Azure AD access reviews](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) (Gérer l’accès des utilisateurs avec les révisions d’accès Azure AD)
- [Manage guest access with Azure AD access reviews](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) (Gérer l’accès des invités avec les révisions d’accès Azure AD)
- [Manage programs and controls for Azure AD access reviews](active-directory-azure-ad-controls-manage-programs-controls.md) (Gérer les programmes et les contrôles pour la révision d’accès Azure AD)
- [Create an access review for members of a group or access to an application](active-directory-azure-ad-controls-create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)
- [Create an access review of users in an Azure AD administrative role](active-directory-privileged-identity-management-how-to-start-security-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
