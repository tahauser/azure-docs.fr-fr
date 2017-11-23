---
title: Azure Operational Security | Microsoft Docs
description: "Découvrez Microsoft Operations Management Suite (OMS), ses services et son fonctionnement."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 8528eeb4eac8397beaa3d0dca37ce9eb33167b32
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-operational-security"></a>Azure Operational Security
## <a name="introduction"></a>Introduction

### <a name="overview"></a>Vue d'ensemble
Nous savons que la sécurité est la priorité dans le cloud et combien il est important que vous trouviez des informations précises et à jour sur la sécurité Azure. L’une des meilleures raisons d’utiliser Azure pour vos applications et services consiste à tirer parti de la large gamme de fonctionnalités et d’outils de sécurité disponibles. Ces outils et fonctionnalités permettent de créer des solutions sécurisées sur la plateforme Azure sécurisée. Windows Azure doit assurer la confidentialité, l’intégrité et la disponibilité des données client, tout en permettant la gestion transparente des responsabilités.

Le présent livre blanc « Azure Operational Security » propose une vue complète des fonctions de sécurité opérationnelle de Windows Azure pour aider les clients à mieux comprendre les nombreux contrôles de sécurité implémentés dans Microsoft Azure, en prenant en compte les perspectives opérationnelles de Microsoft et celles du client.

### <a name="azure-platform"></a>Plateforme Azure
Azure est une plateforme de services de cloud public qui prend en charge un large éventail de systèmes d’exploitation, de langages de programmation, d’infrastructures, d’outils, de bases de données et d’appareils. Elle permet d’exécuter des conteneurs Linux avec l’intégration de Docker, de créer des applications avec JavaScript, Python, .NET, PHP, Java et Node.js, de créer des serveurs principaux pour des appareils iOS, Android et Windows. Le service cloud Azure prend en charge les technologies auxquelles des millions de développeurs et de professionnels de l’informatique font déjà confiance.

Lorsque vous générez ou faites migrer des ressources informatiques vers un fournisseur de services de cloud public, vous comptez sur les capacités de cette organisation à protéger vos applications et données avec les services et les contrôles qu’elle vous fournit pour gérer la sécurité de vos ressources cloud.

L’infrastructure d’Azure est conçue de l’installation vers les applications pour héberger des millions de clients simultanément, et constitue une base fiable permettant de répondre aux besoins des entreprises en matière de sécurité. En outre, Azure vous offre un large éventail d’options de sécurité configurables, ainsi que la possibilité de contrôler ces options pour vous permettre de personnaliser la sécurité et de répondre ainsi aux exigences uniques des déploiements de votre organisation. Ce document vous permettra de comprendre comment les fonctionnalités de sécurité d’Azure peuvent vous aider à répondre à ces besoins.

### <a name="abstract"></a>Résumé
Azure Operational Security comprend les services, contrôles et fonctionnalités auxquels les utilisateurs ont accès pour protéger leurs données, leurs applications et les autres ressources de Microsoft Azure. Azure Operational Security repose sur une infrastructure qui intègre les connaissances acquises via différentes fonctionnalités spécifiques de Microsoft, y compris Microsoft Security Development Lifecycle (SDL), le programme Microsoft Security Response Center et une connaissance approfondie des cybermenaces.

Ce livre blanc décrit l’approche de Microsoft en matière de sécurité opérationnelle au sein de la plateforme cloud Microsoft Azure. Il aborde les services ci-dessous :
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) est la solution de gestion informatique pour le cloud hybride. Utilisée seule ou pour étendre votre déploiement System Center existant, OMS vous donne la flexibilité et le contrôle pour gérer votre infrastructure sur le cloud.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

Avec OMS, vous pouvez gérer n’importe quelle instance dans n’importe quel cloud, notamment local, Azure, AWS, Windows Server, Linux, VMware et OpenStack, à moindre coût par rapport aux solutions concurrentes. Conçu pour les environnements cloud, OMS offre une nouvelle approche de la gestion de votre entreprise, qui constitue le moyen le plus rapide et le plus économique pour répondre aux nouveaux défis métiers et pour gérer les nouvelles charges de travail, applications et environnements cloud.

### <a name="oms-services"></a>Services d’OMS

Les fonctionnalités fondamentales d’OMS sont fournies par un ensemble de services qui s’exécutent dans Azure. Chaque service assure une fonction de gestion spécifique, et vous pouvez combiner plusieurs services pour mettre en œuvre différents scénarios de gestion.

