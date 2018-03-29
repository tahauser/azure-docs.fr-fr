---
title: Accès conditionnel basé sur les applications Azure Active Directory | Microsoft Docs
description: Découvrez le fonctionnement de l’accès conditionnel basé sur les applications Azure Active Directory.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: b35aed3583307c34eadbed3dd13d1ffd51494927
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Accès conditionnel basé sur les applications Azure Active Directory  

Vos employés utilisent des appareils mobiles pour des tâches à la fois personnelles et professionnelles. Tout en veillant à ce que vos employés restent productifs, vous voulez aussi éviter toute perte de données. Avec l’accès conditionnel basé sur les applications Azure Active Directory (Azure AD), vous pouvez restreindre l’accès à vos applications cloud à des applications clientes capables de protéger vos données d’entreprise.  

Cette rubrique explique comment configurer l’accès conditionnel basé sur les applications Azure AD.

## <a name="overview"></a>Vue d'ensemble

Avec l’[accès conditionnel Azure AD](active-directory-conditional-access-azure-portal.md), vous pouvez préciser les méthodes d’accès des utilisateurs autorisés aux ressources. Par exemple, vous pouvez limiter l’accès à vos applications cloud aux appareils approuvés.

Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise. Les stratégies de protection des applications Intune n’exigent pas de solution de gestion des appareils mobiles (MDM), ce qui vous permet de protéger les données de votre entreprise avec ou sans inscription des appareils dans une solution de gestion des appareils.

L’accès conditionnel basé sur les applications Azure Active Directory vous permet de limiter l’accès à vos applications cloud à des applications clientes qui prennent en charge les stratégies de protection des applications Intune. Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook.

Dans la terminologie de l’accès conditionnel, ces applications clientes sont appelées **applications clientes approuvées**.  


![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)


Pour obtenir la liste des applications clientes approuvées, consultez [Spécification d’application cliente approuvée](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Vous pouvez combiner des stratégies d’accès conditionnel basé sur les applications avec d’autres stratégies, telles que les [stratégies d’accès conditionnel au niveau de l’appareil](active-directory-conditional-access-policy-connected-applications.md), afin de bénéficier d’une flexibilité dans la protection des données pour les appareils personnels et d’entreprise.

 


## <a name="before-you-begin"></a>Avant de commencer

Cette rubrique part du principe que vous connaissez :

- Les informations techniques de référence intitulées [Spécification d’application cliente approuvée](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


- Les concepts de base de l’[accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)

- Comment [configurer votre stratégie d’accès conditionnel](active-directory-conditional-access-azure-portal-get-started.md)

- La [migration des stratégies d’accès conditionnel](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Prérequis


Pour créer une stratégie d’accès conditionnel basé sur les applications, vous devez avoir un abonnement à Enterprise Mobility + Security ou à Azure Active Directory Premium, et les utilisateurs doivent disposer d’une licence pour EMS ou Azure AD. 


## <a name="exchange-online-policy"></a>Stratégie Exchange Online 

Ce scénario consiste en une stratégie d’accès conditionnel basé sur les applications pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange

- Reçoit un e-mail qui indique que l’accès est disponible uniquement à l’aide de l’application Outlook

- Télécharge l’application avec le lien

- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD

- Est invité à installer Authenticator (iOS) ou le Portail d’entreprise (Android) pour continuer

- Installe l’application et peut revenir à l’application Outlook pour continuer

- Est invité à inscrire un appareil

- Est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune sont activées au moment de l’accès aux données d’entreprise, et peuvent inviter l’utilisateur à redémarrer l’application, à utiliser un code PIN supplémentaire, etc. (si ceci est configuré pour l’application et la plateforme).

### <a name="configuration"></a>Configuration 

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/01.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :

    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)
 

**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.


3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes**. 

    a. Comme **Applications clientes**, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

    b. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)


**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="exchange-online-and-sharepoint-online-policy"></a>Stratégie Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’accès conditionnel avec la gestion des applications mobiles pour l’accès à Exchange Online et SharePoint Online avec les applications approuvées.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Tente d’utiliser l’application SharePoint pour se connecter à ses sites d’entreprise et les afficher

- Tente de se connecter avec les mêmes informations d’identification que celles de l’application Outlook

- N’est pas obligé de se réinscrire et peut accéder aux ressources


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/71.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.


3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :

    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)




**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. En ligne 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes** :

    a. Comme **Applications clientes**, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

    b. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur les applications ou stratégie d’appareil conforme pour Exchange Online et SharePoint Online

Ce scénario consiste en une stratégie d’accès conditionnel basé sur les applications ou la conformité des appareils pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe que :
 
- Un utilisateur a déjà été inscrit (avec ou sans appareils d’entreprise)

- Les utilisateurs qui ne sont pas enregistrés et inscrits auprès d’Azure AD à l’aide d’une application protégée approuvée doivent inscrire un appareil pour accéder aux ressources

- Les utilisateurs inscrits à l’aide de l’application protégée approuvée ne sont pas obligés de réinscrire l’appareil


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/62.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 

     ![Accès conditionnel](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

    - **Exiger que l’appareil soit marqué comme conforme**

    - **Demander une application cliente approuvée (préversion)**

    - **Demander un des contrôles sélectionnés**   
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/11.png)



**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/61.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes**. 

    Comme **Applications clientes*, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

5. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/11.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur les applications et stratégie d’appareil conforme pour Exchange Online et SharePoint Online

Ce scénario consiste en une stratégie d’accès conditionnel basé sur les applications et la conformité des appareils pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :
 
-   Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
-   Reçoit un e-mail qui indique que l’accès exige l’inscription de l’appareil
-   Télécharge le portail d’entreprise et s’y connecte
-   Vérifie ses e-mails et est invité à utiliser l’application Outlook
-   Télécharge l’application Outlook
-   Ouvre l’application Outlook et entre les informations d’identification utilisées lors de l’inscription
-   L’utilisateur est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune sont activées au moment de l’accès aux données d’entreprise, et peuvent inviter l’utilisateur à redémarrer l’application, à utiliser un code PIN supplémentaire, etc. (si ceci est configuré pour l’application et la plateforme)


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/62.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 

     ![Accès conditionnel](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

    - **Exiger que l’appareil soit marqué comme conforme**

    - **Demander une application cliente approuvée (préversion)**

    - **Demander tous les contrôles sélectionnés**   
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/13.png)



**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/61.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes**. 

    Comme **Applications clientes**, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

    - **Exiger que l’appareil soit marqué comme conforme**

    - **Demander une application cliente approuvée (préversion)**

    - **Demander tous les contrôles sélectionnés**   
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/64.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).






## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
