---
title: "Créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application avec Azure AD| Microsoft Docs"
description: "Découvrez comment créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application."
services: active-directory
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
ms.openlocfilehash: b2f8985f12e17ac69543cfb3a33725f796eedde8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Créer une révision d’accès des membres du groupe ou un accès aux applications avec Azure AD

Les attributions d’accès deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès dont ils n’ont plus besoin. Afin de réduire les risques associés aux affectations d’accès par état, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer une révision d’accès des membres du groupe ou des utilisateurs affectés à une application. Pour plus d’informations sur ces scénarios, voir [Gérer l’accès des utilisateurs](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) et [Gérer l’accès des invités](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Créer une révision d’accès

1. En tant qu’administrateur général, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) et sélectionnez **Programmes**.

2. Sélectionnez le programme qui contient le contrôle de révision d’accès que vous souhaitez créer. Le **programme par défaut »** est toujours présent, mais vous pouvez aussi créer un autre programme. Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise.

3. Dans le programme, sélectionnez **Contrôles**, puis sélectionnez **Ajouter** pour ajouter un contrôle.

4. Nommez chaque révision d’accès. Vous pouvez éventuellement fournir une description pour chaque révision. Le nom est visible par les réviseurs.

5. Définissez les dates de début et de fin. Par défaut, une révision d’accès démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

6. Les révisions d’accès peuvent porter sur les membres d’un groupe ou sur les utilisateurs qui ont été assignés à une application. Vous pouvez affiner davantage la révision d’accès pour passer en revue uniquement les utilisateurs invités qui sont membres (ou assignés à l’application) au lieu d’examiner tous les utilisateurs qui sont membres ou qui ont accès à l’application.

7. Sélectionnez une ou plusieurs personnes pour passer en revue tous les utilisateurs concernés. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès. Si la ressource est un groupe, vous pouvez demander aux propriétaires de groupe d’effectuer la révision. Vous pouvez également demander à ce que les réviseurs fournissent un motif lorsqu’ils approuvent l’accès.

8. Pour finir, sélectionnez **Démarrer**.


## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur afficher des instructions relatives à la [révision d’accès](active-directory-azure-ad-controls-perform-access-review.md). Si votre révision s’adresse à des invités qui doivent réviser leur propre accès, donnez-leur des instructions sur la [façon de réviser son propre accès](active-directory-azure-ad-controls-perform-access-review.md).

Si certains réviseurs sont invités, ces derniers sont uniquement notifiés par e-mail s’ils ont déjà accepté leur invitation.


Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD, dans la section des **Révisions d’accès**. Aucun droit d’accès n’est modifié dans le répertoire avant que [la révision ne soit terminée](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Étapes suivantes

Lorsqu’une révision d’accès a démarré, Azure AD envoie automatiquement aux réviseurs un e-mail les invitant à réviser l’accès. Si un utilisateur n’a pas reçu d’e-mail, vous pouvez lui envoyer les instructions sur la [façon de réviser un accès](active-directory-azure-ad-controls-perform-access-review.md). 

Une fois la période de révision d’accès terminée ou si l’administrateur interrompt la révision d’accès, suivez les étapes décrivant la [réalisation d’une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md) pour voir et appliquer les résultats.


