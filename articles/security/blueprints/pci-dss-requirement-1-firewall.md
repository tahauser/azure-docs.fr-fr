---
title: Plan de traitement des paiements Azure - Conditions relatives au pare-feu
description: Condition 1 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives au pare-feu pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-1"></a>Condition 1 de la norme PCI DSS

**Installer et gérer une configuration de pare-feu pour protéger les données de titulaires de carte**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Les pare-feu sont des périphériques qui contrôlent le trafic autorisé entre les réseaux d’une entité (internes) et des réseaux non approuvés (externes), ainsi que le trafic entrant et sortant dans des zones plus sensibles des réseaux internes approuvés d’une entité. L’environnement des données de titulaires de carte est un exemple de zone plus sensible au sein du réseau approuvé d’une entité.
Un pare-feu examine l’ensemble du trafic réseau et bloque les transmissions qui ne satisfont pas aux critères de sécurité définis.
Tous les systèmes doivent être protégés contre les accès non autorisés en provenance d’un réseau non approuvé, que ce soit en entrée par le biais d’Internet (par exemple e-commerce, accès des employés à Internet à partir de leurs navigateurs, accès des employés aux e-mails, connexions dédiées telles que les connexions inter-entreprises) ou bien par le biais de réseaux sans fil ou d’autres sources. Les chemins de/vers des réseaux non approuvés, en apparence insignifiants, peuvent souvent constituer des chemins non protégés à des systèmes critiques. Les pare-feu sont des mécanismes de protection essentiels sur tout réseau informatique.
D’autres composants de système peuvent assurer une fonctionnalité pare-feu, à condition que ces composants remplissent les conditions minimales des pare-feu définies par la condition 1. Quand d’autres composants système sont utilisés dans l’environnement des données de titulaires de carte pour assurer une fonctionnalité de pare-feu, ces périphériques doivent être inclus dans l’étendue de l’évaluation de la condition 1.

## <a name="pci-dss-requirement-11"></a>Condition 1.1 de la norme PCI DSS

