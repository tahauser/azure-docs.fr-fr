---
title: "Forum aux questions sur la surveillance à distance Azure IoT Suite | Microsoft Docs"
description: "Forum aux questions sur la solution préconfigurée de surveillance à distance IoT Suite"
services: iot-suite
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 3885e40eb4ddbf61b03a467a71d4dd97d00a9042
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Forum Aux Questions (FAQ) relatives à IoT Suite

Voir aussi le [FAQ](iot-suite-faq.md) général.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Combien coûte l’approvisionnement de la nouvelle solution de surveillance à distance ?

La nouvelle solution préconfigurée offre deux options de déploiement :

* Une option *de base* conçue pour les développeurs à la recherche d’un coût de développement moindre ou de clients souhaitant créer une démonstration ou une preuve de concept.
* Une option *standard* conçue pour les entreprises souhaitant déployer une infrastructure prête à l’emploi dans un environnement de production.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Comment maintenir des coûts faibles pendant le développement de ma solution ?

En plus des deux déploiements différenciés, la nouvelle solution de surveillance à distance dispose d’un paramètre pour activer ou désactiver tous les appareils simulés à la demande. La désactivation de la simulation réduit les données ingérées par la solution et, par conséquent, le coût global.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Quelle est la différence entre les options de déploiement de base et standard ? Comment choisir entre les deux options de déploiement ?

Chaque option de déploiement répond à des besoins différents. Le déploiement de base est conçu pour la prise en main et le développement d’une preuve de concept et de petits projets pilotes. Il fournit une architecture simplifiée avec le minimum de ressources nécessaires et à moindre coût. Le déploiement standard est conçu pour créer et personnaliser une solution prête à l’emploi dans un environnement de production et fournit les éléments nécessaires à cette fin. Pour la fiabilité et l’échelle, les microservices d’application sont générés en tant que conteneurs Docker et déployées à l’aide d’un orchestrateur (Kubernetes par défaut). L’orchestrateur est responsable du déploiement, de la mise à l’échelle et de la gestion de l’application. Vous devez choisir une option en fonction de vos besoins actuels. Vous pouvez utiliser l’une, l’autre ou une combinaison des deux selon la phase du projet.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Comment configurer un mappage dynamique sur le tableau de bord ?

Pour plus d’informations, consultez [Upgrade map key to see devices on a dynamic map (Mettre à niveau une clé de mappage pour voir les appareils sur un mappage dynamique)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux cartes Bing - Transactions internes - Niveau 1 pour les plans d’entreprise dans un abonnement Azure. La solution de surveillance à distance est configurée par défaut avec le plan Transactions internes - Niveau 1. Par conséquent, vous pouvez configurer au maximum deux solutions de surveillance à distance préconfigurées sans modification.

### <a name="next-steps"></a>Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Explorer les fonctionnalités de la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-explore.md)
* [Présentation de la solution préconfigurée de maintenance prédictive](iot-suite-predictive-overview.md)
* [Présentation de la solution préconfigurée d’usine connectée](iot-suite-connected-factory-overview.md)
* [Sécurisation de l’Internet des objets de bout en bout](securing-iot-ground-up.md)