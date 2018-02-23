---
title: "Solution Blueprint Sécurité et conformité Azure - Automatisation d’applications web FedRAMP - Protection physique et environnementale"
description: "Automatisation d’applications web FedRAMP - Protection physique et environnementale"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="physical-and-environmental-protection-pe"></a>Protection physique et environnementale (PE)

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication 800-53 rév. 4 du NIST.

## <a name="nist-800-53-control-pe-1"></a>NIST 800-53 - Contrôle PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Stratégie et procédures de protection physique et environnementale

**PE-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles de l’organisation] une stratégie de protection physique et environnementale qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie de protection physique et environnementale et des contrôles de protection physique et environnementale associés ; et révise et met à jour la stratégie actuelle de protection physique et environnementale [Affectation : fréquence définie par l’organisation] ; et les procédures de protection physique et environnementale [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures de protection physique et environnementale au niveau entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-pe-2a"></a>NIST 800-53 - Contrôle PE-2.a

#### <a name="physical-access-authorizations"></a>Autorisations d’accès physique

**PE-2.a** L’organisation développe, approuve et gère une liste de personnes autorisées à accéder à l’installation dans laquelle se trouve le système d’information.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. L’accès physique à un centre de données Microsoft doit être approuvé par l’équipe de gestion du centre de données (DCM) à l’aide de l’outil d’accès au centre de données. Les attributions d’accès nécessitent une date de fin après laquelle l’accès est automatiquement révoqué et doit être réapprouvé. De plus, lorsque l’accès n’est plus requis, les agents de sécurité ou l’équipe de gestion du centre de données doivent demander manuellement la révocation de l’accès. |


 ## <a name="nist-800-53-control-pe-2b"></a>NIST 800-53 - Contrôle PE-2.b

#### <a name="physical-access-authorizations"></a>Autorisations d’accès physique

**PE-2.b** L’organisation émet des informations d’identification d’autorisation pour l’accès à l’installation.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. L’outil d’accès au centre de données est la source faisant autorité qui répertorie l’ensemble du personnel autorisé à accéder à un centre de données spécifique. L’outil est relié à des appareils de contrôle d’accès du dispositif de sécurité physique du centre de données, et autorise l’accès en fonction des niveaux d’accès approuvés par l’équipe de gestion du centre de données. Les niveaux d’accès sont assignés dans l’outil au badge de l’utilisateur émis par Microsoft ou à un badge d’accès temporaire attribué dans le centre de données par le superviseur de salle de commande (CRS,Control Room Supervisor). Les niveaux d’accès sont approuvés par l’équipe de gestion du centre de données. En plus des informations d’identification associées aux badges physiques, certaines zones du centre de données requièrent l’inscription des données biométriques de l’utilisateur (géométrie de la main ou empreintes digitales). |


 ## <a name="nist-800-53-control-pe-2c"></a>NIST 800-53 - Contrôle PE-2.c

#### <a name="physical-access-authorizations"></a>Autorisations d’accès physique

**PE-2.c** L’organisation passe en revue la liste d’accès détaillant les accès autorisés de personnes à l’installation [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. En plus de la révocation d’accès décrite dans la partie a, Microsoft Azure révise les listes d’accès autorisés pour les centres de données Azure tous les 90 jours afin de supprimer/mettre à jour l’accès individuel si nécessaire. |


 ## <a name="nist-800-53-control-pe-2d"></a>NIST 800-53 - Contrôle PE-2.d

#### <a name="physical-access-authorizations"></a>Autorisations d’accès physique

**PE-2.d** L’organisation supprime les personnes de la liste d’accès à l’installation lorsque l’accès n’est plus requis.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure révoque automatiquement l’accès lorsque la date de fin de l’attribution d’accès est atteinte. Lorsque l’accès n’est plus requis, les agents de sécurité ou l’équipe de gestion du centre de données doivent demander manuellement la révocation de l’accès dans l’outil d’accès au centre de données. De plus, Microsoft Azure révoque toutes les autorisations d’accès inutiles détectées à la suite de la révision de la liste d’accès décrite dans la partie c. |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800-53 - Contrôle PE-3.a

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**PE-3.a** L’organisation impose des autorisations d’accès physique aux [Affectation : points d’entrée/sortie définis par l’organisation pour l’installation où le système d’information réside] en vérifiant les autorisations d’accès individuelles avant d’accorder l’accès à l’installation ; et en contrôlant des entrées/sorties au niveau de l’installation en utilisant [Sélection (un ou plusieurs) : [Affectation : systèmes/appareils de contrôle d’accès définis par l’organisation] ; gardes].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure impose des autorisations d’accès physique pour tous les points d’accès physique aux centres de données Azure en maintenant en permanence du personnel, des alarmes, une vidéosurveillance, une authentification multifacteur et des dispositifs de portail avec trappe d’homme. |


 ## <a name="nist-800-53-control-pe-3b"></a>NIST 800-53 - Contrôle PE-3.b

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**PE-3.b** L’organisation gère les journaux d’audit d’accès physique pour [Affectation : points d’entrée/sortie définis par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Tous les accès aux installations de centre de données Azure sont journalisés et audités. |


 ## <a name="nist-800-53-control-pe-3c"></a>NIST 800-53 - Contrôle PE-3.c

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**3 c-PE** L’organisation met en place des [Affectation : mesures de sécurité définies par l’organisation] pour contrôler l’accès aux zones à l’intérieur de l’installation officiellement désignées comme accessibles au public.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les centres de données Azure ne contiennent pas de zones désignées comme accessibles au public. |


 ## <a name="nist-800-53-control-pe-3d"></a>NIST 800-53 - Contrôle PE-3.d

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**PE-3.d** L’organisation escorte les visiteurs et surveille leur activité [Affectation : circonstances définies par l’organisation nécessitant l’escorte et la surveillance des visiteurs].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Tous les visiteurs dont l’accès au centre de données est approuvé (voir PE-2) sont désignés comme « Escort Only »(escorte obligatoire) sur leurs badges ou à l’aide d’autres indices visuel (par exemple, des badges de couleur) et doivent obligatoirement rester en compagnie de leur escorte en permanence. Les visiteurs escortés n’ont pas de niveau d’accès spécifié, et peuvent uniquement se déplacer dans les lieux auxquels leur escorte a accès. Les escortes surveillent toutes les activités de leur visiteur à l’intérieur du centre de données. |


 ## <a name="nist-800-53-control-pe-3e"></a>NIST 800-53 - Contrôle PL-3.e

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**PE-3.e** L’organisation sécurise les clés, combinaisons et autres dispositifs d’accès physique.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les clés physiques et badges d’accès temporaire sont sécurisés au sein du centre d’opérations de sécurité (SOC). Des agents de sécurité sont présents en permanence. Les clés sont contrôlées pour du personnel spécifique en vérifiant la concordance entre le badge d’accès et la clé physique. Des inventaires de clés sont effectués à chaque changement d’équipe, et les clés ne peuvent pas quitter le site. |


 ## <a name="nist-800-53-control-pe-3f"></a>NIST 800-53 - Contrôle PL-3.f

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**PE-3.f** L’organisation inventorie les [Affectation : dispositifs d’accès physique définis par l’organisation] chaque [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les dispositifs d’accès physique au sein des centres de données Azure sont inventoriés au moins annuellement. Les clés et badges d’accès temporaire sont inventoriés plusieurs fois par jour pour chaque équipe. Les lecteurs de badges d’accès et autres dispositifs d’accès similaire sont reliés au système de sécurité physique où leur état est représenté en permanence. |


 ## <a name="nist-800-53-control-pe-3g"></a>NIST 800-53 - Contrôle PL-3.g

#### <a name="physical-access-control"></a>Contrôle d’accès physique

**PE-3.g** L’organisation change les combinaisons et les clés [Affectation : fréquence définie par l’organisation] et/ou quand des clés sont égarées, des combinaisons compromises ou des personnes transférées ou arrêtées.  

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les centres de données Microsoft Azure ont des procédures à suivre en cas de perte de badge ou de clé, ainsi que de transfert ou cessation d’activité de personnes. En cas d’arrêt ou de cessation d’activité d’une personne, l’accès de celle-ci est immédiatement révoqué dans le système et son badge d’accès supprimé. |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800-53 - Contrôle PL-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Contrôle d’accès physique | Accès au système d’information

**PE-3 (1)** L’organisation impose des autorisations d’accès physique au système d’information en plus des contrôles d’accès physique à l’installation dans [Affectation : espaces physiques définis par l’organisation contenant un ou plusieurs composants du système d’information].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure restreint davantage l’accès à certains zones dans les centres de données Microsoft qui contiennent des systèmes critiques (par exemple, colocations, environnements critiques, salles de répartiteur téléphonique, etc.) via différents mécanismes de sécurité tels que contrôle d’accès électronique, dispositifs biométriques et contrôles anti-retour. L’accès aux colocations Azure est accordé en tant que niveau d’accès de l’outil d’accès au centre de données distinct, supérieur à celui d’autres zones d’accès du centre de données. De plus, tous les employés et fournisseurs d’Azure qui ont accès aux colocations Azure Government doivent être formellement soumis à un filtrage cloud de Microsoft et à une vérification de citoyenneté américaine avant d’être autorisés à accéder à l’environnement. Pour plus de détails sur le filtrage cloud pour les colocations Azure Government, voir la section PS-03. |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800-53 - Contrôle PE-4

#### <a name="access-control-for-transmission-medium"></a>Contrôle d’accès pour les moyens de transmission

**PE-4** L’organisation contrôle l’accès physique aux [Affectation : lignes de distribution et de transmission du système d’information définies par l’organisation] au sein des installations de l’organisation à l’aide de [Affectation : mesures de sécurité définies par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure a implémenté un contrôle d’accès pour les moyens de transmission via la conception et la construction de salles de répartiteur téléphonique et de colocations pour protéger les lignes de distribution et de transmission du système d’information contre les dommages accidentels, les perturbations de service et les altérations physiques. L’accès aux salles de répartiteur téléphonique et aux colocations requiert une authentification à deux facteurs (badge d’accès et biométrie). Cela garantit que l’accès est limité aux seules les personnes autorisées (voir PE-2, PE-3). Dans le répartiteur téléphonique, les lignes de transmission et de distribution sont protégées contre les dommages accidentels, les perturbations et les altérations physiques à l’aide de conduits métalliques, d’armoires verrouillées, de cages ou de chemins de câbles. |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800-53 - Contrôle PE-5

#### <a name="access-control-for-output-devices"></a>Contrôle d’accès aux périphériques de sortie

**5-PE** L’organisation contrôle l’accès physique aux périphériques de sortie du système d’information pour empêcher des personnes non autorisées d’accéder à ces sorties.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les centres de données Microsoft Azure n’ont pas de périphériques de sortie (moniteurs, imprimantes, périphériques audio, etc.) connectés en permanence aux ressources ou aux ressources partagées Azure. En plus de cette absence de périphériques de sortie, les agents de sécurité effectuent des vérifications physiques de l’installation plusieurs fois par équipe, afin de contrôler des aspects tels que le verrouillage des portes et la sécurisation des armoires. L’accès au centre de données est limité aux personnes disposant d’autorisations d’accès approuvées. L’accès aux colocations requiert une authentification à deux facteurs (badge d’accès et biométrie). |


 ## <a name="nist-800-53-control-pe-6a"></a>NIST 800-53 - Contrôle PE-6.a

#### <a name="monitoring-physical-access"></a>Surveillance de l’accès physique

**PE-6.a** L’organisation surveille l’accès physique à l’installation dans laquelle réside le système d’information afin de détecter et résoudre les incidents de sécurité physique.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. L’accès physique est surveillé par l’implémentation de dispositifs et processus de sécurité dans les centres de données. Il s’agit, par exemple, de surveillance électronique de contrôle d’accès, d’alarmes et de systèmes vidéos, ainsi que de patrouilles de sécurité surveillant l’installation et son enceinte en permanence. Un superviseur de salle de commande se trouve dans le centre d’opérations de sécurité (SOC) en permanence pour surveiller l’accès physique au centre de données. |


 ## <a name="nist-800-53-control-pe-6b"></a>NIST 800-53 - Contrôle PS-6.b

#### <a name="monitoring-physical-access"></a>Surveillance de l’accès physique

**PE-6.b** L’organisation analyse les journaux d’accès physique [Affectation : fréquence définie par l’organisation] et en cas l’occurrence de [Affectation : événements définis par l’organisation ou indications potentielles d’événements].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les journaux d’accès physique sont analysés en permanence, et conservés à des fins d’investigation ultérieure. |


 ## <a name="nist-800-53-control-pe-6c"></a>NIST 800-53 - Contrôle PL-6.c

#### <a name="monitoring-physical-access"></a>Surveillance de l’accès physique

**PE-6.c** L’organisation coordonne les résultats des analyses et investigations avec la capacité de réponse aux incidents de l’organisation. 

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les événements de sécurité qui se produisent à l’intérieur du centre de données sont documentés par l’équipe de sécurité. Celle-ci établit des rapports contenant les détails d’un événement de sécurité survenu. <br /> Pour les incidents nécessitant une notification au gouvernement, l’équipe de sécurité de Microsoft Azure se coordonne avec le fournisseur d’application principal (par exemple, Office 365) pour informer l’organisme gouvernemental client, US CERT et le programme FedRAMP dans le respect des directives US-CERT (voir IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800-53 - Contrôle PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Surveillance de l’accès physique | Alarmes d’intrusion / Équipements de Surveillance

**PE-6 (1)** L’organisation surveille les alarmes d’intrusion et équipements de surveillance physiques.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. En plus de la sécurité sur site permanente, les centres de données Microsoft Azure (loués et entièrement gérés) utilisent des systèmes de surveillance d’alarmes et de vidéosurveillance. Les alarmes sont surveillées et traitées par le superviseur de salle de commande présent en permanence dans le centre d’opérations de sécurité (SOC). Durant une situation de réponse, le superviseur de salle de commande utilise les caméras situées dans la zone de l’incident analysé pour fournir à l’intervenant des informations en temps réel. |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800-53 - Contrôle PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Surveillance de l’accès physique | Surveillance de l’accès physique aux systèmes d’informations

**PE-6 (4)** L’organisation surveille l’accès physique au système d’information en plus des accès physiques à l’installation dans [Affectation : espaces physiques définis par l’organisation contenant un ou plusieurs composants du système d’information].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure surveille l’accès physique aux installations ainsi qu’aux systèmes d’information dans les centres de données. Tous les équipements des services en ligne de Microsoft sont placés dans des emplacements à l’intérieur de centres de données dont l’accès physique est surveillé. Toutes les salles de colocation et de répartiteur téléphonique sont protégées par contrôle d’accès, alarmes et vidéo. |


 ## <a name="nist-800-53-control-pe-8a"></a>NIST 800-53 - Contrôle PE-8.a

#### <a name="visitor-access-records"></a>Enregistrements des accès des visiteurs

**PE-8.a** L’organisation conserve les enregistrements des accès des visiteurs à l’installation dans laquelle réside de système d’information pendant [Affectation : période définie par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les enregistrements des accès au centre de données sont conservés dans l’outil d’accès au centre de données sous la forme de demandes approuvées. Comme décrit dans PE-3, les visiteurs doivent être escortés en permanence. L’accès de l’escorte au centre de données est journalisé et, le cas échant, peut être mis en corrélation avec le visiteur à des fins d’analyse ultérieure. |


 ## <a name="nist-800-53-control-pe-8b"></a>NIST 800-53 - Contrôle PE-8.b

#### <a name="visitor-access-records"></a>Enregistrements des accès des visiteurs

**RA-8.c** L’organisation examine les enregistrements des accès des visiteurs [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. L’enregistrement d’accès des visiteurs dont la demande d’accès a été approuvée est examiné lors de la vérification de leur identification par rapport à un formulaire d’identité émis par le gouvernement ou à un badge émis par Microsoft. Comme décrit dans PE-3, les visiteurs sont escortés en permanence à l’intérieur du centre de données. |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800-53 - Contrôle PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Enregistrements des accès des visiteurs | Tenue à jour et analyse des enregistrements automatisés

**PE-8 (1)** L’organisation utilise des mécanismes automatisés pour faciliter la tenue à jour et l’analyse des enregistrements des accès des visiteurs.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure conserve les enregistrements des accès au centre de données dans l’outil d’accès au centre de données sous la forme de demandes à l’outil d’accès au centre de données approuvées. Les demandes adressées à l’outil d’accès au centre de données ne peuvent être approuvées que par l’équipe de gestion du centre de données. Les niveaux d’accès à l’intérieur du centre de données sont affectés et gérés dans l’outil d’accès au centre de données. L’accès au centre de données est examiné chaque trimestre. Tous les accès aux centres de données Azure sont enregistrés dans l’outil d’accès au centre de données, et disponibles pour d’éventuelles investigations futures. Les visiteurs doivent être escortés en permanence. L’accès de l’escorte au centre de données est journalisé dans le système de surveillance des alarmes et, le cas échant, peut être mis en corrélation avec le visiteur à des fins d’analyse ultérieure. L’accès d’un visiteur est contrôlé en permanence par l’escorte attribué, et par le superviseur de salle de commande via le système de vidéosurveillance et de surveillance des alarmes. Les visiteurs ne sont pas autorisés à accéder et doivent être accompagnés par leur escorte en permanence. |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800-53 - Contrôle PE-9

#### <a name="power-equipment-and-cabling"></a>Équipement et câblage d’alimentation

**PE-9** L’organisation protège l’équipement et le câblage d’alimentation du système d’information contre les dommages et destructions.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure fournit des espaces de protection et un étiquetage approprié pour les câbles. Les équipements d’infrastructure de Microsoft Azure, tels que les câbles, les lignes électriques et les groupes électrogènes de secours, doivent être placés dans des environnements conçus pour être protégés contre des risques tels que le vol, l’incendie, les explosifs, la fumée, l’eau, la poussière, les vibrations, les séismes, les produits chimiques dangereux, les interférences électriques, les pannes de courant et les perturbations électriques (PICS). Tous les composants portables des services en ligne (par exemple, armoires, serveurs, périphériques réseau) doivent être verrouillés ou attachés afin de les protéger contre le vol ou des dommages pouvant être occasionnés par un déplacement. Les câbles d’alimentation et des systèmes d’information dans tout environnement Microsoft Azure sont dûment étiquetés et protégés contre les interceptions ou dommages éventuels. Les câbles d’alimentation et ceux des systèmes d’information sont séparés partout à l’intérieur d’un environnement afin d’éviter les interférences. |


 ## <a name="nist-800-53-control-pe-10a"></a>NIST 800-53 - Contrôle PE-10.a

#### <a name="emergency-shutoff"></a>Arrêt d’urgence

**PE-10.a** L’organisation offre la possibilité de couper l’alimentation du système d’information et de composants de celui-ci en cas d’urgence.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure a installé des interrupteurs d’urgence dans divers emplacements du centre de données, conformément au code local de prévention des incendies. Dans certains centres de données gérés par Microsoft Azure, la conception-même du centre de données élimine la nécessité d’interrupteurs. |


 ## <a name="nist-800-53-control-pe-10b"></a>NIST 800-53 - Contrôle PE-10.b

#### <a name="emergency-shutoff"></a>Arrêt d’urgence

**PE-10.b** L’organisation place des interrupteurs ou dispositifs d’arrêt d’urgence dans [Affectation : emplacement défini par l’organisation, par système d’information ou composant système] afin de faciliter un accès aisé et sécurisé du personnel.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les interrupteurs sont disposés de façon stratégique pour permettre leur activation en cas d’urgence. Des interrupteurs peuvent être placés dans des colocations, des centres d’opérations des installations gérés, ou conformément au code local de prévention des incendies. |


 ## <a name="nist-800-53-control-pe-10c"></a>NIST 800-53 - Contrôle PE-10.c

#### <a name="emergency-shutoff"></a>Arrêt d’urgence

**PE-10.c** L’organisation protège le dispositif d’arrêt d’urgence contre toute activation non autorisée.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Pour empêcher toute activation accidentelle des interrupteurs, ceux-ci sont munis d’un boîtier de protection, requièrent une double activation, ou émettent une alarme sonore en guide d’avertissement avant l’activation. De plus, les interrupteurs sont sous vidéosurveillance. |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800-53 - Contrôle PE-11

#### <a name="emergency-power"></a>Alimentation de secours

**PE-11** L’organisation fournit un onduleur à court terme pour faciliter [Sélection (un ou plusieurs) : un arrêt ordonné du système d’information ; une transition du système d’information vers une alimentation secondaire à long terme] en cas de perte de la source d’alimentation principale.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure a implémenté une alimentation de secours en protégeant les équipements et circuits du centre de données à l’aide d’un onduleur qui assure l’alimentation à court terme jusqu’à ce que des groupes électrogènes puissent prendre le relais. |


 ### <a name="nist-800-53-control-pe-11-1"></a>NIST 800-53 - Contrôle PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Alimentation de secours | Alimentation secondaire à long terme - Capacité opérationnelle minimale

**PE-11 (1)** L’organisation fournit une alimentation secondaire à long terme pour le système d’information, qui est capable de maintenir la capacité opérationnelle minimale requise en cas de perte prolongée de la source d’alimentation principale.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure a implémenté une alimentation secondaire à long terme pour le système d’information, qui est capable de maintenir la capacité opérationnelle minimale requise en cas de perte prolongée de la source d’alimentation principale. En cas de coupure d’alimentation ou de chute de la tension à un niveau inacceptable, des onduleurs interviennent instantanément pour maintenir l’alimentation. Ceux-ci fournissent suffisamment de puissance pour faire fonctionner les serveurs jusqu’à ce que les groupes électrogènes puissent prendre le relais. Des groupes électrogènes de secours fournissent une alimentation secondaire en cas d’interruption prolongée ou de maintenance planifiée, et peuvent faire fonctionner le centre de données grâce à une réserve locale de carburant en cas de catastrophe naturelle. Azure assure la maintenance des groupes électrogènes diesel de bon nombre de nos centres de données. Des groupes électrogènes de secours sont utilisés le cas échéant pour aider à maintenir la stabilité du réseau, ou dans des situations extraordinaires de réparation et maintenance nécessitant de déconnecter les centres de données du réseau d’alimentation. |


 ## <a name="nist-800-53-control-pe-12"></a>NIST 800-53 - Contrôle PE-12

#### <a name="emergency-lighting"></a>Éclairage de secours

**PE-12** L’organisation utilise et gère un éclairage de secours automatique pour le système d’information, qui s’active en cas de panne ou de coupure d’alimentation, et qui couvre les issues de secours et voies d’évacuation à l’intérieur de l’installation.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les centres de données Microsoft Azure (loués et entièrement gérés) implémentent un éclairage de secours sous la forme d’un système d’éclairage d’urgence alimenté par des circuits dédiés s’appuyant sur des onduleurs et des groupes électrogènes (voir PE-11). L’éclairage de secours automatique est implémenté sur les voies d’évacuation et les issues de secours, ainsi qu’à l’intérieur des colocations conformément au code de sécurité de la NFPA (National Fire Protection Association). En cas de perte de l’alimentation de l’installation, l’éclairage de secours s’allume automatiquement grâce à l’alimentation fournie par les onduleurs et groupes électrogènes. Les systèmes d’éclairage de secours au sein des centres de données Microsoft Azure font l’objet d’une maintenance de routine destinée à s’assurer qu’ils restent en parfait état de marche. |


 ## <a name="nist-800-53-control-pe-13"></a>NIST 800-53 - Contrôle PE-13

#### <a name="fire-protection"></a>Protection incendie

**PE-13** L’organisation utilise et entretient des dispositifs/systèmes de détection et d’extinction d’incendie pour le système d’information, qui sont alimentés par une source d’énergie indépendante.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure a implémenté une protection incendie en installant des systèmes de détection et d’extinction d’incendie dans les centres de données Microsoft Azure. <br /> Les centres de données Microsoft Azure disposent de mécanismes de détection d’incendie robustes. L’approche de la protection incendie de Microsoft Azure inclut l’utilisation de détecteurs de fumée photoélectriques installés sous le plancher et au plafond, qui sont intégrés au système de gicleurs du dispositif de protection incendie. De plus, dans chaque colocation, sont installés des détecteurs quasi instantanés de fumée VESDA (Very Early Smoke Detection Alarm) Xtralis qui analysent l’air. Les unités VESDA sont des systèmes très sensibles d’échantillonnage de l’air installées dans divers espaces importants. Les unités VESDA permettent d’apporter une réponse basée sur une investigation avant le déclenchement d’une alarme réelle de détection d’incendie. <br /> Des boîtiers de type avertisseur manuel d’incendie sont installés partout dans les centres de données pour permettre un déclenchement manuel d’alarme d’incendie. Des extincteurs sont disponibles partout dans les centres de données, qui sont dûment inspectés, entretenus et étiquetés annuellement. Le personnel de sécurité patrouille dans toutes les zones bâties plusieurs fois par équipe. Le personnel du centre de données vérifie quotidiennement le site pour s’assurer que toutes les exigences de veille incendie sont respectées. <br /> Les zones contenant des équipements électriques sensibles (colocations, répartiteurs téléphoniques, etc.) sont protégées par des systèmes de gicleurs à pré-action double (tuyau sec). Les gicleurs à tuyau sec sont un dispositif de pré-action en deux étapes dont le déclenchement requiert une activation de la tête de gicleur (due à la chaleur) ainsi qu’une détection de fumée. L’activation de la tête du gicleur relâche la pression d’air dans le tuyau, qui permet l’admission d’eau dans celui-ci. L’eau est libérée quand un détecteur de chaleur ou de fumée est également activé. <br /> Les systèmes de détection/extinction d’incendie et d’éclairage de secours sont raccordés aux systèmes d’onduleur et de groupe électrogène du centre de données de façon à offrir une source d’alimentation redondante. |


 ### <a name="nist-800-53-control-pe-13-1"></a>NIST 800-53 - Contrôle PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Protection incendie | Dispositifs/systèmes de détection

**PE-13 (1)** L’organisation utilise des dispositifs/systèmes de détection d’incendie pour le système d’information, qui s’activent automatiquement et informent les [Affectation : personnel ou rôles définis par l’organisation] et [Affectation : intervenants d’urgence définis par l’organisation] en cas d’incendie.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure utilise des dispositifs/systèmes de détection d’incendie pour le système d’information, qui s’activent automatiquement et informent le personnel du centre de données ainsi que les intervenants d’urgence en cas d’incendie. Si l’un des mécanismes de détection incendie est déclenché dans une colocation, le service incendie local est automatiquement averti via le système d’alarme incendie. De plus, les systèmes de détection et de protection incendie sont reliés au système de sécurité notifiant le personnel local de sécurité et d’exploitation de l’installation. |


 ### <a name="nist-800-53-control-pe-13-2"></a>NIST 800-53 - Contrôle PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Protection incendie | Dispositifs/systèmes d’extinction

**PE-13 (2)** L’organisation utilise des dispositifs/systèmes d’extinction pour le système d’information, qui informent automatiquement les [Affectation : personnel ou rôles définis par l’organisation] et [Affectation : intervenants d’urgence définis par l’organisation] en cas de déclenchement.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Si l’un des systèmes d’extinction est déclenché dans le centre de données, le service incendie local est automatiquement averti via le système d’alarme incendie. De plus, les systèmes de détection et de protection incendie sont reliés au système de sécurité notifiant le personnel local de sécurité et d’exploitation de l’installation. |


 ### <a name="nist-800-53-control-pe-13-3"></a>NIST 800-53 - Contrôle PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Protection incendie | Extinction automatique

**PE-13 (3)** L’organisation utilise un dispositif d’extinction automatique pour le système d’information lorsque l’installation n’est pas pourvue en personnel de façon permanente.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Les centres de données Microsoft Azure sont pourvus en personnel de façon permanente. Les systèmes d’extinction se déclenchent automatiquement sans intervention manuelle en cas de détection d’une situation d’alarme incendie. |


 ## <a name="nist-800-53-control-pe-14a"></a>NIST 800-53 - Contrôle PE-14.a

#### <a name="temperature-and-humidity-controls"></a>Contrôles de température et d’humidité

**PE-14.a** L’organisation maintient les niveaux de température et d’humidité au sein de l’installation dans laquelle réside le système d’information à [Affectation : niveaux acceptables définis par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure gère les niveaux de température et d’humidité conformément aux directives de l’ASHRAE (American Society of Heating, Refrigerating and Air-conditioning Engineers). Les niveaux de température et d’humidité sont surveillés en permanence par le système de Gestion Technique de Bâtiment (GTB) du centre de données. |


 ## <a name="nist-800-53-control-pe-14b"></a>NIST 800-53 - Contrôle PE-14.b

#### <a name="temperature-and-humidity-controls"></a>Contrôles de température et d’humidité

**PE-14.b** L’organisation surveille les niveaux de température et d’humidité [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Dans les centres de données Microsoft Azure, les niveaux de température et d’humidité sont surveillés en permanence par le système de Gestion Technique de Bâtiment (GBT). Le personnel du centre de données surveille le GBT à partir du centre d’opérations des installations, afin de pouvoir gérer la température et l’humidité à l’intérieur du centre de données avant tout dépassement des seuils d’alarme. |


 ### <a name="nist-800-53-control-pe-14-2"></a>NIST 800-53 - Contrôle PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Contrôles de température et d’humidité | Surveillance à l’aide d’alarmes et de notifications

**PE-14 (2)** L’organisation utilise un système de surveillance de la température et de l’humidité qui génère une alarme ou une notification en cas de changements potentiellement dangereux pour le personnel ou le matériel.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Dans les centres de données Microsoft Azure, les niveaux de température et d’humidité sont surveillés en permanence par le système de Gestion Technique de Bâtiment (GBT). Le personnel du centre de données surveille le GBT à partir du centre d’opérations des installations, afin de pouvoir gérer la température et l’humidité à l’intérieur du centre de données avant tout dépassement des seuils d’alarme. |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800-53 - Contrôle PE-15

#### <a name="water-damage-protection"></a>Protection contre les dégâts des eaux

**PE-15** L’organisation protège le système d’information contre les dégâts résultant de fuites d’eau en fournissant des vannes de fermeture ou d’isolation accessibles, en parfait état de fonctionnement et connues du personnel clé.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure assure la détection des fuites d’eau dans les zones à risque (telles que les unités de traitement de l’air). Les systèmes d’extinction d’incendie sont également pourvus d’alarmes de détection des fuites qui font l’objet d’une surveillance. Le système de détection des fuites d’eau est intégré au système d’alarmes et de notifications de l’installation. Les systèmes de gicleurs dans les centres de données sont répartis en zones. Le personnel du centre de données est parfaitement des procédures d’urgence nécessitant une fermeture des vannes d’eau et des emplacements de celles-ci. Les gicleurs anti-incendie peuvent être fermés individuellement ou par groupes à l’aide de robinets-vannes. Tous les gicleurs dans l’espace critique sont du type à pré-action double nécessitant deux formes d’activation avant la libération du flux. La pression d’eau du système de gicleurs est surveillée, et des alarmes sont déclenchées en cas de fuite. |


 ### <a name="nist-800-53-control-pe-15-1"></a>NIST 800-53 - Contrôle PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>Protection contre les dégâts des eaux | Prise en charge de l’automation

**PE-15 (1)** L’organisation utilise des mécanismes automatisés pour détecter la présence d’eau à proximité du système d’information, qui alertent [Affectation : personnel ou rôles définis par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure utilise des mécanismes automatisés pour détecter la présence d’eau dans les centres de données, qui alertent le personnel du centre de données. Microsoft Azure assure la détection des fuites d’eau dans les zones à risque (telles que les unités de traitement de l’air). Les systèmes d’extinction d’incendie sont également pourvus d’alarmes de détection des fuites qui font l’objet d’une surveillance. Le système de détection des fuites d’eau est intégré au système d’alarmes et de notifications de l’installation. La pression d’eau du système de gicleurs est surveillée, et des alarmes sont déclenchées en cas de fuite. |


 ## <a name="nist-800-53-control-pe-16"></a>NIST 800-53 - Contrôle PE-16

#### <a name="delivery-and-removal"></a>Livraison et élimination

**PE-16** L’organisation autorise, surveille et contrôle les [Affectation : types de composants du système d’information définis par l’organisation] entrants et sortants de l’installation, et gère les enregistrements de ces éléments.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure implémente cette exigence pour le compte des clients. Microsoft Azure applique strictement les prescriptions relatives aux éléments qui sont autorisés à entrer dans le centre de données ainsi qu’à en sortir. L’ensemble des composants et ressources du système sont suivis dans la base de l’outil de gestion des composants. |


 ## <a name="nist-800-53-control-pe-17a"></a>NIST 800-53 - Contrôle PE-17.a

#### <a name="alternate-work-site"></a>Site de travail de secours

**PE-17.a** L’organisation utilise [Affectation : contrôles de sécurité définis par l’organisation] sur des sites de travail de secours.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources du site de travail de secours de niveau entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-pe-17b"></a>800-53 - Contrôle PE-17.b

#### <a name="alternate-work-site"></a>Site de travail de secours

**PE-17.b** L’organisation évalue l’efficacité des contrôles de sécurité au niveau des sites de travail de secours.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources du site de travail de secours de niveau entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800-53 - Contrôle PE-17.c

#### <a name="alternate-work-site"></a>Site de travail de secours

**PE-17.c** L’organisation offre aux employés un moyen de communiquer avec le personnel chargé de la sécurité des informations en cas d’incidents ou de problèmes de sécurité.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les ressources du site de travail de secours de niveau entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-pe-18"></a>NIST 800-53 - Contrôle PE-18

#### <a name="location-of-information-system-components"></a>Emplacement des composants du système d’information

**PE-18** L’organisation positionne les composants du système d’informations au sein de l’installation afin de minimiser les dommages potentiels occasionnés par [Affectation : dangers physiques et environnementaux définis par l’organisation] ainsi que la possibilité d’accès non autorisé.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Les clients ne disposent d’aucun accès physique aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Azure implémente une approche stratégique de la conception de centre de données pour satisfaire au contrôle de l’emplacement des composants du système d’informations. L’équipement de tous les services en ligne de Microsoft est placé dans des emplacements conçus pour être protégés contre des risques tels que le vol, l’incendie, les explosifs, la fumée, l’eau, la poussière, les vibrations, les séismes, les produits chimiques dangereux, les interférences électriques, les pannes de courant, les perturbations électriques (PICS) et les radiations. L’installation et l’infrastructure sont équipés d’entretoisements parasismiques pour la protection contre les risques environnementaux. Toutes les salles de colocation et de répartiteur téléphonique sont protégées par contrôle d’accès, alarmes et vidéo. Des agents de sécurité patrouillent également dans l’installation en permanence. Toutes les ressources Azure portables sont verrouillées ou attachées afin d’offrir une protection contre le vol ou des dommages pouvant être occasionnés par un déplacement. |


