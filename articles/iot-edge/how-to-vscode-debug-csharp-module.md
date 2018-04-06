---
title: Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge | Microsoft Docs
description: Déboguez un module C# avec Azure IoT Edge dans Visual Studio Code.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c2a1acd2c249bdbc92119bc92f055b095f318f00
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour déboguer vos modules Azure IoT Edge.

## <a name="prerequisites"></a>Prérequis

Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Avant de commencer ce guide, effectuez le didacticiel suivant :
- [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

À la fin du didacticiel précédent, vous devez disposer des éléments suivants :
- Un registre Docker local en cours d’exécution sur votre machine de développement. Il est recommandé d’utiliser un registre Docker local pour le prototype et à des fins de test. Vous pouvez mettre à jour le registre de conteneurs dans le fichier `module.json` de chaque dossier de module.
- Un espace de travail de projet de la solution IoT Edge comportant un sous-dossier de module C#.
- Le fichier `Program.cs` avec la version la plus récente du code du module.
- Un runtime Edge en cours d’exécution sur votre machine de développement.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Générer un module C# IoT Edge pour le débogage
1. Pour commencer le débogage, utilisez **Dockerfile.amd64.debug** afin de régénérer votre image docker et de redéployer votre solution Edge. Dans l’explorateur VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de fonction en ajoutant `.debug` à la fin.

2. Regénérez votre solution. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Edge : générer la solution IoT Edge**.

3. Dans Azure IoT Hub Device Explorer, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez `deployment.json` sous le dossier `config`. Le déploiement est créé avec un ID de déploiement dans le terminal intégré de VS Code.

> [!NOTE]
> Vous pouvez consulter l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker images` dans le terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Commencer le débogage du module C# dans VS Code
1. VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lors de la création d’une nouvelle solution IoT Edge. Il sera mis à jour chaque fois que vous ajouterez un nouveau module prenant en charge le débogage. Accédez à l’affichage de débogage et sélectionnez le fichier de configuration de débogage correspondant.
    ![Sélectionnez la configuration de débogage](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accédez à `program.cs`. Ajoutez un point d’arrêt dans ce fichier.

3. Cliquez sur le bouton Démarrer le débogage ou appuyez sur **F5**, puis sélectionnez le processus à attacher.

4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 

> [!NOTE]
> L’exemple précédent montre comment déboguer des modules .NET Core IoT Edge sur des conteneurs. Il repose sur la version de débogage de `Dockerfile.debug`, qui inclut VSDBG (le débogueur en ligne de commande .NET Core) dans votre image de conteneur lors de sa création. Une fois le débogage de vos modules C# terminé, nous vous recommandons d’utiliser ou de personnaliser directement `Dockerfile` sans VSDBG pour les modules IoT Edge prêts à l’emploi.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge et vous l’avez déployé pour le débogage. Vous avez commencé à le déboguer dans Visual Studio Code. Pour en savoir plus sur les autres scénarios quand vous développez Azure IoT Edge dans Visual Studio Code, consultez : 

> [!div class="nextstepaction"]
> [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

