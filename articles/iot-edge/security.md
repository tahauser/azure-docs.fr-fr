---
title: "Sécurité dans Azure IoT Edge | Microsoft Docs"
description: "Sécurité, authentification et autorisation des appareils IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8a5bf1f35fcdd779cf27edeba7dfd5705cbae205
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="securing-azure-iot-edge---preview"></a>Sécurisation d’Azure IoT Edge - préversion

La sécurisation de la périphérie intelligente (Intelligent Edge) est nécessaire si vous voulez avoir une confiance totale dans le fonctionnement d’une solution IoT de bout en bout. Azure IoT Edge est conçu pour une sécurité extensible à différents profils de risque et scénarios de déploiement, et offre la même protection que tous les autres services Azure.

Azure IoT Edge s’exécute sur différents matériels, prend en charge Linux et Windows, et est applicable à différents scénarios de déploiement.  Le risque évalué dépend de nombreuses considérations, notamment la propriété de la solution, la géographie du déploiement, la sensibilité des données, la confidentialité, la verticalité des applications et les conditions de réglementation.  Plutôt que d’offrir des solutions concrètes à des scénarios spécifiques, il est logique de concevoir une infrastructure de sécurité extensible basée sur des principes concrets et conçus pour la mise à l’échelle. 
 
Cet article fournit une vue d’ensemble de l’infrastructure de sécurité. Pour plus d’informations, consultez [Securing the intelligent edge (Sécurisation de la périphérie intelligente)][lnk-edge-blog].

>[!NOTE]
>L’infrastructure de sécurité décrite ci-dessous est en cours d’ajout au produit, et sera disponible lors du lancement de la disponibilité générale d’Azure IoT Edge. Le produit est actuellement en préversion publique, version conçue pour le développement et le prototypage de solutions Edge, et non pour des déploiements de production complets nécessitant toute l’infrastructure de sécurité.   

## <a name="standards"></a>Standards

Les standards facilitent la surveillance et l’implémentation, qui sont des éléments clés de la sécurité.  Une solution de sécurité bien conçue doit se prêter à tout examen et évaluation afin d’encourager l’approbation, et ne doit pas être un obstacle au déploiement.  La conception de l’infrastructure pour sécuriser Azure IoT Edge est basée sur des protocoles de sécurité testés et éprouvés visant à vous familiariser avec elle et à l’utiliser plus facilement. 

## <a name="authentication"></a>Authentification

Une connaissance sans faille des acteurs, appareils et composants qui participent à la réalisation de cette infrastructure par le biais d’une solution IoT de bout en bout est essentielle pour établir un niveau de confiance.  Ces connaissances offrent une responsabilité sécurisée des participants quant à l’activation d’un fondement pour l’acceptation.  Azure IoT Edge obtient ces connaissances grâce à l’authentification.  Le mécanisme principal d’authentification pour la plateforme Azure IoT Edge est l’authentification par certificat.  Ce mécanisme dérive d’un ensemble de normes de l’IETF (Internet Engineering Task Force) régissant l’infrastructure à clé publique (PKiX).     

L’infrastructure de sécurité Azure IoT Edge exige des identités de certificats uniques pour tous les appareils, modules (conteneurs qui encapsulent la logique au sein de l’appareil) et acteurs qui interagissent avec l’appareil Azure IoT Edge, que ce soit physiquement ou par le biais d’une connexion réseau.  Tous les scénarios ou composants ne se prêtent pas nécessairement à l’authentification basée sur certificat, que l’extensibilité de l’infrastructure de sécurité gère de manière sécurisée. 

## <a name="authorization"></a>Autorisation

La capacité à déléguer l’autorité et à contrôler l’accès est cruciale si l’on souhaite bénéficier d’un principe de sécurité fondamental : le principe du moindre privilège.  Les appareils, modules et acteurs peuvent accéder uniquement aux ressources et aux données qui se trouvent dans leur étendue d’autorisation, et uniquement quand l’architecture l’autorise.  Cela signifie que certaines autorisations sont configurables avec des privilèges suffisants, et que d’autres sont appliquées de manière architecturale.  Par exemple, alors qu’un module peut être autorisé par une configuration privilégiée à établir une connexion à Azure IoT Hub, il n’y a aucune raison pour laquelle un module dans un appareil Azure IoT Edge doit accéder au jumeau d’un module dans un autre appareil Azure IoT Edge.  Pour cette raison, ce dernier serait exclu de manière architecturale. 

Parmi les autres schémas d’autorisation figurent les droits de signature de certificat et le contrôle d’accès en fonction du rôle (RBAC).  L’extensibilité de l’infrastructure de sécurité autorise l’adoption d’autres schémas d’autorisation matures. 

## <a name="attestation"></a>Attestation

L’attestation garantit l’intégrité des éléments logiciels.  Elle est importante pour la détection et la prévention des programmes malveillants.  L’infrastructure de sécurité Azure IoT Edge classe l’attestation dans trois catégories principales :

* Attestation statique
* Attestation d’exécution
* Attestation logicielle

### <a name="static-attestation"></a>Attestation statique

L’attestation statique est la vérification de l’intégrité de tous les éléments logiciels, notamment les systèmes d’exploitation, tous les runtimes et les informations de configuration au moment de la mise sous tension de l’appareil.  On emploie souvent le terme de « démarrage sécurisé ».  L’infrastructure de sécurité pour les appareils Azure IoT Edge s’étend aux fournisseurs Silicon et incorpore des fonctionnalités de sécurité intégrées au matériel afin de garantir l’efficacité des processus d’attestation statique. Ces processus incluent le démarrage sécurisé et la mise à niveau sécurisée du microprogramme.  La collaboration étroite avec les fournisseurs Silicon élimine les couches de microprogramme superflues, réduisant ainsi la surface d’attaque. 

