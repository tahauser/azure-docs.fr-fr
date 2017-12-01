---
title: "Solution FedRAMP Azure Blueprint Automation - Audit et responsabilité"
description: "Applications web pour FedRAMP - Audit et responsabilité"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>Audit et responsabilité (AU)

> [!NOTE]
> Ces contrôles sont définis par l’Institut national des normes et de la technologie (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication NIST 800-53 Rév. 4.

## <a name="nist-800-53-control-au-1"></a>NIST 800-53 Contrôle AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Stratégie et procédures d’audit et de responsabilité

**AU-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles définis par l’organisation] une stratégie d’audit et de responsabilité qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie d’audit et de responsabilité et des contrôles d’audit et de responsabilité associés ; et révise et met à jour la stratégie actuelle d’audit et de responsabilité [Affectation : fréquence définie par l’organisation] ; et les procédures d’audit et de responsabilité [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures d’audit et de sécurité de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-2a"></a>NIST 800-53 Control AU-2.a

#### <a name="audit-events"></a>Événements d’audit

**AU-2.a** L’organisation détermine que le système d’information est capable d’auditer les événements suivants : [Affectation : événements auditables définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La fonctionnalité d’audit de cette solution Azure Blueprint est fournie par Azure Monitor et le service Log Analytics dans OMS. Azure Monitor fournit des journaux d’audit détaillés sur l’activité associée aux ressources déployées. Ces journaux ainsi que les journaux de système d’exploitation sont collectés par Log Analytics et stockés dans le dépôt OMS. Log Analytics met en corrélation les données d’audit des ressources déployées par cette solution et peut être étendu à l’application web déployée par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-2b"></a>NIST 800-53 Contrôle AU-2.b

#### <a name="audit-events"></a>Événements d’audit

**AU-2.b** L’organisation coordonne la fonction d’audit de la sécurité avec d’autres entités organisationnelles nécessitant des informations d’audit pour améliorer la prise en charge mutuelle et guider la sélection des événements auditables.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut compter sur un processus établi au niveau de l’entreprise qui détermine les événements auditables. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-2c"></a>NIST 800-53 Contrôle AU-2.c

#### <a name="audit-events"></a>Événements d’audit

**AU-2.c** L’organisation fournit une logique expliquant la raison pour laquelle les événements auditables sont considérés comme suffisants pour prendre en charge des investigations qui ont eu lieu après des incidents de sécurité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les événements audités par cette solution Azure Blueprint incluent des informations suffisantes pour déterminer à quel moment se produisent les événements, la source de l’événement et les conséquences de l’événement. Ils offrent également des informations détaillées utiles pour l’examen des incidents de sécurité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-2d"></a>NIST 800-53 Contrôle AU-2.d

#### <a name="audit-events"></a>Événements d’audit

**AU-2.d** L’organisation détermine que les événements suivants doivent être audités au sein du système d’information : [Affectation : événements audités définis par l’organisation (le sous-ensemble d’événements auditables défini à l’alinéa AU-2), ainsi que la fréquence de (ou la situation nécessitant) l’audit de chaque événement identifié].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les événements audités par cette solution Azure Blueprint incluent ceux audités par les journaux d’activité Azure pour les ressources déployées, les journaux de niveau système d’exploitation, les journaux Active Directory et les journaux SQL Server. Les clients peuvent sélectionner d’autres événements à auditer selon les besoins de leur mission. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 Contrôle AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Événements d’audit | Mises à jour et révisions

**AU-2 (3)** L’organisation révise et met à jour les événements audités [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut compter sur un processus de mise à jour et de révision périodique de niveau entreprise pour l’ensemble défini d’événements audités. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-3"></a>NIST 800-53 Contrôle AU-3

#### <a name="content-of-audit-records"></a>Contenu des enregistrements d’audit

**AU-3** Le système d’information génère des enregistrements d’audit contenant des informations qui établissent le type d’événement qui s’est produit, à quel moment et où l’événement s’est produit, la source de l’événement, les conséquences de l’événement et l’identité des personnes ou sujets associés à l’événement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint s’appuie sur les fonctionnalités d’audit intégrées d’Azure, Windows Server et SQL Server. Ces solutions d’audit capturent les enregistrements d’audit avec un niveau de détail suffisant pour satisfaire aux exigences de ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 Contrôle AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Contenu des enregistrements d’audit | Informations d’audit supplémentaires

**AU-3 (1)** Le système d’information génère des enregistrements d’audit contenant les informations supplémentaires suivantes : [Affectation : informations supplémentaires plus détaillées définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les événements de journal d’activité Azure utilisent un schéma détaillé qui contient les champs de plus de 20 types d’informations d’audit. En plus du journal d’activité, cette solution Azure Blueprint déploie la solution Log Analytics dans OMS qui prend en charge un ensemble diversifié de sources de données, y compris des journaux Windows, des journaux Linux, des journaux de diagnostic Azure et les journaux des clients.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 Contrôle AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Contenu des enregistrements d’audit | Gestion centralisée du contenu planifié des enregistrements d’audit

**AU-3 (2)** Le système d’information fournit une gestion et une configuration centralisées du contenu à capturer dans les enregistrements d’audit générés par [Affectation : composants de système d’information définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Toutes les machines virtuelles déployées par cette solution Azure Blueprint sont jointes au domaine Active Directory déployé. Toutes les machines virtuelles jointes au domaine implémentent une stratégie de groupe qui peut être configurée pour gérer de manière centralisée la configuration du système d’audit au niveau du système d’exploitation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-4"></a>NIST 800-53 Contrôle AU-4

#### <a name="audit-storage-capacity"></a>Capacité de stockage de l’audit

**AU-4** L’organisation alloue la capacité de stockage des enregistrements d’audit conformément aux [Affectation : exigences de stockage des enregistrements d’audit définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint alloue une capacité de stockage suffisante pour conserver les enregistrements d’audit pendant 1 an. Tous les enregistrements d’audit sont collectés par Log Analytics qui est configuré pour une rétention de 1 an. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-5a"></a>NIST 800-53 Contrôle AT-5.a

#### <a name="response-to-audit-processing-failures"></a>Réponse aux échecs du processus d’audit

**AU-5.a** Le système d’information envoie une alerte à [Affectation : personnel ou rôles définis par l’organisation] en cas d’échec du processus d’audit.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le statut d’Azure Monitor et de Log Analytics est disponible sur le site web répertoriant les statuts Azure et le panneau de contrôle d’intégrité du service dans le portail Azure. Des alertes peuvent être configurées via Log Analytics pour fournir une notification relative à d’autres types d’échecs du processus d’audit. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-5b"></a>NIST 800-53 Contrôle AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Réponse aux échecs du processus d’audit

**AU-5.b** Le système d’information effectue les actions supplémentaires suivantes : [Affectation : actions définies à effectuer par l’organisation (par exemple, arrêter le système d’information, remplacer les enregistrements d’audit les plus anciens, arrêter la génération des enregistrements d’audit)].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Tous les enregistrements d’audit générés par les ressources déployées par cette solution Azure Blueprint sont collectés par Log Analytics et conservés pendant une période de 1 an. L’allocation de mémoire pour ce stockage des enregistrements d’audit est allouée dynamiquement afin de garantir une capacité suffisante. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 Contrôle AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Réponse aux échecs du processus d’audit | Capacité de stockage de l’audit

**AU-5 (1)** Le système d’information envoie un avertissement à [Affectation : personnel, rôles et/ou emplacements définis par l’organisation] dans un délai de [Affectation : période définie par l’organisation] lorsque le volume alloué au stockage des enregistrements d’audit atteint [Affectation : pourcentage défini par l’organisation] de la capacité de stockage des enregistrements d’audit maximal du dépôt.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Tous les enregistrements d’audit générés par les ressources déployées par cette solution Azure Blueprint sont collectés par Log Analytics et conservés pendant une période de 1 an. L’allocation de mémoire pour ce stockage des enregistrements d’audit est allouée dynamiquement afin de garantir une capacité suffisante. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 Contrôle AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Réponse aux échecs du processus d’audit | Alertes en temps réel

**AU-5 (2)** Le système d’information fournit une alerte dans [Affectation : période en temps réel définie par l’organisation] à [Affectation : personnel, rôles et/ou emplacements définis par l’organisation] quand les événements d’échec d’audit suivants se produisent : [Affectation : événements d’échec d’audit définis par l’organisation et nécessitant des alertes en temps réel].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le statut des services Azure est disponible dans le panneau de contrôle d’intégrité du service dans le portail Azure. Des alertes peuvent être configurées via Log Analytics pour fournir une notification relative à d’autres types d’échecs du processus d’audit. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-6a"></a>NIST 800-53 Contrôle AT-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Révision, analyse et rapports d’audit

**AU-6.a** L’organisation révise et analyse les enregistrements d’audit des systèmes d’information [Affectation : fréquence définie par l’organisation] en cas d’occurrence de [Affectation : activité inhabituelle ou inappropriée définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de réviser et d’analyser les enregistrements d’audit des ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800-53 Contrôle AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Révision, analyse et rapports d’audit

**AU-6.b** L’organisation transmet ses découvertes à [Affectation : personnel ou rôles définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de signaler les cas d’activité inappropriée ou inhabituelle (définis à l’alinéa AU-06.a) sur les ressources qu’il a déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 Contrôle AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Révision, analyse et rapports d’audit | Intégration du processus

**AU-6 (1)** L’organisation utilise des mécanismes automatisés pour intégrer les processus de révision, d’analyse et de rapports d’audit afin de prendre en charge des processus organisationnels, d’enquêter sur les activités suspectes et d’y apporter une réponse.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut s’appuyer sur des fonctionnalités centralisées de révision, d’analyse et de rapports de niveau entreprise. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 Contrôle AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Révision, analyse et rapports d’audit | Mettre en corrélation les dépôts d’audit

**AU-6 (3)** L’organisation met en corrélation et analyse des enregistrements d’audit dans différents dépôts pour prendre connaissance de la situation à l’échelle de l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente la solution Log Analytics dans OMS pour centraliser les données d’audit sur les ressources déployées, en prenant en charge la connaissance de la situation à l’échelle de l’organisation. Les clients peuvent choisir d’intégrer Log Analytics avec d’autres systèmes. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 Contrôle AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Révision, analyse et rapports d’audit | Révision et analyse centralisées

**AU-6 (4)** Le système d’information permet de réviser et d’analyser de façon centralisée des enregistrements d’audit provenant de plusieurs composants au sein du système.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente la solution Log Analytics dans OMS pour centraliser les données d’audit sur les ressources déployées, en prenant en charge la révision, l’analyse et la création de rapports. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 Contrôle AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Révision, analyse et rapports d’audit | Intégration/analyse et fonctionnalités de surveillance

**AU-6 (5)** L’organisation intègre l’analyse des enregistrements d’audit à l’analyse de [Sélection (un ou plusieurs) : informations d’analyse des vulnérabilités, données de performances, informations de surveillance du système d’information, [Affectation : informations/données définies par l’organisation et collectées à partir d’autres sources]] pour améliorer la probabilité d’identifier toute activité inappropriée ou inhabituelle.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint déploie la solution de sécurité et d’audit d’OMS. Cette solution fournit une vue complète de l’état de la sécurité. Le tableau de bord de sécurité et d’audit fournit un aperçu général de l’état de sécurité des ressources déployées à l’aide des données disponibles sur les solutions OMS déployées, y compris les données de journal et de vulnérabilité issues de l’évaluation des correctifs et de la base de référence. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 Contrôle AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Révision, analyse et rapports d’audit | Corrélation avec la surveillance physique

**AU-6 (6)** L’organisation met en corrélation les informations des enregistrements d’audit avec les informations obtenues suite à la surveillance de l’accès physique pour améliorer la probabilité d’identifier toute activité suspecte, inappropriée, inhabituelle ou malveillante.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | L’équipe SIM Microsoft Azure utilise les données de surveillance physique associées et les met en corrélation avec les enregistrements d’audit pour déterminer la probabilité d’une violation de la logique ou d’un comportement suspect lorsque des incidents physiques sont détectés. |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 Contrôle AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Révision, analyse et rapports d’audit | Actions autorisées

**AU-6 (7)** L’organisation spécifie les actions autorisées pour chaque [Sélection (un ou plusieurs) : processus de système d’information, rôle, utilisateur] associé à la révision et à l’analyse des informations d’audit et aux rapports associés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles Windows déployées par cette solution Azure Blueprint implémentent des autorisations au niveau du système d’exploitation qui limitent les actions qu’un utilisateur peut effectuer en relation avec les informations d’audit. Dans Azure, les utilisateurs ou groupes d’utilisateurs peuvent être affectés à des rôles (par exemple, propriétaire, collaborateur, lecteur ou rôle personnalisé) pour limiter les actions disponibles par rapport aux ressources ou solutions déployées, notamment Log Analytics.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 Contrôle AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Révision, analyse et rapports d’audit | Ajustement du niveau d’audit

**AU-6 (10)** L’organisation ajuste le niveau de révision, d’analyse et de création de rapports de l’audit au sein du système d’information en cas de modification du risque basée sur les informations fournies par les forces de l’ordre, par les renseignements ou d’autres sources d’informations crédibles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’ajuster le niveau de révision, d’analyse et de création de rapports d’audit pour les ressources qu’il a lui-même déployées (y compris les applications, systèmes d’exploitation, bases de données et logiciels) en cas de modification du risque basée sur les informations fournies par les forces de l’ordre, les renseignements ou d’autres sources avérées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-7a"></a>NIST 800-53 Contrôle AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>Réduction des audits et génération de rapports

**AU-7.a** Le système d’information fournit des fonctionnalités de réduction de l’audit et de génération de rapports qui prennent en charge les exigences à la demande en matière de révision, d’analyse et de rapports, ainsi que des investigations effectuées après des faits relatifs à des incidents de sécurité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente la solution Log Analytics dans OMS. Log Analytics assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un dépôt central. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-7b"></a>NIST 800-53 Contrôle AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>Réduction des audits et génération de rapports

**AU-7.b** Le système d’information fournit une fonction de réduction des audits et de génération de rapports qui ne modifie pas le contenu d’origine ou le tri chronologique des enregistrements d’audit.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente la solution Log Analytics dans OMS. Log Analytics assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un dépôt central. Le contenu et le tri chronologique des temps d’audit ne sont pas modifiés lorsqu’ils sont collectés par Log Analytics. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 Contrôle AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Réduction des audits et génération de rapports | Traitement automatique

**AU-7 (1)** Le système d’information permet de traiter les enregistrements d’audit pour les événements d’intérêt en fonction de [Affectation : champs d’audit définis par l’organisation au sein des enregistrements d’audit].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente la solution Log Analytics dans OMS. Log Analytics assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un dépôt central. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation. Log Analytics intègre un puissant langage de requête destiné à extraire les données stockées dans le référentiel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-8a"></a>NIST 800-53 Contrôle AU-8.a

#### <a name="time-stamps"></a>Horodatages

**AU-8.a** Le système d’information utilise l’horloge du système interne pour générer les horodatages des enregistrements d’audit.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par cette solution Azure Blueprint utilisent l’horloge du système interne pour générer les horodatages des enregistrements d’audit. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-8b"></a>NIST 800-53 Control AU-8.b

#### <a name="time-stamps"></a>Horodatages

**AU-8.b** Le système d’information enregistre les horodatages des enregistrements d’audit qui peuvent être mappés aux exigences d’heure UTC (temps universel coordonné) ou d’heure GMT (de Greenwich) [Affectation : granularité de la mesure de temps définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par cette solution Azure Blueprint utilisent l’horloge du système interne pour générer les horodatages des enregistrements d’audit. Les horodateurs sont enregistrés au format UTC. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-8-1a"></a>NIST 800-53 Contrôle AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Horodatages | Synchronisation avec une source de temps faisant autorité

**AU-8 (1).a** Le système d’information compare les horloges internes du système d’information [Affectation : fréquence définie par l’organisation] avec [Affectation : source de temps de référence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par cette solution Azure Blueprint utilisent l’horloge du système interne pour générer les horodatages des enregistrements d’audit. Les horloges de système interne sont configurées de façon à se synchroniser avec une source de temps faisant autorité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800-53 Contrôle AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Horodatages | Synchronisation avec une source de temps faisant autorité

**AU-8 (1).b** Le système d’information synchronise les horloges système internes avec la source de temps faisant autorité lorsque la différence de temps est supérieure à [Affectation : période définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par cette solution Azure Blueprint utilisent l’horloge du système interne pour générer les horodatages des enregistrements d’audit. Les horloges de système interne sont configurées de façon à se synchroniser avec une source de temps faisant autorité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-9"></a>NIST 800-53 Contrôle AU-9

#### <a name="protection-of-audit-information"></a>Protection des informations d’audit

**AU-9** Le système d’information protège les informations et outils d’audit contre tout accès, modification et suppression non autorisés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les contrôles d’accès logiques sont utilisés pour protéger les outils et les informations d’audit au sein de cette solution Azure Blueprint contre tout accès, modification et suppression non autorisés. Azure Active Directory applique l’accès logique approuvé à l’aide des appartenances aux groupes basées sur les rôles. La possibilité d’afficher des informations d’audit et d’utiliser les outils d’audit peut être limitée aux utilisateurs qui demandent ces autorisations. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 Contrôle AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Protection des informations d’audit | Sauvegarde de l’audit sur des composants/systèmes physiques distincts

**AU-9 (2)** Le système d’information sauvegarde les enregistrements d’audit [Affectation : fréquence définie par l’organisation] sur un système physique ou composant système différent du système ou composant en cours d’audit.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente le service Log Analytics dans OMS. Les machines virtuelles déployées et les comptes de stockage de diagnostics Azure sont des sources connectées à Log Analytics et conservées séparément de leur origine. Les données sont collectées par OMS en quasi temps réel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 Contrôle AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Protection des informations d’audit | Protection par chiffrement

**AU-9 (3)** Le système d’information implémente des mécanismes de chiffrement pour protéger l’intégrité des informations et des outils d’audit.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente le service Log Analytics dans OMS. Le service Log Analytics s’assure que les données entrantes proviennent d’une source approuvée en validant des certificats et l’intégrité des données à l’aide de la certification Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 Contrôle AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Protection des informations d’audit | Accès par un sous-ensemble d’utilisateurs disposant de privilèges

**AU-9 (4)** L’organisation autorise l’accès à la gestion de la fonctionnalité d’audit uniquement à [Affectation : sous-ensemble défini par l’organisation d’utilisateurs disposant de privilèges].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les contrôles d’accès logiques sont utilisés pour protéger les outils et les informations d’audit au sein de cette solution Azure Blueprint contre tout accès, modification et suppression non autorisés. Azure Active Directory applique l’accès logique approuvé à l’aide des appartenances aux groupes basées sur les rôles. La possibilité d’afficher des informations d’audit et d’utiliser les outils d’audit peut être limitée aux utilisateurs qui demandent ces autorisations.
 |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-10"></a>NIST 800-53 Contrôle AU-10

#### <a name="non-repudiation"></a>Non-répudiation

**AU-10** Le système d’information assure une protection contre le fait qu’une personne (ou un processus agissant pour le compte d’une personne) nie avoir exécuté [Affectation : actions définies par l’organisation à couvrir par la non-répudiation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La fonctionnalité d’audit de cette solution Azure Blueprint est fournie par Azure Monitor et le service Log Analytics dans OMS. Azure Monitor fournit des journaux d’audit détaillés sur l’activité associée aux ressources déployées. Ces journaux ainsi que les journaux de système d’exploitation sont collectés par Log Analytics et stockés dans le dépôt OMS. Ces journaux contiennent des enregistrements détaillés des événements relatifs au système d’information et peuvent vous protéger contre la non-répudiation. En outre, l’accès aux données de journal est limité à l’aide du contrôle d’accès en fonction du rôle pour empêcher la modification ou la suppression non autorisée des données de journaux. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-11"></a>NIST 800-53 Contrôle AU-11

#### <a name="audit-record-retention"></a>Rétention des enregistrements d’audit

**AU-11** L’organisation conserve les enregistrements d’audit pendant [Affectation : période cohérente avec la stratégie de rétention des enregistrements définie par l’organisation] pour prendre en charge les investigations qui ont eu lieu après des faits relatifs à des incidents de sécurité, ainsi que pour répondre aux exigences de rétention des informations réglementaires et organisationnelles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente le service Log Analytics dans OMS. Log Analytics assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un dépôt central. Une fois collectées, les données sont conservées pendant un an par configuration Log Analytics. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-12a"></a>NIST 800-53 Contrôle AU-12.a

#### <a name="audit-generation"></a>Génération de l’audit

**AU-12.a** Le système d’information fournit des fonctionnalités de génération d’enregistrements d’audit pour les événements auditables définis à l’alinéa AU-2 a. à [Affectation : composants du système d’information définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les événements audités par cette solution Azure Blueprint incluent ceux audités par les journaux d’activité Azure pour les ressources déployées, les journaux de niveau système d’exploitation, les journaux Active Directory et les journaux SQL Server. Les clients peuvent sélectionner d’autres événements à auditer selon les besoins de leur mission. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-12b"></a>NIST 800-53 Contrôle AU-12.b

#### <a name="audit-generation"></a>Génération de l’audit

**AU-12.b** Le système d’information autorise [Affectation : rôles ou personnel définis par l’organisation] à sélectionner les événements auditables à auditer par des composants spécifiques du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès aux fonctions d’audit est limité à l’aide du contrôle d’accès en fonction du rôle dans Azure et au niveau du système d’exploitation de la machine virtuelle. La configuration des événements sélectionnés pour l’audit par les ressources déployées par cette solution Azure Blueprint peut être effectuée par des utilisateurs disposant de l’autorisation appropriée basée sur des rôles. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-au-12c"></a>NIST 800-53 Contrôle AU-12.c

#### <a name="audit-generation"></a>Génération de l’audit

**AU-12.c** Le système d’information génère des enregistrements d’audit pour les événements définis à l’alinéa AU-2.d. avec le contenu défini à l’alinéa AU-3.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les événements audités par cette solution Azure Blueprint incluent ceux audités par les journaux d’activité Azure pour les ressources déployées, les journaux de niveau système d’exploitation, les journaux Active Directory et les journaux SQL Server. Les clients peuvent sélectionner d’autres événements à auditer selon les besoins de leur mission. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 Contrôle AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Génération d’audit | Piste d’audit à l’échelle du système/liée au temps

**AU-12 (1)** Le système d’information compile des enregistrements d’audit à partir de [Affectation : composants de système d’information définis par l’organisation] dans une piste d’audit (logique ou physique) à l’échelle du système et à une heure précise [Affectation : niveau de tolérance défini par l’organisation pour la relation entre les horodatages d’enregistrements individuels dans la piste d’audit].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint implémente le service Log Analytics dans OMS. Log Analytics assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un dépôt central. Les horodatages d’enregistrements d’audit ne sont pas altérés. Par conséquent, la piste d’audit est corrélée au temps. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 Contrôle AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Génération de l’audit | Modifications apportées par des personnes autorisées

**AU-12 (3)** Le système d’information offre la possibilité à [Affectation : rôles ou personnes définis par l’organisation] de modifier l’audit à exécuter sur [Affectation : composants de système d’information définis par l’organisation] en fonction de [Affectation : critères d’événements sélectionnables définis par l’organisation] dans un délai de [Affectation : seuils horaires définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès aux fonctions d’audit est limité à l’aide du contrôle d’accès en fonction du rôle dans Azure et au niveau du système d’exploitation de la machine virtuelle. La configuration des événements sélectionnés pour l’audit par les ressources déployées par cette solution Azure Blueprint peut être effectuée par des utilisateurs disposant de l’autorisation appropriée basée sur des rôles. |
| **Fournisseur (Microsoft Azure)** | Non applicable |
