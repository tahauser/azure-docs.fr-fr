---
title: "Plan de traitement des paiements pour les environnements conformes à la norme PCI DSS"
description: Condition de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 7f85c8b0377e57f08044bac41dbddbbedb7a4f55
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-blueprint-automation-payment-processing-for-pci-dss-compliant-environments"></a>Azure Blueprint Automation : traitement des paiements pour les environnements conformes à la norme PCI DSS

## <a name="overview"></a>Vue d'ensemble

Le traitement des paiements pour les environnements conformes à la norme PCI DSS fournit des directives sur le déploiement d’un environnement PaaS conforme à la norme PCI DSS et adapté à la gestion des données sensibles de carte de paiement. Il présente une architecture de référence commune et est conçu pour simplifier l’adoption de Microsoft Azure. Ce plan est un exemple de solution de bout en bout qui répond aux besoins des entreprises souhaitant une approche basée sur le cloud visant à réduire la charge et les coûts liés aux déploiements.

Ce plan est conçu pour aider à satisfaire aux exigences strictes de la norme PCI DSS 3.2 concernant la collecte, le stockage et la récupération des données de carte de paiement. Il montre comment gérer les données de carte de paiement de manière adaptée (dont le numéro de carte, la date d’expiration et les données de vérification) dans un environnement à plusieurs niveaux sécurisé et conforme, et déployé en tant que solution PaaS Azure de bout en bout. Pour plus d’informations sur les exigences de la norme PCI DSS 3.2 et de cette solution, consultez [Exigences de la normes PCI DSS - Vue d’ensemble](pci-dss-requirements-overview.md).

Conçu simplement pour permettre aux clients de mieux comprendre les exigences spécifiques, ce plan ne doit pas être utilisé tel quel dans un environnement de production. Le déploiement d’une application dans cet environnement, sans y apporter de modifications, ne permet pas de répondre entièrement aux exigences d’une solution conforme à la norme PCI DSS pour une solution personnalisée. Notez les points suivants :
- Ce plan constitue une base de référence qui permet aux clients d’utiliser Microsoft Azure conformément à la norme PCI DSS.
- La certification de conformité PCI DSS d’une solution cliente de production est délivrée par un évaluateur de sécurité qualifié (QSA) agréé.
- Les clients doivent réaliser des évaluations de sécurité et de conformité pour toute solution créée à l’aide de cette architecture de base, car les exigences peuvent varier selon leur implémentation et leur zone géographique.  

