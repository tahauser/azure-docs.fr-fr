---
title: "ROYAUME-UNI-OFFICIEL Vue d’ensemble de l’automatisation des applications Web à trois niveaux"
description: "ROYAUME-UNI-OFFICIEL Vue d’ensemble de l’automatisation des applications Web à trois niveaux"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 787f504dea6e98911d6ba4716e88aff322c70c5b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Vue d’ensemble des principes de sécurité du cloud du National Cyber Security Centre


> [!NOTE]
> Ces principes de sécurité sont définis par le NCSC (National Cyber Security Centre), organisation du Royaume-Uni. Pour obtenir des informations sur les procédures de test et des conseils concernant chaque principe de sécurité, reportez-vous à la [documentation NCSC](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles).



## <a name="ncsc-cloud-security-principle-1"></a>Principe de sécurité du cloud du NCSC 1
### <a name="data-in-transit-protection"></a>Protection des données en transit
Les données utilisateur en transit sur les réseaux doivent être correctement protégées contre la falsification et l’espionnage.

Ceci nécessite une combinaison des méthodes suivantes :

- Protection du réseau : empêcher l’attaquant d’intercepter les données
- Chiffrement : empêcher l’attaquant de lire les données


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Ce programme Blueprint configure des ressources pour communiquer à l’aide de protocoles sécurisés uniquement. Le composant WAF du service Application Gateway est configuré pour accepter les services de communication utilisés en externe avec les protocoles HTTPS/TLS et pour communiquer avec le pool principal uniquement avec les protocoles HTTPS/TLS. Les services Bureau à distance sont configurés pour utiliser des connexions sécurisées. Un VPN est utilisé pour sécuriser le trafic web entre AppGateway et Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Azure utilise le protocole standard TLS (Transport Layer Security) 1.2 avec des clés de chiffrement RSA/SHA256 2 048 bits, comme recommandé par le CESG/NCSC, pour chiffrer les communications entre le client et le cloud, et en interne entre les centres de données et les systèmes Azure. Par exemple, quand des administrateurs utilisent le portail Microsoft Azure pour gérer le service pour leur organisation, les données transmises entre le portail et l’appareil de l’administrateur sont envoyées sur un canal TLS chiffré. Lorsqu’un utilisateur de messagerie se connecte à Outlook.com à l’aide d’un navigateur web standard, la connexion HTTPS fournit un canal sécurisé pour la réception et l’envoi d’e-mail.<br /> <br /> Azure offre à ses clients une gamme d’options pour la sécurisation de leurs propres données et de leur propre trafic. Les fonctionnalités de gestion de certificat intégrées à Azure offrent aux administrateurs la souplesse requise pour configurer des certificats et des clés de chiffrement pour les systèmes de gestion, services individuels, sessions SSH (Secure Shell), connexions de réseau privé virtuel (VPN, Virtual Private Network), connexions RDP (Remote Desktop Protocol) et pour d’autres fonctions. <br /><br /> Les développeurs peuvent utiliser les fournisseurs de services de chiffrement intégrés à Microsoft .NET Framework pour accéder aux algorithmes AES (Advanced Encryption Standard) et à la fonctionnalité SHA-2 (Secure Hash Algorithm) afin de traiter des tâches telles que la validation de signatures numériques. Azure Key Vault aide les clients à protéger les secrets et les clés de chiffrement en les stockant dans des modules HSM (Hardware Security Module). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Principe de sécurité du cloud du NCSC 2
### <a name="asset-protection-and-resilience"></a>Protection et résilience des ressources
Les données utilisateur, ainsi que les ressources qui les stockent ou les traitent doivent être protégées contre tout dommage, altération physique, perte ou interception.

Les aspects suivants doivent être pris en compte :

1. Emplacement physique et compétence juridique
2. Sécurité des centres de données
3. Protection des données au repos
4. Nettoyage des données
5. Élimination de l’équipement
6. Disponibilité et résilience physique


 ## <a name="ncsc-cloud-security-principle-21"></a>Principe de sécurité du cloud du NCSC 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>Emplacement physique et compétence juridique
Pour comprendre les conditions juridiques encadrant l’accès à vos données sans votre consentement, vous devez identifier les emplacements auxquels elles sont stockées, traitées et gérées.
Vous devrez également comprendre comment les contrôles de traitement des données au sein du service sont appliqués, vis-à-vis de la législation du Royaume-Uni. Une protection inappropriée des données utilisateur peut entraîner des sanctions légales et réglementaires et nuire à la réputation.


**Responsabilités :** `Customer`

> [!NOTE]
> Les services Azure sont déployés au niveau régional, et les clients peuvent configurer certains services Azure pour stocker les données des clients uniquement dans une région. Microsoft Azure fournit une liste des centres de données disponibles dans le monde pour assurer disponibilité et fiabilité à l’échelle internationale. Tous les centres de données Azure ont été certifiés selon la norme ISO/IEC 27001:2013. La zone géographique du Royaume-Uni se compose de 2 régions : Royaume-Uni Sud et Royaume-Uni Ouest.

|||
|---|---|
| **Client** | Dans le cadre de ce programme Blueprint, l’administrateur est invité à spécifier la région dans laquelle les ressources Azure doivent être déployées. Les régions recommandées pour le déploiement sont Royaume-Uni Sud ou Royaume-Uni Ouest. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Non applicable |


 ## <a name="ncsc-cloud-security-principle-22"></a>Principe de sécurité du cloud du NCSC 2.2
### <a name="datacenter-security"></a>Sécurité des centres de données
Les emplacements utilisés pour fournir des services cloud requièrent une protection physique contre tout accès non autorisé, falsification, vol ou reconfiguration de systèmes. Des protections inadéquates peuvent entraîner la divulgation, la modification ou la perte de données.


**Responsabilités :** `Microsoft Azure`


