---
title: Ajouter des solutions de gestion Azure Log Analytics | Microsoft Docs
description: "Les solutions de gestion dans Azure représentent une collection de règles logiques, de visualisation et d'acquisition des données qui fournissent des mesures cernant un domaine problématique en particulier."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6ec6dd46d28518baf6f105dd232a4b0e77a8c6f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Ajouter des solutions de gestion Azure Log Analytics dans votre espace de travail

Les solutions de gestion Log Analytics représentent une collection de **règles logiques**, de **visualisation** et **d’acquisition des données** qui fournissent des mesures cernant un domaine problématique en particulier. Cet article liste les solutions de gestion prises en charge par Log Analytics et vous montre comment les ajouter et les supprimer pour un espace de travail à l’aide du portail Azure.

Les solutions de gestion vous offrent des informations plus approfondies pour :

* étudier et résoudre plus rapidement les problèmes opérationnels ;
* collecter et corréler différents types de données machine ;
* vous aider à être proactif avec des activités exposées par la solution.

> [!NOTE]
> Comme Log Analytics inclut une fonctionnalité Recherche dans les journaux, vous n’avez pas besoin d’installer une solution de gestion pour l’activer. Toutefois, en ajoutant des solutions de gestion dans votre espace de travail, vous obtenez des visualisations de données, des recherches suggérées et des informations.

À l’aide de cet article, vous ajoutez des solutions de gestion à un espace de travail à l’aide du portail de la place de marché Azure. Après avoir ajouté une solution, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées à Log Analytics. Le traitement prend généralement quelques minutes à une heure. Une fois les données traitées par le service, vous pouvez les consulter dans Log Analytics.

Vous pouvez facilement supprimer une solution de gestion quand vous n’en avez plus besoin. Lorsque vous supprimez une solution de gestion, ses données ne sont pas envoyées à Log Analytics. Si vous bénéficiez du niveau tarifaire Gratuit, la suppression d’une solution peut réduire la quantité de données utilisées, ce qui vous évitera de dépasser le quota quotidien de données.

## <a name="view-available-management-solutions"></a>Afficher les solutions de gestion disponibles

