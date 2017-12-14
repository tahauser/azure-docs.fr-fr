---
title: "Gérer l’accès invité avec les révisions d’accès Azure AD | Documents Microsoft"
description: "Gérer les utilisateurs invités en tant que membres d’un groupe ou affectés à une application avec les révisions d’accès Azure Active Directory"
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
ms.openlocfilehash: 8d5cc8035d085ac9c8fc46077376836726afbb1a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gérer l’accès invité avec les révisions d’accès Azure AD


Avec Azure Active Directory (Azure AD), vous pouvez facilement mettre en place une collaboration au-delà des limites de l’organisation à l’aide de la [fonctionnalité B2B d’Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Les utilisateurs invités depuis d’autres locataires peuvent être [invités par les administrateurs](active-directory-b2b-admin-add-users.md) ou par [d’autres utilisateurs](active-directory-b2b-how-it-works.md). Cela s’applique également aux identités de réseaux sociaux tels que des comptes Microsoft.

Vous pouvez également facilement vous assurer que les utilisateurs invités disposent de droits d’accès appropriés. Vous pouvez demander directement aux invités ou bien à un décisionnaire de prendre part à une révision d’accès et de renouveler la certification (ou d’effectuer une « attestation ») pour l’accès invité. Les réviseurs peuvent donner leur avis sur les besoins de chaque utilisateur en matière d’accès continu, en fonction des suggestions d’Azure AD. Lorsqu’une révision d’accès est terminée, vous pouvez effectuer des modifications et supprimer l’accès des invités qui n’en ont plus besoin.

> [!NOTE]
> Ce document se concentre sur la revue de l’accès des utilisateurs invités. Si vous souhaitez réviser l’accès de tous les utilisateurs, et pas uniquement celui des invités, consultez l’article [Manage user access with access reviews](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) (Gérer l’accès utilisateur à l’aide des révisions d’accès). Si vous voulez examiner l’appartenance d’un utilisateur à un rôle d’administrateur tel que le rôle Administrateur général, consultez [Démarrer une révision d’accès dans Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Composants requis 

Les révisions d’accès sont disponibles dans l’édition Premium P2 de Azure AD, incluse dans Microsoft Enterprise Mobility + Security, E5. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md). Chaque utilisateur interagissant avec cette fonctionnalité pour créer une révision, y accéder ou bien l’appliquer, doit disposer d’une licence.

Si vous envisagez de demander aux utilisateurs invités de revoir leur propre accès, apprenez-en davantage sur les licences d’utilisateur invité. Pour plus d’informations, consultez l’article [Affectation de licences Azure Active Directory B2B Collaboration](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Créer et exécuter une révision d’accès pour des invités

Tout d’abord, activez les révisions d’accès pour qu’elles apparaissent dans les volets d’accès du réviseur. En tant qu’administrateur global, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD permet plusieurs scénarios de revue des utilisateurs invités.

Sélectionnez l’un des suivants :

 - Un groupe dans Azure AD comprenant un ou plusieurs invités comme membres.
 - Une application connectée à Azure AD ayant un ou plusieurs utilisateurs invités attribués. 

Vous pouvez alors décider de demander à chaque invité de revoir leur propre accès ou de demander à un ou plusieurs utilisateurs de revoir l’accès de chaque invité.

 Ces scénarios sont traités dans les sections suivantes.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Demander aux invités de revoir leur propre appartenance à un groupe

Vous pouvez utiliser les révisions d’accès pour vous assurer que les utilisateurs qui ont été invités et ajoutés à un groupe ont toujours besoin de leur accès. Vous pouvez facilement demander aux invités de revoir leur propre appartenance à ce groupe.

1. Pour démarrer une révision d’accès pour le groupe, choisissez d’y inclure uniquement les utilisateurs invités et faites-leur faire la révision eux-mêmes. Pour plus d’informations, consultez [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).

2. Demandez à chaque invité de revoir leur propre appartenance. Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part d’Azure AD comprenant un lien vers la révision d’accès. Azure AD dispose d’instructions destinées aux invités concernant [la vérification de leur accès](active-directory-azure-ad-controls-perform-access-review.md).

3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Terminer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

4. En plus des utilisateurs indiquant n’avoir plus besoin d’un accès continu, vous pouvez également retirer les utilisateurs n’ayant pas répondu. Il est probable que les utilisateurs qui ne répondent pas ne reçoivent plus de courrier électronique.

5. Si le groupe n’est pas utilisé pour la gestion des accès, vous pouvez également retirer les utilisateurs qui n’ont pas été sélectionnés pour participer à la révision car ils n’ont pas accepté leur invitation. Cela peut être dû à une faute de frappe dans l’adresse e-mail de l’utilisateur invité. Si un groupe est utilisé comme liste de distribution, il est possible que certains utilisateurs invités n’aient pas été sélectionnés pour participer parce qu’ils sont des objets contact.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Demander à un commanditaire de revoir l’appartenance d’un invité à un groupe

Vous pouvez demander à un commanditaire, tel que le propriétaire d’un groupe, d’évaluer si un invité a toujours besoin d’appartenir à un groupe.

1. Pour démarrer une révision d’accès pour le groupe, choisissez d’y inclure uniquement les utilisateurs invités. Spécifiez ensuite un ou plusieurs réviseurs. Pour plus d’informations, consultez [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).

2. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux reçoit un e-mail de la part de Azure AD contenant un lien vers le volet d’accès, dans lequel ils peuvent [effectuer leur révision d’accès](active-directory-azure-ad-controls-perform-access-review.md).

3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Terminer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Demander aux invités de revoir leur propre accès à une application

Vous pouvez utiliser les révisions d’accès pour vous assurer que les utilisateurs qui ont été invités pour une application spécifique ont toujours besoin de leur accès. Vous pouvez facilement demander aux invités de revoir eux-mêmes leur besoin d’accès.

1. Pour démarrer une révision d’accès pour l’application, choisissez d’y inclure uniquement les invités et de leur faire revoir leur accès eux-mêmes. Pour plus d’informations, consultez [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).

2. Demandez à chaque invité de revoir son propre accès à l’application. Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part d’Azure AD comprenant un lien vers la révision d’accès dans le panneau d’accès de votre organisation. Azure AD dispose d’instructions destinées aux invités concernant [la vérification de leur accès](active-directory-azure-ad-controls-perform-access-review.md).

3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Terminer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

4. En plus des utilisateurs indiquant n’avoir plus besoin d’un accès continu, vous pouvez également retirer les utilisateurs invités n’ayant pas répondu. Il est probable que les utilisateurs qui ne répondent pas ne reçoivent plus de courrier électronique. Vous pouvez également supprimer les utilisateurs invités qui n’ont pas été sélectionnées pour participer, surtout s’ils n’ont pas été récemment invités. N’ayant pas accepté leur invitation, ces utilisateurs n’avaient donc pas accès à l’application. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Demander à un commanditaire de revoir l’accès d’un invité à une application

Vous pouvez demander à un commanditaire, tel que le propriétaire d’une application, de revoir le besoin d’un invité à disposer d’un accès continu à l’application.

1. Pour démarrer une révision d’accès pour l’application, choisissez d’y inclure uniquement les invités. Définissez ensuite un ou plusieurs utilisateurs comme réviseurs. Pour plus d’informations, consultez [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).

2. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux reçoit un e-mail de la part de Azure AD contenant un lien vers le volet d’accès, dans lequel ils peuvent [effectuer leur révision d’accès](active-directory-azure-ad-controls-perform-access-review.md).

3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [Terminer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Demander aux invités de revoir leurs besoins d’accès en général

Dans certaines organisations, les invités peuvent ne pas être conscients de leur appartenance à un groupe.

> [!NOTE]
> Dans les versions antérieures du portail Azure, les utilisateurs de type Invité ne pouvaient pas disposer d’un accès administratif. Dans certains cas, il est possible qu’un administrateur de votre répertoire ait défini la valeur UserType d’un invité sur Membre à l’aide de PowerShell. Si cette modification avait auparavant été apportée dans votre répertoire, il est possible que la requête précédente n’inclue pas tous les utilisateurs invités qui disposaient auparavant de droits d’accès administratif. Dans ce cas, vous devez modifier le type d’utilisateur de l’invité ou inclure manuellement l’invité dans le groupe.

1. Créez un groupe de sécurité dans Azure AD avec les invités comme membres, si un groupe approprié n’existe pas déjà. Par exemple, vous pouvez créer un groupe pour lequel l’appartenance des invités est gérée manuellement. Ou bien, vous pouvez créer un groupe dynamique doté d’un nom tel que « Invités de Contoso » pour les utilisateurs dans le client Contoso dont la valeur d’attribut UserType est configurée sur Invité.

2. Pour démarrer une révision d’accès pour ce groupe, choisissez les membres comme réviseurs. Pour plus d’informations, consultez [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).

3. Demandez à chaque invité de revoir leur propre appartenance. Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part d’Azure AD comprenant un lien vers la révision d’accès dans le panneau d’accès de votre organisation. Azure AD dispose d’instructions destinées aux invités concernant [la vérification de leur accès](active-directory-azure-ad-controls-perform-access-review.md).

4. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès. Pour plus d’informations, consultez [Terminer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

5. Retirez l’accès invité aux invités ayant été refusés, n’ayant pas terminé la révision ou bien n’ayant pas accepté leur invitation. Si certains des invités sont des contacts sélectionnés pour participer à la révision car ils n’avaient précédemment pas accepté une invitation, vous pouvez désactiver leur compte à l’aide du portail Azure ou de PowerShell. Si l’invité n’a plus besoin d’un accès et n’est pas un contact, vous pouvez supprimer son objet utilisateur de votre répertoire à l’aide du portail Azure ou de PowerShell.

## <a name="next-steps"></a>Étapes suivantes

[Create an access review for members of a group or access to an application](active-directory-azure-ad-controls-create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)







