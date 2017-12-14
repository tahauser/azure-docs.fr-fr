---
title: "Compléter une révision de sécurité | Microsoft Docs"
description: "Après avoir démarré une vérification d’accès dans Azure AD Privileged Identity Management, découvrez comment la compléter et afficher les résultats"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: 3866438de8fba7a6c42777bbb57746eadf1158eb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Comment effectuer une révision de l’accès dans Azure AD Privileged Identity Management ?
Les administrateurs de rôle privilégié peuvent examiner un accès privilégié une fois qu’une [révision de la sécurité a été démarrée](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) envoie automatiquement un e-mail demandant aux utilisateurs d'examiner leur accès. Si un utilisateur n'a pas reçu d’e-mail, vous pouvez lui envoyer les instructions dans [comment effectuer une révision de sécurité](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Après la période de révision de sécurité, ou après que tous les utilisateurs ont terminé leur auto-examen, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## <a name="manage-security-reviews"></a>Gérer les révisions de sécurité
1. Accédez à la [portail Azure](https://portal.azure.com/) et sélectionnez l’application **Azure AD Privileged Identity Management** sur votre tableau de bord.
2. Sélectionnez la section **Révisions de l’accès** du tableau de bord.
3. Sélectionnez la révision d’accès que vous souhaitez gérer.

Le panneau de détails de la révision d’accès comporte plusieurs options pour la gestion de cette révision.

![Boutons de révision d’accès PIM - capture d’écran][1]

### <a name="remind"></a>Rappeler
Si une vérification de l’accès est configurée de sorte que les utilisateurs doivent eux-mêmes la réviser, le bouton **Rappeler** envoie une notification. 

### <a name="stop"></a>Arrêter
Toutes les révisions d’accès ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour les terminer plus tôt. Si les utilisateurs n’ont pas été révisés à cette date, ils ne pourront plus le faire une fois la révision arrêtée. Vous ne pouvez pas redémarrer une révision arrêtée.

### <a name="apply"></a>Appliquer
Lorsqu’une vérification de l’accès est terminé, soit parce qu’elle a atteint la date de fin ou après un arrêt manuel, le bouton **Appliquer** implémente le résultat de la révision. Si l’accès d’un utilisateur a été refusé lors de la révision, cette étape supprimera l’affectation de son rôle.  

### <a name="export"></a>Exportation
Si vous souhaitez appliquer manuellement les résultats de la révision de sécurité, vous pouvez exporter la révision. Le bouton **Exporter** fait démarrer le téléchargement d'un fichier CSV. Vous pouvez gérer les résultats dans Excel ou d'autres programmes qui s'ouvrent les fichiers CSV.

### <a name="delete"></a>Supprimer
Si vous n'êtes plus intéressé par la révision, supprimez-la. Le bouton **Supprimer** permet de supprimer la révision de l'application PIM.

> [!IMPORTANT]
> La suppression ayant lieu sans affichage préalable d’un avertissement, assurez-vous que vous voulez supprimer cette révision. 

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
