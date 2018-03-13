---
title: "Visualisation en temps réel de données de capteur à partir de votre Azure IoT Hub – Web Apps | Microsoft Docs"
description: "La fonctionnalité Web Apps de Microsoft Azure App Service permet de visualiser les données de température et d’humidité collectées à partir du capteur, et envoyées à votre hub Iot."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "visualisation de données en temps réel, visualisation de données en direct, visualisation de données de capteurs"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.openlocfilehash: 54a2defd6bfe2396e24584c686698d3215893cfd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualiser en temps réel des données de capteur à partir de votre Azure IoT Hub en utilisant la fonctionnalité Web Apps d’Azure App Service

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Ce didacticiel explique comment visualiser en temps réel des données de capteur que votre IoT Hub reçoit en exécutant une application web qui est hébergée sur une application web. Si vous souhaitez essayer de visualiser les données dans votre IoT Hub avec Power BI, voir [Visualiser des données de capteur en temps réel depuis Azure IoT Hub à l’aide de Power BI](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Procédure

- Créez une application web dans le portail Azure.
- Préparation de votre instance IoT Hub pour l’accès aux données via l’ajout d’un groupe de consommateurs.
- Configurez l’application web pour lire des données de capteur à partir de votre IoT Hub.
- Chargez une application web qui sera hébergée par l’application web.
- Ouvrez l’application web pour afficher des données de température et d’humidité en temps réel à partir de votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md), ce qui couvre les exigences suivantes :
  - Un abonnement Azure actif
  - Un IoT Hub associé à votre abonnement
  - Une application cliente qui envoie des messages à votre IoT Hub
- [Télécharger Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Créer une application web

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Web + Mobile** > **Application web**.
2. Saisissez un nom de tâche unique, vérifiez l’abonnement, spécifiez un groupe de ressources et un emplacement, sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.

   Nous vous recommandons de sélectionner le même emplacement que celui de votre groupe de ressources. Cela accélère le traitement et réduit le coût de transfert de données.

   ![Créer une application web](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurer l’application web pour lire des données à partir de votre IoT Hub

1. Ouvrez l’application web que vous venez d’approvisionner.
2. Cliquez sur **Paramètres de l’application**, puis, sous **Paramètres de l’application**, ajoutez les paires clé/valeur suivantes :

   | Clé                                   | Valeur                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtenu à partir de iothub-explorer                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | Le nom du groupe de consommateurs que vous ajoutez à votre IoT hub  |

   ![Ajouter des paramètres à l’application web avec des paires clé/valeur](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Cliquez sur **Paramètres de l’Application**. Sous **Paramètres généraux**, activez l’option **Web sockets**, puis cliquez sur **Enregistrer**.

   ![Activer l’option Web sockets](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Chargez une application web qui sera hébergée par l’application web

Sur GitHub, nous avons mis à disposition une application web qui affiche des données de capteur en temps réel à partir de votre IoT Hub. Il vous suffit de configurer l’application web pour utiliser un référentiel Git, télécharger l’application web à partir de GitHub, puis la charger sur Azure pour l’application web à héberger.

1. Dans l’application web, cliquez sur **Options de déploiement** > **Choisir la source** > **Référentiel Git local**, puis cliquez sur **OK**.

   ![Configurer votre déploiement d’application web pour utiliser le référentiel Git local](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Cliquez sur **Informations d’identification du déploiement**, créez un nom d’utilisateur et un mot de passe pour vous connecter au référentiel Git dans Azure, puis cliquez sur **Enregistrer**.

3. Cliquez sur **Vue d’ensemble**, puis notez de la valeur de l’**URL de clone Git**.

   ![Obtenir l’URL de clone Git de votre application web](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Ouvrez une commande ou une fenêtre de terminal sur votre ordinateur local.

5. Télécharger l’application web à partir de GitHub, puis chargez-la sur Azure pour l’application web à héberger. Pour faire, exécutez les commandes suivantes :

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<URL de clone Git\> est l’URL du référentiel Git que l’on trouve sur la page **Vue d’ensemble** de l’application web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Ouvrez l’application web pour afficher des données de température et d’humidité en temps réel à partir de votre IoT Hub

Sur la page **Vue d’ensemble** de votre application web, cliquez sur l’URL pour ouvrir l’application web.

![Obtenir l’URL de votre application web](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Vous devriez alors voir les données de température et d’humidité en temps réel à partir de votre IoT Hub.

![Page d’application web affichant l’humidité et la température en temps réel](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Vérifiez que l’exemple d’application s’exécute correctement sur votre appareil. Si ce n’est pas le cas, vous obtiendrez un graphique vide ; vous pouvez consulter les didacticiels sous [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Étapes suivantes
Vous avez correctement utilisé votre application web pour visualiser en temps réel des données de capteur à partir de votre IoT Hub.

Pour découvrir une autre façon de visualiser des données dans votre IoT Hub, voir [Visualiser des données de capteur en temps réel depuis Azure IoT Hub à l’aide de Power BI](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