|||
|---|---|
| **Client** | Les clients n’ont aucun accès physique aux ressources système des centres de données Azure. Les mesures de protection des centres de données sont implémentées et gérées par Microsoft Azure. Ce principe est hérité de Microsoft Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implémente ce principe pour le compte des clients. Microsoft Azure s’exécute dans des installations Microsoft géographiquement distribuées, qui partagent l’espace et les services avec d’autres services en ligne Microsoft. Chaque installation est conçue pour fonctionner 24 h/24, 7 j/7, 365 jours par an et emploie diverses mesures standard pour protéger les opérations contre les pannes de courant, intrusions physiques et pannes de réseau. Ces centres de données respectent les normes du secteur (par exemple, ISO 27001) en matière de sécurité physique et de disponibilité. Ils sont gérés, surveillés et administrés par le personnel d’exploitation de Microsoft. <br /> <br /> Les clients Azure ont l’assurance que des contrôles de sécurité physique sont en place dans tous les centres de données Azure. En effet, Azure détient des certifications ISO/IEC 27001:2013 pour tous les centres de données. La zone géographique du Royaume-Uni se compose de deux régions : Royaume-Uni Sud et Royaume-Uni Ouest. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Principe de sécurité du cloud du NCSC 2.3
### <a name="data-at-rest-protection"></a>Protection des données au repos
Pour garantir que les données sont inaccessibles aux parties non autorisées ayant un accès physique à l’infrastructure, les données utilisateur stockées au sein du service doivent être protégées, et ce quel qu’en soit le support de stockage. En l’absence de mesures appropriées, les données peuvent être divulguées de façon fortuite à la suite de l’abandon, de la perte ou du vol d’un support.


**Responsabilités :** `Shared`

> [!NOTE]
> Toutes les solutions de chiffrement fournies par Microsoft Azure à ses clients s’intègrent facilement à Azure Key Vault, ce qui simplifie la gestion des clés de chiffrement.

|||
|---|---|
| **Client** | La confidentialité et l’intégrité de tout stockage blob déployé par cette solution Blueprint sont protégées par le biais du chiffrement du service de stockage Azure. Ce service protège les données au repos des comptes de stockage Azure à l’aide du chiffrement AES 256 bits. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Azure propose toute une série de fonctionnalités de chiffrement, offrant aux clients une grande souplesse et leur permettant notamment de choisir la solution correspondant le mieux à leurs besoins. Azure Key Vault apporte aux clients une solution simple et rentable pour conserver le contrôle des clés utilisées par les applications et les services cloud afin de chiffrer les données. Azure Disk Encryption permet aux clients de chiffrer les machines virtuelles. Le chiffrement du service de stockage Azure offre la possibilité de chiffrer toutes les données placées dans le compte de stockage d’un client. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Principe de sécurité du cloud du NCSC 2.4
### <a name="data-sanitization"></a>Nettoyage des données
Le processus de provisionnement, migration et déprovisionnement des ressources ne doit pas ouvrir la voie à un accès non autorisé aux données utilisateur.

Un assainissement inapproprié des données peut entraîner les situations suivantes :

- Données utilisateur conservées indéfiniment par le fournisseur de services
- Données utilisateur accessibles à d’autres utilisateurs du service lorsque les ressources sont réutilisées
- Données utilisateur perdues ou divulguées à la suite de l’abandon, de la porte ou du vol d’un support


**Responsabilités :** `Microsoft Azure`


|||
|---|---|
| **Client** | Microsoft Azure offre des garanties contractuelles concernant la suppression permanente des données dans Azure. Ce principe est donc hérité de Microsoft Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure applique le processus d’élimination SP800-88r1 du NIST avec classification des données alignée sur la catégorie modérée FIPS-199. Le NIST soutient l’approche d’effacement sécurisé (via le microprogramme du disque dur) pour les lecteurs qui peuvent la prendre en charge. Microsoft détruit les disques durs qui ne peuvent pas être réinitialisés et rend impossible toute récupération des informations (par exemple, par désintégration, fragmentation, pulvérisation ou incinération). Le mode d’élimination approprié est déterminé par le type de ressource. Les enregistrements de la destruction sont conservés. Tous les services Microsoft Azure utilisent des services de gestion des supports de stockage et d’élimination approuvés. <br />  <br /> À l’expiration ou à la résiliation d’un abonnement à un service, le client peut contacter Microsoft et indiquer ses attentes : <br /><br /> (1) Désactiver le compte du client, puis supprimer ses données ; ou <br /> (2) Conserver les données stockées dans le service en ligne dans un compte aux fonctions limitées pendant au moins 90 jours après l’expiration ou la résiliation de l’abonnement du client (la « période de rétention »), de sorte que le client puisse extraire les données. À l’expiration de la période de rétention, Microsoft désactive le compte du client et supprime toutes les données. Les copies de sauvegarde et mises en cache sont supprimées dans les 30 jours suivant la fin de la période de rétention. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Principe de sécurité du cloud du NCSC 2.5
### <a name="equipment-disposal"></a>Élimination de l’équipement
Une fois que l’équipement utilisé pour fournir un service atteint la fin de son cycle de vie, il doit être éliminé de manière à ne pas compromettre la sécurité du service ni les données utilisateur stockées dans le service.


**Responsabilités :** `Microsoft Azure`


|||
|---|---|
| **Client** | Les clients n’ont aucun accès physique aux ressources système des centres de données Azure. Les procédures d’élimination des équipements sont implémentées et gérées par Microsoft Azure. Ce principe est hérité de Microsoft Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implémente ce principe pour le compte des clients. À la fin du cycle de vie d’un système, le personnel d’exploitation de Microsoft applique des procédures de traitement des données et processus d’élimination de matériel rigoureux afin de s’assurer qu’aucun matériel susceptible de contenir des données client n’est accessible à des tiers non approuvés. Microsoft Azure applique le processus d’élimination SP800-88r1 du NIST avec classification des données alignée sur la catégorie modérée FIPS-199. Le NIST soutient l’approche d’effacement sécurisé (via le microprogramme du disque dur) pour les lecteurs qui peuvent la prendre en charge. Microsoft détruit les disques durs qui ne peuvent pas être réinitialisés et rend impossible toute récupération des informations (par exemple, par désintégration, fragmentation, pulvérisation ou incinération). Le mode d’élimination approprié est déterminé par le type de ressource. Les enregistrements de la destruction sont conservés. Tous les services Microsoft Azure utilisent des services de gestion des supports de stockage et d’élimination approuvés. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Principe de sécurité du cloud du NCSC 2.6
### <a name="physical-resilience-and-availability"></a>Disponibilité et résilience physique
Les services affichent différents niveaux de résilience, affectant leur capacité à fonctionner normalement en cas de panne, d’incident ou d’attaque. Un service sans garantie de disponibilité peut devenir indisponible sur des périodes potentiellement prolongées, avec un impact indéterminé sur votre activité.


**Responsabilités :** `Shared`

