---
title: Journalisation et audit Azure | Microsoft Docs
description: Découvrez comment utiliser les données de journalisation pour obtenir des informations détaillées sur votre application.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 032aa4a6cedd49ff9c3b4803561b8b187e8f9af5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-logging-and-auditing"></a>Journalisation et audit Azure
## <a name="introduction"></a>Introduction
### <a name="overview"></a>Vue d'ensemble
Pour aider les clients Azure actuels et à venir à comprendre et à utiliser les différentes fonctionnalités de sécurité disponibles dans la plateforme Azure et ses composants connexes, Microsoft a développé une série de livres blancs, de présentations de sécurité, de meilleures pratiques et de listes de contrôle. Les rubriques sont aussi précises que variées et sont mises à jour régulièrement. Le présent document fait partie de cette série, comme décrit dans la section Résumé ci-après.
### <a name="azure-platform"></a>Plateforme Azure
Azure est une plateforme de services cloud ouverte et flexible, qui prend en charge un large éventail de systèmes d’exploitation, de langages de programmation, d’infrastructures, d’outils, de bases de données et d’appareils.

Vous pouvez par exemple :
-   Exécuter des conteneurs Linux avec l’intégration Docker.

-   Créer des applications avec JavaScript, Python, .NET, PHP, Java et Node.js

-   Créer des serveurs principaux pour les appareils iOS, Android et Windows.

Les services de cloud public Azure prennent en charge les technologies auxquelles des millions de développeurs et de professionnels de l’informatique font déjà confiance.

Lorsque vous générez ou faites migrer des ressources informatiques vers un fournisseur de cloud, vous comptez sur les capacités de cette organisation à protéger vos applications et données avec les services et les contrôles qu’elle vous fournit pour gérer la sécurité de vos ressources cloud.

L’infrastructure d’Azure est conçue de l’installation vers les applications pour héberger des millions de clients simultanément, et constitue une base fiable permettant de répondre aux besoins des entreprises en matière de sécurité. En outre, Azure vous offre un large éventail d’options de sécurité configurables, ainsi que la possibilité de contrôler ces options pour vous permettre de personnaliser la sécurité et de répondre ainsi aux exigences uniques de vos déploiements. Ce document vous aidera à répondre à ces exigences.

### <a name="abstract"></a>Résumé
L’audit et la journalisation des événements liés à la sécurité, de même que les alertes associées, constituent des composants importants dans une stratégie de protection des données efficace. Les journaux et les rapports de sécurité vous fournissent un enregistrement électronique des activités suspectes et vous aident à détecter les motifs pouvant indiquer une tentative d’intrusion ou une intrusion externe effective sur le réseau, ainsi que des attaques internes. Vous pouvez avoir recours aux audits pour surveiller les activités des utilisateurs, documenter la conformité aux exigences réglementaires, effectuer des analyses d’investigation, etc. Les alertes envoient une notification immédiate lorsque des événements de sécurité se produisent.

Les services et produits Microsoft Azure vous proposent des options d’audit et de journalisation configurables qui permettent d’identifier les lacunes dans vos stratégies et mécanismes de sécurité et d’y remédier pour empêcher les violations éventuelles. Les services Microsoft offrent certaines (voire toutes) les options suivantes : systèmes de surveillance, de journalisation et d’analyse centralisés pour une visibilité continue ; alertes en temps voulu ; et rapports vous permettant de gérer la grande quantité d’informations générées par les appareils et les services.

Les données de journal de Microsoft Azure peuvent être exportées pour analyse vers des systèmes SIEM (Security Incident and Event Management) et elles s’intègrent avec des solutions d’audit tierces.

Ce livre blanc présente la génération, la collecte et l’analyse des journaux de sécurité provenant de services hébergés dans Azure. Il vous permettra également d’obtenir des informations sur la sécurité de vos déploiements Azure. Ce livre blanc ne concerne que les applications et services créés et déployés dans Azure.

> [!Note]
> Certaines recommandations contenues dans ce document peuvent augmenter l’utilisation des données, des réseaux ou des ressources de calcul et entraîner des coûts de licence ou d’abonnement supplémentaires.

## <a name="types-of-logs-in-azure"></a>Types de journaux dans Azure
Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. La journalisation fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus. En outre, vous pouvez utiliser les données de journalisation pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

