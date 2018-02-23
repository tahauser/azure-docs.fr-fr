---
title: "Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP"
description: "Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 9b605e500925e8435b15ec8055f8d8f376888aaf
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP

## <a name="overview"></a>Vue d'ensemble

[FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov) est un programme déployé à l’échelle de l’administration américaine, visant à rationaliser l’approche en matière d’évaluation de la sécurité, d’autorisation et de surveillance continue des services et produits cloud. Cette solution Azure Blueprint Security & Compliance fournit des conseils pour le déploiement d’un environnement IaaS (infrastructure as a service) conforme à FedRAMP adapté à une application web simple accessible sur Internet. Cette solution automatise le déploiement et la configuration des ressources Azure pour une architecture de référence commune, illustrant diverses façons dont les clients peuvent satisfaire à des exigences de conformité et de sécurité spécifiques, et sert de base aux clients souhaitant générer et configurer leurs propres solutions sur Azure. La solution implémente un sous-ensemble des contrôles définis dans le document FedRAMP High Baseline, basé sur la publication NIST SP 800-53. Pour plus d’informations sur les exigences FedRAMP High et sur cette solution, consultez [Exigences FedRAMP High - Vue d’ensemble](fedramp-controls-overview.md). ***Remarque : Cette solution se déploie sur Azure Government.***

Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production. Le déploiement d’une application dans cet environnement, sans y apporter de modifications, ne permet pas de répondre entièrement aux exigences du document FedRAMP High Baseline. Notez les points suivants :
- Cette architecture constitue une base de référence qui permet aux clients d’utiliser Azure conformément aux exigences FedRAMP.
- Les clients doivent réaliser des évaluations de sécurité et de conformité pour toute solution créée à l’aide de cette architecture, car les exigences peuvent varier selon leur implémentation. 

