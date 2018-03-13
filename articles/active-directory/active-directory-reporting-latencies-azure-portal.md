---
title: "Latences de création de rapports Azure Active Directory | Microsoft Docs"
description: "Découvrir le délai nécessaire pour que les événements de rapports apparaissent dans votre portail Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ec41817fede495b8262e28d2d614a480d98ff3b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Latences de création de rapports Azure Active Directory

Dans Azure Active Directory, la [création de rapports](active-directory-preview-explainer.md) vous permet d’obtenir toutes les informations dont vous avez besoin pour déterminer l’état de votre environnement. Le temps nécessaire pour que les données de rapport s’affichent dans le portail Azure est également appelé latence. 

Cette rubrique répertorie les informations de latence pour toutes les catégories de rapports dans le portail Azure. 


## <a name="activity-reports"></a>Rapports d’activité

Il existe deux zones de rapports d’activité :

- **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs
- **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire

Le tableau suivant répertorie les informations de latence pour les rapports d’activité.

| Rapport | Minimale | Moyenne | Remarques |
| :-- | --- | --- | :-- |
| Journaux d’audit | 30 minutes  | 1 heure  |Dans certains cas, l’affichage des données d’activité d’audit peut prendre jusqu’à deux heures.|
| Connexions | 15 minutes  | 2 heures |Dans certains cas, l’affichage des données d’activité de connexion peut prendre jusqu’à 24 heures. Cela inclut les données d’activité de connexion provenant d’applications de bureau héritées. |







## <a name="security-reports"></a>Rapports de sécurité

Il existe deux zones de rapports de sécurité :

- **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. 
- **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. 

Le tableau suivant répertorie les informations de latence pour les rapports de sécurité.

| Rapport | Minimale | Moyenne | Maximale |
| :-- | --- | --- | --- |
| Les utilisateurs à risque          | 5 minutes   | 15 minutes  | 2 heures  |
| Connexions risquées         | 5 minutes   | 15 minutes  | 2 heures  |

## <a name="risk-events"></a>Événements à risque

Azure Active Directory utilise les algorithmes Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux comptes de votre utilisateur. Chaque action suspecte détectée est stockée dans un enregistrement appelé événement à risque.

Le tableau suivant répertorie les informations de latence pour les événements à risque.

| Rapport | Minimale | Moyenne | Maximale |
| :-- | --- | --- | --- |
| Connexions depuis des adresses IP anonymes |5 minutes |15 minutes |2 heures |
| Connexions depuis des emplacements non connus |5 minutes |15 minutes |2 heures |
| Utilisateurs avec des informations d’identification volées |2 heures |4 heures |8 heures |
| Voyage impossible vers des emplacements inhabituels |5 minutes |1 heure |8 heures  |
| Connexions depuis des appareils infectés |2 heures |4 heures |8 heures  |
| Connexions depuis des adresses IP avec des activités suspectes |2 heures |4 heures |8 heures  |



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les rapports d’activité dans le portail Azure, consultez :

- [Rapports d’activité de connexion dans le portail Azure Active Directory](active-directory-reporting-activity-sign-ins.md)
- [Rapports d’activité d’audit dans le portail Azure Active Directory](active-directory-reporting-activity-audit-logs.md)

Pour en savoir plus sur les rapports de sécurité dans le portail Azure, consultez :

- [Rapport sur la sécurité des utilisateurs courant un risque dans le portail Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Rapport de connexions à risque dans le portail Azure Active Directory ](active-directory-reporting-security-risky-sign-ins.md)

Pour en savoir plus sur les événements à risque, consultez [Événements à risque dans Azure Active Directory](active-directory-reporting-risk-events.md).
