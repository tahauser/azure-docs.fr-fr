---
title: "Gestion des appareils dans la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment gérer les appareils connectés à la solution de surveillance à distance."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 66005b78a368c15a463844b3f098eac9fd64f621
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="manage-and-configure-your-devices"></a>Gérer et configurer vos appareils

Ce didacticiel montre les fonctionnalités de gestion des appareils de la solution de surveillance à distance. Pour présenter ces fonctionnalités, le didacticiel utilise un scénario dans l’application Contoso IoT.

Contoso a commandé de nouvelles machines visant à développer l’une de ses usines pour accroître la production. En attendant la livraison des nouvelles machines, vous souhaitez exécuter une simulation pour vérifier le comportement de votre solution. En tant qu’opérateur, vous souhaitez gérer et configurer les appareils dans la solution de surveillance à distance.

Pour fournir un moyen extensible de gérer et de configurer des appareils, la solution de surveillance à distance utilise des fonctionnalités IoT Hub telles que les [travaux](../iot-hub/iot-hub-devguide-jobs.md) et les [méthodes directes](../iot-hub/iot-hub-devguide-direct-methods.md). Pour savoir comment un développeur d’appareil implémente des méthodes sur un appareil physique, consultez [Personnaliser la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-customize.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Provisionner un appareil simulé
> * Tester l’appareil simulé
> * Appeler des méthodes d’appareil à partir de la solution
> * Reconfigurer un appareil

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez terminer le didacticiel [Déployer la solution de surveillance à distance préconfigurée](iot-suite-remote-monitoring-deploy.md).

## <a name="add-a-simulated-device"></a>Utiliser un appareil simulé

Accédez à la page **Appareils** dans la solution, puis choisissez **+ Nouvel appareil**. Dans le panneau **Nouvel appareil**, choisissez **Simulé** :

![Provisionner un appareil simulé](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

Laissez le nombre d’appareils à provisionner défini sur **1**. Choisissez l’option **Faulty Engine (Moteur défaillant)** pour le modèle de l’appareil, puis choisissez **Appliquer** pour créer l’appareil simulé :

![Provisionner un appareil de moteur simulé](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

Pour découvrir comment provisionner un appareil *physique*, consultez [Connecter votre appareil à la solution préconfigurée de surveillance à distance](iot-suite-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Tester l’appareil simulé

Pour afficher les détails de votre nouvel appareil simulé, sélectionnez-le dans la liste des appareils dans la page **Appareils**. Les informations sur votre appareil s’affichent dans le panneau **Détail de l’appareil** :

![Afficher le nouvel appareil de moteur simulé](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

Dans **Détail de l’appareil**, vérifiez que votre nouvel appareil envoie des données de télémétrie. Pour afficher les différents flux de données de télémétrie issus de votre appareil, choisissez un nom de télémétrie tel que **vibration** :

![Sélectionner un flux de données de télémétrie à afficher](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

Le panneau **Détail de l’appareil** affiche d’autres informations sur l’appareil, telles que les valeurs d’étiquette, les méthodes qu’il prend en charge et les propriétés qu’il indique.

Pour afficher les diagnostics détaillés, faites défiler jusqu’à **Diagnostics**.

## <a name="act-on-a-device"></a>Agir sur un appareil

Pour agir sur un ou plusieurs appareils, sélectionnez-les dans la liste, puis choisissez **Planifier**. Le modèle d’appareil **Moteur** spécifie quatre méthodes devant être prises en charge par un appareil :

![Méthodes de moteur](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

Choisissez **Redémarrer**, définissez le nom du travail sur **RestartEngine**, puis choisissez **Appliquer** :

![Planifier la méthode de redémarrage](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

Pour suivre l’état du travail dans la page **Maintenance**, choisissez **Travaux** :

![Travail de surveillance des planifications](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Méthodes dans d’autres appareils

Quand vous explorez les différents types d’appareil simulé, vous voyez que les autres types d’appareil prennent en charge différentes méthodes. Dans un déploiement avec des appareils physiques, le modèle d’appareil spécifie les méthodes que l’appareil doit prendre en charge. En règle générale, il appartient au développeur de l’appareil de développer le code qui définit la façon dont l’appareil réagit à un appel de méthode.

Pour planifier une méthode à exécuter sur plusieurs appareils, vous pouvez sélectionner plusieurs appareils dans la liste de la page **Appareils**. Le volet **Planification** affiche les types de méthodes communes à tous les appareils sélectionnés.

## <a name="reconfigure-a-device"></a>Reconfigurer un appareil

Pour changer la configuration d’un appareil, sélectionnez-le dans la liste des appareils de la page **Appareils**, puis choisissez **Reconfigurer**. Le panneau Reconfigurer affiche les valeurs de propriété de l’appareil sélectionné que vous pouvez changer :

![Reconfigurer un appareil](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

Pour apporter une modification, ajoutez un nom pour le travail, mettez à jour les valeurs de propriété et choisissez **Appliquer** :

![Mettre à jour une valeur de propriété d’appareil](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

Pour suivre l’état du travail sur la page **Maintenance**, sélectionnez **Travaux**.

## <a name="next-steps"></a>étapes suivantes

Ce didacticiel vous a montré comment effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Provisionner un appareil simulé
> * Tester l’appareil simulé
> * Appeler des méthodes d’appareil à partir de la solution
> * Reconfigurer un appareil

La gestion de vos appareils n’ayant plus de secrets pour vous, nous vous suggérons de découvrir les opérations suivantes :

* [Dépanner et corriger les problèmes d’un appareil](iot-suite-remote-monitoring-maintain.md)
* [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md)
* [Connecter votre appareil à la solution préconfigurée de surveillance à distance](iot-suite-connecting-devices-node.md)

<!-- Next tutorials in the sequence -->