> [!NOTE]
> Si le client configure Microsoft Azure correctement en activant Azure Site Recovery et en prévoyant un stockage de données alternatif dans un autre centre de données situé géographiquement, Microsoft Azure peut assurer le fonctionnement continu des ressources déployées par le client.

|||
|---|---|
| **Client** | Il incombe au client d’établir un site de stockage alternatif. L’énoncé des travaux d’implémentation relatif aux contrôles du client doit couvrir sa capacité à poursuivre son activité en cas d’incident. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure possède des centres de données approuvés par le NCSC (Royaume-Uni) à différents emplacements géographiques (Royaume-Uni Sud et Royaume-Uni Ouest), assurant ainsi résilience et disponibilité. Il incombe au client de réserver de la capacité dans une autre région à l’aide du service Azure Site Recovery. Une fois qu’il a configuré Azure Site Recovery, Azure démarre et arrête les services du client moyennant une transition transparente vers le site de traitement alternatif. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Principe de sécurité du cloud du NCSC 3
### <a name="separation-between-users"></a>Séparation entre les utilisateurs
Un utilisateur du service malveillant ou compromis ne doit pas être en mesure d’affecter le service ou les données d’un autre utilisateur.

Les facteurs affectant la séparation des utilisateurs sont notamment :

- l’emplacement d’implémentation des contrôles de séparation, fortement influencé par le modèle de service (IaaS, PaaS, SaaS),
- les utilisateurs avec lesquels vous partagez le service, ce qui dépend du modèle de déploiement (par exemple, cloud public, cloud privé ou communauté du cloud computing),
- le niveau d’assurance offert par l’implémentation des contrôles de séparation.

> [!NOTE]
> Dans un service IaaS, vous devez prendre en compte la séparation fournie par les composants de calcul, de stockage et de réseau. En outre, les services SaaS et PaaS basés sur IaaS peuvent hériter de certaines propriétés de séparation de l’infrastructure IaaS sous-jacente.

**Responsabilités :** `Microsoft Azure`


|||
|---|---|
| **Client** | Microsoft Azure assure l’isolation de chaque utilisateur afin d’empêcher tout utilisateur malveillant ou compromis d’affecter le service ou les données d’un autre utilisateur. Ce principe est donc hérité de Microsoft Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Dans la mesure où les serveurs cloud des clients sont virtuels, le paradigme de séparation physique ne s’applique plus. Microsoft Azure a été conçu pour simplifier l’identification et la neutralisation des risques inhérents à un environnement multi-locataire. Le stockage et le traitement des données sont logiquement séparés entre les utilisateurs d’Azure à l’aide d’Active Directory et d’une fonctionnalité développée spécifiquement pour les services multi-locataires, qui vise à garantir que les données utilisateur stockées dans des centres de données Azure partagés ne sont pas accessibles à une autre organisation. <br /> <br /> Pour toute architecture cloud partagée, il est fondamental d’isoler chaque utilisateur afin d’empêcher tout utilisateur malveillant ou compromis d’affecter le service ou les données d’un autre utilisateur. <br /> <br /> Pour plus d’informations sur la séparation des locataires par Microsoft, consultez la description complète dans le document [Azure Security and Compliance Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Sécurité et conformité Azure Blueprint - Principes de sécurité du cloud du NCSC - Matrice des responsabilités du client). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Principe de sécurité du cloud du NCSC 4
### <a name="governance-framework"></a>Cadre de gouvernance
Le fournisseur de services doit disposer d’un cadre de gouvernance de la sécurité qui coordonne et oriente sa gestion du service et des informations correspondantes. Tous les contrôles techniques déployés en dehors de ce cadre seront profondément fragilisés.
La mise en place d’un cadre de gouvernance efficace garantit que les contrôles de procédure, de personnel, physiques et techniques continuent de fonctionner tout au long de la durée de vie d’un service. Ceci doit également permettre de réagir aux modifications du service, aux développements technologiques et à l’apparition de nouvelles menaces.


**Responsabilités :** `Microsoft Azure`


|||
|---|---|
| **Client** | Microsoft Azure applique un cadre de gouvernance de la sécurité documenté pour les services Azure. Ce principe est donc hérité de Microsoft Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Le cadre de conformité de Microsoft intègre une méthodologie standard permettant de définir les domaines de conformité, de déterminer les objectifs s’appliquant à une équipe ou une ressource donnée et de constater si les objectifs de contrôle de domaine sont atteints de façon suffisamment précise dans le cadre d’un ensemble donné de normes industrielles, réglementations ou exigences professionnelles. Le cadre met les contrôles en correspondance avec plusieurs normes réglementaires. Microsoft peut alors concevoir et créer des services en utilisant un ensemble de contrôles commun et ainsi rationaliser la conformité pour un éventail de réglementations, tout en tenant compte de leur évolution future. <br /> <br /> Les processus de conformité de Microsoft aident également les clients à se mettre en conformité à l’égard de différents services et à répondre efficacement à leurs besoins. En s’appuyant sur une technologie d’amélioration de la sécurité et sur des processus de conformité efficaces, Microsoft applique et développe un ensemble complet de certifications tierces. Ces certifications aident les clients à démontrer à leurs propres clients, aux auditeurs et aux autorités de réglementation qu’ils sont prêts à se mettre en conformité. <br /> <br />  Azure respecte un large éventail de normes de conformité internationales et régionales spécifiques à l’industrie, notamment ISO 27001, FedRAMP, SOC 1 et SOC 2. La conformité aux contrôles de sécurité stricts contenus dans ces normes est vérifiée par des audits externes rigoureux qui démontrent que les services Azure appliquent et respectent des normes, certifications, attestations et autorisations de premier ordre. <br /> <br /> Azure est conçu dans le cadre d’une stratégie de conformité qui aide les clients à atteindre leurs objectifs stratégiques et à respecter les normes et réglementations du secteur. Pour assurer l’obtention des certificats et attestations, le cadre de conformité de sécurité inclut des phases de test et d’audit, des analyses de la sécurité, les meilleures pratiques de gestion des risques et l’analyse benchmark de la sécurité. <br /> <br /> Pour plus d’informations sur le respect des cadres de conformité par Microsoft, consultez la description complète dans le document [Azure Security and Compliance Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Sécurité et conformité Azure Blueprint - Principes de sécurité du cloud du NCSC - Matrice des responsabilités du client).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Principe de sécurité du cloud du NCSC 5
### <a name="operational-security"></a>Sécurité opérationnelle
Le service doit être exploité et géré en toute sécurité de façon à empêcher, détecter ou prévenir les attaques. Une sécurité opérationnelle efficace ne doit pas nécessairement impliquer des processus complexes, bureaucratiques longs ou coûteux.

