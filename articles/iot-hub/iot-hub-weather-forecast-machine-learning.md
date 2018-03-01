---
title: "Prévision météo utilisant les données Microsoft Machine Learning de votre IoT Hub | Microsoft Docs"
description: "Utilisez Azure Machine Learning pour prédire le risque de pluie sur la base des données de température et d’humidité que votre IoT Hub collecte à partir d’un capteur."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Prévisions météo avec Machine Learning"
ms.assetid: 8ba7d9e7-699c-4448-b353-0f3e1429d198
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: ec6bd6b053f6546c77508d29065de25739619d96
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Prévision météo utilisant les données de capteur de votre IoT Hub dans Azure Machine Learning

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

L’apprentissage automatique (Machine Learning) utilise des ordinateurs pour exécuter des modèles prédictifs qui apprennent à partir de données existantes afin de prévoir les tendances, résultats et comportements futurs. Azure Machine Learning est un service d’analyse prédictive sur le cloud qui permet de créer et de déployer rapidement des modèles prédictifs sous forme de solutions d’analyse.

## <a name="what-you-learn"></a>Contenu

Vous apprenez à utiliser Azure Machine Learning pour effectuer des prévisions (de chances de pluie) en utilisant les données de température et d’humidité de votre Azure IoT Hub. Les chances de sortie sont la sortie d’un modèle de prévision météo préparé. Le modèle est basé sur des données historiques pour prévoir les chances de pluie en fonction de la température et de l’humidité.

## <a name="what-you-do"></a>Procédure

- Déployez le modèle de prévision météo comme un service web.
- Préparez votre instance IoT Hub pour l’accès aux données via l’ajout d’un groupe de consommateurs.
- Créez un travail Stream Analytics et configurez-le pour :
  - Lire les données de température et d’humidité en temps réel à partir de votre IoT Hub.
  - Appelez le service web pour obtenir les chances de pluie.
  - Enregistrer les résultats dans un stockage d'objets blob Azure.
