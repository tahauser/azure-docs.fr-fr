---
title: "Déployer Azure Stream Analytics avec Azure IoT Edge | Microsoft Docs"
description: "Déployer Azure Stream Analytics en tant que module dans un appareil Edge"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Déployer Azure Stream Analytics en tant que module IoT Edge - préversion

Les appareils IoT peuvent produire d’importantes quantités de données. Ces données doivent parfois être analysées ou traitées avant d’arriver au cloud afin de diminuer la taille des données chargées ou d’éliminer la latence bidirectionnelle d’un insight actionnable.

IoT Edge tire parti des modules Azure Service IoT Edge prédéfinis pour un déploiement rapide. [Azure Stream Analytics][azure-stream] (ASA) est un de ces modules. Vous pouvez créer une tâche ASA depuis son portail, puis vous rendre sur le portail IoT Hub pour la déployer en tant que module IoT Edge.  

Azure Stream Analytics offre une syntaxe de requête très structurée pour l’analyse des données dans le cloud et sur les appareils IoT Edge. Pour en savoir plus sur Azure Stream Analytics sur IoT Edge, consultez la [documentation relative à ASA](../stream-analytics/stream-analytics-edge.md).

Ce didacticiel vous guide dans la création d’une tâche Azure Stream Analytics et son déploiement sur un appareil IoT Edge afin de traiter un flux de télémétrie local directement sur l’appareil, et de générer des alertes pour effectuer des actions sur l’appareil.  Ce didacticiel comprend deux modules. Un module capteur de température simulée (tempSensor) génère des données de température de 20 à 120 degrés, incrémentées de 1 toutes les 5 secondes. Un module Stream Analytics réinitialise le module tempSensor quand la moyenne sur 30 secondes atteint 70. Dans un environnement de production, cette fonctionnalité peut être utilisée pour arrêter un ordinateur ou prendre des mesures préventives quand la température atteint des niveaux dangereux. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une tâche ASA pour traiter des données sur l’appareil Edge
> * Connecter la nouvelle tâche ASA à d’autres modules IoT Edge
> * Déployer la tâche ASA sur un appareil IoT Edge

## <a name="prerequisites"></a>Prérequis

* Un hub IoT 
* L’appareil que vous avez créé et configuré dans le guide de démarrage rapide ou dans la section Déployer Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Vous devez connaître la clé de connexion et l’ID de l’appareil. 
* Docker en cours d’exécution sur votre appareil IoT Edge
    * [Installer Docker sur Windows][lnk-docker-windows]
    * [Installer Docker sur Linux][lnk-docker-linux]
