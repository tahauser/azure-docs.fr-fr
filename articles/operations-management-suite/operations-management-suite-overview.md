---
title: "Vue d’ensemble d’Operations Management Suite (OMS) | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) est une solution de gestion informatique de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.  Cet article décrit la valeur métier d’OMS, identifie les différents services et offres inclus dans OMS et fournit des liens permettant d’accéder à leur contenu détaillé."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 94dedebe48060441cd3167fea87f6b721eb14517
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="what-is-operations-management-suite-oms"></a>Présentation d’Operations Management Suite (OMS)
Cet article fournit une présentation de la suite Operations Management Suite (OMS), notamment une brève vue d’ensemble de sa valeur métier, des services et des solutions de gestion qu’elle comprend, ainsi que des offres qui regroupent différents services et solutions.  Cet article intègre également des liens d’accès vers une documentation détaillée sur le déploiement et l’utilisation de chaque service et solution.

## <a name="from-on-premises-to-the-cloud"></a>Depuis un emplacement local vers le cloud
Depuis longtemps déjà, Microsoft fournit des produits de gestion d’environnements d’entreprise.  Plusieurs de ces produits ont été consolidés dans la suite System Center de produits de gestion en 2007.  Cette suite comprenait Configuration Manager qui fournit des fonctionnalités telles que la distribution de logiciels et l’inventaire logiciel, Operations Manager qui assure une surveillance proactive des systèmes et des applications, Orchestrator qui inclut des runbooks pour automatiser les processus manuels, ainsi que Data Protection Manager qui offre des fonctions de sauvegarde et de récupération des données critiques.

À mesure qu’un nombre croissant de ressources informatiques ont été déplacées vers le cloud, les produits System Center ont intégré des fonctionnalités cloud de plus en plus nombreuses, telles que la gestion de ressources dans Azure par Operations Manager et par Orchestrator.  Cependant, ils étaient toujours essentiellement conçus comme des solutions locales et nécessitaient un important investissement en matière de déploiement et d’administration d’un environnement de gestion local.  Pour tirer profit du cloud et prendre en charge les applications futures, il s’est donc révélé nécessaire d’adopter une nouvelle approche vis-à-vis de la gestion.

## <a name="introducing-operations-management-suite"></a>Présentation d’Operations Management Suite
Operations Management Suite (également désigné sous l’acronyme OMS) est un ensemble de services de gestion qui ont été conçus dans le cloud dès le début.  Plutôt que de déployer et de gérer des ressources locales, les composants OMS sont entièrement hébergés dans Azure.  OMS ne requiert qu’une configuration minime et vous permet d’être opérationnel en quelques minutes seulement.  

- **Coût minimal et facilité de déploiement.**  Tous les composants et données concernant OMS étant stockés dans Azure, vous pouvez commencer à utiliser la solution très rapidement, sans avoir à faire face à la complexité et à l’investissement inhérents aux composants locaux.
- **Mise à l’échelle vers les niveaux du cloud.**  Vous n’avez plus à vous soucier de payer pour des ressources informatiques dont vous n’avez pas l’utilité ni de manquer d’espace de stockage, car le cloud vous permet de payer uniquement les services que vous utilisez réellement et s’adapte rapidement à vos besoins en termes de charge.  Vous pouvez commencer par gérer un petit nombre de ressources, puis procéder à une montée en puissance sur la totalité de votre environnement.
- **Mise à disposition des dernières fonctionnalités.**  Les fonctionnalités des services d’OMS sont continuellement enrichies ou mises à jour.  Vous disposez en permanence d’un accès aux fonctionnalités les plus récentes sans avoir à déployer aucune mise à jour.
- **Services intégrés.**  Bien que chacun des services d’OMS procure en soi une importante valeur ajoutée, ces services peuvent fonctionner de concert pour prendre en charge des scénarios de gestion complexes.  Par exemple, un runbook dans Azure Automation peut piloter un processus de basculement avec Azure Site Recovery, puis enregistrer les informations associées dans Log Analytics pour générer une alerte.
- **Application des connaissances recueillies au niveau mondial.**  Les solutions de gestion disponibles dans OMS ont continuellement accès aux dernières informations.  Par exemple, la solution Sécurité et audit peut exécuter une analyse des menaces reposant sur les derniers risques détectés à travers le monde.
- **Accessibilité en tout lieu.**  Accédez à votre environnement de gestion depuis tout emplacement où vous disposez d’un navigateur.  Installez l’application OMS sur votre smartphone afin d’accéder instantanément à vos données de surveillance.

