---
title: "Sécuriser votre Internet des objets dans Azure | Microsoft Docs"
description: " Les services Azure IoT (Internet des objets) offrent un large éventail de fonctionnalités. Cet article vous aidera à comprendre comment sécuriser vos solutions IoT dans Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: baf9c81867f8dccf3897213121888d40b7472d03
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="internet-of-things-security-overview"></a>Présentation de la sécurité de l’Internet des objets
Les services Azure IoT (Internet des objets) offrent un large éventail de fonctionnalités. Ces services de niveau professionnel vous permettent d’effectuer les tâches suivantes :

* Collecter des données à partir des appareils
* Analyser les flux de données en mouvement
* Stocker et interroger des jeux de données volumineux
* Visualiser les données historiques et en temps réel
* Intégrer des systèmes back-office

Pour fournir ces fonctionnalités, Azure IoT Suite regroupe plusieurs services Azure avec des extensions personnalisées en tant que solutions préconfigurées. Ces solutions préconfigurées sont des implémentations de base des modèles de solution IoT courants qui aident à réduire le temps dont vous avez besoin pour fournir vos solutions IoT. Les Kits de développement logiciel IoT vous permettent de personnaliser et d’étendre ces solutions pour répondre à vos besoins. Vous pouvez également utiliser ces solutions comme des exemples ou modèles lorsque vous développez de nouvelles solutions IoT.

L’Azure IoT Suite est une solution puissante pour vos besoins IoT. Toutefois, il est très important de concevoir vos solutions IoT en prenant en compte la sécurité dès le départ. En raison du nombre élevé d’appareils IoT, tout incident de sécurité peut rapidement se généraliser et provoquer des conséquences significatives.

Pour vous aider à comprendre comment sécuriser vos solutions IoT, nous vous proposons les informations suivantes.

## <a name="security-architecture"></a>Architecture de la sécurité
Lorsque vous concevez un système, il est important de comprendre les menaces potentielles qui pèsent sur ce dernier et d’ajouter les défenses appropriées en conséquence, à mesure de sa conception et de la création de son architecture. Il est particulièrement important de concevoir le produit dès le début dans une optique de sécurité : comprendre comment une personne malveillante peut compromettre un système contribue à s’assurer que les préventions adéquates sont en place dès le début.

Vous pouvez en savoir plus sur architecture de sécurité IoT en lisant l’article [Architecture de sécurité de l’Internet des objets](../iot-suite/iot-security-architecture.md).

Cet article aborde les thèmes suivants :

* [La sécurité commence par un modèle de menace](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Sécurité des solutions IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
* [Modélisation des menaces pour l’architecture de référence Microsoft Azure IoT](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Tout savoir sur la sécurité
L’IoT confronte les entreprises du monde entier à des défis uniques en termes de sécurité, de confidentialité et de conformité. Contrairement à la technologie informatique traditionnelle où ces problèmes sont axés sur les logiciels et leur mode d’implémentation, l’IoT porte sur les effets de la convergence entre le monde informatique et le monde physique. La protection des solutions IoT implique un approvisionnement sécurisé des appareils, une connexion sécurisée entre ces appareils et le cloud et une protection efficace des données dans le cloud, dans le cadre du traitement et du stockage. Cependant, les appareils avec contraintes de ressources, la répartition géographique des déploiements et un grand nombre d’appareils au sein d’une solution limitent ces fonctionnalités.

Vous pouvez apprendre à gérer la sécurité dans ces domaines en lisant l’article [Sécurisation de l’Internet des objets de bout en bout](../iot-suite/securing-iot-ground-up.md).

L’article aborde les thèmes suivants :

* [Infrastructure sécurisée de bout en bout](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure : une infrastructure IoT sécurisée pour votre entreprise](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Meilleures pratiques
La sécurisation d’une infrastructure IoT implique la mise en œuvre d’une stratégie de sécurité complète et rigoureuse. De la sécurisation des données dans le cloud à la protection de l’intégrité des données transitant sur le réseau Internet public, en passant par l’approvisionnement des appareils en toute sécurité, chaque couche contribue à garantir la sécurité de l’infrastructure globale.

Vous pouvez en savoir plus sur les meilleures pratiques de sécurité de l’Internet des objets en lisant [Internet des objets (IoT) : meilleures pratiques en matière de sécurité](../iot-suite/iot-security-best-practices.md).

L’article aborde les thèmes suivants :

* [Fabricant/intégrateur de matériel IoT](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Développeur de solutions IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [Responsable du déploiement de solutions IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [Opérateur de solutions IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