Quatre éléments doivent être pris en compte :

- Gestion de la configuration et des changements : vous devez vous assurer que les modifications apportées au système ont été correctement testées et autorisées. Les modifications ne doivent pas altérer les propriétés de sécurité de façon inattendue.
- Gestion des vulnérabilités : vous devez identifier et atténuer les problèmes de sécurité touchant des composants constitutifs.
- Surveillance à des fins de protection : vous devez mettre en place des mesures pour détecter les attaques et activités non autorisées sur le service.
- Gestion des incidents : assurez-vous de pouvoir répondre aux incidents et rétablir un service sécurisé et disponible.




 ## <a name="ncsc-cloud-security-principle-51"></a>Principe de sécurité du cloud du NCSC 5.1
### <a name="configuration-and-change-management"></a>Gestion de la configuration et des changements
Vous devez avoir une idée précise des ressources qui composent le service, de leurs configurations et de leurs dépendances.
Les changements qui pourraient affecter la sécurité du service doivent être identifiés et gérés. Les changements non autorisés doivent être détectés.
Lorsque les changements ne sont pas gérés efficacement, des failles de sécurité peuvent s’introduire dans un service de façon fortuite. Dans ce cas, même si la vulnérabilité est connue, elle ne peut être correctement atténuée.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Les modèles Azure Resource Manager et les ressources associées composant ce programme Blueprint représentent, pour l’architecture déployée, une ligne de base de type « configuration en tant que code ». La solution est fournie par le biais de GitHub, qui peut être utilisé pour le contrôle de configuration. <br /> <br /> Les privilèges de compte Azure Active Directory sont implémentés à l’aide du contrôle d’accès en fonction du rôle, via l’assignation de rôles aux utilisateurs. Ceci permet de contrôler de façon stricte les utilisateurs pouvant afficher et contrôler les ressources déployées. Les privilèges de compte Active Directory sont implémentés à l’aide du contrôle d’accès en fonction du rôle, via l’assignation des utilisateurs à des groupes de sécurité. Ces groupes de sécurité contrôlent les actions que les utilisateurs peuvent effectuer concernant la configuration du système d’exploitation. Le client peut développer ces schémas basés sur le rôle pour répondre aux besoins de sa mission. <br /> <br /> Pour respecter ce principe, une configuration supplémentaire est requise de la part du client, en vue d’une utilisation en production. Par conséquent, ces configurations devront s’inscrire dans le processus de gestion des changements du client. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure examine et met à jour les paramètres de configuration et les configurations de référence du matériel, des logiciels et des périphériques réseau chaque année. Les modifications sont développées, testées et approuvées avant le passage d’un environnement de développement et/ou de test à un environnement de production. <br /> <br />Microsoft Azure applique les configurations de référence à l’aide du processus de modification et mise en production pour les composants logiciels de Microsoft Azure (système d’exploitation, Fabric, RDFE et XStore, par exemple) et du processus de configuration d’amorçage pour les composants matériels et de périphérique réseau accédant à l’environnement de production Microsoft Azure, comme indiqué ci-dessous. <br /> <br /> Les configurations de référence requises pour les services Azure sont examinées par l’équipe de sécurité et de conformité Azure et par les équipes des services dans le cadre de tests avant le déploiement de leur service de production. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Principe de sécurité du cloud du NCSC 5.2
### <a name="vulnerability-management"></a>Gestion des vulnérabilités
Les fournisseurs de services doivent disposer d’un processus de gestion permettant d’identifier, de trier et d’atténuer les vulnérabilités. Les services qui n’en bénéficient pas deviendront rapidement vulnérables aux attaques utilisant des outils et méthodes publiquement connus.


**Responsabilités :** `Customer`


|||
|---|---|
| **Client** | Il incombe au client d’analyser les vulnérabilités affectant les ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels). L’énoncé des travaux d’implémentation du client doit couvrir les outils utilisés pour effectuer les analyses de vulnérabilité. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | La gestion des mises à jour de sécurité aide à protéger les systèmes des vulnérabilités connues. Azure utilise des systèmes de déploiement intégrés pour gérer la distribution et l’installation des mises à jour de sécurité destinées aux logiciels Microsoft. Azure peut également s’appuyer sur les ressources du Centre de réponse aux problèmes de sécurité Microsoft (MSRC, Microsoft Security Response Center), qui identifie, surveille, traite et résout les incidents de sécurité et vulnérabilités du cloud 24h/24, 365 jours par an. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Principe de sécurité du cloud du NCSC 5.3
### <a name="protective-monitoring"></a>Surveillance à des fins de protection
Un service qui ne surveille pas efficacement les attaques, utilisations abusives et défaillances sera peu susceptible de détecter les attaques (réussies ou non). Par conséquent, il sera dans l’incapacité de répondre rapidement aux violations potentielles de vos environnements et données.


**Responsabilités :** `Customer`


|||
|---|---|
| **Client** | Il incombe au client de surveiller les ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels). L’énoncé des travaux d’implémentation relatif aux contrôles du client doit couvrir les mécanismes permettant de surveiller, détecter et traiter les attaques, utilisations abusives et défaillances. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Le service de sécurité Microsoft Azure a défini des exigences en termes de surveillance active. Les équipes de service configurent des outils de surveillance active conformes à ces exigences. Les outils de surveillance active incluent Monitoring Agent et System Center Operations Manager (SCOM), qui sont configurés pour fournir des alertes en temps réel au personnel de sécurité de Microsoft Azure dans les situations qui nécessitent une action immédiate. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Principe de sécurité du cloud du NCSC 5.4
### <a name="incident-management"></a>Gestion des incidents
À moins que des processus de gestion des incidents soigneusement planifiés ne soient mis en place, il est probable que les décisions prises en cas d’incident soient inappropriées, ce qui peut amplifier l’impact global sur les utilisateurs.
Ces processus ne doivent pas nécessairement être complexes ou nécessiter de nombreuses descriptions. Cependant, une bonne gestion des incidents minimise l’impact des problèmes de sécurité, de fiabilité et d’environnement affectant un service sur les utilisateurs.


**Responsabilités :** `Shared`

