---
title: "Création de rapports Azure Active Directory | Microsoft Docs"
description: "Fournit une vue d’ensemble de la création de rapports Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 66c53b84e1e820178083b3ae703e0a1feada187f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-reporting"></a>Création de rapports Active Directory

Les rapports Azure Active Directory vous permettent d’obtenir de précieuses informations sur le comportement de votre environnement.  
Les données fournies vous permettent de :

- déterminer la façon dont les applications et les services sont utilisés ;
- détecter les risques potentiels affectant l’intégrité de votre environnement ;
- résoudre les problèmes empêchant les utilisateurs d’effectuer leur travail.  

L’architecture de création de rapports s’appuie sur deux axes principaux :

- Rapports de sécurité
- Rapports d’activité

![Reporting](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Rapports de sécurité

Les rapports de sécurité d’Azure Active Directory vous aident à protéger les identités de votre organisation.  
Il existe deux types de rapports de sécurité dans Azure Active Directory :

- **Utilisateurs avec indicateur de risque** : le [rapport de sécurité de ce type](active-directory-reporting-security-user-at-risk.md) présente une vue d’ensemble des comptes d’utilisateurs qui peuvent avoir été compromis.

- **Connexions à risque** : le [rapport de sécurité de ce type](active-directory-reporting-security-risky-sign-ins.md) contient un indicateur pour les tentatives de connexion susceptibles d’avoir été effectuées par une personne autre que le propriétaire légitime d’un compte d’utilisateur. 

**De quelle licence Azure AD avez-vous besoin pour accéder à un rapport de sécurité ?**  
Toutes les éditions d’Azure Active Directory vous indiquent les rapports de sécurité Utilisateurs avec indicateur de risque et Connexions à risque.  
Toutefois, le niveau de granularité d’un rapport varie entre les éditions : 

- Dans les **éditions Azure Active Directory Free et Basic**, vous obtenez déjà la liste des utilisateurs avec indicateur de risque et des connexions à risque. 

- L’édition **Azure Active Directory Premium 1** étend ce modèle en vous permettant également d’examiner certains événements à risque sous-jacent qui ont été détectés pour chaque rapport. 

- L’édition **Azure Active Directory Premium 2** vous fournit les informations les plus détaillées sur les événements à risque sous-jacent. Elle vous permet également de configurer des stratégies de sécurité répondant automatiquement aux niveaux de risque configurés.


## <a name="activity-reports"></a>Rapports d’activité

Il existe deux types de rapports d’activité dans Azure Active Directory :

- **Journaux d’audit** : le [rapport d’activité de ce type](active-directory-reporting-activity-audit-logs.md) vous permet d’accéder à l’historique de toutes les tâches effectuées dans votre locataire.

- **Connexions** : le [rapport d’activité de ce type](active-directory-reporting-activity-sign-ins.md) vous permet d’identifier qui a effectué les tâches signalées par le rapport de journaux d’audit.



Le **rapport de journaux d’audit** vous fournit les enregistrements des activités du système relatifs à la conformité.
Les données fournies vous permettent, entre autres choses, de résoudre des scénarios courants :

- Une personne de mon locataire a obtenu l’accès à un groupe d’administration. Qui lui a fourni cet accès ? 

- Je souhaite connaître la liste des utilisateurs se connectant à une application spécifique, car j’ai récemment intégré l’application et souhaite savoir si j’ai bien fait.

- Je souhaite connaître le nombre de réinitialisations de mot de passe qui se produisent dans mon locataire.


**De quelle licence Azure AD avez-vous besoin pour accéder au rapport de journaux d’audit ?**  
Le rapport de journaux d’audit est disponible pour les fonctionnalités dont vous disposez les licences. Si vous possédez une licence pour une fonctionnalité spécifique, vous avez également accès aux informations du journal d’audit correspondantes.

Pour en savoir plus, voir la section **Comparaison des caractéristiques de disponibilité générale des éditions Free, Basic et Premium**  de l’article [Fonctionnalités d’Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   



Le **rapport d’activité des connexions** vous permet de trouver les réponses aux questions suivantes :

- Quel est le modèle de connexion d’un utilisateur ?
- Combien d’utilisateurs se sont connectés au cours d’une semaine ?
- Quel est l’état de ces connexions ?


**De quelle licence Azure AD avez-vous besoin pour accéder au rapport d’activité des connexions ?**  
Pour accéder au rapport d’activité des connexions, votre locataire doit posséder une licence Azure AD Premium qui lui est associée.


## <a name="programmatic-access"></a>Accès par programme

Outre l’interface utilisateur, les rapports Azure Active Directory vous fournissent également un [accès par programme](active-directory-reporting-api-getting-started-azure-portal.md) à leurs données. Les données de ces rapports peuvent être très utiles pour vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. Les API de création de rapports Azure AD fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation. 


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les différents types de rapports dans Azure Active Directory, voir :

- [Rapport des utilisateurs avec indicateur de risque](active-directory-reporting-security-user-at-risk.md)
- [Rapport sur les connexions à risque](active-directory-reporting-security-risky-sign-ins.md)
- [Rapport de journaux d’audit](active-directory-reporting-activity-audit-logs.md)
- [Rapport de journaux de connexions](active-directory-reporting-activity-sign-ins.md)

Pour en savoir plus sur l’accès aux données des rapports à l’aide de l’API de création de rapports, voir : 

- [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png