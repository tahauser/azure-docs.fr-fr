---
title: "Programme Blueprint Security & Compliance Azure - Automatisation d’applications web FedRAMP - Protection média"
description: "Automatisation d’applications web FedRAMP - Protection média"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 37812c2f7ee79685f9014a7999b4355e649ca6e1
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="media-protection-mp"></a>Protection média (MP)

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour obtenir des informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication NIST 800-53 Rev. 4.

## <a name="nist-800-53-control-mp-1"></a>NIST 800-53 - Contrôle MP-1

#### <a name="media-protection-policy-and-procedures"></a>Procédures et stratégie de protection des supports

**MP-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles de l’organisation] une stratégie de protection des supports qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie de protection des supports et des contrôles de protection des supports associés ; et révise et met à jour la stratégie actuelle de protection des supports [Affectation : fréquence définie par l’organisation] ; et les procédures de protection des supports [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures de protection des supports de niveau d’entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-mp-2"></a>NIST 800-53 Contrôle MP-2

#### <a name="media-access"></a>Accès aux supports

**MP-2** L’organisation restreint l’accès à [Affectation : types définis par l’organisation de supports numériques et/ou non numériques] à [Affectation : personnel ou rôles définis par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure a implémenté l’accès aux supports via l’implémentation de la stratégie de sécurité de Microsoft. L’accès logique aux supports numériques est contrôlé via les groupes de sécurité et objets de stratégie de groupe Active Directory (AD). L’accès physique à tous les supports est limité par le processus d’accès de centre de données. L’accès est restreint aux personnes qui ont une raison légitime d’accéder aux données. Pour plus d’informations sur les contrôles d’accès aux centres de données en place, consultez PE-3, Contrôle d’accès physique. La norme de protection des ressources définit les mécanismes de protection requis pour protéger la confidentialité, l’intégrité et la disponibilité des ressources d’informations au sein des centres de données Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>NIST 800-53 Contrôle MP-3.a

#### <a name="media-marking"></a>Marquage de supports

**MP-3.a** L’organisation marque les supports du système d’information indiquant les limitations de la distribution, la gestion des mises en garde et les marquages de sécurité applicables (le cas échéant) pour les informations.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure marque les ressources au sein des centres de données Microsoft avec une désignation HBI, MBI ou LBI (impact fort, moyen ou faible sur l’entreprise) qui requiert différents niveaux de sécurité et des précautions d’emploi. Les propriétaires de ressources doivent classer leurs ressources stockées dans un centre de données Microsoft. Pour plus d’informations, consultez Norme de classification des ressources et Norme de protection des ressources. |


 ## <a name="nist-800-53-control-mp-3b"></a>NIST 800-53 Contrôle MP-3.b

#### <a name="media-marking"></a>Marquage de supports

**MP-3.b** L’organisation exempte [Affectation : types définis par l’organisation de supports du système d’information] du marquage tant que le support reste au sein de [Affectation : zones contrôlées définies par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure demande aux propriétaires des ressources d’affecter leurs ressources avec une classification de ressource, et aucune ressource n’est exempte de cette exigence. Dans l’environnement de centre de données Microsoft, les ressources font référence aux serveurs, aux appareils réseau et aux bandes magnétiques. Les autres supports numériques comme les lecteurs USB flash, les disques durs externes/amovibles ou les CD/DVD ne sont pas utilisés. Les supports non numériques ne sont pas utilisés dans le centre de données. |


 ## <a name="nist-800-53-control-mp-4a"></a>NIST 800-53 Contrôle MP-4.a

#### <a name="media-storage"></a>Supports de stockage

**MP-4.a** L’organisation contrôle physiquement et stocke de façon sûre [Affectation : types définis par l’organisation de supports numériques et/ou non numériques] dans [Affectation : zones contrôlées définies par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Les ressources de supports numériques Microsoft Azure sont stockées physiquement et en toute sécurité dans des salles de colocalisation de centre de données. Les centres de données Microsoft ont plusieurs couches de contrôle d’accès physique (badge d’accès, biométrie ; consultez PE-3 pour plus d’informations sur les contrôles d’accès physiques) et de surveillance vidéo en place pour fournir un stockage sécurisé. Les supports numériques comprennent les serveurs, appareils réseau et bandes magnétiques utilisés pour la sauvegarde. Les supports non numériques ne sont pas utilisés dans l’environnement du centre de données. |


 ## <a name="nist-800-53-control-mp-4b"></a>NIST 800-53 Contrôle MP-4.b

#### <a name="media-storage"></a>Supports de stockage

**MP-4.b** L’organisation protège le support du système d’information jusqu'à ce que le support soit détruit, ou nettoyé à l’aide de matériel, techniques et procédures approuvés.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Les ressources multimédias numériques Microsoft Azure sont protégées dans des colocations de centre de données Microsoft par le biais des contrôles d’accès physique (PE-3) et des contrôles d’accès logique (IA-2) pour la durée de vie de la ressource. Les ressources Microsoft Azure sont effacées, purgées ou détruites avec des méthodes conformes à SP NIST 800-88 avant la suppression des ressources. Pour la destruction des ressources, Microsoft Azure utilise des services de destruction des ressources sur site. |


 ## <a name="nist-800-53-control-mp-5a"></a>NIST 800-53 Contrôle MP-5.a

#### <a name="media-transport"></a>Transport de supports

**MP-5.a** L’organisation protège et contrôle [Affectation : types définis par l’organisation de supports de système d’information] pendant le transport en dehors des zones contrôlées à l’aide de [Affectation : protections définies par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Les supports numériques Microsoft Azure dans les centres de données Microsoft se composent de serveurs, d’appareils réseau et de bandes magnétiques et disques de sauvegarde, selon les besoins. Les centres de données Microsoft n’utilisent pas de supports non numériques. Microsoft utilise trois méthodes pour protéger un support qui est transporté en dehors du centre de données : 1) Transport sécurisé, 2) Chiffrement, 3) Nettoyage, effacement ou destruction. |


 ## <a name="nist-800-53-control-mp-5b"></a>NIST 800-53 Contrôle MP-5.b

#### <a name="media-transport"></a>Transport de supports

**MP-5.b** L’organisation gère la responsabilité de support du système d’information pendant le transport en dehors des zones contrôlées.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure gère la responsabilité pour les ressources qui quittent le centre de données à l’aide des instructions de NIST SP 800-88 : nettoyage/purge cohérent, destruction de la ressource, chiffrement, inventaire précis, suivi et protection de la chaîne de responsabilité pendant le transport. |


 ## <a name="nist-800-53-control-mp-5c"></a>NIST 800-53 Contrôle MP-5.c

#### <a name="media-transport"></a>Transport de supports

**MP-5.c** L’organisation documente les activités associées au transport des supports de système d’information.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure gère les enregistrements d’inventaire avant le transport, le suivi et la protection de la chaîne de responsabilité au cours du transport, le nettoyage/la purge de la ressource, la destruction de la ressource, la réception de la ressource et la validation de l’inventaire après le transport. |


 ## <a name="nist-800-53-control-mp-5d"></a>NIST 800-53 Contrôle MP-5.d

#### <a name="media-transport"></a>Transport de supports

**MP-5.d** L’organisation restreint les activités associées au transport des supports de système d’information au personnel autorisé.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure limite les activités de transport de la ressource multimédia au personnel autorisé via la protection de la chaîne de responsabilité. L’utilisation de verrous, de scellages temporaires et d’une validation des stocks de ressources garantit que seules les personnes autorisées sont impliquées dans le transport actif. |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800-53 Contrôle MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Transport de supports | Protection par chiffrement

**MP-5 (4)** Le système d’information implémente des mécanismes de chiffrement pour protéger la confidentialité et l’intégrité des informations stockées sur les supports numériques pendant le transport en dehors des zones contrôlées.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure emploie des clés du service de protection des données (STD) pour gérer les clés cryptographiques à l’aide d’un module de chiffrement FIPS 140-2 niveau 3 validé (cert #1694) et HSM (cert #1178) pour sécuriser les données chiffrées AES 256 bits sur les bandes magnétiques. |


 ## <a name="nist-800-53-control-mp-6a"></a>NIST 800-53 Contrôle MP-6.a

#### <a name="media-sanitization"></a>Assainissement des supports

**MP-6.a** L’organisation assainit [Affectation : support de système d’information défini par l’organisation] avant la suppression, la sortie du contrôle de l’organisation ou la sortie pour utilisation et réutilisation avec [Affectation : procédures et techniques d’assainissement définies par l’organisation] conformément aux normes et stratégiques de la région et de l’organisation.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure exige que les supports numériques dans l’environnement de centre de données Microsoft Azure soient nettoyés/purgés à l’aide d’outils Microsoft Azure approuvés et de manière conforme à NIST SP 800-88, Recommandations pour l’assainissement de supports, avant d’être réutilisés ou supprimés. Les supports non numériques ne sont pas utilisés par Microsoft Azure dans l’environnement du centre de données. |


 ## <a name="nist-800-53-control-mp-6b"></a>NIST 800-53 Contrôle MP-6.b

#### <a name="media-sanitization"></a>Assainissement des supports

**MP-6.b** L’organisation utilise des mécanismes d’assainissement avec la puissance et l’intégrité correspondant à la catégorie de sécurité ou classification des informations.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure utilise des unités et processus d’effacement des données pour nettoyer et purger les données de manière conforme à NIST SP 800-88 et qui correspondent à la classification de ressource Microsoft Azure pour la ressource. Pour les ressources nécessitant une destruction, Microsoft Azure utilise des services de destruction des ressources sur site. |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800-53 Contrôle MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Assainissement de supports | Examiner / Approuver / Suivre / Documenter / Vérifier

**MP-6 (1)** L’organisation examine, approuve, suit, documente et vérifie les actions de nettoyage et de destruction de supports.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure a implémenté les procédures d’assainissement de supports conformément aux instructions de NIST SP 800-88 pour la norme de classification des ressources et la norme de protection des ressources. Tous les supports magnétiques ou électroniques sont nettoyés/purgés en suivant les spécifications NIST SP 800-88 conformément à leur classification Azure actif. Azure utilise des unités d’effacement des données à partir des solutions de protocole extrêmes (EPS). Le logiciel EPS répond aux exigences de NIST SP 800-88 pour l’effacement et le nettoyage/la purge de façon sûre. |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800-53 Contrôle MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Assainissement de supports | Test de l’équipement

**MP-6 (2)** L’organisation teste le matériel et les procédures de nettoyage [Affectation : fréquence définie par l’organisation] pour vérifier que l’assainissement prévu est effectué.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure utilise des unités et processus d’effacement des données pour nettoyer et purger les données de manière conforme à NIST SP 800-88. Tous les 180 jours, les opérations des contrôleurs de domaine testent les unités et le processus d’effacement de données Microsoft Azure. Dans le test, les opérations des contrôleurs de domaine vérifient que l’assainissement prévu est effectué via une analyse des disques durs testés pour confirmer que les données ont été purgées par les unités d’effacement des données |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800-53 Contrôle MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Assainissement des supports | Méthodes non destructrices

**MP-6 (3)** L’organisation applique des techniques de nettoyage non destructrices pour les appareils de stockage portables avant de connecter ces appareils au système d’information dans les circonstances suivantes : [Affectation : circonstances définies par l’organisation nécessitant l’assainissement des appareils de stockage portables].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure s’assure que les centres de données Azure suivent les outils et la procédure de sécurité pour les supports amovibles dans le runbook Data Center Services pour empêcher l’infection de l’environnement d’administration par des programmes malveillants sur les appareils de stockage portable. La procédure spécifie que les actions suivantes doivent être effectuées avec les lecteurs USB avant de les utiliser dans l’environnement d’administration : <br /> (1) Formater les lecteurs USB lorsque les lecteurs sont achetés auprès d’un fabricant ou fournisseur, avant la première utilisation ou lors de la réutilisation pour un autre outil. <br /> (2) Analyser n’importe quel lecteur USB à utiliser dans une zone désignée par l’administration à la recherche de logiciels malveillants, avant d’accepter le lecteur dans la zone. <br /> (3) Après l’utilisation d’un lecteur dans une zone désignée par l’administration, formater le disque avant de quitter la zone. <br /> Les outils et la procédure de sécurité pour les supports amovibles requièrent également que les lecteurs portables perdus, jetés, volés ou mal placés ne doivent jamais être réintroduits dans des centres de données Azure, mais plutôt être catalogués et détruits. |


 ## <a name="nist-800-53-control-mp-7"></a>NIST 800-53 - Contrôle MP-7

#### <a name="media-use"></a>Utilisation des supports

**MP-7** L’organisation [Sélection : restreint ; interdit] l’utilisation de [Affectation : types définis par l’organisation de supports de système d’information] sur [Affectation : systèmes d’information ou composants système] définis par l’organisation à l’aide de [Attribution : dispositifs de protection définis par l’organisation].

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure demande aux propriétaires des ressources d’affecter leurs ressources avec une classification de ressource, et aucune ressource n’est exempte de cette exigence. Dans l’environnement de centre de données Microsoft Azure, les ressources font référence aux serveurs et aux appareils réseau. Les autres supports numériques, comme les lecteurs USB flash, sont gérés par des stratégies et procédures régissant la façon dont ces appareils sont gérés. Les CD/DVD ne sont pas utilisés. Les supports non numériques ne sont pas utilisés dans le centre de données. L’utilisation de supports numériques dans les environnements de centre de données Microsoft Azure est surveillée 24 x 7 via la couverture CCTV. Pour plus d’informations, consultez PE-06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800-53 Contrôle MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Utilisation des supports | Interdire l’utilisation en absence de propriétaire

**MP-7 (1)** l’organisation interdit l’utilisation d’appareils de stockage portables dans les systèmes d’information sur l’entreprise lorsque ces appareils n’ont aucun propriétaire identifiable.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Il n’existe aucun support contrôlé par le client dans l’étendue des systèmes déployés sur Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft restreint l’utilisation des supports amovibles, accessibles en écriture aux supports qui ont été explicitement approuvés par la gestion du centre de données via les outils des contrôleurs de domaine et la procédure pour les supports amovibles. Les supports personnels ou qui n’ont aucun propriétaire identifiable sont interdits dans n’importe quelle zone de production, comme indiqué dans le document des réglementations et des règles de travail pour les centres de données de Microsoft. |
