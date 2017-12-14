---
title: "Notifications d’approvisionnement de comptes| Microsoft Docs"
description: "Découvrez comment vous assurer d’être informé des problèmes liés à l’approvisionnement des utilisateurs qui nécessitent votre attention en activant les notifications d’approvisionnement de comptes."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: d056ed0b7030079f3ea47a56c65e08a9ecfe4583
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="account-provisioning-notifications"></a>Notifications d’approvisionnement de comptes
Avec l’approvisionnement d’utilisateurs, vous pouvez automatiser le processus de gestion des utilisateurs dans les applications SaaS tierces. <br>
Bien qu’il soit automatisé, votre interaction avec ce processus est parfois nécessaire. <br>
C’est par exemple le cas quand le mot de passe du compte que vous avez configuré pour échanger des données avec une application SaaS tierce a expiré. 

En activant les notifications d’approvisionnement de comptes, vous pouvez vous assurer d’être informé des problèmes liés à l’approvisionnement des utilisateurs qui nécessitent votre attention.

Vous activez ou désactivez les notifications d’approvisionnement de comptes dans le cadre de votre configuration d’approvisionnement d’utilisateurs pour une application SaaS tierce.

![Approvisionnement d’utilisateurs][1] 

Pour activer les notifications d’approvisionnement de comptes, cochez la case associée dans la page de boîte de dialogue **Confirmation** , puis tapez l’alias de messagerie du destinataire.

![Notifications d’approvisionnement de comptes][2]

Vous pouvez entrer une liste de distribution comme destinataire ; toutefois, il convient de noter que le message électronique de notification contient des liens vers des rapports qui sont accessibles uniquement aux administrateurs Azure AD.

Si vous avez activé les notifications d’approvisionnement de comptes, vous recevrez des messages électroniques à propos des problèmes critiques liés à l’approvisionnement des utilisateurs. Cependant, pour éviter une surcharge de courrier électronique, vous recevrez un seul message de notification par jour pour chaque application SaaS pour laquelle la notification par courrier électronique est activée.

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png
