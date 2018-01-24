---
title: "Accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Utilisez le contrôle d’accès conditionnel dans Azure Active Directory pour vérifier des conditions spécifiques lors de l’authentification pour l’accès aux applications."
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
ms.date: 11/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3ca9157823a52bd0e4e8dd3b2656fc2724c8b4b6
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/23/2017
---
# <a name="conditional-access-in-azure-active-directory"></a>Accès conditionnel dans Azure Active Directory

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Avec la prolifération des appareils (y compris des appareils BYOD), des réseaux d’entreprise externes et des applications SaaS tierces, les professionnels de l’informatique sont confrontés à deux objectifs contradictoires :

- Permettre aux utilisateurs d’être productifs où et quand ils le veulent
- Protéger les ressources de l’entreprise à tout moment

Pour améliorer la productivité, Azure Active Directory fournit à vos utilisateurs un large éventail d’options permettant d’accéder à vos ressources d’entreprise. Avec la gestion des accès aux applications, Azure Active Directory vous permet de garantir que seules *les bonnes personnes* ont accès à vos applications. Et si vous souhaitiez mieux contrôler comment les bonnes personnes accèdent à vos ressources dans certaines conditions ? Et si vous étiez en mesure de bloquer l’accès à certaines applications, même pour *les bonnes personnes* ? Par exemple, vous pouvez accepter que les bonnes personnes puissent accéder à certaines applications à partir d’un réseau approuvé, mais pas qu’elles y accèdent à partir d’un réseau non approuvé. L’accès conditionnel apporte une réponse à ces questions.

L’accès conditionnel est une fonctionnalité d’Azure Active Directory qui vous permet d’appliquer des contrôles sur l’accès aux applications dans votre environnement en fonction de conditions spécifiques. Grâce aux contrôles, vous pouvez soit ajouter d’autres conditions requises à l’accès, soit bloquer ce dernier. L’implémentation de l’accès conditionnel repose sur des stratégies. Une approche fondée sur des stratégies simplifie votre expérience de configuration, car elle suit votre perception des modalités d’accès.  

En général, vous définissez vos modalités d’accès à l’aide d’instructions basées sur le modèle suivant :

![Contrôle](./media/active-directory-conditional-access-azure-portal/10.png)

Lorsque vous remplacez les deux occurrences de « *this* » par des informations concrètes, vous disposez d’un exemple d’instruction de stratégie qui vous est probablement familier :

*Lorsque des prestataires essaient d’accéder à nos applications cloud à partir de réseaux non approuvés, bloquer l’accès.*

L’instruction de stratégie ci-dessus met en évidence la puissance de l’accès conditionnel. Vous pouvez permettre à des prestataires d’accéder à vos applications cloud (**qui**). Mais avec l’accès conditionnel, vous pouvez également définir les conditions dans lesquelles l’accès est possible (**comment**).

Dans le contexte de l’accès conditionnel Azure Active Directory,

- « **When this happens** » est une **instruction de condition**
- « **Then do this** » est un **contrôle**

![Contrôle](./media/active-directory-conditional-access-azure-portal/11.png)

Une stratégie d’accès conditionnel combine une instruction de condition à des contrôles.

