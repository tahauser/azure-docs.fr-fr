---
title: "Utiliser le portail Azure pour créer un IoT Hub | Microsoft Azure"
description: "Comment créer, gérer et supprimer des IoT Hubs Azure via le portail Azure. Inclut des informations sur les niveaux de tarification, l’évolutivité, la sécurité et la configuration de la messagerie."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.openlocfilehash: ac1a52355ffa5354bebe3b98fdb75783bcd57697
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Création d’un IoT Hub à l’aide du portail Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Cet article aborde les points suivants :

* Comment trouver le service IoT Hub dans le portail Azure.
* Comment créer et gérer des hubs IoT.

## <a name="where-to-find-the-iot-hub-service"></a>Où trouver le service IoT Hub

Vous pouvez trouver le service IoT Hub aux emplacements suivants sur le portail :

* Choisissez **+ Nouveau**, puis **Internet des objets**.
* Sur le marketplace, choisissez **Internet des objets**.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Vous pouvez créer un hub IoT à l'aide des méthodes suivantes :

* L’option **+ Nouveau** ouvre le panneau indiqué dans la capture d’écran suivante. Les étapes de création du hub IoT via cette méthode et via le marketplace sont identiques.
* Sur le marketplace, choisissez **Créer** pour ouvrir le panneau indiqué dans la capture d’écran suivante.

Les sections suivantes décrivent les différentes étapes permettant de créer un hub IoT :

### <a name="choose-the-name-of-the-iot-hub"></a>Choisir le nom du hub IoT

Pour créer un IoT Hub, vous devez le nommer. Le nom doit être unique parmi tous les hubs IoT.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Choisir le niveau tarifaire.

Vous pouvez choisir entre quatre niveaux : **Gratuit**, **Standard S1**, **Standard S2** et**Standard S3**. Le niveau gratuit permet la connexion de seulement 500 appareils au IoT Hub, avec jusqu’à 8 000 messages par jour.

**Standard S1** : utilisez l’édition S1 pour les solutions IoT avec un grand nombre d’appareils qui génèrent chacun de petites quantités de données. Chaque unité de l’édition S1 permet de transmettre au maximum 400 000 messages par jour sur l’ensemble des appareils connectés.

**Standard S2** : utilisez l’édition S2 pour les solutions IoT dans lesquelles les appareils génèrent de grandes quantités de données. Chaque unité de l’édition S2 permet de transmettre au maximum 6 millions de messages par jour sur l’ensemble des appareils connectés.

**Standard S3** : utilisez l’édition S3 pour les solutions IoT qui génèrent de grandes quantités de données. Chaque unité de l’édition S3 permet de transmettre au maximum 300 millions de messages par jour sur l’ensemble des appareils connectés.

![][4]

> [!NOTE]
> IoT Hub ne permet qu’un hub gratuit par abonnement Azure.

### <a name="iot-hub-units"></a>Unités de hub IoT

Le nombre de messages autorisés par unité par jour dépend du niveau de tarification de votre concentrateur. Par exemple, si vous souhaitez qu’IoT Hub prenne en charge l’arrivée de 700 000 messages, vous choisissez deux unités de niveau S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Les partitions appareil-à-cloud et groupe de ressources

Vous pouvez modifier le nombre de partitions pour un hub IoT. Le nombre de partitions par défaut est 4 ; vous pouvez choisir un nombre différent dans la liste déroulante.

Vous n’avez pas besoin de créer explicitement un groupe de ressources vide. Lorsque vous créez une ressource, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant.

![][5]

### <a name="choose-subscription"></a>Choisir un abonnement

Azure IoT Hub montre automatiquement la liste des abonnements Azure auquel le compte utilisateur est lié. Vous pouvez choisir l’abonnement Azure auquel associer le hub IoT.

### <a name="choose-the-location"></a>Choisir l’emplacement

L’option d’emplacement fournit une liste des régions dans lesquelles IoT Hub est disponible.

### <a name="create-the-iot-hub"></a>Créer le hub IoT

Lorsque toutes les étapes précédentes sont terminées, vous pouvez créer le hub IoT. Cliquez sur **Créer** pour démarrer le processus principal afin de créer et de déployer le hub IoT avec les options sélectionnées.

La création du hub IoT peut prendre quelques minutes, car le déploiement principal doit s’effectuer sur les serveurs d’emplacement appropriés.

## <a name="change-the-settings-of-the-iot-hub"></a>Modifier les paramètres du hub IoT

Vous pouvez modifier les paramètres d’un hub IoT existant après sa création, dans le panneau IoT Hub.

![][8]

**Stratégies d’accès partagé** : ces stratégies définissent les autorisations pour que les appareils et services se connectent au IoT Hub. Vous pouvez accéder à ces stratégies en cliquant sur **Stratégies d’accès partagé** sous **Général**. Dans ce panneau, vous pouvez soit modifier les stratégies existantes, soit ajouter une nouvelle stratégie.

### <a name="create-a-policy"></a>Création d’une stratégie

* Cliquez sur **Ajouter** pour ouvrir un panneau. Ici, vous pouvez ouvrir un panneau dans lequel vous pouvez saisir le nouveau nom de stratégie, et les autorisations que vous voulez associer à la stratégie, comme illustré dans la figure suivante :

    Il existe plusieurs autorisations qui peuvent être associées à ces stratégies partagées. Les stratégies **Lecture de registre** et **Écriture de registre** accordent les autorisations en lecture et en écriture au registre d’identité. Le choix de l'option en écriture active automatiquement l'option en lecture.

    La stratégie de **connexion de service** autorise l’accès aux points de terminaison de service tels que la **réception appareil-à-cloud**. La stratégie de **connexion d’appareil** accorde des autorisations pour envoyer et recevoir des messages à l’aide des points de terminaison côté appareil de IoT Hub.

