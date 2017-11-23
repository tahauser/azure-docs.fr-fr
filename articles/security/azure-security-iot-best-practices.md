---
title: "Internet des objets (IoT) : bonnes pratiques en matière de sécurité | Microsoft Docs"
description: "L’article fournit une liste organisée des meilleures pratiques en matière de sécurité pour l’Internet des objets, ainsi que des recommandations générales."
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 8937437652a78e05b94574cb9fe5df7962edb1ad
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="internet-of-things-security-best-practices"></a>Internet des objets (IoT) : meilleures pratiques en matière de sécurité
La sécurisation de l’infrastructure de l’Internet des objets (IoT) est un aspect critique pour toutes les personnes travaillant avec les solutions IoT. En raison du nombre d’appareils concernés et de leur nature distribuée, l’impact d’un événement de sécurité pouvant compromettre des millions d’appareils IoT n’est pas trivial et peut avoir des conséquences importantes.

Pour cette raison, la sécurité de l’IoT nécessite une approche en profondeur. Les données doivent être sécurisées dans le Cloud lorsqu’elles se déplacent sur des réseaux privés et publics. Vous devez mettre en place des méthodes pour configurer en toute sécurité les appareils IoT. Chaque couche entre l’appareil et le réseau ou le service Cloud principal nécessite des garanties de sécurité renforcées.

Les meilleures pratiques de l’IoT peuvent être classées de la manière suivante :

* Fabricant ou intégrateur de matériel IoT
* Développeur de solutions IoT
* Responsable du déploiement de solutions IoT
* Opérateur de solutions IoT

Cet article résume les [meilleures pratiques de sécurité pour l’Internet des objets (IoT)](../iot-suite/iot-security-best-practices.md). Reportez-vous à cet article pour obtenir plus d’informations.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricant ou intégrateur de matériel IoT
Si vous êtes fabricant ou intégrateur de matériel IoT, suivez les meilleures pratiques ci-dessous :

* **Concevoir le matériel selon les exigences minimales**: la conception du matériel doit inclure les fonctionnalités minimales requises pour son fonctionnement, et rien de plus. 
* **Protéger l’appareil contre les falsifications**: intégrez un mécanisme permettant de détecter toute falsification physique du matériel (ouverture du capot, suppression d’une partie de l’appareil, etc.). 
* **Intégrer la sécurité au matériel**: si le [coût des marchandises vendues](https://en.wikipedia.org/wiki/Cost_of_goods_sold) le permet, intégrez des fonctionnalités de sécurité, telles qu’un stockage sécurisé et chiffré, ainsi qu’une fonctionnalité de démarrage basée sur un Module de plateforme sécurisée (TPM).
* **Sécuriser les mises à jour**: le microprogramme doit être mis à jour durant toute la durée de vie de l’appareil.

## <a name="iot-solution-developer"></a>Développeur de solutions IoT
Si vous êtes développeur de solutions IoT, suivez les meilleures pratiques ci-dessous :

* **Respecter la méthodologie de développement de logiciels sécurisés**: le développement de logiciels sécurisés requiert une réflexion de bout en bout sur la sécurité, depuis le lancement du projet jusqu’à son implémentation, aux tests et au déploiement.
* **Choisir des logiciels open source avec précaution**: les logiciels open source offrent la possibilité de développer des solutions rapidement.
* **Intégrer les composants avec précaution**: les failles de sécurité logicielle se situent, pour nombre d’entre elles, à la limite des bibliothèques et des API. 

## <a name="iot-solution-deployer"></a>Responsable du déploiement de solutions IoT
Si vous êtes responsable du déploiement de solutions IoT, suivez les meilleures pratiques ci-dessous :

* **Déployer le matériel de façon sécurisée**: les déploiements IoT peuvent nécessiter un déploiement du matériel dans des lieux non sécurisés, par exemple dans des espaces publics ou des régions non supervisées.
* **Protéger les clés d’authentification**: au cours du déploiement, chaque appareil requiert des ID et les clés d’authentification associées, générées par le service cloud. Protégez physiquement ces clés, même après le déploiement. Une clé compromise pourrait être utilisée par un appareil malveillant pour usurper l’identité d’un appareil existant.

## <a name="iot-solution-operator"></a>Opérateur de solutions IoT
Si vous êtes opérateur de solutions IoT, suivez les meilleures pratiques ci-dessous :

* **Garder le système à jour**: vérifiez que les systèmes d’exploitation et tous les pilotes des appareils sont mis à jour avec les dernières versions. 
* **Protéger le système contre les activités malveillantes**: implémentez les dernières fonctionnalités de protection antivirus et anti-programme malveillant sur le système d’exploitation de chaque appareil, lorsque celui-ci le permet. 
* **Audit fréquent**: il est essentiel d’auditer l’infrastructure IoT vis-à-vis des problèmes de sécurité pour traiter les incidents de sécurité.
* **Protéger physiquement l’infrastructure IoT**: les attaques de sécurité les plus menaçantes envers l’infrastructure IoT sont lancées par le biais d’un accès physique aux appareils.
* **Protéger les informations d’identification cloud**: les informations d’identification cloud utilisées pour configurer et exploiter un déploiement IoT offrent peut-être le moyen le plus simple d’accéder à un système IoT et de le compromettre. 

