---
title: "Simulation d’appareil dans la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel montre comment utiliser le simulateur d’appareil avec la solution préconfigurée de surveillance à distance."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8b84b90e72f8cac1fc1f8a90391b7a5a4f6be1f4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Tester votre solution avec des appareils simulés

Ce didacticiel montre comment personnaliser le microservice de simulateur d’appareil avec la solution préconfigurée de surveillance à distance. Pour illustrer les fonctionnalités de simulateur d’appareil, ce didacticiel utilise deux scénarios dans l’application IoT Contoso.

Dans le premier scénario, Contoso souhaite tester un nouvel appareil d’éclairage connecté. Pour exécuter les tests, vous créez un appareil simulé ayant les caractéristiques suivantes :

*Propriétés*

| Nom                     | Valeurs                      |
| ------------------------ | --------------------------- |
| Couleur                    | White, Red, Blue (Blanc, Rouge, Bleu)            |
| Brightness (Luminosité)               | 0 à 100                    |
| Estimated remaining life (Durée de vie restante estimée) | Compte à rebours à partir de 10 000 heures |

*Télémétrie*

Le tableau suivant présente les données que l’appareil d’éclairage communique au cloud sous la forme d’un flux de données :

| Nom   | Valeurs      |
| ------ | ----------- |
| État | « on » (allumé), « off » (éteint) |
| online | true, false |

> [!NOTE]
> La valeur de télémétrie **online** est obligatoire pour tous les types simulés.

*Méthodes*

Le tableau suivant indique les actions prises en charge par le nouvel appareil :

| Nom        |
| ----------- |
| Switch on (Allumer)   |
| Switch off (Éteindre)  |

*État initial*

Le tableau suivant présente l’état initial de l’appareil :

| Nom                     | Valeurs |
| ------------------------ | -------|
| Initial color (Couleur initiale)            | Blanc  |
| Initial brightness (Luminosité initiale)       | 75     |
| Initial remaining life (Durée de vie restante initiale)   | 10 000 |
| Initial telemetry status (État initial de la télémétrie) | « on » (allumé)   |

Dans le second scénario, vous ajoutez un nouveau type de données de télémétrie à l’appareil **Chiller** (Refroidisseur) existant de Contoso.

Ce didacticiel montre comment utiliser le simulateur d’appareil avec la solution préconfigurée de surveillance à distance :

Ce didacticiel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Créer un type d’appareil
> * Simuler un comportement personnalisé de l’appareil
> * Ajouter un nouveau type d’appareil au tableau de bord
> * Envoyer des données de télémétrie personnalisées à partir d’un type d’appareil existant

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez effectuer le didacticiel [Déployer la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-deploy.md).

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>Service de simulation d’appareil

Le service de simulation d’appareil dans la solution préconfigurée vous permet d’apporter des modifications aux types d’appareil simulé intégrés et de créer des types d’appareil simulé. Vous pouvez utiliser des types d’appareil personnalisés pour tester le comportement de la solution de surveillance à distance avant de connecter vos appareils physiques à la solution.

## <a name="create-a-simulated-device-type"></a>Créer un type d’appareil simulé

Pour créer un type d’appareil dans le microservice de simulation, le plus simple consiste à copier et à modifier un type existant. Les étapes suivantes montrent comment copier l’appareil **Chiller** intégré pour créer un appareil **Lightbulb** :

1. Utilisez la commande suivante pour cloner le dépôt GitHub **device-simulation** sur l’ordinateur local :

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Chaque type d’appareil a un fichier de modèle JSON et des scripts associés dans le dossier `Services/data/devicemodels`. Copiez les fichiers **Chiller** pour créer les fichiers **Lightbulb** (Appareil d’éclairage) comme indiqué dans le tableau suivant :

    | Source                      | Destination                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Définir les caractéristiques du nouveau type d’appareil

Le fichier `lightbulb-01.json` définit les caractéristiques du type, telles que les données de télémétrie qu’il génère et les méthodes qu’il prend en charge. Les étapes suivantes mettent à jour le fichier `lightbulb-01.json` pour définir l’appareil **Lightbulb** :