> [!NOTE]
> Dans les situations où des incidents de sécurité touchant le client peuvent affecter l’état de sécurité de Microsoft Azure, il incombe au client d’en informer Microsoft Azure.

|||
|---|---|
| **Client** | Il incombe au client d’établir un processus de gestion des incidents pour les ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels). L’énoncé des travaux d’implémentation du client doit couvrir le signalement des incidents et alertes, la capacité à répondre aux incidents en temps voulu et le transfert des informations au service PGA et autres organisations HMG le cas échéant. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft a implémenté un processus de gestion des incidents de sécurité pour permettre une réponse coordonnée aux incidents potentiels. <br /> <br /> Si Microsoft prend connaissance de tout accès non autorisé à toute donnée client stockée sur ses équipements ou dans ses installations, ou d’un accès non autorisé à ces équipements ou installations entraînant une perte, une divulgation ou une altération des données client, Microsoft procédera aux actions suivantes, comme il l’a déclaré : <br /> <br />   - Informer rapidement le client de l’incident de sécurité. <br /> - Examiner l’incident de sécurité dans les plus brefs délais et fournir au client des informations détaillées sur l’incident. <br /> - Appliquer des procédures raisonnables permettant d’atténuer rapidement les effets de l’incident de sécurité et de réduire les dommages encourus.  <br />  <br /> Un cadre de gestion des incidents impliquant la définition de rôles et l’allocation de responsabilités a été défini. L’équipe de gestion des incidents de sécurité Windows Azure (WASIM, Windows Azure Security Incident Management) est chargée de la gestion des incidents de sécurité, notamment du processus d’escalade. Elle doit également garantir l’implication d’équipes de spécialistes le cas échéant. Les responsables des opérations Azure sont chargés de superviser l’examen et la résolution des incidents de sécurité et de confidentialité avec l’aide d’autres fonctions. <br /> <br /> Pour plus d’informations sur les processus de réponse aux incidents de Microsoft, consultez la description complète dans le document [Azure Security and Compliance Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Sécurité et conformité Azure Blueprint - Principes de sécurité du cloud du NCSC - Matrice des responsabilités du client). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Principe de sécurité du cloud du NCSC 6
### <a name="personnel-security"></a>Sécurité du personnel
Lorsque le personnel du fournisseur de services a accès à vos données et systèmes, vous devez pouvoir leur accorder une confiance extrême. Une sélection rigoureuse, accompagnée d’une formation adéquate, réduit le risque de compromission accidentelle ou malveillante par le personnel du fournisseur de services.
Le fournisseur de services doit sélectionner attentivement le personnel et lui faire suivre des formations sur la sécurité. Le personnel endossant ces rôles doit comprendre ses responsabilités. Les fournisseurs doivent indiquer clairement la façon dont ils sélectionnent et gèrent le personnel bénéficiant de rôles privilégiés.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Le client est chargé de sélectionner les individus et de faire suivre des formations régulières sur la sécurité au personnel ayant accès aux ressources déployées par le client. L’énoncé des travaux d’implémentation du client doit couvrir les critères de sélection relatifs aux rôles et la fréquence des formations sur la sécurité. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Les membres du personnel Microsoft Azure qui exploitent les services Azure et assurent le service clientèle (ou les sous-traitants de Microsoft apportant leur assistance pour les opérations de plateforme, le dépannage et le support technique) sont soumis à un contrôle des antécédents standard de Microsoft (ou à une procédure équivalente) visant à évaluer leur formation, leur parcours professionnel et leur casier judiciaire. Ces contrôles des antécédents répondent globalement aux exigences des normes BPSS/BS7858 du gouvernement du Royaume-Uni. Ils n’incluent pas spécifiquement une vérification d’identité formelle.  <br /> <br /> Microsoft inclut des dispositions de confidentialité dans les contrats signés avec ses employés et sous-traitants. Tous les employés et sous-traitants de Microsoft concernés participent à un programme de formation sur la sécurité parrainé par Microsoft Azure, informant le personnel de ses responsabilités en matière de sécurité des informations. <br /> <br /> Le personnel et les sous-traitants des services Microsoft Azure suspectés d’atteinte à la sécurité et/ou de violation de la stratégie de sécurité des informations font l’objet d’une enquête et d’une action disciplinaire appropriée pouvant impliquer une résiliation du contrat. Si les circonstances le justifient, Microsoft peut porter l’affaire devant les autorités policières pour engager des poursuites. <br /> <br /> Pour compléter ce système de contrôle des antécédents et de formation sur la sécurité, Microsoft déploie des combinaisons de contrôles préventifs, défensifs et réactifs favorisant la protection contre les activités de développement et/ou d’administration non autorisées, comprenant les mécanismes suivants : <br />  <br /> - Contrôles d’accès stricts aux données sensibles, impliquant notamment une authentification par carte à puce à deux facteurs pour l’exécution d’opérations sensibles <br /> - Combinaisons de contrôles améliorant la détection indépendante des activités malveillantes <br /> - Niveaux multiples de surveillance, d’enregistrement et de génération de rapports |


 ## <a name="ncsc-cloud-security-principle-7"></a>Principe de sécurité du cloud du NCSC 7
