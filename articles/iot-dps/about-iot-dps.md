---
title: "Vue d’ensemble du service Azure IoT Hub Device Provisioning | Microsoft Docs"
description: "Décrit l’approvisionnement d’appareils dans Azure avec le service Device Provisioning et IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 12/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 108ae4a66d2c13906cd0892b6c8ee5b20c3ca605
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Approvisionner des appareils avec le service Azure IoT Hub Device Provisioning
Microsoft Azure fournit un ensemble complet de services cloud publics intégrés qui répondent à tous les besoins de votre solution IoT. Le service IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui permet d’effectuer un approvisionnement sans contact juste-à-temps sur le hub IoT approprié, sans intervention humaine. Les clients peuvent ainsi approvisionner des millions d’appareils de façon sécurisée et scalable.

## <a name="when-to-use-device-provisioning-service"></a>Quand utiliser le service Device Provisioning
Il existe de nombreux scénarios d’approvisionnement dans lesquels le service Device Provisioning est un excellent choix pour la connexion et la configuration d’appareils sur IoT Hub, comme :

* Approvisionnement sans contact sur une solution IoT unique sans coder en dur en usine les informations de connexion IoT Hub (configuration initiale)
* Équilibrage de la charge des appareils sur plusieurs hubs
* Connexion d’appareils à la solution IoT de leur propriétaire en fonction des données de transaction de vente (architecture multilocataire)
* Connexion d’appareils à une solution IoT particulière en fonction des cas d’usage (isolement de la solution)
* Connexion d’un appareil au hub IoT avec la latence la plus faible (géopartitionnement)
* Réapprovisionnement basé sur une modification de l’appareil
* Restaurer les clés utilisées par l’appareil pour se connecter à IoT Hub (en cas de non-utilisation de certificats X.509 pour la connexion)

## <a name="behind-the-scenes"></a>Dans les coulisses
Tous les scénarios listés dans la section précédente peuvent être réalisés à l’aide du service d’approvisionnement pour lesquels l’approvisionnement est effectué sans contact en utilisant le même flux. Un grand nombre des étapes manuelles généralement impliquées dans l’approvisionnement sont automatisées avec le service Device Provisioning pour réduire le temps de déploiement des appareils IoT et diminuer le risque d’erreur manuelle. Voici une description de ce qui se passe dans les coulisses de l’approvisionnement d’un appareil. La première étape est manuelle, toutes les suivantes sont automatiques.

![Flux d’approvisionnement de base](./media/about-iot-dps/dps-provisioning-flow.png)