1. Dans le fichier `lightbulb-01.json`, mettez à jour les métadonnées de l’appareil comme le montre l’extrait de code suivant :

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Dans le fichier `lightbulb-01.json`, mettez à jour la définition de la simulation comme le montre l’extrait de code suivant :

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. Dans le fichier `lightbulb-01.json`, mettez à jour les propriétés du type d’appareil comme le montre l’extrait de code suivant :

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Dans le fichier `lightbulb-01.json`, mettez à jour les définitions des données de télémétrie du type d’appareil comme le montre l’extrait de code suivant :

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. Dans le fichier `lightbulb-01.json`, mettez à jour les méthodes du type d’appareil comme le montre l’extrait de code suivant :

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. Enregistrez le fichier `lightbulb-01.json`.

### <a name="simulate-custom-device-behavior"></a>Simuler un comportement personnalisé de l’appareil

Le fichier `scripts/lightbulb-01-state.js` définit le comportement de simulation du type **Lightbulb**. Les étapes suivantes mettent à jour le fichier `scripts/lightbulb-01-state.js` pour définir le comportement de l’appareil **Lightbulb** :

1. Modifiez la définition de l’état dans le fichier `scripts/lightbulb-01-state.js` comme le montre l’extrait de code suivant :

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Remplacez la fonction **vary** par la fonction **flip** suivante :

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

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Enregistrez le fichier `scripts/lightbulb-01-state.js`.

Le fichier `scripts/SwitchOn-method.js` implémente la méthode **Switch On** dans un appareil **Lightbulb**. Les étapes suivantes mettent à jour le fichier `scripts/SwitchOn-method.js` :

1. Modifiez la définition de l’état dans le fichier `scripts/SwitchOn-method.js` comme le montre l’extrait de code suivant :

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

1. Enregistrez le fichier `scripts/SwitchOn-method.js`.

1. Effectuez une copie du fichier `scripts/SwitchOn-method.js` appelée `scripts/SwitchOff-method.js`.

1. Pour éteindre l’appareil d’éclairage, modifiez la fonction **main** dans le fichier `scripts/SwitchOff-method.js` comme suit :

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Enregistrez le fichier `scripts/SwitchOff-method.js`.

### <a name="test-the-lightbulb-device-type"></a>Tester le type d’appareil Lightbulb

Pour tester le type d’appareil **Lightbulb**, vous pouvez commencer par déterminer s’il se comporte normalement en exécutant une copie locale du service **device-simulation**. Après avoir testé et débogué votre nouveau type d’appareil localement, vous pouvez regénérer le conteneur et redéployer le service **device-simulation** sur Azure.

Pour tester et déboguer localement vos modifications, consultez [Device simulation overview](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) (Vue d’ensemble de la simulation d’appareil).

Configurez le projet pour copier les nouveaux fichiers d’appareil **Lightbulb** dans le répertoire de sortie :

* Si vous utilisez Visual Studio, veillez à ajouter les trois nouveaux fichiers lightbulb que vous avez créés dans la section précédente au projet **Services** dans la solution. Utilisez ensuite **l’Explorateur de solutions** pour les marquer pour la copie vers le répertoire de sortie.

* Si vous utilisez Visual Studio Code, ouvrez le fichier **Services.csproj** et ajoutez les trois nouveaux fichiers lightbulb que vous avez créés dans la section précédente. Consultez les entrées existantes de fichier modèle d’appareil dans le fichier **Services.csproj** en guise d’exemples.

Pour tester le nouvel appareil dans une solution déployée, consultez une des sources suivantes :

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Déploiement de conteneurs à partir d’un compte docker-hub personnalisé)
* [Update a deployed container via manual copy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy) (Mettre à jour un conteneur déployé par le biais d’une copie manuelle)

Dans la page **Appareils**, vous pouvez provisionner des instances de votre nouveau type :