**1.1** Établissement et implémentation des normes de configuration des pare-feu et des routeurs comprenant les éléments suivants (1.1.1 à 1.1.7).


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore protège par pare-feu le CDE à l’aide de l’isolation PaaS. Par ailleurs, une implémentation App Service Environment garantit la protection des données qui entrent et sortent du CDE.<br /><br />Un environnement [ASE (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) est un plan de service Premium utilisé à des fins de conformité. Pour plus d’informations sur les contrôles et la configuration d’ASE, consultez [Aide PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>Condition 1.1.1 de la norme PCI DSS

**1.1.1** Processus formel d’approbation et de test de toutes les connexions réseau et des changements apportés aux configurations des pare-feu et des routeurs


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Une instance de Contoso Webstore établit un modèle DevOps CI/CD pour garantir que tous les changements sont correctement gérés. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fournit une journalisation complète des changements. Vous pouvez passer en revue les changements et vérifier leur exactitude. Pour obtenir des instructions spécifiques, consultez [Aide PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Azure Security Center](https://azure.microsoft.com/services/security-center/) offre un aperçu central de l’état de sécurité de toutes vos ressources Azure. Vous pouvez vérifier d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et vous pouvez identifier rapidement les ressources nécessitant votre attention.|



### <a name="pci-dss-requirement-112"></a>Condition 1.1.2 de la norme PCI DSS

**1.1.2** Diagramme du réseau actuel qui identifie toutes les connexions entre l’environnement de données de titulaires de carte et les autres réseaux, notamment tout réseau sans fil

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Consultez la documentation de référence sur la conception et l’architecture de Contoso Webstore fournie dans le cadre du modèle d’installation de la solution.|



### <a name="pci-dss-requirement-113"></a>Condition 1.1.3 de la norme PCI DSS

**1.1.3** Diagramme actuel montrant le flux des données de titulaires de carte dans les systèmes et les réseaux

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Consultez le diagramme de flux de données et le modèle de menace de Contoso Webstore.|



### <a name="pci-dss-requirement-114"></a>Condition 1.1.4 de la norme PCI DSS

**1.1.4** Conditions relatives au pare-feu au niveau de chaque connexion Internet et entre toute zone démilitarisée (DMZ) et la zone de réseau interne

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise des périphériques de protection des limites tels que des passerelles, des ACL réseau et des pare-feu d’application pour contrôler les communications sur les limites internes et externes au niveau de la plateforme. Le client configure ensuite ces périphériques en fonction de ses besoins et de ses spécifications. Microsoft Azure filtre les communications à leur arrivée dans la plateforme. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit une zone DMZ à l’aide de l’isolation PaaS. Par ailleurs, une implémentation App Service Environment garantit la protection des données qui entrent et sortent du CDE.<br /><br />Un environnement [ASE (App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro) est un plan de service Premium utilisé à des fins de conformité. Pour plus d’informations sur les contrôles et la configuration d’ASE, consultez [Aide PCI - App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>Condition 1.1.5 de la norme PCI DSS

**1.1.5** Description des groupes, des rôles et des responsabilités pour la gestion des composants du réseau

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise le [contrôle d’accès en fonction du rôle (RBAC) Azure](/azure/active-directory/role-based-access-control-configure) pour isoler les rôles d’utilisateur. RBAC permet une gestion précise de l’accès pour Azure. Il existe des configurations spécifiques pour l’accès à l’abonnement et l’accès à Azure Key Vault.|



### <a name="pci-dss-requirement-116"></a>Condition 1.1.6 de la norme PCI DSS

**1.1.6** Documentation de la justification professionnelle et approbation de l’utilisation de tous les services, protocoles et ports autorisés, notamment la documentation des fonctions de sécurité implémentées pour les protocoles considérés comme étant non sécurisés.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore ouvre uniquement les ports et les protocoles nécessaires conformément à la conception de l’autorité d’inscription. Pour plus d’informations sur le flux de données, consultez le diagramme de flux de données et le modèle de menace.|



### <a name="pci-dss-requirement-117"></a>Condition 1.1.7 de la norme PCI DSS

**1.1.7** Exigence d’analyse des règles concernant les pare-feu et les routeurs au moins tous les six mois

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Dans Contoso Webstore, les ensembles de règles de pare-feu sont examinés pour vérifier qu’aucune règle inutile ou inutilisée n’est incluse. De par sa conception, la démonstration est déployée avec des privilèges minimum et le chemin le plus court.|



## <a name="pci-dss-requirement-12"></a>Condition 1.2 de la norme PCI DSS

**1.2** Créer des configurations de pare-feu et de routeur qui limitent les connexions entre les réseaux non approuvés et tous les composants système dans l’environnement des données de titulaires de carte. 

> [!NOTE]
> Un « réseau non approuvé » correspond à tout réseau externe aux réseaux appartenant à l’entité sous investigation et/ou qui n’est pas sous le contrôle ou la gestion de l’entité.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le CDE de Contoso Webstore est défini dans la documentation de l’autorité d’inscription et du déploiement. Les réseaux non approuvés sont refusés par défaut.|



### <a name="pci-dss-requirement-121"></a>Condition 1.2.1 de la norme PCI DSS

**1.2.1** Restreindre le trafic entrant et sortant au trafic nécessaire à l’environnement des données de titulaires de carte et refuser tout autre trafic.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Le CDE de Contoso Webstore est défini dans la documentation de l’autorité d’inscription et du déploiement. Les réseaux non approuvés sont refusés par défaut. La démonstration Contoso Webstore configure le pare-feu d’application Microsoft Azure de manière à autoriser uniquement les plages d’adresses IP spécifiées à accéder aux services Microsoft Azure. Contoso Webstore fournit un pare-feu qui refuse tout accès au niveau des limites du CDE. Toutes les configurations sont effectuées durant la configuration initiale du déploiement.

> [!NOTE]
> ASE (App Service Environment) est utilisé dans cette solution pour isoler le CDE. Toutefois, il est essentiel que votre évaluateur de sécurité qualifié (QSA) évalue cette solution, car l’ASE implémente une isolation DMZ qui autorise les connexions sortantes effectuées par l’ASE. PCI-DSS exige le blocage de toutes les connexions entrantes et sortantes qui ne sont pas obligatoires. Pour fonctionner correctement, ASE établit des connexions sortantes en fonction des besoins, selon la définition établie dans [« Considérations relatives à la mise en réseau pour un environnement App Service Environment »](/azure/app-service/app-service-environment/network-info). Les clients doivent évaluer les connexions sortantes avec votre QSA avant de déployer la solution dans un environnement de production pour vérifier qu’elles répond aux conditions. |



### <a name="pci-dss-requirement-122"></a>Condition 1.2.2 de la norme PCI DSS

**1.2.2** Sécuriser et synchroniser les fichiers de configuration des routeurs.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit des configurations synchronisées pour les contrôles réseau natifs Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>Condition 1.2.3 de la norme PCI DSS

**1.2.3** Installer des pare-feu de périmètre entre tous les réseaux sans fil et l’environnement des données de titulaires de carte, et configurer ces pare-feu pour refuser ou, s’il est nécessaire à des fins professionnelles, autoriser uniquement le trafic entre l’environnement sans fil et l’environnement de données de titulaires de carte.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Aucune solution ou fonctionnalité sans fil n’est activée dans Contoso Webstore.|



## <a name="pci-dss-requirement-13"></a>Condition 1.3 de la norme PCI DSS

**1.3** Interdire l’accès public direct entre Internet et tout composant du système dans l’environnement des données de titulaires de carte.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise des périphériques de protection des limites basés sur le réseau et l’hôte, notamment des pare-feu, des équilibreurs de charge et des ACL. Ces périphériques utilisent des mécanismes tels que l’isolation du VLAN, NAT et le filtrage de paquets pour séparer le trafic des clients en provenance d’Internet et le trafic de gestion. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit, au moment du déploiement, les configurations du pare-feu d’application Azure pour autoriser uniquement les plages d’adresses IP spécifiées à accéder au site, notamment les machines virtuelles Azure du bastion dans leur CDE.|



### <a name="pci-dss-requirement-131"></a>Condition 1.3.1 de la norme PCI DSS

**1.3.1** Implémenter une zone DMZ pour limiter le trafic entrant aux seuls composants de système fournissant des services, protocoles et ports autorisés, accessibles au public.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’implémentation Contoso Webstore de sa zone DMZ garantit que seuls les services autorisés peuvent se connecter au CDE.|



### <a name="pci-dss-requirement-132"></a>Condition 1.3.2 de la norme PCI DSS

**1.3.2** Limiter le trafic Internet entrant aux adresses IP dans la DMZ.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’implémentation Contoso Webstore de sa zone DMZ garantit que seuls les services autorisés peuvent se connecter au CDE.|



### <a name="pci-dss-requirement-133"></a>Condition 1.3.3 de la norme PCI DSS

**1.3.3** Implémenter des mesures anti-usurpation pour détecter et pour empêcher les adresses IP de source frauduleuse de pénétrer sur le réseau. (Par exemple, bloquer le trafic provenant d’Internet avec une adresse de source interne.)

**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implémente le filtrage réseau pour empêcher le trafic falsifié et limiter le trafic entrant et sortant aux composants de plateforme approuvés. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-134"></a>Condition 1.3.4 de la norme PCI DSS

**1.3.4** Ne pas autoriser le trafic sortant non autorisé de l’environnement des données de titulaires de carte vers Internet.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’architecture de Contoso Webstore empêche le trafic sortant non autorisé de l’environnement dans l’étendue vers Internet. Pour cela, des ACL de trafic sortant sont configurées pour les protocoles et ports approuvés dans Microsoft Azure. Ces contrôles incluent l’accès au CDE dans la base de données SQL Server. <br /><br />Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Aide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>Condition 1.3.5 de la norme PCI DSS

**1.3.5** Les connexions « établies » sont les seules autorisées sur le réseau.


**Responsabilités :&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implémente le filtrage réseau pour empêcher le trafic falsifié et limiter le trafic entrant et sortant aux composants de plateforme approuvés. Le réseau Microsoft Azure est isolé pour séparer le trafic des clients du trafic de gestion. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Non applicable.|



### <a name="pci-dss-requirement-136"></a>Condition 1.3.6 de la norme PCI DSS

**1.3.6** Placer les composants de système qui stockent les données de titulaires de carte (comme une base de données) dans une zone de réseau interne, isolée de la zone DMZ et des autres réseaux non approuvés.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise l’isolation du réseau et NAT pour séparer le trafic des clients du trafic de gestion. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | L’architecture de Contoso Webstore empêche le trafic sortant non autorisé de l’environnement dans l’étendue vers Internet. Pour cela, des ACL de trafic sortant sont configurées pour les protocoles et ports approuvés dans Microsoft Azure. Ces contrôles incluent l’accès au CDE dans la base de données SQL Server. <br /><br />Une instance de SQL Database PaaS est utilisée pour illustrer les mesures de sécurité de base de données. Pour plus d’informations, consultez [Aide PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>Condition 1.3.7 de la norme PCI DSS

**1.3.7** Ne pas divulguer les adresses IP et les informations d’acheminement privées à des parties non autorisées. 

> [!NOTE]
> Les méthodes d’altération de l’adressage IP peuvent inclure, mais ne sont pas limités à :
> - NAT (Network Address Translation)
> - Protection des serveurs contenant des données de titulaires de carte derrière des serveurs proxy/parefeu
> - Retrait ou filtrage des annonces d’acheminement pour les réseaux privés employant des adresses inscrites
> - Utilisation interne de l’espace d’adresse RFC1918 au lieu d’adresses inscrites


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise NAT (Network Address Translation) et l’isolation du réseau pour séparer le trafic des clients du trafic de gestion. Les périphériques Azure sont identifiés de façon unique par leur UUID et sont authentifiés à l’aide de Kerberos. Les périphériques réseau gérés par Azure sont identifiés par une adresse IP définie selon le document RFC 1918. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore place toutes les données de titulaires de cartes derrière des pare-feu/serveurs de proxy et utilise l’espace d’adressage RFC1918 en interne.|



## <a name="pci-dss-requirement-14"></a>Condition 1.4 de la norme PCI DSS

**1.4** Installer un logiciel de pare-feu personnel ou une fonctionnalité équivalente sur tout appareil informatique portable (notamment les appareils appartenant à la société et/ou à l’employé) qui se connecte à Internet en dehors du réseau (par exemple, les ordinateurs portables utilisés par les employés) et qui permet également un accès au CDE. Les configurations de pare-feu (ou fonctionnalité équivalente) comprennent ce qui suit : -Des réglages de configuration spécifiques sont définis.
- Un pare-feu personnel (ou fonctionnalité équivalente) fonctionne activement.
- Le pare-feu personnel (ou fonctionnalité équivalente) ne peut pas être altéré par les utilisateurs des appareils informatiques portables.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore ne protège pas les appareils des utilisateurs finaux. [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) peut être utilisé pour gérer les appareils mobiles utilisés par votre personnel pour accéder aux données d’entreprise.|



## <a name="pci-dss-requirement-15"></a>Condition 1.5 de la norme PCI DSS

**1.5** Assurer que les stratégies de sécurité et les procédures opérationnelles pour la gestion des pare-feu sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit, au moment du déploiement, les configurations du pare-feu d’application Azure pour autoriser uniquement les plages d’adresses IP spécifiées à accéder au site, notamment les machines virtuelles Azure du bastion dans leur CDE.|




