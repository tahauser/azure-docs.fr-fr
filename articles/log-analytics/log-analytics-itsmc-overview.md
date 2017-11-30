---
title: IT Service Management Connector dans Azure Log Analytics | Microsoft Docs
description: "Cet article fournit une vue d’ensemble du connecteur de gestion des services informatiques (ITSMC) et des informations sur l’utilisation de cette solution pour surveiller et gérer les éléments de travail ITSM dans OMS Log Analytics et résoudre rapidement les problèmes éventuels."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: bd384255b3c46b3ae88b1269ab26e0ddaa6f6e77
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Gérer de manière centralisée les éléments de travail ITSM à l’aide d’IT Service Management Connector (version préliminaire)

![Symbole d’IT Service Management Connector](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management Connector (ITSMC) assure une intégration bidirectionnelle entre un produit/service ITSM (IT Service Management) et Log Analytics.  À travers cette connexion, vous pouvez créer des incidents, des alertes ou des événements dans un produit ITSM sur la base des alertes Log Analytics, d’enregistrements de journal ou d’alertes Azure. Le connecteur importe aussi des données telles que des incidents et des demandes de modification du produit ITSM dans OMS Log Analytics.

Avec ITSMC, vous pouvez :

  - Intégrer des alertes opérationnelles à vos méthodes de gestion des incidents dans l’outil ITSM de votre choix.
    - Créez des éléments de travail (tels que des alertes, des événements et des incidents) dans ITSM à partir des alertes OMS et via la fonctionnalité de recherche dans les journaux.
    - Créez des éléments de travail basés sur vos alertes Azure Activity Log via une action ITSM dans les groupes d’actions.

  - Unifier les données de surveillance, de journal et de gestion des services utilisées à l’échelle de votre organisation.
    - Mettez en corrélation les données d’incidents et de demandes de modification de vos outils ITSM avec les données de journal pertinentes dans l’espace de travail Log Analytics.   
    - Examinez des tableaux de bord de haut niveau pour obtenir une vue d’ensemble des incidents, des demandes de modification et des systèmes impactés.
    - Écrivez des requêtes Log Analytics pour obtenir des insights dans les données de gestion des services.

## <a name="adding-the-it-service-management-connector-solution"></a>Ajout de la solution IT Service Management Connector

Ajoutez la solution IT Service Management Connector à votre espace de travail Log Analytics en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

La vignette ITSMC telle que vous pouvez la voir dans la galerie de solutions est présentée ici :

![vignette du connecteur](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Une fois ajoutée, la solution IT Service Management Connector s’affiche sous **OMS** > **Paramètres** > **Sources connectées**.

![ITSMC connecté](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Par défaut, ITSMC actualise les données de connexion toutes les 24 heures. Pour actualiser instantanément les données de votre connexion en rapport avec des modifications ou mises à jour de modèle que vous apportez, cliquez sur le bouton « Actualiser » en regard de votre connexion.

 ![Actualisation d’ITSMC](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-itsmc-connection-with-your-itsm-productsservices"></a>Configuration de la connexion ITSMC avec vos produits/services ITSM

ITSM permet de se connecter à **System Center Service Manager**, **ServiceNow**, **Provance** et **Cherwell**.

Utilisez les procédures suivantes qui vous concernent :

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Utilisation de la solution

Une fois le connecteur configuré, il démarre la collecte des données à partir du produit/service ITSM connecté. Selon le nombre d’incidents et de demandes de modification présents dans le produit/service ITSM, la synchronisation initiale s’effectue normalement en quelques minutes.

> [!NOTE]
> - Les données importées à partir du produit ITSM par la solution ITSM s’affichent dans Log Analytics sous forme d’enregistrements de journal du type **ServiceDesk_CL**.
> - Un enregistrement de journal contient un champ nommé **ServiceDeskWorkItemType_s**, qui est soit un incident soit une requête de modification, c’est-à-dire les deux types de données importées à partir du produit ITSM.

## <a name="data-synced-from-itsm-product"></a>Données synchronisées à partir du produit ITSM
Les incidents et les demandes de modification sont synchronisés entre votre produit ITSM et votre espace de travail Log Analytics.
Les informations suivantes présentent des exemples de données collectées par ITSM :

> [!NOTE]

> Selon le type d’élément de travail importé dans Log Analytics, l’événement **ServiceDesk_CL** contient les champs suivants :

**Élément de travail :****Incidents**  
ServiceDeskWorkItemType_s="Incident"

**Champs**

- ServiceDeskConnectionName
- ID du service d’assistance
- State
- Urgence
- Impact
- Priorité
- Escalade
- Créé par
- Résolu par
- Fermé par
- Source
- Affecté à
- Catégorie
- Intitulé
- Description
- Date de création
- Date de fermeture
- Date de résolution
- Date de dernière modification
- Ordinateur


**Élément de travail :****Demandes de modification**

ServiceDeskWorkItemType_s="ChangeRequest"

**Champs**
- ServiceDeskConnectionName
- ID du service d’assistance
- Créé par
- Fermé par
- Source
- Affecté à
- Intitulé
- Type
- Catégorie
- State
- Escalade
- État conflictuel
- Urgence
- Priorité
- Risque
- Impact
- Affecté à
- Date de création
- Date de fermeture
- Date de dernière modification
- Date de la demande
- Date de début prévue
- Date de fin prévue
- Date de début du travail
- Date de fin du travail
- Description
- Ordinateur

## <a name="output-data-for-a-servicenow-incident"></a>Données de sortie pour un incident ServiceNow

| Champ OMS | Champ ITSM |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Urgence |
| Impact_s |Impact|
| Priority_s | Priorité |
| CreatedBy_s | Ouvert par |
| ResolvedBy_s | Résolu par|
| ClosedBy_s  | Fermé par |
| Source_s| Type de contact |
| AssignedTo_s | Affecté à  |
| Category_s | Catégorie |
| Title_s|  Brève description |
| Description_s|  Remarques |
| CreatedDate_t|  Ouvert |
| ClosedDate_t| Fermé|
| ResolvedDate_t|Résolu|
| Ordinateur  | Élément de configuration |

## <a name="output-data-for-a-servicenow-change-request"></a>Données de sortie pour une demande de modification ServiceNow

| Champ OMS | Champ ITSM |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Demandé par |
| ClosedBy_s | Fermé par |
| AssignedTo_s | Affecté à  |
| Title_s|  Brève description |
| Type_s|  Type |
| Category_s|  Catégorie |
| CRState_s|  State|
| Urgency_s|  Urgence |
| Priority_s| Priorité|
| Risk_s| Risque|
| Impact_s| Impact|
| RequestedDate_t  | Date demandée |
| ClosedDate_t | Date de fermeture |
| PlannedStartDate_t  |     Date de début prévue |
| PlannedEndDate_t  |   Date de fin prévue |
| WorkStartDate_t  | Date de début réelle |
| WorkEndDate_t | Date de fin réelle|
| Description_s | Description |
| Ordinateur  | Élément de configuration |

**Exemple d’écran Log Analytics pour les données ITSM :**

![Écran Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="itsmc-integration-with-other-oms-solutions"></a>Intégration d’ITSMC à d’autres solutions OMS

Le connecteur ITSM prend actuellement en charge l’intégration avec la solution Service Map.

La solution Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques. Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent.

Pour en savoir plus : [Carte de service](../operations-management-suite/operations-management-suite-service-map.md).

Si vous utilisez aussi la solution Service Map, vous pouvez afficher les éléments de service d’assistance créés dans les solutions ITSM comme dans l’exemple suivant :

![Intégration de ServiceMap](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Créer des éléments de travail ITSM pour des alertes OMS

Une fois la solution ITSM en place, vous pouvez configurer les alertes OMS pour qu’elles déclenchent la création d’éléments de travail dans votre outil ITSM connecté. Procédez comme suit :

1. Dans la fenêtre **Recherche dans les journaux**, exécutez une requête de recherche dans les journaux pour afficher les données. Les résultats de la requête correspondent aux sources des éléments de travail.
2. Dans **Recherche dans les journaux**, cliquez sur **Alerte** pour ouvrir la page **Ajouter une règle d’alerte**.

    ![Écran Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. Dans la fenêtre **Ajouter une règle d’alerte**, spécifiez les champs **Nom**, **Gravité**, **Requête de recherche** et **Critères d’alerte** (fenêtre de temps/mesures métriques).
4. Sélectionnez **Oui** pour **Actions ITSM**.
5. Sélectionnez votre connexion ITSM dans la liste **Sélectionner une connexion**.
6. Spécifiez les informations requises.
7. Pour créer un élément de travail distinct pour chaque entrée de journal de cette alerte, cochez la case **Créer des éléments de travail distincts pour chaque entrée de journal**

    Ou

    laissez cette case décochée pour créer un seul élément de travail pour l’ensemble des entrées de journal associées à cette alerte.

7. Cliquez sur **Enregistrer**.

L’alerte OMS que vous avez créée est visible sous **Paramètres**>**Alertes**. Les éléments de travail de la connexion ITSM correspondante sont créés lorsque les critères de l’alerte spécifiée sont remplis.

## <a name="create-itsm-work-items-from-oms-logs"></a>Créer des éléments de travail ITSM à partir de journaux OMS

Vous pouvez également créer des éléments de travail dans les sources ITSM connectées directement à partir d’un enregistrement de journal. Procédez comme suit :

1. Sous **Recherche dans les journaux**, recherchez les données requises, sélectionnez les informations détaillées, puis cliquez sur **Créer un élément de travail**.

    La fenêtre **Créer un élément de travail ITSM** s’affiche :

    ![Écran Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Ajoutez les informations suivantes :

  - **Titre de l’élément de travail** : titre de l’élément de travail.
  - **Description de l’élément de travail** : description du nouvel élément de travail.
  - **Ordinateur concerné** : nom de l’ordinateur sur lequel les données de journal ont été trouvées.
  - **Sélectionner une connexion** : connexion ITSM dans laquelle vous souhaitez créer cet élément de travail.
  - **Élément de travail** : type d’élément de travail.

3. Pour utiliser un modèle d’élément de travail existant pour un incident, cliquez sur **Oui** sous l’option **Générer l’élément de travail en fonction du modèle**, puis cliquez sur **Créer**.

    Ou,

    Cliquez sur **Non** si vous souhaitez fournir des valeurs personnalisées.

4. Indiquez les valeurs appropriées dans les zones de texte **Type de contact**, **Impact**, **Urgence**, **Catégorie** et **Sous-catégorie**, puis cliquez sur **Créer**.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Créer des éléments de travail ITSM à partir d’alertes Azure

ITSMC est intégré à des groupes d’actions.

Les [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) offrent une méthode modulaire et réutilisable pour déclencher des actions pour vos alertes Azure. Grâce à l’action ITSM dans les groupes d’actions, vous pouvez créer des éléments de travail dans votre produit ITSM qui inclut une connexion à une solution de connecteur ITSM.

Procédez comme suit :

1. Dans le portail Azure, cliquez sur **Moniteur**.
2. Dans le volet gauche, cliquez sur **Groupes d’actions**. La fenêtre **Ajouter un groupe d’actions** s’affiche.

    ![Groupes d’actions](media/log-analytics-itsmc/action-groups.png)

3. Attribuez un **Nom** et un **Nom court** à votre groupe d’actions. Sélectionnez le **Groupe de ressources** et l’**Abonnement** pour lesquels vous voulez créer votre groupe d’actions.

    ![Détails du groupe d’actions](media/log-analytics-itsmc/action-groups-details.png)

4. Dans la liste d’actions, sélectionnez **ITSM** dans le menu déroulant **Type d’action**. Entrez un **Nom** pour l’action et cliquez sur **Modifier les détails**.
5. Sélectionnez l’**Abonnement** dans lequel se trouve votre espace de travail Log Analytics. Sélectionnez le nom de **Connexion** (le nom de votre connecteur ITSM) suivi du nom de votre espace de travail. Par exemple, « MaSolutionTSMMConnector(MonEspaceDeTravail) ».

    ![Détails de l’action ITSM](./media/log-analytics-itsmc/itsm-action-details.png)

6. Sélectionnez le type d’**Élément de travail** dans le menu déroulant.
   Choisissez d’utiliser un modèle existant ou renseignez les champs requis par votre produit ITSM.
7. Cliquez sur **OK**.

Lorsque vous créez/modifiez une règle d’alerte Azure, utilisez un groupe d’actions qui contient une action ITSM. Quand l’alerte se déclenche, l’élément de travail est créé dans l’outil ITSM.

>[!NOTE]

> Seules les alertes de journal d’activité prennent en charge l’action ITSM ; les autres alertes Azure ne la prennent pas en charge.


## <a name="troubleshoot-itsm-connections-in-oms"></a>Dépanner des connexions ITSM dans OMS
1.  Si la connexion échoue à partir de l’interface utilisateur de la source connectée avec un message **Erreur lors de l’enregistrement de la connexion**, procédez comme suit :
 - Pour les connexions ServiceNow, Cherwell et Provance, vérifiez que vous avez correctement entré le nom d’utilisateur, le mot de passe, l’ID client et la clé secrète client pour chacune des connexions.
        - vérifiez que vous disposez de privilèges suffisants dans le produit ITSM correspondant pour établir la connexion.
 - Pour les connexions Service Manager, vérifiez que l’application web est correctement déployée et que la connexion hybride est créée. Pour vérifier que la connexion est établie avec l’ordinateur Service Manager local, accédez à l’URL de l’application web, comme décrit dans la documentation concernant l’établissement d’une [connexion hybride](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

2.  Si les données de ServiceNow ne sont pas synchronisées dans Log Analytics, vérifiez que l’instance ServiceNow n’est pas en état de veille. Parfois, les instances de développement ServiceNow entrent en veille quand elles restent longtemps inactives. Autrement, signalez le problème.
3.  Si des alertes OMS se déclenchent mais qu’aucun élément de travail n’est créé dans le produit ITSM ou qu’aucun élément de configuration n’est créé/lié à des éléments de travail ou pour obtenir d’autres informations génériques, examinez les emplacements suivants :
 -  ITSM : la solution affiche un résumé des connexions, éléments de travail, ordinateurs, etc. Cliquez sur la vignette indiquant **État du connecteur**. Vous accédez alors à **Recherche dans les journaux** avec la requête appropriée. Pour plus d’informations, consultez les enregistrements de journal pour lesquels LogType_S a la valeur ERROR.
 - Page **Recherche dans les journaux** : examinez les erreurs/informations connexes directement en utilisant la requête *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Résoudre les problèmes de déploiement de l’application web Service Manager
1.  En cas de problèmes de déploiement d’application web, vérifiez que vous disposez des autorisations suffisantes dans l’abonnement mentionné pour créer/déployer des ressources.
2.  Si vous obtenez l’erreur **« Référence d’objet non définie sur une instance d’un objet »** pendant l’exécution du [script](log-analytics-itsmc-service-manager-script.md), vérifiez que vous avez entré des valeurs valides sous la section **Configuration utilisateur**.
3.  Si vous échouez à créer l’espace de noms du relais Service Bus, vérifiez que le fournisseur de ressources requis est inscrit dans l’abonnement. S’il n’est pas inscrit, créez manuellement l’espace de noms Service Bus Relay à partir du portail Azure. Vous pouvez également le créer lors de la [création de la connexion hybride](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) à partir du portail Azure.


## <a name="contact-us"></a>Nous contacter

Pour toute question ou tout commentaire à propos de la solution IT Service Management Connector, contactez-nous à l’adresse [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes
[Ajouter des produits/services ITSM à IT Service Management Connector](log-analytics-itsmc-connections.md).
