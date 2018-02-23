---
title: "Solution Blueprint Sécurité et conformité Azure - Automatisation d’applications web FedRAMP - Maintenance"
description: "Automatisation d’applications web FedRAMP - Maintenance"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="maintenance-ma"></a>Maintenance (MA)

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication 800-53 rév. 4 du NIST.

## <a name="nist-800-53-control-ma-1"></a>NIST 800-53 Contrôle MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Stratégie et procédures de maintenance du système

**MA-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles de l’organisation] une stratégie de maintenance du système qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie de maintenance du système et des contrôles de maintenance du système associés ; et révise et met à jour la stratégie actuelle de maintenance du système [Affectation : fréquence définie par l’organisation] ; et les procédures de maintenance du système [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures de maintenance du système de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-2a"></a>NIST 800-53 Contrôle MA-2.a

#### <a name="controlled-maintenance"></a>Maintenance contrôlée

**MA-2.a** L’organisation planifie, exécute, documente et vérifie les enregistrements de maintenance et les réparations des composants du système informatique conformément aux spécifications du fabricant ou du fournisseur et/ou aux exigences organisationnelles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la maintenance contrôlée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800-53 Contrôle MA-2.b

#### <a name="controlled-maintenance"></a>Maintenance contrôlée

**MA-2.b** L’organisation approuve et surveille toutes les activités de maintenance, qu’elles soient exécutées sur site ou à distance et que l’équipement soit inspecté sur site ou déplacé vers un autre emplacement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la maintenance contrôlée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-2c"></a>NIST 800-53 Contrôle MA-2.c

#### <a name="controlled-maintenance"></a>Maintenance contrôlée

**MA-2.c** L’organisation requiert que [Affectation : personnel ou rôles définis par l’organisation] approuvent de manière explicite le retrait du système informatique ou de composants du système du site de l’organisation à des fins de maintenance ou de réparations hors site.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure requiert que les ressources de la propriété (par ex., serveur ou appareil réseau) nécessitant un transfert hors site bénéficient d’une approbation explicite du propriétaire de la ressource. |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800-53 Contrôle MA-2.d

#### <a name="controlled-maintenance"></a>Maintenance contrôlée

**MA-2.d** L’organisation assainit l’équipement pour supprimer toutes les informations des supports associés avant le retrait du site de l’organisation à des fins de maintenance ou de réparations hors site.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | La Norme de protection des ressources de Microsoft Azure définit les précautions de manipulation des ressources requises dans le cas d’un transfert hors site des ressources. La Norme de protection des ressources requiert que les ressources de stockage des données soient nettoyées/purgées conformément au contrôle NIST SP 800-88, Guidelines for Media Sanitization (Instructions d’assainissement des supports), avant de quitter le centre de données. |


 ## <a name="nist-800-53-control-ma-2e"></a>NIST 800-53 Contrôle MA-2.e

#### <a name="controlled-maintenance"></a>Maintenance contrôlée

**MA-2.e** L’organisation vérifie tous les contrôles de sécurité potentiellement affectés pour vérifier que les contrôles fonctionnent toujours correctement suite à des actions de maintenance ou de réparation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la maintenance contrôlée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-2f"></a>NIST 800-53 Contrôle MA-2.f

#### <a name="controlled-maintenance"></a>Maintenance contrôlée

**MA-2.f** L’organisation inclut [Affectation : informations relatives à la maintenance définies par l’organisation] dans les enregistrements de maintenance de l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est responsable de la maintenance contrôlée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-2-2a"></a>NIST 800-53 Contrôle MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Maintenance contrôlée | Activités de maintenance automatiques

**MA-2 (2).a** L’organisation utilise des mécanismes automatiques pour planifier, exécuter et documenter la maintenance et les réparations.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’automatiser les activités de maintenance. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-2-2b"></a>NIST 800-53 Contrôle MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Maintenance contrôlée | Activités de maintenance automatiques

**MA-2 (2).b** L’organisation produit des enregistrements à jour, précis et complets de toutes les actions de maintenance et de réparation demandées, planifiées, en cours et terminées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’automatiser les activités de maintenance. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-3"></a>NIST 800-53 Contrôle MA-3

#### <a name="maintenance-tools"></a>Outils de maintenance

**MA-3** L’organisation approuve, contrôle et surveille les outils de maintenance du système informatique.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure utilise plusieurs outils pour exécuter la maintenance. Les outils de maintenance logicielle sont approuvés, contrôlés et entretenus par le biais du processus de modification et de publication de Microsoft Azure. <br /> L’équipe Services du site conserve un inventaire des outils de maintenance approuvés à utiliser dans le centre de donnés (voir MA-3). Il est indiqué au personnel en charge de la maintenance d’utiliser les outils de maintenance fournis. L’approbation des gestionnaires du centre de données est requise pour utiliser des outils non fournis par le centre de données. Les outils manuels physiques (tournevis, clés, etc.) sont exclus de ce contrôle. <br /> Chaque site dispose d’une zone de verrouillage physique ou d’une pièce à accès contrôlé pour le stockage des outils de maintenance professionnels, comme les analyseurs EtherScope Fluke, les testeurs Fibre Channel Fluke, les toners Ethernet, etc. L’équipe Services du site effectue des vérifications de routine de l’inventaire pour vérifier l’état de tous les outils. L’accès à la zone de verrouillage ou à la pièce de stockage de maintenance fait l’objet d’un suivi dans les journaux du lecteur de carte d’accès, qui sont disponibles en cas d’enquête. |


 ### <a name="nist-800-53-control-ma-3-1"></a>NIST 800-53 Contrôle MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Outils de maintenance | Inspection des outils

**MA-3 (1)** L’organisation inspecte les outils de maintenance apportés sur un site par le personnel en charge de la maintenance pour détecter toute modification incorrecte ou non autorisée.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | L’équipe Services du site de Microsoft Azure conserve un inventaire des outils de maintenance approuvés à utiliser dans le centre de donnés (voir MA-3 pour plus de détails). Il est indiqué au personnel en charge de la maintenance d’utiliser les outils de maintenance fournis. L’approbation des gestionnaires du centre de données (DCM) est requise pour utiliser des outils non fournis par le centre de données. |


 ### <a name="nist-800-53-control-ma-3-2"></a>NIST 800-53 Contrôle MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Outils de maintenance | Inspection des supports

**MA-3 (2)** L’organisation vérifie les supports contenant les programmes de diagnostic et de test pour détecter tout code malveillant avant que les supports ne soient utilisés dans le système informatique.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure interdit l’utilisation de supports de stockage ou informatiques mobiles dans l’environnement de production des centres de données Microsoft Azure sans l’autorisation des gestionnaires du centre de données. L’utilisation de supports personnels est interdite dans l’environnement de production des centres de données Microsoft Azure. <br /> Microsoft Azure a mis en place une procédure pour inspecter les ordinateurs portables avant qu’ils ne soient utilisés dans l’environnement de production des centres de données Microsoft Azure. Les responsables de la sécurité sont formés pour s’assurer que les ordinateurs portables utilisés dans l’environnement de production ont passé l’inspection avec succès. |


 ### <a name="nist-800-53-control-ma-3-3"></a>NIST 800-53 Contrôle MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Outils de maintenance | Empêcher le retrait non autorisé

**MA-3 (3)** L’organisation empêche le retrait non autorisé de l’équipement de maintenance contenant des informations sur l’organisation en vérifiant que l’équipement ne contient aucune information sur l’organisation ; en assainissant ou en détruisant l’équipement ; en conservant l’équipement au sein du site ; ou en obtenant une exemption de la part de [Affectation : personnel ou rôles définis par l’organisation] autorisant explicitement le retrait de l’équipement du site.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure utilise des outils de maintenance spécifique au centre de données qui sont conservés sur le site et ne sont pas retirés. Chaque site dispose d’une zone de verrouillage physique ou d’une pièce de stockage qui stocke les outils de maintenance, comme les analyseurs EtherScope Fluke, les testeurs Fibre Channel Fluke, les toners Ethernet, etc. L’accès à la zone de verrouillage ou à la pièce de stockage est contrôlé dans DCAT pour empêcher tout accès non autorisé aux outils de maintenance. <br /> Les informations organisationnelles sont protégées durant la maintenance par les contrôles dans MA-4. Pour accéder aux informations organisationnelles, l’utilisateur doit disposer de comptes et d’authentificateurs privilégiés. |


 ## <a name="nist-800-53-control-ma-4a"></a>NIST 800-53 Contrôle MA-4.a

#### <a name="nonlocal-maintenance"></a>Maintenance non locale

**MA-4.a** L’organisation approuve et surveille les activités de diagnostic et de maintenance non locales.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’effectuer la maintenance non locale sur les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-4b"></a>NIST 800-53 Contrôle MA-4.b

#### <a name="nonlocal-maintenance"></a>Maintenance non locale

**MA-4.b** L’organisation autorise l’utilisation d’outils de diagnostic et de maintenance non locale tant qu’ils sont conformes à la stratégie de l’organisation et documentés dans le plan de sécurité pour le système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’effectuer la maintenance non locale sur les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800-53 Contrôle MA-4.c

#### <a name="nonlocal-maintenance"></a>Maintenance non locale

**MA-4.c** L’organisation utilise des authentificateurs forts lors des sessions de diagnostic et de maintenance non locale.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’effectuer la maintenance non locale sur les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-4d"></a>NIST 800-53 Contrôle MA-4.d

#### <a name="nonlocal-maintenance"></a>Maintenance non locale

**MA-4.d** L’organisation établit des enregistrements pour les activités de diagnostic et de maintenance non locale.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’effectuer la maintenance non locale sur les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-4e"></a>NIST 800-53 Contrôle MA-4.e

#### <a name="nonlocal-maintenance"></a>Maintenance non locale

**MA-4.e** L’organisation met fin à la session et aux connexions réseau une fois la maintenance non locale terminée.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’effectuer la maintenance non locale sur les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-4-2"></a>NIST 800-53 Contrôle MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Maintenance non locale | Documenter la maintenance non locale

**MA-4 (2)** L’organisation documente dans le plan de sécurité pour le système informatique, les stratégies et les procédures pour l’établissement et l’utilisation de connexions à des fins de diagnostic et de maintenance non locale.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de documenter la maintenance non locale dans le plan de sécurité pour les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-4-3"></a>NIST 800-53 Contrôle MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Maintenance non locale | Sécurité / Assainissement comparable

**MA-4 (3)** L’organisation requiert que les services de diagnostic et de maintenance non locale soient exécutés à partir d’un système informatique disposant d’une sécurité comparable à la fonctionnalité implémentée sur le système étant inspecté ; ou retirer le composant à inspecter du système informatique avant les activités de diagnostic ou de maintenance non locale, assainit le composant (suppression des informations sur l’organisation) avant son retrait du site de l’organisation, et une fois l’inspection effectuée, inspecte et assainit (détection de logiciel potentiellement malveillant) avant de reconnecter le composant au système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’exécuter la maintenance non locale des systèmes d’exploitation qu’il a lui-même déployés à partir d’un système informatique avec une sécurité comparable. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-4-6"></a>NIST 800-53 Contrôle MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Maintenance non locale | Protection par chiffrement

**MA-4 (6)** Le système informatique implémente des mécanismes de chiffrement pour protéger l’intégrité et la confidentialité des communications relatives au diagnostic et à la maintenance non locale.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de mettre en œuvre des mécanismes de chiffrement lors de l’exécution de diagnostics et d’une maintenance non locale sur les systèmes d’exploitation qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-5a"></a>NIST 800-53 Contrôle MA-5.a

#### <a name="maintenance-personnel"></a>Personnel en charge de la maintenance

**MA-5.a** L’organisation établit un processus pour l’autorisation du personnel en charge de la maintenance et conserve une liste des organisations ou du personnel de maintenance autorisés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les processus d’autorisation et d’accompagnement du personnel en charge de la maintenance du système de niveau d’entreprise du client peuvent être suffisants pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-5b"></a>NIST 800-53 Contrôle MA-5.b

#### <a name="maintenance-personnel"></a>Personnel en charge de la maintenance

**MA-5.b** L’organisation s’assure que tout personnel non accompagné effectuant la maintenance du système informatique dispose des autorisations d’accès requises.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les processus d’autorisation et d’accompagnement du personnel en charge de la maintenance du système de niveau d’entreprise du client peuvent être suffisants pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-5c"></a>NIST 800-53 Contrôle MA-5.c

#### <a name="maintenance-personnel"></a>Personnel en charge de la maintenance

**MA-5.c** L’organisation désigne du personnel au sein de l’organisation avec les autorisations d’accès et la compétence technique requises pour superviser les activités de maintenance du personnel qui ne dispose pas des autorisations d’accès requises.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les processus d’autorisation et d’accompagnement du personnel en charge de la maintenance du système de niveau d’entreprise du client peuvent être suffisants pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-5-1a"></a>NIST 800-53 Contrôle MA-5 (1).a

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personnel en charge de la maintenance | Individus sans accès approprié

**MA-5 (1).a** L’organisation implémente des procédures pour l’utilisation de personnel en charge de la maintenance ne disposant pas des autorisations de sécurité appropriées ou dont les membres ne sont pas des citoyens américains, incluant les exigences suivantes, le personnel en charge de la maintenance ne disposant pas des autorisations nécessaires, ou d’approbations d’accès formelles est accompagné et supervisé durant les activités de maintenance et de diagnostic sur le système informatique par du personnel de l’organisation approuvé disposant d’autorisations d’accès appropriées, et qualifié sur le plan technique; avant le début des activités de maintenance ou de diagnostic par du personnel ne disposant pas des autorisations d’accès nécessaires ou d’approbations d’accès formelles, tous les composants de stockage d’informations volatiles dans le système informatique sont assainis et tous les supports de stockage non volatile sont retirés ou physiquement déconnectés du système et placés en sécurité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les processus d’autorisation et d’accompagnement du personnel en charge de la maintenance du système de niveau d’entreprise du client peuvent être suffisants pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ma-5-1b"></a>NIST 800-53 Contrôle MA-5 (1).b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personnel en charge de la maintenance | Individus sans accès approprié

**MA-5 (1).b** L’organisation développe et implémente des protocoles de sécurité alternatifs au cas où un composant du système informatique ne peut pas être assaini, retiré ou déconnecté du système.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les processus d’autorisation et d’accompagnement du personnel en charge de la maintenance du système de niveau d’entreprise du client peuvent être suffisants pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ma-6"></a>NIST 800-53 Contrôle MA-6

#### <a name="timely-maintenance"></a>Maintenance adéquate

**MA-6** L’organisation obtient du support et/ou des pièces détachées pour la maintenance pour [Affectation : composants du système informatique définis par l’organisation] dans un délai de [Affectation : période de temps définie par l’organisation] après la panne.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Les centres de données Microsoft Azure ont à disposition du personnel de maintenance en interne pour prendre en charge les systèmes d’infrastructure du centre de données critiques, ainsi que les opérations du centre de données. Les équipes ont identifié des composants système technologiques et de sécurité critiques pour lesquels elles conservent des pièces détachées sur site. Les systèmes critiques sont désignés dans les configurations N+1 et les services sont conçus pour être résilients. Ceci permet à l’équipe de gestion du centre de données de respecter les objectifs de récupération en cas d’interruption du service ou d’une situation de plan de contingence. Les services critiques du système informatique sont provisionnés à partir de plusieurs centres de données pour éviter une interruption du service due à un incident dans l’un des centres de données. Les applications du client doivent être déployées vers plusieurs centres de données à des fins de redondance et de résilience. |
