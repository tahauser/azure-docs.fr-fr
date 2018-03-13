---
title: "Accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Découvrez comment l’accès conditionnel dans Azure Active Directory peut vous aider à gérer le contrôle d’accès à partir d’un emplacement central."
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
ms.date: 02/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 7e4a65d81a7dade9ae63a24657d15a97a3a588b2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Accès conditionnel dans Azure Active Directory

La sécurité est une priorité pour les organisations qui utilisent le cloud. L’un des aspects clés de la sécurité en matière de gestion des ressources du cloud est l’identité et l’accès. Dans les environnements mobiles et cloud, les utilisateurs peuvent accéder aux ressources de votre organisation en utilisant différents appareils et applications, n’importe où. Il en suffit donc plus de contrôler les personnes autorisées à accéder à une ressource. Afin de maîtriser l’équilibre entre sécurité et productivité, les professionnels de l’informatique doivent aussi prendre en compte les moyens d’accès à une ressource dans leur décision du contrôle d’accès. L’accès conditionnel Azure AD vous permet de satisfaire cette exigence. L’accès conditionnel est une fonctionnalité d’Azure Active Directory qui vous permet d’appliquer des contrôles sur l’accès aux applications dans votre environnement en fonction de conditions spécifiques depuis un emplacement central. 


![Contrôle](./media/active-directory-conditional-access-azure-portal/81.png)

Cet article vous fournit une vue d’ensemble conceptuelle de l’accès conditionnel dans Azure AD.


## <a name="common-scenarios"></a>Scénarios courants

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Avec la prolifération des appareils (y compris des appareils BYOD), des réseaux d’entreprise externes et des applications SaaS tierces, les professionnels de l’informatique sont confrontés à deux objectifs contradictoires :

- Permettre aux utilisateurs d’être productifs où et quand ils le veulent
- Protéger les ressources de l’entreprise à tout moment

À l’aide de stratégies d’accès conditionnel, vous pouvez appliquer les contrôles d’accès appropriés conformes aux conditions requises. L’accès conditionnel Azure AD vous fournit une sécurité supplémentaire chaque fois que nécessaire et ne freine pas l’utilisateur dans le cas contraire. 

Voici quelques problèmes d’accès courants que l’accès conditionnel peut vous aider à résoudre :