| Service  | Description|
| :------------- | :-------------|
| Log Analytics | Surveillez et analysez la disponibilité et les performances de différentes ressources, notamment de machines physiques et virtuelles. |
|Automation | Automatisez des processus manuels et appliquez des configurations pour machines physiques et virtuelles. |
| Sauvegarde | Sauvegardez et restaurez les données critiques. |
| Site Recovery | Assurez la haute disponibilité des applications critiques. |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un référentiel central. Ces données peuvent comprendre des événements, des données de performances ou des données personnalisées fournies par le biais de l’API. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation.


Cette méthode vous permet de consolider les données issues de différentes sources et de combiner ainsi des données de vos services Azure avec votre environnement local existant. En outre, cette approche dissocie clairement la collecte des données de l’exécution d’actions sur ces dernières, de sorte que toutes les actions sont disponibles sur tous les types de données.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

Le service Log Analytics gère vos données basées sur le cloud en toute sécurité en utilisant les méthodes suivantes :
-   ségrégation des données
-   conservation des données
-   sécurité physique
-   gestion des incidents
-   conformité
-   certifications relatives aux normes de sécurité

### <a name="azure-backup"></a>Sauvegarde Azure

Le composant [Sauvegarde Azure](http://azure.microsoft.com/documentation/services/backup) fournit des services de sauvegarde et de restauration des données et fait partie de la suite OMS de produits et services.
Il protège les données de vos applications et les conserve des années durant, sans nécessiter aucun investissement en capital et moyennant des frais d’exploitation minimes. Cette solution permet de sauvegarder des données à partir de serveurs Windows physiques et virtuels, en plus des charges de travail des applications telles que SQL Server et SharePoint. Elle peut également être utilisée par [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) afin de répliquer les données protégées vers Azure pour la redondance et le stockage à long terme.


Les données protégées dans le service Sauvegarde Azure sont stockées dans un archivage de sauvegarde situé dans une zone géographique spécifique. Les données sont répliquées dans la même région et, selon le type d’archivage, peuvent également être répliquées vers une autre région afin d’accroître la résilience.

### <a name="management-solutions"></a>Solutions de gestion
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.


Les [solutions de gestion](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) constituent des ensembles de logiques prépackagés qui implémentent un scénario de gestion spécifique utilisant un ou de plusieurs services OMS. Microsoft et ses partenaires mettent à votre disposition différentes solutions que vous pouvez facilement ajouter à votre abonnement Azure pour augmenter la valeur de votre investissement dans la solution OMS. En tant que partenaire, vous pouvez créer vos propres solutions pour prendre en charge vos applications et services et les fournir aux utilisateurs par le biais de la plateforme Place de marché Azure ou des modèles de démarrage rapide.


![Solutions de gestion](./media/azure-operational-security/azure-operational-security-fig4.png)

La [solution Gestion des mises à jour](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) constitue un bon exemple de solution utilisant plusieurs services pour offrir des fonctionnalités supplémentaires. Cette solution utilise l’agent [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour Windows et Linux afin de collecter les informations relatives aux mises à jour requises sur chaque agent. Elle écrit ces données dans le référentiel Log Analytics qui vous permet de les analyser à l’aide du tableau de bord inclus.

Lorsque vous créez un déploiement, le système utilise les runbooks d’[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) pour installer les mises à jour requises. Vous gérez la totalité de ce processus dans le portail et n’avez pas à vous soucier des détails sous-jacents.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center vous permet de protéger vos ressources Azure. Il fournit des fonctions intégrées de surveillance de la sécurité et de gestion des stratégies sur vos abonnements Azure. Au sein du service, vous pouvez définir des stratégies non seulement sur vos abonnements Azure, mais également sur les [groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), pour une granularité accrue.

### <a name="security-policies-and-recommendations"></a>Stratégies et recommandations de sécurité

Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement ou groupe de ressources spécifique.

Dans le Centre de sécurité, vous devez définir des stratégies en fonction des exigences de sécurité de votre entreprise et du type d’applications ou de sensibilité des données.

![Stratégies et recommandations de sécurité](./media/azure-operational-security/azure-operational-security-fig5.png)


Les stratégies qui sont activées au niveau de l’abonnement sont automatiquement propagées à tous les groupes de ressources de l’abonnement, comme indiqué dans le schéma de droite :


### <a name="data-collection"></a>Collecte des données

Azure Security Center collecte les données de vos machines virtuelles afin d’évaluer l’état de leur sécurité, de fournir des recommandations en matière de sécurité et de vous avertir des menaces. Lorsque vous accédez à Azure Security Center pour la première fois, la collecte de données est activée sur toutes les machines virtuelles de votre abonnement. La collecte de données est recommandée, mais vous pouvez refuser cette fonctionnalité en la désactivant dans la stratégie d’Azure Security Center.

### <a name="data-sources"></a>Sources de données

- Pour assurer une visibilité sur l’état de la sécurité, identifier les vulnérabilités, recommander des mesures d’atténuation et détecter les menaces actives, Azure Security Center analyse les données provenant des sources suivantes :

-   Services Azure : utilise les informations relatives à la configuration des services Azure que vous avez déployés en communiquant avec le fournisseur de ressources de ce service.

- Trafic réseau : tire parti des métadonnées de trafic réseau échantillonnées provenant de l’infrastructure de Microsoft, telles que l’IP/le port source/de destination, la taille de paquet et le protocole réseau.

-   Solutions de partenaires : collecte les alertes de sécurité des solutions de partenaires intégrées, telles que les solutions de pare-feu et anti-programme malveillant.

-   Vos machines virtuelles : utilise les informations de configuration et les données relatives à des événements de sécurité, telles que des événements Windows et des journaux d’audit, des journaux IIS, des messages syslog et des fichiers de vidage sur incident, sur vos machines virtuelles.

### <a name="data-protection"></a>Protection des données

Pour aider les clients à prévenir, détecter et contrer les menaces, Azure Security Center collecte et traite des données de sécurité, notamment des informations de configuration, des métadonnées, des journaux d’événements et des fichiers de vidage sur incident. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.

-   **Ségrégation des données :**les données client sont maintenues séparées logiquement sur chaque composant, dans l’ensemble du service. Toutes les données sont balisées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service.

-   **Accès aux données** : pour fournir des recommandations en matière de sécurité et enquêter sur les éventuelles menaces de sécurité, le personnel de Microsoft peut accéder aux informations collectées ou analysées par les services Azure, notamment les fichiers de vidage sur incident, les événements de création de processus, les captures instantanées et artefacts de disque de machine virtuelle, qui peuvent involontairement exposer des données client ou personnelles provenant de vos machines virtuelles. Nous respectons les [Conditions d’utilisation et la Déclaration de confidentialité de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), qui indiquent que Microsoft n’utilise pas les données client ou ne tire pas d’informations de ces dernières à des fins commerciales, publicitaires ou similaires.

-   **Utilisation des données** : Microsoft utilise des modèles et des informations sur les menaces observées auprès de multiples locataires pour améliorer ses fonctionnalités de prévention et de détection. Cette utilisation s’effectue en accord avec les engagements de confidentialité décrits dans la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) de Microsoft.

### <a name="data-location"></a>Emplacement des données

Le Centre de sécurité Azure collecte des copies éphémères de vos fichiers de vidage sur incident et les analyse pour obtenir des preuves de tentatives d’attaque par le biais de code malveillant exploitant une faille de sécurité et de compromis ayant abouti. Azure Security Center effectue cette analyse dans la même région géographique que l’espace de travail, puis supprime les copies éphémères une fois l’analyse terminée. Les artefacts des ordinateurs sont stockés de manière centralisée dans la même région que la machine virtuelle.

-   **Vos comptes de stockage** : un compte de stockage est spécifié pour chaque région où des machines virtuelles fonctionnent. Cela vous permet de stocker des données dans la même région que la machine virtuelle à partir de laquelle les données sont collectées.

-   **Stockage Azure Security Center** : les informations relatives aux alertes de sécurité, notamment les alertes des partenaires, les recommandations et le statut d’intégrité de la sécurité, sont stockées de manière centralisée (actuellement aux États-Unis). Ces informations peuvent inclure des informations de configuration associées et des événements de sécurité collectés à partir de vos machines virtuelles, le cas échéant, pour vous fournir l’alerte de sécurité, la recommandation ou l’état d’intégrité de la sécurité.


## <a name="azure-monitor"></a>Azure Monitor

La solution [Sécurité et audit d’OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) permet au service informatique de surveiller activement toutes les ressources, ce qui peut aider à minimiser l’impact des incidents de sécurité. Cette solution possède des domaines de sécurité qui peuvent être utilisés pour la surveillance des ressources. Le domaine de sécurité fournit un accès rapide à des options pour la surveillance de la sécurité. Les domaines suivants sont abordés plus en détail :

-   Évaluation des logiciels malveillants
-   Update assessment (Évaluation des mises à jour)
-   Identité et accès.

Azure Monitor propose des références vers des informations sur des types de ressources spécifiques. Il offre des services de visualisation, requête, routage, alertes, mise à l’échelle et automatisation pour les données de l’infrastructure Azure (journal d’activité) et pour chaque ressource Azure (journaux de diagnostic).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus.

En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

### <a name="azure-activity-log"></a>Journaux d’activité


Ce journal fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Le journal d’activité était précédemment appelé « journal d’audit » ou « journal des opérations », car il indique les événements de plan de contrôle pour vos abonnements.

![Journaux d’activité](./media/azure-operational-security/azure-operational-security-fig7.png)

Avec le journal d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur des ressources dans votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas d’opérations de lecture (GET) ni d’opérations pour les ressources qui utilisent le modèle Classic.

### <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure

Ces journaux sont émis par une ressource et fournissent des données riches et fréquentes sur le fonctionnement de cette ressource. Le contenu de ces journaux varie en fonction du type de ressource.

Par exemple, les journaux des événements système Windows sont une catégorie de journal de diagnostic pour les machines virtuelles et les objets blob, les tables, et les journaux de file d’attente sont les catégories de journaux de diagnostic pour les comptes de stockage.

Les journaux de diagnostic diffèrent du [journal d’activité (anciennement appelé journal d’audit ou journal des opérations)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource.

### <a name="metrics"></a>Mesures

Azure Monitor vous permet d’utiliser la télémétrie pour surveiller les performances et l’intégrité de vos charges de travail sur Azure. Les mesures (aussi appelées compteurs de performances) émises par la plupart des ressources Azure sont le type de données de télémétrie Azure plus important. Azure Monitor propose plusieurs façons de configurer et d’utiliser ces [mesures](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) pour l’analyse et le dépannage. Les mesures sont une source précieuse de télémétrie et vous permettent d’effectuer les tâches suivantes :

-   **Suivre les performances** de votre ressource (par exemple une machine virtuelle, un site web ou une application logique) en traçant ses mesures sur un graphique de portail et en épinglant ce graphique sur un tableau de bord.

-   **Être averti d’un problème** ayant un impact sur les performances de votre ressource lorsqu’une mesure dépasse un certain seuil.

-   **Configurer des actions automatisées**, telles que la mise à l’échelle automatique d’une ressource ou le déclenchement d’un runbook lorsqu’une mesure dépasse un certain seuil.

-   **Effectuer des analyses avancées** ou créer des rapports sur les tendances de performances ou d’utilisation de vos ressources.

-   **Archiver** l’historique des performances ou d’intégrité de votre ressource à des fins d’audit/de conformité.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Cette fonctionnalité Azure active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l'extension de diagnostic à partir de différentes sources. Les sources actuellement prises en charge sont les [rôles Web et les rôles de travail Azure Cloud Service](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview) sous Microsoft Windows et [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Les autres services Azure ont leurs propres diagnostics distincts.

## <a name="azure-network-watcher"></a>Azure Network Watcher

L’audit de sécurité de votre réseau est essentiel pour détecter ses vulnérabilités et assurer la conformité avec votre modèle de gouvernance réglementaire et de sécurité informatique. Avec l’affichage du groupe de sécurité, vous pouvez récupérer le groupe de sécurité réseau et les règles de sécurité configurés, ainsi que les règles de sécurité en vigueur. Avec la liste des règles appliquées, vous pouvez identifier les ports ouverts et évaluer la vulnérabilité du réseau.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure. Ce service inclut la capture de paquets, le tronçon saut suivant, la vérification des flux IP, l’affichage de groupe de sécurité, les journaux de flux de groupe de sécurité réseau. La surveillance basée sur des scénarios fournit une vue de bout en bout des ressources réseau, à l’inverse de la surveillance des ressources réseau qui s’intéresse à chaque ressource spécifique.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Network Watcher propose actuellement les fonctionnalités suivantes :

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Journaux d’audit</a>** - Les opérations réalisées dans le cadre de la configuration des réseaux sont journalisées. Ces journaux peuvent être affichés dans le portail Azure ou récupérés à l’aide des outils Microsoft tels que Power BI ou des outils tiers. Les journaux d’audit sont disponibles via le portail, PowerShell, l’interface de ligne de commande et l’API REST. Pour plus d’informations sur les journaux d’audit, consultez la page Afficher les journaux d’activité pour auditer les actions sur les ressources. Les journaux d’audit sont disponibles pour les opérations effectuées sur toutes les ressources réseau.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Vérification des flux IP</a>** - Vérifie si un paquet est autorisé ou refusé en fonction des paramètres de paquet des informations à 5 tuples de flux (adresse IP de destination, adresse IP source, port de destination, port source et protocole). Si le paquet est refusé par un groupe de sécurité réseau, la règle et le groupe qui ont refusé le paquet sont renvoyés.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Tronçon suivant</a>** - Détermine le tronçon suivant pour les paquets routés dans la structure de réseau Azure, vous permettant de diagnostiquer les itinéraires définis par l’utilisateur mal configurés.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Affichage des groupes de sécurité</a>** - Obtient les règles de sécurité appliquées et effectives d’une machine virtuelle.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Journalisation des flux de groupe de sécurité réseau</a>** - Les journaux de flux pour les groupes de sécurité réseau vous permettent de capturer les journaux relatifs au trafic autorisé ou refusé par les règles de sécurité dans le groupe. Le flux est défini par des informations à 5 tuples : adresse IP source, adresse IP de destination, port source, port de destination et protocole.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) peut stocker des métriques qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées à un service de stockage. Les transactions sont indiquées au niveau de l’opération d’API ainsi qu’au niveau du service de stockage, et la capacité est indiquée au niveau du service de stockage. Les données de métriques peuvent être utilisées pour analyser l'utilisation du service de stockage, diagnostiquer les problèmes au niveau des demandes effectuées auprès du service de stockage et améliorer les performances des applications qui utilisent un service.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) effectue la journalisation et fournit des données de métriques pour un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage La journalisation Storage Analytics est disponible pour les [services BLOB, de File d’attente et de Table](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage.

Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort. Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison BLOB, mais pas dans ses points de terminaison de Table ou de File d’attente, seuls des journaux relatifs au service BLOB sont créés.

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer dans le [portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez l’article [Surveillance d’un compte de stockage dans le portail Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Set Service Properties pour activer Storage Analytics individuellement pour chaque service.

Les données agrégées sont stockées dans un objet blob connu (pour la journalisation) et dans des tables connues (pour les métriques), qui sont accessibles via les API du service BLOB et du service de Table.

Storage Analytics a une limite de 20 To pour la quantité de données stockées qui est indépendante de la limite totale pour votre compte de stockage. Tous les journaux sont stockés dans des objets [blob de blocs](https://docs.microsoft.com/azure/storage/storage-analytics) dans un conteneur nommé $logs, qui est automatiquement créé lorsque Storage Analytics est activé pour un compte de stockage.

Les actions suivantes effectuées par Storage Analytics sont facturables :

-   Demandes de création d’objets blob pour la journalisation
-   Demandes de création d'entités de table pour les métriques.

> [!Note]
> Pour plus d’informations sur les stratégies de facturation et de rétention de données, consultez la page [Storage Analytics and Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing) (Storage Analytics et facturation).
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Le compte de stockage peut prendre en charge un plus grand nombre de disques s’ils ne sont pas tous fortement sollicités en même temps.

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
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD inclut également une suite complète de fonctionnalités de gestion d’identité, comme l’authentification multifacteur, l’inscription d’appareil, la gestion de mot de passe libre-service, la gestion de groupes libre-service, la gestion des comptes privilégiés, le contrôle d’accès en fonction du rôle, la surveillance de l’utilisation de l’application, ainsi que la création d’audits complets, la surveillance de la sécurité et la création d’alertes.

-   En améliorant la sécurité des applications grâce à l’authentification multifacteur et l’accès conditionnel Azure AD.

-   En surveillant l’utilisation des applications et en protégeant votre entreprise contre les menaces avancées, grâce à des fonctionnalités de création de rapports et de surveillance.

Azure Active Directory (Azure AD) comprend des rapports sur la sécurité, les activités et l’audit concernant votre annuaire. [Le rapport d’audit d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) permet aux clients d’identifier les actions privilégiées qui se sont produites dans leur Azure Active Directory. Ces actions privilégiées incluent les modifications d'élévation (par exemple la création de rôle ou les réinitialisations de mot de passe), la modification des configurations de stratégie (par exemple les stratégies de mot de passe) ou bien les modifications apportées à la configuration de répertoire (par exemple les modifications apportées aux paramètres de fédération de domaine).

Les rapports fournissent l’enregistrement d’audit pour le nom d’événement, l’acteur qui a effectué l’action, la ressource cible affectée par la modification, ainsi que la date et l’heure (UTC). Les clients sont en mesure de récupérer la liste des événements d’audit pour leur annuaire Azure Active Directory par le biais du [portail Azure](https://portal.azure.com/), comme décrit à la page [Afficher vos journaux d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Voici une liste des rapports fournis :

| Rapports de sécurité  | Rapports d’activité| Rapport d’audit |
| :------------- | :-------------| :-------------|
|Connexions à partir de sources inconnues | Utilisation des applications : résumé | Rapport d’audit d’annuaire |
|Connexions après plusieurs échecs | Utilisation des applications : présentation détaillée |   |
|Connexions depuis plusieurs zones géographiques | Tableau de bord de l’application |  |
|Connexions depuis des adresses IP avec des activités suspectes |Erreurs de configuration de compte |  |
|Activité de connexion anormale |Appareils des utilisateur individuels |  |
|Connexions à partir d’appareils potentiellement infectés |Activité des utilisateurs individuels |   |
|Utilisateurs ayant une activité de connexion anormale |Rapport d'activité de groupes |   |
| |Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe |   |
| |Activité de réinitialisation de mot de passe |   | |



Les données de ces rapports peuvent être utiles pour vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. Les [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) de création de rapports Azure AD fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

Les événements du rapport d’audit d’Azure AD sont conservés pendant 180 jours.

> [!Note]
> Pour plus d’informations sur la rétention des rapports, consultez la page [Stratégies de rétention des rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Pour les clients intéressés par le stockage de leurs [événements d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) pour des périodes de rétention plus longues, l’API de création de rapports peut être utilisée pour extraire régulièrement des événements d’audit dans un magasin de données distinct.

## <a name="summary"></a>Résumé

Cet article explique comment protéger votre vie privée et sécuriser vos données, en utilisant dans le même temps des logiciels et des services facilitant la gestion de l’infrastructure informatique de votre organisation. Microsoft reconnaît que lorsque vous confiez vos données à des tiers, cette confiance exige une sécurité rigoureuse. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service. La sécurisation et la protection des données constituent une priorité de premier plan pour Microsoft.

Cet article explique

-   Comment les données sont collectées, traitées et sécurisées dans Operations Management Suite (OMS).

-   Comment analyser rapidement les événements de plusieurs sources de données. Comment identifier les risques de sécurité et comprendre la portée et l’impact des menaces et des attaques pour atténuer les dommages d’une violation de la sécurité.

-   Comment identifier les modèles d’attaque en visualisant le trafic IP malveillant sortant et les types de menaces malveillantes. Comment comprendre la posture de sécurité de tout votre environnement indépendamment de la plateforme.

-   Comment capturer toutes les données du journal et des événements nécessaires pour un audit de sécurité ou de conformité. Comment réduire le temps et les ressources nécessaires pour effectuer un audit de sécurité avec un ensemble complet et exportable de données de journal et d’événements, dans lequel vous pouvez effectuer des recherches.

<ul>
<li>Comment collecter les événements de sécurité, les audits et les analyses sur les violations pour garder un œil sur vos ressources :</li>
<ul>
<li>État de la sécurité</li>
<li>Problème important</li>
<li>Résumés des menaces</li>
</ul>
</ul>

## <a name="next-steps"></a>Étapes suivantes

- [Conception et sécurité opérationnelle](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft place la sécurité au cœur de la conception de ses services et de ses logiciels pour s’assurer de la résilience de son infrastructure de cloud et de sa capacité à se défendre contre les attaques de sécurité.

- [Operations Management Suite | Security &amp; Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance)

Utilisez les données et les fonctions d’analyse de sécurité de Microsoft pour une détection des menaces plus intelligente et plus efficace.

- [Guide des opérations et de planification du Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) : couvre un ensemble d’étapes et de tâches à suivre pour optimiser votre utilisation du Security Center en fonction des critères de sécurité et du modèle de gestion cloud de votre entreprise.