Pour obtenir une présentation rapide du fonctionnement de cette solution, regardez cette [vidéo](https://aka.ms/pciblueprintvideo) qui explique son déploiement.

## <a name="solution-components"></a>Composants de la solution

L’architecture de base est constituée des éléments suivants :

- **Diagramme architectural**. Le diagramme illustre l’architecture de référence utilisée pour la solution Contoso Webstore.
- **Modèles de déploiement**. Dans ce déploiement, des [modèles Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) sont utilisés pour déployer automatiquement les composants de l’architecture dans Microsoft Azure, en spécifiant des paramètres de configuration pendant l’installation.
- **Scripts de déploiement automatisé**. Ces scripts permettent de déployer la solution de bout en bout. Les scripts sont constitués des éléments suivants :
    - Un script de configuration de l’installation des modules et de configuration de [l’administrateur général](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) est utilisé pour installer et vérifier que les modules PowerShell et rôles d’administrateur général nécessaires sont configurés correctement.
    - Un script PowerShell d’installation est utilisé pour déployer la solution de bout en bout, qui est fournie sous la forme d’un fichier .zip et d’un fichier .bacpac contenant une application web de démonstration prête à l’emploi, avec un [exemple de base de données SQL](https://github.com/Microsoft/azure-sql-security-sample). content. Le code source de cette solution est disponible dans le [dépôt du code du plan de traitement des paiements][code-repo]. 

## <a name="architectural-diagram"></a>Diagramme architectural

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Scénario utilisateur

Le plan aborde le cas d’usage suivant.

> Ce scénario traite d’un webstore fictif qui a choisi de passer à une solution PaaS Azure pour le traitement des cartes de paiement. La solution gère la collecte des informations utilisateur de base, y compris les données de paiement. La solution ne traite pas les paiements avec ces données de carte. Une fois les données collectées, les clients sont chargés d’effectuer les transactions à l’aide d’un processeur de paiement. Pour plus d’informations, consultez [Review and Guidance for Implementation](https://aka.ms/pciblueprintprocessingoverview)\(Examen et conseils pour l’implémentation).

### <a name="use-case"></a>Cas d’utilisation
Un petit webstore appelé *Contoso Webstore* est prêt à passer au cloud pour son système de paiement. Il a choisi Microsoft Azure pour héberger le processus d’achat et pour autoriser un comptable à collecter les paiements par carte de crédit de ses clients.

L’administrateur recherche une solution qui peut être rapidement déployée pour atteindre ses objectifs avec une solution cloud. Il va utiliser cette preuve de concept (POC) pour décider, avec les parties prenantes, de la manière dont Azure peut leur servir à collecter, stocker et récupérer des données de carte de paiement, en vue de se conformer aux exigences strictes de la norme PCI DSS.

> Vous devrez réaliser des évaluations de sécurité et de conformité pour toute solution créée à l’aide de cette architecture et utilisée par cette preuve de concept, car les exigences peuvent varier selon votre implémentation et votre zone géographique. La norme PCI DSS nécessite que vous travailliez directement avec un évaluateur de sécurité qualifié (QSA) agréé afin de certifier votre solution prête pour la production.

### <a name="elements-of-the-foundational-architecture"></a>Éléments de l’architecture de base

L’architecture de base a été conçue avec les éléments fictifs suivants :

Le site de domaine `contosowebstore.com`

Des rôles d’utilisateur pour illustrer le cas d’usage et donner une idée de l’interface utilisateur

#### <a name="role-site-and-subscription-admin"></a>Rôle : Administrateur du site et des abonnements

|Item      |Exemple|
|----------|------|
|Nom d’utilisateur : |`adminXX@contosowebstore.com`|
| Nom : |`Global Admin Azure PCI Samples`|
|Type d’utilisateur :| `Subscription Administrator and Azure Active Directory Global Administrator`|

- Le compte administrateur ne peut pas lire les informations de carte de paiement non masquées. Toutes les actions sont enregistrées.
- Le compte administrateur ne peut pas se connecter à SQL Database ni le gérer.
- Le compte administrateur peut gérer Active Directory et les abonnements.

#### <a name="role-sql-administrator"></a>Rôle : Administrateur SQL

|Item      |Exemple|
|----------|------|
|Nom d’utilisateur : |`sqlAdmin@contosowebstore.com`|
| Nom : |`SQLADAdministrator PCI Samples`|
| Prénom : |`SQL AD Administrator`|
|Nom : |`PCI Samples`|
|Type d’utilisateur :| `Administrator`|

- Le compte sqladmin ne peut pas afficher les informations de carte de paiement non filtrées. Toutes les actions sont enregistrées.
- Le compte sqladmin peut gérer la base de données SQL.

#### <a name="role-clerk"></a>Rôle : Comptable

|Item      |Exemple|
|----------|------|
|Nom d’utilisateur :| `receptionist_EdnaB@contosowebstore.com`|
| Nom : |`Edna Benson`|
| Prénom :| `Edna`|
|Nom :| `Benson`|
| Type d’utilisateur : |`Member`|

Edna Benson est réceptionniste et directrice commerciale. Elle est chargée de vérifier que les informations clients sont correctes et que la facturation a bien été effectuée. Edna est l’utilisateur connecté pour toutes les interactions avec le site web de démonstration Contoso Webstore. Edna dispose des autorisations suivantes : 

- Elle peut créer et lire les informations clients
- Elle peut modifier les informations client
- Elle peut modifier le numéro de carte de paiement, la date d’expiration et le code CVV.

> Dans Contoso Webstore, l’utilisateur est connecté automatiquement en tant que l’utilisateur **Edna** pour tester les fonctionnalités de l’environnement déployé.

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - Estimation de la tarification

Cette architecture de base et cet exemple d’application web ont un coût mensuel et un coût d’utilisation horaire qui doivent être pris en compte lors du dimensionnement de la solution. Ces coûts peuvent être estimés à l’aide de la [calculatrice de coûts Azure](https://azure.microsoft.com/pricing/calculator/). À compter de septembre 2017, le coût mensuel estimé de cette solution est de 2 500 $, dont 1 000 $ de frais d’utilisation pour ASE v2. Ces coûts varient en fonction de la quantité utilisée et sont susceptibles de changer. Il revient au client de calculer les coûts mensuels estimés au moment du déploiement, afin d’obtenir une estimation plus précise. 

Cette solution a utilisé les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

>- Application Gateway
>- Azure Active Directory
>- App Service Environment v2
>- OMS Log Analytics
>- Azure Key Vault
>- Groupes de sécurité réseau
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Application web Azure
>- Azure Automation
>- Runbooks Azure Automation
>- DNS Azure
>- Réseau virtuel Azure
>- Machine virtuelle Azure
>- Groupe de ressources et stratégies Azure
>- Stockage Blob Azure
>- Contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory

## <a name="deployment-architecture"></a>Architecture de déploiement

La section suivante décrit en détail les éléments de développement et d’implémentation.

### <a name="network-segmentation-and-security"></a>Segmentation et sécurité du réseau

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

L’architecture de base réduit le risque de failles de sécurité à l’aide d’Application Gateway et de son pare-feu d’applications web (WAF), dans lequel est activé l’ensemble de règles OWASP. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’application web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (mode WAF)
- [Mode Prévention](/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

#### <a name="virtual-network"></a>Réseau virtuel

L’architecture de base définit un réseau privé virtuel avec l’espace d’adressage 10.0.0.0/16.

#### <a name="network-security-groups"></a>Groupes de sécurité réseau

Chaque niveau du réseau dispose d’un groupe de sécurité réseau (NSG) dédié :
- Un groupe de sécurité réseau DMZ pour le pare-feu et le WAF Application Gateway
- Un groupe de sécurité réseau pour le serveur de rebond de gestion (hôte bastion)
- Un groupe de sécurité réseau pour l’environnement App Service

Chaque groupe de sécurité réseau a ses propres ports et protocoles ouverts pour une utilisation sécurisée et correcte de la solution. Pour plus d’informations, consultez [Guide PCI - Groupes de sécurité réseau](#network-security-groups).

Chaque groupe de sécurité réseau a ses propres ports et protocoles ouverts pour une utilisation sécurisée et correcte de la solution. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
- Activation du stockage des [journaux de diagnostic et des événements](/azure/virtual-network/virtual-network-nsg-manage-log) dans le compte de stockage 
- Connexion d’OMS Log Analytics aux [diagnostics du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

#### <a name="subnets"></a>Sous-réseaux
 Vérifiez que chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

#### <a name="custom-domain-ssl-certificates"></a>Certificats SSL de domaine personnalisé
 Le trafic HTTPS est activé à l’aide d’un certificat SSL de domaine personnalisé.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide du chiffrement, de l’audit des base de données et d’autres mesures.

#### <a name="azure-storage"></a>Stockage Azure

Pour répondre aux exigences du chiffrement des données au repos, l’ensemble du [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise le [chiffrement du service de stockage](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Azure SQL Database

L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :

- [Authentification et autorisation AD](/azure/sql-database/sql-database-aad-authentication)
- [Audit de base de données SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Chiffrement transparent des données](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Règles de pare-feu](/azure/sql-database/sql-database-firewall-configure), permettant la gestion des pools de workers ASE et des IP clients
- [Détection des menaces SQL](/azure/sql-database/sql-database-threat-detection-get-started)
- [Colonnes Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [SQL Database Dynamic Data Masking](/azure/sql-database/sql-database-dynamic-data-masking-get-started), à l’aide du script PowerShell post-déploiement

### <a name="logging-and-auditing"></a>Journalisation et audit

[Operations Management Suite (OMS)](/azure/operations-management-suite/) peut fournir à Contoso Webstore des fonctionnalités avancées de journalisation pour toutes les activités système et utilisateur, y compris la journalisation des données relatives aux titulaires des cartes. Vous pouvez passer en revue les changements et vérifier leur exactitude. 

- **Journaux d’activité :** les [journaux d’activité](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement.
- **Journaux de diagnostic :** les [journaux de diagnostic](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) correspondent à l’ensemble des journaux émis par chaque ressource. Ces journaux incluent les journaux des événements système Windows, ainsi que les journaux du Stockage Blob Azure, des tables et des files d’attente.
- **Journaux du pare-feu** : Application Gateway fournit des journaux de diagnostic et d’accès complets. Les journaux de pare-feu sont disponibles pour les ressources Application Gateway pour lesquelles WAF est activé.
- **Archivage des journaux :** tous les journaux de diagnostic sont configurés pour être enregistrés dans un compte de stockage Azure centralisé et chiffré pour archivage, avec une période de rétention définie (2 jours). Les journaux sont ensuite connectés à Azure Log Analytics en vue d’être traités, stockés et insérés dans un tableau de bord. [Log Analytics](https://azure.microsoft.com/services/log-analytics) est un service OMS qui vous permet de collecter et d’analyser les données générées par les ressources de vos environnements locaux et cloud.

### <a name="encryption-and-secrets-management"></a>Chiffrement et gestion des secrets

Contoso Webstore chiffre toutes les données de carte de paiement et utilise Azure Key Vault pour gérer les clés, ce qui empêche la récupération des données relatives aux titulaires de cartes.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) est utilisé pour chiffrer toutes les données relatives au titulaire de la carte, ainsi que la date d’expiration et le code CVV.
- Les données sont stockées sur disque à l’aide [d’Azure Disk Encryption](/azure/security/azure-security-disk-encryption) et de BitLocker.

### <a name="identity-management"></a>Gestion des identités

Les technologies suivantes fournissent des fonctionnalités de gestion des identités dans l’environnement Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) est le service cloud et multilocataire de gestion des annuaires et des identités, proposé par Microsoft. Tous les utilisateurs de la solution ont été créés dans Azure Active Directory, y compris ceux qui accèdent à la base de données SQL.
- L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). En outre, le chiffrement des colonnes de base de données utilise également Azure AD pour authentifier l’application auprès de la base de données SQL Azure. Pour plus d’informations, consultez [Always Encrypted : Protéger les données sensibles dans SQL Database](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités pouvant affecter les identités de votre organisation et configure les réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation. Enfin, il examine les incidents suspects et prend les mesures nécessaires pour les résoudre.
- Le [contrôle d’accès en fonction du rôle (RBAC) Azure](/azure/active-directory/role-based-access-control-configure) permet une gestion précise de l’accès pour Azure. L’accès à l’abonnement est limité à l’administrateur des abonnements, tandis que l’accès à Azure Key Vault est interdit à tous les utilisateurs.

Pour plus d’informations sur l’utilisation des fonctionnalités de sécurité d’Azure SQL Database, consultez l’exemple [Contoso Clinic demo application](https://github.com/Microsoft/azure-sql-security-sample) (Application de démonstration Contoso Clinic).
   
### <a name="web-and-compute-resources"></a>Ressources web et ressources de calcul

#### <a name="app-service-environment"></a>Environnement App Service

[Azure App Service](/azure/app-service/) est un service managé pour le déploiement des applications web. L’application Contoso Webstore est déployée comme une [application web App Service](/azure/app-service-web/app-service-web-overview).

[Azure App Service Environment(ASE v2)](/azure/app-service/app-service-environment/intro) est une fonctionnalité d’Azure App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications App Service à grande échelle. Il s’agit d’un plan de service Premium qui est utilisé par cette architecture de base pour permettre la conformité à la norme PCI DSS.

Les environnements ASE sont isolés de façon à exécuter les applications d’un seul client, et sont toujours déployés dans un réseau virtuel. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications, et les applications peuvent établir des connexions sécurisées à haut débit sur les réseaux virtuels avec les ressources d’entreprise locales.

L’utilisation des environnements ASE pour cette architecture permet les contrôles et configurations suivants :

- Hébergement dans un réseau virtuel et règles de sécurité réseau
- Configuration des environnements ASE avec un certificat ILB auto-signé pour la communication HTTPS
- [Mode Équilibrage de charge interne](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (mode 3)
- Désactivation de [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings), protocole TLS déprécié selon la norme PCI DSS
- Modification du [chiffrement TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Contrôle des [ports réseau de trafic entrant](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF – Restreindre les données](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Autorisation du [trafic SQL Database](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Serveur de rebond (hôte bastion)

Étant donné que l’environnement App Service est sécurisé et verrouillé, il est nécessaire d’avoir un mécanisme qui permet d’autoriser toutes les versions de DevOps et toute modification nécessaire, telle que la capacité à surveiller l’application web à l’aide de Kudu. La machine virtuelle est située derrière l’équilibreur de charge NAT, ce qui permet de se connecter à la machine virtuelle par un port autre que TCP 3389. 

Une machine virtuelle a été créée en tant que serveur de rebond (hôte bastion) avec les configurations suivantes :

-   [Extension Antimalware](/azure/security/azure-security-antimalware)
-   [Extension OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extension Diagnostics Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) avec Azure Key Vault (conforme à Azure Government, à la norme PCI DSS, à la loi américaine HIPAA et à d’autres exigences).
-   [Stratégie d’arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources des machines virtuelles non utilisées.

### <a name="security-and-malware-protection"></a>Sécurité et protection contre les programmes malveillants

[Azure Security Center](https://azure.microsoft.com/services/security-center/) vous offre une vue centralisée de l’état de sécurité de toutes vos ressources Azure. Vous pouvez vérifier d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et vous pouvez identifier rapidement les ressources nécessitant votre attention.  

[Azure Advisor](/azure/advisor/advisor-overview) est un conseiller personnalisé basé dans le cloud qui décrit les bonnes pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) pour Azure Cloud Services et Machines virtuelles est une fonctionnalité de protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant lorsque des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur vos systèmes Azure.

### <a name="operations-management"></a>Gestion des opérations

#### <a name="application-insights"></a>Application Insights

Utilisez [Application Insights](https://azure.microsoft.com/services/application-insights/) pour obtenir des insights actionnables grâce à la gestion des performances d’applications et à l’analytique instantanée.

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) est un service d’Operations Management Suite (OMS) qui vous permet de collecter et d’analyser les données générées par les ressources de votre cloud et de vos environnements locaux.

#### <a name="oms-solutions"></a>Solutions OMS

Ces solutions OMS supplémentaires doivent être envisagées et configurées :
- [Activity Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure Networking Analytics](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Change Tracking](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Key Vault Analytics](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Service Map](/azure/operations-management-suite/operations-management-suite-service-map)
- [Security and Audit](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Antimalware](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Update Management](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Intégration Security Center

Le déploiement par défaut est destiné à fournir des recommandations Security Center, indiquant un état de configuration sain et sécurisé. Vous pouvez activer la collecte des données dans Azure Security Center. Pour plus d’informations, consultez l’article [Azure Security Center -Bien démarrer](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Déployer la solution

Les composants qui permettent de déployer cette solution sont disponibles dans le [dépôt de code du plan PCI][code-repo]. Pour déployer l’architecture de base, vous devez effectuer plusieurs étapes à l’aide de Microsoft PowerShell v5. Pour vous connecter au site web, vous devez fournir un nom de domaine personnalisé (par exemple, contoso.com). Vous pouvez le spécifier à l’aide du commutateur `-customHostName` de l’étape 2. Pour plus d’informations, consultez [Acheter un nom de domaine personnalisé pour Azure Web Apps](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Vous n’avez pas besoin d’un nom de domaine personnalisé pour déployer et exécuter la solution. Toutefois, sans ce nom, vous ne pourrez pas vous connecter au site web à des fins de démonstration.

Les scripts ajoutent des utilisateurs de domaine au locataire Azure AD que vous spécifiez. Nous vous recommandons de créer un locataire Azure AD que vous utiliserez à des fins de test.

Si vous rencontrez des problèmes pendant le déploiement, consultez [FAQ and troubleshooting](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

Il est fortement recommandé d’utiliser une nouvelle installation de PowerShell pour déployer la solution. Vous pouvez également vérifier que vous utilisez les modules les plus récents pour la bonne exécution des scripts d’installation. Dans cet exemple, nous nous connectons au serveur de rebond (hôte bastion) et nous exécutons les commandes suivantes. Notez que cela active la commande de domaine personnalisé.


1. Installez les modules nécessaires et configurez les rôles d’administrateur.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Pour obtenir des instructions d’utilisation détaillées, consultez [Script Instructions - Setup Administrative Account and Permission](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Installer solution-update-management 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Pour obtenir des instructions d’utilisation détaillées, consultez [Script Instructions - Deploy and Configure Azure Resources](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Journalisation et surveillance OMS Une fois la solution déployée, un espace de travail [Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) peut être ouvert, et les exemples de modèles fournis dans le référentiel de la solution peuvent être utilisés pour montrer comment configurer un tableau de bord de surveillance. Les exemples de modèles OMS se trouvent dans le [dossier omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Notez que les données doivent être collectées dans OMS pour que les modèles soient déployés correctement. Cette opération peut prendre une heure ou plus, suivant l’activité sur le site.
 
    Lorsque vous configurez la journalisation OMS, vous pouvez inclure les ressources suivantes :
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Modèle de menace

Diagramme de flux de données et exemple de modèle de menace pour le [modèle de menace du plan de traitement des paiements](https://aka.ms/pciblueprintthreatmodel) Contoso Webstore.

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matrice de responsabilités des clients

Les clients doivent conserver une copie de la [Matrice de responsabilités](https://aka.ms/pciblueprintcrm32), dans laquelle sont répertoriées les conditions de la norme PCI DSS qui sont sous la responsabilité du client, ainsi que celles qui sont sous la responsabilité de Microsoft Azure.

## <a name="pci-compliance-review"></a>Évaluation de conformité PCI 

La solution a été évaluée par Coalfire Systems, Inc. (évaluateurs de sécurité qualifiés PCI-DSS). Le [document relatif à l’évaluation de conformité PCI](https://aka.ms/pciblueprintprocessingoverview) fournit l’évaluation de la solution réalisée par l’auditeur, ainsi que des suggestions pour transformer le plan en déploiement prêt pour la production.

## <a name="disclaimer-and-acknowledgements"></a>Clause d’exclusion de responsabilité et remerciements

*Septembre 2017*

- Ce document est fourni à titre d’information uniquement. MICROSOFT ET AVYAN N’ACCORDENT AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.  
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft ou Avyan.  
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.  

  > [!NOTE]
  > Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.  

- La solution de ce document est conçue comme une architecture de base et ne doit pas être utilisée telle quelle dans un environnement de production. Pour obtenir la conformité PCI, les clients doivent se rapprocher d’un évaluateur de sécurité qualifié.  
- Tous les noms de clients, enregistrements de transactions et autres données associées de cette page sont fictifs. Ils ont été créés pour les besoins de cette architecture de base et sont fournis à titre d’exemple uniquement. Toute association ou lien sont purement involontaires ou fortuits.  
- Cette solution a été développée conjointement par Microsoft et Avyan Consulting, et est disponible avec une [Licence MIT](https://opensource.org/licenses/MIT).
- Cette solution a été évaluée par Coalfire, l’auditeur PCI-DSS de Microsoft. Le [document relatif à l’évaluation de conformité PCI](https://aka.ms/pciblueprintcrm32) est une évaluation indépendante, réalisée par une tierce partie, de la solution et de ses composants. 

### <a name="document-authors"></a>Auteurs du document

- *Frank Simorjay (Microsoft)*  
- *Gururaj Pandurangi (Avyan Consulting)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Dépôt de code"