* Cliquez sur **Créer** pour ajouter la stratégie créée à la liste existante.

![][10]

## <a name="endpoints"></a>Endpoints

Pour afficher la liste des points de terminaison associés à l’IoT Hub que vous essayez de modifier, cliquez sur **Points de terminaison**. Il existe deux types de points de terminaison : les points de terminaison intégrées à IoT Hub, et ceux que vous avez ajoutés à IoT Hub après sa création.

![][11]

### <a name="built-in-endpoints"></a>Points de terminaison intégrés

Il existe deux catégories de points de terminaison intégrés : les **commentaires de messages cloud-à-appareil** et les **événements**.

* Paramètres **Commentaire de messages cloud-à-appareil** : ce paramètre contient deux configurations secondaires : **Cloud vers appareil TTL** (durée de vie) et **Durée de rétention** (en heures) pour les messages. Lorsque vous créez une instance IoT Hub, ces deux paramètres sont paramétrés par défaut sur une heure. Pour les ajuster, utilisez les curseurs ou saisissez les valeurs de votre choix.
* Paramètres **Événements** : ils présentent plusieurs configurations secondaires, qui sont pour certaines en lecture seule. La liste ci-dessous décrit ces paramètres :

  * **Partitions** : une valeur par défaut est définie lors de la création de l’instance IoT Hub. Vous pouvez ici modifier le nombre de partitions.

  * **Nom compatible et point de terminaison Concentrateur d'événements** : lorsque le concentrateur IoT est créé, un Concentrateur d'événements est créé en interne, et vous pouvez nécessiter l'accès dans certaines circonstances. S’il est impossible de personnaliser le nom compatible et les valeurs de points de terminaison du concentrateur d’événements, vous pouvez copier ces valeurs en cliquant sur **Copier**.

  * **Durée de rétention** : cette valeur est définie par défaut sur un jour, mais pouvez la modifier à l’aide de la liste déroulante. Cette valeur est exprimée en jours pour le paramètre d’appareil-à-cloud.

  * **Groupes de consommateurs** : les groupes de consommateurs permettent à plusieurs lecteurs de lire les messages indépendamment à partir du hub IoT. Chaque hub IoT est créé avec un groupe de consommateurs par défaut. Toutefois, vous pouvez ajouter ou supprimer des groupes de consommateurs de vos instances IoT Hub via ce paramètre.

  > [!NOTE]
  > Le groupe de consommateurs par défaut ne peut être ni modifié ni supprimé.

### <a name="custom-endpoints"></a>Points de terminaison personnalisés

Vous pouvez ajouter des points de terminaison personnalisés sur votre IoT Hub par le biais de ce portail. En haut du panneau **Points de terminaison**, cliquez sur **Ajouter** afin d’ouvrir le panneau **Ajouter un point de terminaison**. Saisissez les informations requises, puis cliquez sur **OK**. Votre point de terminaison personnalisé est désormais répertorié dans le panneau principal **Points de terminaison**.

![][13]

Pour en savoir plus sur les points de terminaison personnalisés, consultez [Référence - Points de terminaison IoT Hub][lnk-devguide-endpoints].

## <a name="routes"></a>Itinéraires

Cliquez sur **Itinéraires** pour gérer la façon dont IoT Hub distribue vos messages cloud-à-appareil.

![][14]

Pour ajouter des itinéraires à votre IoT Hub, cliquez sur **Ajouter** en haut du panneau **Itinéraires*** , saisissez les informations requises, puis cliquez sur **OK**. Dès lors, votre itinéraire est répertorié dans le panneau principal **Itinéraires**. Pour modifier un itinéraire, cliquez dessus dans la liste des itinéraires. Pour activer un itinéraire, cliquez dessus dans la liste des itinéraires, puis définissez le paramètre **Activer/Désactiver** sur **Désactiver**. Pour enregistrer la modification, cliquez sur **OK** en bas du panneau.

![][15]

## <a name="pricing-and-scale"></a>Tarification et mise à l'échelle

La tarification d'un concentrateur IoT existant peut être modifiée via les paramètres de **tarification** avec les exceptions suivantes :

* Dans l’implémentation actuelle, un hub IoT avec une référence gratuite ne peut pas changer de niveau pour une référence payante, ou vice-versa.
* Il ne peut y avoir qu’un niveau gratuit de IoT Hub par abonnement Azure.

![][12]

Vous ne pouvez changer de niveau que lorsque le nombre de messages envoyés dans la journée dépasse le quota défini pour le niveau inférieur. Par exemple, si le nombre de messages par jour est supérieur à 400 000, le niveau correspondant au IoT Hub peut être modifié. En revanche, si vous modifiez le niveau S1, l’IoT Hub est limité pour ce jour.

## <a name="delete-the-iot-hub"></a>Supprimez IoT Hub

Vous pouvez accéder au concentrateur IoT Hub en cliquant sur **Parcourir**, puis en choisissant le concentrateur à supprimer. Cliquez sur le bouton **Supprimer** situé sous le nom de l’IoT Hub pour supprimer celui-ci.

## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gérer en bloc des appareils IoT][lnk-bulk]
* [Métriques d’IoT Hub][lnk-metrics]
* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]
* [Sécuriser votre solution IoT de bout en bout][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