![Afficher la liste des simulations disponibles](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Vous pouvez afficher les données de télémétrie de l’appareil simulé :

![Afficher les données de télémétrie de l’appareil d’éclairage](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Vous pouvez appeler les méthodes **SwitchOn** et **SwitchOff** sur votre appareil :

![Appeler les méthodes de l’appareil d’éclairage](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Pour générer une image Docker avec le nouveau type d’appareil en vue d’un déploiement sur Azure, consultez [Building a customized Docker image](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image) (Génération d’une image Docker personnalisée).

## <a name="add-a-new-telemetry-type"></a>Ajouter un nouveau type de données de télémétrie

Cette section explique comment modifier un type d’appareil simulé existant pour prendre en charge un nouveau type de données de télémétrie.

### <a name="locate-the-chiller-device-type-files"></a>Rechercher les fichiers du type d’appareil Chiller

Les étapes suivantes vous montrent comment rechercher les fichiers qui définissent l’appareil **Chiller** intégré :

1. Si ce n’est déjà fait, utilisez la commande suivante pour cloner le dépôt GitHub **device-simulation** sur l’ordinateur local :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Chaque type d’appareil a un fichier de modèle JSON et des scripts associés dans le dossier `data/devicemodels`. Les fichiers qui définissent le type d’appareil **Chiller** simulés sont les suivants :
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Spécifier le nouveau type de données de télémétrie

Les étapes suivantes vous montrent comment ajouter un nouveau type **Internal Temperature** (Température interne) au type d’appareil **Chiller** :

1. Ouvrez le fichier `chiller-01.json` .

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

1. Enregistrez le fichier `chiller-01.json`.

1. Ouvrez le fichier `scripts/chiller-01-state.js` .

1. Ajoutez les champs suivants à la variable **state** :

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Ajoutez la ligne suivante à la fonction **main** :

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Enregistrez le fichier `scripts/chiller-01-state.js`.

### <a name="test-the-chiller-device-type"></a>Tester le type d’appareil Chiller

Pour tester le type d’appareil **Chiller** mis à jour, vous pouvez commencer par déterminer s’il se comporte normalement en exécutant une copie locale du service **device-simulation**. Après avoir testé et débogué localement votre type d’appareil mis à jour, vous pouvez regénérer le conteneur et redéployer le service **device-simulation** sur Azure.

Quand vous exécutez le service **device-simulation** localement, il envoie les données de télémétrie à votre solution de surveillance à distance. Dans la page **Appareils**, vous pouvez provisionner des instances de votre type mis à jour.

Pour tester et déboguer localement vos modifications, consultez [Running the service with Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) (Exécution du service avec Visual Studio) ou [Build and Run from the command line](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line) (Effectuer une génération et une exécution à partir de la ligne de commande).

Pour tester le nouvel appareil dans une solution déployée, consultez une des sources suivantes :

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Déploiement de conteneurs à partir d’un compte docker-hub personnalisé)
* [Update a deployed container via manual copy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy) (Mettre à jour un conteneur déployé par le biais d’une copie manuelle)

Dans la page **Appareils**, vous pouvez provisionner des instances de votre type mis à jour :

![Ajouter un refroidisseur mis à jour](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Vous pouvez afficher les données de télémétrie de **température interne** de l’appareil simulé.

Pour générer une image Docker avec le nouveau type d’appareil en vue d’un déploiement sur Azure, consultez [Building a customized Docker image](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image) (Génération d’une image Docker personnalisée).

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Créer un type d’appareil
> * Simuler un comportement personnalisé de l’appareil
> * Ajouter un nouveau type d’appareil au tableau de bord
> * Envoyer des données de télémétrie personnalisées à partir d’un type d’appareil existant

L’utilisation du service de simulation d’appareil n’ayant plus de secrets pour vous, nous vous suggérons à présent de découvrir comment [connecter un appareil physique à votre solution de surveillance à distance](iot-suite-connecting-devices-node.md).

Pour plus d’informations sur le développement de la solution de surveillance à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->