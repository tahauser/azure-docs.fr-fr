---
title: "Réviser les accès avec les révisions d’accès Azure AD | Microsoft Docs"
description: "Découvrez comment réviser les accès à l’aide des révisions d’accès Azure Active Directory."
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
ms.openlocfilehash: 98658a2b9fdd0ede98b3f6c10a19af527a8677cc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Réviser les accès avec les révisions d’accès Azure AD

Azure Active Directory (Azure AD) simplifie la manière dont les entreprises gèrent l’accès aux applications et aux membres des groupes dans Azure AD et d’autres services en ligne Microsoft, avec une fonction appelée révision d’accès. Vous avez peut-être reçu un e-mail de la part de Microsoft vous invitant à réviser l’accès, les membres d’un groupe ou les utilisateurs ayant accès à une application. 

## <a name="open-an-access-review"></a>Ouvrir une révision d’accès

Pour voir les révisions d’accès en attente, cliquez sur le lien contenu dans l’e-mail. Si vous n’avez pas reçu l’e-mail, vous pouvez localiser les révisions d’accès en procédant comme suit :

1. Connectez-vous au [panneau d’accès Azure AD](https://myapps.microsoft.com).

2. Sélectionnez le symbole d’utilisateur dans le coin supérieur droit de la page, qui affiche votre nom et organisation par défaut. Si plusieurs organisations sont listées, sélectionnez l’organisation qui a demandé une révision d’accès.

3. Si une vignette étiquetée **Révisions d’accès** figure à droite de la page, cliquez dessus. Si la vignette n’est pas visible, cela signifie qu’il n’y a pas de révisions d’accès à effectuer pour cette organisation et qu’aucune action n’est nécessaire pour l’instant.

## <a name="fill-out-an-access-review"></a>Remplir une révision d’accès

Lorsque vous sélectionnez une révision d’accès dans la liste, vous obtenez les noms des utilisateurs qui doivent être révisés. Si la demande portait sur la révision d’un de vos propres accès, vous ne verrez peut-être que votre nom.

Pour chaque ligne de la liste, vous pouvez décider d’accepter ou de refuser l’accès de l’utilisateur. Sélectionnez la ligne et choisissez d’accepter ou de refuser. (Si vous ne connaissez pas l’utilisateur, vous pouvez aussi l’indiquer.)

Le réviseur peut demander que vous fournissiez une justification en cas d’acceptation d’un accès permanent ou d’une appartenance à un groupe.

## <a name="next-steps"></a>Étapes suivantes

Un accès refusé pour un utilisateur n’est pas supprimé immédiatement. Il peut être supprimé une fois la révision terminée ou lorsqu’un administrateur interrompt la révision. Si vous souhaitez modifier votre réponse et accepter un utilisateur précédemment refusé, ou refuser un utilisateur accepté précédemment, sélectionnez la ligne, réinitialisez la réponse et choisissez une nouvelle réponse. Vous pouvez suivre cette procédure jusqu'à la fin de la révision d’accès.



