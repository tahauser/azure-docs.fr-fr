---
title: "Conditions dans l’accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Découvrez comment les affectations sont utilisées dans l’accès conditionnel Azure Active Directory pour déclencher une stratégie."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2415a2c2c0143b4abeb8ec1ecab379a204456874
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Conditions dans l’accès conditionnel Azure Active Directory 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Dans une stratégie d’accès conditionnel, vous définissez la réponse (« faire ») sur la raison du déclenchement de votre stratégie (« quand cela se produit »). 

![Contrôle](./media/active-directory-conditional-access-conditions/10.png)


Dans le contexte de l’accès conditionnel :

- « **When this happens** » est une **instruction de condition**. 
- « **Faire** » est un **contrôle d’accès**.

Une stratégie d’accès conditionnel combine une condition à des contrôles d’accès.

![Contrôle](./media/active-directory-conditional-access-conditions/61.png)

Cet article vous donne une vue d’ensemble des conditions et de la façon dont elles sont utilisées dans une stratégie d’accès conditionnel. 


## <a name="users-and-groups"></a>Utilisateurs et groupes

La condition d’utilisateurs et de groupes est obligatoire dans une stratégie d’accès conditionnel. Dans votre stratégie, vous pouvez soit sélectionner **Tous les utilisateurs** soit sélectionner des utilisateurs et groupes spécifiques.

![Contrôle](./media/active-directory-conditional-access-conditions/02.png)

Lorsque vous sélectionnez :

- **Tous les utilisateurs**, votre stratégie est appliquée à tous les utilisateurs dans l’annuaire. Cela inclut les utilisateurs invités.

- **Sélectionnez les utilisateurs et les groupes**, vous pouvez cibler des ensembles d’utilisateurs spécifiques. Par exemple, vous pouvez sélectionner un groupe qui contient tous les membres du département Ressources humaines, lorsque vous avez une application RH sélectionnée en tant qu’application cloud. 

- Un groupe, qui peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés.

Vous pouvez également exclure des utilisateurs ou groupes spécifiques d’une stratégie. Les comptes de service sont un cas d’utilisation courant si votre stratégie met en œuvre l’authentification multifacteur. 

Le ciblage d’ensembles spécifiques d’utilisateurs est utile pour le déploiement d’une nouvelle stratégie. Dans une nouvelle stratégie, vous devez cibler uniquement un ensemble initial d’utilisateurs pour valider le comportement de stratégie. 



## <a name="cloud-apps"></a>Applications cloud 

