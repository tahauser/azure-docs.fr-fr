---
title: "Automatisation Azure Blueprint pour FedRAMP - Intégrité du système et des informations"
description: "Applications Web pour FedRAMP - Intégrité du système et des informations"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 1dc6805a5a1f610f06ce58bd4bd644346436294e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-information-integrity-si"></a>Intégrité du système et des informations (SI)

> [!NOTE]
> Ces contrôles sont définis par l’Institut national des normes et de la technologie (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 Revision 4 du service NIST. Pour obtenir des informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication NIST 800-53 Rev. 4.

## <a name="nist-800-53-control-si-1"></a>NIST 800-53 - Contrôle SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Stratégie et procédures relatives à l’intégrité du système et des informations

**SI-1** L’organisation développe, documente et communique à [Affectation : personnel ou rôles de l’organisation] une stratégie d’intégrité du système et des informations qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités de l’organisation et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie d’intégrité du système et des informations et des contrôles d’intégrité du système et des informations associés ; et révise et met à jour la stratégie actuelle d’intégrité du système et des informations [Affectation : fréquence définie par l’organisation] ; et les procédures d’intégrité du système et des informations [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures d’intégrité du système et des informations de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800-53 - Contrôle SI-2.a

#### <a name="flaw-remediation"></a>Correction des défauts

**SI-2.a** L’organisation identifie, signale et corrige les défauts du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie la solution OMS Automation & Control pour suivre l’état des mises à jour pour les machines virtuelles Windows déployées dans cette architecture. À partir du tableau de bord OMS, la vignette Update Management affiche l’état de correction des défauts pour tous les serveurs Windows déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800-53 - Contrôle SI-2.b

#### <a name="flaw-remediation"></a>Correction des défauts

**SI-2.b** L’organisation teste les mises à jour des logiciels et des microprogrammes liées à la correction des défauts pour évaluer l’efficacité et les effets secondaires potentiels avant l’installation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de tester les mises à jour liées à la correction des défauts pour évaluer l’efficacité et les effets secondaires potentiels avant l’installation sur des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800-53 - Contrôle SI-2.c

#### <a name="flaw-remediation"></a>Correction des défauts

**SI-2.c** L’organisation installe les mises à jour des logiciels et des microprogrammes relatifs à la sécurité pendant la [Affectation : période définie par l’organisation] de la version des mises à jour.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles Windows déployées par ce plan Azure Blueprint sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution déploie également la solution OMS Automation & Control par le biais de laquelle des déploiements de mise à jour peuvent être créés pour déployer les correctifs sur les serveurs Windows si nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800-53 - Contrôle SI-2.d

#### <a name="flaw-remediation"></a>Correction des défauts

**SI-2.d** L’organisation incorpore la correction des défauts dans le processus de gestion de la configuration de l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’inclure la correction des défauts dans la gestion de la configuration. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800-53 - Contrôle SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Correction des défauts | Gestion centralisée

**SI-2 (1)** L’organisation centralise la gestion du processus de correction des défauts.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie la solution OMS Automation & Control pour suivre l’état des mises à jour des machines virtuelles Windows déployées dans cette architecture. À partir du tableau de bord OMS, la vignette Update Management affiche l’état de correction des défauts pour tous les serveurs Windows déployés. Des déploiements de mise à jour peuvent être créés pour déployer les correctifs sur les serveurs Windows si nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800-53 - Contrôle SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Correction des défauts | État de la correction automatisée des défauts

**SI-2 (2)** L’organisation utilise les mécanismes automatisés [Affectation : fréquence définie par l’organisation] pour déterminer l’état des composants du système d’information concernant la correction des défauts.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie la solution OMS Automation & Control pour suivre l’état des mises à jour des machines virtuelles Windows déployées dans cette architecture. Pour chaque ordinateur Windows géré, une analyse est effectuée deux fois par jour. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé et si une analyse de conformité est lancée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800-53 - Contrôle SI-2 (3).a

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correction des défauts | Délai de correction des défauts / Évaluation des actions correctives

**SI-2 (3).a** L’organisation mesure le délai entre l’identification des défauts et la correction des défauts.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de corriger les défauts des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800-53 - Contrôle SI-2 (3).b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correction des défauts | Délai de correction des défauts / Évaluation des actions correctives

**SI-2 (3).b** L’organisation établit des [Affectation : évaluations définies par l’organisation] pour la mise en œuvre d’actions correctives.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut se reposer sur les évaluations de l’entreprise pour les processus de correction des défauts. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-3a"></a>NIST 800-53 - Contrôle SI-3.a

#### <a name="malicious-code-protection"></a>Protection contre les codes malveillants

**SI-3.a** L’organisation utilise des mécanismes de protection contre les codes malveillants aux points d’entrée et de sortie du système d’information pour détecter et éliminer les codes malveillants.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie les protections contre les programmes malveillants pour toutes les machines virtuelles Windows déployées par le client à l’aide de l’extension de machine virtuelle Microsoft Antimalware. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-3b"></a>NIST 800-53 - Contrôle SI-3.b

#### <a name="malicious-code-protection"></a>Protection contre les codes malveillants

**SI-3.b** L’organisation met à jour les mécanismes de protection contre les codes malveillants lorsque de nouvelles versions sont disponibles conformément à la stratégie et aux procédures de gestion de la configuration de l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie les protections contre les programmes malveillants pour toutes les machines virtuelles Windows déployées par le client à l’aide de l’extension de machine virtuelle Microsoft Antimalware. Cette extension est configurée pour mettre automatiquement à jour le moteur anti-programmes malveillants et les signatures de protection lors de la mise à disposition de la version. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-3c"></a>NIST 800-53 - Contrôle SI-3.c

#### <a name="malicious-code-protection"></a>Protection contre les codes malveillants

**SI-3.c** L’organisation configure les mécanismes de protection contre les codes malveillants pour effectuer des analyses périodiques du système d’information [Affectation : fréquence définie par l’organisation] et les analyses en temps réel des fichiers à partir des sources externes au niveau de [Sélection (au moins une) ; point de terminaison ; points d’entrée/sortie du réseau] lorsque les fichiers sont téléchargés, ouverts ou exécutés en fonction de la stratégie de sécurité de l’organisation ; et [Sélection (au moins une) : bloque le code malveillant ; met le code malveillant en quarantaine ; envoie une alerte à l’administrateur ; [Affectation : action définie par l’organisation]] en réponse à la détection d’un code malveillant.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie les protections contre les programmes malveillants pour toutes les machines virtuelles Windows déployées par le client à l’aide de l’extension de machine virtuelle Microsoft Antimalware. Cette extension est configurée pour effectuer des analyses en temps réel et périodiques (hebdomadaires), mettre à jour automatiquement le moteur contre les logiciels malveillants et les signatures de protection et effectuer des actions de correction automatique. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800-53 - Contrôle SI-3.d

#### <a name="malicious-code-protection"></a>Protection contre les codes malveillants

**SI-3.d** L’organisation gère la réception de faux positifs pendant la détection et l’éradication de codes malveillants et l’impact potentiel sur la disponibilité du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’assurer la protection contre les codes malveillants. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800-53 - Contrôle SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Protection contre les codes malveillants | Gestion centralisée

**SI-3 (1)** L’organisation centralise la gestion des mécanismes de protection contre les codes malveillants.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie les protections contre les programmes malveillants pour toutes les machines virtuelles Windows déployées par le client à l’aide de l’extension de machine virtuelle Microsoft Antimalware. OMS Azure fournit une fonctionnalité centralisée permettant d’examiner l’état actuel de la solution anti-programme malveillant. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800-53 - Contrôle SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Protection contre les codes malveillants | Mises à jour automatiques

**SI-3 (2)** Le système d’information met automatiquement à jour les mécanismes de protection contre les codes malveillants.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie les protections contre les programmes malveillants pour toutes les machines virtuelles Windows déployées par le client à l’aide de l’extension de machine virtuelle Microsoft Antimalware. Cette extension est configurée pour mettre automatiquement à jour le moteur anti-programmes malveillants et les signatures de protection lors de la mise à disposition de la version. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800-53 - Contrôle SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Protection contre les codes malveillants | Détection de codes non basée sur la signature

**SI-3 (7)** Le système d’information met en œuvre des mécanismes de détection des codes malveillants non basée sur la signature.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie les protections contre les programmes malveillants pour toutes les machines virtuelles Windows déployées par le client à l’aide de l’extension de machine virtuelle Microsoft Antimalware. Cette extension est configurée pour effectuer une détection heuristique. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800-53 - Contrôle SI-4.a

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.a** L’organisation surveille le système d’information pour détecter les attaques et les indicateurs d’attaques potentielles conformément aux [Affectation : objectifs de surveillance définis par l’organisation] ; et les connexions locales, réseau et à distance non autorisées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie Log Analytics et la solution OMS Security and Audit. Cette solution fournit une vue complète de l’état de sécurité, des attaques et des indicateurs d’attaques potentielles. Le tableau de bord Security and Audit fournit un aperçu général de l’état de sécurité des ressources déployées à l’aide des données disponibles sur les solutions OMS déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800-53 - Contrôle SI-4.b

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.b** L’organisation identifie toute utilisation non autorisée du système d’information via des [Affectation : techniques et méthodes définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie la solution OMS Security and Audit. Le domaine Identity and Access fournit un tableau de bord avec une vue d’ensemble de l’état de l’identité du système d’information, y compris le nombre de tentatives de connexion ayant échoué et le nombre actuel de comptes connectés. Les informations disponibles dans ce tableau de bord peuvent vous aider à identifier une activité suspecte potentielle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800-53 - Contrôle SI-4.c

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.c** L’organisation déploie de façon stratégique des dispositifs de surveillance dans le système d’information pour collecter des informations essentielles déterminées par l’organisation ; et à des emplacements ad hoc dans le système pour suivre les types spécifiques de transactions présentant un intérêt pour l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie Log Analytics et la solution OMS Security and Audit. Le tableau de bord Security and Audit fournit un aperçu général de l’état de sécurité des ressources déployées à l’aide des données disponibles sur les solutions OMS déployées, y compris des informations sur les données de surveillance du système d’exploitation des machines virtuelles. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800-53 - Contrôle SI-4.d

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.d** L’organisation protège les informations obtenues à partir d’outils de surveillance des intrusions contre tout accès, modification et suppression non autorisés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les contrôles d’accès logiques sont utilisés pour protéger les informations de surveillance au sein de ce plan Azure Blueprint contre tout accès, modification et suppression non autorisés. Azure Active Directory applique l’accès logique approuvé à l’aide des appartenances aux groupes basées sur les rôles. La possibilité d’afficher des informations de surveillance et d’utiliser des outils de surveillance peut être limitée aux utilisateurs qui demandent ces autorisations. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4e"></a>NIST 800-53 - Contrôle SI-4.e

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.e** L’organisation accroît l’activité de surveillance du système d’information chaque fois qu’un soupçon de risque accru pour les opérations et les ressources organisationnelles, les individus, les autres organisations ou la nation reposant sur des informations relatives au respect des lois, des informations décisionnelles ou d’autres sources d’informations crédibles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800-53 - Contrôle SI-4.f

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.f** L’organisation obtient un avis juridique concernant les activités de surveillance du système d’information conformément aux lois fédérales, décrets, directives, politiques ou réglementations en vigueur.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800-53 - Contrôle SI-4.g

#### <a name="information-system-monitoring"></a>Surveillance du système d’information

**SI-4.g** L’organisation fournit des [Affectation : informations de surveillance du système d’information définies par l’organisation] à [Affectation : personnel ou rôles définis par l’organisation] [Sélection (au moins une) : en fonction des besoins ; [Affectation : fréquence définie par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800-53 - Contrôle SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Surveillance du système d’information | Système de détection des intrusions à l’échelle du réseau

**SI-4 (1)** L’organisation connecte et configure des outils individuels de détection des intrusions dans un système de détection des intrusions à l’échelle du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800-53 - Contrôle SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Surveillance du système d’information | Outils automatisés pour l’analyse en temps réel

**SI-4 (2)** L’organisation utilise des outils automatisés pour prendre en charge l’analyse des événements quasiment en temps réel.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie Log Analytics et différentes solutions OMS, y compris la solution Security and Audit. Log Analytics fournit des analyses des événements sur l’ensemble des ressources déployées quasiment en temps réel. La solution OMS fournit une vue complète de l’état de sécurité de l’ensemble des domaines de la solution. OMS fournit un aperçu général de l’état de sécurité des ressources déployées à l’aide des données disponibles sur les solutions OMS déployées. OMS peut être configuré pour générer des alertes en fonction de critères définis. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 - Contrôle SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Surveillance du système d’information | Trafic des communications entrantes et sortantes

**SI-4 (4)** Le système d’information surveille le trafic des communications entrantes et sortantes [Affectation : fréquence définie par l’organisation] pour détecter des activités ou des conditions inhabituelles ou non autorisées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800-53 - Contrôle SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Surveillance du système d’information | Alertes générées par le système

**SI-4 (5)** Le système d’information alerte [Affectation : personnel ou rôles définis par l’organisation] lorsque les indications suivantes de compromission ou de compromission potentielle sont identifiées : [Affectation : indicateurs de compromission définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie différentes solutions OMS, y compris la solution Security and Audit. Log Analytics fournit des analyses des événements sur l’ensemble des ressources déployées quasiment en temps réel. La solution OMS fournit une vue complète de l’état de sécurité de l’ensemble des domaines de la solution. OMS peut être configuré pour générer des alertes en fonction de critères définis. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800-53 - Contrôle SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Surveillance du système d’information | Analyse du trafic des communications

**SI-4 (11)** L’organisation analyse le trafic des communications sortantes à la frontière externe du système d’information et à certains [Affectation : éléments internes du système définis par l’organisation (par exemple, les sous-réseaux, les sous-systèmes)] pour détecter les anomalies.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’analyser les anomalies du trafic des communications des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800-53 - Contrôle SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Surveillance du système d’information | Détection sans fil des intrusions

**SI-4 (14)** L’organisation utilise un système de détection des intrusions sans fil pour identifier les appareils sans fil non fiables et détecter les tentatives d’attaque et les compromissions/failles potentielles du système d’information.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Aucun matériel contrôlé par le client, y compris les appareils sans fil, n’est autorisé dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure surveille régulièrement les signaux sans fil non autorisés sur une base trimestrielle, comme indiqué dans AC-18. <br /> Microsoft Azure implémente ce contrôle pour le compte des clients des services PaaS et IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800-53 - Contrôle SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Surveillance du système d’informations | Mettre en corrélation les informations de surveillance

**SI-4 (16)** L’organisation met en corrélation les informations des outils de surveillance utilisés dans l’ensemble du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint déploie Log Analytics et différentes solutions OMS, y compris la solution Security and Audit. OMS fournit un aperçu général de l’état de sécurité des ressources déployées à l’aide des données disponibles sur les solutions OMS déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800-53 - Contrôle SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Surveillance du système d’information | Analyser le trafic/l’exfiltration secrète

**SI-4 (18)** L’organisation analyse le trafic des communications sortantes à la frontière externe du système d’information (c’est-à-dire, le périmètre du système) et à certains [Affectation : éléments internes du système définis par l’organisation (par exemple, les sous-systèmes, les sous-réseaux)] pour détecter l’exfiltration secrète d’informations.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’analyser le trafic des communications des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800-53 - Contrôle SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Surveillance du système d’information | Personnes présentant un risque accru

**SI-4 (19)** L’organisation met en œuvre une [Affectation : surveillance supplémentaire définie par l’organisation] des personnes identifiées par les [Affectation : sources définies par l’organisation] comme présentant un niveau accru de risque.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les personnes présentant un risque accru. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800-53 - Contrôle SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Surveillance du système d’information | Utilisateurs disposant de privilèges

**SI-4 (20)** L’organisation met en œuvre la [Affectation : surveillance supplémentaire définie par l’organisation] des utilisateurs disposant de privilèges.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller les utilisateurs disposant de privilèges. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800-53 - Contrôle SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Surveillance du système d’information | Services réseau non autorisés

**SI-4 (22)** Le système d’information détecte les services réseau qui n’ont pas été autorisés ou approuvés par [Affectation : processus d’autorisation ou d’approbation définis par l’organisation] et [Sélection (au moins une) : audits ; alertes [Affectation : personnel ou rôles définis par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de détecter les services réseau non autorisés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800-53 - Contrôle SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Surveillance du système d’information | Appareils basés sur l’hôte

**SI-4 (23)** L’organisation met en œuvre des [Affectation : mécanismes de surveillance basés sur l’hôte définis par l’organisation] à [Affectation : composants du système d’information définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce plan Azure Blueprint collecte les données de surveillance à partir des ressources déployées, y compris les données des fonctionnalités de surveillance basées sur l’hôte. Microsoft Monitoring Agent est installé sur toutes les machines virtuelles Windows pour collecter les données de surveillance utilisées par Log Analytics et d’autres solutions d’OMS. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800-53 - Contrôle SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Surveillance du système d’information | Indicateurs de compromission

**SI-4 (24)** Le système d’information détecte, collecte, distribue et utilise des indicateurs de compromission.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de découvrir, collecter, distribuer et utiliser des indicateurs de compromission sur les ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800-53 - Contrôle SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>Alertes de sécurité, conseils et directives

**SI-5.a** L’organisation reçoit les alertes de sécurité, les conseils et les directives du système d’information des [Affectation : organisations externes définies par l’organisation] de manière continue.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de gérer les alertes de sécurité, les conseils et les directives des ressources déployées par le client (pour inclure des applications, des systèmes d’exploitation, des bases de données et des logiciels). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800-53 - Contrôle SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>Alertes de sécurité, conseils et directives

**SI-5.b** L’organisation génère des alertes de sécurité, des conseils et des directives internes comme jugé nécessaire.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de gérer les alertes de sécurité, les conseils et les directives des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800-53 - Contrôle SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>Alertes de sécurité, conseils et directives

**SI-5.c** L’organisation communique les alertes de sécurité, les conseils et les directives aux interlocuteurs suivants : [Sélection (au moins une) : [Affectation : personnel ou rôles définis par l’organisation] ; [Affectation : éléments au sein de l’organisation définis par l’organisation] ; [Affectation : organisations externes définies par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de gérer les alertes de sécurité, les conseils et les directives des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800-53 - Contrôle PS-5.d

#### <a name="security-alerts-advisories-and-directives"></a>Alertes de sécurité, conseils et directives

**SI-5.d** L’organisation implémente les directives de sécurité conformément aux délais établis ou notifie l’organisation émettrice du degré de non-conformité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de gérer les alertes de sécurité, les conseils et les directives des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800-53 - Contrôle SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alertes de sécurité, conseils et directives | Alertes et conseils automatisés

**SI-5 (1)** L’organisation utilise les mécanismes automatisés pour rendre les informations des alertes de sécurité et des conseils disponibles dans toute l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de gérer les alertes de sécurité, les conseils et les directives des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800-53 - Contrôle SI-6.a

#### <a name="security-function-verification"></a>Vérification de la fonction de sécurité

**SI-6.a** Le système d’information vérifie le bon fonctionnement des [Affectation : fonctions de sécurité définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de vérifier la fonction de sécurité des ressources déployées par le client (pour inclure des applications, des systèmes d’exploitation, des bases de données et des logiciels). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 - Contrôle SI-6.b

#### <a name="security-function-verification"></a>Vérification de la fonction de sécurité

**SI-6.b** Le système d’information effectue cette vérification des [Sélection (au moins une) : [Affectation : états de transition du système définis par l’organisation] ; suite à une commande d’un utilisateur disposant de privilèges appropriés ; [Affectation : fréquence définie par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de vérifier la fonction de sécurité des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 - Contrôle SI-6.c

#### <a name="security-function-verification"></a>Vérification de la fonction de sécurité

**SI-6.c** Le système d’information informe [Affectation : personnel ou rôles définis par l’organisation] des tests de vérification de sécurité ayant échoué.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de vérifier la fonction de sécurité des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800-53 - Contrôle SI-6.d

#### <a name="security-function-verification"></a>Vérification de la fonction de sécurité

**SI-6.d** Le système d’information [Sélection (au moins une) : arrête le système d’information ; redémarre le système d’information ; [Affectation : autre(s) action(s) définie(s) par l’organisation]] lorsque des anomalies sont détectées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de vérifier la fonction de sécurité des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-7"></a>NIST 800-53 - Contrôle SI-7

#### <a name="software-firmware-and-information-integrity"></a>Intégrité des informations, des microprogrammes et des logiciels

**SI-7** L’organisation utilise des outils de vérification de l’intégrité pour détecter les modifications non autorisées des [Affectation : informations, microprogrammes et logiciels définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par ce plan Azure Blueprint exécutent des systèmes d’exploitation Windows. Windows assure la validation d’intégrité des fichiers en temps réel, la protection et la récupération des fichiers du système noyau installés dans le cadre de Windows ou de mises à jour autorisées du système Windows par le biais de la fonctionnalité Protection des ressources Windows. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800-53 - Contrôle SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Intégrité des informations, des microprogrammes et des logiciels | Contrôles d’intégrité

**SI-7 (1)** Le système d’information effectue un contrôle d’intégrité des [Affectation : logiciels, microprogrammes et informations définis par l’organisation] [Sélection (au moins une) : au démarrage ; à [Affectation : états de transition ou événements relatifs à la sécurité définis par l’organisation] ; [Affectation : fréquence définie par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par ce plan Azure Blueprint exécutent des systèmes d’exploitation Windows. Windows assure la validation d’intégrité des fichiers en temps réel, la protection et la récupération des fichiers du système noyau installés dans le cadre de Windows ou de mises à jour autorisées du système Windows par le biais de la fonctionnalité Protection des ressources Windows. WRP permet le contrôle d’intégrité en temps réel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800-53 - Contrôle SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Intégrité des informations, des microprogrammes et des logiciels | Notification automatiques des violations de l’intégrité

**SI-7 (2)** L’organisation utilise des outils automatisés qui informent [Affectation : personnel ou rôles définis par l’organisation] de la découverte d’incohérences pendant la vérification de l’intégrité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par ce plan Azure Blueprint exécutent des systèmes d’exploitation Windows. Windows assure la validation d’intégrité des fichiers en temps réel, la protection et la récupération des fichiers du système noyau installés dans le cadre de Windows ou de mises à jour autorisées du système Windows par le biais de la fonctionnalité Protection des ressources Windows.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800-53 - Contrôle SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Intégrité des informations, des microprogrammes et des logiciels | Réponse automatique en cas de violation de l’intégrité

**SI-7 (5)** Le système d’information [Sélection (au moins une) : arrête le système d’information ; redémarre le système d’information ; implémente [Affectation : dispositifs de protection de sécurité définis par l’organisation]] lorsque des violations de l’intégrité sont détectées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de répondre automatiquement aux violations d’intégrité au sein des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800-53 - Contrôle SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Intégrité des informations, des microprogrammes et des logiciels | Intégration de la détection et des réponses

**SI-7 (7)** L’organisation incorpore la détection des [affectation : modifications de la sécurité du système d’information définies par l’organisation] non autorisées dans la fonctionnalité de réponse aux incidents de l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de protéger l’intégrité des logiciels et des informations des ressources déployées par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800-53 - Contrôle SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Intégrité des informations, des microprogrammes et des logiciels | Code binaire ou exécutable sur la machine

**SI-7 (14)** L’organisation interdit l’utilisation d’un code binaire ou exécutable sur la machine provenant de sources ayant une garantie limitée ou sans garantie et lorsque le code source n’a pas été fourni ; et fournit des exceptions aux exigences de code en cas de besoins impératifs liés à la mission/au fonctionnement et avec l’approbation du responsable autorisé.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures d’intégrité du système et des informations d’entreprise du client peuvent contenir des exigences relatives à l’obtention du code source du code binaire ou exécutable sur la machine de certaines sources. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-8a"></a>NIST 800-53 - Contrôle SI-8.a

#### <a name="spam-protection"></a>Protection contre les courriers indésirables

**SI-8.a** L’organisation utilise des mécanismes de protection contre les courriers indésirables aux points d’entrée et de sortie du système d’information pour détecter les codes malveillants et agir en fonction.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun serveur de messagerie n’est déployé dans le cadre de ce plan Azure Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800-53 - Contrôle SI-8.b

#### <a name="spam-protection"></a>Protection contre les courriers indésirables

**SI-8.b** L’organisation met à jour les mécanismes de protection contre les courriers indésirables lorsque de nouvelles versions sont disponibles conformément à la stratégie et aux procédures de gestion de la configuration de l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun serveur de messagerie n’est déployé dans le cadre de ce plan Azure Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800-53 - Contrôle SI-8 (1)

#### <a name="spam-protection--central-management"></a>Protection contre les courriers indésirables | Gestion centralisée

**SI-8 (1)** L’organisation centralise la gestion des mécanismes de protection contre les courriers indésirables.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun serveur de messagerie n’est déployé dans le cadre de ce plan Azure Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800-53 - Contrôle SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Protection contre les courriers indésirables | Mises à jour automatiques

**SI-8 (2)** Le système d’information met automatiquement à jour les mécanismes de protection contre les courriers indésirables.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun serveur de messagerie n’est déployé dans le cadre de ce plan Azure Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-10"></a>NIST 800-53 - Contrôle SI-10

#### <a name="information-input-validation"></a>Validation des entrées d’informations

**SI-10** Le système d’information contrôle la validité des [Affectation : entrées d’informations définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de valider les entrées d’informations sur les ressources déployées par le client (pour inclure des applications, des systèmes d’exploitation, des bases de données et des logiciels). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-11a"></a>NIST 800-53 - Contrôle SI-11.a

#### <a name="error-handling"></a>Gestion des erreurs

**SI-11.a** Le système d’information génère des messages d’erreur qui fournissent les informations nécessaires à la mise en œuvre d’actions correctives sans révéler des informations pouvant être exploitées par des adversaires.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par ce plan Azure Blueprint utilisent des versions commerciales des systèmes d’exploitation et des applications logicielles. Ce logiciel utilise les meilleures pratiques du secteur pour garantir qu’aucune information sensible n’est révélée dans les messages d’erreur. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-11b"></a>NIST 800-53 - Contrôle SI-11.b

#### <a name="error-handling"></a>Gestion des erreurs

**SI-11.b** Le système d’information révèle les messages d’erreur uniquement à [Affectation : personnel ou rôles définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par ce plan Azure Blueprint utilisent des versions commerciales des systèmes d’exploitation et des applications logicielles. Ce logiciel utilise les meilleures pratiques du secteur pour communiquer les messages d’erreur appropriés dans le contexte des utilisations recevant le message. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-12"></a>NIST 800-53 - Contrôle SI-12

#### <a name="information-handling-and-retention"></a>Conservation et gestion des informations

**SI-12** L’organisation gère et conserve les informations dans le système d’information et les informations générées par le système conformément aux lois fédérales, aux décrets, aux directives, aux politiques, aux réglementations, aux normes et aux conditions opérationnelles en vigueur.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de gérer et de conserver les informations dans les ressources déployées par le client (notamment les applications, systèmes d’exploitation, bases de données et logiciels) et les informations générées par ces ressources. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-si-16"></a>NIST 800-53 - Contrôle SI-16

#### <a name="memory-protection"></a>Protection de la mémoire

**SI-16** Le système d’information implémente des [Affectation : sauvegardes de sécurité définies par l’organisation] pour protéger sa mémoire contre une exécution de code non autorisée.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par ce plan Azure Blueprint exécutent des systèmes d’exploitation Windows. Windows a mis en place des protections pour empêcher l’exécution de code dans des emplacements mémoire restreints : NX (No Execute), randomisation du format d’espace d’adresse (ASLR, Address Space Layout Randomization) et PED (Prévention de l’exécution des données). |
| **Fournisseur (Microsoft Azure)** | Non applicable |
