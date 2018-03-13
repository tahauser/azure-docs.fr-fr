---
title: "Configurer les stratégies d’accès conditionnel basé sur les appareils dans Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer les stratégies d’accès conditionnel basé sur les appareils dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2354a8bf81189f70bb8d0d63c3df3236403c11fc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurer les stratégies d’accès conditionnel basé sur les appareils dans Azure Active Directory

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez préciser les méthodes d’accès des utilisateurs autorisés aux ressources. Par exemple, vous pouvez limite l’accès à certaines ressources à des appareils de confiance. Une stratégie d’accès conditionnel qui requiert un appareil de confiance est aussi appelée stratégie d’accès conditionnel basé sur les appareils.

Cette rubrique fournit des informations sur la configuration des stratégies d’accès conditionnel basé sur les appareils pour les applications connectées à Azure AD. 


## <a name="before-you-begin"></a>Avant de commencer

L’accès conditionnel basé sur les appareils fait le lien entre l’**accès conditionnel Azure AD** et la **gestion des appareils Azure AD**. Si l’un de ces aspects vous est inconnu, consultez d’abord les rubriques suivantes :

- **[Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)** - Cette rubrique fournit une vue d’ensemble conceptuelle de l’accès conditionnel et explique la terminologie associée.

- **[Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)** - Cette rubrique vous donne une vue d’ensemble des différentes options à votre disposition pour connecter des appareils avec Azure AD. 


## <a name="trusted-devices"></a>Appareils de confiance

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Pour certaines ressources de votre environnement, accorder l’accès aux utilisateurs appropriés n’est parfois pas suffisant. Outre les utilisateurs appropriés, vous pourriez également vouloir qu’appareil de confiance soit utilisé pour accéder à une ressource. Dans votre environnement, vous pouvez définir un appareil de confiance selon les caractéristiques suivantes :

- Les [plateformes d’appareils](active-directory-conditional-access-conditions.md#device-platforms) sur un appareil
- Si un appareil est conforme ou non
- Si un appareil est joint à un domaine 

Les [plateformes d’appareils](active-directory-conditional-access-conditions.md#device-platforms) se caractérisent par le système d’exploitation qui s’exécute sur votre appareil. Dans votre stratégie d’accès conditionnel basé sur les appareils, vous pouvez limiter l’accès à certaines ressources à des plateformes d’appareils spécifiques.



Dans une stratégie d’accès conditionnel basé sur les appareils, vous pouvez demander que des appareils de confiance soient marqués comme conformes.

![Applications cloud](./media/active-directory-conditional-access-policy-connected-applications/24.png)

Les appareils peuvent être marqués comme conformes dans le répertoire par :

- Intune 
- Un système géré par un appareil mobile tiers qui gère les appareils Windows 10 via l’intégration d’Azure AD 
 
  

Seuls les périphériques connectés à Azure AD peuvent être marqués comme conformes. Pour connecter un appareil à Azure Active Directory, vous disposez des options suivantes : 

- Appareils inscrits sur Azure AD
- Appareil joints Azure AD
- Appareils joints Azure AD hybrides

    ![Applications cloud](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Si vous avez une empreinte Active Directory (AD) locale, vous pouvez envisager d’utiliser des appareils qui ne sont pas connectés à Azure AD, mais qui y sont joints, comme appareils de confiance.

![Applications cloud](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Étapes suivantes

Avant de configurer une stratégie d’accès conditionnel basé sur les appareils dans votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md).

