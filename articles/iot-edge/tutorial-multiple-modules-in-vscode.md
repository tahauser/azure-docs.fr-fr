---
title: Utilisation de plusieurs modules IoT Edge dans Visual Studio Code | Microsoft Docs
description: Déployer Azure Machine Learning en tant que module sur un appareil Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0ea2dc723c674e7119b6ef38771a73ff4c11e98d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code - version préliminaire
Vous pouvez utiliser Visual Studio Code pour développer votre solution IoT Edge avec plusieurs modules. Ce didacticiel vous présente la création, la mise à jour et le déploiement d’une solution IoT Edge qui dirige simplement les données de capteur sur l’appareil IoT Edge simulé dans Visual Studio Code. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une solution IoT Edge
> * Utiliser VS Code pour ajouter un nouveau module à votre solution IoT Edge en cours d’exécution. 
> * Déployer la solution IoT Edge (plusieurs modules) sur votre appareil IoT Edge
> * Afficher les données générées

## <a name="prerequisites"></a>Prérequis

* Exécuter les didacticiels ci-dessous
  * [Déployer le module C#](tutorial-csharp-module.md)
  * [Déployer la fonction C#](tutorial-deploy-function.md)
  * [Déployer le module Python](tutorial-python-module.md)
* [Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) avec intégration de l’explorateur pour la gestion des images et des conteneurs.


## <a name="prepare-your-first-iot-edge-solution"></a>Préparer votre première solution IoT Edge
1. Dans la palette de commande VS Code, saisissez et exécutez la commande **Edge : nouvelle solution IoT Edge**. Ensuite, sélectionnez le dossier de votre espace de travail, fournissez le nom de la solution (le nom par défaut est **EdgeSolution**), et créez un module C# (**SampleModule**) en tant que premier module utilisateur dans cette solution. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (`localhost:5000/<first module name>`). Vous pouvez par ailleurs le remplacer par une instance Azure Container Registry ou Docker Hub.

> [!NOTE]
> Si vous utilisez un registre Docker local, assurez-vous qu’il est exécuté en saisissant la commande `docker run -d -p 5000:5000 --restart=always --name registry registry:2` dans la fenêtre de votre console.

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Il affiche un dossier `modules`, un dossier `.vscode` et un fichier modèle du manifeste de déploiement dans le dossier racine. Vous pouvez observer les configurations de débogage dans le dossier `.vscode`. L’ensemble des codes du module utilisateur sont des sous-dossiers du dossier `modules`. L’élément `deployment.template.json` est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir de l’élément `module.json`, qui existe dans chaque dossier de module.

3. Ajoutez votre second module dans ce projet de solution. Cette fois, saisissez et exécutez la commande **Edge : ajouter le module IoT Edge**, puis sélectionnez le fichier modèle de déploiement à mettre à jour. Ensuite, sélectionnez une **Fonction Azure - C#** portant le nom **SampleFunction** et son référentiel d’image Docker à ajouter.

4. Désormais, votre première solution IoT Edge comportant 2 modules de base est prête. Le module C# par défaut agit comme un module de message de canal, tandis que la fonction C# agit comme une fonction de message de canal. Dans l’élément `deployment.template.json`, vous verrez que cette solution comporte 3 modules. Le message sera généré à partir du module `tempSensor` et sera dirigé directement via `SampleModule` et `SampleFunction`, puis envoyé vers votre IoT hub. Mettez à jour les itinéraires de ces modules à l’aide du contenu ci-dessous. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Enregistrez ce fichier.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Développer et déployer votre solution IoT Edge
1. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Edge : générer la solution IoT Edge**. En fonction du fichier `module.json` de chaque module, cette commande procède à une vérification et commence à développer, placer en conteneur et envoyer chaque image Docker du module. Ensuite, elle analyse la valeur requise sur le fichier `deployment.template.json`, génère l’élément `deployment.json` avec la valeur réelle dans le dossier `config`. L’avancement de la génération s’affiche sur le terminal intégré à VS Code.

2. Dans Azure IoT Hub Device Explorer, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez `deployment.json` sous le dossier `config`. Le déploiement est créé avec un ID de déploiement dans le terminal intégré de VS Code.

3. Si vous [simulez un appareil IoT Edge](tutorial-simulate-device-linux.md) sur votre machine de développement. Vous verrez que l’ensemble des conteneurs d’image du module démarreront dans quelques minutes.

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller les données reçues par IoT Hub, sélectionnez **Affichage** > **Palette de commandes** et recherchez **IoT: Start monitoring D2C message**. 
2. Pour arrêter de surveiller les données, utilisez la commande **IoT: Stop monitoring D2C message** dans la palette de commandes. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une solution IoT Edge avec un module C#, vous avez ensuite ajouté un module de fonction, mis à jour les itinéraires pour la solution, développée et déployée sur votre appareil IoT Edge simulé. Vous pouvez consulter les didacticiels suivants pour en savoir plus sur les autres scénarios pouvant survenir lors du développement d’Azure IoT Edge dans VS Code.

> [!div class="nextstepaction"]
> [Déboguer un module C# dans VS Code](how-to-vscode-debug-csharp-module.md)
> [Déboguer une fonction C# dans VS Code](how-to-vscode-debug-azure-function.md)