---
title: "Visualisation en temps réel des données de capteur depuis Azure IoT Hub : Power BI | Documents Microsoft"
description: "Power BI permet d’afficher des données sur les températures et l’humidité collectées par le capteur et envoyées à votre instance Azure IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "visualisation de données en temps réel, visualisation de données en direct, visualisation de données de capteurs"
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 82915a92464f240591777595e878a534cde0136c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualiser des données de capteur en temps réel depuis Azure IoT Hub, à l’aide de Power BI

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Vous apprenez à visualiser les données de capteur en temps réel que votre instance Azure IoT Hub reçoit par l’intermédiaire de Power BI. Si vous souhaitez essayer de visualiser les données dans votre instance IoT Hub avec Web Apps, consultez la rubrique [Utiliser Azure Web Apps pour visualiser les données de capteur en temps réel à partir d’Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Procédure

- Préparation de votre instance IoT Hub pour l’accès aux données via l’ajout d’un groupe de consommateurs.
- Création, configuration et exécution d’un travail Stream Analytics pour le transfert de données depuis votre instance IoT Hub vers votre compte Power BI.
- Création et publication d’un rapport Power BI pour visualiser les données.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- Un compte Microsoft Power BI. ([Essayez Power BI gratuitement](https://powerbi.microsoft.com/)).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Créer, configurer et exécuter un travail Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Internet des objets** > **Tâche Stream Analytics**.
1. Saisissez les informations ci-après concernant le travail.

   **Nom du travail** : nom du travail. Le nom doit être globalement unique.

   **Groupe de ressources** : utilisez le groupe de ressources que votre instance IoT Hub exploite.

   **Emplacement** : utilisez le même emplacement que votre groupe de ressources.

   **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

   ![Créer un travail Stream Analytics dans Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Ajouter une entrée au travail Stream Analytics

1. Ouvrez le travail Stream Analytics.
1. Sous **Topologie du travail**, cliquez sur **Entrées**.
1. Dans le volet **Entrées**, cliquez sur **Ajouter**, puis saisissez les informations suivantes :

   **Alias d’entrée** : alias unique de l’entrée.

   **Source** : sélectionnez **IoT Hub**.

   **Groupe de consommateurs** : sélectionnez le groupe de consommateurs que vous venez de créer.
1. Cliquez sur **Créer**.

   ![Ajouter une entrée à un travail Stream Analytics dans Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Ajouter une sortie au travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Sorties**.
1. Dans le volet **Sorties**, cliquez sur **Ajouter**, puis saisissez les informations suivantes :

   **Alias de sortie** : alias unique de la sortie.

   **Section sink**: sélectionnez **Power BI**.
1. Cliquez sur **Autoriser**, puis connectez-vous à votre compte Power BI.
1. Une fois authentifié, saisissez les informations suivantes :

   **Espace de travail de groupe** : sélectionnez l’espace de travail de groupe cible.

   **Nom du jeu de données** : saisissez le nom de jeu de données.

   **Nom de la table** : saisissez le nom de la table.
1. Cliquez sur **Créer**.

   ![Ajouter une sortie à un travail Stream Analytics dans Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurer la requête du travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Requête**.
1. Remplacez `[YourInputAlias]` par l’alias d’entrée du travail.
1. Remplacez `[YourOutputAlias]` par l’alias de sortie du travail.
1. Cliquez sur **Enregistrer**.

   ![Ajouter une requête à un travail Stream Analytics dans Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

Dans le travail Stream Analytics, cliquez sur **Démarrer** > **Maintenant** > **Démarrer**. Une fois le travail lancé, l’état correspondant passe de **Arrêté** à **Exécution**.

![Exécuter un travail Stream Analytics dans Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Créer et publier un rapport Power BI pour visualiser les données

1. Vérifiez que l’exemple d’application s’exécute correctement sur votre appareil. Si ce n’est pas le cas, vous pouvez consulter les didacticiels sous [Configurer votre appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Connectez-vous à votre compte [Power BI](https://powerbi.microsoft.com/en-us/).
1. Accédez à l’espace de travail de groupe que vous définissez lors de la création de la sortie du travail Stream Analytics.
1. Cliquez sur **Jeux de données de diffusion en continu**.

   Vous devez voir apparaître le jeu de données répertorié que vous avez indiqué lors de la création de la sortie du travail Stream Analytics.
1. Sous **ACTIONS**, cliquez sur la première icône pour créer un rapport.

   ![Créer un rapport Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Créez un graphique en courbes pour afficher la température en temps réel et au fil du temps.
   1. Sur la page de création du rapport, ajoutez un graphique en courbes.
   1. Sur le volet **Champs**, développez la table que vous avez indiquée lors de la création de la sortie du travail Stream Analytics.
   1. Faites glisser **EventEnqueuedUtcTime** vers **Axe** dans le volet **Visualisations**.
   1. Faites glisser **Température** vers **Valeurs**.

      Le graphique en courbes est désormais créé. L’axe des abscisses affiche la date et l’heure du fuseau horaire UTC. Quant à l’axe des ordonnées, il affiche la température fournie par le capteur.

      ![Ajouter un graphique en courbes sur la température dans un rapport Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Créez un autre graphique en courbes pour afficher l’humidité en temps réel, au fil du temps. Pour ce faire, suivez la procédure ci-dessus et placez **EventEnqueuedUtcTime** sur l’axe des abscisses et **Humidité** sur l’axe des ordonnées.

   ![Ajouter un graphique en courbes sur l’humidité dans un rapport Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Cliquez sur **Enregistrer** pour enregistrer le rapport.
1. Cliquez sur **Fichier** > **Publier sur le web**.
1. Cliquez sur **Créer le code incorporé**, puis cliquez sur **Publier**.

Vous obtenez le lien d’accès au rapport, que vous pouvez partager avec les utilisateurs pour leur permettre d’y accéder, ainsi qu’un extrait de code, qui permet d’intégrer le rapport dans votre blog ou site web.

![Publier un rapport Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft propose également des [applications mobiles Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/), qui permettent d’afficher les tableaux de bord et rapports Power BI sur votre appareil mobile et d’interagir avec eux.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement utilisé Power BI pour visualiser les données de capteur en temps réel, à partir de votre instance Azure IoT Hub.
Cela dit, il existe un autre moyen de visualiser ces données depuis Azure IoT Hub. Voir [Utiliser Azure Web Apps pour visualiser les données de capteur en temps réel à partir d’Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
