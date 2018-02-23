---
title: "Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP - Gestion de la configuration"
description: "Automatisation d’applications web FedRAMP - Gestion de la configuration"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6566783769d37ee829df3894fdb5673b4edafd2c
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="configuration-management-cm"></a>Gestion de la configuration (CM)

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication 800-53 rév. 4 du NIST.

## <a name="nist-800-53-control-cm-1"></a>NIST 800-53 Contrôle CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Stratégie et procédures de gestion de la configuration

**CM-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles de l’organisation] une stratégie de gestion de la configuration qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie de gestion de la configuration et des contrôles de gestion de la configuration associés ; et révise et met à jour la stratégie actuelle de gestion de la configuration [Affectation : fréquence définie par l’organisation] ; et les procédures de gestion de la configuration [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures de gestion de la configuration de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800-53 Contrôle CM-2

#### <a name="baseline-configuration"></a>Configuration de base

**CM-2** L’organisation développe, documente et conserve sous le contrôle de la configuration, une configuration de base courante du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les modèles Azure Resource Manager et les ressources associées composant ce programme Blueprint représentent, pour l’architecture déployée, une ligne de base de type « configuration en tant que code ». La solution est fournie par le biais de GitHub, qui peut être utilisé pour le contrôle de configuration. La solution inclut une base Configuration d’état souhaité (DSC) pour chaque machine virtuelle déployée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800-53 Contrôle CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuration de base | Révisions et mises à jour

**CM-2. (1).a** L’organisation vérifie et met à jour la configuration de base du système informatique [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue et de mettre à jour la configuration de base des ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800-53 Contrôle CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuration de base | Révisions et mises à jour

**CM-2. (1).b** L’organisation vérifie et met à jour la configuration de base du système informatique lorsque nécessaire en raison de [Affectation : circonstances définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue et de mettre à jour la configuration de base des ressources qu’il a lui-même déployées lorsque nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800-53 Contrôle CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuration de base | Révisions et mises à jour

**CM-2 (1).c** L’organisation passe en revue et met à jour la configuration de base du système informatique dans le cadre des installations et des mises à niveau des composants du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue et de mettre à jour la configuration de base des ressources qu’il a lui-même déployées lorsque nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 Contrôle CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Configuration de base | Prise en charge de l’automatisation pour la précision / l’actualisation

**CM-2 (2)** L’organisation utilise des mécanismes automatiques pour disposer d’une configuration de base à jour, complète, précise et disponible du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les modèles Azure Resource Manager et les ressources associées composant ce programme Blueprint représentent, pour l’architecture déployée, une ligne de base de type « configuration en tant que code ». La solution est fournie par le biais de GitHub, qui peut être utilisé pour le contrôle de configuration. Un script d’automatisation est disponible dans le portail Azure pour toutes les ressources déployées. Il fournit une représentation toujours à jour de ces ressources.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 Contrôle CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Configuration de base | Rétention des configurations précédentes

**CM-2 (3)** L’organisation conserve [Affectation : versions précédentes des configurations de base du système informatique définies par l’organisation] pour prendre en charge la restauration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de conserver les versions précédentes des configurations de base des ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 Contrôle CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuration de base | Configurer des systèmes, composants ou appareils pour les zones à risque élevé

**CM-2 (7).a** L’organisation dote les [Affectation : systèmes informatiques, composants systèmes ou appareils définis par l’organisation] de [Affectation : configurations définies par l’organisation] pour les individus voyageant vers des emplacements considérés par l’organisation comme étant exposés à des risques significatifs.

**Responsabilités :** `Not Applicable`

|||
|---|---|
| **Client** | Il n’existe aucun appareil physique contrôlé par le client au sein de l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Le contenu du client Microsoft Azure n’est jamais stocké en dehors de Microsoft Azure, qui est physiquement situé aux États-Unis. Le personnel Microsoft Azure ne voyage pas avec des appareils appartenant à l’inventaire des appareils Microsoft Azure. Par conséquent, ce contrôle ne s’applique pas à Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 Contrôle CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuration de base | Configurer des systèmes, composants ou appareils pour les zones à risque élevé

**CM-2 (7).b** L’organisation applique des [Affectation : dispositifs de sécurité définis par l’organisation] aux appareils lorsque les employés reviennent de leurs déplacements.

**Responsabilités :** `Not Applicable`

|||
|---|---|
| **Client** | Il n’existe aucun appareil physique contrôlé par le client au sein de l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Le contenu du client Microsoft Azure n’est jamais stocké en dehors de Microsoft Azure et le personnel Microsoft Azure ne voyage pas avec des appareils appartenant à l’inventaire des appareils Microsoft Azure. Par conséquent, ce contrôle n’est pas applicable. |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800-53 Contrôle CM-3.a

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.a** L’organisation détermine les types de modifications apportées au système informatique qui sont contrôlés par la configuration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de déterminer les types de modifications apportées aux ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels) qui sont contrôlés par la configuration. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800-53 Contrôle CM-3.b

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.b** L’organisation passe en revue les modifications contrôlées par la configuration proposées pour le système informatique et approuve ou refuse ces modifications en prenant en compte de manière explicite les analyses de l’impact sur la sécurité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue les modifications contrôlées par la configuration proposées pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800-53 Contrôle CM-3.c

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.c** L’organisation documente les décisions liées à la modification de la configuration associées au système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de documenter les modifications contrôlées par la configuration associées aux ressources qu’il a lui-même déployées (voir CM-03.b). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800-53 Contrôle CM-3.d

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.d** L’organisation met en œuvre les modifications contrôlées par la configuration approuvées pour le système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de mettre en œuvre les modifications contrôlées par la configuration approuvées dans le contrôle CM-03.b. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800-53 Contrôle CM-3.e

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.e** L’organisation conserve des enregistrements des modifications du système informatique contrôlées par la configuration pendant [Affectation : durée définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de conserver un enregistrement des modifications contrôlées par la configuration pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800-53 Contrôle CM-3.f

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.f** L’organisation audite et passe en revue les activités associées aux modifications contrôlées par la configuration apportées au système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’auditer et de passer en revue les modifications de la configuration. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800-53 Contrôle CM-3.g

#### <a name="configuration-change-control"></a>Contrôle de la modification de la configuration

**CM-3.g** L’organisation coordonne et fournit une surveillance des activités de contrôles de modification de la configuration via [Affectation : élément de contrôle de modification de la configuration défini par l’organisation (par ex., comité)] qui se réunit [Sélection (un ou plusieurs choix) : [Affectation : fréquence définie par l’organisation] ; [Affectation : conditions de modifications de la configuration définies par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de coordonner et de fournir une surveillance des activités de contrôle de modification de la configuration. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800-53 Contrôle CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Contrôle des modifications de la configuration | Documentation / Notification / Interdiction automatique des modifications

**CM-3 (1).a** L’organisation utilise des mécanismes automatiques pour documenter les modifications du système informatique proposées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser des mécanismes automatiques pour documenter les modifications proposées (voir CM-03.b). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800-53 Contrôle CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Contrôle des modifications de la configuration | Documentation / Notification / Interdiction automatique des modifications

**CM-3 (1).b** L’organisation utilise des mécanismes automatiques pour avertir [Affectation : autorités d’approbation définies par l’organisation] des modifications proposées du système informatique et demander l’approbation des modifications.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser un mécanisme automatique pour acheminer et demander l’approbation des modifications proposées pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800-53 Contrôle CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Contrôle des modifications de la configuration | Documentation / Notification / Interdiction automatique des modifications

**CM-3 (1).c** L’organisation utilise des mécanismes automatiques pour surligner les modifications du système informatique proposées qui n’ont pas été approuvées ou refusées pendant [Affectation : durée définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser un mécanisme automatique pour surligner les propositions de modifications qui n’ont pas été passées en revue. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800-53 Contrôle CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Contrôle des modifications de la configuration | Documentation / Notification / Interdiction automatique des modifications

**CM-3 (1).d** L’organisation utilise des mécanismes automatiques pour interdire des modifications du système informatique tant que les approbations requises n’ont pas été reçues.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser un mécanisme automatique pour interdire l’implémentation des modifications non approuvées pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800-53 Contrôle CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Contrôle des modifications de la configuration | Documentation / Notification / Interdiction automatique des modifications

**CM-3 (1).e** L’organisation utilise des mécanismes automatiques pour documenter toutes les modifications du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser un mécanisme automatique pour documenter toutes les modifications implémentées sur les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800-53 Contrôle CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Contrôle des modifications de la configuration | Documentation / Notification / Interdiction automatique des modifications

**CM-3 (1).f** L’organisation utilise des mécanismes automatiques pour avertir [Affectation : personnel défini par l’organisation] lorsque des modifications du système informatique approuvées sont terminées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser un mécanisme automatique pour fournir des notifications lorsque des modifications approuvées sont terminées sur les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 Contrôle CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Contrôle des modifications de la configuration | Tester / Valider / Documenter les modifications

**CM-3 (2)** L’organisation teste, valide et documente les modifications du système informatique avant de les implémenter dans le système opérationnel.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de tester, valider et documenter les modifications des ressources qu’il a lui-même déployées avant leur implémentation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 Contrôle CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Contrôle des modifications de la configuration | Représentant de la sécurité

**CM-3 (4)** L’organisation requiert qu’un représentant de la sécurité informatique soit membre de [Affectation : élément de contrôle de modification de la configuration défini par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de désigner un représentant de la sécurité informatique comme membre de l’élément de contrôle de modification de la configuration défini dans le contrôle CM-03.g. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 Contrôle CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Contrôle des modifications de la configuration | Gestion du chiffrement

**CM-3 (6)** L’organisation s’assure que les mécanismes de chiffrement utilisés pour fournir des [Affectation : dispositifs de sécurité définis par l’organisation] sont gérés par la configuration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de s’assurer que les mécanismes de chiffrement sont gérés par la configuration. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800-53 Contrôle CM-4

#### <a name="security-impact-analysis"></a>Analyse de l’impact sur la sécurité

**CM-4** L’organisation analyse les modifications du système informatique pour déterminer les impacts potentiels sur la sécurité avant de les implémenter.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’analyser les modifications proposées pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 Contrôle CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Analyse de l’impact sur la sécurité | Environnements de test séparés

**CM-4 (1)** L’organisation analyse les modifications du système informatique dans un environnement de test séparé avant leur implémentation dans un environnement opérationnel, et examine les impacts sur la sécurité dus à des défauts, des faiblesses, une incompatibilité ou une malveillance intentionnelle.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’analyser les modifications proposées pour les ressources qu’il a lui-même déployées dans un environnement de test avant leur implémentation dans un environnement opérationnel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800-53 Contrôle CM-5

#### <a name="access-restrictions-for-change"></a>Restrictions d’accès pour les modifications

**CM-5** L’organisation définit, documente, approuve et applique des restrictions d’accès physique et logique associées aux modifications du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les privilèges de compte Azure Active Directory sont implémentés à l’aide du contrôle d’accès en fonction du rôle, via l’assignation de rôles aux utilisateurs. Ceci permet de contrôler de façon stricte les utilisateurs pouvant afficher et contrôler les ressources déployées. Les privilèges de compte Active Directory sont implémentés à l’aide du contrôle d’accès en fonction du rôle, via l’assignation des utilisateurs à des groupes de sécurité. Ces groupes de sécurité contrôlent les actions que les utilisateurs peuvent effectuer concernant la configuration du système d’exploitation. Ces schémas basés sur les rôles peuvent être étendus par le client pour répondre aux besoins des missions. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 Contrôle CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Restrictions d’accès pour les modifications | Application / Audit d’accès automatique

**CM-5 (1)** Le système informatique applique des restrictions et prend en charge l’audit des actions d’application.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les privilèges de compte Azure Active Directory sont implémentés à l’aide du contrôle d’accès en fonction du rôle, via l’assignation de rôles aux utilisateurs. Ceci permet de contrôler de façon stricte les utilisateurs pouvant afficher et contrôler les ressources déployées. Les privilèges de compte Active Directory sont implémentés à l’aide du contrôle d’accès en fonction du rôle, via l’assignation des utilisateurs à des groupes de sécurité. Ces groupes de sécurité contrôlent les actions que les utilisateurs peuvent effectuer concernant la configuration du système d’exploitation. L’ensemble des accès et des tentatives d’accès font l’objet d’un audit. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 Contrôle CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Restrictions d’accès pour les modifications | Passer en revue les modifications du système

**CM-5 (2)** L’organisation passe en revue les modifications du système informatique [Affectation : fréquence définie par l’organisation] et [Affectation : circonstances définies par l’organisation] pour déterminer si des modifications non autorisées ont eu lieu.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue les modifications des ressources qu’il a lui-même déployées pour déterminer si des modifications non autorisées ont eu lieu. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 Contrôle CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Restrictions d’accès pour les modifications | Composants signés

**CM-5 (3)** Le système informatique empêche l’installation de [Affectation : composants logiciels et de microprogramme définis par l’organisation] sans vérification que le composant a été signé numériquement à l’aide d’un certificat reconnu et approuvé par l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par ce programme Blueprint mettent en œuvre Windows AppLocker pour spécifier quels utilisateurs peuvent installer et/ou exécuter des applications spécifiques. De plus, toutes les mises à niveau du système d’exploitation Windows bénéficient d’une signature numérique. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 Contrôle CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restrictions d’accès pour les modifications | Limiter les privilèges de production / opérationnels

**CM-5 (5).a** L’organisation limite les privilèges de modification des composants du système informatique et des informations liées au système au sein d’un environnement de production ou opérationnel.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de limiter les privilèges permettant d’effectuer des modifications dans les environnements de production ou opérationnels qu’il a lui-même déployés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 Contrôle CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restrictions d’accès pour les modifications | Limiter les privilèges de production / opérationnels

**CM-5 (5).b** L’organisation passe en revue et réévalue les privilèges [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue et de réévaluer les privilèges définis dans le contrôle CM-05(05).a. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800-53 Contrôle CM-6.a

#### <a name="configuration-settings"></a>Paramètres de configuration

**CM-6.a** L’organisation établit et documente les paramètres de configuration des produits informatiques utilisés au sein du système informatique avec [Affectation : listes de contrôle de configuration de la sécurité définies par l’organisation] qui reflètent le mode le plus restrictif cohérent avec les exigences opérationnelles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint inclut une ligne de base Configuration d’état souhaité (DSC) pour chaque machine virtuelle déployée. Ces scripts PowerShell déclaratifs définissent et configurent les ressources auxquelles ils sont appliqués. La base DSC incluse pour les ressources déployées par cette solution peut être étendue par le client pour répondre à des besoins métier. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 Contrôle CM-6.b

#### <a name="configuration-settings"></a>Paramètres de configuration

**CM-6.b** L’organisation met en œuvre les paramètres de la configuration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint inclut une ligne de base Configuration d’état souhaité (DSC) pour chaque machine virtuelle déployée. La base est automatiquement appliquée aux machines virtuelles durant le déploiement à l’aide de l’extension de machine virtuelle de script personnalisé. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 Contrôle CM-6.c

#### <a name="configuration-settings"></a>Paramètres de configuration

**CM-6.c** L’organisation identifie, documente et approuve les écarts par rapport aux paramètres de configuration établis pour [Affectation : composants du système informatique définis par l’organisation] en fonction de [Affectation : exigences opérationnelles définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’identifier, de documenter et d’approuver les écarts par rapports aux paramètres de configuration établis pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800-53 Contrôle CM-6.d

#### <a name="configuration-settings"></a>Paramètres de configuration

**CM-6.d** L’organisation surveille et contrôle les modifications des paramètres de la configuration conformément aux stratégies et aux procédures organisationnelles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie Automation DSC. Automation DSC aligne les configurations des ordinateurs avec une configuration spécifique définie par l’organisation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 Contrôle CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Paramètres de configuration | Gestion / Application / Vérification centrale automatique

**CM-6 (1)** L’organisation utilise des mécanismes automatiques pour gérer, appliquer et vérifier centralement les paramètres de configuration pour [Affectation : composants du système informatique définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie Azure Automation DSC. Automation DSC aligne les configurations des ordinateurs avec une configuration spécifique définie par l’organisation et surveilles les modifications en continu. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 Contrôle CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Paramètres de configuration | Répondre aux modifications non autorisées

**CM-6 (2)** L’organisation utilise [Affectation : dispositifs de sécurité définis par l’organisation] pour répondre aux modifications non autorisées de [Affectation : paramètres de configuration définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie Azure Automation DSC. Automation DSC, qui fait partie d’Operations Management Suite (OMS) d’Azure, peut être configuré pour générer une alerte ou pour résoudre les configurations erronées détectées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800-53 Contrôle CM-7.a

#### <a name="least-functionality"></a>Fonctionnalités essentielles

**CM-7.a** L’organisation configure le système informatique pour ne fournir que les fonctionnalités essentielles.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources déployées par ce programme Blueprint sont configurées pour fournir les fonctionnalités essentielles à leur objectif d’utilisation principal. Une base Configuration d’état souhaité (DSC) est incluse pour chaque machine virtuelle déployée. Ces scripts PowerShell déclaratifs définissent et configurent les ressources auxquelles ils sont appliqués. La base DSC incluse pour les ressources déployées par cette solution peut être étendue par le client pour limiter davantage les fonctionnalités afin de répondre à des besoins métier. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800-53 Contrôle CM-7.b

#### <a name="least-functionality"></a>Fonctionnalités essentielles

**CM-7.b** L’organisation interdit ou restreint l’utilisation des fonctions, ports, protocoles et/ou services suivants : [Affectation : fonctions, ports, protocoles et/ou services interdits ou restreints définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie Azure Application Gateway et des groupes de sécurité réseau pour restreindre l’utilisation des ports et protocoles uniquement à ceux nécessaires. La configuration d’Application Gateway, des groupes de sécurité réseau et des bases DSC pour les machines virtuelles peut être affinée par le client pour restreindre l’utilisation de fonctions, ports, protocoles et services afin de proposer uniquement les fonctionnalités requises. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800-53 Contrôle CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>Fonctionnalités essentielles | Revue périodique

**CM-7 (1).a** L’organisation passe en revue le système informatique [Affectation : fréquence définie par l’organisation] pour identifier les fonctions, ports, protocoles et services non nécessaires et/ou non sécurisés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue les ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels) pour identifier les fonctions, ports, protocoles et services non nécessaires et/ou non sécurisés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800-53 Contrôle CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>Fonctionnalités essentielles | Revue périodique

**CM-7 (1).b** L’organisation désactive [Affectation : fonctions, ports, protocoles et services définis par l’organisation au sein du système informatique considérés comme non nécessaires et/ou non sécurisés].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de désactiver les fonctions, ports, protocoles et services considérés comme non nécessaires ou non sécurisés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 Contrôle CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Fonctionnalités essentielles | Empêcher l’exécution d’un programme

**CM-7 (2)** Le système informatique empêche l’exécution d’un programme conformément aux [Sélection (un ou plusieurs choix) : [Affectation : stratégies concernant l’utilisation et les restrictions d’utilisation d’un logiciel]; règles autorisant les conditions d’utilisation d’un logiciel].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’empêcher l’exécution d’un programme conformément aux stratégies d’utilisation d’un logiciel qu’il a lui-même définies. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 Contrôle CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>Fonctionnalités essentielles | Logiciels autorisés / Mise sur liste verte

**CM-7 (5).a** L’organisation identifie [Affectation : logiciels définis par l’organisation dont l’exécution est autorisés sur le système informatique].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’identifier les logiciels autorisés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 Contrôle CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>Fonctionnalités essentielles | Logiciels autorisés / Mise sur liste verte

**CM-7 (5).b** L’organisation utilise une stratégie tout refuser, autoriser par exception pour autoriser l’exécution de logiciels autorisés sur le système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser une stratégie tout refuser, autoriser par exception pour autoriser l’exécution de logiciels autorisés sur les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 Contrôle CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>Fonctionnalités essentielles | Logiciels autorisés / Mise sur liste verte

**CM-7 (5).c** L’organisation vérifie et met à jour la liste des logiciels autorisés [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer en revue et de mettre à jour la liste des logiciels autorisés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800-53 Contrôle CM-8.a

#### <a name="information-system-component-inventory"></a>Inventaire des composants du système informatique

**CM-8.a** L’organisation développe et documente un inventaire des composants du système informatique qui reflète de manière précise le système informatique actuel ; inclut tous les composants dans la limite d’autorisation du système informatique ; a le niveau de granularité considéré nécessaire pour le suivi et la création de rapports ; et inclut [Affectation : informations définies par l’organisation considérées nécessaires pour obtenir une connaissance efficace des composants du système informatique].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie toutes les ressources dans un groupe de ressources Azure Resource Manager. Azure Resource Manager fournit une liste toujours à jour des ressources déployées et peut être personnalisé pour baliser et regrouper des ressources à des fins de gestion d’inventaire. Les ressources déployées par cette solution sont dotées d’une étiquette de ressource spécifique qui peut être associée aux limites du système. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800-53 Contrôle CM-8.b

#### <a name="information-system-component-inventory"></a>Inventaire des composants du système informatique

**CM-8.b** L’organisation passe en revue et met à jour l’inventaire des composants du système informatique [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie toutes les ressources dans un groupe de ressources Azure Resource Manager. Azure Resource Manager fournit une liste toujours à jour des ressources déployées qui est disponible dans le portail Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 Contrôle CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventaire des composants du système informatique | Mises à jour durant les installations / suppressions

**CM-8 (1)** L’organisation met à jour l’inventaire des composants du système informatique dans le cadre des installations et suppressions de composants, et des mises à jour du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie toutes les ressources dans un groupe de ressources Azure Resource Manager. Le panneau des ressources dans le portail Azure répertorie toutes les ressources déployées, et fournit un inventaire toujours à jour au fur et à mesure que des ressources sont déployées et supprimées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 Contrôle CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Inventaire des composants du système informatique | Maintenance automatique

**CM-8 (2)** L’organisation utilise des mécanismes automatiques pour disposer d’un inventaire à jour, complet, précis et disponible des composants du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie toutes les ressources dans un groupe de ressources Azure Resource Manager. Le panneau des ressources dans le portail Azure répertorie toutes les ressources déployées, et fournit un inventaire toujours à jour au fur et à mesure que des ressources sont déployées et supprimées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 Contrôle CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventaire des composants du système informatique | Détection automatique de composant non autorisé

**CM-8 (3).a** L’organisation utilise des mécanismes automatiques [Affectation : fréquence définie par l’organisation] pour détecter la présence de composants matériels, logiciels et de microprogramme non autorisés au sein du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’utiliser des mécanismes automatiques pour détecter la présence de logiciels non autorisés sur les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 Contrôle CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventaire des composants du système informatique | Détection automatique de composant non autorisé

**CM-8 (3).b** L’organisation effectue les actions suivantes lorsque des composants non autorisés sont détectés : [Sélection (un ou plusieurs choix) : désactive l’accès au réseau pour ces composants ; isole les composants ; avertit [Affectation : personnel ou rôles définis par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de passer à l’action lorsque des logiciels non autorisés sont détectés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 Contrôle CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Inventaire des composants du système informatique | Informations de responsabilité

**CM-8 (4)** L’organisation inclut dans l’inventaire des composants du système informatique, une méthode permettant d’identifier par [Sélection (un ou plusieurs choix) : nom ; titre ; rôle], les individus responsables de la gestion de ces composants.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie toutes les ressources dans un groupe de ressources Azure Resource Manager. Les étiquettes de ressource Azure sont des paires clé / valeur qui peuvent être utilisées pour classer les ressources à des fins de comptabilité et/ou de gestion. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 Contrôle CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventaire des composants du système informatique | Pas de comptabilité dupliquée des composants

**CM-8 (5)** L’organisation vérifie que tous les composants dans la limite d’autorisation du système informatique ne sont pas dupliqués dans d’autres inventaires des composants du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Ce programme Blueprint déploie toutes les ressources dans un groupe de ressources Azure Resource Manager. Azure Resource Manager fournit une liste toujours à jour des ressources déployées. Les ressources déployées par cette solution sont dotées d’une étiquette de ressource spécifique qui peut être associée aux limites du système. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800-53 Contrôle CM-9.a

#### <a name="configuration-management-plan"></a>Plan de gestion de la configuration

**CM-9.a** L’organisation développe, documente et met en œuvre un plan de gestion de la configuration pour le système informatique qui traite des rôles, des responsabilités, des processus et des procédures de gestion de la configuration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de développer, documenter et mettre en œuvre un plan de gestion de la configuration pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800-53 Contrôle CM-9.b

#### <a name="configuration-management-plan"></a>Plan de gestion de la configuration

**CM-9.b** L’organisation développe, documente et met en œuvre un plan de gestion de la configuration pour le système informatique qui établit un processus d’identification des éléments de la configuration au cours du cycle de vie du développement du système et de gestion de la configuration des éléments de configuration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de développer, documenter et mettre en œuvre un plan de gestion de la configuration pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 Contrôle CM-9.c

#### <a name="configuration-management-plan"></a>Plan de gestion de la configuration

**CM-9.c** L’organisation développe, documente et met en œuvre un plan de gestion de la configuration pour le système informatique qui définit les éléments de configuration du système informatique et place les éléments de configuration sous la gestion de la configuration.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de développer, documenter et mettre en œuvre un plan de gestion de la configuration pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800-53 Contrôle CM-9.d

#### <a name="configuration-management-plan"></a>Plan de gestion de la configuration

**CM-9.d** L’organisation développe, documente et met en œuvre un plan de gestion de la configuration pour le système informatique qui protège le plan de gestion de la configuration contre toute divulgation ou modification non autorisée.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de développer, documenter et mettre en œuvre un plan de gestion de la configuration pour les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800-53 Contrôle CM-10.a

#### <a name="software-usage-restrictions"></a>Restrictions d’utilisation de logiciels

**CM-10.a** L’organisation utilise des logiciels et la documentation associée conformément aux contrats et lois sur le copyright.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les licences Windows et SQL Server sont incluses pour les ressources déployées par ce programme Blueprint. Il s’agit d’une fonctionnalité intégrée à Azure. Les organisations disposant d’accords de licence logicielle existants peuvent envisager de déployer des modèles de licence différents. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800-53 Contrôle CM-10.b

#### <a name="software-usage-restrictions"></a>Restrictions d’utilisation de logiciels

**CM-10.b** L’organisation effectue le suivi des logiciels et de la documentation associée protégés par des licences basées sur la quantité pour contrôler la copie et la distribution.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les licences Windows et SQL Server sont incluses pour les ressources déployées par ce programme Blueprint. L’utilisateur n’a pas besoin d’effectuer le suivi séparé de l’utilisation des licences. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800-53 Contrôle CM-10.c

#### <a name="software-usage-restrictions"></a>Restrictions d’utilisation de logiciels

**CM-10.c** L’organisation contrôle et documente l’utilisation de la technologie de partage de fichiers pair à pair pour s’assurer que cette fonctionnalité n’est pas utilisée à des fins de distribution, d’affichage, de performance ou de reproduction non autorisés de travail soumis aux lois de copyright.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucune fonctionnalité de partage de fichiers pair à pair n’est déployée par ce programme Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 Contrôle CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Restrictions d’utilisation de logiciels | Logiciels open source

**CM-10 (1)** L’organisation établit les restrictions suivantes sur l’utilisation de logiciels open source : [Affectation : restrictions définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de gestion de la configuration de niveau entreprise du client peut traiter les restrictions sur l’utilisation de logiciels open source. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800-53 Contrôle CM-11.a

#### <a name="user-installed-software"></a>Logiciels installés par le client

**CM-11.a** L’organisation établit [Affectation : stratégies définies par l’organisation] régissant l’installation de logiciels par les utilisateurs.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’établir une stratégie régissant l’installation de logiciels par les utilisateurs sur les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800-53 Contrôle CM-11.b

#### <a name="user-installed-software"></a>Logiciels installés par le client

**CM-11.b** L’organisation applique des stratégies d’installation de logiciels via [Affectation : méthodes définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’appliquer des stratégies d’installation des logiciels. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800-53 Contrôle CM-11.c

#### <a name="user-installed-software"></a>Logiciels installés par le client

**CM-11.c** L’organisation surveille la conformité à la stratégie [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de surveiller la conformité des ressources qu’il a lui-même déployées avec les stratégies identifiées dans le contrôle CM-11.a. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 Contrôle CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Logiciels installés par l’utilisateur | Alertes pour les installations non autorisées

**CM-11 (1)** Le système informatique alerte [Affectation : personnel ou rôles définis par l’organisation] en cas de détection d’une installation de logiciel non autorisée.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de fournir des alertes en cas de détection d’installation de logiciel non autorisée. |
| **Fournisseur (Microsoft Azure)** | Non applicable |