1. Le fabricant de l’appareil ajoute les informations d’enregistrement de l’appareil à la liste d’inscriptions dans le portail Azure.
2. L’appareil contacte le point de terminaison du service d’approvisionnement défini en usine. L’appareil transmet au service d’approvisionnement ses informations d’identification pour prouver son identité.
3. Le service d’approvisionnement valide l’identité de l’appareil en comparant l’ID d’enregistrement et la clé à l’entrée de liste d’inscriptions à l’aide d’une stimulation de nonces ([Module de plateforme sécurisée](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) ou d’une vérification X.509 standard (X.509).
4. Le service d’approvisionnement enregistre l’appareil sur un hub IoT et renseigne l’[état jumeau souhaité](../iot-hub/iot-hub-devguide-device-twins.md).
5. Le hub IoT retourne les informations d’ID d’appareil au service d’approvisionnement.
6. Le service d’approvisionnement retourne les informations de connexion du hub IoT à l’appareil. L’appareil peut maintenant commencer à envoyer des données directement au hub IoT.
7. L’appareil se connecte au hub IoT.
8. L’appareil obtient l’état souhaité de son jumeau dans le hub IoT.

## <a name="provisioning-process"></a>Processus de mise en service
Le processus de déploiement d’un appareil, dans lequel le service Device Provisioning joue un rôle qui peut être effectué indépendamment, se déroule en deux étapes :

* L’**étape de fabrication** qui permet de créer et préparer l’appareil en usine.
* L’**étape de configuration du cloud** qui configure le service Device Provisioning pour l’approvisionnement automatique.

Ces deux étapes s’intègrent parfaitement aux processus de fabrication et de déploiement existants. Le service Device Provisioning simplifie même certains processus de déploiement qui impliquent de nombreuses tâches manuelles pour obtenir les informations de connexion sur l’appareil.

### <a name="manufacturing-step"></a>Étape de fabrication
Cette étape englobe toutes les tâches de la ligne de fabrication. Les rôles impliqués dans cette étape sont notamment le concepteur de silicium, le fabricant de silicium, l’intégrateur et/ou le fabricant final de l’appareil. Cette étape concerne la création du matériel.

Le service Device Provisioning ne constitue pas une nouvelle étape dans le processus de fabrication, il est plutôt lié à l’étape existante d’installation du logiciel initial et (dans l’idéal) du module HSM sur l’appareil. Dans cette étape, plutôt que de créer un ID d’appareil, l’appareil est simplement programmé avec les informations du service d’approvisionnement pour qu’il puisse l’appeler afin d’obtenir son attribution de solution IoT/d’informations de connexion quand il est allumé.

Dans cette étape également, le fabricant fournit à l’opérateur ou au système de déploiement de l’appareil les informations de clé d’identification. Il peut s’agir simplement de confirmer que tous les appareils ont un certificat X.509 généré par une autorité de certification fournie par l’opérateur ou le responsable de déploiement de l’appareil, ou bien d’extraire la partie publique d’une paire de clés de type EK TPM sur chaque appareil TPM. Ces services sont proposés par de nombreux fabricants de silicium aujourd'hui.

### <a name="cloud-setup-step"></a>Étape de configuration du cloud
Cette étape configure le cloud pour l’approvisionnement automatique approprié. Généralement, deux types d’utilisateurs sont impliqués dans l’étape de configuration du cloud : une personne qui sait comment les appareils doivent être configurés initialement (un opérateur d’appareil) et une autre personne qui sait comment les appareils doivent être répartis dans les hubs IoT (un opérateur de solution).

L’approvisionnement doit passer par une configuration initiale unique, tâche généralement gérée par l’opérateur de solution. Une fois le service d’approvisionnement configuré, il n’a pas besoin d’être modifié, sauf si le cas d’utilisation change.

Une fois que le service a été configuré pour la configuration automatique, il doit être préparé pour inscrire les appareils. Cette étape est effectuée par l’opérateur d’appareil, qui connaît la configuration souhaitée du ou des appareils et doit vérifier que le service d’approvisionnement peut correctement attester de l’identité de l’appareil au moment de rechercher son hub IoT. L’opérateur d’appareil récupère les informations de la clé d’identification auprès du fabricant et les ajoute à la liste d’inscriptions. La liste d’inscriptions peut être mise à jour par la suite, à mesure que de nouvelles entrées sont ajoutées ou que des entrées existantes sont mises à jour avec les dernières informations des appareils.

## <a name="registration-and-provisioning"></a>Enregistrement et approvisionnement
L’*approvisionnement* désigne différentes choses, selon le secteur dans lequel est utilisé le terme. Dans le contexte de l’approvisionnement d’appareils IoT dans une solution cloud, il désigne un processus en deux parties :

1. La première partie établit la connexion initiale entre l’appareil et la solution IoT en enregistrant l’appareil.
2. La deuxième partie applique la configuration appropriée à l’appareil selon les exigences spécifiques de la solution dans laquelle il est enregistré.

C’est seulement une fois que ces deux étapes sont terminées que l’on peut dire que l’appareil est complètement approvisionné. Certains services cloud fournissent uniquement la première étape du processus d’approvisionnement (l’enregistrement des appareils au point de terminaison de la solution IoT) et ne fournissent pas la configuration initiale. Le service Device Provisioning automatise les deux étapes pour fournir une expérience d’approvisionnement fluide pour l’appareil.

## <a name="features-of-the-device-provisioning-service"></a>Fonctionnalités du service Device Provisioning
Le service Device Provisioning propose de nombreuses fonctionnalités qui le rendent idéal pour l’approvisionnement d’appareils.

* Prise en charge de l’**attestation sécurisée** pour les identités X.509 et TPM.
* **Liste d’inscriptions** contenant l’enregistrement complet des appareils/groupes d’appareils susceptibles d’être enregistrés à un moment donné. La liste d’inscriptions contient des informations sur la configuration souhaitée de l’appareil après son enregistrement et peut être mise à jour à tout moment.
* **Plusieurs stratégies d’allocation** pour contrôler la façon dont le service Device Provisioning affecte des appareils aux hubs IoT en fonction de vos scénarios.
* Des **journaux de surveillance et de diagnostic** pour vérifier que tout fonctionne correctement.
* Une **prise en charge multihub** qui permet au service Device Provisioning d’affecter des appareils à plusieurs hubs IoT. Le service Device Provisioning peut communiquer avec les hubs de plusieurs abonnements Azure.
* Une **prise en charge entre régions** qui permet au service Device Provisioning d’affecter des appareils à des IoT Hubs d’autres régions.

Vous trouverez d’autres d’informations sur les concepts et les fonctionnalités impliqués dans l’approvisionnement d’appareils dans les [concepts d’appareil](concepts-device.md), les [concepts de service](concepts-service.md) et les [concepts de sécurité](concepts-security.md).

## <a name="cross-platform-support"></a>Prise en charge multiplateforme
Le service Device Provisioning, comme tous les services Azure IoT, fonctionne sur plusieurs plateformes avec une variété de systèmes d’exploitation. Azure propose des kits de développement logiciel open source dans différentes [langues](https://github.com/Azure/azure-iot-sdks), ce qui facilite la connexion des appareils et la gestion du service. Le service Device Provisioning prend en charge les protocoles suivants de connexion des appareils :

* HTTPS
* AMQP
* AMQP sur WebSockets
* MQTT
* MQTT sur WebSockets

Le service Device Provisioning prend uniquement en charge les connexions HTTPS pour les opérations de service.

## <a name="regions"></a>Régions
Le service Device Provisioning est disponible dans de nombreuses régions. Nous tenons à jour une liste des régions existantes et récemment annoncées pour tous les services, dans [Régions Azure](https://azure.microsoft.com/regions/). Pour connaître la disponibilité du service Device Provisioning, accédez à la page [Statut Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> Le service Device Provisioning est global, il n’est pas associé à une zone géographique précise. Toutefois, vous devez spécifier une région dans laquelle les métadonnées associées à votre profil du service Device Provisioning seront hébergées.

## <a name="availability"></a>Disponibilité
Nous mettons en place une disponibilité à 99,9 % du contrat de niveau de service (SLA) pour Device Provisioning. Vous pouvez [lire le SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/) explique la disponibilité garantie d’Azure dans son ensemble.

## <a name="quotas"></a>Quotas
Chaque abonnement Azure a des limites de quota par défaut qui peuvent impacter l’étendue de votre solution IoT. La limite actuelle par abonnement est de 10 services Device Provisioning par abonnement.

Pour plus d’informations sur les limites de quota :

* [Limites du service d’abonnement Azure](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Composants Azure connexes
Le service Device Provisioning automatise l’approvisionnement des appareils avec Azure IoT Hub. Découvrez plus d’informations sur [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant une vue d’ensemble de l’approvisionnement des appareils IoT dans Azure. L’étape suivante consiste à tester un scénario IoT de bout en bout.
> [!div class="nextstepaction"]
> [Configurer le service IoT Hub Device Provisioning avec le portail Azure](quick-setup-auto-provision.md)
> [Créer et approvisionner un appareil simulé](quick-create-simulated-device.md)
> [Configurer un appareil pour l’approvisionnement](tutorial-set-up-device.md)
