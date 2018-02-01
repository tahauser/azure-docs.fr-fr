---
title: "Personnaliser la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Cet article fournit des informations sur la façon dont vous pouvez accéder au code source pour la solution préconfigurée de surveillance à distance."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f5d38091b59110859d4376a5cd16a19f24dad65b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Personnaliser la solution préconfigurée de surveillance à distance

Cet article fournit des informations sur la façon dont vous pouvez accéder au code source et personnaliser la solution préconfigurée de surveillance à distance. Cet article aborde les points suivants :

* Les dépôts GitHub qui contiennent le code source et les ressources pour les microservices qui composent la solution préconfigurée.
* Des scénarios de personnalisation courants tels que l’ajout d’un nouveau type d’appareil.

La vidéo suivante présente une vue d’ensemble des options pour personnaliser la solution préconfigurée de surveillance à distance :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

## <a name="project-overview"></a>Vue d’ensemble du projet

### <a name="implementations"></a>Implémentations

La solution de surveillance à distance a des implémentations .NET et Java. Les deux implémentations fournissent des fonctionnalités similaires et s’appuient sur les mêmes services Azure sous-jacents. Vous trouverez les dépôts GitHub de niveau supérieur aux emplacements suivants :

* [Solution .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solution Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Microservices

Si vous êtes intéressé par une fonctionnalité spécifique de la solution, vous pouvez accéder aux dépôts GitHub associés à chaque microservice. Chaque microservice implémente une partie différente des fonctionnalités de la solution. Pour en savoir plus sur l’architecture globale, consultez [Architecture de la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md).

Le tableau suivant récapitule la disponibilité actuelle de chaque microservice en fonction du langage :

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Microservice      | Description | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Interface utilisateur web            | Application web pour la solution de surveillance à distance. Implémente l’interface utilisateur à l’aide du framework React.js. | [Non disponible (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [Non disponible (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Gestionnaire IoT Hub   | Gère la communication avec IoT Hub.        | [Disponible](https://github.com/Azure/iothub-manager-java) | [Disponible](https://github.com/Azure/iothub-manager-dotnet)   |
| Authentification    |  Gère l’intégration d’Azure Active Directory.  | Pas encore disponible | [Disponible](https://github.com/Azure/pcs-auth-dotnet)   |
| Simulation d’appareil | Gère un pool d’appareils simulés. | Pas encore disponible | [Disponible](https://github.com/Azure/device-simulation-dotnet)   |
| Télémétrie         | Met les données de télémétrie de l’appareil à la disposition de l’interface utilisateur. | [Disponible](https://github.com/Azure/device-telemetry-java) | [Disponible](https://github.com/Azure/device-telemetry-dotnet)   |
| Agent de télémétrie   | Analyse le flux des données de télémétrie, stocke les messages d’IoT Hub et génère des alertes en fonction des règles définies.  | [Disponible](https://github.com/Azure/telemetry-agent-java) | [Disponible](https://github.com/Azure/telemetry-agent-dotnet)   |
| Configuration de l’interface utilisateur         | Gère les données de configuration à partir de l’interface utilisateur. | [Disponible](https://github.com/azure/pcs-ui-config-java) | [Disponible](https://github.com/azure/pcs-ui-config-dotnet)   |
| Adaptateur de stockage   |  Gère les interactions avec le service de stockage.   | [Disponible](https://github.com/azure/pcs-storage-adapter-java) | [Disponible](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Proxy inversé     | Expose des ressources privées de façon gérée par le biais d’un point de terminaison unique. | Pas encore disponible | [Disponible](https://github.com/Azure/reverse-proxy-dotnet)   |

La solution Java utilise les microservices de proxy inverse, de simulation et d’authentification .NET. Ces microservices seront remplacés par des versions Java dès qu’elles seront disponibles.

## <a name="presentation-and-visualization"></a>Présentation et visualisation

Les sections suivantes décrivent les options qui permettent de personnaliser la couche de présentation et de visualisations dans la solution de surveillance à distance :

### <a name="change-the-logo-in-the-ui"></a>Changer le logo dans l’interface utilisateur

Le déploiement par défaut utilise le nom de société et le logo Contoso dans l’interface utilisateur. Pour changer ces éléments d’interface utilisateur afin d’afficher les nom et logo de votre société :

1. Utilisez la commande suivante pour cloner le dépôt de l’interface utilisateur web :

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Pour changer le nom de société, ouvrez le fichier `src/common/lang.js` dans un éditeur de texte.

1. Recherchez la ligne suivante dans le fichier :

    ```js
    CONTOSO: 'Contoso',
    ```

1. Remplacez `Contoso` par le nom de votre société. Par exemple :

    ```js
    CONTOSO: 'YourCo',
    ```

1. Enregistrez le fichier .

1. Pour mettre à jour le logo, ajoutez un nouveau fichier SVG au dossier `assets/icons`. Le logo existant est le fichier `assets/icons/Contoso.svg`.

1. Ouvrez le fichier `src/components/layout/leftNav/leftNav.js` dans un éditeur de texte.

1. Recherchez la ligne suivante dans le fichier :

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Remplacez `Contoso.svg` par le nom du fichier de votre logo. Par exemple :

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Recherchez la ligne suivante dans le fichier :

    ```js
    alt="ContosoIcon"
    ```

1. Remplacez `ContosoIcon` par votre texte `alt`. Par exemple :

    ```js
    alt="YourCoIcon"
    ```

1. Enregistrez le fichier .

1. Pour tester les changements, vous pouvez exécuter l’interface `webui` mise à jour sur votre ordinateur local. Pour savoir comment générer et exécuter la solution `webui` localement, consultez [Build, run and test locally](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) (Générer, exécuter et tester localement) dans le fichier lisez-moi du dépôt GitHub `webui`.

1. Pour déployer vos changements, consultez le [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Personnaliser la carte

Pour obtenir des informations détaillées sur des composants de la carte de la solution, consultez la page GitHub [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Personnaliser la carte).

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Autres options de personnalisation

Pour affiner la couche de présentation et de visualisations dans la solution de surveillance à distance, vous pouvez modifier le code. Les dépôts GitHub pertinents sont les suivants :

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Interface utilisateur web de surveillance à distance Azure PCS](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>Connectivité des appareils et streaming

Les sections suivantes décrivent les options qui permettent de personnaliser la couche de la connectivité des appareils et du streaming dans la solution de surveillance à distance. Les [modèles d’appareil](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) décrivent les types d’appareil et les données de télémétrie de la solution. Vous utilisez des modèles d’appareil pour les appareils simulés et les appareils physiques.

Pour découvrir un exemple d’implémentation d’appareil physique, consultez [Connexion de votre appareil à la solution préconfigurée de surveillance à distance](iot-suite-connecting-devices-node.md).

Si vous utilisez un _appareil physique_, vous devez fournir à l’application cliente un modèle d’appareil qui contient la spécification des métadonnées et des données de télémétrie de l’appareil.

Les sections suivantes abordent l’utilisation des modèles d’appareil avec des appareils simulés :

### <a name="add-a-telemetry-type"></a>Ajouter un type de télémétrie

Les types d’appareil dans la solution de démonstration Contoso spécifient les données de télémétrie envoyées par chaque type d’appareil. Pour spécifier les types de données de télémétrie supplémentaires, un appareil peut envoyer des définitions de télémétrie sous forme de métadonnées à la solution. Si vous utilisez ce format, le tableau de bord utilise les données de télémétrie de votre appareil et les méthodes disponibles dynamiquement, sans que vous ayez besoin de modifier l’interface utilisateur. Vous pouvez également modifier la définition du type d’appareil dans la solution.

Pour savoir comment ajouter des données de télémétrie personnalisées au microservice de _simulateur d’appareil_, consultez [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Ajouter un type d’appareil

La solution de démonstration Contoso définit certains exemples de types d’appareil. Cette solution vous permet de définir des types d’appareil personnalisés pour répondre aux exigences de votre application. Par exemple, votre entreprise peut utiliser une passerelle industrielle en tant qu’appareil principal connecté à la solution.

Pour créer une représentation exacte de votre appareil, vous devez modifier l’application qui s’exécute sur celui-ci afin de répondre aux exigences de l’appareil.

Pour savoir comment ajouter un nouveau type d’appareil au microservice de _simulateur d’appareil_, consultez [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>Définir des méthodes personnalisées pour les appareils simulés

Pour savoir comment définir des méthodes personnalisées pour les appareils simulés dans la solution de surveillance à distance, consultez [Device Models](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) (Modèles d’appareil) dans le dépôt GitHub.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Utilisation d’un appareil physique

Pour implémenter des méthodes et travaux sur vos appareils physiques, consultez les articles suivants sur IoT Hub :

* [Comprendre et appeler des méthodes directes à partir d’IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md)
* [Planifier des travaux sur plusieurs appareils](../iot-hub/iot-hub-devguide-jobs.md)

### <a name="other-customization-options"></a>Autres options de personnalisation

Pour affiner la couche de la connectivité des appareils et du streaming dans la solution de surveillance à distance, vous pouvez modifier le code. Les dépôts GitHub pertinents sont les suivants :

* [Télémétrie d’appareil (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [Télémétrie d’appareil (Java)](https://github.com/Azure/device-telemetry-java)
* [Agent de télémétrie (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Agent de télémétrie (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>Traitement et analyse des données

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Pour modifier la couche de traitement et d’analyse des données dans la solution de surveillance à distance, vous pouvez modifier le code. Les dépôts GitHub pertinents sont les suivants :

* [Agent de télémétrie (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Agent de télémétrie (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>Infrastructure

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Pour modifier l’infrastructure dans la solution de surveillance à distance, vous pouvez modifier le code. Les dépôts GitHub pertinents sont les suivants :

* [Gestionnaire IoTHub (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [Gestionnaire IoTHub (Java)](https://github.com/Azure/iothub-manager-java)
* [Adaptateur de stockage (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [Adaptateur de stockage (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour personnaliser la solution préconfigurée.

Pour plus d’informations conceptuelles sur la solution préconfigurée de surveillance à distance, consultez [Architecture de la surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md).

Pour plus d’informations sur la personnalisation de la solution de surveillance à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->