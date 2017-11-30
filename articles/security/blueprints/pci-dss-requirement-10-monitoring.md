---
title: "Plan de traitement des paiements Azure - Conditions relatives à la surveillance"
description: Condition 10 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives à la surveillance pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-10"></a>Condition 10 de la norme PCI DSS

**Effectuer le suivi et surveiller tous les accès aux ressources réseau et aux données de titulaires de carte**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour connaître les procédures de test et les directives de chaque condition, reportez-vous à la documentation de la norme PCI DSS.

Les mécanismes de journalisation et la possibilité de suivre les activités des utilisateurs sont essentiels pour prévenir, détecter ou minimiser l’impact d’une compromission des données. La présence de journaux dans tous les environnements permet de suivre de près, d’émettre des alertes et d’analyser les incidents éventuels. En l’absence de journaux de l’activité du système, il est très difficile, voire impossible, de déterminer la cause d’une compromission.

## <a name="pci-dss-requirement-101"></a>Condition 10.1 de la norme PCI DSS

**10.1** Implémenter des cheminements d’audit pour relier tous les accès aux composants de système à chaque utilisateur individuel.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure limite l’accès aux outils d’administration et de diagnostics au personnel autorisé assumant des responsabilités pertinentes. Microsoft Azure limite l’accès privilégié aux outils utilisés dans l’environnement de production selon le principe des privilèges minimum. Microsoft Azure enregistre et conserve un journal de tous les accès des utilisateurs aux composants système Microsoft Azure dans l’environnement de la plateforme.<br /><br />Les composants de la plateforme Microsoft Azure (notamment le système d’exploitation, CloudNet et Fabric) sont configurés pour journaliser et collecter les événements de sécurité. Les activités de l’administrateur dans la plateforme Microsoft Azure sont journalisées. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore journalise toutes les activités du système et de l’utilisateur, notamment les données CHD. Pour plus d’informations, consultez la page [Aide PCI - Journalisation](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>Condition 10.2 de la norme PCI DSS

**10.2** Implémenter des pistes d’audit automatisées pour tous les composants système afin de reconstituer les événements suivants :
- **10.2.1** Tous les accès des utilisateurs individuels aux données de titulaires de carte
- **10.2.2** Toutes les actions exécutées par un utilisateur disposant de privilèges racines ou administratifs
- **10.2.3** Accès à toutes les pistes d’audit
- **10.2.4** Tentatives d’accès logique non valides
- **10.2.5** L’utilisation et les modifications des mécanismes d’identification et d’authentification (notamment la création de nouveaux comptes et l’élévation de privilèges) et toutes les modifications, additions ou suppressions aux comptes avec des privilèges racines ou administratifs
- **10.2.6** Initialisation, arrêt ou interruption des journaux d’audit
- **10.2.7** Création et suppression d’objets au niveau système

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure limite l’accès aux outils d’administration et de diagnostics au personnel autorisé assumant des responsabilités pertinentes. Microsoft Azure limite l’accès privilégié aux outils utilisés dans l’environnement de production selon le principe des privilèges minimum. Microsoft Azure enregistre et conserve un journal de tous les accès des utilisateurs aux composants système Microsoft Azure dans l’environnement de la plateforme.<br /><br />Les composants de la plateforme Microsoft Azure (notamment le système d’exploitation, CloudNet et Fabric) sont configurés pour journaliser et collecter les événements de sécurité. Les activités de l’administrateur dans la plateforme Microsoft Azure sont journalisées. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore journalise toutes les activités du système et de l’utilisateur, notamment les données CHD. Pour plus d’informations, consultez la page [Aide PCI - Journalisation](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>Condition 10.3 de la norme PCI DSS

**10.3** Enregistrer au moins les entrées de piste d’audit suivantes de tous les composants système pour chaque événement :
- **10.3.1** Identification de l’utilisateur
- **10.3.2** Type d’événement
- **10.3.3** Date et heure
- **10.3.4** Indication de succès ou d’échec
- **10.3.5** Origine de l’événement
- **10.3.6** Identité ou nom des données, du composant système ou de la ressource affectés

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a établi des procédures pour synchroniser les serveurs et les périphériques réseau dans l’environnement Microsoft Azure avec les serveurs de temps NTP de couche 1, lesquels sont synchronisés avec les satellites GPS. La synchronisation est effectuée automatiquement toutes les cinq minutes. Microsoft Azure est chargé de vérifier que l’heure des hôtes de service est correctement synchronisée. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore enregistre l’identification de l’utilisateur, le type d’événement, l’horodatage, les événements de réussite/d’échec, la source de l’événement et le nom de la ressource comme l’exige le contrôle 10.3.|



## <a name="pci-dss-requirement-104"></a>Condition 10.4 de la norme PCI DSS

**10.4** À l’aide d’une technologie de synchronisation temporelle, synchroniser tous les systèmes d’horloge et temporels critiques et vérifier que les éléments suivants sont mis en œuvre pour l’acquisition, la distribution et le stockage du temps. 
> [!NOTE]
> Le protocole NTP (Network Time Protocol) est un exemple de technologie de synchronisation temporelle.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a établi des procédures pour synchroniser les serveurs et les périphériques réseau dans l’environnement Microsoft Azure avec les serveurs de temps NTP de couche 1, lesquels sont synchronisés avec les satellites GPS. La synchronisation est effectuée automatiquement toutes les cinq minutes. Microsoft Azure est chargé de vérifier que l’heure des hôtes de service est correctement synchronisée. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La synchronisation temporelle pour le service PaaS est effectuée par Azure.|



### <a name="pci-dss-requirement-1041"></a>Condition 10.4.1 de la norme PCI DSS

**10.4.1** L’heure des systèmes critiques est correcte et la même pour tous.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.4](#pci-dss-requirement-10-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La synchronisation temporelle pour le service PaaS est effectuée par Azure.|



### <a name="pci-dss-requirement-1042"></a>Condition 10.4.2 de la norme PCI DSS

**10.4.2** Les données temporelles sont protégées.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.4](#pci-dss-requirement-10-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La synchronisation temporelle pour le service PaaS est effectuée par Azure.|



### <a name="pci-dss-requirement-1043"></a>Condition 10.4.3 de la norme PCI DSS

**10.4.3** Les paramètres temporels sont reçus de sources temporelles reconnues par le secteur.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.4](#pci-dss-requirement-10-4). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | La synchronisation temporelle pour le service PaaS est effectuée par Azure.|



## <a name="pci-dss-requirement-105"></a>Condition 10.5 de la norme PCI DSS

**10.5** Protéger les pistes d’audit de sorte qu’elles ne puissent pas être modifiées.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les outils FIM et IDS sont implémentés dans l’environnement Microsoft Azure. Microsoft Azure utilise EWS pour prendre en charge l’analyse en temps réel des événements au sein de son environnement d’exploitation. Les agents MA et le système AIMS génèrent des alertes en temps réel sur les événements susceptibles de compromettre le système. <br /><br />La journalisation des événements liés aux services, aux utilisateurs et à la sécurité (journaux du serveur web, journaux du serveur FTP, etc.) est activée et conservée de manière centralisée. Azure limite l’accès aux journaux d’audit au personnel autorisé en fonction de leurs responsabilités. Les journaux des événements sont archivés sur l’infrastructure d’archivage sécurisé Azure et sont conservés pendant 180 jours. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore prend en charge l’audit de tous les éléments pour OMS. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut effectuer une sauvegarde sur une source externe.|



### <a name="pci-dss-requirement-1051"></a>Condition 10.5.1 de la norme PCI DSS

**10.5.1** Limiter l’affichage des pistes d’audit aux utilisateurs qui en ont besoin pour mener à bien leur travail.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.5](#pci-dss-requirement-10-5). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore prend en charge l’audit de tous les éléments pour OMS. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut effectuer une sauvegarde sur une source externe.|



### <a name="pci-dss-requirement-1052"></a>Condition 10.5.2 de la norme PCI DSS

**10.5.2** Protéger les fichiers de piste d’audit contre toute modification non autorisée.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.5](#pci-dss-requirement-10-5). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore prend en charge l’audit de tous les éléments pour OMS. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut effectuer une sauvegarde sur une source externe.|



### <a name="pci-dss-requirement-1053"></a>Condition 10.5.3 de la norme PCI DSS

**10.5.3** Sauvegarder rapidement les fichiers de piste d’audit sur un serveur centralisé réservé à la journalisation ou sur des supports difficiles à altérer.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.5](#pci-dss-requirement-10-5). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore prend en charge l’audit de tous les éléments pour OMS. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut effectuer une sauvegarde sur une source externe.|



### <a name="pci-dss-requirement-1054"></a>Condition 10.5.4 de la norme PCI DSS

**10.5.4** Écrire les journaux pour les technologies orientées vers l’extérieur sur un serveur de journal interne, centralisé et sécurisé, ou sur un périphérique de support.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.5](#pci-dss-requirement-10-5). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore prend en charge l’audit de tous les éléments pour OMS. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut effectuer une sauvegarde sur une source externe.|



### <a name="pci-dss-requirement-1055"></a>Condition 10.5.5 de la norme PCI DSS

**10.5.5** Analyser les journaux à l’aide d’un logiciel de surveillance de l’intégrité des fichiers ou de détection des modifications pour vérifier que les données contenues dans les journaux ne peuvent pas être modifiées sans entraîner le déclenchement d’une alerte (l’ajout de nouvelles données ne doit pas engendrer d’alerte).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.5](#pci-dss-requirement-10-5). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore prend en charge l’audit de tous les éléments pour OMS. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut effectuer une sauvegarde sur une source externe.|



## <a name="pci-dss-requirement-106"></a>Condition 10.6 de la norme PCI DSS

**10.6** Examiner les journaux et les évènements de sécurité de tous les composants système pour identifier les anomalies ou les activités suspectes.
 
> [!NOTE]
> Des outils de journalisation, d’analyse et d’alerte peuvent être utilisés pour respecter cette condition.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les outils FIM et IDS sont implémentés dans l’environnement Microsoft Azure. Microsoft Azure utilise EWS pour prendre en charge l’analyse en temps réel des événements au sein de son environnement d’exploitation. Les agents MA et le système AIMS génèrent des alertes en temps réel sur les événements susceptibles de compromettre le système. <br /><br />La journalisation des événements liés aux services, aux utilisateurs et à la sécurité (journaux du serveur web, journaux du serveur FTP, etc.) est activée et conservée de manière centralisée. Azure limite l’accès aux journaux d’audit au personnel autorisé en fonction de leurs responsabilités. Les journaux des événements sont archivés sur l’infrastructure d’archivage sécurisé Azure et sont conservés pendant 180 jours. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise [Azure Security Center](https://azure.microsoft.com/services/security-center/) pour surveiller, signaler et empêcher les anomalies. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure.|



### <a name="pci-dss-requirement-1061"></a>Condition 10.6.1 de la norme PCI DSS

**10.6.1** Examiner les points suivants au moins une fois par jour :
- Tous les événements de sécurité
- Les journaux de tous les composants de système qui stockent, traitent ou transmettent des CHD et/ou SAD
- Les journaux de tous les composants système critiques
- Les journaux de tous les serveurs et composants système qui remplissent des fonctions de sécurité (par exemple, les pare-feu, les systèmes de détection d’intrusion/systèmes de prévention d’intrusion [IDS/IPS], les serveurs d’authentification, les serveurs de redirection de l’e-commerce, etc.)

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.6](#pci-dss-requirement-10-6). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise [Azure Security Center](https://azure.microsoft.com/services/security-center/) pour surveiller, signaler et empêcher les anomalies. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure.|



### <a name="pci-dss-requirement-1062"></a>Condition 10.6.2 de la norme PCI DSS

**10.6.2** Examiner régulièrement les journaux de tous les autres composants système conformément aux politiques et à la stratégie de gestion des risques de l’organisation, ainsi que le détermine l’évaluation des risques annuelle de l’organisation.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.6](#pci-dss-requirement-10-6). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise [Azure Security Center](https://azure.microsoft.com/services/security-center/) pour surveiller, signaler et empêcher les anomalies. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure.|



### <a name="pci-dss-requirement-1063"></a>Condition 10.6.3 de la norme PCI DSS

**10.6.3** Suivi des exceptions et des anomalies identifiées pendant le processus d’examen.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Consultez la section « Microsoft Azure » pour la [condition 10.6](#pci-dss-requirement-10-6). |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore utilise [Azure Security Center](https://azure.microsoft.com/services/security-center/) pour surveiller, signaler et empêcher les anomalies. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure.|



## <a name="pci-dss-requirement-107"></a>Condition 10.7 de la norme PCI DSS

**10.7** Conserver l’historique de la piste d’audit pendant au moins un an, en gardant au moins les trois derniers mois à disposition à des fins d’analyse (par exemple, disponibles en ligne, dans des archives ou pouvant être restaurés à partir d’une sauvegarde).

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure conserve les journaux d’audit pendant un an, les trois derniers mois étant immédiatement accessibles par le biais du portail interne. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore journalise toutes les activités du système et de l’utilisateur, notamment les données CHD. Pour plus d’informations, consultez la page [Aide PCI - Journalisation](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>Condition 10.8 de la norme PCI DSS

**10.8** **Condition supplémentaire pour les prestataires de services uniquement :** Implémenter un processus pour détecter et signaler à temps les pannes des systèmes de contrôle de sécurité critiques, notamment les pannes relatives aux :
- Pare-feux
- IDS/IPS
- FIM
- Protection contre les virus
- Contrôles d’accès physiques
- Contrôles d’accès logiques
- Mécanismes de journalisation d’audit
- Contrôles de segmentation (le cas échéant) 

> [!NOTE]
> Cette condition est considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle sera obligatoire.



**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise EWS pour prendre en charge l’analyse en temps réel des événements au sein de son environnement d’exploitation. Les agents MA et le système AIMS génèrent des alertes en temps réel sur les événements susceptibles de compromettre le système. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore journalise toutes les activités du système et de l’utilisateur, notamment les données CHD. Pour plus d’informations, consultez la page [Aide PCI - Journalisation](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>Condition 10.8.1 de la norme PCI DSS

**10.8.1** **Condition supplémentaire pour les prestataires de services uniquement :** Intervenir face aux pannes de contrôles de sécurité critiques en temps opportun. Les processus de résolution des pannes de contrôles de sécurité doivent comprendre les opérations suivantes :
- Restauration des fonctions de sécurité
- Identification et documentation de la durée (date et heure de début et de fin) de la panne de sécurité
- Identification et documentation des causes de la panne, notamment la cause racine, et documentation des corrections nécessaires pour résoudre la cause racine
- Identification et résolution des problèmes de sécurité survenus pendant la panne
- Évaluation des risques pour déterminer si d’autres actions sont indispensables à la suite d’une panne de sécurité
- Implémentation de contrôles pour éviter la répétition d’une telle panne -Reprise de la surveillance des contrôles de sécurité 

> [!NOTE]
> Cette condition est considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle sera obligatoire.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utilise EWS pour prendre en charge l’analyse en temps réel des événements au sein de son environnement d’exploitation. Les agents MA et le système AIMS génèrent des alertes en temps réel sur les événements susceptibles de compromettre le système. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore journalise toutes les activités du système et de l’utilisateur, notamment les données CHD. Pour plus d’informations, consultez la page [Aide PCI - Journalisation](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>Condition 10.9 de la norme PCI DSS

**10.9** Assurer que les stratégies de sécurité et les procédures opérationnelles pour le contrôle de tous les accès aux ressources du réseau et aux données de titulaires de carte sont documentées, utilisées et connues de toutes les parties concernées.


**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan PCI&#8209;DSS&nbsp;)** | Contoso Webstore fournit un cas d’usage et une description du mode de gestion et de protection des données de titulaires de carte.|