### <a name="secure-development"></a>Développement sécurisé
Les services doivent être conçus et développés de façon à identifier et atténuer les menaces pour leur sécurité. Dans le cas contraire, ils peuvent être vulnérables aux problèmes de sécurité susceptibles de compromettre vos données, de provoquer une perte de service ou d’ouvrir la voie à d’autres activités malveillantes.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Les machines virtuelles déployées par ce programme Blueprint exécutent des systèmes d’exploitation Windows. Windows assure la validation d’intégrité des fichiers en temps réel, la protection et la récupération des fichiers du système noyau installés dans le cadre de Windows ou de mises à jour autorisées du système Windows par le biais de la fonctionnalité Protection des ressources Windows, qui permet une vérification de l’intégrité en temps réel. <br /> <br /> Windows a mis en place des protections pour empêcher l’exécution de code dans des emplacements mémoire restreints : NX (No Execute), randomisation du format d’espace d’adresse (ASLR, Address Space Layout Randomization) et PED (Prévention de l’exécution des données). <br /> <br /> Ce programme Blueprint déploie des protections anti-programme malveillant basées sur l’hôte pour toutes les machines virtuelles Windows implémentées à l’aide de Microsoft Windows Defender. Windows Defender est configuré pour mettre automatiquement à jour le moteur anti-programme malveillant et les signatures de protection lors de la mise à disposition de la version. <br /> <br /> Pour respecter ce principe, une configuration supplémentaire est requise de la part du client, en vue d’une utilisation en production. Par conséquent, ces configurations devront s’inscrire dans le processus de développement sécurisé du client. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Le SDL (Security Development Lifecycle) Microsoft offre un processus de modélisation des menaces efficace pour identifier les menaces et les vulnérabilités affectant les logiciels et les services. La modélisation des menaces est un exercice d’équipe incluant le responsable des opérations, les chefs de programme ou de projet, les développeurs et les testeurs. Il s’agit d’une tâche d’analyse de sécurité clé pour la conception de solutions. Les membres de l’équipe utilisent l’outil de modélisation des menaces SDL pour modéliser tous les services et projets lors de leur génération et lors de leur mise à jour avec de nouvelles fonctionnalités. Les modèles de menace couvrent tout le code exposé sur la surface d’attaque et tout le code écrit par un tiers ou fourni sous licence par un tiers. Par ailleurs, ils prennent en compte toutes les limitations d’approbation. La méthode basée sur l’usurpation, la falsification, la répudiation, la divulgation d’informations, le déni de service et l’élévation de privilège (STRIDE, Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege) est utilisée pour simplifier l’identification et la résolution des menaces de sécurité à un stade précoce du processus de conception, avant qu’elles ne puissent affecter les clients. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Principe de sécurité du cloud du NCSC 8
### <a name="supply-chain-security"></a>Sécurité de la chaîne d’approvisionnement
Le fournisseur de services doit veiller à ce que sa chaîne d’approvisionnement prenne en charge de façon satisfaisante tous les principes de sécurité revendiqués par le service.
Les services cloud reposent souvent sur des produits et services tiers. Par conséquent, si ce principe n’est pas implémenté, la compromission de la chaîne d’approvisionnement peut fragiliser la sécurité du service et affecter l’implémentation d’autres principes de sécurité.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Il incombe au client de fournir une documentation relative à une chaîne d’approvisionnement sécurisée pour tout logiciel et système d’exploitation acquis auprès d’un tiers utilisé dans son abonnement Azure. L’énoncé des travaux d’implémentation du client doit couvrir toute exception concernant l’application des processus identifiés par cette documentation relative à la chaîne d’approvisionnement. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Le groupe en charge de la chaîne d’approvisionnement cloud de Microsoft (MCSC, Microsoft Cloud Supply Chain) comprend six équipes uniques contribuant à protéger Azure des menaces envers la chaîne d’approvisionnement.  <br />  <br /> - Approvisionnement <br /> - Opérations du client <br /> - Qualité du déploiement <br /> - Gestion de la relation fournisseur <br /> - Éléments de rechange <br />  <br /> Pour plus d’informations sur le groupe MCSC de Microsoft, consultez la description complète dans le document [Azure Security and Compliance Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Sécurité et conformité Azure Blueprint - Principes de sécurité du cloud du NCSC - Matrice des responsabilités du client). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Principe de sécurité du cloud du NCSC 9
### <a name="secure-user-management"></a>Gestion sécurisée des utilisateurs
Votre fournisseur doit mettre à votre disposition les outils vous permettant de gérer en toute sécurité l’utilisation de son service. Les procédures et interfaces de gestion représentent une part essentielle de la barrière de sécurité, empêchant tout accès non autorisé et toute modification de vos ressources, applications et données.

Les aspects suivants doivent être pris en compte :

- Authentification des utilisateurs auprès des interfaces de gestion et canaux de support
- Séparation et contrôle d’accès au sein des interfaces de gestion



 ## <a name="ncsc-cloud-security-principle-91"></a>Principe de sécurité du cloud du NCSC 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Authentification des utilisateurs auprès des interfaces de gestion et au sein des canaux de support
