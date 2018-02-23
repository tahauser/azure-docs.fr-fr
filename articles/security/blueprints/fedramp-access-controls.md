---
title: "Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP - Contrôle d’accès"
description: "Automatisation d’applications web FedRAMP - Contrôle d’accès"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 73ce33bc6136b9b76661dc9e29b3a11c3eabc5f8
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="access-control-ac"></a>Contrôle d’accès (autorité de certification)

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication 800-53 rév. 4 du NIST.

## <a name="nist-800-53-control-ac-1"></a>NIST 800-53 Contrôle AC-1

#### <a name="access-control-policy-and-procedures"></a>Stratégie et procédures du contrôle d’accès

**AC-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles de l’organisation] une stratégie de contrôle d’accès qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie de contrôle d’accès et des contrôles d’accès associés ; et révise et met à jour la stratégie actuelle de contrôle d’accès [Affectation : fréquence définie par l’organisation] ; et les procédures de contrôle d’accès [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures du contrôle d’accès de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800-53 Contrôle AC-2.a

#### <a name="account-management"></a>Gestion de compte

**AC-2.a** L’organisation identifie et sélectionne les types de comptes de système d’information suivants pour appuyer les missions et les fonctions organisationnelles : [Affectation : types de comptes du système d’information définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint s’appuie sur les types de compte système suivants et les implémente : comptes d’utilisateur Azure Active Directory (utilisés pour déployer la solution et gérer l’accès aux ressources Azure), comptes d’utilisateur des systèmes d’exploitation Windows (gérés par Active Directory) et comptes de service Microsoft SQL Server. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800-53 Contrôle AC-2.b

#### <a name="account-management"></a>Gestion de compte

**AC-2.b** L’organisation affecte des gestionnaires de comptes aux comptes du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’affectation des gestionnaires de comptes identifiés dans l’alinéa AC-02.a. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800-53 Contrôle AC-2.c

#### <a name="account-management"></a>Gestion de compte

**AC-2.c** L’organisation établit les conditions d’appartenance aux rôles et aux groupes.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’établissement des critères d’appartenance aux rôles et aux groupes pour les types de comptes gérés par le client (voir AC-02. a). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800-53 Contrôle AC-2.d

#### <a name="account-management"></a>Gestion de compte

**AC-2.d** L’organisation spécifie les utilisateurs autorisés du système d’information, l’appartenance aux groupes et aux rôles, ainsi que les autorisations d’accès (p. ex., les privilèges) et d’autres attributs (au besoin) pour chaque compte.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut se fier à un processus d’autorisation de compte établi au niveau de l’entreprise. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800-53 Contrôle AC-2.e

#### <a name="account-management"></a>Gestion de compte

**AC-2.e** L’organisation doit obtenir l’approbation des [Affectation : personnel ou rôles définis par l’organisation] pour les requêtes de création de compte du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut se fier à un processus d’autorisation de compte établi au niveau de l’entreprise. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800-53 Contrôle AC-2.f

#### <a name="account-management"></a>Gestion de compte

**AC-2.f** L’organisation crée, active, modifie, désactive et supprime les comptes du système d’information conformément aux [Affectation : procédures ou conditions définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut compter sur un processus de gestion des comptes établi au niveau de l’entreprise. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800-53 Contrôle AC-2.g

#### <a name="account-management"></a>Gestion de compte

**AC-2.g** L’organisation surveille l’utilisation des comptes du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le tableau de bord Identité et accès de la solution de sécurité et d’audit Microsoft Operations Management Suite. Ce tableau de bord permet aux gestionnaires de comptes de surveiller l’utilisation des comptes du système d’information. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800-53 Contrôle AC-2.h

#### <a name="account-management"></a>Gestion de compte

**AC-2.h** L’organisation notifie les gestionnaires de comptes lorsque les comptes ne sont plus requis ; lorsque les utilisateurs sont résiliés ou transférés ; et en cas de modification de l’utilisation ou des éléments à connaître du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès au niveau de l’entreprise du client peuvent établir un processus pour notifier le gestionnaire de comptes approprié lorsqu’un compte n’est plus nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800-53 Contrôle AC-2.i

#### <a name="account-management"></a>Gestion de compte

**AC-2.i** L’organisation autorise l’accès au système d’information en fonction d’une autorisation d’accès valide, de l’utilisation prévue du système et d’autres attributs requis par l’organisation ou les missions ou fonctions commerciales connexes.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès du client au niveau de l’entreprise peuvent établir un processus d’autorisation d’accès. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800-53 Contrôle AC-2.j

#### <a name="account-management"></a>Gestion de compte

**AC-2.j** L’organisation examine les comptes pour s’assurer qu’ils sont conformes aux exigences en matière de gestion des comptes [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’examen des comptes qu’il contrôle à la fréquence requise pour déterminer si les comptes sont conformes à toutes les exigences de l’organisation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800-53 Contrôle AC-2.k

#### <a name="account-management"></a>Gestion de compte

**AC-2.k** L’organisation établit un processus de réémission des informations d’identification pour les comptes partagés ou de groupe (si déployés) lorsque des personnes sont retirées du groupe.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès au niveau de l’entreprise du client peuvent établir un processus de gestion des informations d’identification du compte collectif. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 Contrôle AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Gestion des comptes | Gestion automatisée des comptes du système

**AC-2 (1)** L’organisation utilise des mécanismes automatisés pour gérer les comptes du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le tableau de bord Identité et accès de la solution de sécurité et d’audit Microsoft Operations Management Suite. Ce tableau de bord permet aux gestionnaires de comptes de surveiller l’utilisation des comptes du système d’information. OMS peut être configuré pour envoyer des alertes lorsqu’une activité atypique est suspectée ou que d’autres événements prédéfinis se produisent. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 Contrôle AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Gestion des comptes | Suppression des comptes temporaires et d’urgence

**AC-2 (2)** Le système d’information [Sélection : supprime; désactive] automatiquement les comptes temporaires et d’urgence après [Affectation : période définie par l’organisation pour chaque type de compte].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint ne déploie pas de comptes temporaires ou d’urgence. S’ils ne sont pas désactivés manuellement, le contrôleur de domaine déployé désactive automatiquement tous les comptes inactifs après 35 jours. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 Contrôle AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Gestion des comptes | Désactiver les comptes inactifs

**AC-2 (3)** Le système d’information désactive automatiquement les comptes inactifs après [Affectation : période définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le contrôleur de domaine déployé par ce programme Blueprint est configuré pour désactiver tous les comptes d’utilisateur après 35 jours d’inactivité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 Contrôle AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Gestion des comptes | Mesures d’audit automatisées

**AC-2 (4)** Le système d’information vérifie automatiquement les actions de création, de modification, d’activation, de désactivation et de suppression des comptes et en notifie [Affectation : personnel ou rôles définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente les types de compte système suivants : comptes d’utilisateur Azure Active Directory, comptes d’utilisateur de systèmes d’exploitation Windows, comptes de service SQL Server. Les actions de gestion de compte Azure Active Directory génèrent un événement dans le journal des activités Azure ; les actions de gestion de compte au niveau du système d’exploitation génèrent un événement dans le journal du système. Ces journaux sont collectés par Log Analytics et stockés dans le dépôt OMS. OMS peut être configuré pour envoyer des alertes lorsque des événements prédéfinis se produisent.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 Contrôle AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Gestion des comptes | Déconnexion pour inactivité

**AC-2 (5)** L’organisation requiert une déconnexion des utilisateurs après [Affectation : période d’inactivité définie par l’organisation ou description des conditions de la déconnexion].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut établir une stratégie selon laquelle les utilisateurs se déconnectent lorsqu’ils prévoient d’être inactifs pendant un certain temps (ou en fonction d’autres facteurs). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 Contrôle AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>Gestion des comptes | Schémas basés sur des rôles

**AC-2 (7).a** L’organisation établit et administre des comptes utilisateurs privilégiés conformément à un système d’accès basé sur des rôles qui organise l’accès et les privilèges autorisés du système d’information en fonction des rôles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente les types de compte système suivants : comptes d’utilisateur Azure Active Directory, comptes d’utilisateur de systèmes d’exploitation Windows, comptes de service SQL Server. Les privilèges de compte Azure Active Directory sont implémentés en utilisant le contrôle d’accès en fonction du rôle en assignant les utilisateurs à des rôles ; les privilèges de compte Active Directory sont implémentés en utilisant le contrôle d’accès en fonction du rôle en assignant les utilisateurs à des groupes de sécurité. Ces schémas basés sur les rôles peuvent être étendus par le client pour répondre aux besoins des missions. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 Contrôle AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>Gestion des comptes | Schémas basés sur des rôles

**AC-2 (7).b** L’organisation surveille les attributions de rôles dotés de privilèges.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le tableau de bord Identité et accès de la solution de sécurité et d’audit Microsoft Operations Management Suite. Ce tableau de bord permet aux gestionnaires de comptes de surveiller l’utilisation des comptes du système d’information. Cette solution peut être interrogée pour créer un rapport sur les attributions de rôles dotés de privilèges. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 Contrôle AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>Gestion des comptes | Schémas basés sur des rôles

**AC-2 (7).c** L’organisation effectue des [Affectation : actions définies par l’organisation] lorsque les attributions de rôles dotés de privilèges ne sont plus appropriées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’intervention sur les comptes qu’il gère lorsque les attributions de rôles dotés de privilèges ne sont plus appropriées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 Contrôle AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Gestion des comptes | Restrictions sur l’utilisation des comptes partagés/de groupe

**AC-2 (9)** L’organisation autorise uniquement l’utilisation des comptes partagés ou de groupe qui respectent les conditions [Affectation : conditions définies par l’organisation pour établir des comptes partagés ou de groupe].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun compte partagé/de groupe n’est activé sur les ressources déployées par ce programme Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 Contrôle AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Gestion des comptes | Résiliation des informations d’identification de compte partagé/de groupe

**AC-2 (10)** Le système d’information résilie les informations d’identification des comptes partagés/de groupe lorsque les membres quittent le groupe.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun compte partagé/de groupe n’est activé sur les ressources déployées par ce programme Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 Contrôle AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Gestion des comptes | Conditions d’utilisation

**AC-2 (11)** Le système d’information applique [Affectation : circonstances et/ou conditions d’utilisation définies par l’organisation] aux [Affectation : comptes du système d’information définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe peut être établie dans Active Directory et configurée pour implémenter des restrictions d’heure ou d’autres conditions d’utilisation de compte. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 Contrôle AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gestion des comptes | Surveillance des comptes/utilisation atypique

**AC-2 (12).a** L’organisation surveille l’[Affectation : utilisation atypique définie par l’organisation] des comptes du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le tableau de bord Identité et accès de la solution de sécurité et d’audit Microsoft Operations Management Suite. Ce tableau de bord permet aux gestionnaires de comptes de surveiller les tentatives d’accès aux ressources déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 Contrôle AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gestion des comptes | Surveillance des comptes/utilisation atypique

**AC-2 (12).b** L’organisation crée des rapports sur l’utilisation atypique des comptes du système d’information pour [Affectation : personnel ou rôles définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le tableau de bord Identité et accès de la solution de sécurité et d’audit Microsoft Operations Management Suite. Ce tableau de bord permet aux gestionnaires de comptes de surveiller les tentatives d’accès aux ressources déployées. Cette solution peut être configurée pour envoyer des alertes en cas de suspicion d’activité atypique ou d’autres événements prédéfinis. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 Contrôle AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Gestion des comptes | Désactiver les comptes des personnes à risque élevé

**AC-2 (13)** L’organisation désactive les comptes des utilisateurs qui présentent un risque élevé pendant la [Affectation : période définie par l’organisation] de découverte du risque.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures de contrôle d’accès du client au niveau de l’entreprise peuvent établir des conditions pour désactiver les comptes des utilisateurs qui représentent un risque important pour l’organisation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800-53 Contrôle AC-3

#### <a name="access-enforcement"></a>Application de l’accès

**AC-3** Le système d’information applique les autorisations approuvées pour l’accès logique aux informations et aux ressources du système, conformément aux stratégies de contrôle d’accès applicables.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint utilise des autorisations d’accès logique à l’aide du contrôle d’accès en fonction du rôle appliqué par Azure Active Directory en assignant des rôles aux utilisateurs, par Active Directory en assignant les utilisateurs à des groupes de sécurité et par les contrôles de niveau système d’exploitation Windows. Les rôles Active Directory Azure assignés aux utilisateurs ou groupes permettent de contrôler l’accès logique aux ressources dans Azure au niveau de la ressource, du groupe ou de l’abonnement. Les groupes de sécurité Active Directory contrôlent l’accès logique aux fonctions et ressources de niveau système d’exploitation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800-53 Contrôle AC-4

#### <a name="information-flow-enforcement"></a>Application du flux d’informations

**AC-4** Le système d’information applique les autorisations approuvées pour contrôler le flux des informations au sein du système et entre les systèmes interconnectés en fonction des [Affectation : stratégies de contrôle du flux des informations définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint impose des restrictions au flux d’informations via des groupes de sécurité réseau appliqués aux sous-réseaux dans lesquels les ressources sont déployées, à Application Gateway et à l’équilibreur de charge. Les groupes de sécurité réseau veillent à ce que le flux d’informations soit contrôlé entre les ressources en fonction des règles approuvées. Application Gateway et l’équilibreur de charge acheminent dynamiquement le trafic vers des ressources spécifiques en fonction des rôles approuvés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 Contrôle AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Application du flux d’informations | Filtres de stratégie de sécurité

**AC-4 (8)** Le système d’information applique le contrôle des flux d’informations à l’aide de [Affectation : filtres de stratégie de sécurité définis par l’organisation] comme base des décisions de contrôle des flux pour les [Affectation : flux d’informations définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’application du contrôle des flux d’informations au sein des ressources qu’il a déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 Contrôle AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Application du flux d’informations | Séparation physique/logique des flux d’informations

**AC-4 (21)** Le système d’information sépare logiquement ou physiquement les flux d’information à l’aide de [Affectation : mécanismes et/ou techniques définis par l’organisation] pour accomplir des [Affectation : séparations obligatoires définies par l’organisation et par type d’information].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la séparation des flux d’informations au sein des ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800-53 Contrôle AC-5.a

#### <a name="separation-of-duties"></a>Séparation des tâches

**AC-5.a** L’organisation sépare les [Affectation : tâches des individus définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la séparation des tâches entre les comptes qu’il contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800-53 Contrôle AC-5.b

#### <a name="separation-of-duties"></a>Séparation des tâches

**AC-5.b** L’organisation présente la séparation des tâches des individus.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la présentation de la séparation des tâches entre les comptes qu’il contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800-53 Contrôle AC-5.c

#### <a name="separation-of-duties"></a>Séparation des tâches

**AC-5.c** L’organisation définit les autorisations d’accès au système d’information pour prendre en charge la séparation des tâches.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente un contrôle d’accès en fonction du rôle qui peut être configuré pour séparer les tâches en fonction des exigences de l’organisation. Les privilèges de compte Azure Active Directory sont implémentés en utilisant le contrôle d’accès en fonction du rôle en assignant les utilisateurs à des rôles ; les privilèges de compte Active Directory sont implémentés en utilisant le contrôle d’accès en fonction du rôle en assignant les utilisateurs à des groupes de sécurité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800-53 Contrôle AC-6

#### <a name="least-privilege"></a>Privilège minimum

**AC-6** L’organisation applique le principe du privilège minimum, n’autorisant que les accès autorisés pour les utilisateurs (ou les processus agissant au nom des utilisateurs) qui sont nécessaires pour accomplir les tâches assignées conformément aux missions de l’organisation et aux fonctions opérationnelles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le contrôle d’accès en fonction du rôle pour limiter les utilisateurs aux seuls privilèges explicitement attribués. Les privilèges de compte Azure Active Directory sont implémentés en utilisant le contrôle d’accès en fonction du rôle en assignant les utilisateurs à des rôles ; les privilèges de compte Active Directory sont implémentés en utilisant le contrôle d’accès en fonction du rôle en assignant les utilisateurs à des groupes de sécurité.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 Contrôle AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Privilège minimum | Autoriser l’accès aux fonctions de sécurité

**AC-6 (1)** L’organisation autorise explicitement l’accès aux [Affectation : fonctions de sécurité définies par l’organisation (déployées dans le matériel, les logiciels et les microprogrammes) et aux informations relatives à la sécurité].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès au niveau de l’entreprise du client peuvent établir un processus d’autorisation d’accès qui comprend l’accès aux fonctions de sécurité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 Contrôle AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Privilège minimum | Accès non privilégié aux fonctions non sécuritaires

**AC-6 (2)** L’organisation exige que les utilisateurs de comptes ou de rôles du système d’information qui ont accès à des [Affectation : fonctions de sécurité définies par l’organisation ou informations pertinentes pour la sécurité] utilisent des comptes ou des rôles non privilégiés lorsqu’ils accèdent à des fonctions non sécuritaires.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut exiger que les utilisateurs utilisent des comptes non privilégiés lorsqu’ils accèdent à des fonctions non sécuritaires. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 Contrôle AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Privilège minimum | Accès réseau aux commandes privilégiées

**AC-6 (3)** L’organisation autorise l’accès réseau aux [Affectation : commandes privilégiées définies par l’organisation] uniquement pour les [Affectation : besoins opérationnels impérieux définis par l’organisation] et présente un argument en faveur de cet accès dans le plan de sécurité du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut définir des commandes privilégiées accessibles sur un réseau. Remarque : les clients n’ont pas d’accès physique à l’infrastructure Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 Contrôle AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Privilège minimum | Comptes privilégiés

**AC-6 (5)** L’organisation limite les comptes privilégiés du système d’information aux [Affectation : membres du personnel ou rôles définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut définir des restrictions d’utilisation des comptes privilégiés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 Contrôle AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>Privilèges minimum | Révision des privilèges utilisateur

**AC-6 (7).a** L’organisation révise [Affectation : fréquence définie par l’organisation] les privilèges attribués aux [Affectation : rôles ou catégories d’utilisateurs définis par l’organisation] pour valider la nécessité de ces privilèges.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’examen des privilèges utilisateur des comptes qu’il contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 Contrôle AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>Privilèges minimum | Révision des privilèges utilisateur

**AC-6 (7).b** L’organisation réaffecte ou supprime les privilèges, le cas échéant, pour refléter correctement la mission ou les besoins organisationnels.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le cas échéant, le client est responsable de la réaffectation ou de la suppression des privilèges pour les comptes qu’il contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 Contrôle AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Privilège minimum | Niveaux de privilège pour l’exécution du code

**AC-6 (8)** Le système d’information empêche l’exécution des [Affectation : logiciels définis par l’organisation] à des niveaux de privilèges supérieurs à ceux des utilisateurs exécutant le logiciel.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le contrôle d’accès en fonction du rôle pour limiter les utilisateurs aux seuls privilèges explicitement attribués. Les protections au niveau du système d’exploitation de la machine virtuelle ne permettent pas aux logiciels de s’exécuter à un niveau de privilèges supérieur à celui des utilisateurs exécutant le logiciel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 Contrôle AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Privilège minimum | Audit de l’utilisation des fonctions privilégiées

**AC-6 (9)** Le système d’information effectue un audit de l’exécution des fonctions privilégiées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le service Log Analytics dans OMS. Les machines virtuelles et les comptes de stockage de diagnostic Azure déployés sont connectés à Log Analytics, garantissant ainsi l’audit de l’exécution des fonctions privilégiées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 Contrôle AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Privilège minimum | Interdire aux utilisateurs non privilégiés d’exécuter des fonctions privilégiées

**AC-6 (10)** Le système d’information empêche les utilisateurs non privilégiés d’exécuter des fonctions privilégiées telles que la désactivation, le contournement ou la modification des mesures de sécurité/contre-mesures mises en œuvre.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint implémente le contrôle d’accès en fonction du rôle pour limiter les utilisateurs aux seuls privilèges explicitement attribués.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800-53 Contrôle AT-7.b

#### <a name="unsuccessful-logon-attempts"></a>Essais de connexion infructueux

**AC-7.a** Le système d’information impose une limite de [Affectation : nombre défini par l’organisation] tentatives consécutives de connexion non valides d’un utilisateur pendant une [Affectation : période définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le portail Azure limite les tentatives de connexion non valides consécutives des utilisateurs. Une stratégie de groupe est appliquée au niveau du système d’exploitation pour toutes les machines virtuelles déployées par ce programme Blueprint. La stratégie limite les tentatives de connexion non valides consécutives par les utilisateurs à un maximum de 3 dans un délai de 15 minutes. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800-53 Contrôle AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Essais de connexion infructueux

**AC-7.b** Le système d’information [Sélection : verrouille le compte/nœud pour une [Affectation : période définie par l’organisation] ; verrouille le compte/nœud jusqu’à ce qu’il soit libéré par un administrateur ; retarde l’invite de prochaine connexion selon [Affectation : algorithme de délai défini par l’organisation]] automatiquement lorsque le nombre maximum de tentatives infructueuses est dépassé.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le portail Azure verrouille les comptes après des tentatives de connexion non valides consécutives des utilisateurs. Une stratégie de groupe est appliquée au niveau du système d’exploitation pour toutes les machines virtuelles déployées par ce programme Blueprint. La stratégie verrouille les comptes durant 3 heures après 3 tentatives de connexion non valides consécutives des utilisateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 Contrôle AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Essais de connexion infructueux | Purger/effacer le contenu de l’appareil mobile

**AC-7 (2)** Le système d’information purge et efface les informations des [Affectation : appareils mobiles définis par l’organisation] en fonction des [Affectation : exigences et techniques définies par l’organisation] après [Affectation : nombre défini par l’organisation] tentatives infructueuses consécutives de connexion à l’appareil.

**Responsabilités :** `Not Applicable`

|||
|---|---|
| **Client** | Les appareils mobiles ne font pas partie des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure n’autorise pas les appareils mobiles à l’intérieur de la limite d’Azure. Par conséquent, ce contrôle n’est pas applicable à Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800-53 Contrôle AC-8.a

#### <a name="system-use-notification"></a>Notification d’utilisation du système

**AC-8.a** Le système d’information affiche aux utilisateurs [Affectation : message de notification ou bannière utilisée par le système défini par l’organisation] avant d’accorder l’accès au système d’envoi de notifications de confidentialité et de sécurité conformes aux lois fédérales applicables, aux décrets exécutifs, aux directives, aux stratégies, aux règlements, aux normes et aux conseils, et indique que les utilisateurs ont accès à un système d’information des États-Unis ; Système d’information du gouvernement ; l’utilisation du système d’information peut être surveillée, enregistrée et vérifiée ; l’utilisation non autorisée du système d’information est interdite et passible de sanctions pénales et civiles ; et l’utilisation du système d’information implique le consentement à la surveillance et à l’enregistrement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe implémente une notification d’utilisation du système qui est affichée aux utilisateurs avant la connexion. Remarque : ce programme Blueprint implémente un exemple de notification d’utilisation du système. Le client doit modifier ce texte pour répondre aux exigences de l’organisation et/ou de l’organisme de réglementation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800-53 Contrôle AC-8.b

#### <a name="system-use-notification"></a>Notification d’utilisation du système

**AC-8.b** Le système d’information continue d’afficher le message ou la bannière de notification à l’écran jusqu’à ce que les utilisateurs acceptent les conditions d’utilisation et prennent des mesures explicites pour se connecter ou accéder au système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe implémente une notification d’utilisation du système qui est affichée aux utilisateurs avant leur connexion. L’utilisateur doit accepter la notification pour se connecter. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800-53 Contrôle AC-8.c

#### <a name="system-use-notification"></a>Notification d’utilisation du système

**AC-8.c** Le système d’information des systèmes accessibles publiquement affiche l’information sur l’utilisation du système [Affectation : conditions définies par l’organisation], avant d’accorder un accès supplémentaire ; affiche les références, le cas échéant, à la surveillance, à l’enregistrement ou à la vérification qui sont conformes aux mesures d’adaptation en matière de protection de la vie privée pour de tels systèmes qui interdisent généralement ces activités ; et comprend une description des utilisations autorisées du système.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’affichage d’une notification d’utilisation du système sur toutes les ressources qu’il déploie accessibles publiquement. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800-53 Contrôle AC-10

#### <a name="concurrent-session-control"></a>Contrôle des sessions simultanées

**AC-10** Le système d’information limite le nombre de sessions simultanées pour chaque [Affectation : compte défini par l’organisation et/ou type de compte] à [Affectation : nombre défini par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Une stratégie de système d’exploitation est implémentée pour les machines virtuelles déployées par ce programme Blueprint. La stratégie implémente des limitations de sessions simultanées (2 sessions). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800-53 Contrôle AC-11.a

#### <a name="session-lock"></a>Verrouillage de session

**AC-11.a** Le système d’information empêche tout accès ultérieur au système en déclenchant un verrouillage de session après [Affectation : période d’inactivité définie par l’organisation] ou à la demande d’un utilisateur.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe implémente un verrouillage d’inactivité pour les sessions de protocole RDP (Remote Desktop Protocol). Les utilisateurs peuvent déclencher manuellement le verrouillage. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800-53 Contrôle AC-11.b

#### <a name="session-lock"></a>Verrouillage de session

**AC-11.b** Le système d’information maintient le verrouillage de session jusqu’à ce que l’utilisateur rétablisse l’accès en suivant les procédures d’identification et d’authentification établies.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe implémente un verrouillage d’inactivité pour les sessions de protocole RDP (Remote Desktop Protocol). Les utilisateurs doivent se réauthentifier pour déverrouiller la session.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 Contrôle AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Verrouillage de session | Masquage des informations à l’aide d’une image

**CA-11 (1)** Le système d’information dissimule, via le verrouillage de session, les informations précédemment visibles à l’écran avec une image visible publiquement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe implémente un verrouillage d’inactivité pour les sessions de protocole RDP (Remote Desktop Protocol). Le verrouillage de session masque les informations précédemment visibles. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800-53 Contrôle AC-12

#### <a name="session-termination"></a>Arrêt de session

**AC-12** Le système d’information arrête automatiquement une session utilisateur après [Affectation : conditions ou événements déclenchant la déconnexion de la session définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La configuration de l’hôte de session de Bureau à distance pour les machines virtuelles Windows déployées par ce programme Blueprint peut être paramétrée pour répondre aux exigences d’arrêt de session de l’organisation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800-53 Contrôle AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Arrêt de session | Déconnexions/affichages de messages initié(e)s par l’utilisateur

**AC-12 (1).a** Le système d’information fournit une fonctionnalité de déconnexion des sessions de communications initiées par l’utilisateur lorsque l’authentification est utilisée pour accéder aux [Affectation : ressources d’informations définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le portail Azure et les systèmes d’exploitation de machines virtuelles déployés par ce programme Blueprint permettent d’initier une déconnexion. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800-53 Contrôle AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Arrêt de session | Déconnexions/affichages de messages initié(e)s par l’utilisateur

**AC-12 (1).b** Le système d’information affiche un message de déconnexion explicite aux utilisateurs indiquant l’arrêt fiable des sessions de communications authentifiées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le portail Azure et les systèmes d’exploitation de machines virtuelles déployés par ce programme Blueprint permettent d’initier une déconnexion. Le processus de déconnexion indique aux utilisateurs que la session a été arrêtée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800-53 Contrôle AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Actions autorisées sans identification ou authentification

**AC-14.a** L’organisation identifie les [Affectation : actions de l’utilisateur définies par l’organisation] qui peuvent être exécutées sur le système d’information sans identification ni authentification, conformément aux missions organisationnelles ou aux fonctions métier.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de l’identification des actions qui peuvent être effectuées sans identification ni authentification sur les ressources qu’il déploie (par exemple la consultation d’une page web accessible publiquement). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800-53 Contrôle AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Actions autorisées sans identification ou authentification

**AC-14.b** L’organisation détaille et justifie les actions des utilisateurs ne nécessitant pas d’identification ou d’authentification dans la conformité du plan de sécurité du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la documentation des actions de l’utilisateur qui ne nécessitent pas d’identification ou d’authentification sur les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800-53 Contrôle AC-17.a

#### <a name="remote-access"></a>Accès à distance

**AC-17.a** L’organisation établit et documente les restrictions d’utilisation, les exigences en matière de configuration et de connexion et les directives d’implémentation pour chaque type d’accès à distance autorisé.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut définir des restrictions d’accès à distance. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800-53 Contrôle AC-17.b

#### <a name="remote-access"></a>Accès à distance

**AC-17.b** L’organisation autorise l’accès à distance au système d’information avant d’autoriser de telles connexions.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès du client au niveau de l’entreprise peuvent établir un processus d’autorisation d’accès à distance. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 Contrôle AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Accès à distance | Contrôle/surveillance automatique

**AC-17 (1)** Le système d’information surveille et contrôle les méthodes d’accès à distance.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint offre un accès à distance au système d’information via le portail Azure, via une connexion Bureau à distance passant par un serveur de rebond, et via une application web implémentée par le client. Les accès via le portail Azure et les sessions Bureau à distance sont audités et peuvent être surveillés via OMS. Le cas échéant, le client doit mettre en place des contrôles d’accès à distance à l’application web. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 Contrôle AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Accès à distance | Protection de la confidentialité/de l’intégrité à l’aide du chiffrement

**AC-17 (2)** Le système d’information met en œuvre des mécanismes de chiffrement pour protéger la confidentialité et l’intégrité des sessions d’accès à distance.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès à distance aux ressources déployées par ce programme Blueprint, y compris le portail Azure, la connexion Bureau à distance et la passerelle d’application web, est sécurisé par TLS. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 Contrôle AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Accès à distance | Points de contrôle d’accès gérés

**AC-17 (3)** Le système d’information achemine tous les accès à distance par [Affectation : nombre défini par l’organisation] points de contrôle d’accès au réseau gérés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès à distance à l’application web notionnelle déployée par ce programme Blueprint se fait via une passerelle d’application. L’accès à distance à toutes les autres ressources se fait par un serveur de rebond. Il n’existe aucun autre point de terminaison accessible publiquement. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 Contrôle AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>Accès à distance | Commandes privilégiées/accès

**AC-17 (4).a** L’organisation autorise uniquement l’exécution de commandes privilégiées et l’accès à des informations relatives à la sécurité via un accès à distance dans le cadre de [Affectation : besoins définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut définir des commandes privilégiées accessibles à distance et comprendre un raisonnement. Remarque : les clients n’ont aucun accès direct au réseau vers une infrastructure Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 Contrôle AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>Accès à distance | Commandes privilégiées/accès

**AC-17 (4).b** L’organisation présente l’argument d’un tel accès dans son plan de sécurité du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut définir des commandes privilégiées accessibles à distance et comprendre un raisonnement. Remarque : les clients n’ont aucun accès direct au réseau vers une infrastructure Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 Contrôle AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Accès à distance | Déconnexion/désactivation de l’accès

**AC-17 (9)** L’organisation offre la capacité de déconnecter ou de désactiver rapidement l’accès à distance au système d’information dans un délai de [Affectation : période définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint offre un accès à distance au système d’information via le portail Azure, via une connexion Bureau à distance passant par un serveur de rebond, et via une application web. Si un compte Azure Active Directory est désactivé ou supprimé, l’accès au portail Azure est immédiatement déconnecté. De même, si un compte au niveau du système d’exploitation d’une machine virtuelle est désactivé ou supprimé, l’accès au Bureau à distance passant par le serveur de rebond est immédiatement déconnecté. Les clients doivent implémenter la déconnexion d’accès à distance pour l’application web. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800-53 Contrôle AC-18.a

#### <a name="wireless-access"></a>Accès sans fil

**AC-18.a** L’organisation établit des restrictions d’utilisation, des exigences en matière de configuration et de connexion ainsi que des directives de mise en œuvre pour l’accès sans fil.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’y a pas d’accès sans fil dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure établit des restrictions d’utilisation, des exigences de configuration/connexion et des directives d’implémentation pour l’accès sans fil via le standard de sécurité réseau, qui interdit explicitement l’utilisation du sans-fil dans l’environnement Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800-53 Contrôle AC-18.b

#### <a name="wireless-access"></a>Accès sans fil

**AC-18.b** L’organisation autorise l’accès sans fil au système d’information avant d’autoriser de telles connexions.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’y a pas d’accès sans fil dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure ne permet pas l’accès sans fil au sein des centres de données Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 Contrôle AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Accès sans fil | Authentification et chiffrement

**AC-18 (1)** Le système d’information protège l’accès sans fil au système par authentification des [Sélection (un ou plusieurs) : utilisateurs ; dispositifs] et chiffrement.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’y a pas d’accès sans fil dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure ne permet pas l’accès sans fil dans l’environnement Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 Contrôle AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Accès sans fil | Désactiver la mise en réseau sans fil

**AC-18 (3)** L’organisation désactive, lorsqu’elle ne sont pas conçues pour être utilisée, les fonctionnalités de mise en réseau sans fil intégrées à l’intérieur des composants du système d’information avant la délivrance et le déploiement.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’y a pas d’accès sans fil dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure ne permet pas l’accès sans fil dans l’environnement Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 Contrôle AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Accès sans fil | Restriction des configurations par les utilisateurs

**AC-18 (4)** L’organisation identifie et autorise explicitement les utilisateurs autorisés à configurer de manière indépendante les fonctionnalités de mise en réseau sans fil.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’y a pas d’accès sans fil dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure ne permet pas l’accès sans fil dans l’environnement Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 Contrôle AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Accès sans fil | Antennes/Niveaux de puissance de transmission

**AC-18 (5)** L’organisation sélectionne les antennes radio et étalonne les niveaux de puissance de transmission pour réduire la probabilité que les signaux utilisables puissent être reçus en dehors des limites contrôlées par l’organisation.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’y a pas d’accès sans fil dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure ne permet pas l’accès sans fil dans l’environnement Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800-53 Contrôle AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Contrôle d’accès pour les appareils mobiles

**AC-19.a** L’organisation établit des restrictions d’utilisation, des exigences de configuration, des exigences de connexion et des directives d’implémentation pour les appareils mobiles qu’elle contrôle.

**Responsabilités :** `Not Applicable`

|||
|---|---|
| **Client** | Il n’existe aucun appareil mobile contrôlé par le client dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure n’autorise pas les appareils mobiles à l’intérieur de la limite d’Azure. Par conséquent, ce contrôle n’est pas applicable à Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800-53 Contrôle AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Contrôle d’accès pour les appareils mobiles

**AC-19.b** L’organisation autorise la connexion des appareils mobiles aux systèmes d’information de l’organisation.

**Responsabilités :** `Not Applicable`

|||
|---|---|
| **Client** | Il n’existe aucun appareil mobile contrôlé par le client dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure n’autorise pas les appareils mobiles à l’intérieur de la limite d’Azure. Par conséquent, ce contrôle n’est pas applicable à Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 Contrôle AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Contrôle d’accès pour les appareils mobiles | Chiffrement complet de l’appareil/du conteneur

**CA-19 (5)** L’organisation utilise un [Sélection : chiffrement du dispositif complet ; chiffrement du conteneur] pour protéger la confidentialité et l’intégrité des informations sur ses [Affectation : appareils mobiles définis par l’organisation].

**Responsabilités :** `Not Applicable`

|||
|---|---|
| **Client** | Il n’existe aucun appareil mobile contrôlé par le client dans le cadre des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure n’autorise pas les appareils mobiles à l’intérieur de la limite d’Azure. Par conséquent, ce contrôle n’est pas applicable à Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800-53 Contrôle AC-20.a

#### <a name="use-of-external-information-systems"></a>Utilisation de systèmes d’information externes

**AC-20.a** Conformément à toute relation d’approbation établie avec d’autres organisations qui possèdent, exploitent ou assurent la maintenance de systèmes d’information externes, l’organisation établit les conditions générales qui permettent aux personnes autorisées d’accéder au système d’information à partir de systèmes d’information externes.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut inclure une disposition concernant l’utilisation des offres de services cloud dans le cadre de FedRAMP. Azure a obtenu une autorisation provisoire d’exploitation (P-ATO) de la part du Joint Authorization Board (JAB) de la FedRAMP permettant aux agences gouvernementales d’acquérir des services cloud Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800-53 Contrôle AC-20.b

#### <a name="use-of-external-information-systems"></a>Utilisation de systèmes d’information externes

**AC-20.b** Conformément à toute relation d’approbation établie avec d’autres organisations qui possèdent, exploitent ou assurent la maintenance de systèmes d’information externes, l’organisation établit les conditions générales qui permettent aux personnes autorisées de traiter, de stocker ou de transmettre des informations contrôlées par l’organisation au moyen de systèmes d’information externes.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut inclure une disposition concernant l’utilisation des offres de services cloud dans le cadre de FedRAMP. Azure a obtenu une autorisation provisoire d’exploitation (P-ATO) de la part du Joint Authorization Board (JAB) de la FedRAMP permettant aux agences gouvernementales d’acquérir des services cloud Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 Contrôle AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Utilisation de systèmes d’information externes | Limites d’utilisation autorisée

**AC-20 (1)** L’organisation permet aux personnes autorisées d’utiliser un système d’information externe pour accéder au système d’information ou pour traiter, stocker ou transmettre des informations contrôlées par l’organisation uniquement lorsque l’organisation vérifie la mise en œuvre des contrôles de sécurité requis sur le système externe, tel qu’il est précisé dans la stratégie et le plan de sécurité de l’organisation en matière de sécurité des informations ; ou lorsqu’elle a établi des accords de connexion ou de traitement du système d’information valides avec l’entité organisationnelle qui héberge le système d’information externe.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le groupe de technologie de l’information au niveau de l’entreprise du client peut vérifier la conformité des fournisseurs de services cloud avec les exigences de sécurité des informations de l’entreprise et accorder l’autorisation d’utiliser les offres de services cloud associées. Azure a obtenu une autorisation provisoire d’exploitation (P-ATO) de la part du Joint Authorization Board (JAB) de la FedRAMP. Azure est évalué par un organisme d’évaluation tiers approuvé par FedRAMP (3PAO) pour vérifier la conformité avec le contrôle de la sécurité FedRAMP et d’autres exigences. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 Contrôle AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Utilisation de systèmes d’informations externes | Appareils de stockage portables

**AC-20 (2)** L’organisation [Sélection : restreint ; interdit] l’utilisation d’appareils de stockage portables contrôlés par l’organisation par des personnes autorisées sur des systèmes d’information externes.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft n’autorise pas les appareils de stockage portables contrôlés par le client dans l’environnement Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800-53 Contrôle AC-21.a

#### <a name="information-sharing"></a>Partage d’informations

**AC-21.a** L’organisation facilite le partage d’informations en permettant aux utilisateurs autorisés de déterminer si les autorisations d’accès attribuées au partenaire de partage correspondent aux restrictions d’accès aux informations pour [Affectation : circonstances de partage de l’information définies par l’organisation lorsque la discrétion de l’utilisateur est requise].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de contrôle d’accès du client au niveau de l’entreprise peut inclure des dispositions relatives au partage d’informations. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 Contrôle AC-21.b

#### <a name="information-sharing"></a>Partage d’informations

**AC-21.b** L’organisation a recours à des [Affectation : mécanismes automatisés ou processus manuels définis par l’organisation] pour aider les utilisateurs à prendre des décisions concernant le partage d’informations et la collaboration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut s’appuyer sur une fonctionnalité d’aide à la décision au niveau de l’entreprise. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800-53 Contrôle AC-22.a

#### <a name="publicly-accessible-content"></a>Contenu accessible publiquement

**AC-22.a** L’organisation désigne les personnes autorisées à publier des informations sur un système d’information accessible publiquement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès au niveau de l’entreprise du client peuvent désigner des personnes autorisées à publier des informations accessibles publiquement. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800-53 Contrôle AC-22.b

#### <a name="publicly-accessible-content"></a>Contenu accessible publiquement

**AC-22.b** L’organisation forme les personnes autorisées à vérifier que les informations accessibles publiquement ne contiennent pas d’informations non publiques.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut compter sur une formation au niveau de l’entreprise des personnes autorisées à publier des informations accessibles publiquement. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800-53 Contrôle AC-22.c

#### <a name="publicly-accessible-content"></a>Contenu accessible publiquement

**AC-22.c** L’organisation examine les informations proposées avant de les afficher dans le système d’information accessible publiquement, pour vérifier l’absence de toute information non publique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès du client au niveau de l’entreprise peuvent établir un processus d’examen du contenu en attente de publication dans un système accessible publiquement. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800-53 Contrôle AC-22.d

#### <a name="publicly-accessible-content"></a>Contenu accessible publiquement

**AC-22.d** L’organisation examine le contenu du système d’information accessible publiquement en recherchant la moindre information non publique tous les [Affectation : fréquence définie par l’organisation] et supprime ces informations, le cas échéant.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de contrôle d’accès du client au niveau de l’entreprise peuvent établir un processus d’examen périodique du contenu publié sur les systèmes accessibles publiquement. |
| **Fournisseur (Microsoft Azure)** | Non applicable |