* Python 2.7.x sur votre appareil IoT Edge
    * [Installez Python 2.7 sur Windows][lnk-python].
    * Python 2.7 est déjà installé sur la plupart des distributions Linux, Ubuntu inclus.  Utilisez la commande suivante pour vous assurer que pip est installé : `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>Créer une tâche ASA

Dans cette section, vous créez une tâche Azure Stream Analytics pour extraire des données de votre hub IoT, demander les données de télémétries envoyées depuis votre appareil et envoyer les résultats à un conteneur de stockage Azure (Blob). Pour plus d’informations, consultez la section **Vue d’ensemble** de la [documentation Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Il vous faut un compte de Stockage Azure pour fournir un point de terminaison qui servira de sortie pour votre tâche ASA. L’exemple ci-dessous utilise un type de stockage d’objet blob.  Pour plus d’informations, consultez la section **Objets blob** de la [documentation sur le stockage Azure][azure-storage].

1. Dans le portail Azure, accédez à **Créer une ressource** et entrez `Storage account` dans la barre de recherche. Sélectionnez **Compte de stockage - blob, fichier, table, file d’attente**.

2. Entrez un nom pour votre compte de stockage et sélectionnez le même emplacement que votre hub IoT. Cliquez sur **Créer**. Notez le nom. Vous en aurez besoin plus tard.

    ![Nouveau compte de stockage][1]

3. Accédez au compte de stockage que vous venez de créer. Cliquez sur **Parcourir les objets blob**. 
4. Créez un conteneur pour le module ASA afin de stocker les données. Affectez le niveau d’accès **Conteneur**. Cliquez sur **OK**.

    ![paramètres de stockage][10]

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le portail Azure, accédez à **Créer une ressource** > **Internet des Objets** et sélectionnez **Tâche Stream Analytics**.

2. Entrez un nom, choisissez **Edge** comme environnement d’hébergement, puis utilisez les valeurs restantes par défaut.  Cliquez sur **Créer**.

    >[!NOTE]
    >Actuellement, les travaux ASA sur IoT Edge ne sont pas pris en charge dans la région Ouest des États-Unis 2. 

3. Accédez à la tâche créée. Sélectionnez **Entrées**, puis cliquez sur **Ajouter**.

4. Tapez `temperature` pour l’alias d’entrée, choisissez **Flux de données** comme type de source, et utilisez les valeurs par défaut des autres paramètres. Cliquez sur **Créer**.

   ![Entrée ASA](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Sélectionnez **Sorties**, puis cliquez sur **Ajouter**.

6. Tapez `alert` pour l’alias de sortie et utilisez les valeurs par défaut des autres paramètres. Cliquez sur **Créer**.

   ![Sortie ASA](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Sélectionnez **Requête**.
8. Remplacez le texte par défaut par la requête suivante :

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. Cliquez sur **Enregistrer**.

## <a name="deploy-the-job"></a>Déployer la tâche

Vous êtes désormais prêt à déployer la tâche ASA sur votre appareil IoT Edge.

1. Dans le portail Azure, dans votre hub IoT, accédez à **IoT Edge (préversion)** et ouvrez la page de détails de votre appareil IoT Edge.
1. Sélectionnez **Définir des modules**.
1. Si vous aviez déployé le module tempSensor sur cet appareil, il peut se remplir automatiquement. Si ce n’est pas le cas, effectuez les étapes suivantes pour ajouter ce module :
   1. Cliquez sur **Ajouter un module IoT Edge**.
   1. Entrez `tempSensor` comme nom et `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` pour l’URI de l’image. 
   1. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.
1. Pour ajouter votre tâche ASA Edge, sélectionnez **Importer le module Azure Stream Analytics IoT Edge**.
1. Sélectionnez votre abonnement et la tâche ASA Edge que vous avez créée. 
1. Sélectionnez votre abonnement et le compte de stockage que vous avez créé. Cliquez sur **Enregistrer**.

    ![définir le module][6]

1. Copiez le nom généré automatiquement pour votre module ASA. 

    ![module de température][11]

1. Cliquez sur **Suivant** pour configurer les itinéraires.
1. Copiez ce qui suit dans **Itinéraires**.  Remplacez _{moduleName}_ par le nom du module que vous avez copié :

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

1. Revenez à la page de détails de l’appareil et cliquez sur **Actualiser**.  Vous devez voir le nouveau module Stream Analytics en cours d’exécution avec le module **IoT Edge agent** et le **hub IoT Edge**.

    ![sortie de module][7]

## <a name="view-data"></a>Afficher les données

Vous pouvez désormais aller sur votre appareil IoT Edge pour consulter les interactions entre les modules ASA et tempSensor.

Vérifiez que tous les modules sont en cours d’exécution dans Docker :

   ```cmd/sh
   docker ps  
   ```

   ![sortie de docker][8]

Affichez tous les journaux système et les données des métriques. Utilisez le nom du module Stream Analytics :

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Vous devriez voir la température de l’ordinateur augmenter progressivement jusqu’à ce qu’elle atteigne 70 degrés pendant 30 secondes. Le module Stream Analytics déclenche alors une réinitialisation qui fait redescendre la température de l’ordinateur à 21. 

   ![journal de docker][9]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un conteneur de stockage Azure et une tâche Streaming Analytics pour qu’ils analysent les données de votre appareil IoT Edge.  Vous avez ensuite chargé un module ASA personnalisé pour extraire les données de votre appareil et les faire passer par le flux jusqu’à un objet blob pour les télécharger.  Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver des solutions pour votre entreprise.

> [!div class="nextstepaction"] 
> [Déployer un modèle Azure Machine Learning en tant que module][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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