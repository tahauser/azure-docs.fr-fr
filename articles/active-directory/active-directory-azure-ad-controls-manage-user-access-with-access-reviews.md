---
title: "Gérer les accès des utilisateurs avec les révisions d’accès Azure AD | Microsoft Docs"
description: "Découvrez comment gérer les accès utilisateurs en tant qu’appartenance à un groupe ou en tant qu’affectation à une application avec les révisions d’accès Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 6a4d25b2eb228cafab48419a0d0eda92bba9f1ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gérer les accès des utilisateurs avec les révisions d’accès Azure AD

Avec Azure Active Directory (Azure AD), vous pouvez facilement vous assurer que les utilisateurs ont un accès approprié. Vous pouvez demander directement aux utilisateurs ou bien à un décisionnaire de prendre part à une révision d’accès et de renouveler la certification (ou d’effectuer une « attestation ») pour l’accès utilisateur. Les réviseurs peuvent donner leur avis sur les besoins de chaque utilisateur en matière d’accès continu, en fonction des suggestions d’Azure AD. Lorsqu’une révision d’accès est terminée, vous pouvez effectuer des modifications et supprimer l’accès des utilisateurs qui n’en ont plus besoin.

> [!NOTE]
> Si vous souhaitez examiner uniquement les accès des utilisateurs invités, plutôt que tous les types d’accès utilisateurs, consultez [Gestion des accès utilisateurs invités avec les révisions d’accès](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). Si vous voulez examiner l’appartenance d’un utilisateur à un rôle d’administrateur tel que le rôle Administrateur général, consultez [Démarrer une révision d’accès dans Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Composants requis 

Les révisions d’accès sont disponibles dans l’édition Premium P2 de Azure AD, incluse dans Microsoft Enterprise Mobility + Security, E5. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md). Chaque utilisateur interagissant avec cette fonctionnalité pour créer une révision, y accéder ou bien l’appliquer, doit disposer d’une licence.


## <a name="create-and-perform-an-access-review"></a>Créer et exécuter une révision d’accès

Vous pouvez avoir un ou plusieurs utilisateurs comme réviseurs dans une révision d’accès.  

1. Sélectionnez un groupe dans Azure AD comprenant un ou plusieurs membres. Ou bien sélectionnez une application connectée à Azure AD ayant un ou plusieurs utilisateurs attribués. 

2. Décidez si vous souhaitez que chaque utilisateur révise son propre accès, ou si vous voulez qu’un ou plusieurs utilisateurs révisent l’accès de tout le monde.

3. Activez les révisions d’accès pour qu’elles apparaissent sur les volets d’accès du réviseur. En tant qu’administrateur global, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Démarrez la révision d’accès. Pour plus d’informations, consultez [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).

5. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux reçoit un e-mail de la part de Azure AD contenant un lien vers le volet d’accès, dans lequel ils peuvent [effectuer leur révision d’accès](active-directory-azure-ad-controls-perform-access-review.md).

6. Si les réviseurs n’ont pas donné leur avis, vous pouvez demander à Azure AD de leur envoyer un rappel. Par défaut, Azure AD envoie automatiquement un rappel à mi-parcours de l’échéance fixée pour les réviseurs n’ayant pas encore répondu.

7. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Terminer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Étapes suivantes

[Create an access review for members of a group or access to an application](active-directory-azure-ad-controls-create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)




