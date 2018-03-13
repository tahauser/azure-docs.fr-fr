---
title: "Vue d’ensemble d’Azure Operational Security | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de l’infrastructure Azure Operational Security."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: f656edee1460ce2bb17a582ca240e3bb4089b708
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-operational-security-overview"></a>Vue d’ensemble d’Azure Operational Security
Azure Operational Security renvoie aux services, contrôles et fonctionnalités auxquels les utilisateurs ont accès pour protéger leurs données, leurs applications et d’autres ressources dans Microsoft Azure. [Azure Operational Security](https://docs.microsoft.com/azure/security/azure-operational-security) est une infrastructure qui intègre les connaissances acquises via différentes fonctionnalités spécifiques de Microsoft, notamment Microsoft Security Development Lifecycle (SDL), le programme Microsoft Security Response Center et une connaissance approfondie des cybermenaces.

Cet article de présentation d’Azure Operational Security se concentre sur les points suivants :

- Azure Operations Management Suite
-   Azure Security Center
-   Azure Monitor
-   Azure Network Watcher
-   Azure Storage Analytics
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
Le service des opérations informatiques est chargé de gérer l’infrastructure du centre de données, les applications et les données, y compris la stabilité et la sécurité de ces systèmes. Toutefois, l’obtention d’informations de sécurité dans des environnements informatiques de plus en plus complexes requiert souvent de la part des organisations qu’elles bricolent et rassemblent les données de plusieurs systèmes de sécurité et de gestion.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.

OMS est une solution de gestion informatique basée sur le cloud proposant de nombreuses offres comme IT Automation, Security & Compliance, Log Analytics, ainsi que la sauvegarde et la récupération. Par conséquent, il s’agit de la solution idéale pour gérer et protéger votre infrastructure informatique, localement et dans le cloud.

Les fonctionnalités fondamentales d’OMS sont fournies par un ensemble de services qui s’exécutent dans Azure. Chaque service assure une fonction de gestion spécifique, et vous pouvez combiner plusieurs services pour mettre en œuvre différents scénarios de gestion. Ces services incluent :

-   Log Analytics
-   Automatisation
-   Sauvegarde
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un référentiel central. Ces données peuvent comprendre des événements, des données de performances ou des données personnalisées fournies par le biais de l’API. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation. Cette méthode vous permet de consolider les données issues de différentes sources, et de combiner des données de vos services Azure avec votre environnement local existant. En outre, cette approche dissocie clairement la collecte des données de l’exécution d’actions sur ces dernières, de sorte que toutes les actions sont disponibles sur tous les types de données.

### <a name="automation"></a>Automatisation
Microsoft [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) permet aux utilisateurs d’automatiser les tâches répétitives, manuelles, de longue durée et susceptibles de générer des erreurs, qui sont communément exécutées dans un environnement cloud et d’entreprise. Il fait gagner du temps, accroît la fiabilité des tâches d'administration récurrentes et planifie même leur exécution automatique à intervalles réguliers. Vous pouvez automatiser les processus à l'aide de Runbooks ou automatiser la gestion de configuration avec la Configuration de l'état souhaité (DSC, Desired State Configuration).

### <a name="backup"></a>Sauvegarde
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) est le service Azure qui vous permet de sauvegarder (ou de protéger) et de restaurer vos données dans le cloud Microsoft. Azure Backup remplace votre solution de sauvegarde locale ou hors site par une solution basée dans le cloud à la fois fiable, sécurisée et économique. Azure Backup propose plusieurs composants que vous pouvez télécharger et déployer sur l’ordinateur ou sur le serveur approprié, ou dans le cloud. Vous déployez un composant (ou un agent) en fonction de ce que vous souhaitez protéger. Vous pouvez utiliser tous les composants de Sauvegarde Azure (que vous protégiez des données en local ou dans le cloud) pour sauvegarder des données dans un coffre Recovery Services d’Azure. Consultez le [tableau des composants d’Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) assure la continuité d’activité en orchestrant la réplication des machines virtuelles et physiques locales vers Azure ou vers un site secondaire. Si votre site principal est inaccessible, vous effectuez un basculement vers l’emplacement secondaire afin que les utilisateurs puissent continuer à travailler, puis vous procédez à une restauration automatique lorsque le système redevient fonctionnel. Utilisez Azure Security Center pour effectuer une détection des menaces plus intelligente et efficace.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) est une solution Microsoft de type IDaaS (Identity as a Service) qui :

