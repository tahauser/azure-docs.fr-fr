---
title: "Rapports d’activité d’audit dans le portail Azure Active Directory | Microsoft Docs"
description: "Présentation des rapports d’activité d’audit dans le portail Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 33dc234d4d0ca3ec58e069a928a30bcd8ec41e6d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité d’audit dans le portail Azure Active Directory 

Dans Azure Active Directory (Azure AD), vous pouvez obtenir toutes les informations dont vous avez besoin pour déterminer l’état de votre environnement.

L’architecture de création de rapports dans Azure AD comprend les composants suivants :

- **Activité** 
    - **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs
    - **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, consultez Connexions risquées.
    - **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, consultez Utilisateurs avec indicateur de risque.

Cette rubrique vous donne une vue d’ensemble des activités d’audit.
 
## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
* Utilisateurs ayant le rôle d’administrateur de sécurité ou de lecteur de la sécurité
* Administrateurs généraux
* Les utilisateurs individuels (non administrateurs) peuvent voir leurs propres activités


## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit dans Azure Active Directory fournissent des enregistrements des activités du système pour la conformité.  
Les **Journaux d’audit** dans la section **Activité** **d’Azure Active Directory** constituent votre premier point d’entrée pour toutes les données d’audit.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/61.png "Journaux d’Audit")

Un journal d’audit a une vue de liste par défaut qui indique :

- la date et l’heure de l’occurrence
- l’initiateur / intervenant (*qui*) d’une activité 
- l’activité (*quoi*) 
- la cible

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/18.png "Journaux d’Audit")

Vous pouvez personnaliser la vue sous forme de liste en cliquant sur **Colonnes** dans la barre d’outils.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/19.png "Journaux d’Audit")

Cela vous permet d’afficher des champs supplémentaires ou de supprimer des champs qui sont déjà affichés.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/21.png "Journaux d’Audit")


En cliquant sur un élément dans la vue sous forme de liste, vous pouvez obtenir plus d’informations sur cet élément.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/22.png "Journaux d’Audit")


## <a name="filtering-audit-logs"></a>Filtrage des journaux d’audit

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données d’audit à l’aide des champs suivants :

- Plage de dates
- Initié par (intervenant)
- Catégorie
- Type de ressource d’activité
- Activité

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/23.png "Journaux d’Audit")


Le filtre **Plage de dates** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Personnalisée

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Le filtre **Initié par** vous permet de définir le nom d’un intervenant ou son nom principal universel (UPN).

Le filtre **Catégorie** vous permet de sélectionner un des filtres suivants :

- Tous
- Catégorie principale
- Répertoire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre service
- Approvisionnement de comptes - Substitution de mot de passe automatique
- Utilisateurs invités
- Service MIM
- Identity Protection
- B2C

Le filtre **Type de ressource d’activité** vous permet de sélectionner un des filtres suivants :

- Tous 
- Groupe
- Répertoire
- Utilisateur
- Application
- Stratégie
- Appareil
- Autres

Lorsque vous sélectionnez le **type de ressource d’activité** **Groupe**, vous obtenez une catégorie de filtre supplémentaire qui vous permet de fournir également une **source** :

- Azure AD
- O365


Le filtre **Activité** est basé sur la catégorie et le type de ressource d’activité que vous choisissez. Vous pouvez sélectionner une activité spécifique que vous souhaitez voir ou toutes les choisir. 

Vous pouvez obtenir la liste de toutes les activités d’audit à l’aide de l’API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta où $tenantdomain correspond à votre nom de domaine. Vous pouvez également consulter l’article sur les [évènements de rapports d’audit](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Raccourcis de journaux d’audit

En plus d’**Azure Active Directory**, le portail Azure vous offre deux autres points d’entrée pour les données d’audit :

- Utilisateurs et groupes
- Applications d’entreprise

### <a name="users-and-groups-audit-logs"></a>Journaux d’audit des utilisateurs et des groupes

Les rapports d’audit basés sur les utilisateurs et les groupes vous permettent d’obtenir des réponses aux questions telles que :

- Quels types de mises à jour ont été appliquées aux utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Combien de mots de passe ont été modifiés ?

- Qu’a fait un administrateur dans un répertoire ?

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des groupes comportant des modifications d’adhésion ?

- Les propriétaires de groupe ont-ils été modifiés ?

- Quelles licences ont été attribuées à un groupe ou un utilisateur ?

Si vous souhaitez simplement consulter les données d’audit connexes aux utilisateurs et aux groupes, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** de **Utilisateurs et groupes**. Dans ce point d’entrée, **Utilisateurs et groupes** est présélectionné comme **Type de ressource d’activité**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/93.png "Journaux d’Audit")

### <a name="enterprise-applications-audit-logs"></a>Journaux d’audit d’applications d’entreprise

Les rapports d’audit basés sur les applications vous permettent d’obtenir des réponses aux questions telles que :

* Quelles applications ont été ajoutées ou mises à jour ?
* Quelles applications ont été supprimées ?
* Le principal du service d’une application a-t-il été modifié ?
* Les noms des applications ont-ils été modifiés ?
* Qui a donné son consentement à une application ?

Si vous souhaitez simplement consulter les données d’audit connexes à vos applications, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** du panneau **Applications d’entreprise**. Dans ce point d’entrée, **Applications d’entreprise** est présélectionné comme **Type de ressource d’activité**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/134.png "Journaux d’Audit")

Vous pouvez filtrer davantage cette vue pour afficher uniquement les **groupes** ou les **utilisateurs**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/25.png "Journaux d’Audit")


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble des rapports, consultez [Création de rapports Azure Active Directory](active-directory-reporting-azure-portal.md).

