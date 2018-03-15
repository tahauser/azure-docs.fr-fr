---
title: "Simulation d’appareil dans la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel montre comment utiliser le simulateur d’appareil avec la solution préconfigurée de surveillance à distance."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 9f51c35be09af6f3a8dde7061dcf57a9c4cc9fdb
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-new-simulated-device"></a>Créer un appareil simulé

Ce didacticiel montre comment personnaliser le microservice de simulateur d’appareil dans la solution préconfigurée de monitoring à distance. Pour illustrer les fonctionnalités de simulateur d’appareil, ce didacticiel utilise deux scénarios dans l’application IoT Contoso.

Dans le premier scénario, Contoso souhaite tester un nouvel appareil d’éclairage connecté. Pour exécuter les tests, vous créez un appareil simulé ayant les caractéristiques suivantes :

*Propriétés*

| NOM                     | Valeurs                      |
| ------------------------ | --------------------------- |
| Couleur                    | White, Red, Blue (Blanc, Rouge, Bleu)            |
| Brightness (Luminosité)               | 0 à 100                    |
| Estimated remaining life (Durée de vie restante estimée) | Compte à rebours à partir de 10 000 heures |

*Télémétrie*

Le tableau suivant présente les données que l’appareil d’éclairage communique au cloud sous la forme d’un flux de données :

| NOM   | Valeurs      |
| ------ | ----------- |
| Statut | « on » (allumé), « off » (éteint) |
| Température | Degrés F |
| online | true, false |

> [!NOTE]
> La valeur de télémétrie **online** est obligatoire pour tous les types simulés.

*Méthodes*

Le tableau suivant indique les actions prises en charge par le nouvel appareil :

| NOM        |
| ----------- |
| Switch on (Allumer)   |
| Switch off (Éteindre)  |

*État initial*

Le tableau suivant présente l’état initial de l’appareil :

| NOM                     | Valeurs |
| ------------------------ | -------|
| Initial color (Couleur initiale)            | Blanc  |
| Initial brightness (Luminosité initiale)       | 75     |
| Initial remaining life (Durée de vie restante initiale)   | 10 000 |
| Initial telemetry status (État initial de la télémétrie) | « on » (allumé)   |
| Initial telemetry temperature (Température initiale de la télémétrie) | 200   |

Dans le second scénario, vous ajoutez un nouveau type de données de télémétrie à l’appareil **Chiller** (Refroidisseur) existant de Contoso.

Ce didacticiel montre comment utiliser le simulateur d’appareil avec la solution préconfigurée de surveillance à distance :

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Créer un type d’appareil
> * Simuler un comportement personnalisé de l’appareil
> * Ajouter un nouveau type d’appareil au tableau de bord
> * Envoyer des données de télémétrie personnalisées à partir d’un type d’appareil existant

La vidéo suivante explique pas à pas comment connecter des appareils simulés et réels à la solution de surveillance à distance :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Une instance déployée de la solution de surveillance à distance dans votre abonnement Azure. Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez terminer le didacticiel [Déployer la solution de surveillance à distance préconfigurée](iot-suite-remote-monitoring-deploy.md).

* Visual Studio 2017. Si vous n’avez pas Visual Studio 2017, vous pouvez télécharger la version gratuite [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/).

* L’extension Visual Studio [Cloud Explorer pour Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview).

