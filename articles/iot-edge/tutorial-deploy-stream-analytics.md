---
title: Déployer Azure Stream Analytics avec Azure IoT Edge | Microsoft Docs
description: Déployer Azure Stream Analytics en tant que module dans un appareil Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c94652017216bd9c8ff319e0b19fa3597c75e81c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Déployer Azure Stream Analytics en tant que module IoT Edge - version préliminaire

Les appareils IoT peuvent produire d’importantes quantités de données. Ces données doivent parfois être analysées ou traitées avant d’arriver au cloud afin de diminuer la quantité des données chargées ou éliminer la latence d’opération complète d’une information exploitable.

Azure IoT Edge tire parti des modules Azure Service IoT Edge prédéfinis pour un déploiement rapide. [Azure Stream Analytics][azure-stream] est un de ces modules. Vous pouvez créer un travail Azure Stream Analytics depuis son portail, puis accéder au portail Azure IoT Hub pour le déployer en tant que module IoT Edge. 

Azure Stream Analytics offre une syntaxe de requête très structurée pour l’analyse des données dans le cloud et sur les appareils IoT Edge. Pour plus d’informations, consultez sur Azure Stream Analytics sur IoT Edge, consultez la [documentation Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Ce didacticiel illustre la création d’un travail Azure Stream Analytics et son déploiement dans un service IoT Edge. Ceci vous permet de traiter un flux de données de télémétrie local directement sur l’appareil et de générer des alertes ayant une action immédiate sur l’appareil. 

Ce didacticiel présente deux modules : 
* Un module capteur de température simulée (tempSensor) qui génère des données de température de 20 à 120 degrés, incrémentées de 1 toutes les 5 secondes. 
* Un module Stream Analytics qui réinitialise le module tempSensor quand la moyenne sur 30 secondes atteint 70. Dans un environnement de production, vous pouvez utiliser cette fonctionnalité pour arrêter un ordinateur ou prendre des mesures préventives quand la température atteint des niveaux dangereux. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un travail Azure Stream Analytics pour traiter les données à la périphérie.
> * Connecter le nouveau travail Azure Stream Analytics à d’autres modules IoT Edge.
> * Déployer le travail Azure Stream Analytics sur un appareil IoT Edge.

## <a name="prerequisites"></a>Prérequis


* Un IoT Hub. 
* L’appareil que vous avez créé et configuré dans le guide de démarrage rapide ou dans les articles sur le déploiement d’Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. Vous devez connaître la clé de connexion et l’ID de l’appareil. 
* Docker en cours d’exécution sur votre appareil IoT Edge.
    * [Installer Docker sur Windows][lnk-docker-windows].
    * [Installer Docker sur Linux][lnk-docker-linux].
* Python 2.7.x sur votre appareil IoT Edge.
    * [Installez Python 2.7 sur Windows][lnk-python].
    * Python 2.7 est déjà installé sur la plupart des distributions Linux, Ubuntu inclus. Utilisez la commande suivante pour vous assurer que pip est installé : `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Créer un travail Azure Stream Analytics

Dans cette section, vous créez un travail Azure Stream Analytics pour extraire des données de votre IoT Hub, demander les données de télémétries envoyées depuis votre appareil, puis envoyer les résultats à un conteneur de stockage Azure Blob. Pour plus d’informations, consultez la section « Vue d’ensemble » de la [documentation Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Il vous faut un compte de Stockage Azure pour fournir un point de terminaison qui servira de sortie pour votre travail Azure Stream Analytics. L’exemple dans cette section utilise un type de stockage Blob. Pour plus d’informations, consultez la section « Objets blob » de la [documentation sur le stockage Azure][azure-storage].

1. Dans le portail Azure, accédez à **Créer une ressource**, entrez **Compte de stockage** dans la zone de recherche, puis sélectionnez **Compte de stockage : blob, fichier, table, file d’attente**.

2. Dans le volet **Créer un compte de stockage**, entrez un nom pour votre compte de stockage, sélectionnez le même emplacement que celui où votre IoT Hub est stocké, puis sélectionnez **Créer**. Notez le nom. Il sera utile plus tard.

    ![Créez un compte de stockage.][1]

3. Accédez au compte de stockage que vous venez de créer, puis sélectionnez **Parcourir les objets Blob**. 

4. Créez un conteneur pour que le module Azure Stream Analytics stocke des données, définissez le niveau d’accès sur **Conteneur**, puis sélectionnez **OK**.

    ![Paramètres de stockage][10]

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le portail Azure, accédez à **Créer une ressource** > **Internet des objets** et sélectionnez **Travail Stream Analytics**.

2. Dans le volet **Nouveau travail Stream Analytics**, procédez comme suit :

    a. Dans la zone **Nom du travail**, entrez un nom pour le travail.
    
    b. Sous **Environnement d’hébergement**, sélectionnez **Edge**.
    
    c. Dans les autres champs, utilisez les valeurs par défaut.

    > [!NOTE]
    > Actuellement, les travaux Azure Stream Analytics sur IoT Edge ne sont pas pris en charge dans la région Ouest des États-Unis 2. 

3. Sélectionnez **Créer**.

4. Dans le travail créé, sous **Topologie du travail**, sélectionnez **Entrées**, puis **Ajouter**.

5. Dans le volet **Nouvelle entrée**, procédez comme suit :

    a. Dans la zone **Alias d’entrée**, entrez **température**.
    
    b. Dans la zone **Type de source**, sélectionnez **Flux de données**.
    
    c. Dans les autres champs, utilisez les valeurs par défaut.

   ![Entrée Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Sélectionnez **Créer**.

7. Sous **Topologie du travail**, sélectionnez **Sorties**, puis **Ajouter**.

8. Dans le volet **Nouvelle sortie**, procédez comme suit :

    a. Dans la zone **Alias de sortie**, tapez **alerte**.
    
    b. Dans les autres champs, utilisez les valeurs par défaut. 
    
    c. Sélectionnez **Créer**.

   ![Sortie Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. Sous **Topologie du travail**, sélectionnez **Requête**, puis remplacez le texte par défaut par la requête suivante :

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

10. Sélectionnez **Enregistrer**.

## <a name="deploy-the-job"></a>Déployer la tâche

Vous êtes désormais prêt à déployer le travail Azure Stream Analytics sur votre appareil IoT Edge.

1. Dans le portail Azure, dans votre IoT Hub, accédez à **IoT Edge (préversion)** et ouvrez la page de détails de votre appareil IoT Edge.

2. Sélectionnez **Définir des modules**.  
    Si vous aviez déployé le module tempSensor sur cet appareil, il peut se remplir automatiquement. Sinon, ajoutez le module en procédant comme suit :

   a. Sélectionnez **Ajouter un module IoT Edge**.

   b. Pour le nom, tapez **tempSensor**.
    
   c. Pour l’URI de l’image, entrez **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**. 

   d. Laissez les autres paramètres inchangés.
   
   e. Sélectionnez **Enregistrer**.

3. Pour ajouter votre tr Azure Stream Analytics Edge, sélectionnez **Importer le module Azure Stream Analytics IoT Edge**.

4. Sélectionnez votre abonnement et le travail Azure Stream Analytics Edge que vous avez créé. 

5. Sélectionnez votre abonnement et le compte de stockage que vous avez créé, puis sélectionnez **Enregistrer**.

    ![Définir le module][6]

6. Copiez le nom de votre module Azure Stream Analytics. 

    ![Module de température][11]

7. Cliquez sur **Suivant** pour configurer les itinéraires.

8. Copiez le code suivant dans **Itinéraires**. Remplacez _{moduleName}_ par le nom du module que vous avez copié :

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

9. Sélectionnez **Suivant**.

10. À l’étape **Vérifier le modèle**, sélectionnez **Envoyer**.

11. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**.  
    Vous devez voir le nouveau module Stream Analytics en cours d’exécution avec le module Agent IoT Edge et IoT Edge Hub.

    ![Sortie de module][7]

## <a name="view-data"></a>Afficher les données

Vous pouvez désormais aller sur votre appareil IoT Edge pour consulter les interactions entre les modules Azure Stream Analytics et tempSensor.

1. Vérifiez que tous les modules sont en cours d’exécution dans Docker :

   ```cmd/sh
   docker ps  
   ```

   ![Sortie de docker][8]

2. Affichez tous les journaux système et les données des métriques. Utilisez le nom du module Stream Analytics :

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Vous devriez voir la température de l’ordinateur augmenter progressivement jusqu’à ce qu’elle atteigne 70 degrés pendant 30 secondes. Le module Stream Analytics déclenche alors une réinitialisation qui fait redescendre la température de l’ordinateur à 21. 

   ![Journal de docker][9]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un conteneur de stockage Azure et un travail Streaming Analytics pour qu’ils analysent les données de votre appareil IoT Edge. Vous avez ensuite chargé un module Azure Stream Analytics personnalisé pour extraire les données de votre appareil et les faire passer par le flux jusqu’à un objet blob pour les télécharger. Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver d’autres solutions pour votre entreprise.

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
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