Une application cloud est un site web ou un service. Cela inclut les sites web protégés par le proxy d’application Azure. Pour une description détaillée des applications cloud prises en charge, consultez [Affectation des applications cloud](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

La condition d’applications de cloud est obligatoire dans une stratégie d’accès conditionnel. Dans votre stratégie, vous pouvez soit sélectionner **Toutes les applications cloud** soit sélectionner des applications spécifiques.

![Contrôle](./media/active-directory-conditional-access-conditions/03.png)

Vous pouvez sélectionner :

- **Toutes les applications cloud** pour les stratégies de base à appliquer à toute l’organisation. Un cas d’usage courant pour cette sélection est une stratégie qui requiert l’authentification multifacteur lorsqu’un risque d’ouverture de session est détecté sur quelle application cloud.

- Applications cloud individuelles pour cibler des services spécifiques par stratégie. Par exemple, vous pouvez demander aux utilisateurs d’avoir un [Appareil conforme](active-directory-conditional-access-policy-connected-applications.md) pour accéder à SharePoint Online. Cette stratégie s’applique aussi à d’autres services lorsqu’ils accèdent à des contenus SharePoint, par exemple, Microsoft Teams. 

Vous pouvez également exclure des applications spécifiques d’une stratégie. Toutefois, ces applications sont toujours soumis aux stratégies appliquées aux services auxquels elles accèdent. 



## <a name="sign-in-risk"></a>Risque à la connexion

Un risque de connexion est une indication de la probabilité (haute, moyenne ou faible) qu’une tentative de connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur. Azure AD calcule le niveau de risque de connexion lors de la connexion d’un utilisateur. Vous pouvez utiliser le niveau de risque de connexion calculé en tant que condition dans une stratégie d’accès conditionnel. 

![Conditions](./media/active-directory-conditional-access-conditions/22.png)

Pour utiliser cette condition, vous devez avoir [Azure Active Directory Identity Protection](active-directory-identityprotection.md) activé.
 
Les cas d’utilisation courants pour cette condition sont des stratégies qui :

- Empêchez les utilisateurs avec un risque élevé de se connecter pour empêcher de potentiels utilisateurs non légitimes d’accéder à vos applications cloud. 
- Exigez l’authentification multifacteur pour les personnes présentant un risque de connexion moyen. En appliquant l’authentification multifacteur, vous pouvez fournir l’assurance supplémentaire que la connexion est effectuée par le propriétaire légitime d’un compte.

Pour plus d’informations, consultez [Connexions risquées](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Plateformes d’appareils

La plateforme d’appareils se caractérise par le système d’exploitation qui s’exécute sur votre appareil. Azure AD identifie la plateforme à l’aide des informations fournies par l’appareil, telles que l’agent utilisateur. Étant donné que ces informations ne sont pas vérifiées, il est recommandé que toutes les plateformes aient une stratégie appliquée, soit en bloquant l’accès, ce qui nécessite la compatibilité avec les stratégies Intune soit en nécessitant que l’appareil soit joint à un domaine. La valeur par défaut est d’appliquer la stratégie à toutes les plateformes d’appareils. 


![Conditions](./media/active-directory-conditional-access-conditions/24.png)

Pour obtenir la liste complète des plateformes d’appareils prises en charge, consultez [Condition de plateforme d’appareil](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Un cas d’usage commun pour cette condition est une stratégie qui limite l’accès à vos applications cloud aux [appareils de confiance](active-directory-conditional-access-policy-connected-applications.md#trusted-devices). Pour d’autres scénarios, y compris les conditions de plateforme d’appareil, consultez [Accès conditionnel basé sur les applications Azure Active Directory](active-directory-conditional-access-mam.md).


## <a name="locations"></a>Emplacements

Les emplacements vous permettent de définir des conditions en fonction de l’endroit à partir duquel a été effectuée une tentative de connexion. 
     
![Conditions](./media/active-directory-conditional-access-conditions/25.png)

Les cas d’utilisation courants pour cette condition sont des stratégies qui :

- Exigez l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise.  

- Bloquez l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques. 

Pour plus d’informations, consultez [Conditions d’emplacement dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>Applications clientes

La condition d’applications client vous permet d’appliquer une stratégie à différents types d’applications, par exemple :

- Sites et services web
- Applications mobiles et de bureau. 

![Conditions](./media/active-directory-conditional-access-conditions/04.png)

Une application est classée comme :

- Un site ou service web si elle utilise le protocole d’authentification unique, SAML, WS-Fed ou OpenID Connect pour un client confidentiel.

- Une application de bureau si elle utilise l’application mobile OpenID Connect pour un client natif.

Pour obtenir la liste complète des applications clientes utilisables dans une stratégie d’accès conditionnel, consultez la [référence technique sur l’accès conditionnel Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Les cas d’utilisation courants pour cette condition sont des stratégies qui :

- Exigez un [appareil conforme](active-directory-conditional-access-policy-connected-applications.md) pour les applications mobiles et de bureau qui téléchargent de grandes quantités de données sur l’appareil, tout en autorisant l’accès par navigateur à partir de n’importe quel appareil.

- Bloquez l’accès aux applications web, mais autorisez l’accès à partir des applications de bureau et mobiles.

Outre l’utilisation de l’authentification unique web et des protocoles d’authentification modernes, vous pouvez appliquer cette condition aux applications de messagerie qui utilisent Exchange ActiveSync, comme les applications de messagerie natives sur la plupart des smartphones. Actuellement, les applications clientes utilisant des protocoles hérités doivent être sécurisées à l’aide d’AD FS.

 Pour plus d'informations, consultez les pages suivantes :

- [Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Accès conditionnel basé sur les applications Azure Active Directory](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 