La place de marché Azure contient la liste des [solutions de gestion pour Log Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Vous pouvez installer des solutions de gestion à partir de la place de marché Azure en cliquant sur le lien **Obtenir maintenant** au bas de chaque solution.

## <a name="add-a-management-solution"></a>Ajout d’une solution de gestion
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement Azure.
2. Sélectionnez **Créer une ressource** > **Surveillance + Gestion** > **Afficher tout**.  
    ![Panneau Surveillance + gestion](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. À droite de **Solutions de gestion**, cliquez sur **Plus**.
5. Dans le panneau **Solutions de gestion**, sélectionnez une solution de gestion à ajouter à un espace de travail.  
    ![Panneau Surveillance + gestion](./media/log-analytics-add-solutions/management-solutions.png)  
6. Dans le panneau de la solution de gestion, passez en revue les informations la concernant, puis cliquez sur **Créer**.
7. Dans le panneau *Nom de la solution gestion*, sélectionnez un espace de travail à associer à la solution de gestion.
8. Vous pouvez également modifier les paramètres de l’espace de travail concernant l’abonnement Azure, le groupe de ressources et l’emplacement. Vous pouvez également choisir les **options d’automatisation**. Cliquez sur **Créer**.  
    ![espace de travail de la solution](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Pour utiliser la solution de gestion que vous avez ajoutée à votre espace de travail, accédez à **Log Analytics** > **Abonnements** > ***nom de l’espace de travail*** > **Vue d’ensemble**. Une nouvelle vignette pour votre solution de gestion s’affiche. Cliquez sur la vignette pour l’ouvrir et démarrer la solution une fois les données de la solution recueillies.

## <a name="remove-a-management-solution"></a>Suppression d’une solution de gestion

1. Dans le [portail Azure](https://portal.azure.com), accédez à **Log Analytics** > **Abonnements** > ***nom de l’espace de travail*** puis, dans le panneau ***nom de l’espace de travail***, cliquez sur **Solutions**.
2. Dans la liste des solutions de gestion, sélectionnez la solution à supprimer.
3. Dans le panneau de la solution de votre espace de travail, cliquez sur **Supprimer**.  
    ![supprimer la solution](./media/log-analytics-add-solutions/solution-delete.png)  
4. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

## <a name="offers-and-pricing-tiers"></a>Offres et niveaux tarifaires

Le tableau suivant indique les solutions de gestion appartenant à chaque offre Operations Management Suite.
Il indique également les niveaux tarifaires disponibles pour chaque solution de gestion.
Toutes les solutions indiquées dans le tableau suivant sont disponibles dans le portail Azure et la galerie de solutions du portail Log Analytics.

| Solution de gestion                                                                       | Offre                                                                     | Niveaux tarifaires<sup>1</sup>                                                 | Notes |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Activity Log Analytics](log-analytics-activity.md)                                                                   | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | 90 jours de données disponibles gratuitement<br>Données non soumises à la limite du niveau gratuit |
| [AD Assessment](log-analytics-ad-assessment.md)                                           | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [AD Replication Status](log-analytics-ad-replication-status.md)                           | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [Agent Health](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Données non soumises à la limite du niveau gratuit<br> Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [Alert Management](log-analytics-solution-alert-management.md)                            | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [Application Insights Connector (préversion)](log-analytics-app-insights-connector.md)                                               | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automation & Control</li></ul>                                  | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [Azure Application Gateway Analytics](log-analytics-azure-networking-analytics.md)    | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Azure Network Security Group Analytics](log-analytics-azure-networking-analytics.md)     | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Azure SQL Analytics (préversion)](log-analytics-azure-sql.md)                                                       | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br>Par&nbsp;nœud&nbsp;(OMS)                                                                          | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation|
| [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md)     | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
|[Sauvegarde](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Insight & Analytics</li></ul>                                   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)                                                                       | Nécessite un archivage de sauvegarde classique.<br> Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [Capacity and Performance (préversion)](log-analytics-capacity.md)                                                   | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Suivi des modifications](log-analytics-change-tracking.md)                                       | <ul><li>Automation & Control</li></ul>                                  | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [Conteneurs](log-analytics-containers.md)                                                 | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Connecteur de gestion des services informatiques](log-analytics-itsmc-overview.md)                                                | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)     | |
| HDInsight HBase Monitoring <br>(Préversion)                                                  | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Key Vault Analytics](log-analytics-azure-key-vault.md)                   | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Logic Apps B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [Malware Assessment](log-analytics-malware.md)                                            | <ul><li>Security and Compliance</li></ul>                                 | Gratuit<br> Standalone<br>Par&nbsp;nœud&nbsp;(OMS)                                                                           | Si vous ajoutez les solutions de sécurité et de conformité après le 19 juin 2017, la [facturation s’effectue pour chaque nœud](https://azure.microsoft.com/pricing/details/security-compliance/), quel que soit le niveau tarifaire de l’espace de travail. Les 60 premiers jours sont gratuits.  |
| [Network Performance Monitor](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Insight & Analytics</li></ul>                                   | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | |
| [Office 365 Analytics (préversion)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Security and Audit](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Security&nbsp;&&nbsp;Compliance</li></ul>                       | Gratuit<br> Standalone<br>Par&nbsp;nœud&nbsp;(OMS)                                                                           | La collecte des journaux des événements de sécurité requiert cette solution<br>Si vous ajoutez les solutions de sécurité et de conformité après le 19 juin 2017, la [facturation s’effectue pour chaque nœud](https://azure.microsoft.com/pricing/details/security-compliance/), quel que soit le niveau tarifaire de l’espace de travail. Les 60 premiers jours sont gratuits. |
| [Service Fabric Analytics (version préliminaire)](log-analytics-service-fabric.md)                     | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Service Map (préversion)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Insight & Analytics</li></ul>                      | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Disponible dans les régions Est des États-Unis, Europe de l’Ouest et Ouest-Centre des États-Unis    |
| [Site Recovery](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Insight & Analytics</li></ul>                                   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)                                                                       | Nécessite un coffre Site Recovery classique.<br> Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [SQL Assessment](log-analytics-sql-assessment.md)                                         | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| Démarrer/arrêter des machines virtuelles pendant les heures creuses<br>(Préversion)                                              | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [System Center Operations Manager Assessment (préversion)](log-analytics-scom-assessment.md)  | <ul><li>Insight & Analytics</li><li>Log Analytics</li></ul>        | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automation & Control</li></ul>                                  | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |
| [Update Compliance (préversion)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Pas de frais pour les données ou les nœuds<br>Données non soumises à la limite du niveau gratuit.<br> Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | Pas de frais pour les données ou les nœuds<br>Données non soumises à la limite du niveau gratuit.<br> Non disponible pour ajout à partir du portail/de la place de marché Azure. |
| [VMware Monitoring (préversion)](log-analytics-vmware.md)                                | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> standard<br> Premium&nbsp;(OMS)<br> Par&nbsp;Go&nbsp;(autonome)<br> Par&nbsp;nœud&nbsp;(OMS)   | |
| [Wire Data 2.0 (préversion)](log-analytics-wire-data.md)                                                                 | <ul><li>Insight & Analytics</li></ul>                                   | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)                                                                         | Disponible dans les régions Est des États-Unis, Europe de l’Ouest et Ouest-Centre des États-Unis |

<sup>1</sup> Les niveaux tarifaires *Standard* et *Premium (OMS)* sont disponibles uniquement pour les clients qui ont créé leur espace de travail Log Analytics avant le 21 septembre 2016.

### <a name="community-provided-management-solutions"></a>Solutions de gestion fournies par la communauté

Les solutions fournies par la communauté sont disponibles à partir de la [galerie de modèles Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) et directement auprès des auteurs.

| Solution de gestion               | Offre                                                                     | Niveaux de tarification                         | Notes |
| ---                               | ---                                                                       | ---                                   | ---   |
| Toutes les solutions fournies par la communauté  | <ul><li>Insight&nbsp;&&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuit<br> Par&nbsp;nœud&nbsp;(OMS)     |   Nécessite la liaison de votre espace de travail Log Analytics à un compte Automation |




## <a name="data-collection-details"></a>Détails sur la collecte de données
Les tableaux suivants présentent les méthodes de collecte des données et d’autres informations sur le mode de collecte pour les solutions de gestion Log Analytics et les sources de données. Les tableaux sont classés en fonction des offres de solutions qui sont équivalentes aux [niveaux tarifaires des abonnements](https://go.microsoft.com/fwlink/?linkid=827926). La solution Log Analytics des activités est disponible pour tous les niveaux tarifaires gratuits.

L’agent Windows Log Analytics et l’agent System Center Operations Manager sont pratiquement identiques. L’agent Windows inclut des fonctionnalités supplémentaires lui permettant de se connecter à l’espace de travail Log Analytics et d’acheminer via un proxy. Si vous utilisez un agent Operations Manager, celui-ci doit être ciblé en tant qu’agent OMS pour communiquer avec Log Analytics. Les agents Operations Manager dans ce tableau sont des agents OMS connectés à Operations Manager. Pour plus d’informations sur la connexion de votre environnement Operations Manager existant à Log Analytics, consultez [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Le type d’agent que vous utilisez détermine la façon dont les données sont envoyées à Log Analytics, avec les conditions suivantes :
> - Vous utilisez l’agent Windows ou un agent OMS lié à Operations Manager.
> - Lorsque Operations Manager est requis, les données de l’agent Operations Manager pour la solution sont toujours envoyées à Log Analytics à l’aide du groupe d’administration Operations Manager. De plus, lorsque Operations Manager est requis, seul l’agent Operations Manager est utilisé par la solution.
> - Lorsque Operations Manager n’est pas requis et que le tableau indique que les données de l’agent Operations Manager sont envoyées à Log Analytics à l’aide du groupe d’administration, les données de l’agent Operations Manager sont toujours envoyées à Log Analytics à l’aide de groupes d’administration. Les agents Windows contournent le groupe d’administration et envoient leurs données directement à Log Analytics.
> - Lorsque les données de l’agent Operations Manager ne sont pas envoyées à l’aide d’un groupe d’administration, elles sont envoyées directement à Log Analytics, en contournant le groupe d’administration.

### <a name="insight--analytics--log-analytics"></a>Insight & Analytics / Log Analytics

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Activity Log Analytics | Azure |   |   |   |   |   | sur notification |
| AD Assessment |Windows |&#8226; |&#8226; |  |  |&#8226; |7 jours |
| AD Replication Status |Windows |&#8226; |&#8226; |  |  |&#8226; |5 jours |
| Agent Health | Windows et Linux | &#8226; | &#8226; |   |   | &#8226; | 1 minute |
| Alert Management (Nagios) |Linux |&#8226; |  |  |  |  |à l'arrivée |
| Alert Management (Zabbix) |Linux |&#8226; |  |  |  |  |1 minute |
| Alert Management (Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3 minutes |
| Application Insights Connector (préversion) | Azure |   |   |   |   |   | sur notification |
| Azure Application Gateway Analytics | Azure |   |   |   |   |   | sur notification |
| Azure Network Security Group Analytics | Azure |   |   |   |   |   | sur notification |
| Azure SQL Analytics (préversion) |Windows |  |  |  |  |  | 10 minutes |
| Gestion de la capacité |Windows |&#8226; |&#8226; |  |  |&#8226; |à l'arrivée |
| Conteneurs | Windows et Linux | &#8226; | &#8226; |   |   |   | 3 minutes |
| Key Vault Analytics |Windows |  |  |  |  |  |sur notification |
| Network Performance Monitor | Windows | &#8226; | &#8226; |   |   |   | Établissements de liaisons TCP toutes les 5 secondes, données envoyées toutes les 3 minutes |
| Office 365 Analytics (préversion) |Windows |  |  |  |  |  |sur notification |
| Service Fabric Analytics |Windows |  |  |&#8226; |  |  |5 minutes |
| Service Map | Windows et Linux | &#8226; | &#8226; |   |   |   | 15 secondes |
| SQL Assessment |Windows |&#8226; |&#8226; |  |  |&#8226; |7 jours |
| SurfaceHub |Windows |&#8226; |  |  |  |  |à l'arrivée |
| System Center Operations Manager Assessment (préversion) | Windows | &#8226; | &#8226; |   |   | &#8226; | sept jours |
| Upgrade Analytics (préversion) | Windows | &#8226; |   |   |   |   | 2 jours |
| VMware Monitoring (préversion) | Linux | &#8226; |   |   |   |   | 3 minutes |
| Wire Data |Windows (2012 R2 / 8.1 ou ultérieur) |&#8226; |&#8226; |  |  |  | 1 minute |


### <a name="automation--control"></a>Automation & Control

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automation Hybrid Worker | Windows | &#8226; | &#8226; |   |   |   | n/a |
| Suivi des modifications |Windows |&#8226; |&#8226; |  |  |&#8226; |Toutes les heures |
| Suivi des modifications |Linux |&#8226; |  |  |  |  |Toutes les heures |
| Update Management | Windows |&#8226; |&#8226; |  |  |&#8226; |au moins 2 fois par jour et 15 minutes après l'installation d'une mise à jour |

### <a name="security--compliance"></a>Security & Compliance

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Antimalware Assessment |Windows |&#8226; |&#8226; |  |  |&#8226; |Toutes les heures |
| Security and Audit<sup>1</sup> | Windows et Linux | partielle | partielle | partielle |   | partielle | divers |

<sup>1</sup>La solution Security and Audit peut collecter les journaux des agents Windows, Operations Manager et Linux. Consultez [Sources de données](#data-sources) pour obtenir des informations sur la collecte de données concernant :

- syslog
- Journaux des événements de sécurité Windows
- Journaux du pare-feu Windows
- Journaux des événements Windows



### <a name="protection--recovery"></a>Protection et récupération

| Solution de gestion | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Sauvegarde | Azure |   |   |   |   |   | n/a |
| Azure Site Recovery | Azure |   |   |   |   |   | n/a |


### <a name="data-sources"></a>Sources de données


| Source de données | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Journaux d’activité Azure |Windows |  |  |  |  |  |sur notification |
| Journaux de diagnostic Azure |Windows |  |  |  |  |  |sur notification |
| Métriques de diagnostic Azure |Windows |  |  |  |  |  |sur notification |
| ETW |Windows |  |  |&#8226; |  |  |5 minutes |
| Journaux IIS |Windows |&#8226; |&#8226; |&#8226; |  |  |5 minutes |
| Compteurs de performance |Windows |&#8226; |&#8226; |  |  |  |comme prévu, minimum de 10 secondes |
| Compteurs de performance |Linux |&#8226; |  |  |  |  |comme prévu, minimum de 10 secondes |
| syslog |Linux |&#8226; |  |  |  |  |depuis le stockage Azure : 10 minutes ; à partir de l’agent : à l’arrivée |
| Journaux des événements de sécurité Windows |Windows |&#8226; |&#8226; |&#8226; |  |  |pour le stockage Azure : 10 minutes ; pour l'agent : à l'arrivée |
| Journaux du pare-feu Windows |Windows |&#8226; |&#8226; |  |  |  |à l'arrivée |
| Journaux des événements Windows |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |pour le stockage Azure : 10 minutes ; pour l'agent : à l'arrivée |



## <a name="preview-management-solutions-and-features"></a>Solutions de gestion et fonctionnalités en préversion
En exécutant un service et en suivant des pratiques de développement, nous pouvons collaborer avec des clients pour développer des solutions et des fonctionnalités.

Pendant la préversion privée, nous accordons à un petit groupe de clients un accès à une implémentation anticipée de la fonctionnalité ou de la solution pour obtenir des commentaires et apporter des améliorations. Cette implémentation anticipée a des fonctionnalités minimales.

Notre objectif est d’effectuer des expérimentations rapides pour trouver ce qui fonctionne et ce qui ne fonctionne pas. Nous ne quittons ce processus que quand, au vu des commentaires des clients utilisant la préversion privée, nous savons que nous pouvons passer à une préversion publique.

Pendant la préversion publique, nous mettons la fonctionnalité ou la solution à la disposition de tous les utilisateurs pour obtenir d’autres commentaires et valider notre avancée et notre efficacité. Pendant cette phase :

* Les fonctionnalités en préversion apparaissent sous l’onglet Paramètres et peuvent être activées par tout utilisateur.
* Les solutions en préversion sont ajoutées par le biais de la galerie ou à l’aide d’un script.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Que dois-je savoir sur les fonctionnalités et solutions en préversion ?
Nous sommes motivés par les nouvelles fonctionnalités et solutions de gestion, et à l’idée de travailler avec vous pour les développer.

Les solutions et fonctionnalités en préversion ne sont pas adaptées à chacun. Avant de demander à participer à une préversion privée ou d’activer une préversion publique, soyez certain d’être prêt à utiliser quelque chose qui est en cours de développement.

Quand vous activez une fonctionnalité en préversion par le biais du portail, un avertissement apparaît, vous rappelant que la fonctionnalité est disponible en préversion.

#### <a name="for-both-private-and-public-preview"></a>Pour les préversions *privées* et *publiques*
Les informations suivantes s’appliquent aux préversions publiques et privées :

* Les choses ne marchent pas toujours correctement.
  * Les problèmes vont d’une gêne mineure à quelque chose qui ne fonctionne pas du tout.
* La préversion peut avoir un impact négatif sur vos systèmes et votre environnement.
  * Malgré tous nos efforts, des événements inattendus peuvent parfois affecter les systèmes que vous utilisez.
* Une perte/altération des données peut se produire.
* Nous pouvons vous demander de collecter des journaux de diagnostic ou d’autres données pour nous aider à résoudre les problèmes.
* La fonctionnalité ou la solution peut être supprimée (temporairement ou définitivement).
  * Les connaissances acquises pendant la préversion peuvent nous amener à ne pas publier la fonctionnalité ou la solution.
* Les préversions peuvent ne pas fonctionner ou ne pas avoir été testées avec toutes les configurations, donc nous pouvons limiter :
  * Les systèmes d’exploitation utilisables (par exemple, une fonctionnalité peut s’appliquer uniquement à Linux en préversion).
  * Le type d’agent (MMA, Operations Manager) utilisable (par exemple, une fonctionnalité peut ne pas fonctionner avec Operations Manager en préversion).  
* Les solutions et fonctionnalités en préversion ne sont pas couvertes par le Contrat de niveau de service.
* L’utilisation des fonctionnalités en préversion occasionne des frais d’utilisation.
* Des fonctionnalités ou fonctions nécessaires pour la fonctionnalité ou la solution peuvent faire défaut ou être incomplètes.
* Les fonctionnalités et solutions peuvent ne pas être disponibles dans toutes les régions.
* Les fonctionnalités et solutions peuvent ne pas être localisées.
* Les fonctionnalités et solutions peuvent n’être utilisables que par un nombre limité de clients ou d’appareils.
* Vous pouvez être amené à utiliser des scripts pour effectuer une configuration et pour activer la solution ou la fonctionnalité.
* L’interface utilisateur (IU) est inachevée et peut changer d’un jour à l’autre.
* Les préversions publiques peuvent ne pas être appropriées pour vos systèmes de production ou critiques.

#### <a name="for-private-preview"></a>Pour la préversion *privée*
Outre les points ci-dessus, les informations suivantes sont propres aux préversions privées :

* Vous êtes supposé nous faire part de vos commentaires sur votre expérience pour que nous puissions améliorer la fonctionnalité ou la solution.
* Nous pouvons vous contacter pour recueillir vos commentaires au moyen d’enquêtes, par téléphone ou par e-mail.
* Les choses ne fonctionnent pas toujours correctement.
* Nous pouvons imposer un accord de confidentialité pour la participation ou inclure du contenu confidentiel.
  * Avant de vous exprimer dans un billet de blog ou un tweet, ou de communiquer par un autre moyen avec des tiers, contactez le directeur de programmes responsable de la préversion pour connaître les restrictions qui s’appliquent à la divulgation d’informations.
* N’exécutez pas la fonctionnalité ou la solution sur des systèmes de production ou critiques.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Comment puis-je accéder aux fonctionnalités et solutions privées en préversion ?
La nature de la préversion privée détermine la forme de l’invitation.

* En répondant à l’enquête client mensuelle et en nous autorisant à vous suivre, vous augmentez vos chances d’être invité à une préversion privée.
* Votre équipe des comptes Microsoft peut vous désigner.
* Vous pouvez vous inscrire en fonction des détails publiés sur twitter [msopsmgmt](https://twitter.com/msopsmgmt).
* Vous pouvez vous inscrire en fonction des détails partagés à l’occasion d’événements communautaires : recherchez-nous dans les rencontres, conférences et communautés en ligne.

## <a name="next-steps"></a>Étapes suivantes
* [Recherchez dans les journaux](log-analytics-log-searches.md) les informations détaillées collectées par les solutions de gestion.
