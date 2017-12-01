---
title: "Déployer Azure Stream Analytics avec Azure IoT Edge | Microsoft Docs"
description: "Déployer Azure Stream Analytics en tant que module dans un appareil Edge"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Déployer Azure Stream Analytics en tant que module IoT Edge - version préliminaire

Les appareils IoT peuvent produire d’importantes quantités de données. Ces données doivent parfois être analysées ou traitées avant d’arriver au cloud afin de diminuer la taille des données chargées ou éliminer la latence d’opération complète d’une information exploitable.

[Azure Stream Analytics][azure-stream] (ASA) offre une syntaxe de requête très structurée pour l’analyse des données dans le cloud et sur les appareils IoT Edge. Pour en savoir plus sur Azure Stream Analytics sur IoT Edge, consultez la [documentation relative à ASA](../stream-analytics/stream-analytics-edge.md).

Ce didacticiel vous guide dans la création d’une tâche Azure Stream Analytics et son déploiement sur un appareil IoT Edge afin de traiter un flux de télémétrie local directement sur l’appareil, et de générer des alertes pour effectuer des actions sur l’appareil.  Ce didacticiel comprend deux modules. Un module capteur de température simulée (tempSensor) qui génère des données de température de 20 à 120 degrés, incrémenté de 1 toutes les 5 secondes, et un module ASA qui filtre des températures supérieures à 100 degrés. Le module ASA réinitialise le module tempSensor lorsque la moyenne sur 30 secondes atteint les 100 degrés.

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une tâche ASA pour traiter des données sur l’appareil Edge
> * Connecter la nouvelle tâche ASA à d’autres modules IoT Edge
> * Déployer la tâche ASA sur un appareil IoT Edge

## <a name="prerequisites"></a>Composants requis

* Un IoT Hub 
* L’appareil que vous avez créé et configuré dans le guide de démarrage rapide ou dans la section Déployer Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin].
* Docker sur votre appareil IoT Edge
    * [Installez Docker sur Windows][lnk-docker-windows] et assurez-vous qu’il s’exécute correctement.
    * [Installez Docker sur Linux][lnk-docker-linux] et assurez-vous qu’il s’exécute correctement.
* Python 2.7.x sur votre appareil IoT Edge
    * [Installez Python 2.7 sur Windows][lnk-python].
    * Python 2.7 est déjà installé sur la plupart des distributions Linux, Ubuntu inclus.  Utilisez la commande suivante pour vous assurer que pip est installé : `sudo apt-get install python-pip`.

> [!NOTE]
> Remarque : la chaîne de connexion de votre appareil et l’ID de l’appareil IoT Edge seront nécessaires dans ce didacticiel.

IoT Edge tire parti des modules préconfigurés IoT Edge d’Azure Service pour un déploiement rapide. Azure Stream Analytics (ASA) est un de ces modules. Vous pouvez créer une tâche ASA depuis son portail, puis vous rendre sur le portail IoT Hub pour la déployer en tant que module IoT Edge.  

Pour plus d’informations sur Azure Stream Analytics, consultez la section **Vue d’ensemble** de la [documentation Stream Analytics][azure-stream].

## <a name="create-an-asa-job"></a>Créer une tâche ASA

Dans cette section, vous créez une tâche Azure Stream Analytics pour extraire des données de votre IoT Hub, demander les données de télémétries envoyées depuis votre appareil et envoyer les résultats à un conteneur de stockage Azure (Blob). Pour plus d’informations, consultez la section **Vue d’ensemble** de la [documentation Stream Analytics][azure-stream]. 

> [!NOTE]
> Il vous faut un compte de Stockage Azure pour fournir un point de terminaison qui servira de sortie pour votre tâche ASA. L’exemple ci-dessous utilise un type de stockage d’objet blob.  Pour plus d’informations, consultez la section **Objets blob** de la [documentation sur le stockage Azure][azure-storage].

1. Dans le portail Azure, accédez à **Créer une ressource -> Stockage**, cliquez sur **Afficher tout**, puis sur **Compte de stockage - objet blob, fichier, table, file**.

2. Entrez un nom pour votre compte de stockage et sélectionnez le même emplacement que votre IoT Hub. Cliquez sur **Créer**. Notez le nom. Il sera utile plus tard.

    ![Nouveau compte de stockage][1]