Afin de maintenir un service sécurisé, les utilisateurs doivent être correctement authentifiés avant d’être autorisés à effectuer des activités de gestion, signaler des erreurs ou demander des modifications du service.
Ces activités peuvent être effectuées via un portail web de gestion des services ou via d’autres canaux, tels que le téléphone ou l’e-mail. Elles sont susceptibles d’inclure des fonctions comme l’approvisionnement de nouveaux éléments de service, la gestion des comptes d’utilisateur et la gestion des données utilisateur.
Les fournisseurs de services doivent s’assurer que toutes les demandes de gestion pouvant avoir un impact sur la sécurité sont effectuées sur des canaux sécurisés et authentifiés. Si les utilisateurs ne sont pas soumis à une authentification forte, un imposteur peut effectuer avec succès des actions privilégiées, fragilisant la sécurité du service ou des données.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Lorsque des administrateurs accèdent au portail Microsoft Azure pour gérer les ressources Azure pour leur organisation, les données transmises entre le portail et l’appareil de l’administrateur sont envoyées sur un canal TLS (Transport Layer Security) chiffré à l’aide de clés de chiffrement RSA/SHA256 2 048 bits, comme recommandé par le CESG/NCSC.  <br /> <br /> Ce programme Blueprint emploie l’authentification Windows, le Bureau à distance et BitLocker. Ces composants peuvent être configurés d’après les modules de chiffrement FIPS 140 validés. <br /> <br /> Ce programme Blueprint utilise des autorisations d’accès logique à l’aide du contrôle d’accès en fonction du rôle appliqué par Azure Active Directory en assignant des rôles aux utilisateurs, par Active Directory en assignant les utilisateurs à des groupes de sécurité et par les contrôles de niveau système d’exploitation Windows. Les rôles Active Directory Azure assignés aux utilisateurs ou groupes permettent de contrôler l’accès logique aux ressources dans Azure au niveau de la ressource, du groupe ou de l’abonnement. Les groupes de sécurité Active Directory permettent de contrôler l’accès logique aux fonctions et ressources de niveau système d’exploitation. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Les clients administrent leurs ressources Azure via le Portail Azure, qui donne accès à l’ensemble des machines virtuelles, bases de données, services cloud et autres ressources configurées pour le compte du client. L’accès web au Portail Azure est sécurisé par des connexions TLS 1.2 standard utilisant des clés de chiffrement RSA/SHA256 2 048 bits, comme recommandé par le CESG/NCSC. Les contrôles d’accès en fonction du rôle permettent aux clients d’accorder à des utilisateurs et groupes spécifiques un accès limité aux ressources de gestion Azure. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Principe de sécurité du cloud du NCSC 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>Séparation et contrôle d’accès au sein des interfaces de gestion
De nombreux services cloud sont gérés par le biais d’applications web ou d’API. Ces interfaces représentent un élément essentiel de la sécurité du service. Si les utilisateurs ne sont pas correctement séparés au sein des interfaces de gestion, un utilisateur unique peut être en mesure d’affecter le service ou de modifier les données d’un autre utilisateur.
Vos comptes administratifs privilégiés ont probablement accès à de gros volumes de données. En limitant les autorisations des utilisateurs individuels afin de leur donner accès uniquement aux données nécessaires, on peut limiter les dommages causés par des utilisateurs malveillants, des informations d’identification compromises ou des appareils compromis.
Le contrôle d’accès en fonction du rôle offre un mécanisme permettant de mettre cette méthode en place. Il peut donc représenter une fonctionnalité essentielle pour les utilisateurs gérant des déploiements plus importants.
L’exposition des interfaces de gestion à des réseaux moins accessibles (par exemple, à une communauté plutôt qu’à des réseaux publics) complique la tâche des attaquants qui tentent d’y accéder et de les attaquer. En effet, ils doivent d’abord accéder à l’un de ces réseaux. Pour obtenir des conseils sur l’évaluation des risques liés à l’exposition des interfaces à différents types de réseaux, consultez le principe 11.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Ce programme Blueprint déploie un service Application Gateway et un équilibreur de charge, et configure des règles de groupe de sécurité réseau pour contrôler les commutations sur les limites externes et entre les sous-réseaux internes. Les fonctionnalités utilisateur sont séparées des fonctionnalités de gestion du système via l’application de contrôles d’accès logique et l’architecture du système. Les interfaces dédiées aux fonctionnalités de gestion du système sont distinctes des interfaces utilisateur. La connectivité de gestion s’effectue par le biais d’un hôte bastion sécurisé (jumpbox) situé dans un sous-réseau de gestion avec des règles de groupe de sécurité réseau pour limiter l’accès aux ressources de production de façon appropriée. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Comme indiqué dans la section sur la séparation entre les utilisateurs, le concept de séparation est intégré au cœur d’Azure. Azure Active Directory (Azure AD ou AAD) peut être utilisé pour fournir à chaque utilisateur qui s’authentifie sur le Portail Azure un accès limité aux ressources qu’ils sont autorisés à voir et gérer. Par conséquent, les différents comptes clients sont rigoureusement séparés les uns des autres lorsqu’ils sont gérés par le biais du Portail Azure commun. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Principe de sécurité du cloud du NCSC 10
### <a name="identity-and-authentication"></a>Identité et authentification
Tous les accès aux interfaces de service doivent être limités aux utilisateurs authentifiés et autorisés.
Une authentification faible auprès de ces interfaces peut ouvrir la voie à des accès non autorisés à vos systèmes, avec différentes conséquences : vol ou modification de vos données, modification de votre service ou déni de service.
Il est essentiel que l’authentification s’effectue sur des canaux sécurisés. L’e-mail, le protocole HTTP et le téléphone sont vulnérables aux interceptions et piratages psychologiques.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Ce programme Blueprint utilise Active Directory pour la gestion des comptes. L’accès aux ressources déployées par ce programme Blueprint est protégé contre les attaques par relecture grâce à la fonctionnalité Kerberos intégrée d’Azure Active Directory, d’Active Directory et du système d’exploitation Windows. Dans l’authentification Kerberos, l’authentificateur envoyé par le client contient des données supplémentaires, comme une liste d’adresses IP chiffrées, les horodateurs client et la durée de vie de ticket. Si un paquet est relu, l’horodatage est vérifié. Si l’horodatage est antérieur à ou identique à celui d’un authentificateur précédent, le paquet est rejeté. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Azure fournit des services permettant d’assurer le suivi des identités et d’intégrer celles-ci aux magasins d’identités qui sont peut-être déjà utilisés. Azure AD est un service de gestion d’identité et d’accès complet pour le cloud, qui permet de sécuriser l’accès aux données dans les applications cloud ou locales. Azure AD simplifie également la gestion des utilisateurs et des groupes en associant services d’annuaire essentiels, gouvernance avancée des identités, sécurité et gestion des accès aux applications. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Principe de sécurité du cloud du NCSC 11
### <a name="external-interface-protection"></a>Protection des interfaces externes
Toutes les interfaces externes ou moins fiables du service doivent être identifiées et protégées de façon appropriée.
Si certaines des interfaces exposées sont privées (par exemple, les interfaces de gestion), l’impact d’une compromission peut être plus important.
Vous pouvez utiliser différents modèles pour vous connecter à des services cloud exposant vos systèmes d’entreprise à différents niveaux de risque.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Ce programme Blueprint déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire aux fonctionnalités système et de gestion (par exemple, le trafic externe ne peut pas accéder aux sous-réseaux de base de données, de gestion ou Active Directory).  <br /> <br /> Un service Application Gateway est déployé pour gérer les connexions externes à une application web déployée par le client. Les connexions externes pour l’accès à la gestion sont limitées à un hôte bastion (jumpbox) déployé dans un sous-réseau de gestion, et des règles de sécurité réseau sont appliquées pour restreindre les connexions externes aux adresses IP autorisées. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Microsoft utilise une méthode appelée « Red Teaming » pour améliorer les contrôles et processus de sécurité Azure via des tests de pénétration réguliers. L’équipe Red Team est un groupe employé à plein-temps chez Microsoft. Son rôle consiste à exécuter des attaques ciblées et persistantes sur l’infrastructure, les plateformes et les applications Microsoft sans toucher aux applications et données des clients finaux. <br /> <br /> Pour plus d’informations sur la méthode Red Teaming de Microsoft et pour obtenir une description des efforts mis en œuvre dans le cadre du Blue Teaming, consultez la description complète dans le document [Azure Security and Compliance Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Sécurité et conformité Azure Blueprint - Principes de sécurité du cloud du NCSC - Matrice des responsabilités du client).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Principe de sécurité du cloud du NCSC 12
### <a name="secure-service-administration"></a>Administration sécurisée des services
Les systèmes utilisés pour l’administration d’un service cloud bénéficieront d’un accès hautement privilégié à ce service. Toute compromission de ces systèmes aurait des impacts significatifs : possibilité de contourner les contrôles de sécurité, de voler ou manipuler de grands volumes de données, etc.
La conception, l’implémentation et la gestion des systèmes d’administration doivent s’appuyer sur les bonnes pratiques d’entreprise, tout en affichant leur valeur élevée auprès des attaquants.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Il incombe au client d’assurer la mise en place d’une station de travail sécurisée pour l’administration de son abonnement Azure et des ressources qu’il a lui-même déployées (notamment les applications, systèmes d’exploitation, bases de données et logiciels). L’énoncé des travaux d’implémentation du client doit couvrir les mécanismes utilisés pour limiter le risque d’exploitation des ressources déployées par le client. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Le personnel d’exploitation Microsoft Azure est tenu d’utiliser des stations de travail d’administration sécurisées (SAW, secure admin workstation), également appelées stations de travail avec accès privilégié (PAW, privileged access workstation). L’approche SAW est une extension de la pratique recommandée bien établie qui consiste à utiliser des comptes d’utilisateur et d’administrateur distincts pour le personnel d’administration. Cette pratique utilise un compte d’administration affecté à titre individuel et totalement distinct du compte standard de l’utilisateur. SAW s’appuie sur cette pratique de séparation des comptes en fournissant une station de travail digne de confiance pour ces comptes sensibles. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Principe de sécurité du cloud du NCSC 13
### <a name="audit-information-for-users"></a>Informations d’audit pour les utilisateurs
Vous devez obtenir les enregistrements d’audit nécessaires pour surveiller l’accès à votre service et aux données qu’il contient. Le type d’informations d’audit à votre disposition aura un impact direct sur votre capacité à détecter une activité inappropriée ou malveillante et à y répondre dans des délais raisonnables.