Pour obtenir une présentation rapide du fonctionnement de cette solution, regardez cette [vidéo](https://aka.ms/fedrampblueprintvideo) qui explique son déploiement.

Cliquez [ici](https://aka.ms/fedrampblueprintrepo) pour obtenir des instructions de déploiement.

## <a name="solution-components"></a>Composants de la solution

Cette solution Azure Blueprint Security & Compliance déploie automatiquement une architecture de référence d’application web IaaS avec des contrôles de sécurité préconfigurés pour aider les clients à se conformer aux exigences FedRAMP. La solution se compose de modèles Azure Resource Manager et de scripts PowerShell qui guident le déploiement et la configuration des ressources. Une [documentation sur la conformité](#compliance-documentation) est également fournie, indiquant l’héritage des contrôles de sécurité d’Azure, ainsi que les ressources et configurations déployées qui s’alignent sur les contrôles de sécurité NIST SP 800-53, ce qui permet aux organisations de se conformer rapidement aux obligations.

## <a name="architecture-diagram"></a>Diagramme de l’architecture

Cette solution déploie une architecture de référence pour une application web IaaS avec un backend de base de données. L’architecture inclut une couche web, une couche données, une infrastructure Active Directory, une passerelle d’application et un équilibreur de charge. Les machines virtuelles déployées sur les couches web et données sont configurées dans un groupe à haute disponibilité, tandis que les instances de SQL Server sont configurées dans un groupe de disponibilité AlwaysOn pour une haute disponibilité. Les machines virtuelles sont jointes au domaine, et des stratégies de groupe Active Directory sont utilisées pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation. Un serveur de rebond de gestion (bastion) fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées.

![alt text](images/fedramp-architectural-diagram.png?raw=true "Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

* **Azure Virtual Machines**
    - (1) Gestion/bastion (Windows Server 2016 Datacenter)
    - (2) Contrôleur de domaine Active Directory (Windows Server 2016 Datacenter)
    - (2) Nœud de cluster SQL Server (SQL Server 2016 sur Windows Server 2012 R2)
    - (1) Témoin SQL Server (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **Groupes à haute disponibilité**
    - (1) Contrôleurs de domaine Active Directory
    - (1) Nœuds de cluster SQL et témoin
    - (1) Web/IIS
* **Azure Virtual Network**
    - (1) /16 réseaux virtuels
    - (5) /24 sous-réseaux
    - Les paramètres DNS sont définis sur les deux contrôleurs de domaine
* **Azure Load Balancer**
    - (1) Équilibreur de charge SQL
* **Application Gateway Azure**
    - (1) WAF Application Gateway activé
      - Mode de pare-feu : prévention
      - Ensemble de règles : OWASP 3.0
      - Écouteur : port 443
* **Stockage Azure**
    - (7) Comptes de stockage géoredondant
* **Azure Backup**
    - (1) Coffre Recovery Services
* **Azure Key Vault**
    - (1) Key Vault
* **Azure Active Directory**
* **Azure Resource Manager**
* **Azure Log Analytics**
* **Azure Automation**
    - (1) Compte Automation
* **Operations Management Suite**
    - (1) Espace de travail OMS

## <a name="deployment-architecture"></a>Architecture de déploiement

La section suivante décrit en détail les éléments de développement et d’implémentation.

### <a name="network-segmentation-and-security"></a>Segmentation et sécurité du réseau

#### <a name="application-gateway"></a>Application Gateway

L’architecture réduit le risque de failles de sécurité à l’aide d’Application Gateway et de son pare-feu d’applications web (WAF), dans lequel est activé l’ensemble de règles OWASP. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’application web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode WAF)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0

#### <a name="virtual-network"></a>Réseau virtuel

L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

#### <a name="network-security-groups"></a>Groupes de sécurité réseau

Cette solution déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion à l’intérieur d’un réseau virtuel. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire pour les fonctionnalités système et de gestion.

Consultez la configuration des [groupes de sécurité réseau](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) déployés avec cette solution. Les organisations peuvent configurer des groupes de sécurité réseau en modifiant le fichier ci-dessus et en suivant [cette documentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

Chaque sous-réseau dispose d’un groupe de sécurité réseau (NSG) dédié :
- 1 NSG pour la passerelle d’application (LBNSG)
- 1 NSG pour le serveur de rebond (MGTNSG)
- 1 NSG pour les contrôleurs de domaine principaux et secondaires (ADNSG)
- 1 NSG pour les serveurs SQL et le témoin de partage de fichiers (SQLNSG)
- 1 NSG pour la couche web (WEBNSG)

#### <a name="subnets"></a>Sous-réseaux

Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide de plusieurs mesures de chiffrement.

#### <a name="azure-storage"></a>Stockage Azure

Pour répondre aux exigences du chiffrement des données au repos, l’ensemble des comptes de stockage utilisent le [chiffrement du service de stockage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>Base de données SQL

SQL Database est configuré pour utiliser [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), qui effectue le chiffrement et le déchiffrement en temps réel des données et fichiers journaux pour protéger les informations au repos. TDE protège les données des accès non autorisés. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption permet de chiffrer les disques de machines virtuelles IaaS Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) exploite la fonctionnalité BitLocker de Windows pour fournir le chiffrement des volumes des disques de données et du système d’exploitation. La solution est intégrée à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

### <a name="logging-and-auditing"></a>Journalisation et audit

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fournit une journalisation complète de l’activité système et utilisateur, ainsi que de l’intégrité du système. 

- **Journaux d’activité :** les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement.
- **Journaux de diagnostic :** les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) correspondent à l’ensemble des journaux émis par chaque ressource. Ces journaux incluent les journaux système des événements Windows, les journaux de stockage Azure, les journaux d’audit Key Vault, ainsi que les journaux de pare-feu et d’accès Application Gateway.
- **Archivage des journaux :** les journaux de diagnostic et les journaux d’activité Azure peuvent être connectés à Azure Log Analytics en vue d’opérations de traitement, de stockage et de tableau de bord. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifique de l’organisation.

### <a name="secrets-management"></a>Gestion des secrets

La solution utilise Azure Key Vault pour gérer les clés et les secrets.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. 
- La solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque de machine virtuelle IaaS.

### <a name="identity-management"></a>Gestion des identités

Les technologies suivantes fournissent des fonctionnalités de gestion des identités dans l’environnement Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) est le service Microsoft de gestion des annuaires et des identités, basé sur le cloud, multilocataire.
- L’authentification auprès d’une application web déployée par le client peut être effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet une gestion précise de l’accès pour Azure. L’accès aux abonnements est limité à l’administrateur des abonnements, tandis que l’accès aux ressources peut être limité en fonction du rôle d’utilisateur.
- Une instance Active Directory IaaS déployée fournit la gestion des identités au niveau du système d’exploitation pour les machines virtuelles IaaS déployées.
   
### <a name="compute-resources"></a>Ressources de calcul

#### <a name="web-tier"></a>Couche web

La solution déploie les machines virtuelles de la couche web sur un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Les groupes à haute disponibilité veillent à ce que les machines virtuelles soient distribuées sur plusieurs clusters matériels isolés pour améliorer la disponibilité.

#### <a name="database-tier"></a>Couche base de données

La solution déploie les machines virtuelles de la couche base de données sur un groupe à haute disponibilité en tant que [groupe de disponibilité AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). La fonctionnalité de groupe de disponibilité Always On fournit des fonctionnalités de haute disponibilité et de récupération d’urgence. 

#### <a name="active-directory"></a>Active Directory

Toutes les machines virtuelles déployées par la solution sont jointes au domaine, et des stratégies de groupe Active Directory sont utilisées pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation. Les machines virtuelles Active Directory sont déployées dans un groupe à haute disponibilité.


#### <a name="jumpbox-bastion-host"></a>Serveur de rebond (bastion)

Un serveur de rebond de gestion (bastion) fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. Le groupe de sécurité réseau associé au sous-réseau de gestion où se trouve la machine virtuelle du serveur de rebond n’autorise les connexions que sur le port TCP 3389 pour RDP. 

### <a name="malware-protection"></a>Protection contre les programmes malveillants

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

### <a name="patch-management"></a>Gestion des correctifs

Les machines virtuelles Windows déployées par cette solution Azure Security and Compliance Blueprint Automation sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution déploie également la solution OMS Azure Automation par le biais de laquelle des déploiements de mise à jour peuvent être créés pour déployer les correctifs sur les serveurs Windows si nécessaire.

### <a name="operations-management"></a>Gestion des opérations

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) est un service d’Operations Management Suite (OMS) qui permet de collecter et d’analyser les données générées par les ressources des environnements Azure et locaux.

#### <a name="oms-solutions"></a>Solutions OMS

Les solutions OMS suivantes sont préinstallées dans le cadre de cette solution :
- [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Analyse anti-programme malveillant](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Gestion des mises à jour](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Suivi des modifications](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Documentation sur la conformité

### <a name="customer-responsibility-matrix"></a>Matrice de responsabilités des clients

La [matrice de responsabilités des clients](https://aka.ms/blueprinthighcrm) (classeur Excel) répertorie tous les contrôles de sécurité requis par le document FedRAMP High Baseline. La matrice indique, pour chaque contrôle (ou sous-partie de contrôle), si son implémentation revient à Microsoft, au client ou aux deux. 

### <a name="control-implementation-matrix"></a>Matrice de mise en œuvre des contrôles

La [matrice de mise en œuvre des contrôles](https://aka.ms/blueprintwacim) (classeur Excel) répertorie tous les contrôles de sécurité requis par le document FedRAMP High Baseline. La matrice indique, pour chaque contrôle (ou sous-partie de contrôle) auquel est associée une responsabilité du client dans la matrice de responsabilités des clients, (1) si la solution Blueprint Automation implémente le contrôle et (2) une description de l’alignement de l’implémentation sur les exigences de contrôle. Ce contenu est également disponible [ici](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Déployer la solution

Cette solution Azure Security and Compliance Blueprint Automation se compose de fichiers de configuration JSON et de scripts PowerShell qui sont gérés par le service d’API d’Azure Resource Manager pour déployer des ressources dans Azure. Des instructions détaillées sur le déploiement sont disponibles [ici](https://aka.ms/fedrampblueprintrepo). ***Remarque : Cette solution se déploie sur Azure Government.***

#### <a name="quickstart"></a>Démarrage rapide
1. Clonez ou téléchargez [ce dépôt GitHub](https://aka.ms/fedrampblueprintrepo) sur votre station de travail locale.

2. Exécutez le script PowerShell de prédéploiement : azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Cliquez sur le bouton ci-dessous, connectez-vous au portail Azure, entrez les paramètres de modèle ARM requis, puis cliquez sur **Acheter**.

    [![Déploiement sur Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

- Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.  
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.  
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.  
- Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.  
- Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
- Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