### <a name="is-it-just-for-the-cloud"></a>La solution est-elle uniquement destinée au cloud ?
Le fait que les services OMS s’exécutent dans le cloud ne signifie pas qu’ils ne peuvent pas gérer votre environnement local de manière efficace.  Il vous suffit de placer un agent sur tout ordinateur Windows ou Linux de votre centre de données pour que cet agent envoie des données à Log Analytics, qui les analysera en même temps que toutes les autres données collectées à partir des services cloud ou locaux.  Utilisez les services de sauvegarde Azure et Azure Site Recovery pour tirer profit des capacités du cloud en matière de sauvegarde et de haute disponibilité pour vos ressources locales.  
Les runbooks figurant dans le cloud ne peuvent généralement pas accéder à vos ressources locales, mais vous pouvez aussi installer un agent sur un ou plusieurs ordinateurs qui hébergeront des runbooks dans votre centre de données.  Lorsque vous démarrez un runbook, vous devez simplement indiquer si vous souhaitez l’exécuter dans le cloud ou sur un Worker local.

## <a name="hybrid-management-with-system-center"></a>Gestion hybride avec System Center
Si vous disposez d’une installation System Center existante, vous pouvez intégrer ces composants aux services OMS afin de fournir une solution hybride pour vos environnements locaux et cloud, de façon à tirer profit des spécialités de chaque produit.  Connectez votre groupe de gestion Operations Manager existant à Log Analytics pour analyser les agents gérés dans le cloud.  Utilisez votre processus de sauvegarde existant avec Data Protection Manager pour sauvegarder vos données dans le cloud.  


## <a name="oms-services"></a>Services d’OMS
Les fonctionnalités fondamentales d’OMS sont fournies par un ensemble de services qui s’exécutent dans Azure.  Chaque service assure une fonction de gestion spécifique, et vous pouvez combiner plusieurs services pour mettre en œuvre différents scénarios de gestion.

|| Service | Description |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Surveillez et analysez la disponibilité et les performances de différentes ressources, notamment de machines physiques et virtuelles. |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automatisation | Automatisez des processus manuels et appliquez des configurations pour machines physiques et virtuelles. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Sauvegarde | Sauvegardez et restaurez les données critiques. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Assurez la haute disponibilité des applications critiques. |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un référentiel central.  Ces données peuvent comprendre des événements, des données de performances ou des données personnalisées fournies par le biais de l’API. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation.  Cette méthode vous permet de consolider les données issues de différentes sources, et de combiner des données de vos services Azure avec votre environnement local existant.  En outre, cette approche dissocie clairement la collecte des données de l’exécution d’actions sur ces dernières, de sorte que toutes les actions sont disponibles sur tous les types de données.  

