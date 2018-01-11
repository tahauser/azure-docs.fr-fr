---
title: "Rechercher les rapports d’activité utilisateur Azure Active Directory dans le portail Azure | Microsoft Docs"
description: "Découvrez où se trouvent les rapports d’activité utilisateur Azure Active Directory sur le portail Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: dhanyahk
ms.openlocfilehash: 23c186e268e9a43982ec6c34d350900793fad8de
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Trouver les rapports d’activité sur le Portail Azure

Dans cet article, nous expliquons comment trouver les rapports d’activité utilisateur Azure Active Directory sur le portail Azure.

## <a name="whats-new"></a>Nouveautés

Dans le portail Azure Classic, les rapports ont été répartis dans des catégories distinctes :
* Rapports de sécurité
* Rapports d’activité
* Rapports d’application intégrée

### <a name="activity-and-integrated-app-reports"></a>Rapports d’activité et d’application intégrée

Dans le portail Azure, pour la création de rapports basés sur le contexte, les rapports existants sont fusionnés en une seule vue. Une seule API sous-jacente fournit les données de la vue.

Pour afficher cette vue, dans le panneau **Azure Active Directory**, sous **Activité**, sélectionnez **Journaux d’audit**.

![Journaux d’audit](./media/active-directory-reporting-migration/482.png "Journaux d’Audit")

Les rapports suivants sont consolidés dans cette vue :

* Rapport d’audit
* Activité de réinitialisation de mot de passe
* Activité de l’enregistrement de la réinitialisation de mot de passe
* Activité de groupes en libre-service
* Changements de nom de groupe d’Office 365
* Activité d’approvisionnement de compte
* État de substitution de mot de passe
* Erreurs de configuration de compte


Le rapport d’utilisation de l’application a été amélioré et inclus dans la vue **Connexions**. Pour afficher cette vue, sur le panneau **Azure Active Directory**, sous **Activité**, sélectionnez **Connexions**.

![Vue Connexions](./media/active-directory-reporting-migration/483.png "Vue Connexions")

La vue **Connexions** inclut toutes les connexions des utilisateurs. Vous pouvez utiliser ces informations pour obtenir des informations sur l’utilisation de l’application. Vous pouvez également afficher des informations sur l’utilisation de l’application dans la vue d’ensemble **Applications d’entreprise** sous la section **Gérer**.

![Applications d’entreprise](./media/active-directory-reporting-migration/484.png "Applications d’entreprise")

## <a name="access-a-specific-report"></a>Accéder à un rapport spécifique

Bien que le portail Azure offre une vue unique, vous pouvez également consulter des rapports spécifiques.

### <a name="audit-logs"></a>Journaux d’audit

En réponse aux commentaires des clients, dans le portail Azure, vous pouvez utiliser un filtrage avancé pour accéder aux données de votre choix. Vous pouvez utiliser le filtre *Catégorie de l’activité*, qui répertorie les différents types de journaux d’activité dans Azure AD. Pour restreindre les résultats à ce que vous recherchez, vous pouvez sélectionner une catégorie.

Par exemple, si vous souhaitez simplement obtenir les activités liées aux réinitialisations de mot de passe libre-service, vous pouvez choisir la catégorie **Gestion des mots de passe en libre-service**. Les catégories affichées sont basées sur la ressource que vous utilisez.  

![Options de catégories dans la page Filtrer les journaux d’audit](./media/active-directory-reporting-migration/06.png "Options de catégories dans la page Filtrer les journaux d’audit")

Les catégories d’activités sont les suivantes :

- Annuaire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre-service
- Approvisionnement des comptes

### <a name="application-usage"></a>Utilisation des applications

Pour afficher plus d’informations sur l’utilisation des applications (pour toutes les applications ou une seule application), sous **Activité**, sélectionnez **Connexions**. Pour restreindre les résultats, vous pouvez filtrer par nom d’utilisateur ou par nom d’application.

![Page Filtrer les événements de connexion](./media/active-directory-reporting-migration/07.png "Page Filtrer les événements de connexion")

### <a name="security-reports"></a>Rapports de sécurité