- Utilisez Microsoft Azure Storage Explorer pour afficher les prévisions météorologiques.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- Un compte Azure Machine Learning Studio. ([Essayez Machine Learning Studio gratuitement](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Déployer le modèle de prévision météo comme un service web

1. Accédez à la [page de modèle de prévision météo](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Cliquez sur **Ouvrir dans Studio** dans Microsoft Azure Machine Learning Studio.
   ![Ouvrez la page de modèle de prévision météo dans la galerie Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Cliquez sur **Exécuter** pour valider les étapes du modèle. Cette étape peut prendre 2 minutes.
   ![Ouverture du modèle de prévision météorologique dans Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Cliquez sur **CONFIGURER LE SERVICE WEB** > **Service web prédictif**.
   ![Déploiement du modèle de prévision météorologique dans Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Dans le diagramme, faites glisser le module **Entrée du service web** près du module **Modèle de score**.
1. Connectez le module **Entrée du service web** au module **Modèle de score**.
   ![Connexion de deux modules dans Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Cliquez sur **EXÉCUTER** pour valider les étapes du modèle.
1. Cliquez sur **DÉPLOYER LE SERVICE WEB** pour déployer le modèle en tant que service web.
1. Sur le tableau de bord du modèle, téléchargez le classeur **Excel 2010 ou version antérieure**  pour **DEMANDE/RÉPONSE**.

   > [!Note]
   > Assurez-vous de télécharger le classeur **Excel 2010 ou version antérieure** même si vous exécutez une version ultérieure d’Excel sur votre ordinateur.

   ![Téléchargement du classeur Excel pour le point de terminaison DEMANDE/RÉPONSE](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Ouvrez le classeur Excel, notez **l’URL DU SERVICE WEB** et la **CLÉ D’ACCÈS**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Créer, configurer et exécuter un travail Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Internet des objets** > **Tâche Stream Analytics**.
1. Saisissez les informations ci-après concernant le travail.

   **Nom du travail** : nom du travail. Le nom doit être globalement unique.

   **Groupe de ressources** : utilisez le groupe de ressources que votre instance IoT Hub exploite.

   **Emplacement** : utilisez le même emplacement que votre groupe de ressources.

   **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

   ![Créer un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Ajouter une entrée au travail Stream Analytics

1. Ouvrez le travail Stream Analytics.
1. Sous **Topologie du travail**, cliquez sur **Entrées**.
1. Dans le volet **Entrées**, cliquez sur **Ajouter**, puis saisissez les informations suivantes :

   **Alias d’entrée** : alias unique de l’entrée.

   **Source** : sélectionnez **IoT Hub**.

   **Groupe de consommateurs** : sélectionnez le groupe de consommateurs que vous avez créé.

   ![Ajouter une entrée à un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Ajouter une sortie au travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Sorties**.
1. Dans le volet **Sorties**, cliquez sur **Ajouter**, puis saisissez les informations suivantes :

   **Alias de sortie** : alias unique de la sortie.

   **Sink** : sélectionnez **Stockage d’objets blob**.

   **Compte de stockage** : Le compte de stockage pour votre stockage d’objets blob. Vous pouvez utiliser un compte de stockage existant ou en créer un nouveau.

   **Conteneur** : le conteneur dans lequel l’objet blob est enregistré. Vous pouvez créer un conteneur ou utiliser un conteneur existant.

   **Format de sérialisation de l’événement** : Sélectionnez **CSV**.

   ![Ajouter une sortie à un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Ajouter une fonction au travail Stream Analytics pour appeler le service web que vous avez déployé

1. Sous **Topologie du travail**, cliquez sur **Fonctions** > **Ajouter**.
1. Entrez les informations suivantes :

   **Alias de la fonction** : entrez `machinelearning`.

   **Type de fonction** : sélectionnez **Azure ML**.

   **Option d’importation** : sélectionnez **Importer à partir d’un autre abonnement**.

   **URL** : entrez l’URL DU SERVICE WEB que vous avez notée à partir du classeur Excel.

   **Clé** : entrez la CLÉ D’ACCÈS que vous avez notée à partir du classeur Excel.

   ![Ajouter une requête à un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurer la requête du travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Requête**.
1. Remplacez le code existant par ce qui suit :

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Remplacez `[YourInputAlias]` par l’alias d’entrée du travail.

   Remplacez `[YourOutputAlias]` par l’alias de sortie du travail.

1. Cliquez sur **Enregistrer**.

### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

Dans le travail Stream Analytics, cliquez sur **Démarrer** > **Maintenant** > **Démarrer**. Une fois le travail lancé, l’état correspondant passe de **Arrêté** à **Exécution**.

![Exécuter la tâche Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Utiliser Microsoft Azure Storage Explorer pour afficher les prévisions météorologiques

Exécutez l’application cliente pour commencer la collecte et l’envoi de données de température et d’humidité à votre IoT Hub. Pour chaque message que votre IoT Hub reçoit, le travail Stream Analytics appelle le service web de prévision météorologique pour produire les chances de pluie. Le résultat est ensuite enregistré dans votre stockage d’objets blob Azure. Azure Storage Explorer est un outil que vous pouvez utiliser pour afficher le résultat.

1. [Téléchargez et installez Microsoft Azure Storage Explorer](http://storageexplorer.com/).
1. Ouvrez l’Explorateur de stockage Azure.
1. Connectez-vous à votre compte Azure.
1. Sélectionnez votre abonnement.
1. Cliquez sur votre abonnement **Comptes de stockage** > votre compte de stockage > **Conteneurs d’objets blob** > Votre conteneur.
1. Ouvrez un fichier .csv pour voir le résultat. La dernière colonne enregistre les chances de pluie.

   ![Obtenir des résultats de prévisions météo avec Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Résumé

Vous avez correctement utilisé Azure Machine Learning pour produire le risque de pluie sur la base des données de température et d’humidité que votre IoT Hub reçoit.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]