![Présentation de Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Collecte de données
Vous disposez de différentes méthodes pour regrouper des données dans le référentiel afin que Log Analytics puisse les analyser.

- **Ordinateurs et machines virtuelles Windows ou Linux.**  Vous installez Microsoft Monitoring Agent sur les ordinateurs ou machines virtuelles [Windows](../log-analytics/log-analytics-windows-agent.md) et [Linux](../log-analytics/log-analytics-linux-agents.md) dont vous souhaitez collecter les données.  L’agent télécharge automatiquement à partir de Log Analytics une configuration qui définit les événements et les données de performances à collecter.  Vous pouvez facilement installer cet agent sur les machines virtuelles s’exécutant dans Azure à l’aide du Portail Azure.  Si vous disposez d’un environnement Operations Manager existant, vous pouvez connecter le groupe de gestion à Log Analytics et démarrer automatiquement la collecte de données à partir de tous les agents existants.
- **Services Azure.**  Log Analytics collecte les données de télémétrie générées par [les diagnostics Azure et la surveillance Azure](../log-analytics/log-analytics-azure-storage.md) et les regroupe dans le référentiel, ce qui vous permet de surveiller les ressources Azure.
- **API Collecteur de données.**  Log Analytics comporte une [API REST assurant le remplissage de données issues de n’importe quel client](../log-analytics/log-analytics-data-collector-api.md).  Cela vous permet de collecter des données à partir d’applications tierces ou d’implémenter des scénarios de gestion personnalisés.  Une méthode courante consiste à utiliser un runbook dans Azure Automation pour collecter les données, puis à utiliser l’API Collecteur de données pour écrire ces données dans le référentiel.

#### <a name="reporting-and-analyzing-data"></a>Signalement et analyse de données
Log Analytics intègre un puissant langage de requête destiné à extraire les données stockées dans le référentiel.  Étant donné que toutes les sources de données sont stockées sous forme d’enregistrements, vous pouvez analyser les données provenant de plusieurs sources dans une seule et même requête.
  
Outre l’analyse ad hoc, Log Analytics offre différentes méthodes de signalement et d’analyse de données à partir d’une requête.

- **Vues et tableaux de bord.**  Les [vues](../log-analytics/log-analytics-view-designer.md) et les [tableaux de bord](../log-analytics/log-analytics-dashboards.md) permettent de visualiser les résultats d’une requête dans le portail.  Les solutions de gestion comprennent généralement des vues qui analysent les données de la solution.  Vous pouvez également créer vos propres vues personnalisées pour analyser les données et les rendre aussitôt accessibles dans votre portail personnalisé.
- **Exportation.**  Vous avez la possibilité d’exporter les résultats de n’importe quelle requête afin de les analyser en dehors de Log Analytics.  Vous pouvez même planifier une exportation régulière vers [Power BI](../log-analytics/log-analytics-powerbi.md) qui offre d’importantes fonctionnalités de visualisation et d’analyse.
- **API Recherche dans les journaux.**  Log Analytics comporte une [API REST assurant la collecte de données à partir de n’importe quel client](../log-analytics/log-analytics-log-search-api.md).  Cela vous permet de manipuler par programmation les données collectées dans le référentiel ou d’accéder à ces données par le biais d’un autre outil d’analyse.

#### <a name="alerting"></a>Génération d’alertes
Lorsque le service Log Analytics détecte un problème, il peut vous [alerter de manière proactive](../log-analytics/log-analytics-alerts.md) ou prendre des mesures correctives.  Comme toutes les autres analyses de Log Analytics, cette opération est effectuée par le biais d’une recherche dans les journaux.  Cette recherche s’exécute à intervalles réguliers, et une alerte est créée si les résultats correspondent à des critères spécifiques.

![Alertes Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Outre la création d’un enregistrement d’alerte dans le référentiel Log Analytics, les alertes peuvent exécuter les actions ci-après.

- **E-mail.**  Une alerte vous envoie un e-mail pour vous informer de manière proactive de la détection d’un problème.
- **Runbook.**  Une alerte de Log Analytics peut démarrer un runbook dans Azure Automation.  Cette opération est généralement effectuée pour tenter de résoudre le problème détecté.  Si le problème est survenu dans Azure ou dans un autre cloud, le runbook peut être démarré dans le cloud. Dans le cas d’un problème sur une machine physique ou virtuelle, le runbook peut être démarré sur un agent local.
- **Webhook.**  Une alerte peut démarrer un webhook et lui transmettre des données issues des résultats de la recherche dans les journaux.  Cette approche autorise l’intégration de la solution à des services externes, tels qu’un autre système de génération d’alertes, ou peut tenter d’entreprendre une action corrective pour un site web externe.

### <a name="azure-automation"></a>Azure Automation
Le service [Azure Automation](http://azure.microsoft.com/documentation/services/automation) assure l’automatisation des processus et la gestion des configurations pour OMS.  Il automatise les processus manuels et facilite l’application de configurations pour machines physiques et virtuelles.  

#### <a name="process-automation"></a>Automatisation de processus
Azure Automation automatise les processus manuels à l’aide de [runbooks](../automation/automation-runbook-types.md) qui reposent sur un script PowerShell ou un workflow PowerShell.  Ce service inclut également des ressources prenant en charge les runbooks, comme les variables qui peuvent être partagées entre plusieurs runbooks, ainsi que les informations d’identification et les connexions qui vous permettent de stocker des informations chiffrées susceptibles d’être requises pour un runbook à des fins d’authentification.
Les runbooks assurent l’automatisation des processus pour les autres services de la suite.  Étant donné que chacun des autres services est accessible avec PowerShell ou par le biais d’une API REST, vous pouvez créer des runbooks pour effectuer certaines opérations telles que la collecte des données de gestion dans Log Analytics ou le lancement d’une sauvegarde avec le service Sauvegarde Azure.

##### <a name="accessing-resources"></a>Accès aux ressources
Étant donné que les runbooks reposent sur PowerShell, ils peuvent gérer n’importe quelle ressource accessible avec les applets de commande PowerShell.  Lorsque vous [chargez un module](../automation/automation-integration-modules.md) dans votre compte Automation, ce module devient disponible pour tous les runbooks dans ce compte. 
 
Lorsque les runbooks s’exécutent dans le cloud, ils peuvent accéder à toutes les ressources accessibles à partir du cloud.  Il peut s’agir de ressources dans votre abonnement Azure, dans un autre cloud tel qu’Amazon Web Services (AWS), ou dans un service accessible par le biais d’une API REST.  Les runbooks dans le cloud ne s’exécutent à l’aide d’aucune information d’identification, mais peuvent tirer parti des ressources d’Automation, telles que les informations d’identification, les connexions et les certificats, pour authentifier les ressources auxquelles ils ont accès.

Les ressources disponibles dans votre centre de données ne sont probablement pas accessibles à partir d’un runbook s’exécutant dans le cloud.  Toutefois, vous pouvez installer une ou plusieurs fonctionnalités [Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md) dans votre centre de données pour exécuter des runbooks qui doivent disposer d’un accès à des ressources locales.  Lorsque vous démarrez un runbook, vous devez indiquer s’il doit s’exécuter dans le cloud ou sur un Worker spécifique.

![Runbooks Azure Automation](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Démarrage d'un runbook
Les runbooks peuvent être [démarrés par un certain nombre de méthodes](../automation/automation-starting-a-runbook.md) et donc être inclus dans divers scénarios de gestion.  

- **Portail Azure.**  Comme d’autres services Azure, Azure Automation peut être géré à partir du Portail Azure.  Vous pouvez non seulement démarrer les runbooks, mais également les importer ou créer vos propres runbooks.
- **Planification.**  Vous pouvez planifier le démarrage de runbooks à intervalles réguliers.  Cette fonction vous permet de répéter automatiquement un processus de gestion régulier ou de collecter des données dans Log Analytics.
- **PowerShell et API.**  Vous pouvez démarrer des runbooks et leur transmettre les informations de paramètre requises à partir d’une applet de commande PowerShell ou de l’API REST Azure Automation.  
- **Webhook.**  Un webhook peut être créé pour tout runbook autorisant le démarrage du webhook à partir d’applications externes ou de sites web.
- **Alerte Log Analytics.**  Une alerte dans Log Analytics peut démarrer automatiquement un runbook pour tenter de corriger le problème qu’elle a identifié.

#### <a name="configuration-management"></a>Gestion des configurations
[Configuration d’état souhaité (DSC) PowerShell](../automation/automation-dsc-overview.md) est une plateforme de gestion dans Windows PowerShell qui vous permet de déployer et d’appliquer la configuration de machines physiques et virtuelles.  Azure Automation gère les configurations DSC et fournit un serveur collecteur dans le cloud auquel les agents peuvent accéder pour récupérer les configurations requises.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Sauvegarde Azure et Azure Site Recovery
Les services Sauvegarde Azure et Azure Site Recovery contribuent à la continuité d’activité et à la récupération d’urgence.  Chacun d’eux comporte des fonctionnalités qui vous permettent de veiller au maintien de la disponibilité des applications lorsque des interruptions surviennent et à la reprise des opérations normales lorsque les systèmes sont de nouveau accessibles.  Ces deux services concourent à la réalisation des objectifs de point de récupération (RPO) et des objectifs de délai de récupération (RTO) définis pour votre organisation. Votre RPO définit la limite admissible dans laquelle les données peuvent ne pas être disponibles lors d’une interruption, tandis que le RTO indique la durée admissible pendant laquelle un service ou une application peuvent rester inaccessibles au cours d’une interruption.

#### <a name="azure-backup"></a>Azure Backup
Le service [Sauvegarde Azure](http://azure.microsoft.com/documentation/services/backup) fournit des services de sauvegarde et de restauration des données pour OMS.  Il protège les données de vos applications et les conserve des années durant, sans nécessiter aucun investissement en capital et moyennant des frais d’exploitation minimes.  Cette solution permet de sauvegarder des données à partir de serveurs Windows physiques et virtuels, en plus des charges de travail des applications telles que SQL Server et SharePoint.  Elle peut également être utilisée par System Center Data Protection Manager (DPM) afin de répliquer les données protégées vers Azure pour la redondance et le stockage à long terme.

Les données protégées dans le service Sauvegarde Azure sont stockées dans un archivage de sauvegarde situé dans une zone géographique spécifique. Les données sont répliquées dans la même région et, selon le type d’archivage, peuvent également être répliquées vers une autre région afin d’accroître la résilience.

Dans Azure Backup, il existe trois scénarios fondamentaux.

- **Machine Windows avec un agent Sauvegarde Azure.** Sauvegardez des fichiers et des dossiers à partir de n’importe quel client ou serveur Windows directement dans votre archivage de sauvegarde Azure.<br><br>![Machine Windows avec un agent Sauvegarde Backup](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) ou serveur de sauvegarde Microsoft Azure.** Tirez profit de DPM ou du serveur de sauvegarde Microsoft Azure pour sauvegarder des fichiers, des dossiers et des charges de travail d’applications telles que SQL ou SharePoint dans un stockage local, puis les répliquer dans votre archivage de sauvegarde Azure. Ce scénario prend en charge les machines virtuelles Windows et Linux sur Hyper-V ou VMware.<br><br>![System Center Data Protection Manager (DPM) ou serveur de sauvegarde Microsoft Azure](media/operations-management-suite-overview/overview-backup-02.png)
- **Extensions de machine virtuelle Azure.** Sauvegardez des machines virtuelles Windows ou Linux dans Azure vers votre archivage de sauvegarde Azure.<br><br>![Extensions de machine virtuelle Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) assure la continuité d’activité en orchestrant la réplication des machines virtuelles et physiques locales vers Azure ou vers un site secondaire. Si votre site principal est inaccessible, vous effectuez un basculement vers l’emplacement secondaire afin que les utilisateurs puissent continuer à travailler, puis vous procédez à une restauration automatique lorsque le système redevient fonctionnel. 

Le service Azure Site Recovery assure la haute disponibilité des serveurs et des applications.  Il contribue à mettre en œuvre la stratégie de continuité d’activité et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles Hyper-V et VMware locales, ainsi que de serveurs physiques Windows ou Linux. Vous pouvez répliquer des machines vers un centre de données secondaire ou étendre votre centre de données en les répliquant sur Azure. Site Recovery fournit également un basculement et une récupération des charges de travail en toute simplicité. Cette solution s’intègre avec des mécanismes de récupération d’urgence tels que SQL Server AlwaysOn et fournit des plans de récupération pour un basculement facile des charges de travail réparties sur plusieurs machines.

Dans Azure Site Recovery, il existe trois scénarios de réplication fondamentaux.

- **Réplication de machines virtuelles Hyper-V.**  Si les machines virtuelles Hyper-V sont gérées dans des clouds VMM, vous pouvez les répliquer vers un centre de données secondaire ou vers Azure Storage. La réplication vers Azure se fait via une connexion Internet sécurisée. La réplication vers un centre de données secondaire se fait via le réseau LAN.  Si les machines virtuelles Hyper-V ne sont pas gérées par VMM, vous ne pouvez les répliquer que sur le stockage Azure. La réplication vers Azure se fait via une connexion Internet sécurisée.<br><br>![La réplication de machines virtuelles Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Réplication de machines virtuelles VMware.**  Vous pouvez répliquer des machines virtuelles VMware vers un centre de données secondaire exécutant VMware ou vers Azure Storage. La réplication vers Azure peut se faire via Azure ExpressRoute ou un réseau privé virtuel de site à site, ou via une connexion Internet sécurisée. La réplication vers un centre de données secondaire se fait sur le canal de données InMage Scout.<br><br>![Réplication de machines virtuelles VMware](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Réplication de serveurs physiques Windows et Linux.**  Vous pouvez répliquer des serveurs physiques vers un centre de données secondaire ou vers Azure Storage. La réplication vers Azure peut se faire via Azure ExpressRoute ou un réseau privé virtuel de site à site, ou via une connexion Internet sécurisée. La réplication vers un centre de données secondaire se fait sur le canal de données InMage Scout. Azure Site Recovery dispose d’une solution OMS qui affiche des statistiques, mais vous devez utiliser le portail Azure pour toutes les opérations.<br><br>![La réplication de serveurs physiques Windows ou Linux](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery stocke les métadonnées dans des archivages situés dans une zone géographique Azure spécifique. Le service Site Recovery ne stocke aucune donnée répliquée.

## <a name="management-solutions"></a>Solutions de gestion
Les [solutions de gestion](operations-management-suite-solutions.md) constituent des ensembles de logiques prépackagés qui implémentent un scénario de gestion spécifique tirant profit d’un ou de plusieurs services OMS.  Microsoft et ses partenaires mettent à votre disposition différentes solutions que vous pouvez facilement ajouter à votre abonnement Azure pour augmenter la valeur de votre investissement dans la solution OMS.  En tant que partenaire, vous pouvez créer vos propres solutions pour prendre en charge vos applications et services et les fournir aux utilisateurs par le biais de la plateforme Place de marché Azure ou des modèles de démarrage rapide.

La [solution Gestion des mises à jour](oms-solution-update-management.md) constitue un bon exemple de solution tirant profit de plusieurs services pour offrir des fonctionnalités supplémentaires.  Cette solution utilise l’agent Log Analytics pour Windows et Linux afin de collecter les informations relatives aux mises à jour requises sur chaque agent.  Elle écrit ces données dans le référentiel Log Analytics qui vous permet de les analyser à l’aide du tableau de bord inclus.  Lorsque vous créez un déploiement, le système utilise les runbooks d’Azure Automation pour installer les mises à jour requises.  Vous gérez la totalité de ce processus dans le portail et n’avez pas à vous soucier des détails sous-jacents.

![Solution](media/operations-management-suite-overview/overview-solution.png)

La plupart des solutions peuvent exécuter une ou plusieurs des fonctions ci-après.

- Collecte d’informations supplémentaires.  Log Analytics collecte diverses données émanant des clients et des services, notamment les événements et les données de performances.  Une solution de gestion peut collecter des informations supplémentaires non disponibles à partir d’autres sources de données, généralement à l’aide de runbooks Azure Automation.
- Analyse supplémentaire des informations collectées.  Les solutions de gestion intègrent des tableaux de bord et des vues qui assurent l’analyse et la visualisation des données.  Ces éléments renvoient à des recherches prédéfinies dans les journaux qui vous permettent d’explorer les données détaillées.  Ils peuvent également effectuer des analyses sur les données déjà collectées dans le référentiel, par exemple en recherchant dans les événements de sécurité les modèles qui indiquent une menace.
- Ajout de fonctionnalités.  Certaines solutions proposées par Microsoft peuvent reposer sur les capacités des services de base pour offrir des fonctionnalités supplémentaires.  Par exemple, le service Carte de service fournit sa propre console pour découvrir et mapper les dépendances des serveurs et des processus en temps réel.
Microsoft et ses partenaires ajoutent régulièrement des solutions à la suite OMS pour vous permettre d’accroître continuellement la valeur métier de votre investissement.  Vous pouvez parcourir et installer les solutions Microsoft par le biais du Catalogue de solutions dans le portail OMS, ou parcourir et installer les solutions de Microsoft et de ses partenaires au moyen de la plateforme Place de marché Azure dans le Portail Azure.  

![Galerie de solutions](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* En savoir plus sur [Azure Automation](../automation/automation-intro.md).
* En savoir plus sur [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* En savoir plus sur [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Découvrir les [solutions disponibles](../log-analytics/log-analytics-add-solutions.md) dans les différentes offres OMS. 