-   active IAM en tant que service cloud ;
-   assure la centralisation de la gestion de l’accès, l’authentification unique et le compte-rendu ;
-   prend en charge la gestion intégrée de l’accès à des [milliers d’applications](https://azure.microsoft.com/marketplace/active-directory/) dans la galerie d’applications, notamment à Salesforce, Google Apps, Box, Concur et bien d’autres encore.

Azure AD inclut également une suite complète de [fonctionnalités de gestion d’identité](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), comme l’[authentification multifacteur](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), l’[inscription d’appareil]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), la [gestion de mot de passe libre-service](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), la [gestion de groupes libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), la [gestion des comptes privilégiés](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), le [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is), la [surveillance de l’utilisation de l’application](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), ainsi que la [création d’audits complets](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), la [surveillance de la sécurité et la création d’alertes](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Avec Azure Active Directory, toutes les applications que vous publiez pour vos partenaires et clients (professionnels et particuliers) présentent des fonctionnalités identiques de gestion de l’identité et de l’accès. Vous êtes ainsi en mesure de réduire de manière significative vos coûts opérationnels.

## <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) vous aide à protéger les données de vos machines virtuelles dans Azure en vous offrant une visibilité sur les paramètres de sécurité de vos machines virtuelles et en surveillant les menaces. Security Center surveille les éléments suivants sur vos machines virtuelles :

-   Paramètres de sécurité du système d’exploitation avec les règles de configuration recommandées
-   Sécurité du système et mises à jour critiques manquantes
-   Recommandations de protection du point de terminaison
-   Validation du chiffrement de disque
-   Attaques réseau

Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) qui fournit des [rôles intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

>[!Note]
>Pour plus d’informations sur les rôles et les actions autorisées dans Security Center, consultez l’article [Permissions in Azure Security Center (Autorisations dans Azure Security Center)](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Security Center utilise Microsoft Monitoring Agent (le même agent que celui utilisé par Operations Management Suite et le service Log Analytics). Les données collectées à partir de cet agent sont stockées dans un [espace de travail](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) Log Analytics existant associé à votre abonnement Azure ou dans un nouvel espace de travail, en tenant compte de la zone géographique de la machine virtuelle.

## <a name="azure-monitor"></a>Azure Monitor
Les problèmes de performances dans votre application cloud peuvent affecter votre entreprise. Avec plusieurs composants interconnectés et de nouvelles versions fréquentes, des dégradations peuvent se produire à tout moment. Et si vous développez une application, vos utilisateurs découvrent généralement des problèmes que vous n’avez pas trouvés dans le test. Vous devez prendre connaissance de ces problèmes immédiatement et disposer d’outils pour diagnostiquer et résoudre les problèmes.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) est l’outil de base pour la surveillance des services s’exécutant sur Azure. Il vous fournit des données au niveau de l’infrastructure sur le débit d’un service et l’environnement. Si vous gérez toutes vos applications dans Azure et décidez d’augmenter ou de diminuer les ressources, Azure Monitor vous donne le nécessaire pour démarrer.

En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle. Il inclut :

-   Journaux d’activité
-   Journaux de diagnostic Azure
-   Mesures
-   Azure Diagnostics

### <a name="azure-activity-log"></a>Journaux d’activité
Ce journal fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Le [journal d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) était précédemment appelé « journal d’audit » ou « journal des opérations », car il indique les événements de plan de contrôle pour vos abonnements.

### <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure
Les [journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sont générés par une ressource et fournissent des informations riches et fréquentes sur le fonctionnement de cette ressource. Le contenu de ces journaux varie en fonction du type de ressource.

Par exemple, les journaux des événements système Windows sont une catégorie de journal de diagnostic pour les machines virtuelles et les objets blob, les tables, et les journaux de file d’attente sont les catégories de journaux de diagnostic pour les comptes de stockage.

Les journaux de diagnostic diffèrent du [journal d’activité (anciennement appelé journal d’audit ou journal des opérations)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource.

### <a name="metrics"></a>Mesures
Azure Monitor vous permet d’utiliser la télémétrie pour surveiller les performances et l’intégrité de vos charges de travail sur Azure. Les mesures (aussi appelées compteurs de performances) émises par la plupart des ressources Azure sont le type de données de télémétrie Azure plus important. Azure Monitor propose plusieurs façons de configurer et d’utiliser ces [mesures](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) pour l’analyse et le dépannage.

### <a name="azure-diagnostics"></a>Azure Diagnostics
Cette fonctionnalité Azure active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l'extension de diagnostic à partir de différentes sources. Les sources actuellement prises en charge sont les [rôles web et les rôles de travail Azure Cloud Services](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), les [machines virtuelles Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) sous Microsoft Windows et [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Les clients créent un réseau de bout en bout dans Azure en orchestrant et composant diverses ressources réseau telles qu’un réseau virtuel, ExpressRoute, Application Gateway, des équilibrages de charge, etc. La surveillance est disponible sur chacune des ressources réseau.

Le réseau de bout en bout peut avoir des configurations et des interactions entre les ressources complexes, générant des scénarios compliqués pour lesquels la surveillance basée sur des scénarios via Network Watcher est nécessaire.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) simplifie la surveillance et le diagnostic de votre réseau Azure. Les outils de diagnostic et de visualisation disponibles avec Network Watcher vous permettent de transférer les captures de paquets à distance sur une machine virtuelle Azure, d’obtenir des informations sur le trafic réseau via des journaux de flux et de diagnostiquer les passerelles et les connexions VPN.

Network Watcher propose actuellement les fonctionnalités suivantes :

- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) - Fournit une vue au niveau du réseau montrant les différentes interconnexions et associations entre les ressources réseau dans un groupe de ressources.
-   [Capture de paquets variables](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) - Capture des données de paquets dans et en dehors d’une machine virtuelle. Les options de filtrage avancées et les contrôles précis comme la possibilité de définir des limites de taille et de temps apportent de la polyvalence. Les données des paquets peuvent être stockées dans un magasin de blobs ou sur le disque local au format .cap.
-   [Vérification des flux IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - Vérifie si un paquet est autorisé ou refusé en fonction des paramètres de paquet des informations à 5 tuples de flux (adresse IP de destination, adresse IP source, port de destination, port source et protocole). Si le paquet est refusé par un groupe de sécurité, la règle et le groupe qui ont refusé le paquet sont renvoyés.
-   [Tronçon suivant](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) - Détermine le tronçon suivant pour les paquets routés dans la structure de réseau Azure, vous permettant de diagnostiquer les itinéraires définis par l’utilisateur mal configurés.
-   [Affichage des groupes de sécurité](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) - Obtient les règles de sécurité appliquées et effectives d’une machine virtuelle.
-   [Journalisation des flux de groupe de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) - Les journaux de flux pour les groupes de sécurité réseau vous permettent de capturer les journaux relatifs au trafic autorisé ou refusé par les règles de sécurité dans le groupe. Le flux est défini par des informations de tuple à 5 éléments : adresse IP source, adresse IP de destination, port source, port de destination et protocole.
-   [Résolution des problèmes de connexion et de passerelle de réseau virtuel](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) - Offre la possibilité de résoudre les problèmes associés aux connexions et passerelles de réseau virtuel.
-   [Limites d’abonnement réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) - Vous permet d’afficher l’utilisation des ressources réseau par rapport aux limites.
-   [Configuration du journal de diagnostic](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) - Fournit un seul volet pour activer ou désactiver les journaux de diagnostic pour les ressources réseau dans un groupe de ressources.

Pour en savoir plus sur la configuration de Network Watcher, consultez la rubrique [Configurer Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Opérations de développement (DevOps)
Avant le développement d’applications DevOps, des équipes sont chargées de rassembler les exigences professionnelles à remplir pour un programme logiciel et l’écriture de code. Ensuite, une équipe chargée de l’assurance qualité teste le programme dans un environnement de développement distinct et si les exigences sont remplies, elle publie le code de déploiement des opérations. Les équipes de déploiement sont ensuite réparties par domaine en plus petits groupes, comme la mise en réseau et les bases de données par exemple. Chaque fois qu’un logiciel est « balancé » à une équipe indépendante, cela ajoute des goulots d’étranglement.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) permet aux équipes de fournir des solutions plus sécurisées et de meilleure qualité, de manière plus rapide et économique. Les clients s’attendent à une expérience dynamique et fiable lors de l’utilisation de logiciels et de services.  Les équipes doivent rapidement itérer les mises à jour logicielles, en mesurer l’impact et réagir rapidement en développant de nouvelles itérations pour résoudre les problèmes ou améliorer la valeur du logiciel.  Les plateformes cloud comme Microsoft Azure ont supprimé les goulots d’étranglement traditionnels et aidé à faire de l’infrastructure une marchandise. Dans tous les secteurs, ce sont les logiciels qui font la différence dans les résultats des entreprises. Aucune organisation, aucun développeur ni aucun professionnel de l’informatique ne peut se permettre d’ignorer le mouvement DevOps.

Les professionnels DevOps expérimentés adoptent plusieurs des pratiques suivantes. Ces pratiques [impliquent de faire appel à des collaborateurs](https://www.visualstudio.com/learn/what-is-devops-culture/) pour concevoir des stratégies basées sur les scénarios spécifiques de l’entreprise.  Certains outils peuvent appuyer l’automatisation de ces différentes pratiques :

-   Des techniques de [planification et gestion de projets agiles](https://www.visualstudio.com/learn/what-is-agile/) sont utilisées pour planifier et répartir le travail en sprints, pour gérer la capacité de l’équipe et pour aider les équipes à s’adapter rapidement à l’évolution des besoins de l’entreprise.
-   Le [contrôle de version, généralement avec Git](https://www.visualstudio.com/learn/what-is-git/), permet à des équipes situées n’importe où dans le monde de partager la source et d’effectuer des intégrations avec des outils de développement logiciel pour automatiser le pipeline de mise en production.
-   [L’intégration continue ](https://www.visualstudio.com/learn/what-is-continuous-integration/) stimule la fusion et le test de code en continu, ce qui permet de trouver les erreurs plut tôt.  Les autres avantages sont notamment un gain de temps sur la résolution des problèmes de fusion et la rapidité des retours pour les équipes de développement.
-   La [livraison continue](https://www.visualstudio.com/learn/what-is-continuous-delivery/) des solutions logicielles dans les environnements de production et de test permet aux organisations de corriger rapidement les bogues et de répondre aux exigences de l’entreprise en constante évolution.
-   La [surveillance](https://www.visualstudio.com/learn/what-is-monitoring/) des applications en cours d’exécution, y compris les environnements de production pour contrôler l’intégrité de l’application et l’utilisation du client, aide les organisations à élaborer une hypothèse et à valider ou refuser rapidement des stratégies.  Les données enrichies sont capturées et stockées dans divers formats d’enregistrement.
-   [L’infrastructure en tant que code (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) est une pratique qui permet l’automatisation et la validation de la création et de la destruction des réseaux et des machines virtuelles pour permettre de proposer des plateformes d’hébergement d’applications stables et sûres.
-   L’architecture de [microservices](https://www.visualstudio.com/learn/what-are-microservices/) est utilisée pour isoler des exemples d’utilisation métier en petits services réutilisables.  Cette architecture est synonyme d’extensibilité et d’efficacité.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la solution de sécurité et d’audit d’OMS, consultez les articles suivants :

- [Operations Management Suite | Security &amp; Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Surveiller et répondre aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).
- [Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources).