**Responsabilités :** `Shared`


|||
|---|---|
| **Client** | Les événements audités par ce programme Blueprint incluent ceux audités par les journaux d’activité Azure pour les ressources déployées, les journaux de niveau système d’exploitation et les journaux Active Directory. Ces journaux des événements incluent des informations suffisantes pour déterminer à quel moment se produisent les événements, la source de l’événement et les conséquences de l’événement. Ils offrent également des informations détaillées utiles pour l’examen des incidents de sécurité. Les clients peuvent sélectionner d’autres événements à auditer selon les besoins de leur mission. Des journaux d’audit sont disponibles dans le portail Azure pour toutes les ressources Azure. |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Azure Log Analytics collecte les enregistrements des événements qui se produisent au sein des systèmes et réseaux d’une organisation dès leur survenue, avant qu’un tiers ne puisse les falsifier. De plus, il permet différents types d’analyse en mettant en corrélation les données de plusieurs ordinateurs. Azure offre aux clients la possibilité de générer et de collecter des événements de sécurité à partir des rôles Azure IaaS et PaaS vers le stockage central dans leurs abonnements. Ces événements collectés peuvent être exportés vers les systèmes SIEM (Security Information and Event Management) locaux pour la surveillance en cours. Une fois les données transférées vers le stockage, les données de diagnostic peuvent être affichées selon une multitude d’options. <br /> <br /> Les diagnostics intégrés Azure peuvent s’avérer utiles pour le débogage. Pour les applications déployées dans Azure, un ensemble d’événements de sécurité relatifs au système d’exploitation est activé par défaut. Les clients peuvent ajouter, supprimer ou modifier des événements à auditer en personnalisant la stratégie d’audit de système d’exploitation. <br /> <br /> De façon générale, il est relativement simple de commencer à collecter les journaux à l’aide du transfert d’événements Windows (WEF, Windows Event Forwarding) ou d’Azure Diagnostics, plus avancé, lorsque des machines virtuelles Windows sont déployées avec le service IaaS dans Azure. En outre, Azure Diagnostics peut être configuré pour collecter les événements et journaux à partir des instances de rôle PaaS. Quand vous utilisez des machines virtuelles IaaS, un client configure et active simplement les événements de sécurité souhaités, tout comme il permet aux serveurs Windows d’enregistrer les audits dans son centre de données local. Pour les applications web, il est également possible d’activer la journalisation IIS s’il s’agit de l’application et du déploiement primaires dans Azure. Les clients peuvent toujours stocker des données de sécurité dans des comptes de stockage aux emplacements géographiques pris en charge de leur choix pour répondre aux exigences de souveraineté des données. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Principe de sécurité du cloud du NCSC 14
### <a name="secure-use-of-the-service"></a>Utilisation sécurisée du service
La sécurité des services cloud et des données qu’ils stockent peut être compromise si vous ne les utilisez pas correctement. Par conséquent, lorsque vous utilisez le service, vous endossez certaines responsabilités quant à la protection adéquate de vos données.
La portée de votre responsabilité varie selon les modèles de déploiement du service cloud et le scénario dans lequel vous envisagez d’utiliser le service. Des fonctionnalités spécifiques de certains services peuvent également avoir une influence. Par exemple, au-delà des considérations générales couvertes par les principes de sécurité du cloud, la façon dont un réseau de distribution de contenu protège votre clé privée ou dont un fournisseur de service de paiement cloud détecte les transactions frauduleuses représente une considération de sécurité essentielle.
Avec les offres IaaS et PaaS, vous êtes responsable de certains aspects importants de la sécurité de vos données et charges de travail. Par exemple, si vous fournissez une instance de calcul IaaS, vous aurez normalement pour responsabilité d’installer un système d’exploitation moderne, de configurer ce système d’exploitation de façon sécurisée, de déployer chaque application de façon sécurisée et de maintenir cette instance en appliquant des correctifs ou en assurant la maintenance requise.


**Responsabilités :** `Customer`

> [!NOTE]
> Le client peut utiliser Azure Security Center pour bénéficier d’une visibilité et d’un contrôle accrus sur la sécurité des ressources Azure et ainsi anticiper, détecter et traiter plus efficacement les menaces. Azure Security Center fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble des abonnements Azure, aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

|||
|---|---|
| **Client** | Les modèles Azure Resource Manager et les ressources associées qui composent ce programme Blueprint s’appuient sur une approche de la sécurité basée sur une protection fiable. Pour respecter ce principe, une configuration supplémentaire est requise de la part du client, en vue d’une utilisation en production (logiciel de gestion de base de données et déploiement d’applications web, par exemple). |
| **Fournisseur&nbsp;(Microsoft&nbsp;Azure)** | Non applicable |

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.