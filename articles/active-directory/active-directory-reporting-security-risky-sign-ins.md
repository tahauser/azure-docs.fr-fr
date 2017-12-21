---
title: "Rapport de connexions risquées dans le portail Azure Active Directory | Microsoft Docs"
description: "En savoir plus sur le rapport des connexions risquées dans le portail Azure Active Directory"
services: active-directory
author: MarkusVi
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 542a111ea5227db727deeac9c56b9f4b4a9f694c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Rapport de connexions à risque dans le portail Azure Active Directory

Grâce aux rapports sur la sécurité dans Azure Active Directory (Azure AD), vous pouvez obtenir des informations sur les risques de compromission des comptes d’utilisateur au sein de votre environnement. 

Azure AD détecte les actions suspectes liées aux comptes des utilisateurs. Pour chaque action détectée, un enregistrement appelé *événement à risque* est créé. Pour en savoir plus, voir [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Les événements à risque détectés sont utilisés pour déterminer les valeurs suivantes :

- **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, voir [Connexions risquées](active-directory-identityprotection.md#risky-sign-ins). 

- **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, voir [Utilisateurs avec indicateur de risque](active-directory-identityprotection.md#users-flagged-for-risk).  

Dans le [portail Azure](https://portal.azure.com), vous trouverez les rapports de sécurité dans le panneau **Azure Active Directory** dans la section **Sécurité**. 

![les connexions risquées.](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>De quelle licence Azure AD avez-vous besoin pour accéder à un rapport de sécurité ?  

Toutes les éditions d’Azure Active Directory vous indiquent les rapports de connexions risquées.  
Toutefois, le niveau de granularité d’un rapport varie entre les éditions : 

- Dans les **éditions Azure Active Directory Free et Basic**, vous obtenez déjà la liste des connexions risquées. 

- L’édition **Azure Active Directory Premium 1** étend ce modèle en vous permettant également d’examiner certains événements à risque sous-jacent qui ont été détectés pour chaque rapport. 

- L’édition **Azure Active Directory Premium 2** vous fournit les informations les plus détaillées sur tous les événements à risque sous-jacent. Elle vous permet également de configurer des stratégies de sécurité répondant automatiquement aux niveaux de risque configurés.



## <a name="azure-active-directory-free-and-basic-edition"></a>Édition Azure Active Directory gratuite et de base

La version gratuite et la version de base d’Azure Active Directory proposent une fonction de création de listes des connexions risquées qui ont été détectées pour les utilisateurs. Ce rapport répertorie :

- **Utilisateur** : nom d’utilisateur qui a été utilisé pendant l’opération de connexion.
- **IP** : adresse IP du périphérique qui a été utilisé pour la connexion à Azure Active Directory.
- **Emplacement** : emplacement utilisé pour la connexion à Azure Active Directory.
- **Heure de connexion** : heure à laquelle la connexion a été effectuée.
- **État** : état de la connexion.


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Selon votre analyse de la connexion à risque, vous pouvez envoyer des commentaires à Azure Active Directory par l’intermédiaire des actions suivantes :

- Résoudre
- Marquer comme faux positif
- Ignorer
- Réactiver

![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Pour en savoir plus, voir [Fermeture manuelle des événements à risque](active-directory-identityprotection.md#closing-risk-events-manually).

Ce rapport fournit une option permettant de :

- Rechercher des ressources
- Télécharger les données du rapport


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/93.png)


## <a name="azure-active-directory-premium-editions"></a>Éditions Premium d’Azure Active Directory

Le rapport des connexions risquées dans les éditions Premium d’Azure Active Directory vous fournit les informations suivantes :

- Informations agrégées sur les [types d’événements à risque](active-directory-identity-protection-risk-events.md) qui ont été détectés

- Une option pour télécharger le rapport


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/456.png)


Lorsque vous sélectionnez un événement à risque, vous obtenez une vue de rapport détaillé pour cet événement à risque qui vous permet d’effectuer les opérations suivantes :

- Une option pour configurer une [stratégie de résolution du risque utilisateur](active-directory-identityprotection.md#user-risk-security-policy)  

- Passer en revue la chronologie de la détection de l’événement à risque  

- Passer en revue la liste des utilisateurs pour lesquels cet événement à risque a été détecté

- [Fermer manuellement les événements à risque](active-directory-identityprotection.md#closing-risk-events-manually) ou réactiver un événement à risque fermé manuellement. 


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/457.png)

Lorsque vous sélectionnez un utilisateur, vous obtenez une vue de rapport détaillé pour cet utilisateur qui vous permet d’effectuer les opérations suivantes :

- Ouvrir la vue Toutes les connexions

- Réinitialisez le mot de passe de l'utilisateur.

- Ignorer tous les événements

- Analysez les événements à risque signalés pour l’utilisateur. 


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Pour analyser un événement à risque, sélectionnez-en un dans la liste.  
Cette opération ouvre le panneau **Détails** pour cet événement à risque. Dans le panneau **Détails**, vous avez le choix entre [fermer manuelle un événement à risque](active-directory-identityprotection.md#closing-risk-events-manually) ou réactiver un événement à risque fermé manuellement. 


![Connexions risquées](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Azure Active Directory Identity Protection, voir [Protection de l’identité Azure Active Directory](active-directory-identityprotection.md).