#### <a name="azure-ad-anomalous-activity-reports"></a>Rapports d’activités anormales Azure AD

Les rapports de sécurité sur les activités anormales d’Azure Active Directory issus du portail Azure Classic ont été consolidés pour vous fournir une seule vue centralisée. Cette vue affiche tous les événements liés à la sécurité qu’Azure AD peut détecter et signaler dans des rapports.

Le tableau suivant répertorie les rapports de sécurité sur les activités anormales d’Azure AD et les types d’événements à risque correspondants dans le portail Azure.

| Rapport d’activités anormales Azure AD |  Type d’événement à risque signalé par Identity Protection|
| :--- | :--- |
| Utilisateurs avec des informations d’identification volées | Informations d’identification divulguées |
| Activité de connexion anormale | Voyage impossible vers des emplacements inhabituels |
| Connexions à partir d’appareils potentiellement infectés | Connexions depuis des appareils infectés|
| Connexions à partir de sources inconnues | Connexions depuis des adresses IP anonymes |
| Connexions depuis des adresses IP avec des activités suspectes | Connexions depuis des adresses IP avec des activités suspectes |
| - | Connexions depuis des emplacements non connus |

Les rapports de sécurité sur les activités anormales d’Azure AD suivants ne sont pas inclus en tant qu’événements à risque dans le portail Azure :

* Connexions après plusieurs échecs
* Connexions depuis plusieurs zones géographiques

Ces rapports sont toujours disponibles dans le portail Azure Classic, mais ils seront déconseillés ultérieurement.

Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Événements à risque détectés

Dans le portail Azure, vous pouvez accéder aux rapports sur les événements à risque détectés dans le panneau **Azure Active Directory** sous **Sécurité**. Les événements à risque détectés sont suivis dans les rapports suivants :   

- les utilisateurs à risque ;
- les connexions risquées.

![Rapports de sécurité](./media/active-directory-reporting-migration/04.png "Rapports de sécurité")

Pour plus d’informations sur les rapports de sécurité, consultez :

- [Rapport sur la sécurité des utilisateurs courant un risque dans le portail Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Rapports sur les connexions risquées dans le portail Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Comparaison des rapports d’activité entre le Portail Azure Classic et le Portail Azure

Le tableau de cette section répertorie les rapports existants dans le portail Azure Classic. Il explique également comment obtenir les mêmes informations dans le portail Azure.

Pour afficher toutes les données d’audit, dans le panneau **Azure Active Directory**, sous **Activité**, accédez à **Journaux d’audit**.

![Journaux d’audit](./media/active-directory-reporting-migration/61.png "Journaux d’Audit")

| Portail Azure Classic                 | Pour rechercher dans le portail Azure                                                         |
| ---                                  | ---                                                                        |
| Journaux d’audit                           | Pour **Catégorie d’activité**, sélectionnez **Annuaire principal**.                       |
| Activité de réinitialisation de mot de passe              | Pour **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**. |
| Activité de l’enregistrement de la réinitialisation de mot de passe | Pour **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**.     |
| Activité de groupes en libre-service         | Pour **Catégorie d’activité**, sélectionnez **Gestion des groupes en libre-service**.        |
| Activité d’approvisionnement de compte        | Pour **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.         |
| État de substitution de mot de passe             | Pour **Catégorie d’activité**, sélectionnez **Substitution automatique de mot de passe d’application**.      |
| Erreurs de configuration de compte          | Pour **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.        |
| Changements de nom de groupe d’Office 365         | Pour **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**. Pour **Type de ressource activité**, sélectionnez **Groupe**. Pour **Source de l’activité**, sélectionnez **Groupes O365**.|

Pour afficher le rapport **d’utilisation des applications**, dans le panneau **Azure Active Directory**, sous **Gérer**, sélectionnez **Applications d’entreprise**, puis **Connexions**.


![Rapport sur les connexions d’applications d’entreprise](./media/active-directory-reporting-migration/199.png "Rapport sur les connexions d’applications d’entreprise")

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble des rapports, consultez [Création de rapports Azure Active Directory](active-directory-reporting-azure-portal.md).