- **[Risque à la connexion](active-directory-conditional-access-conditions.md#sign-in-risk)** : Azure AD Identity Protection détecte les risques à la connexion. Comment limite l’accès lorsqu’un risque à la connexion détecté signale une personne malveillante ? Comment faire pour avoir une preuve supplémentaire qu’une connexion a été établie par un utilisateur légitime, voire pour empêcher des utilisateurs spécifiques d’accéder à une application si vos doutes sont suffisamment forts ?

- **[Emplacement réseau](active-directory-conditional-access-locations.md)** : Azure AD est accessible partout. Que faire si une tentative d’accès est effectuée depuis un emplacement réseau qui n’est pas contrôlé par votre service informatique ? La combinaison du nom d’utilisateur et du mot de passe peut fournir une preuve d’identité suffisante pour les tentatives d’accès aux ressources depuis votre réseau d’entreprise. Comment faire pour demander une preuve d’identité plus forte pour les tentatives d’accès initiées depuis d’autres pays ou régions du monde inattendus ? Comment faire pour bloquer les tentatives d’accès depuis certains emplacements ?  

- **[Gestion des appareils](active-directory-conditional-access-conditions.md#device-platforms)** : dans Azure AD, les utilisateurs peuvent accéder aux applications cloud à partir d’un large éventail d’appareils, y compris des appareils mobiles et personnels. Comment faire pour que les tentatives d’accès soient uniquement effectuées par des utilisateurs se servant des appareils gérés par votre service informatique ? Comment faire pour empêcher certains types d’appareils d’accéder aux applications cloud dans votre environnement ? 

- **[Application cliente](active-directory-conditional-access-conditions.md#client-apps)** : aujourd'hui, vous pouvez accéder à de nombreuses applications de cloud en utilisant différents types d’applications comme les applications sur le Web, les applications mobiles ou les applications de bureau. Que faire lorsqu’une tentative d’accès est effectuée avec un type d’application cliente provoquant des problèmes connus ? Comment faire pour exiger qu’un appareil géré par votre service informatique soit utilisé pour certains types d’applications ? 

Ces questions et les réponses associées représentent des scénarios d’accès courants pour l’accès conditionnel Azure AD. L’accès conditionnel est une fonctionnalité d’Azure Active Directory qui permet de gérer les scénarios d’accès en utilisant une approche basée sur des stratégies.


## <a name="conditional-access-policies"></a>Stratégies d’accès conditionnel

Une stratégie d’accès conditionnel est la définition d’un scénario d’accès utilisant le modèle suivant :

![Contrôle](./media/active-directory-conditional-access-azure-portal/10.png)

**Faire** définit la réponse de votre stratégie. Il est important de noter que l’objectif d’une stratégie d’accès conditionnel n’est pas d’accorder l’accès à une application cloud. Dans Azure AD, l’autorisation d’accès aux applications cloud est soumise aux affectations des utilisateurs. Avec une stratégie d’accès conditionnel, vous contrôlez la manière dont les utilisateurs autorisés (utilisateurs autorisés à accéder à une application cloud) peuvent accéder aux applications cloud dans des conditions spécifiques. Dans votre réponse, vous appliquez des exigences supplémentaires comme l’authentification multifacteur, un appareil géré et autres. Dans le contexte de l’accès conditionnel Azure AD, les exigences appliquées par votre stratégie sont appelées des contrôles d’accès. Dans sa forme la plus restrictive, votre stratégie peut empêcher tout accès. Pour plus d’informations, consultez [Contrôles d’accès dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-controls.md).
     

**Quand cela se produit** définit la raison du déclenchement de votre stratégie. Cette raison est caractérisée par un groupe de conditions qui ont été remplies. Dans l’accès conditionnel Azure AD, les conditions d’attribution jouent un rôle spécial :

- **[Utilisateurs](active-directory-conditional-access-conditions.md#users-and-groups)** : les utilisateurs effectuent une tentative d’accès (**Qui**). 

- **[Applications cloud](active-directory-conditional-access-conditions.md#cloud-apps)** : les cibles d’une tentative d’accès (**Quoi**).    

Ces deux conditions sont obligatoires dans une stratégie d’accès conditionnel. Outre les deux conditions obligatoires, vous pouvez également inclure des conditions supplémentaires qui décrivent le mode d’exécution de la tentative d’accès. Par exemple, il est courant que des appareils mobiles ou des emplacements situés en dehors du réseau d’entreprise soient utilisés. Pour plus d’informations, consultez [Conditions dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-conditions.md).   

Une stratégie d’accès conditionnel combine des conditions à des contrôles d’accès. 

![Contrôle](./media/active-directory-conditional-access-azure-portal/51.png)

Avec l’accès conditionnel Azure AD, vous pouvez contrôler la façon dont les utilisateurs autorisés peuvent accéder à vos applications cloud. L’objectif d’une stratégie d’accès conditionnel est d’appliquer des contrôles d’accès supplémentaires sur une tentative d’accès à une application cloud en fonction de la méthode utilisée pour cela.

L’un des avantages de l’utilisation d’une approche basée sur des stratégies pour protéger l’accès aux applications cloud est que vous pouvez commencer à définir les critères des stratégies de votre environnement à l’aide de la structure décrite dans cet article, sans vous préoccuper de la mise en œuvre technique. 

## <a name="what-you-need-to-know"></a>Bon à savoir

### <a name="general-requirements-for-using-conditional-access"></a>Exigences générales pour utiliser l’accès conditionnel

Vous pouvez utiliser l’accès conditionnel Azure AD pour protéger les applications cloud quand une tentative d’authentification provient de :

- Un navigateur web

- Une application cliente qui utilise l’authentification moderne

- Exchange ActiveSync

Pour plus d'informations, consultez les [applications clientes](active-directory-conditional-access-conditions.md#client-apps).

Certaines [applications cloud](active-directory-conditional-access-conditions.md#cloud-apps) prennent également en charge des protocoles d’authentification hérités. C’est le cas, par exemple, de SharePoint Online et Exchange Online. Quand une application cliente peut utiliser un protocole d’authentification hérité pour accéder à une application cloud, Azure AD ne peut pas appliquer de stratégie d’accès conditionnel sur cette tentative d’accès. Pour empêcher une application cliente de contourner la mise en œuvre des stratégies, vous devez vérifier s’il est possible d’activer uniquement l’authentification moderne sur les applications cloud concernées.

Voici des exemples d’applications clientes auxquelles l’accès conditionnel ne s’applique pas :

- Office 2010 et versions antérieures

- Office 2013 quand l’authentification moderne n’est pas activée

Pour plus d’informations, consultez [Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md).


### <a name="license-requirements-for-using-conditional-access"></a>Conditions requises de licences pour utiliser l’accès conditionnel

L’utilisation de l’accès conditionnel requiert une licence Azure AD Premium. Pour trouver la licence appropriée à vos besoins, consultez [Comparaison des fonctionnalités mises à la disposition générale des éditions gratuite, de base et Premium](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features).


## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez en savoir plus sur :
    - les conditions, consultez [Conditions d’emplacement dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-conditions.md).

    - les contrôles d’accès, consultez [Contrôles d’accès dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-controls.md).

- Pour savoir acquérir de l’expérience dans la configuration d’une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