* Un compte sur [Docker Hub](https://hub.docker.com/). Vous pouvez vous inscrire gratuitement.

* [Git](https://git-scm.com/downloads) doit être installé sur votre ordinateur de bureau.

## <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement

Effectuez les tâches suivantes afin de préparer votre environnement de développement pour l’ajout d’un nouvel appareil simulé à votre solution de surveillance à distance :

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Configurer l’accès SSH à la machine virtuelle de solution dans Azure

Quand vous avez créé votre solution de surveillance à distance sur [www.azureiotsuite.com](https://www.azureiotsuite.com), vous avez choisi un nom de solution. Le nom de la solution devient le nom du groupe de ressources Azure qui contient les différentes ressources déployées utilisées par la solution. Les commandes suivantes utilisent un groupe de ressources nommé **Contoso-01**. Vous devez remplacer **Contoso-01** par le nom de votre groupe de ressources.

Ces commandes utilisent la commande `az` [d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Vous pouvez installer Azure CLI 2.0 sur votre ordinateur de développement, ou utiliser [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) dans le [portail Azure](http://portal.azure.com). Azure CLI 2.0 est préinstallé dans Cloud Shell.

1. Pour vérifier le nom du groupe de ressources qui contient vos ressources de surveillance à distance, exécutez la commande suivante :

    ```sh
    az group list | grep "name"
    ```

    Cette commande dresse la liste de tous les groupes de ressources dans votre abonnement. Dans cette liste doit figurer un groupe de ressources avec le même nom que votre solution de surveillance à distance.

1. Pour faire de votre groupe de ressources le groupe par défaut pour les commandes ultérieures, exécutez la commande suivante en remplaçant **Contoso-01** par le nom de votre groupe de ressources :

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Pour dresser la liste des ressources dans votre groupe de ressources, exécutez la commande suivante :

    ```sh
    az resource list -o table
    ```

    Prenez note des noms de votre machine virtuelle et de votre groupe de sécurité réseau. Vous utiliserez ces valeurs lors d’étapes ultérieures.

1. Pour activer l’accès SSH à votre machine virtuelle, exécutez la commande suivante en utilisant le nom du groupe de sécurité réseau noté à l’étape précédente :

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Pour afficher la liste des règles de trafic entrant pour votre réseau, exécutez la commande suivante :

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. Pour remplacer le mot de passe de machine virtuelle par un mot de passe que vous connaissez, exécutez la commande suivante. Utilisez le nom de la machine virtuelle noté précédemment et un mot de passe de votre choix :

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Pour rechercher l’adresse IP de votre machine virtuelle, utilisez la commande suivante et notez l’adresse IP publique :

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. Vous pouvez maintenant utiliser SSH pour vous connecter à votre machine virtuelle. La commande `ssh` est préinstallée dans Cloud Shell. Utilisez l’adresse IP publique notée à l’étape précédente et, à l’invite, entrez le mot de passe que vous avez configuré pour la machine virtuelle :

    ```sh
    ssh azureuser@public-ip-address
    ```

    Vous avez désormais accès à l’interpréteur de commandes sur la machine virtuelle qui exécute les conteneurs Docker dans la solution de surveillance à distance. Pour afficher les conteneurs en cours d’exécution, utilisez la commande suivante :

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Rechercher les chaînes de connexion de service

Dans le didacticiel, vous travaillez avec une solution Visual Studio qui se connecte aux services IoT Hub et Cosmos DB de la solution. Les étapes suivantes illustrent une façon de rechercher les valeurs de chaîne de connexion dont vous avez besoin :

1. Pour rechercher la chaîne de connexion Cosmos DB, exécutez la commande suivante dans la session SSH connectée à la machine virtuelle :

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Prenez note de la chaîne de connexion. Vous utiliserez cette valeur plus loin dans le didacticiel.

1. Pour rechercher la chaîne de connexion IoT Hub, exécutez la commande suivante dans la session SSH connectée à la machine virtuelle :

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Prenez note de la chaîne de connexion. Vous utiliserez cette valeur plus loin dans le didacticiel.

> [!NOTE]
> Vous pouvez aussi trouver ces chaînes de connexion dans le portail Azure ou en exécutant la commande `az`.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Arrêter le service de simulation d’appareil sur la machine virtuelle

Quand vous modifiez le service de simulation d’appareil, vous pouvez l’exécuter localement pour tester vos modifications. Avant d’exécuter le service de simulation d’appareil localement, vous devez arrêter l’instance en cours d’exécution sur la machine virtuelle en procédant comme suit :

1. Pour rechercher le **CONTAINER ID** du service **device-simulation**, exécutez la commande suivante dans la session SSH connectée à la machine virtuelle :

    ```sh
    docker ps
    ```

    Prenez note de l’ID de conteneur du service **device-simulation**.

1. Pour arrêter le conteneur **device-simulation**, exécutez la commande suivante :

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Cloner les dépôts GitHub

Dans ce didacticiel, vous travaillez avec les projets Visual Studio **device-simulation** et **storage-adapter**. Vous pouvez cloner les dépôts de code source à partir de GitHub. Effectuez cette étape sur votre ordinateur de développement local où Visual Studio est installé :

1. Ouvrez une invite de commandes et accédez au dossier où vous souhaitez enregistrer votre copie des dépôts GitHub **device-simulation** et **storage-adapter**.

1. Pour cloner la version .NET du dépôt **device-simulation**, exécutez la commande suivante :

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Le service de simulation d’appareil dans la solution de surveillance à distance vous permet d’apporter des modifications aux types d’appareils simulés intégrés et de créer des types d’appareils simulés. Vous pouvez utiliser des types d’appareils personnalisés pour tester le comportement de la solution de surveillance à distance avant de connecter vos appareils physiques.

1. Pour cloner la version .NET du dépôt **storage-adapter**, exécutez la commande suivante :

    ```cmd
    git clone https://github.com/Azure/storage-adapter.git
    ```

    Le service de simulation d’appareil utilise le service d’adaptateur de stockage pour se connecter au service Cosmos DB dans Azure. La solution de surveillance à distance stocke les données de configuration d’appareil simulé dans une base de données Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Exécuter le service d’adaptateur de stockage local

Le service de simulation d’appareil utilise le service d’adaptateur de stockage pour se connecter à la base de données Cosmos DB de la solution. Si vous exécutez le service de simulation d’appareil localement, vous devez aussi exécuter le service d’adaptateur de stockage localement. Les étapes suivantes montrent comment exécuter le service d’adaptateur de stockage à partir de Visual Studio :

1. Dans Visual Studio, ouvrez le fichier de solution **pcs-storage-adapter.sln** dans votre clone local du dépôt **storage-adapter**.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **WebService**, choisissez **Propriétés**, puis **Déboguer**.

1. Dans la section **Variables d’environnement**, modifiez la valeur de la variable **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** pour qu’elle corresponde à la chaîne de connexion Cosmos DB notée précédemment. Ensuite, enregistrez vos modifications.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **WebService**, choisissez **Déboguer**, puis **Démarrer une nouvelle instance**.

1. Le service commence à s’exécuter localement et ouvre `http://localhost:9022/v1/status` dans votre navigateur par défaut. Vérifiez que la valeur **État** est « OK: Alive and well ».

1. Laissez le service d’adaptateur de stockage s’exécuter localement jusqu’à ce que vous ayez terminé le didacticiel.

Tout est désormais en place et vous pouvez commencer à ajouter un nouveau type d’appareil simulé à votre solution de surveillance à distance.

## <a name="create-a-simulated-device-type"></a>Créer un type d’appareil simulé

Pour créer un type d’appareil dans le service de simulation d’appareil, le plus simple consiste à copier et à modifier un type existant. Les étapes suivantes montrent comment copier l’appareil **Chiller** intégré pour créer un appareil **Lightbulb** :

1. Dans Visual Studio, ouvrez le fichier de solution **device-simulation.sln** dans votre clone local du dépôt **device-simulation**.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulationAgent**, choisissez **Propriétés**, puis **Déboguer**.

1. Dans la section **Variables d’environnement**, modifiez la valeur de la variable **PCS\_IOTHUB\_CONNSTRING** pour qu’elle corresponde à la chaîne de connexion IoT Hub notée précédemment. Ensuite, enregistrez vos modifications.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution **device-simulation**, puis choisissez **Définir les projets de démarrage**. Choisissez **Projet de démarrage unique** et sélectionnez **SimulationAgent**. Cliquez ensuite sur **OK**.

1. Chaque type d’appareil a un fichier de modèle JSON et des scripts associés dans le dossier **Services/data/devicemodels**. Dans l’Explorateur de solutions, copiez les fichiers **Chiller** pour créer les fichiers **Lightbulb** comme indiqué dans le tableau suivant :

    | Source                      | Destination                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Définir les caractéristiques du nouveau type d’appareil

Le fichier **lightbulb-01.json** définit les caractéristiques du type, telles que les données de télémétrie qu’il génère et les méthodes qu’il prend en charge. Les étapes suivantes mettent à jour le fichier **lightbulb-01.json** pour définir l’appareil **Lightbulb** :

1. Dans le fichier **lightbulb-01.json**, mettez à jour les métadonnées de l’appareil comme le montre l’extrait de code suivant :

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Dans le fichier **lightbulb-01.json**, mettez à jour la définition de simulation comme le montre l’extrait de code suivant :

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. Dans le fichier **lightbulb-01.json**, mettez à jour les propriétés du type d’appareil comme le montre l’extrait de code suivant :

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Dans le fichier **lightbulb-01.json**, mettez à jour les définitions des données de télémétrie du type d’appareil comme le montre l’extrait de code suivant :

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. Dans le fichier **lightbulb-01.json**, mettez à jour les méthodes du type d’appareil comme le montre l’extrait de code suivant :

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Enregistrez le fichier **lightbulb-01.json**.

### <a name="simulate-custom-device-behavior"></a>Simuler un comportement personnalisé de l’appareil

Le fichier **scripts/lightbulb-01-state.js** définit le comportement de simulation du type **Lightbulb**. Les étapes suivantes mettent à jour le fichier **scripts/lightbulb-01-state.js** pour définir le comportement de l’appareil **Lightbulb** :

1. Modifiez la définition de l’état dans le fichier **scripts/lightbulb-01-state.js** comme le montre l’extrait de code suivant :

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Ajoutez une fonction **flip** après la fonction **vary** avec la définition suivante :

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Modifiez la fonction **main** pour implémenter le comportement comme le montre l’extrait de code suivant :

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Enregistrez le fichier **scripts/lightbulb-01-state.js**.

Le fichier **scripts/SwitchOn-method.js** implémente la méthode **Switch On** dans un appareil **Lightbulb**. Les étapes suivantes mettent à jour le fichier **scripts/SwitchOn-method.js** :

1. Modifiez la définition de l’état dans le fichier **scripts/SwitchOn-method.js** comme le montre l’extrait de code suivant :

    ```js
    var state = {
       status: "on"
    };
    ```

1. Pour allumer l’appareil d’éclairage, modifiez la fonction **main** comme suit :

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Enregistrez le fichier **scripts/SwitchOn-method.js**.

1. Effectuez une copie du fichier **scripts/SwitchOn-method.js** nommée **scripts/SwitchOff-method.js**.

1. Pour arrêter l’appareil d’éclairage, modifiez la fonction **main** dans le fichier **scripts/SwitchOff-method.js** comme suit :

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Enregistrez le fichier **scripts/SwitchOff-method.js**.

1. Dans l’Explorateur de solutions, sélectionnez tour à tour chacun de vos quatre nouveaux fichiers. Dans la fenêtre **Propriétés** de chaque fichier, vérifiez que **Copier dans le répertoire de sortie** a la valeur **Copier si plus récent**.

### <a name="configure-the-device-simulation-service"></a>Configurer le service de simulation d’appareil

Pour limiter le nombre d’appareils simulés qui se connectent à la solution pendant les tests, configurez le service pour exécuter un seul appareil de refroidissement et un seul appareil d’éclairage. Les données de configuration sont stockées dans l’instance Cosmos DB dans le groupe de ressources de la solution. Pour modifier les données de configuration, utilisez la vue **Cloud Explorer** dans Visual Studio :

1. Pour ouvrir la vue **Cloud Explorer** dans Visual Studio, choisissez **Vue**, puis **Cloud Explorer**.

1. Pour trouver le document de configuration de simulation, dans **Rechercher des ressources**, entrez **simualtions.1**.

1. Double-cliquez sur le document **simulations.1** pour l’ouvrir et le modifier.

1. Dans la valeur **Données**, recherchez le tableau **DeviceModels** qui ressemble à l’extrait de code suivant :

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Pour définir un seul appareil simulé de refroidissement et d’éclairage, remplacez le tableau **DeviceModels** par le code suivant :

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Enregistrez les modifications apportées au document **simulations.1**.

> [!NOTE]
> Vous pouvez également utiliser l’Explorateur de données Cosmos DB dans le portail Azure pour modifier le document **simulations.1**.

### <a name="test-the-lightbulb-device-type-locally"></a>Tester le type d’appareil Lightbulb localement

Vous êtes maintenant prêt à tester votre nouveau type d’appareil d’éclairage simulé en exécutant le projet de simulation d’appareil localement.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulationAgent**, choisissez **Déboguer**, puis **Démarrer une nouvelle instance**.

1. Pour vérifier que les deux appareils simulés sont connectés à votre IoT Hub, ouvrez le portail Azure dans votre navigateur.

1. Naviguez jusqu’au hub IoT dans le groupe de ressources qui contient votre solution de surveillance à distance.

1. Dans la section **Surveillance**, choisissez **Métriques**. Vérifiez qu’il y a deux **Appareils connectés** :

    ![Nombre d’appareils connectés](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. Dans votre navigateur, accédez au **Tableau de bord** de votre solution de surveillance à distance. Dans le panneau de télémétrie sur le **Tableau de bord**, sélectionnez **temperature**. La température de vos deux appareils simulés s’affiche sur le graphique :

    ![Données de télémétrie de température](media/iot-suite-remote-monitoring-test/telemetry.png)

La simulation de l’appareil d’éclairage s’exécute maintenant localement. L’étape suivante consiste à déployer votre code de simulateur mis à jour sur la machine virtuelle qui exécute les microservices de surveillance à distance dans Azure.

Avant de continuer, vous pouvez arrêter le débogage des projets de simulation d’appareil et d’adaptateur de stockage dans Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Déployer le simulateur mis à jour dans le cloud

Les microservices dans la solution de surveillance à distance s’exécutent dans des conteneurs docker. Les conteneurs sont hébergés sur la machine virtuelle de la solution dans Azure. Dans cette section, vous allez :

* Créer une image docker de simulation d’appareil.
* Charger l’image vers votre dépôt de hub docker.
* Importer l’image dans la machine virtuelle de votre solution.

Les étapes suivantes partent du principe que vous avez un dépôt nommé **lightbulb** dans votre compte Docker Hub.

1. Dans Visual Studio, dans le projet **device-simulation**, ouvrez le fichier **solution\scripts\docker\build.cmd**.

1. Modifiez la ligne qui définit la variable d’environnement **DOCKER_IMAGE** afin qu’elle corresponde au nom de votre dépôt Docker Hub :

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Enregistrez la modification.

1. Dans Visual Studio, dans le projet **device-simulation**, ouvrez le fichier **solution\scripts\docker\publish.cmd**.

1. Modifiez la ligne qui définit la variable d’environnement **DOCKER_IMAGE** afin qu’elle corresponde au nom de votre dépôt Docker Hub :

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Enregistrez la modification.

1. Ouvrez une invite de commandes en tant qu’administrateur. Ensuite, accédez au dossier **scripts\docker** dans votre clone du dépôt GitHub **device-simulation**.

1. Pour générer l’image docker, exécutez la commande suivante :

    ```cmd
    build.cmd
    ```

1. Pour vous connecter à votre compte Docker Hub, exécutez la commande suivante :

    ```cmd
    docker login
    ```

1. Pour charger votre nouvelle image vers votre compte Docker Hub, exécutez la commande suivante :

    ```cmd
    publish.cmd
    ```

1. Pour vérifier le chargement, accédez à [https://hub.docker.com/](https://hub.docker.com/). Recherchez votre dépôt **lightbulb** et choisissez **Details**. Ensuite, choisissez **Tags** :

    ![Docker Hub](media/iot-suite-remote-monitoring-test/dockerhub.png)

    Les scripts ont ajouté la balise **testing** à l’image.

1. Utilisez le protocole SSH pour vous connecter à la machine virtuelle de votre solution dans Azure. Ensuite, accédez au dossier **App** et modifiez le fichier **docker-compose.yaml** :

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Modifiez l’entrée du service de simulation d’appareil pour qu’elle corresponde à votre image docker :

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Enregistrez vos modifications.

1. Pour redémarrer tous les services avec les nouveaux paramètres, exécutez la commande suivante :

    ```sh
    sudo ./start.sh
    ```

1. Pour vérifier le fichier journal de votre nouveau conteneur de simulation d’appareil, exécutez la commande suivante pour rechercher l’ID de conteneur :

    ```sh
    docker ps
    ```

    Ensuite, exécutez la commande suivante à l’aide de l’ID de conteneur :

    ```sh
    docker logs {container ID}
    ```

Vous avez maintenant terminé les étapes de déploiement d’une version mise à jour du service de simulation d’appareil sur votre solution de surveillance à distance.

Dans votre navigateur, accédez au **Tableau de bord** de votre solution de surveillance à distance. Dans le panneau de télémétrie sur le **Tableau de bord**, sélectionnez **temperature**. La température de vos deux appareils simulés s’affiche sur le graphique :

![Données de télémétrie de température](media/iot-suite-remote-monitoring-test/telemetry.png)

Dans la page **Appareils**, vous pouvez provisionner des instances de votre nouveau type :

![Afficher la liste des simulations disponibles](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Vous pouvez afficher les données de télémétrie de l’appareil simulé :

![Afficher les données de télémétrie de l’appareil d’éclairage](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Vous pouvez appeler les méthodes **SwitchOn** et **SwitchOff** sur votre appareil :

![Appeler les méthodes de l’appareil d’éclairage](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Ajouter un nouveau type de données de télémétrie

Cette section explique comment modifier un type d’appareil simulé existant pour prendre en charge un nouveau type de données de télémétrie.

### <a name="locate-the-chiller-device-type-files"></a>Rechercher les fichiers du type d’appareil Chiller

Les étapes suivantes vous montrent comment rechercher les fichiers qui définissent l’appareil **Chiller** intégré :

1. Si ce n’est déjà fait, utilisez la commande suivante pour cloner le dépôt GitHub **device-simulation** sur l’ordinateur local :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Chaque type d’appareil a un fichier de modèle JSON et des scripts associés dans le dossier `data/devicemodels`. Les fichiers qui définissent le type d’appareil **Chiller** simulés sont les suivants :

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Spécifier le nouveau type de données de télémétrie

Les étapes suivantes vous montrent comment ajouter un nouveau type **Internal Temperature** (Température interne) au type d’appareil **Chiller** :

1. Ouvrez le fichier **chiller-01.json**.

1. Mettez à jour la valeur **SchemaVersion** comme suit :

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Dans la section **InitialState**, ajoutez les deux définitions suivantes :

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Dans le tableau **Telemetry**, ajoutez la définition suivante :

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Enregistrez le fichier **chiller-01.json**.

1. Ouvrez le fichier **scripts/chiller-01-state.js**.

1. Ajoutez les champs suivants à la variable **state** :

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Ajoutez la ligne suivante à la fonction **main** :

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Enregistrez le fichier **scripts/chiller-01-state.js**.

### <a name="test-the-chiller-device-type"></a>Tester le type d’appareil Chiller

Pour tester le type d’appareil **Chiller** mis à jour, exécutez d’abord une copie locale du service **device-simulation** pour vérifier s’il se comporte normalement. Après avoir testé et débogué localement votre type d’appareil mis à jour, vous pouvez regénérer le conteneur et redéployer le service **device-simulation** sur Azure.

Quand vous exécutez le service **device-simulation** localement, il envoie les données de télémétrie à votre solution de surveillance à distance. Dans la page **Appareils**, vous pouvez provisionner des instances de votre type mis à jour.

Pour tester et déboguer localement vos modifications, consultez la section précédente [Tester le type d’appareil Lightbulb localement](#test-the-lightbulb-device-type-locally).

Pour déployer votre service de simulation d’appareil mis à jour à la machine virtuelle de la solution dans Azure, consultez la section précédente [Tester le type d’appareil Lightbulb localement](#deploy-the-updated-simulator-to-the-cloud).

Dans la page **Appareils**, vous pouvez provisionner des instances de votre type mis à jour :

![Ajouter un refroidisseur mis à jour](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Vous pouvez afficher les données de télémétrie de **température interne** de l’appareil simulé.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Créer un type d’appareil
> * Simuler un comportement personnalisé de l’appareil
> * Ajouter un nouveau type d’appareil au tableau de bord
> * Envoyer des données de télémétrie personnalisées à partir d’un type d’appareil existant

Vous savez maintenant comment personnaliser le service de simulation d’appareil. Nous vous suggérons à présent de découvrir comment [connecter un appareil physique à votre solution de surveillance à distance](iot-suite-connecting-devices-node.md).

Pour plus d’informations sur le développement de la solution de surveillance à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->