![Contrôle](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Commandes

Dans une stratégie d’accès conditionnel, les contrôles définissent l’action à effectuer lorsqu’une instruction de condition est remplie.  
Grâce aux contrôles, vous pouvez bloquer ou autoriser l’accès avec des exigences supplémentaires.
Lorsque vous configurez une stratégie qui autorise l’accès, vous devez sélectionner au moins une exigence.  

Il existe deux types de contrôles : 

- **Contrôle d’octroi** : Les contrôles d’octroi déterminent si un utilisateur peut effectuer l’authentification et atteindre la ressource à laquelle il essaie de se connecter. Si vous avez sélectionné plusieurs contrôles, vous pouvez indiquer si tous ces contrôles sont requis lors du traitement de votre stratégie.
L’implémentation actuelle d’Azure Active Directory vous permet de configurer les exigences de contrôle d’octroi suivantes :

    ![Contrôle](./media/active-directory-conditional-access-azure-portal/73.png)

- **Contrôles de session** : Les contrôles de session permettent de limiter l’expérience dans une application cloud. Les contrôles de session sont appliqués par les applications cloud et s’appuient sur des informations supplémentaires fournies par Azure AD à l’application concernant la session.

    ![Contrôle](./media/active-directory-conditional-access-azure-portal/31.png)


Pour plus d’informations, consultez [Contrôles dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-controls.md).


## <a name="condition-statement"></a>Instruction de condition

La section précédente vous a présenté les options permettant de bloquer ou de limiter l’accès à vos ressources sous forme de contrôles. Dans une stratégie d’accès conditionnel, vous définissez les critères que vos contrôles doivent remplir, sous la forme d’une instruction de condition.  

Vous pouvez inclure les assignations suivantes dans votre instruction de condition :

![Contrôle](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>Qui ?

Lorsque vous configurez une stratégie d’accès conditionnel, vous devez sélectionner les utilisateurs ou les groupes auxquels s’applique votre stratégie. Souvent, les contrôles s’appliquent à un ensemble donné d’utilisateurs. Dans une instruction conditionnelle, vous pouvez définir cet ensemble en sélectionnant les utilisateurs et les groupes concernés par votre stratégie. Au besoin, vous pouvez également exclure explicitement un ensemble d’utilisateurs de votre stratégie en les exemptant.  

![Contrôle](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>Quoi ?

Lorsque vous configurez une stratégie d’accès conditionnel, vous devez sélectionner les applications cloud auxquelles s’applique votre stratégie.
En général, certaines applications d’un environnement requièrent plus de protection que d’autres. Cela concerne notamment les applications qui ont accès à des données sensibles.
En sélectionnant des applications cloud, vous définissez celles auxquelles s’applique votre stratégie. Au besoin, vous pouvez également exclure explicitement un ensemble d’applications de votre stratégie.

![Contrôle](./media/active-directory-conditional-access-azure-portal/09.png)

Pour obtenir la liste complète des applications cloud utilisables dans une stratégie d’accès conditionnel, consultez la [référence technique sur l’accès conditionnel Azure Active Directory](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).

### <a name="how"></a>Comment ?

Tant que l’accès à vos applications s’effectue dans des conditions que vous pouvez contrôler, il est potentiellement inutile d’imposer des contrôles supplémentaires sur les modalités d’accès de vos utilisateurs à vos applications cloud. Toutefois, les choses peuvent être différentes si l’accès à vos applications cloud s’effectue notamment à partir de réseaux non approuvés ou d’appareils non conformes. Dans une instruction de condition, vous pouvez définir certaines conditions d’accès qui précisent des exigences supplémentaires concernant le mode d’accès à vos applications.

![Conditions](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Conditions

Dans l’implémentation actuelle d’Azure Active Directory, vous pouvez définir des conditions pour les aspects suivants :

- Risque à la connexion
- Plateformes d’appareils
- Emplacements
- Applications clientes


![Conditions](./media/active-directory-conditional-access-azure-portal/01.png)

### <a name="sign-in-risk"></a>Risque à la connexion

Un risque à la connexion est un objet qui permet à Azure Active Directory de suivre la probabilité qu’une tentative de connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur. Dans cet objet, la probabilité (haute, moyenne ou faible) est stockée sous forme d’un attribut appelé [niveau de risque de connexion](active-directory-reporting-risk-events.md#risk-level). Cet objet est généré lors de la connexion d’un utilisateur si des risques de connexion ont été détectés par Azure Active Directory. Pour plus d’informations, consultez [Connexions risquées](active-directory-identityprotection.md#risky-sign-ins).  
Vous pouvez utiliser le niveau de risque de connexion calculé en tant que condition dans une stratégie d’accès conditionnel. 

![Conditions](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>Plateformes d’appareils

La plateforme d’appareil est caractérisée par le système d’exploitation exécuté sur votre appareil : vous pouvez définir les plateformes d’appareils incluses et celles qui sont exclues d’une stratégie.  
Pour utiliser des plateformes d’appareils dans la stratégie, commencez par régler les options de configuration sur **Oui**, puis sélectionnez une, plusieurs ou l’ensemble des plateformes d’appareils auxquelles s’applique la stratégie. Si vous sélectionnez certaines plateformes d’appareils, la stratégie ne s’applique qu’à celles-ci. Dans ce cas, la stratégie est sans effet sur les connexions aux autres plateformes prises en charge.


![Conditions](./media/active-directory-conditional-access-azure-portal/02.png)

Pour obtenir la liste complète des plateformes d’appareils prises en charge, consultez [Condition de plateforme d’appareil](active-directory-conditional-access-technical-reference.md#device-platform-condition).


### <a name="locations"></a>Emplacements

Les emplacements vous permettent de définir des conditions en fonction de l’endroit à partir duquel a été effectuée une tentative de connexion. La liste des emplacements peut contenir des **emplacements nommés** ou des **adresses IP approuvées MFA**.  

Les **emplacements nommés** sont une fonctionnalité d’Azure Active Directory qui permet de définir des étiquettes pour les emplacements à partir desquels des tentatives de connexion ont été effectuées. Pour définir un emplacement, vous pouvez configurer des plages d’adresses IP, ou sélectionner un pays ou une région.  

![Conditions](./media/active-directory-conditional-access-azure-portal/42.png)

Vous pouvez aussi marquer un emplacement nommé comme emplacement approuvé. Pour une stratégie d’accès conditionnel, l’emplacement approuvé est une autre option de filtre qui vous permet de sélectionner *tous les emplacements approuvés* dans votre condition d’emplacements.
Les emplacements nommés sont également importants pour la détection des [événements à risque](active-directory-reporting-risk-events.md), car ils permettent de réduire le nombre de faux positifs pour l’événement à risque Déplacement impossible vers des emplacements inhabituels. 

Le nombre d’emplacements nommés que vous pouvez configurer est limité par la taille de l’objet associé dans Azure AD. Vous pouvez configurer les éléments suivants :
 
 - Un emplacement nommé avec 500 plages d’adresses IP maximum
 - Un maximum de 60 emplacements nommés (préversion) avec une plage d’adresses IP assignée à chacun d’eux. 

Pour plus d’informations, consultez [Emplacements nommés dans Azure Active Directory](active-directory-named-locations.md).


Les **adresses IP approuvées MFA** sont une fonctionnalité de Multi-Factor Authentication, qui vous permet de définir les plages d’adresses IP approuvées correspondant à l’intranet local de votre organisation. Quand vous configurez une condition d’emplacement, les adresses IP approuvées vous permettent de faire la distinction entre les connexions effectuées depuis le réseau de votre organisation et celles provenant de tous les autres emplacements. Pour plus d’informations, consultez [Adresses IP approuvées](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Dans votre stratégie d’accès conditionnel, vous pouvez :

- Inclure
    - N’importe quel emplacement
    - Tous les emplacements approuvés
    - Des emplacements sélectionnés
- Exclure
    - Tous les emplacements approuvés
    - Des emplacements sélectionnés
     
![Conditions](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>Applications clientes

L’application cliente peut être l’application (navigateur web, application mobile, client de bureau) que vous avez utilisée pour vous connecter à Azure Active Directory ou vous pouvez sélectionner Exchange Active Sync.  
L’authentification héritée concerne les clients qui utilisent l’authentification de base, comme les anciens clients Office qui n’exploitent pas l’authentification moderne. Pour l’instant, l’accès conditionnel ne fonctionne pas avec l’authentification héritée.

![Conditions](./media/active-directory-conditional-access-azure-portal/04.png)


Pour obtenir la liste complète des applications clientes utilisables dans une stratégie d’accès conditionnel, consultez la [référence technique sur l’accès conditionnel Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-condition).




## <a name="common-scenarios"></a>Scénarios courants

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exiger l’authentification multifacteur pour les applications

Dans de nombreux environnements, certaines applications nécessitent un niveau de protection plus élevé que d’autres.
C’est le cas des applications qui ont accès à des données sensibles.
Si vous souhaitez ajouter une couche de protection supplémentaire pour ces applications, vous pouvez configurer une stratégie d’accès conditionnel qui requiert l’authentification multifacteur lorsque les utilisateurs accèdent à ces applications.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exiger l’authentification multifacteur pour l’accès à partir de réseaux non approuvés

Ce scénario est semblable au précédent, car il ajoute une exigence pour l’authentification multifacteur.
Toutefois, la principale différence réside dans la condition de cette exigence.  
Tandis que le scénario précédent se concentre sur les applications ayant accès à des données sensibles, ce scénario concerne les emplacements approuvés.  
En d’autres termes, l’authentification multifacteur peut être requise si un utilisateur accède à une application à partir d’un réseau que vous n’avez pas approuvé.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Seuls les appareils approuvés ont accès aux services Office 365

Si vous utilisez Intune dans votre environnement, vous pouvez utiliser d’emblée l’interface de stratégie d’accès conditionnel dans la console Azure.

De nombreux clients Intune utilisent l’accès conditionnel pour vérifier que seuls les appareils approuvés ont accès aux services Office 365. Cela signifie que les appareils mobiles sont inscrits dans Intune, qu’ils répondent aux critères de la stratégie de conformité et que des PC Windows sont joints à un domaine local. L’avantage, c’est que vous n’avez pas à définir la même stratégie pour chacun des services Office 365.  Quand vous créez une stratégie, configurez les applications cloud pour inclure chacune des applications Office 365 que vous souhaitez protéger avec l’accès conditionnel.

### <a name="switching-a-device-from-corporate-owned-to-bring-your-own-device-byod"></a>Conversion d’un appareil d’entreprise en appareil BYOD (Apportez votre propre appareil)
Si vous souhaitez bloquer des appareils d’entreprise inscrits en modifiant leur propriété pour les convertir en appareils personnels, vous pouvez utiliser l’accès conditionnel Azure Active Directory (AAD). Vous devez d’abord créer une stratégie d’accès conditionnel où l’option **Bloquer l’accès** est sélectionnée dans les contrôles d’accès du panneau **Accorder**. Créez ensuite un groupe **Appareil dynamique** en définissant la propriété **deviceOwnership** sur **Personnel**. Enfin, ciblez la stratégie ci-dessus sur le nouveau groupe.

## <a name="next-steps"></a>étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