Azure génère une journalisation complète pour chaque service Azure. Ces journaux sont classés selon ces deux types principaux :
-   Les **journaux de contrôle/gestion** vous offrent une visibilité sur les opérations CREATE, UPDATE et DELETE d’Azure Resource Manager. Les [journaux d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) sont un exemple de ce type de journal.

-   Les **journaux des plans de données** vous offrent une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource Azure. Il s’agit par exemple des journaux du système d’événements, de la sécurité et des applications Windows dans une machine virtuelle, ou des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) configurés via Azure Monitor.


-   Les **événements traités** fournissent des informations sur les événements/alertes analysés en votre nom. Les alertes [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) en sont un exemple, où [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) a traité et analysé votre abonnement et fournit des alertes de sécurité très concises.

Le tableau suivant répertorie les principaux types de journaux disponibles dans Azure.

| Catégorie du journal | Type de journal | Utilisations | Intégration |
| ------------ | -------- | ------ | ----------- |
|[Journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Événements de plan de contrôle sur les ressources d’Azure Resource Manager|   Fournissent des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement.| API Rest et [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Données fréquentes sur les opérations des ressources d’Azure Resource Manager de l’abonnement| Fournissent des informations sur les opérations effectuées par votre ressource| Azure Monitor, [diffusion](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Création de rapports AAD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Journaux et rapports|Activités d’authentification des utilisateurs et informations sur l’activité système en matière de gestion des utilisateurs et des groupes|[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Machines virtuelles et services cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Journal des événements Windows et Syslog Linux|    Capture les données système et les données de journalisation sur les machines virtuelles, puis les transfère vers un compte de stockage de votre choix.|   Windows avec stockage [WAD](https://docs.microsoft.com/azure/azure-diagnostics) (Windows Azure Diagnostics) et Linux dans Azure Monitor|
|[Analyse du stockage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Journalise le stockage et fournit des données de métriques pour un compte de stockage|Fournit des informations sur les demandes, analyse les tendances d’utilisation et diagnostique les problèmes liés à votre compte de stockage.|    API REST ou [bibliothèque cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Journalisation des flux de groupe de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Format JSON, affiche les flux entrants et sortants, par règle|Afficher des informations sur le trafic IP entrant et sortant via un groupe de sécurité réseau|[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Journaux, exceptions et diagnostics personnalisés|    Service de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Traitement des données/alertes de sécurité| Alerte Azure Security Center, alerte OMS| Informations relatives à la sécurité et alertes.|   API REST, JSON|

### <a name="activity-log"></a>Journal d’activité
Le [journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Le journal d’activité était précédemment appelé « journal d’audit » ou « journal des opérations », car il indique les [événements de plan de contrôle](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) de vos abonnements. Avec le journal d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur des ressources de votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas les opérations (GET) de lecture.

Les éléments PUT, POST, DELETE font ici référence à l’ensemble des opérations d’écriture relatives aux ressources contenues dans le journal d’activité. Par exemple, vous pouvez utiliser les journaux d’activité pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation modifie une ressource.

![Journal d’activité](./media/azure-log-audit/azure-log-audit-fig1.png)


Vous pouvez extraire des événements de votre journal d’activité à l’aide du portail Azure, de l’[interface de ligne de commande](https://docs.microsoft.com/azure/storage/storage-azure-cli), des applets de commande PowerShell et de l’[API REST Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). La période de rétention des données des journaux d’activité est de 19 jours.

Scénarios d’intégration
-   [Créez une alerte via e-mail ou webhook qui déclenche un événement de journal d’activité.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Diffusez-le en continu vers un Event Hub](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) pour qu’un service tiers ou une solution d’analyse personnalisée (p. ex. PowerBI) l’ingère.

-   Analysez-le dans PowerBI à l’aide du [pack de contenu PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

-   [Enregistrez-le dans un compte de stockage pour l’archivage ou l’inspection manuelle.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) Vous pouvez spécifier la durée de rétention (en jours) à l’aide des profils de journal.

-   Interrogez-le et affichez-le dans le portail Azure.

-   Interrogez-le via l’applet de commande PowerShell, l’interface de ligne de commande ou l’API REST.

-   Exportez le journal d’activité avec les profils de journal dans [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Vous pouvez utiliser un compte de stockage ou un [espace de noms Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) qui n’est pas dans le même abonnement que celui générant le journal. L’utilisateur qui configure le paramètre doit disposer d’un accès [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) approprié aux deux abonnements
### <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure
Les journaux de diagnostic Azure sont générés par une ressource et fournissent des informations riches et fréquentes sur le fonctionnement de cette ressource. Le contenu de ces journaux varie selon le type de ressource (p. ex. les [journaux d’événements Windows du système](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) sont une catégorie de journaux de diagnostic pour les machines virtuelles et les [objets blob ; les journaux de files d’attente et de tables](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) sont des catégories de journaux de diagnostic pour les comptes de stockage) et diffère du journal d’activité, qui fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement.

![Journaux de diagnostic Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Les journaux de diagnostic Azure offrent plusieurs options de configuration (portail Azure) via PowerShell, l’interface de ligne de commande (CLI) et l’API REST.

**Scénarios d’intégration**
-   Enregistrez-les dans un [compte de stockage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des paramètres de diagnostic.

-   [Diffusez-les en continu sur Event Hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) pour qu’un service tiers ou une solution d’analyse personnalisée (comme [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)) les ingère.

-   Analysez-les avec [OMS Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Services pris en charge, schéma pour les journaux de diagnostic et catégories de journaux prises en charge par type de ressource**


| de diffusion en continu | Schéma et documentation | Type de ressource | Catégorie |
| ------- | ------------- | ------------- | -------- |
|Équilibreur de charge| [Analyse des journaux de l’équilibreur de charge Azure (version préliminaire)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Network Security Group|[Analytique des journaux pour les groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Passerelles d’application|[Journalisation des diagnostics pour Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Journalisation d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Recherche Azure|[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Accès aux journaux de diagnostic d’Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Audit|
|Data Lake Analytics|[Accès aux journaux de diagnostic d’Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Audit|
|||Microsoft.DataLakeAnalytics/accounts|Requests|
|||Microsoft.DataLakeStore/accounts|Requests|
|Logic Apps|[Schéma de suivi personnalisé Logic Apps B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Journalisation des diagnostics Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Log Analytics pour Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Journaux de diagnostic d’Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Journaux de diagnostic des travaux](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Exécution|
|||Microsoft.StreamAnalytics/streamingjobs|Création|
|Service Bus|[Journaux de diagnostic Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Création de rapports Active Directory
Azure Active Directory (Azure AD) comprend des rapports sur la sécurité, les activités et l’audit concernant votre annuaire. Le [rapport d’audit d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) permet aux clients d’identifier les actions privilégiées qui se sont produites dans leur Azure Active Directory. Ces actions privilégiées incluent les modifications d'élévation (par exemple la création de rôle ou les réinitialisations de mot de passe), la modification des configurations de stratégie (par exemple les stratégies de mot de passe) ou bien les modifications apportées à la configuration de répertoire (par exemple les modifications apportées aux paramètres de fédération de domaine).

Les rapports fournissent l’enregistrement d’audit pour le nom d’événement, l’acteur qui a effectué l’action, la ressource cible affectée par la modification, ainsi que la date et l’heure (UTC). Les clients sont en mesure de récupérer la liste des événements d’audit pour leur annuaire Azure Active Directory par le biais du [portail Azure](https://portal.azure.com/), comme décrit à la page [Afficher vos journaux d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Voici une liste des rapports fournis :

| Rapports de sécurité | Rapports d’activité | Rapport d’audit |
| :--------------- | :--------------- | :------------ |
|Connexions à partir de sources inconnues| Utilisation des applications : résumé| Rapport d’audit d’annuaire|
|Connexions après plusieurs échecs|  Utilisation des applications : présentation détaillée||
|Connexions depuis plusieurs zones géographiques|    Tableau de bord de l’application||
|Connexions depuis des adresses IP avec des activités suspectes|   Erreurs de configuration de compte||
|Activité de connexion anormale|    Appareils des utilisateur individuels||
|Connexions à partir d’appareils potentiellement infectés|   Activité des utilisateurs individuels||
|Utilisateurs ayant une activité de connexion anormale| Rapport d'activité de groupes||
||Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe||
||Activité de réinitialisation de mot de passe|||

Les données de ces rapports peuvent être utiles à vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. Les API de création de rapports Azure AD fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) à partir de divers outils et langages de programmation.

Les événements du rapport d’audit d’Azure AD sont conservés pendant 180 jours.

> [!Note]
> Pour plus d’informations sur la rétention des rapports, consultez la page [Stratégies de rétention des rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Pour les clients intéressés par le stockage de leurs événements d’audit pour des périodes de rétention plus longues, l’API de création de rapports peut être utilisée pour extraire régulièrement des [événements d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) dans un magasin de données distinct.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Journaux de machines virtuelles avec Azure Diagnostics
[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) est la fonctionnalité Azure qui active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l’extension de diagnostic à partir de différentes sources. Sont actuellement pris en charge les [rôles de travail et web Azure Cloud Services](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Journaux de machines virtuelles avec Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

[Machines virtuelles Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) fonctionnant sous Microsoft Windows et [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Vous pouvez activer Azure Diagnostics sur une machine virtuelle, comme suit :

-   Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio pour suivre les machines virtuelles Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Configurer les diagnostics Azure sur une machine virtuelle Azure à distance](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Utilisation de PowerShell pour configurer les diagnostics sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[L’analyse du stockage Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) effectue la journalisation et fournit les données d’indicateurs de performance d’un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage La journalisation Storage Analytics est disponible pour les [services BLOB, de File d’attente et de Table](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage.

Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort. Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison BLOB, mais pas dans ses points de terminaison de Table ou de File d’attente, seuls des journaux relatifs au service BLOB sont créés.

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer dans le [portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez l’article [Surveillance d’un compte de stockage dans le portail Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Set Service Properties pour activer Storage Analytics individuellement pour chaque service.

Les données agrégées sont stockées dans un objet blob connu (pour la journalisation) et dans des tables connues (pour les métriques), qui sont accessibles via les API du service BLOB et du service de Table.

Storage Analytics a une limite de 20 To pour la quantité de données stockées qui est indépendante de la limite totale pour votre compte de stockage. Tous les journaux sont stockés dans des objets [blob de blocs](https://docs.microsoft.com/azure/storage/storage-analytics) dans un conteneur nommé $logs, qui est automatiquement créé lorsque Storage Analytics est activé pour un compte de stockage.

> [!Note]
> Pour plus d’informations sur les stratégies de facturation et de rétention de données, consultez l’article [Storage Analytics and Billing (Storage Analytics et facturation)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
>
> [!Note]
> Pour plus d’informations sur les limites des comptes de stockage, consultez la page [Objectifs de performance et évolutivité d’Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Les types de demandes authentifiées et anonymes suivants sont enregistrés.



| Authentifiée  | Anonyme|
| :------------- | :-------------|
| Demandes ayant réussi | Demandes ayant réussi |
|Demandes ayant échoué, y compris les erreurs de délai d’expiration, limitation, réseau, autorisation et autres erreurs | Demandes utilisant une signature d’accès partagé (SAS), y compris les demandes ayant réussi et ayant échoué |
| Demandes utilisant une signature d’accès partagé (SAS), y compris les demandes ayant réussi et ayant échoué |Erreurs de délai d’expiration pour le client et le serveur |
|   Demandes de données d’analyse |    Demandes GET ayant échoué avec le code d’erreur 304 (non modifié) |
| Les demandes effectuées par Storage Analytics lui-même, telles que la création ou la suppression d'un journal, ne sont pas enregistrées. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Aucune autre demande anonyme ayant échoué n'est enregistrée. La liste complète des données enregistrées est disponible dans les rubriques [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Opérations et messages d’état enregistrés Storage Analytics) et [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Format de journal de Storage Analytics). |

### <a name="azure-networking-logs"></a>Journaux réseaux Azure
La journalisation et la surveillance réseau dans Azure sont complètes et couvrent deux grandes catégories :

-   [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) - La surveillance réseau basée sur des scénarios est fournie avec les fonctionnalités de Network Watcher. Ce service inclut la capture de paquets, le tronçon saut suivant, la vérification des flux IP, l’affichage de groupe de sécurité, les journaux de flux de groupe de sécurité réseau. La surveillance basée sur des scénarios fournit une vue de bout en bout des ressources réseau, à l’inverse de la surveillance des ressources réseau qui s’intéresse à chaque ressource spécifique.

-   [Surveillance des ressources](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) - La surveillance au niveau des ressources se compose de quatre fonctionnalités : journaux de diagnostic, mesures, résolution des problèmes et intégrité des ressources. Toutes ces fonctionnalités sont conçues au niveau des ressources réseau.

![Journaux réseaux Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure.

**Journalisation des flux de groupe de sécurité réseau** - Les journaux de flux pour les groupes de sécurité réseau vous permettent de capturer les journaux relatifs au trafic autorisé ou refusé par les règles de sécurité dans le groupe. Ces journaux de flux sont écrits au format JSON et affichent les flux entrants et sortants en fonction de la règle, la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), ainsi que l’autorisation ou le refus du trafic.

### <a name="network-security-group-flow-logging"></a>Journalisation des flux de groupe de sécurité réseau

Les [journaux des flux de groupe de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) désignent une fonctionnalité de Network Watcher qui vous permet de visualiser des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau. Ces journaux de flux sont écrits au format JSON et affichent les flux entrants et sortants en fonction de la règle, la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), ainsi que l’autorisation ou le refus du trafic.

Même si les journaux de flux ciblent les groupes de sécurité réseau, ils ne sont pas affichés de la même façon que les autres journaux. Les journaux de flux sont uniquement stockés au sein d’un compte de stockage.

Les mêmes stratégies de rétention que celles des autres journaux s’appliquent aux journaux de flux. Les journaux ont une stratégie de rétention qui peut être définie dans une plage comprise entre 1 et 365 jours. Si aucune stratégie de rétention n’est définie, les journaux sont conservés indéfiniment.

**Journaux de diagnostic**

Les événements périodiques et spontanés sont créés par les ressources réseau et journalisés dans les comptes de stockage, puis envoyés à un hub d’événements ou à Log Analytics. Ces journaux fournissent des informations sur l’intégrité d’une ressource. Ces journaux peuvent être affichés dans des outils tels que Power BI et Log Analytics. Pour savoir comment afficher les journaux de diagnostic, consultez [Solutions d’analyse réseaux Azure dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Journaux de diagnostic](./media/azure-log-audit/azure-log-audit-fig5.png)

Les journaux de diagnostic sont disponibles pour [l’équilibrage de charge](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), les itinéraires et [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher permet d’afficher les journaux de diagnostic. Cet affichage contient toutes les ressources réseau qui prennent en charge la journalisation des diagnostics. À partir de celui-ci, vous pouvez activer et désactiver les ressources réseau facilement et rapidement.


Outre les capacités de journalisation susmentionnées, Network Watcher propose actuellement les fonctionnalités suivantes :
- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) - Fournit une vue au niveau du réseau montrant les différentes interconnexions et associations entre les ressources réseau dans un groupe de ressources.

- [Capture de paquets variables](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) - Capture des données de paquets dans et en dehors d’une machine virtuelle. Les options de filtrage avancées et les contrôles précis comme la possibilité de définir des limites de taille et de temps apportent de la polyvalence. Les données de paquets peuvent être stockées dans un magasin d’objets blob ou sur un disque local au format .cap.

-   [Vérification des flux IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - Vérifie si un paquet est autorisé ou refusé en fonction des paramètres de paquet des informations à 5 tuples de flux (adresse IP de destination, adresse IP source, port de destination, port source et protocole). Si le paquet est refusé par un groupe de sécurité, la règle et le groupe qui ont refusé le paquet sont renvoyés.

-   [Tronçon suivant](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) - Détermine le tronçon suivant pour les paquets routés dans la structure de réseau Azure, vous permettant de diagnostiquer les itinéraires définis par l’utilisateur mal configurés.

-   [Affichage des groupes de sécurité](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) - Obtient les règles de sécurité appliquées et effectives d’une machine virtuelle.

-   [Résolution des problèmes de connexion et de passerelle de réseau virtuel](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) - Offre la possibilité de résoudre les problèmes associés aux connexions et passerelles de réseau virtuel.

-   [Limites d’abonnement réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) - Vous permet d’afficher l’utilisation des ressources réseau par rapport aux limites.

### <a name="application-insight"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisez-le pour analyser votre application web en direct. Ce service détecte automatiquement les problèmes de performances. Il intègre de puissants outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application.

 Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité.

 Il fonctionne avec des applications sur un large éventail de plates-formes, notamment .NET, Node.js et J2EE, hébergées sur site ou dans le cloud. Il s’intègre à votre processus DevOps et offre des points de connexion à divers outils de développement.

![Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights est destiné à l’équipe de développement et permet de comprendre les performances et l’utilisation de votre application. Il analyse les éléments suivants :

-   **Taux de demandes, temps de réponse et taux d’échec** : identifiez les pages les plus consultées, à quel moment de la journée, et déterminez où se trouvent vos utilisateurs. Identifiez les pages qui offrent les meilleures performances. Si vos temps de réponse et votre taux d’échec augmentent lorsqu’il y a plus de requêtes, vous avez peut-être un problème de ressources.

-   **Taux de dépendance, temps de réponse et taux d’échec** : déterminez si des services externes vous ralentissent.

-   **Exceptions** : analysez les statistiques agrégées, ou choisissez des instances en particulier et explorez l’arborescence des appels de procédure et les requêtes connexes. Les exceptions de serveur et de navigateur sont signalées.

-   **Consultations de pages et performances de chargement** : indiquées par le navigateur de vos utilisateurs.

-   **Appels AJAX** à partir de pages web : taux, temps de réponse et taux d’échec.

-   **Nombre de sessions et d’utilisateurs**.

-   **Compteurs de performances** de vos ordinateurs serveurs Windows ou Linux, par exemple le processeur, la mémoire et l’utilisation du réseau.

-   **Diagnostics d’hébergement** de Docker ou Azure.

-   **Journaux de suivi des diagnostics** de votre application : pour pouvoir mettre en corrélation les événements de suivi avec les demandes.

-   **Mesures et événements personnalisés** que vous écrivez vous-même dans le code client ou serveur, pour effectuer le suivi des événements commerciaux tels que les articles vendus ou les matchs gagnés.

**Liste et description des scénarios d’intégration :**

| Scénarios d’intégration | Description |
| --------------------- | :---------- |
|[Plan de l’application](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Les composants de votre application, avec des alertes et des mesures clés.||
|[Recherche de diagnostic pour les données d’instance](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Cherchez et filtrez les événements, comme les requêtes, les exceptions, les appels de dépendance, les suivis de journaux et les affichages de pages.||
|[Metrics Explorer pour les données agrégées](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Explorez, filtrez et segmentez des données agrégées, comme les taux de demandes, d’échecs et d’exceptions, les temps de réponse et les durées de chargement des pages.||
|[Tableaux de bord](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Combinez des données de plusieurs sources et partagez-les avec d’autres. Idéal pour les applications à composants multiples et pour un affichage en continu dans la salle de l’équipe.||
|[Flux de métriques temps réel](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Lorsque vous déployez une nouvelle version, observez ces indicateurs de performance quasiment en temps réel pour vous assurer que tout fonctionne comme prévu.||
|[Analyse](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Répondez à des questions difficiles sur les performances et l’utilisation de votre application avec ce langage de requêtes puissant.||
|[Alertes automatiques et manuelles](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Les alertes automatiques s’adaptent aux habitudes télémétriques normales de votre application et se déclenchent lorsqu’un comportement inhabituel est détecté. Vous pouvez également définir des alertes sur des niveaux particuliers de mesures personnalisées ou standard.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Consultez les données de performances dans le code. Accédez au code à partir de l’arborescence des appels de procédure.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Intégrez des mesures d’utilisation à d’autres données décisionnelles.||
|[API REST](https://dev.applicationinsights.io/)|Écrivez du code pour exécuter des requêtes sur vos propres données brutes et mesures.||
|[Exportation continue](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Exportation en bloc de données brutes vers le système de stockage dès leur arrivée.||

### <a name="azure-security-center-alerts"></a>Alertes Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.

La détection des menaces d’Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires connectées. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces. Les alertes de sécurité, ainsi que les recommandations sur la façon de répondre à la menace, sont hiérarchisées dans Azure Security Center.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Azure Security Center emploie des analyses de sécurité avancées allant bien au-delà des approches simplement basées sur la signature. Les innovations en matière de Big Data et de technologies [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) sont appliquées pour évaluer des événements dans toute la structure du cloud, et permettent ainsi de détecter des menaces qui seraient impossibles à identifier à l’aide de méthodes manuelles et de prédire l’évolution des attaques. Ces analyses de sécurité comprennent les éléments suivants :

-   **Informations sur les menaces intégrées** : recherche les éléments malveillants en exploitant des informations globales concernant les menaces provenant de produits et services Microsoft, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) et de sources externes.

-   **Analyse comportementale** : applique des modèles connus pour détecter les comportements malveillants.

-   **Détection des anomalies** : utilise le profilage statistique pour créer une ligne de base historique. Il avertit sur les écarts par rapport aux lignes de base établies qui se conforment à un vecteur d’attaque potentielle.


De nombreuses équipes de sécurité et d’intervention utilisent une solution SIEM (Security Information and Event Management) comme point de départ pour le triage et l’examen des alertes de sécurité. Grâce à l’intégration des journaux Azure, les clients peuvent synchroniser quasiment en temps réel les alertes de l’Azure Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par les journaux de diagnostics et d’audit Azure, avec leur solution SIEM ou Log Analytics.


## <a name="log-analytics"></a>Log Analytics

Log Analytics est un service d’[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) qui vous permet de collecter et d’analyser les données générées par les ressources de votre cloud et de vos environnements locaux. Il vous fournit des informations en temps réel à l’aide d’une recherche intégrée et de tableaux de bord personnalisés permettant d’analyser facilement des millions d’enregistrements dans l’ensemble de vos charges de travail et serveurs, quel que soit leur emplacement physique.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Au cœur de Log Analytics se trouve le référentiel OMS, qui est hébergé dans le cloud Azure. Les données sont collectées dans le référentiel à partir de sources connectées en configurant des sources de données et en ajoutant des solutions à votre abonnement. Les sources de données et les solutions créeront chacune différents types d'enregistrements avec leur propre jeu de propriétés, mais elles peuvent toujours être analysées ensemble dans des requêtes vers le référentiel. Vous pouvez ainsi utiliser les mêmes outils et méthodes pour travailler avec différents types de données collectées par différentes sources.

Les sources connectées représentent les ordinateurs et autres ressources qui génèrent des données collectées par Log Analytics. Cela peut inclure des agents installés sur des ordinateurs [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) et [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) directement connectés, ou des agents d’un [groupe d’administration System Center Operations Manager connecté](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics peut également collecter des données à partir d’un [stockage Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[sources de données](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) représentent les différents types de données collectées à partir de chaque source connectée. Cela inclut les événements et les [données de performances](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) d’agents [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) et Linux en plus des sources telles que les [journaux IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) et les [journaux de texte personnalisés](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Vous configurez chaque source de données que vous souhaitez collecter, et la configuration est automatiquement remise à chaque source connectée.

Il existe quatre façons différentes de [collecter des journaux et des métriques pour les services Azure :](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Diagnostics Azure directement dans Log Analytics (Diagnostics dans le tableau suivant)

2.  Diagnostics Azure vers stockage Azure, puis vers Log Analytics (Stockage dans le tableau suivant)

3.  Connecteurs pour les services Azure (Connecteurs dans le tableau suivant)

4.  Des scripts pour collecter puis publier les données dans Log Analytics (vide dans le tableau suivant et pour les services qui ne sont pas répertoriés)

| de diffusion en continu | Type de ressource | Journaux | Mesures | Solution |
| :------ | :------------ | :--- | :------ | :------- |
|Passerelles d’application|  Microsoft.Network/<br>applicationGateways|  Diagnostics|Diagnostics|    [Azure Application](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)[Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Connecteur|  Connecteur|  [Connecteur](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)[Application Insights (version préliminaire)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Comptes Automation|   Microsoft.Automation/<br>AutomationAccounts|    Diagnostics||       [Plus d’informations](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Comptes Batch|    Microsoft.Batch/<br>batchAccounts|  Diagnostics|    Diagnostics||
|Services cloud classiques||       Stockage||       [Plus d’informations](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostics|||
|Data Lake analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostics|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>accounts|   Diagnostics|||
|Espace de noms Event Hub|   Microsoft.EventHub/<br>namespaces|  Diagnostics|    Diagnostics||
|IoT Hubs|  Microsoft.Devices/<br>IoTHubs||     Diagnostics||
|Key Vault| Microsoft.KeyVault/<br>vaults|  Diagnostics  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Équilibreurs de charge|    Microsoft.Network/<br>loadBalancers|    Diagnostics|||
|Logic Apps|    Microsoft.Logic/<br>workflows|  Diagnostics|    Diagnostics||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Group|   Microsoft.Network/<br>networksecuritygroups|Diagnostics||   [Azure Network Security Group Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Coffres de récupération|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services Analytics (version préliminaire)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Services de recherche|   Microsoft.Search/<br>searchServices|    Diagnostics|    Diagnostics||
|Espace de noms Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnostics|Diagnostics|    [Service Bus Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Stockage||    [Service Fabric Analytics (version préliminaire)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>bases de données||       Diagnostics||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Stockage|||         Script| [Azure Storage Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtual Machines|  Microsoft.Compute/<br>virtualMachines|  Extension|  Extension||
||||Diagnostics||
|Groupes de machines virtuelles identiques|   Microsoft.Compute/<br>virtualMachines    ||Diagnostics||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Batteries de serveurs web|Microsoft.Web/<br>serverfarms||   Diagnostics
|Sites web| Microsoft.Web/<br>sites ||      Diagnostics|    [Plus d’informations](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Intégration des journaux avec les systèmes SIEM locaux
[L’intégration des journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324) permet d’intégrer des journaux bruts de vos ressources Azure dans vos **systèmes SIEM (Security Information and Event Management) locaux**.

![Intégration des journaux](./media/azure-log-audit/azure-log-audit-fig9.png)

L’intégration des journaux Azure collecte des diagnostics Azure à partir de vos machines virtuelles Windows (WAD), de journaux d’activité Azure, d’alertes Azure Security Center et de journaux du fournisseur de ressources Azure. Cette intégration offre un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.



L’intégration des journaux Azure prend actuellement en charge l’intégration des journaux d’activité Azure, du journal des événements Windows à partir des machines virtuelles Windows de votre abonnement Azure, des alertes Azure Security Center, des journaux de diagnostic Azure et des journaux d’audit Azure Active Directory.

| Type de journal | Log Analytics prenant en charge JSON (Splunk, ArcSight, Qradar) |
| :------- | :-------------------------------------------------------- |
|Journaux d’audit AAD|    Oui|
|Journaux d’activité| OUI|
|Alertes ASC |OUI|
|Journaux de diagnostic (journaux des ressources)|  OUI|
|Journaux des machines virtuelles|   Oui, via les événements transmis et non via JSON|


Le tableau suivant décrit la catégorie des journaux et les détails de l’intégration SIEM.

[Prise en main de l’intégration des journaux Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) : ce didacticiel vous guide dans la procédure d’installation de l’intégration des journaux Azure et d’intégration des journaux du stockage Azure WAD, des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure Active Directory.

Scénarios d’intégration

-   [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar.

-   [FAQ de l’intégration des journaux Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.

-   [Intégration des alertes du Security Center avec les journaux Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) : ce document montre comment synchroniser les alertes du Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par Azure Diagnostics et les journaux d’audit Azure dans leur solution SIEM ou Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

- [Audit et journalisation](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Protéger les données en conservant le niveau de visibilité et en répondant rapidement aux alertes de sécurité reçues

- [Collecte des journaux de sécurité et d’audit dans Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Les paramètres que vous devez appliquer pour vous assurer que vos instances Azure collectent les journaux de sécurité et d’audit appropriés.

- [Configurer les paramètres d’audit pour la collection de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Les administrateurs de collection de sites peuvent récupérer l’historique des actions d’un utilisateur spécifique, ainsi que l’historique des actions effectuées pendant une certaine plage de dates. 

- [Effectuer des recherches dans le journal d’audit dans le Centre de conformité et sécurité Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Vous pouvez utiliser le Centre de conformité et sécurité Office 365 pour effectuer des recherches dans le journal d’audit unifié et afficher les activités utilisateur et administrateur de votre organisation Office 365.


