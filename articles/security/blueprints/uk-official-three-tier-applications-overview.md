---
title: "Azure Blueprint Automation - Applications web à trois couches pour la classification UK-OFFICIAL"
description: "Azure Blueprint Automation - Applications web à trois couches pour la classification UK-OFFICIAL"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5f5694367d9be2ae66c7303cfea063b7f4979307
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation-three-tier-web-applications-for-uk-official"></a>Azure Blueprint Automation : Applications web à trois couches pour la classification UK-OFFICIAL

## <a name="overview"></a>Vue d'ensemble

 Cet article fournit des conseils, ainsi que des scripts d’automatisation, pour la création d’une architecture web à trois couches Microsoft Azure, adaptée à la gestion de nombreuses charges de travail classées « OFFICIAL » par le gouvernement britannique.

 À l’aide d’une approche de type « Infrastructure as Code », le groupe de modèles [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (ARM) déploie un environnement qui s’aligne sur les 14 [principes de sécurité cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) du National Cyber Security Centre (NCSC), ainsi que sur les [contrôles de sécurité critiques](https://www.cisecurity.org/critical-controls.cfm) du Center for Internet Security (CIS) au Royaume-Uni.

 Le NCSC recommande aux clients d’utiliser ses principes de sécurité cloud dans le but d’évaluer les propriétés de sécurité du service, et de mieux comprendre la répartition des responsabilités entre le client et le fournisseur. Nous avons fourni des informations sur chacun de ces principes pour vous aider à comprendre la répartition des responsabilités.

 Cette architecture, et les modèles ARM (Azure Resource Manager) correspondants, sont accompagnés du livre blanc de Microsoft concernant le [plan Azure à usage du gouvernement britannique](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ce livre blanc explique comment chacun des services Azure s’aligne sur les 14 principes de sécurité cloud du NCSC, permettant ainsi aux organisations de répondre plus rapidement à leurs obligations de conformité, en utilisant les services cloud de Microsoft Azure dans le monde entier et au Royaume-Uni.

 Ce modèle déploie l’infrastructure de la charge de travail. Le code de l’application, ainsi que les logiciels de prise en charge de couche Métier et de couche Données, doivent être installés et configurés. Des instructions détaillées sur le déploiement sont disponibles [ici](https://aka.ms/ukwebappblueprintrepo).

 Si vous n’avez pas d’abonnement Azure, vous pouvez vous inscrire rapidement et facilement : [Bien démarrer avec Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

 Les modèles Azure offrent une architecture d’application web à trois couches dans un environnement cloud Azure, adaptée aux charges de travail du gouvernement britannique. L’architecture fournit un environnement sécurisé hybride qui étend un réseau local à Azure, ce qui permet d’accéder aux charges de travail web de manière sécurisée, que ce soit au sein de l’entreprise ou par Internet.

![texte de remplacement](images/diagram.png?raw=true "Architecture à trois couches Azure pour le gouvernement britannique")

 Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

(1) Réseau virtuel /16 - Réseau virtuel opérationnel
- (3) Sous-réseaux /24 - 3 couches (web, métier, données)
- (1) Sous-réseau /27 - ADDS
- (1) Sous-réseau /27 - Sous-réseau de passerelle
- (1) Sous-réseau /29 - Sous-réseau Application Gateway
- Utilise le DNS par défaut (fourni par Azure)
- Appairage activé dans le réseau virtuel de gestion
- Groupe de sécurité réseau (NSG) pour la gestion des flux de trafic

(1) Sous-réseau /24 - Réseau virtuel de gestion
- (1) Sous-réseau /27
- Utilise le DNS ADDS (2) et les entrées Azure DNS (1)
- Appairage activé dans le réseau virtuel opérationnel
- Groupe de sécurité réseau (NSG) pour la gestion des flux de trafic

(1) Application Gateway
- WAF : activé
- Mode WAF : prévention
- Ensemble de règles : OWASP 3.0
- Écouteur HTTP sur le port 80
- Connectivité/trafic régulés par le groupe de sécurité réseau
- Point de terminaison d’adresse IP publique défini (Azure)

(1) VPN - Tunnel VPN IPSec de site à site, basé sur les itinéraires
- Point de terminaison d’adresse IP publique défini (Azure)
- Connectivité/trafic régulés par le groupe de sécurité réseau
- (1) passerelle de réseau local (point de terminaison local)
- (1) passerelle de réseau Azure (point de terminaison Azure)

(9) Machines virtuelles - Toutes les machines virtuelles sont déployées avec des paramètres Azure IaaS Antimalware DSC

- (2) Contrôleurs de domaine Active Directory Domain Services (Windows Server 2012 R2)
  - (2) Rôles serveur DNS - 1 par machine virtuelle
  - (2) Cartes réseau connectées au réseau virtuel opérationnel - 1 par machine virtuelle
  - Les deux sont joints au domaine défini dans le modèle
    - Domaine créé dans le cadre du déploiement


- (1) Machine virtuelle de gestion du serveur de rebond (hôte bastion)
  - 1 carte réseau sur le réseau virtuel de gestion avec adresse IP publique
    - Le groupe de sécurité réseau est utilisé pour limiter le trafic (entrant et sortant) vers des sources spécifiques
  - Non joint au domaine


- (2) Machines virtuelles de couche Web
  - (2) Rôles serveur IIS - 1 par machine virtuelle
  - (2) Cartes réseau connectées au réseau virtuel opérationnel - 1 par machine virtuelle
  - Non joint au domaine


- (2) Machines virtuelles de couche Métier
  - (2) Cartes réseau connectées au réseau virtuel opérationnel - 1 par machine virtuelle
  - Non joint au domaine


- (2) Machines virtuelles de couche Données
  - (2) Cartes réseau connectées au réseau virtuel opérationnel - 1 par machine virtuelle
  - Non joint au domaine

Groupes à haute disponibilité
- (1) Groupe de machines virtuelles du contrôleur de domaine Active Directory - 2 machines virtuelles
- (1) Groupe de machines virtuelles de couche Web - 2 machines virtuelles
- (1) Groupe de machines virtuelles de couche Métier - 2 machines virtuelles
- (1) Groupe de machines virtuelles de couche Données - 2 machines virtuelles

Load Balancer
- (1) Équilibreur de charge de couche Web
- (1) Équilibreur de charge de couche Métier
- (1) Équilibreur de charge de couche Données

Storage
- (14) Nombre total de comptes de stockage
  - Groupe à haute disponibilité du contrôleur de domaine Active Directory
    - (2) Comptes principaux de stockage localement redondant - 1 pour chaque machine virtuelle  
    - (1) Compte de stockage localement redondant de diagnostic pour le groupe à haute disponibilité ADDS
  - Machine virtuelle serveur de rebond de gestion
    - (1) Compte principal de stockage localement redondant pour la machine virtuelle serveur de rebond
    - (1) Compte de stockage localement redondant de diagnostic pour la machine virtuelle serveur de rebond
  - Machines virtuelles de couche Web
    - (2) Comptes principaux de stockage localement redondant - 1 pour chaque machine virtuelle  
    - (1) Compte de stockage localement redondant de diagnostic pour le groupe à haute disponibilité de couche Web
  - Machines virtuelles de couche Métier
    - (2) Comptes principaux de stockage localement redondant - 1 pour chaque machine virtuelle  
    - (1) Compte de stockage localement redondant de diagnostic pour le groupe à haute disponibilité de couche Métier
  - Machines virtuelles de couche Données
    - (2) Comptes principaux de stockage localement redondant - 1 pour chaque machine virtuelle  
    - (1) Compte de stockage localement redondant de diagnostic pour le groupe à haute disponibilité de couche Données

### <a name="deployment-architecture"></a>Architecture de déploiement :

**Réseau local** : réseau local privé implémenté au sein d’une organisation.

**Réseau virtuel de production**: le [réseau virtuel](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) de production héberge l’application et les autres ressources opérationnelles exécutées dans Azure. Chaque réseau virtuel peut contenir plusieurs sous-réseaux, qui sont utilisés pour isoler et gérer le trafic réseau.

**Couche Web** : gère les requêtes HTTP entrantes. Les réponses sont retournées via cette couche.

**Couche Métier** : implémente les processus métier et toute autre logique fonctionnelle pour le système.

**Couche Données** : permet le stockage des données persistantes, à l’aide des [groupes de disponibilité AlwaysOn SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) pour fournir une haute disponibilité. Les clients peuvent utiliser [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) comme alternative PaaS.

**Passerelle** : la [passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) assure la connectivité entre les routeurs du réseau local et ceux du réseau virtuel de production.

**Passerelle Internet et adresse IP publique** : la passerelle Internet expose les services d’application aux utilisateurs via Internet. Le trafic qui accède à ces services est sécurisé avec [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), qui propose des fonctionnalités de routage de couche 7 et des fonctionnalités d’équilibrage de charge avec la protection par pare-feu d’applications web (WAF).

**Réseau virtuel de gestion** : ce [réseau virtuel](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) contient des ressources qui implémentent des fonctionnalités de gestion et de surveillance pour les charges de travail exécutées dans le réseau virtuel de production.

**Serveur de rebond (jumpbox)** : aussi appelé [hôte bastion](https://en.wikipedia.org/wiki/Bastion_host). Il s’agit d’une machine virtuelle sécurisée située sur le réseau, que les administrateurs utilisent pour se connecter aux machines virtuelles du réseau virtuel de production. Le serveur de rebond a un groupe de sécurité réseau qui autorise le trafic distant provenant uniquement d’adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic RDP (Remote Desktop Protocol), la source du trafic doit être définie dans le groupe de sécurité réseau. La gestion des ressources de production est réalisée via le protocole RDP, à l’aide d’une machine virtuelle serveur de rebond sécurisée.

**Itinéraires définis par l’utilisateur** : les [itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) sont utilisés pour définir le flux du trafic IP au sein des réseaux virtuels Azure.

**Réseaux virtuels appairés** : les réseaux virtuels de production et de gestion sont connectés à l’aide de [VNET Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Ces réseaux virtuels sont toujours gérés comme des ressources distinctes. Toutefois, lorsque les machines virtuelles souhaitent s’y connecter, ils n’apparaissent que comme un seul et même réseau. Ces réseaux communiquent entre eux directement à l’aide d’adresses IP privées. Pour utiliser VNET Peering, les réseaux virtuels doivent se trouver dans la même région Azure.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle.

**Active Directory Domain Services (AD DS)** : cette architecture permet un déploiement dédié [d’Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx).

**Journalisation et audit** : le [journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) capture les opérations effectuées sur les ressources de votre abonnement (par exemple, qui a démarré l’opération, quand l’opération a eu lieu ou l’état de l’opération), ainsi que les valeurs des autres propriétés qui peuvent vous aider à analyser l’opération. Le journal d’activité Azure est un service de plateforme Azure qui capture toutes les actions effectuées dans un abonnement. Les journaux peuvent être archivés ou exportés si nécessaire.

**Surveillance du réseau et alertes** : [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) est un service de plateforme qui fournit une fonctionnalité de capture des paquets réseau, une fonctionnalité de journalisation des flux, des outils de topologie et une fonctionnalité de diagnostic pour le trafic réseau de vos réseaux virtuels.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="business-continuity"></a>Continuité des activités

**Haute disponibilité** : les charges de travail serveur sont regroupées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour garantir la haute disponibilité des machines virtuelles dans Azure. Cette configuration aide à garantir qu’au moins l’une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non planifié, et répondra au niveau de 99,95 % inscrit dans les contrats de niveau de service Azure.

### <a name="logging-and-audit"></a>Journalisation et audit

**Surveillance** : [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) est un service de plateforme qui permet de surveiller en un seul et même endroit le journal d’activité, les métriques et les journaux de diagnostic de toutes vos ressources Azure. Azure Monitor peut être configuré pour visualiser, interroger, acheminer, archiver et agir sur les métriques et les journaux provenant des ressources Azure. Il est recommandé d’utiliser le contrôle d’accès basé sur les ressources pour sécuriser la piste d’audit et garantir que les utilisateurs ne puissent pas modifier les journaux.

**Journaux d’activité** : configurez les [journaux d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) pour fournir des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement.

**Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) correspondent à l’ensemble des journaux émis par une ressource. Ces journaux incluent les journaux des événements système Windows, ainsi que les journaux des objets blob, des tables et des files d’attente.

**Journaux du pare-feu** : Application Gateway fournit des journaux de diagnostic et d’accès complets. Les journaux de pare-feu sont disponibles pour les ressources de passerelle d’application avec WAF activé.

**Archivage des journaux** : les données des journaux peuvent être écrites dans un compte de stockage Azure centralisé à des fins d’archivage et pendant une période de rétention définie. Les journaux peuvent être traités avec Azure Log Analytics ou par un système SIEM tiers.

### <a name="identity"></a>Identité

**Active Directory Domain Services (AD DS)** : cette architecture permet un déploiement d’Active Directory Domain Services dans Azure. Pour obtenir des recommandations spécifiques sur l’implémentation d’Active Directory dans Azure, consultez les articles suivants :

[Extension d’Active Directory Domain Services (AD DS) à Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Recommandations concernant le déploiement de Windows Server Active Directory sur des machines virtuelles Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Intégration Active Directory** : comme alternative à l’architecture AD DS dédiée, les clients peuvent utiliser l’intégration [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) ou [Active Directory dans Azure, joint à une forêt locale](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Sécurité

**Sécurité de gestion** : ce plan Azure permet aux administrateurs de se connecter au réseau virtuel de gestion et au serveur de rebond à l’aide du protocole RDP, à partir d’une source approuvée. Le trafic du réseau virtuel de gestion est contrôlé à l’aide des groupes de sécurité réseau. L’accès au port 3389 est limité au trafic provenant d’une plage d’adresses IP approuvée, qui peut accéder au sous-réseau contenant le serveur de rebond.

Les clients peuvent également envisager d’utiliser un [modèle administratif de sécurité renforcée](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) pour sécuriser l’environnement lors de la connexion au réseau virtuel de gestion et au serveur de rebond. Pour renforcer la sécurité, il est conseillé aux clients d’utiliser une [station de travail avec accès privilégié](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) et une configuration RDGateway. L’utilisation des appliances virtuelles de réseau et des zones DMZ publiques/privées proposent des améliorations de sécurité supplémentaires.

**Sécurisation du réseau** : les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sont recommandés pour chaque sous-réseau afin de fournir un deuxième niveau de protection contre le trafic qui rentre en contournant une passerelle mal configurée ou désactivée. Exemple : [Modèle ARM pour le déploiement d’un groupe de sécurité réseau](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Sécurisation des points de terminaison publics** : la passerelle Internet expose les services d’application aux utilisateurs via Internet. Le trafic qui accède à ces services est sécurisé avec [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), qui fournit un pare-feu d’applications web et une gestion du protocole HTTPS.

**Plages d’adresses IP** : les plages d’adresses IP de l’architecture sont des suggestions. Il est conseillé aux clients de tenir compte de leur environnement et d’utiliser les plages adaptées à celui-ci.

**Connectivité hybride** : les charges de travail cloud sont connectées au centre de données local via un réseau VPN IPSEC qui utilise la passerelle VPN Azure. Les clients doivent vérifier qu’ils utilisent une passerelle VPN appropriée pour se connecter à Azure. Exemple : [Modèle ARM de passerelle VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Les clients qui exécutent des charges de travail stratégiques à grande échelle avec des exigences de Big Data peuvent utiliser une architecture réseau hybride avec [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) pour connecter un réseau privé aux services cloud Microsoft.

**Séparation des préoccupations** : cette architecture de référence attribue un réseau virtuel à chaque type d’opérations (administratives et commerciales). La séparation des réseaux virtuels et des sous-réseaux permet de gérer le trafic, notamment de restreindre le trafic entrant et sortant, en utilisant des groupes de sécurité réseau entre les segments réseau, et en suivant les bonnes pratiques de la rubrique [Services cloud et sécurité réseau Microsoft](https://docs.microsoft.com/azure/best-practices-network-security).

**Gestion des ressources** : les ressources Azure, telles que les machines virtuelles, les réseaux virtuels et les équilibreurs de charge, sont regroupées dans des [groupes de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource) pour être gérées. Les rôles du contrôle d’accès basé sur les ressources peuvent ensuite être affectés à chaque groupe de ressources pour restreindre l’accès aux seuls utilisateurs autorisés.

**Restrictions de contrôle d’accès** : utilisez le [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) pour gérer les ressources de votre application à l’aide de [rôles personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). Le contrôle RBAC peut être utilisé pour restreindre les opérations que DevOps peut effectuer sur chaque couche. Lorsque vous accordez des autorisations, utilisez le [principe des privilèges minimum](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Journalisez toutes les opérations d’administration et réalisez des audits réguliers pour vérifier qu’aucune modification de configuration n’est prévue.

**Accès à Internet** : cette architecture de référence utilise [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) comme passerelle Internet et équilibreur de charge. Certains clients peuvent également envisager d’utiliser des appliances virtuelles réseau tierces pour d’autres couches de réseau, en guise d’alternative à [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Azure Security Center** : [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) fournit une vue centralisée de l’état de sécurité des ressources d’un abonnements, et fournit des recommandations qui aident à empêcher la compromission des ressources. Il peut également être utilisé pour appliquer des stratégies plus granulaires. Par exemple, les stratégies peuvent être appliquées à certains groupes de ressources, ce qui permet à l’entreprise d’adapter sa posture face aux risques. Il est recommandé aux clients d’activer Azure Security Center dans leur abonnement Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentation de conformité concernant les principes de sécurité cloud du NCSC

Le Crown Commercial Service (l’agence chargée d’améliorer les activités commerciales et d’approvisionnement du gouvernement) a mis à jour le classement G-Cloud des services cloud d’entreprise de Microsoft vers la version 6, classification qui couvre toutes ses offres de catégorie OFFICIAL. Pour obtenir des informations détaillées sur Azure et la classification G-Cloud, consultez le [résumé de l’évaluation de sécurité Azure UK G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Ce plan Azure UK-OFFICIAL s’aligne sur les 14 [principes de sécurité cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) du NCSC qui permettent de garantir qu’un environnement est adapté aux charges de travail de classification UK-OFFICIAL.

La [matrice de responsabilité des clients](https://aka.ms/blueprintuk-gcrm) (classeur Excel) répertorie les 14 principes de sécurité cloud, et indique, pour chaque principe (ou sous-principe), si sa mise en œuvre est de la responsabilité de Microsoft, du client, ou des deux.

La [matrice de mise en œuvre des principes](https://aka.ms/ukwebappblueprintpim) (classeur Excel) répertorie les14 principes de sécurité cloud, et indique, pour chaque principe (ou sous-principe) qui est de la responsabilité du client selon la Matrice de responsabilités des clients, (1) si Azure Blueprint Automation met en œuvre le principe et (2) une description de la façon dont la mise en œuvre s’aligne avec les exigences du principe. Ce contenu est également disponible [ici](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

En outre, le Cloud Security Alliance (CSA) a publié une matrice de contrôle cloud pour aider les clients à évaluer les fournisseurs cloud et à se poser les questions nécessaires avant de passer aux services cloud. En réponse, Microsoft Azure a répondu au questionnaire [CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA) du Cloud Security Alliance, dans lequel est expliqué comment Microsoft aborde les principes suggérés.

## <a name="deploy-the-solution"></a>Déployer la solution

Il existe deux méthodes pour déployer cette solution de plan Azure. La première méthode utilise un script PowerShell, alors que la deuxième utilise le portail Azure pour déployer l’architecture de référence. Des instructions détaillées sur le déploiement sont disponibles [ici](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