3. Dans le portail Azure, accédez au compte de stockage que vous venez de créer. Cliquez sur **Parcourir les objets blob** sous **Service Blob**. 
4. Créez un conteneur pour le module ASA afin de stocker les données. Affectez le niveau d’accès _Conteneur_. Cliquez sur **OK**.

    ![paramètres de stockage][10]

5. Dans le portail Azure, accédez à **Créer une ressource** > **Internet des Objets** et sélectionnez **Tâche Stream Analytics**.

2. Entrez un nom, choisissez **Edge** comme environnement d’hébergement, puis utilisez les valeurs restantes par défaut.  Cliquez sur **Créer**.

    >[!NOTE]
    >Actuellement, les travaux ASA sur IoT Edge ne sont pas pris en charge dans la région Ouest des États-Unis 2. Sélectionnez un emplacement différent.

    ![Création ASA][5]

2. Rendez-vous dans la tâche créée, sous **Topologie de la tâche**, sélectionnez **Entrées** et cliquez sur **Ajouter**.

3. Entrez le nom `temperature`, choisissez **Flux de données** comme type de source, et utilisez les valeurs par défaut des autres paramètres. Cliquez sur **Créer**.

    ![Entrée ASA][2]

    > [!NOTE]
    > Les entrées supplémentaires peuvent comporter des points de terminaison IoT Edge spécifiques.

4. Sous **Topologie de la tâche**, sélectionnez **Sorties** et cliquez sur **Ajouter**.

5. Entrez le nom `alert` et utilisez les valeurs par défaut. Cliquez sur **Créer**.

    ![Sortie ASA][3]

6. Sous **Topologie de la tâche**, sélectionnez **Requête** et entrez :

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Déployer la tâche

Vous êtes désormais prêt à déployer la tâche ASA sur votre appareil IoT Edge.

1. Dans le portail Azure, dans votre IoT Hub, accédez à **IoT Edge (version préliminaire)** et ouvrez le panneau de votre *{deviceId}*.

1. Sélectionnez **Définir modules** puis **Importer le module IoT Edge d’Azure Service**.

1. Sélectionnez l’abonnement et la tâche ASA Edge que vous avez créée. Sélectionnez ensuite votre compte de stockage. Cliquez sur **Enregistrer**.

    ![définir module][6]

1. Cliquez sur **Ajouter un module IoT Edge** pour ajouter le module capteur de température. Entrez _tempSensor_ comme nom et `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` comme URL de l’image. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.

    ![module de température][11]

1. Copiez le nom du module ASA. Cliquez sur **Suivant** pour configurer les itinéraires.

1. Copiez ce qui suit dans **Itinéraires**.  Remplacez le nom _{moduleName}_ par le nom du module que vous avez copié :

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. Cliquez sur **Suivant**.

1. À l’étape **Vérifier le modèle**, cliquez sur **Envoyer**.

1. Revenez à la page de détails de l’appareil et cliquez sur **Actualiser**.  Vous devez voir le nouveau module _{moduleName}_ en cours d’exécution avec le module **agent IoT Edge** et le **hub IoT Edge**.

    ![sortie de module][7]

## <a name="view-data"></a>Afficher les données

Vous pouvez désormais aller sur votre appareil IoT Edge pour consulter les interactions entre les modules ASA et tempSensor.

1. Dans l’invite de commandes, configurez le runtime avec la chaîne de connexion de votre appareil IoT Edge :

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Pour démarrer le runtime, exécutez la commande :

    ```cmd/sh
    iotedgectl start  
    ```

1. Pour afficher les modules en cours d’exécution, exécutez la commande :

    ```cmd/sh
    docker ps  
    ```

    ![sortie de docker][8]

1. Pour afficher tous les journaux système et les données de mesure, exécutez la commande. Utilisez le nom du module ci-dessus :

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![journal de docker][9]

1. Dans le portail Azure, dans votre compte de stockage, sous **Service d’objet blob**, cliquez sur **Parcourir les objets blob** sélectionnez votre conteneur puis le ficher JSON récemment créé.

1. Cliquez sur **Télécharger** et affichez les résultats.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un conteneur de stockage Azure et une tâche Streaming Analytics pour qu’ils analysent les données de votre appareil IoT Edge.  Vous avez ensuite chargé un module ASA personnalisé pour extraire les données de votre appareil et les faire passer par le flux jusqu’à un objet blob pour les télécharger.  Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver des solutions pour votre entreprise.

> [!div class="nextstepaction"] 
> [Déployer un modèle Azure Machine Learning en tant que module][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/