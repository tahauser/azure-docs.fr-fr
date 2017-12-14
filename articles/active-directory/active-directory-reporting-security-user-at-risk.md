---
title: "Rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory | Microsoft Docs"
description: "En savoir plus sur le rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory"
services: active-directory
author: MarkusVi
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: dca87eef3026ef43c0548a73f1b071b56e410e90
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Rapport sur la sécurité des utilisateurs avec indicateur de risque dans le portail Azure Active Directory

Grâce aux rapports sur la sécurité dans Azure Active Directory (Azure AD), vous pouvez obtenir des informations sur les risques de compromission des comptes d’utilisateur au sein de votre environnement. 

Azure Active Directory détecte les actions suspectes liées aux comptes des utilisateurs. Pour chaque action détectée, un enregistrement appelé *événement à risque* est créé. Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Les événements à risque détectés sont utilisés pour déterminer les valeurs suivantes :

- **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour plus d’informations, consultez [Connexions risquées](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour plus d’informations, consultez [Utilisateurs avec indicateur de risque](active-directory-identityprotection.md#users-flagged-for-risk).  

Dans le portail Azure, vous trouverez les rapports de sécurité dans le panneau **Azure Active Directory** dans la section **Sécurité**.  

![les connexions risquées.](./media/active-directory-reporting-security-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De quelle licence Azure AD avez-vous besoin pour accéder à un rapport de sécurité ?  

Toutes les éditions d’Azure Active Directory vous indiquent les rapports d’utilisateurs associés à un indicateur de risque.  
Toutefois, le niveau de granularité d’un rapport varie entre les éditions : 

- Dans les **éditions Azure Active Directory Free et Basic**, vous obtenez déjà la liste des utilisateurs associés à un indicateur de risque. 

- L’édition **Azure Active Directory Premium 1** étend ce modèle en vous permettant également d’examiner certains événements à risque sous-jacent qui ont été détectés pour chaque rapport. 

- L’édition **Azure Active Directory Premium 2** vous fournit les informations les plus détaillées sur tous les événements à risque sous-jacent. Elle vous permet de configurer des stratégies de sécurité répondant automatiquement aux niveaux de risque configurés.



## <a name="azure-active-directory-free-and-basic-edition"></a>Édition Azure Active Directory gratuite et de base

Le rapport sur les utilisateurs avec indicateur de risque dans les éditions gratuites et de base d’Azure Active Directory vous fournit une liste de comptes d’utilisateurs qui ont peut-être été compromis. 


![Connexions risquées](./media/active-directory-reporting-security-user-at-risk/03.png)

La sélection d’un utilisateur ouvre le panneau des données utilisateur correspondant.
Vous pouvez vérifier l’historique des connexions d’un utilisateur à risque et réinitialiser son mot de passe, le cas échéant.

![Connexions risquées](./media/active-directory-reporting-security-user-at-risk/46.png)


Cette boîte de dialogue fournit une option permettant de :

- Télécharger le rapport

- Rechercher des utilisateurs

![Connexions risquées](./media/active-directory-reporting-security-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Éditions Premium d’Azure Active Directory

Le rapport sur les utilisateurs avec indicateur de risque dans les éditions Premium d’Azure Active Directory vous fournit les informations suivantes :

- Une [liste des comptes d’utilisateurs](active-directory-identityprotection.md#users-flagged-for-risk) qui ont peut-être été compromis 

- Informations agrégées sur les [types d’événements à risque](active-directory-identity-protection-risk-events.md) qui ont été détectés

- Une option pour télécharger le rapport

- Une option pour configurer une [stratégie de résolution du risque utilisateur](active-directory-identityprotection.md#user-risk-security-policy)  


![Connexions risquées](./media/active-directory-reporting-security-user-at-risk/71.png)

Lorsque vous sélectionnez un utilisateur, vous obtenez une vue de rapport détaillé pour cet utilisateur qui vous permet d’effectuer les opérations suivantes :

- Ouvrir la vue Toutes les connexions

- Réinitialisez le mot de passe de l'utilisateur.

- Ignorer tous les événements

- Analysez les événements à risque signalés pour l’utilisateur. 


![Connexions risquées](./media/active-directory-reporting-security-user-at-risk/324.png)


Pour analyser un événement à risque, sélectionnez-en un depuis la liste pour ouvrir le panneau **Détails** de cet événement à risque. Dans le panneau **Détails**, vous avez le choix entre [fermer manuelle un événement à risque](active-directory-identityprotection.md#closing-risk-events-manually) ou réactiver un événement à risque fermé manuellement. 


![Connexions risquées](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Azure Active Directory Identity Protection, voir [Protection de l’identité Azure Active Directory](active-directory-identityprotection.md).

