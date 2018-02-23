---
title: "Solution Blueprint Sécurité et conformité Azure - Automatisation d’applications web FedRAMP - Protection du système et des communications"
description: "Automatisation d’applications web FedRAMP - Protection du système et des communications"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: ce0917cec67612736103932903eab18d7f0f21bb
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="system-and-communications-protection-sc"></a>Protection du système et des communications (SC)

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication NIST 800-53 Rév. 4.

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 Contrôle SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Stratégie et procédures de protection du système et des communications

**SC-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles de l’organisation] une stratégie de protection du système et des communications qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie de protection du système et des communications et des contrôles de protection du système et des communications associés ; et révise et met à jour la stratégie actuelle de protection du système et des communications [Affectation : fréquence définie par l’organisation] ; et les procédures de protection du système et des communications [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures de protection du système et des communications de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 Contrôle SC-2

#### <a name="application-partitioning"></a>Partitionnement d’application

**SC-2** Le système informatique sépare les fonctionnalités utilisateur (notamment les services d’interface utilisateur) des fonctionnalités de gestion du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint sépare les fonctionnalités utilisateur des fonctionnalités de gestion du système informatique via l’application de contrôles d’accès logique et l’architecture du système. Les fonctionnalités utilisateur sont limitées aux interfaces d’applications web déployées par le client. Les interfaces des fonctionnalités de gestion du système sont distinctes des interfaces utilisateur. La connectivité de gestion s’effectue par le biais d’un hôte bastion sécurisé (jumpbox) situé dans un sous-réseau de gestion avec des règles de groupe de sécurité réseau pour limiter l’accès aux ressources de production tel que nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 Contrôle SC-3

#### <a name="security-function-isolation"></a>Isolation des fonctions de sécurité

**SC-3** Le système informatique isole les fonctions de sécurité des fonctions non liées à la sécurité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par cette solution Blueprint exécutent des systèmes d’exploitation Windows. Windows gère des domaines d’exécution séparés pour chaque processus d’exécution en affectant un espace d’adresse virtuelle privée à chaque processus. En outre, la solution implémente une architecture et des contrôles d’accès conçus pour isoler les fonctionnalités de sécurité lorsque cela est nécessaire. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 Contrôle SC-4

#### <a name="information-in-shared-resources"></a>Informations dans les ressources partagées

**SC-4** Le système informatique empêche le transfert d’informations non autorisé et non intentionnel via des ressources système partagées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par cette solution Blueprint exécutent des systèmes d’exploitation Windows. Le système d’exploitation gère les ressources (par ex. mémoire, stockage) de manière à ce que les informations ne soient accessibles que par les utilisateurs et les rôles disposant des autorisations appropriées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 Contrôle SC-5

#### <a name="denial-of-service-protection"></a>Protection contre le déni de service

**SC-5** Le système informatique protège contre ou limite les effets des types suivants d’attaques par déni de service : [Affectation : types d’attaques par déni de service définis par l’organisation ou références à des sources pour ces informations] en utilisant [Affectation : dispositifs de sécurité définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie une passerelle d’application qui inclut un pare-feu d’applications web et des fonctionnalités d’équilibrage de charge. Les machines virtuelles déployées prenant en charge le niveau web, le niveau de base de données et Active Directory sont déployées dans un groupe à haute disponibilité évolutif. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 Contrôle SC-6

#### <a name="resource-availability"></a>Disponibilité des ressources

**SC-6** Le système informatique protège la disponibilité des ressources en affectant [Affectation : ressources définies par l’organisation] par [Sélection (un ou plusieurs choix) ; priorité ; quota ; [Affectation : dispositifs de sécurité définis par l’organisation]].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par cette solution Blueprint exécutent des systèmes d’exploitation Windows. Chaque processus Windows fournit les ressources nécessaires pour exécuter un programme. La priorité des ressources est gérée par le système d’exploitation. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 Contrôle SC-7.a

#### <a name="boundary-protection"></a>Protection de la limite

**SC-7.a** Le système informatique surveille et contrôle les communications sur la limite externe du système et des limites internes clés au sein du système.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie une et un équilibreur de charge, et configure des règles de groupe de sécurité réseau pour contrôler les commutations aux limites externes et entre les sous-réseaux internes. OMS Log Analytics collecte les journaux de diagnostic et des événements pour la passerelle Application Gateway, l’équilibreur de charge et le groupe de sécurité réseau, pour permettre le monitoring par le client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 Contrôle SC-7.b

#### <a name="boundary-protection"></a>Protection de la limite

**SC-7.b** Le système informatique implémente des sous-réseaux pour les composants système accessibles publiquement qui sont [Sélection : physiquement ; logiquement] séparés des réseaux internes à l’organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire aux fonctionnalités système et de gestion (par exemple, le trafic externe ne peut pas accéder aux sous-réseaux de base de données, de gestion ou Active Directory). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 Contrôle SC-7.c

#### <a name="boundary-protection"></a>Protection de la limite

**SC-7.c** Le système informatique se connecte à des réseaux ou des systèmes informatiques externes uniquement par le biais d’interfaces gérées consistant en des appareils de protection de la limite organisés selon l’architecture de sécurité d’une organisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie une passerelle d’application pour gérer les connexions externes à une application web déployée par le client. Les connexions externes pour l’accès en gestion sont limitées à un hôte bastion/jumpbox déployé dans un sous-réseau de gestion avec des règles de sécurité réseau appliquées pour restreindre les connexions externes aux adresses IP autorisées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 Contrôle SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Protection de la limite | Points d’accès

**SC-7 (3)** L’organisation limite le nombre de connexions réseau externes au système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie deux adresses IP publiques : l’une associée à la passerelle d’application et l’autre adresse associée à l’hôte bastion/jumpbox de gestion. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800-53 Contrôle SC-7 (4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>Protection de la limite | Services de télécommunications externes

**SC-7 (4).a** L’organisation implémente une interface de gestion pour chaque service de télécommunication externe.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie deux adresses IP publiques : l’une associée à la passerelle d’application et l’autre adresse associée à l’hôte bastion/jumpbox de gestion. La gestion de ces interfaces est activée via une mise en réseau définie par logiciel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800-53 Contrôle SC-7 (4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>Protection de la limite | Services de télécommunications externes

**SC-7 (4).b** L’organisation établit une stratégie de flux du trafic pour chaque interface gérée.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie deux adresses IP publiques : l’une associée à la passerelle d’application et l’autre adresse associée à l’hôte bastion/jumpbox de gestion. La gestion de ces interfaces est activée via une mise en réseau définie par logiciel. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800-53 Contrôle SC-7 (4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>Protection de la limite | Services de télécommunications externes

**SC-7 (4).c** L’organisation protège la confidentialité et l’intégrité des informations transmises via chaque interface.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La passerelle d’application web déployée par cette solution Blueprint est configurée avec un écouteur HTTPS, pour assurer la confidentialité et l’intégrité des sessions de communication. Les connexions Bureau à distance au jumpbox sont également chiffrées pour offrir confidentialité et intégrité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800-53 Contrôle SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>Protection de la limite | Services de télécommunications externes

**SC-7 (4).d** L’organisation documente chaque exception à la stratégie de flux du trafic à l’aide d’une requête de mission/métier connexe et la durée de cette requête.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les clients ne sont pas responsables des opérations du centre de données (pour inclure les services de télécommunications). Tous les services de télécommunications sont fournis et gérés par Microsoft Azure. Ce contrôle est hérité d’Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800-53 Contrôle SC-7 (4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>Protection de la limite | Services de télécommunications externes

**SC-7 (4).e** L’organisation passe en revue les exceptions à la stratégie de flux du trafic [Affectation : fréquence définie par l’organisation] et supprime les exceptions qui ne sont plus liées à une requête de mission/métier explicite.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les clients ne sont pas responsables des opérations du centre de données (pour inclure les services de télécommunications). Tous les services de télécommunications sont fournis et gérés par Microsoft Azure. Ce contrôle est hérité d’Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 Contrôle SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Protection de la limite | Refuser par défaut / Autoriser par exception

**SC-7 (5)** Le système informatique sur les interfaces gérées refuse le trafic des communications réseau par défaut et autorise le trafic des communications réseau par exception (c’est-à-dire, tout refuser, autoriser par exception).

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ensembles de règles appliqués aux groupes de sécurité réseau déployés par cette solution Blueprint sont configurés à l’aide d’un schéma « refuser par défaut ». |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 Contrôle SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Protection de la limite | Empêcher le tunneling fractionné pour les appareils distants

**SC-7 (7)** Le système informatique, en association avec un appareil distant, empêche l’appareil d’établir simultanément des connexions non distantes avec le système et de communiquer via une autre connexion avec des ressources dans des réseaux externes.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie de configuration d’appareil distant de niveau entreprise du client peut traiter le tunneling fractionné. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 Contrôle SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Protection de la limite | Acheminer le trafic aux serveurs proxy authentifiés

**SC-7 (8)** Le système informatique achemine [Affectation : trafic des communications internes défini par l’organisation] à [Affectation : réseaux externes définis par l’organisation] par le biais de serveurs proxy authentifiés sur des interfaces gérées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’acheminer les informations qu’il a lui-même définies par le biais d’un proxy authentifié à un réseau externe. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 Contrôle SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Protection de la limite | Empêcher l’exfiltration non autorisée

**SC-7 (10)** L’organisation empêche l’exfiltration non autorisée d’informations via les interfaces gérées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client d’empêcher l’exfiltration non autorisée d’informations via les interfaces gérées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 Contrôle SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Protection de la limite | Protection basée sur hôte

**SC-7 (12)** L’organisation met en œuvre [Affectation : mécanismes de protection de la limite basés sur l’hôte définis par l’organisation] à [Affectation : composants du système d’information définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par cette solution Blueprint sont configurées avec un pare-feu basé sur un hôte activé. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 Contrôle SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Protection de la limite | Isolation des outils de sécurité / mécanismes / composants de support

**SC-7 (13)** L’organisation isole [Affectation : outils de sécurité, mécanismes et composants de support définis par l’organisation] des autres composants du système informatique interne en implémentant des sous-réseaux avec des interfaces gérées séparés physiquement des autres composants du système.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie des ressources dans une architecture avec un sous-réseau de gestion séparé pour le déploiement client de composants de support et d’outils de sécurité des informations. Les sous-réseaux sont séparés logiquement par des règles de groupe de sécurité réseau. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 Contrôle SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Protection de la limite | Échec en toute sécurité

**SC-7 (18)** Le système informatique échoue en toute sécurité en cas d’échec opérationnel d’un appareil de protection de la limite.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun appareil de protection de la limite physique au sein de l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure déploie un VPN SSL et des serveurs de passerelle redondants et séparés géographiquement. Lorsqu’un système de passerelle échoue, il le fait en toute sécurité et l’accès à l’environnement est restreint. Afin d’établir une connexion à l’environnement Microsoft Azure, un utilisateur doit établir une connexion distincte à un serveur de passerelle actif géré par Microsoft Azure. <br /> En outre, si les appareils réseau Microsoft Azure (notamment les routeurs de périphérie, les routeurs d’accès, les équilibreurs de charge, les commutateurs d’agrégation et TORS) échouent, le circuit affecté est déconnecté, échouant ainsi en toute sécurité. L’échec d’un appareil réseau Microsoft Azure ne peut pas mener à, ni causer, l’entrée d’informations externes au système dans l’appareil. Un échec ne doit pas non plus permettre la publication d’informations non autorisée. La redondance intégrée permet l’échec des ressources Microsoft Azure sans impact sur la disponibilité. |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 Contrôle SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Protection de la limite | Isolation / Ségrégation dynamique

**SC-7 (20)** Le système informatique fournit la possibilité d’isoler de manière dynamique [Affectation : composants du système informatique définis par l’organisation] des autres composants du système.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de s’assurer que le système a la possibilité d’isoler de manière dynamique les ressources qu’il a lui-même déployées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 Contrôle SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Protection de la limite | Isolation des composants du système informatique

**SC-7 (21)** L’organisation utilise des mécanismes de protection pour séparer [Affectation : composants du système informatique définis par l’organisation] prenant en charge [Affectation : missions et/ou fonctions métier définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire pour les fonctionnalités système et de gestion. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 Contrôle SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Confidentialité et intégrité des transmissions

**SC-8** Le système informatique protège [Sélection (un ou plusieurs choix) : la confidentialité ; l’intégrité] des informations transmises.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’implémentation de SI-8 (1) est conforme à cette exigence de contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 Contrôle SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Confidentialité et intégrité des transmissions | Protection par chiffrement ou protection physique différente

**SC-8 (1)** Le système informatique met en œuvre des mécanismes de chiffrement pour [Sélection (un ou plusieurs choix) : empêcher la divulgation non autorisée d’informations ; détecter les modifications apportées aux informations] durant la transmission sauf en cas de protection autre à l’aide de [Affectation : dispositifs de sécurité physiques différents définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint configure des ressources pour communiquer à l’aide de protocoles sécurisés uniquement. Le composant WAF du service Application Gateway est configuré pour accepter les services de communication utilisés en externe avec les protocoles HTTPS/TLS et pour communiquer avec le pool principal uniquement avec les protocoles HTTPS/TLS. SQL Server est configuré pour communiquer uniquement via HTTPS/TLS. Les services Bureau à distance sont configurés pour utiliser des connexions sécurisées. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 Contrôle SC-10

#### <a name="network-disconnect"></a>Déconnexion réseau

**SC-10** Le système informatique met fin à la connexion réseau associée à une session de communications à la fin de la session ou après [Affectation : durée définie par l’organisation] d’inactivité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’authentification pour les sessions Bureau à distance est gérée par Active Directory. Une fois l’accès d’un utilisateur désactivé dans Active Directory, les sessions à distance prennent immédiatement fin. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 Contrôle SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Établissement et gestion de clés de chiffrement

**SC-12** L’organisation établit et gère des clés de chiffrement pour le chiffrement requis utilisé dans le système informatique conformément aux [Affectation : exigences définies par l’organisation pour la création, la distribution, le stockage, l’accès et la destruction des clés].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint déploie un Azure Key Vault. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Azure Key Vault peut créer des clés à l’aide d’une fonctionnalité de création de clé par module de sécurité matériel (HSM) FIPS 140-2 de niveau 2. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 Contrôle SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Établissement et gestion de clés de chiffrement | Disponibilité

**SC-12 (1)** L’organisation fait en sorte que les informations restent disponibles en cas de perte par les utilisateurs des clés de chiffrement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Azure Key Vault permet de stocker les clés de chiffrement et les secrets utilisés dans cette solution Blueprint. Key Vault simplifie le processus de gestion des clés pour les clés qui chiffrent les données et y accèdent. Les authentificateurs suivants sont stockés dans Key Vault : mot de passe Azure pour déployer le compte, mot de passe administrateur de la machine virtuelle, mot de passe de compte de service SQL Server. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 Contrôle SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Établissement et gestion de clés de chiffrement | Clés symétriques

**SC-12 (2)** L’organisation produit, contrôle et distribue des clés de chiffrement symétriques à l’aide de processus et d’une technologie de gestion des clés [Sélection : conformes à NIST FIPS ; approuvés par NSA].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Azure Key Vault permet de générer, de contrôler et de distribuer des clés de chiffrement. Azure Key Vault peut créer des clés à l’aide d’une fonctionnalité de création de clé par module de sécurité matériel (HSM) FIPS 140-2 de niveau 2. Les clés sont stockées et gérées dans des conteneurs chiffrés en toute sécurité dans Azure Key Vault. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 Contrôle SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Établissement et gestion de clés de chiffrement | Clés asymétriques

**SC-12 (3)** L’organisation produit, contrôle et distribue des clés de chiffrement asymétriques à l’aide de [Sélection : processus et technologie de gestion des clés approuvés par NSA ; matériel de saisie prépositionné ou certificats de Classe 3 PKI approuvés ; jetons de sécurité du matériel et certificats de Classe 3 ou de Classe 4 PKI approuvés pour protéger la clé privée de l’utilisateur].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de générer, contrôler et distribuer les clés de chiffrement asymétriques (si elles sont utilisées dans les ressources qu’il a lui-même déployées). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 Contrôle SC-13

#### <a name="cryptographic-protection"></a>Protection par chiffrement

**SC-13** Le système informatique met en œuvre [Affectation : utilisations du chiffrement définies par l’organisation et type de chiffrement requis pour chaque utilisation] conformément aux lois fédérales, décrets présidentiels, directives, stratégies, réglementations et normes applicables.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint utilise l’authentification Windows, le Bureau à distance et BitLocker. Ces composants peuvent être configurés d’après les modules de chiffrement FIPS 140 validés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 Contrôle SC-15.a

#### <a name="collaborative-computing-devices"></a>Appareils informatiques collaboratifs

**SC-15.a** Le système informatique interdit l’activation à distance d’appareils informatiques collaboratifs avec les exceptions suivantes : [Affectation : exceptions définies par l’organisation où l’activation à distance est autorisée].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint n’entraîne aucun déploiement d’appareil informatique collaboratif. Remarque : des appareils informatiques collaboratifs physiques existent au sein de l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 Contrôle SC-15.b

#### <a name="collaborative-computing-devices"></a>Appareils informatiques collaboratifs

**SC-15.b** Le système informatique fournit une indication d’utilisation explicite aux utilisateurs présents physiquement devant les appareils.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint n’entraîne aucun déploiement d’appareil informatique collaboratif. Remarque : des appareils informatiques collaboratifs physiques existent au sein de l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 Contrôle SC-17

#### <a name="public-key-infrastructure-certificates"></a>Certificats d’infrastructure de clé publique

**SC-17** L’organisation émet des certificats de clé publique dans le cadre d’une [Affectation : stratégie de certificat définie par l’organisation] ou obtient des certificats de clé publique auprès d’un fournisseur de servies agréé.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut utiliser une infrastructure de clé publique de niveau entreprise pour émettre le certificat. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 Contrôle SC-18.a

#### <a name="mobile-code"></a>Code mobile

**SC-18.a** L’organisation définit un code mobile et des technologies de code mobile acceptables et inacceptables.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de protection des communications et du système de niveau entreprise du client peuvent définir le code mobile acceptable et inacceptable. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 Contrôle SC-18.b

#### <a name="mobile-code"></a>Code mobile

**SC-18.b** L’organisation établit des restrictions d’utilisation et des directives d’implémentation pour le code mobile et les technologies de code mobile.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les procédures de protection des communications et du système de niveau entreprise du client peuvent établir les restrictions d’utilisation de code mobile. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 Contrôle SC-18.c

#### <a name="mobile-code"></a>Code mobile

**SC-18.c** L’organisation autorise, surveille et contrôle l’utilisation de code mobile au sein du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut utiliser un processus de niveau entreprise pour l’autorisation, le monitoring et le contrôle de l’utilisation de code mobile. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 Contrôle SC-19.a

#### <a name="voice-over-internet-protocol"></a>Protocole voix sur IP (VoIP)

**SC-19.a** L’organisation établit des restrictions d’utilisation et des directives d’implémentation pour les technologies de protocole VoIP (voix sur IP) en fonction des dommages potentiels au système informatique en cas d’utilisation malveillante.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint n’entraîne aucun déploiement de technologie de protocole VoIP. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 Contrôle SC-19.b

#### <a name="voice-over-internet-protocol"></a>Protocole voix sur IP (VoIP)

**SC-19.b** L’organisation autorise, surveille et contrôle l’utilisation de VoIP au sein du système informatique.

**Responsabilités :** `Customer Only`

|||
|---|---|
| Client | Cette solution Blueprint n’entraîne aucun déploiement de technologie de protocole VoIP. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 Contrôle SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Service sécurisé de résolution de nom / d’adresse (source faisant autorité)

**SC-20.a** Le système informatique fournit des artefacts supplémentaires de vérification de l’intégrité et d’authentification de l’origine des données, ainsi que les données de résolution de nom faisant autorité que le système retourne en réponse aux requêtes de résolution de nom/d’adresse externes.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de fournir un service de résolution de nom et d’adresse sécurisé. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 Contrôle SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Service sécurisé de résolution de nom / d’adresse (source faisant autorité)

**SC-20.b** Le système informatique fournit les moyens d’indiquer l’état de sécurité des zones enfants et (si l’enfant prend en charge les services de résolution sécurisés) d’activer la vérification d’une chaîne de confiance entre des domaines parents et enfants, dans le cadre d’un espace de noms hiérarchique et distribué.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de fournir un service de résolution de nom et d’adresse sécurisé. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 Contrôle SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Service sécurisé de résolution de nom / d’adresse (outil de résolution récursif ou de mise en cache)

**SC-21** Le système informatique demande et effectue l’authentification de l’origine des données et la vérification de l’intégrité des données sur les réponses de résolution de nom/d’adresse que le système reçoit de sources faisant autorité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de configurer les ressources qu’il a lui-même déployées afin de demander et d’exécuter l’authentification de l’origine des données et la vérification de l’intégrité des données sur les réponses de résolution de nom/d’adresse que le système reçoit de sources faisant autorité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800-53 Contrôle SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architecture et provisionnement du service de résolution de nom / d’adresse

**SC-22** Les systèmes informatiques qui fournissent collectivement un service de résolution de nom/d’adresse pour une organisation sont tolérants aux pannes et mettent en œuvre une séparation de rôle interne/externe.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de s’assurer que les systèmes fournissant des services de résolution d’adresse pour les ressources qu’il a lui-même déployées sont tolérants aux pannes et mettent en œuvre une séparation de rôle interne/externe. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 Contrôle SC-23

#### <a name="session-authenticity"></a>Authenticité de session

**SC-23** Le système informatique protège l’authenticité des sessions de communications.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès à distance aux ressources déployées par cette solution Blueprint, y compris le portail Azure, la connexion Bureau à distance et la passerelle d’application web, est sécurisé par TLS. TLS fournit l’authenticité des communications au niveau de la session. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800-53 Contrôle SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Authenticité de session | Invalider les identificateurs de session lors de la déconnexion

**SC-23 (1)** Le système informatique invalide les identificateurs de session lors de la déconnexion de l’utilisateur ou autre mode de fin de session.

**Responsabilités :** `Customer Only`

|||
|---|---|
| Client | L’accès à distance aux ressources déployées par cette solution Blueprint, y compris le portail Azure, la connexion Bureau à distance et la passerelle d’application web, est sécurisé par TLS. Le portail Azure et les sessions Bureau à distance invalident les identificateurs de session lors de la déconnexion. L’invalidation de session web est appliquée via les règles Azure Application Gateway - Pare-feu d’applications web (WAF). Le pare-feu d’applications web (WAF) applique l’affinité de cookie par session et exécute une expiration de la session après 30 minutes (configurable après le déploiement sur des règles spécifiques à l’organisation) d’inactivité du client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 Contrôle SC-24

#### <a name="fail-in-known-state"></a>Échec en état connu

**SC-24** Le système informatique échoue en un [Affectation : état connu défini par l’organisation] pour [Affectation : types d’échec définis par l’organisation] préservant [Affectation : informations sur l’état du système définies par l’organisation] en cas d’échec.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Il incombe au client de s’assurer que les ressources qu’il a lui-même déployées échouent en état connu. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 Contrôle SC-28

#### <a name="protection-of-information-at-rest"></a>Protection des informations au repos

**SC-28** Le système informatique protège [Sélection (un ou plusieurs choix) : la confidentialité ; l’intégrité] des [Affectation : informations au repos définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’implémentation de SC-28 (1) est conforme à cette exigence de contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800-53 Contrôle SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Protection des informations au repos | Protection par chiffrement

**SC-28 (1)** Le système informatique met en œuvre des mécanismes de chiffrement pour empêcher la divulgation et la modification non autorisées des [Affectation : informations définies par l’organisation] sur [Affectation : composants du système informatique définis par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par cette solution Blueprint mettent en œuvre le chiffrement de disque pour protéger la confidentialité et l’intégrité des informations au repos. Azure Disk Encryption pour Windows est implémenté à l’aide de la fonctionnalité BitLocker de Windows. SQL Server est configuré pour utiliser Transparent Data Encryption (TDE), qui effectue le chiffrement et le déchiffrement en temps réel des données et fichiers journaux pour protéger les informations au repos. TDE protège les données des accès non autorisés. Le client peut choisir de mettre en œuvre des contrôles au niveau de l’application supplémentaires pour protéger l’intégrité des informations stockées. La confidentialité et l’intégrité de tout stockage blob déployé par cette solution Blueprint sont protégées par le biais du chiffrement du service de stockage Azure. Ce service protège les données au repos des comptes de stockage Azure à l’aide du chiffrement AES 256 bits. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 Contrôle SC-39

#### <a name="process-isolation"></a>Isolement des processus

**SC-39** Le système informatique conserve un domaine d’exécution distinct pour chaque processus d’exécution.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les machines virtuelles déployées par cette solution Blueprint exécutent des systèmes d’exploitation Windows. Windows gère des domaines d’exécution séparés pour chaque processus d’exécution en affectant un espace d’adresse virtuelle privée à chaque processus. |
| **Fournisseur (Microsoft Azure)** | Non applicable |
