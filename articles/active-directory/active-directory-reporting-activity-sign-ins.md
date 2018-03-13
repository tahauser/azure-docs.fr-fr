---
title: "Rapports d’activité de connexion dans le portail Azure Active Directory | Microsoft Docs"
description: "Présentation des rapports d’activité de connexion dans le portail Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ad7b1aae4ee14e46a5df6be0ebc29bfd455852d4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité de connexion dans le portail Azure Active Directory

Grâce à la fonction de création de rapports Azure Active Directory (Azure AD) dans le [portail Azure](https://portal.azure.com), vous pouvez obtenir les informations dont vous avez besoin pour évaluer l’état de votre environnement.

L’architecture de création de rapports dans Azure Active Directory comprend les composants suivants :

- **Activité** 
    - **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs
    - **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, consultez Connexions risquées.
    - **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, consultez Utilisateurs avec indicateur de risque.

Cette rubrique vous donne une vue d’ensemble des activités de connexion.

## <a name="pre-requisite"></a>Conditions préalables

### <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
* Utilisateurs ayant le rôle d’administrateur de sécurité ou de lecteur de la sécurité
* Administrateurs généraux
* Tous les utilisateurs (non administrateurs) peuvent accéder à leurs propres connexions 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>De quelle licence Azure AD avez-vous besoin pour accéder à l’activité de connexion ?
* Votre client doit avoir une licence Azure AD Premium associée pour afficher tous les rapports d’activités de connexion.


## <a name="signs-in-activities"></a>Activités de connexion

Avec les informations fournies par le rapport sur les connexions des utilisateurs, trouvez des réponses aux questions telles que :

* Quel est le modèle de connexion d’un utilisateur ?
* Combien d’utilisateurs se sont connectés au cours d’une semaine ?
* Quel est l’état de ces connexions ?

La zone **Connexions** de la section Activité de **Azure Active** constitue votre premier point d’entrée pour toutes les activités de connexion.


![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/61.png "Activité de connexion")


Un journal d’audit inclut un mode Liste par défaut, qui indique :

- l’utilisateur associé ;
- l’application à laquelle l’utilisateur est connecté ;
- l’état de la connexion ;
- l’heure de la connexion.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/41.png "Activité de connexion")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/19.png "Activité de connexion")

Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/42.png "Activité de connexion")

En cliquant sur un élément dans le mode Liste, vous pouvez obtenir toutes les informations disponibles le concernant.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/43.png "Activité de connexion")


## <a name="filtering-sign-in-activities"></a>Filtrage des activités de connexion

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données de connexion à l’aide des champs suivants :

- Intervalle de temps
- Utilisateur
- Application
- Client
- État de la connexion

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/44.png "Activité de connexion")


Le filtre **Intervalle de temps** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Personnalisée

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Le filtre **Utilisateur** vous permet de spécifier le nom ou le nom d’utilisateur principal de l’utilisateur qui vous intéresse.

Le filtre **Application** vous permet de spécifier le nom de l’application qui vous intéresse.

Le filtre **Client** vous permet de spécifier des informations sur l’appareil qui vous intéresse.

Le filtre **État de la connexion** vous permet de sélectionner l’un des filtres suivants :

- Tous
- Succès
- Échec


## <a name="sign-in-activities-shortcuts"></a>Raccourcis relatifs aux activités de connexion

En plus d’Azure Active Directory, le portail Azure vous offre deux autres points d’entrée pour les données sur les activités de connexion :

- Utilisateurs et groupes
- Applications d’entreprise


### <a name="users-and-groups-sign-ins-activities"></a>Activités de connexion des utilisateurs et des groupes

Avec les informations fournies par le rapport sur les connexions des utilisateurs, trouvez des réponses aux questions telles que :

- Quel est le modèle de connexion d’un utilisateur ?
- Combien d’utilisateurs se sont connectés au cours d’une semaine ?
- Quel est l’état de ces connexions ?



Votre point d’entrée pour ces données est le graphique des connexions des utilisateurs dans la section **Overview** (Vue d’ensemble) sous **Utilisateurs et groupes**.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/45.png "Activité de connexion")

Le graphique des connexions des utilisateurs affiche les agrégations hebdomadaires des connexions de tous les utilisateurs au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/46.png "Activité de connexion")

Lorsque vous cliquez sur un jour dans le graphique des connexions, vous obtenez une liste détaillée des activités de connexion correspondantes.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/41.png "Activité de connexion")

Chaque ligne dans la liste des activités de connexion vous fournit des informations détaillées sur la connexion sélectionnée, par exemple :

* Qui s’est connecté ?
* Qui a l’UPN associé ?
* Quelle application a été la cible de la connexion ?
* Quelle est l’adresse IP de la connexion ?
* Quel est l’état de la connexion ?

L’option **Connexions** vous fournit une vue d’ensemble complète de toutes les connexions utilisateur.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/51.png "Activité de connexion")



## <a name="usage-of-managed-applications"></a>Utilisation des applications gérées

En disposant d’une vue centrée sur les applications de vos données de connexion, vous pouvez répondre aux questions telles que :

* Qui utilise mes applications ?
* Quelles sont les 3 principales applications dans votre organisation ?
* J’ai récemment déployé une application. Comment se comporte-t-elle ?

Les 3 principales applications de votre organisation dans le rapport sur les 30 derniers jours apparaissant dans la section **Vue d’ensemble** sous **Enterprise applications** (Applications d’entreprise) constituent votre point d’entrée.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/64.png "Activité de connexion")

Le graphique d’utilisation des applications affiche les agrégations hebdomadaires des connexions pour vos 3 principales applications au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/47.png "Activité de connexion")

Si vous le souhaitez, vous pouvez définir la focalisation sur une application spécifique.


![Reporting](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Reporting")

Lorsque vous cliquez sur un jour dans le graphique d’utilisation des applications, vous obtenez une liste détaillée des activités de connexion.


![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/48.png "Activité de connexion")


L’option **Connexions** vous fournit une vue d’ensemble complète de tous les événements de connexion à vos applications.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins/49.png "Activité de connexion")



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les codes d’erreur de l’activité des connexions, voir [Codes d’erreur des rapports d’activité des connexions dans le portail Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

