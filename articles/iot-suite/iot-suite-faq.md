---
title: FAQ sur IoT Azure Suite | Microsoft Docs
description: "Forum Aux Questions (FAQ) relatives à IoT Suite"
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
ms.openlocfilehash: c79c90c4f6c28153d4d299015a06a6bc37145081
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Forum Aux Questions (FAQ) relatives à IoT Suite

Consultez également les [questions fréquentes sur la solution préconfigurée d’usine connectée](iot-suite-faq-cf.md) et les [questions fréquentes sur la surveillance à distance](iot-suite-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Où trouver le code source des solutions préconfigurées ?

Le code source est stocké dans les référentiels GitHub suivants :

* [Solution préconfigurée de surveillance à distance (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solution préconfigurée de surveillance à distance (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Solution préconfigurée de maintenance prédictive](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Solution préconfigurée d’usine connectée](https://github.com/Azure/azure-iot-connected-factory)

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>La nouvelle architecture de microservices est-elle disponible pour tous les trois solutions préconfigurées ?

Actuellement, la solution de surveillance à distance utilise l’architecture de microservices, car elle traite le scénario plus large.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quels sont les avantages de la nouvelle architecture basée sur des microservices open source dans la nouvelle mise à jour ?

Ces deux dernières années, l’architecture cloud a considérablement évolué. Les microservices ont émergé comme un modèle efficace pour obtenir mise à l’échelle et flexibilité sans sacrifier la vitesse de développement. Plusieurs services de Microsoft utilisent ce modèle d’architecture en interne et obtiennent de très bons résultats en termes d’extensibilité et de fiabilité. Nous mettons ces apprentissages en pratique pour que nos clients puissent en bénéficier.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>La nouvelle solution préconfigurée est-elle disponible dans la même zone géographique que la solution existante ?

Oui, la nouvelle solution de surveillance à distance est disponible dans les mêmes zones géographiques.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et un clic sur l’option supprimer d’une solution préconfigurée dans azureiotsuite.com ?

* Si vous supprimez la solution préconfigurée dans [azureiotsuite.com](https://www.azureiotsuite.com/), vous supprimez toutes les ressources qui ont été approvisionnées lors de la création de la solution préconfigurée. Si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées.
* Si vous supprimez le groupe de ressources sur le [portail Azure](https://portal.azure.com), vous supprimez uniquement les ressources de ce groupe de ressources. Vous devez également supprimer l’application Azure Active Directory associée à la solution préconfigurée.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Puis-je continuer à tirer parti de mes investissements existants dans Azure IoT Suite ?

Oui. Toute solution qui existe aujourd'hui continue à fonctionner dans votre abonnement Azure et le code source reste disponible dans GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’IoT Hub puis-je configurer dans un abonnement ?

Par défaut, vous pouvez approvisionner [10 instances IoT Hub par abonnement](../azure-subscription-service-limits.md#iot-hub-limits). Vous pouvez créer un [ticket de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter cette limite. Par conséquent, étant donné que chaque solution préconfigurée approvisionne un nouvel IoT Hub, vous ne pouvez configurer que 10 solutions préconfigurées au maximum dans un abonnement.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’Azure Cosmos DB puis-je configurer dans un abonnement ?

Cinquante. Vous pouvez créer un [ticket de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que 50 instances de Cosmos DB par abonnement.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux cartes Bing - Transactions internes - Niveau 1 pour les plans d’entreprise dans un abonnement Azure. La solution de surveillance à distance est configurée par défaut avec le plan Transactions internes - Niveau 1. Par conséquent, vous pouvez configurer au maximum deux solutions de surveillance à distance préconfigurées sans modification.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Puis-je créer une solution préconfigurée si je dispose de Microsoft Azure pour DreamSpark ?

> [!NOTE]
> Microsoft Azure pour DreamSpark s’appelle désormais Microsoft Imagine pour les étudiants.

Actuellement, il est impossible de créer une solution préconfigurée avec un compte [Microsoft Azure pour DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Vous pouvez toutefois créer en quelques minutes un [compte d’essai gratuit pour Azure](https://azure.microsoft.com/free/), que vous pouvez utiliser pour créer une solution préconfigurée.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Puis-je créer une solution préconfigurée si j’ai un abonnement du fournisseur de solutions Cloud (CSP) ?

Actuellement, vous ne pouvez pas créer de solution préconfigurée avec un abonnement de fournisseur de solutions Cloud (CSP). Vous pouvez toutefois créer en quelques minutes un [compte d’essai gratuit pour Azure](https://azure.microsoft.com/free/), que vous pouvez utiliser pour créer une solution préconfigurée.

### <a name="how-do-i-delete-an-aad-tenant"></a>Comment supprimer un client AAS ?

Consultez le billet de blog d’Eric Golpe, [Procédure pas à pas pour la suppression d’un client Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Explorer les fonctionnalités de la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-explore.md)
* [Présentation de la solution préconfigurée de maintenance prédictive](iot-suite-predictive-overview.md)
* [Présentation de la solution préconfigurée d’usine connectée](iot-suite-connected-factory-overview.md)
* [Sécurisation de l’Internet des objets de bout en bout](securing-iot-ground-up.md)