### <a name="runtime-attestation"></a>Attestation d’exécution

Une fois qu’un système a terminé un processus de démarrage validé et qu’il est opérationnel, les systèmes sécurisés bien conçus détectent les tentatives d’injection de logiciels malveillants par le biais des interfaces et des ports système, et ils prennent des contre-mesures appropriées.  Les acteurs malveillants en possession d’appareils de périphérie intelligente peuvent injecter du contenu malveillant par des moyens autres que les interfaces des appareils, comme la falsification et les attaques par canal latéral.   Ce contenu malveillant, qui peut un programme malveillant ou des modifications de configuration non autorisées, n’est normalement pas détecté par le processus de démarrage sécurisé, car il se produit après le processus de démarrage.  Les contre-mesures proposées ou appliquées par le matériel de l’appareil contribuent grandement à atténuer ces menaces.  L’infrastructure de sécurité d’Azure IoT Edge appelle explicitement des extensions afin de combattre les menaces au moment de l’exécution.     

### <a name="software-attestation"></a>Attestation logicielle

Tous les systèmes sains, notamment les systèmes de périphérie intelligente, doivent pouvoir recevoir des correctifs et des mises à niveau.  La sécurité est importante pour les processus de mise à jour, car ils peuvent constituer des vecteurs de menaces potentiels.  L’infrastructure de sécurité d’Azure IoT Edge applique les mises à jour par le biais de packages mesurés et signés afin de garantir l’intégrité et l’authentification de la source des packages.  Cela s’applique à tous les systèmes d’exploitation et aux éléments logiciels des applications. 

## <a name="hardware-root-of-trust"></a>Racine matérielle de confiance

Pour de nombreux déploiements d’appareils de périphérie intelligente, en particulier ceux qui sont déployés dans des endroits où des acteurs malveillants potentiels ont un accès physique à l’appareil, la sécurité offerte par le matériel de l’appareil est la dernière défense pour la protection.  Pour cette raison, le fait d’ancrer l’approbation dans du matériel inviolable est essentiel pour de tels déploiements.  L’infrastructure de sécurité d’Azure IoT Edge implique la collaboration des fournisseurs de matériel de confiance pour offrir différentes versions de racine matérielle de confiance afin de prendre en charge différents scénarios de déploiement et profils de risque. Cela inclut le strict respect des normes de protocole de sécurité communes telles que le Trusted Platform Module (ISO/IEC 11889) et le Device Identifier Composition Engine (DICE) du Trusted Computing Group.  Cela comprend également des technologies intégrées et sécurisées comme TrustZones et Software Guard Extensions (SGX). 

## <a name="certification"></a>Certification

Pour aider les clients à prendre des décisions informées lors de l’approvisionnement des appareils Azure IoT Edge pour leur déploiement, l’infrastructure Azure IoT Edge inclut des exigences de certification.  À la base de ces exigences figurent des certifications relatives aux revendications de sécurité et des certifications relatives à la validation de l’implémentation de sécurité.  Par exemple, une certification de revendication de sécurité signalerait que l’appareil IoT Edge utilise du matériel sécurisé connu comme résistant aux attaques de démarrage. Un certificat de validation signalerait que le matériel sécurisé a été correctement implémenté pour offrir cette fonctionnalité dans l’appareil.  Conformément au principe de simplicité, l’infrastructure vise à réduire au plus la charge de certification.   

## <a name="extensibility"></a>Extensibilité

L’extensibilité est un aspect de première classe de l’infrastructure de sécurité Azure IoT Edge.  La technologie IoT pilotant différents types de transformations professionnelles, il est évident que la sécurité doit évoluer en toute transparence afin de gérer les scénarios émergents.  L’infrastructure de sécurité Azure IoT Edge repose sur une base solide et extensible dans différentes dimensions de façon à inclure ce qui suit : 

* Services de sécurité internes tels que le service Device Provisioning pour Azure IoT Hub.
* Services tiers tels que des services de sécurité gérés pour différents domaines d’application (comme industrie ou soins de santé) ou focus technologiques (par exemple la surveillance de la sécurité sur les réseaux à maillage ou les services d’attestation matérielle silicone) par le biais d’un réseau complet de partenaires.
* Systèmes hérités devant inclure la récupération avec des stratégies de sécurité de substitution, comme l’utilisation d’une technologie sécurisée autre que les certificats pour la gestion de l’authentification et de l’identité.
* Matériel sécurisé pour l’adoption fluide de nouvelles technologies de matériel sécurisé et les contributions des partenaires Silicon.

Il ne s’agit que de quelques exemples de dimensions pour l’extensibilité, et l’infrastructure de sécurité Azure IoT Edge est conçue pour être sécurisée dès le départ afin de prendre en charge cette extensibilité. 

Au final, la réussite de la sécurisation de la périphérie intelligente résulte des contributions d’une communauté ouverte motivée par l’intérêt commun envers la sécurisation d’IoT.  Ces contributions peuvent se présenter sous la forme de technologies sécurisées ou de services.  L’infrastructure de sécurité Azure IoT Edge offre une base solide pour la sécurité, qui est extensible afin de fournir une couverture maximale, et permet d’offrir le même niveau de confiance et d’intégrité dans la périphérie intelligente que le cloud Azure.  

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la façon dont Azure IoT Edge [sécurise la périphérie intelligente][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 