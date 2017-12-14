---
title: "Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur Azure Active Directory pour les applications SaaS | Documents Microsoft"
description: "Découvrez comment vérifier l’état des tâches d’approvisionnement automatique de comptes d’utilisateur et comment résoudre les problèmes d’approvisionnement d’utilisateurs individuels."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: d2ec1b25e39717fb2411d35e3b0a9f5eb3a977be
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Didacticiel : Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur


Azure Active Directory comprend un [service d’approvisionnement de comptes d’utilisateur](active-directory-saas-app-provisioning.md) qui permet d’automatiser l’approvisionnement ou la suppression de comptes d’utilisateur dans des applications SaaS et d’autres systèmes, pour gérer le cycle de vie des identités de bout en bout. Azure AD prend en charge des connecteurs préintégrés d’approvisionnement d’utilisateur pour l’ensemble des applications et systèmes dans la section « Suggestions » de la [Galerie d’applications Azure AD](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Cet article décrit comment vérifier l’état de l’approvisionnement des travaux après qu’ils ont été configurés, et résoudre les problèmes d’approvisionnement d’utilisateurs individuels et de groupes.

## <a name="overview"></a>Vue d'ensemble

Les connecteurs d’approvisionnement sont principalement définis et configurés via le [portail de gestion Azure](https://portal.azure.com), en suivant la [documentation fournie](active-directory-saas-tutorial-list.md) pour l’application où un approvisionnement de compte d’utilisateur est souhaité. Une fois configurés et opérationnels, les travaux d’approvisionnement pour une application peuvent faire l’objet de rapports créés à l’aide de l’une des deux méthodes suivantes :

* **Portail de gestion Azure** : cet article décrit principalement la récupération d’informations de rapport à partir du [portail de gestion Azure](https://portal.azure.com), qui fournit un rapport de synthèse ainsi que des journaux d’audit détaillés sur l’approvisionnement pour une application donnée.

* **API d’audit**  : Azure Active Directory fournit également une API d’audit qui permet la récupération par programmation des journaux d’audit détaillés sur l’approvisionnement. Pour une documentation spécifique sur l’utilisation de cette API, voir [Référence d’API d’audit Azure Active Directory](active-directory-reporting-api-audit-reference.md). Bien que cet article ne traite pas spécifiquement de l’utilisation de l’API, il détaille les types d’événements d’approvisionnement qui sont enregistrés dans le journal d’audit.

### <a name="definitions"></a>Définitions

Cet article utilise les termes suivants, définis ci-dessous :

* **Système source** : référentiel d’utilisateurs à partir duquel le service d’approvisionnement Azure AD se synchronise. Azure Active Directory est le système source pour la majorité des connecteurs d’approvisionnement pré-intégrés, mais il existe des exceptions (par exemple, la synchronisation entrante des jours de travail).

* **Système cible** : référentiel des utilisateurs avec lequel le service d’approvisionnement Azure AD se synchronise. Il s’agit généralement d’une application SaaS (par exemple, Salesforce, ServiceNow, Google Apps, Dropbox for Business) mais, dans certains cas, il peut s’agir d’un système local tel qu’Active Directory (par exemple, une synchronisation entrante des jours de travail sur Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Obtention de rapports d’approvisionnement du portail de gestion Azure

Pour obtenir des informations de rapport d’approvisionnement pour une application donnée, ouvrez le [portail de gestion Azure](https://portal.azure.com), puis accédez à l’Application d’entreprise pour laquelle l’approvisionnement est configuré. Par exemple, si vous approvisionnez des utilisateurs pour LinkedIn Elevate, le chemin de navigation pour accéder aux détails de l’application est le suivant :

**Azure Active Directory &gt; Applications d’entreprise &gt; Toutes les applications &gt; LinkedIn Elevate**

À ce stade, vous pouvez accéder tant au rapport de synthèse sur l’approvisionnement qu’aux journaux d’audit de l’approvisionnement, tous deux décrits ci-dessous.


### <a name="provisioning-summary-report"></a>Rapport de synthèse sur l’approvisionnement

Le rapport de synthèse sur l’approvisionnement est visible sous l’onglet **Approvisionnement** pour une application données. Il figure dans la section Détails de la synchronisation sous **Paramètres**, et fournit les informations suivantes :

* Le nombre total d’utilisateurs et de groupes qui ont été synchronisés et figurent actuellement dans l’étendue pour l’approvisionnement entre le système source et le système cible.

* La dernière exécution de la synchronisation. Les synchronisations se produisent généralement toutes les 20 à 40 minutes après l’achèvement d’une synchronisation complète.

* Indique si une synchronisation initiale complète a été effectuée.

* Indique si le processus d’approvisionnement a été mis en quarantaine, et la raison de la mise en quarantaine (par exemple, un échec de communication avec le système cible en raison d’informations d’identification d’administration non valides).

Le rapport de synthèse sur l’approvisionnement doit être le premier emplacement que les administrateurs consultent pour vérifier l’intégrité opérationnelle du travail d’approvisionnement.

 ![Rapport de synthèse](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Journaux d’audit de l’approvisionnement
Toutes les activités effectuées par le service d’approvisionnement sont enregistrées dans les journaux d’audit d’Azure AD, qui peuvent être consultés sous l’onglet **Journaux d’audit**, dans la catégorie **Approvisionnement de comptes**. Les types d’événements d’activité journalisés sont les suivants :

* **Événements d’importation** : un événement d’importation est enregistré chaque fois que le service d’approvisionnement Azure AD récupère des informations concernant un utilisateur ou un groupe à partir d’un système source ou d’un système cible. Lors de la synchronisation, les utilisateurs sont d’abord extraits du système source, les résultats étant enregistrés en tant qu’événements d’importation. Les ID correspondants des utilisateurs extraits font ensuite l’objet d’une requête sur le système cible pour vérifier s’ils existent, les résultats étant également enregistrés en tant qu’événements d’importation. Ces événements enregistrent tous les attributs utilisateur mappés ainsi que leurs valeurs observées par le service d’approvisionnement Azure AD au moment de l’événement. 

* **Événements de règle de synchronisation**: ces événements rapportent sur les résultats des règles de mappage d’attribut et de tous les filtres d’étendue configurés, après que les données utilisateur ont été importées et évaluées à partir des systèmes source et cible. Par exemple, si un utilisateur dans un système source est censé figurer dans l’étendue pour l’approvisionnement, et ne pas exister dans le système cible, cet événement enregistre le fait que l’utilisateur sera approvisionné dans le système cible. 

* **Événements d’exportation** : un événement d’exportation est enregistré chaque fois que service d’approvisionnement Azure AD écrit un objet groupe ou compte d’utilisateur dans un système cible. Ces événements enregistrent l’ensemble des attributs et de leurs valeurs qui ont été écrits par le service d’approvisionnement Azure AD au moment de l’événement. Si une erreur s’est produite lors de l’écriture de l’objet groupe ou compte d’utilisateur dans le système cible, elle s’affiche ici.

* **Événements de traitement d’escrow** : un événement de traitement d’escrow se produit quand le service d’approvisionnement rencontre un échec lors d’une tentative d’exécution d’une opération et commence à retenter celle-ci durant un intervalle de temps d’interruption. Un événement escrow est enregistré à chaque abandon d’une opération d’approvisionnement.

Lors de l’examen d’événements d’approvisionnement pour un utilisateur, les événements se produisent généralement dans l’ordre suivant :

1. Événement d’importation : l’utilisateur est extrait du système source.

2. Événement d’importation : le système cible est interrogé afin de vérifier l’existence de l’utilisateur extrait.

3. Événement de règle de synchronisation : les données utilisateur des systèmes source et cible sont évaluées par rapport aux règles de mappage d’attribut et aux filtres d’étendue configurés afin de déterminer l’action éventuelle à effectuer.

4. Événement d’exportation : si l’événement de règle de synchronisation a dicté qu’une action doit être exécutée (par exemple, Ajouter, Mettre à jour, Supprimer), les résultats de l’action sont enregistrés dans l’événement d’exportation.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Recherche d’événements d’approvisionnement pour un utilisateur spécifique

Le cas d’usage le plus courant des journaux d’audit de l’approvisionnement consiste à vérifier l’état d’approvisionnement d’un compte d’utilisateur. Pour examiner les derniers événements d’approvisionnement pour un utilisateur spécifique :

1. Accédez à la section **Journaux d’audit**.

2. Dans le menu **Catégorie**, sélectionnez **Approvisionnement de comptes**.

3. Dans le menu **Plage de dates**, sélectionnez la plage de dates dans laquelle vous voulez rechercher.

4. Dans la barre **Recherche**, entrez l’ID de l’utilisateur que vous souhaitez rechercher. Le format de la valeur d’ID doit correspondre à ce que vous avez sélectionné en tant qu’ID correspondant principale dans la configuration du mappage d’attribut (par exemple, userPrincipalName ou numéro d’ID d’employé). La valeur d’ID requise est visible dans la colonne Cible(s).

5. Appuyez sur Entrée pour rechercher. Les événements d’approvisionnement les plus récents sont retournés en premier.

6. Si des événements sont retournés, notez les types d’activités et si celles-ci ont réussi ou échoué. Si aucun résultat n’est retourné, cela signifie que l’utilisateur n’existe pas ou qu’il n’a pas encore été détecté par le processus d’approvisionnement si une synchronisation complète n’a pas encore été accomplie.

7. Cliquez sur les différents événements pour afficher des détails étendus, dont toutes les propriétés utilisateur qui ont été extraites, évaluées ou écrites dans le cadre de l’événement.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Conseils pour consulter les journaux d’audit de l’approvisionnement

Pour optimiser la lisibilité dans le portail Azure, sélectionnez le bouton **Colonnes**, puis choisissez les colonnes suivantes :

* **Date** : indique la date à laquelle l’événement s’est produit.
* **Cible(s)** : indique le nom de l’application et l’ID de l’utilisateur qui font l’objet de l’événement.
* **Activité** : indique le type d’activité, comme décrit précédemment.
* **Statut** : indique si l’événement a réussi ou non.
* **Raison du statut** : résumé de ce qui s’est passé dans le cadre de l’événement d’approvisionnement.


## <a name="troubleshooting"></a>résolution des problèmes

Le rapport de synthèse sur l’approvisionnement et les journaux d’audit jouent un rôle clé pour aider les administrateurs à résoudre différents problèmes d’approvisionnement de comptes d’utilisateur.

Pour obtenir des instructions basées sur un scénario concernant la manière de résoudre des problèmes d’approvisionnement automatique d’utilisateurs, voir [Problèmes lors de la configuration et de l’approvisionnement des utilisateurs pour une application](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
