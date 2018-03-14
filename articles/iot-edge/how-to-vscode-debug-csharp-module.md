---
title: "Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge | Microsoft Docs"
description: "Déboguez un module C# avec Azure IoT Edge dans Visual Studio Code."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5ed517cf8d70cd279a55b79ad448709116cf511b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour déboguer vos modules Azure IoT Edge.

## <a name="prerequisites"></a>Prérequis
Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Avant de commencer ce guide, effectuez le didacticiel suivant :
- [Utiliser Visual Studio Code pour développer un module C# avec Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

À la fin du didacticiel précédent, vous devez disposer des éléments suivants :
- Un registre Docker local en cours d’exécution sur votre machine de développement. Ce registre est destiné au prototypage et aux tests.
- Le fichier `Program.cs` avec le code de module de filtre le plus récent.
- Un fichier `deployment.json` mis à jour pour vos modules de capteur et de filtre.
- Un runtime IoT Edge qui s’exécute sur votre machine de développement.

## <a name="build-your-iot-edge-module-for-debugging"></a>Générer votre module IoT Edge pour le débogage
1. Pour démarrer le débogage, utilisez **dockerfile.debug** afin de regénérer votre image Docker et de redéployer votre solution IoT Edge. Dans l’Explorateur Visual Studio Code, sélectionnez le dossier Docker pour l’ouvrir. Ensuite, sélectionnez le dossier **linux-x64**, cliquez avec le bouton droit sur le fichier **Dockerfile.debug** et sélectionnez **Build IoT Edge module Docker image** (Créer une image Docker du module IoT Edge).

    ![Capture d’écran de l’Explorateur Visual Studio Code](./media/how-to-debug-csharp-module/build-debug-image.png)

3. Dans la fenêtre **Sélectionner un dossier**, recherchez ou tapez **./bin/Debug/netcoreapp2.0/publish**. Ensuite, sélectionnez **Select Folder as EXE_DIR** (Sélectionner le dossier en tant que EXE_DIR).
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filtermodule:latest`. Si vous effectuez un déploiement dans le registre local, il doit s’agir de : `localhost:5000/filtermodule:latest`.
5. Distribuez l’image vers votre référentiel Docker. Utilisez la commande **Edge: Push IoT Edge module Docker image** (Edge : Transmettre l’image Docker du module IoT Edge) et entrez l’URL de l’image dans la zone de texte contextuelle en haut de la fenêtre de VS Code. Utilisez la même URL d’image qu’à l’étape précédente.
6. Vous pouvez réutiliser `deployment.json` pour effectuer le redéploiement. Dans la palette de commandes, tapez et sélectionnez **Edge : Restart Edge** (Edge : Redémarrer Edge) pour que le module de filtre s’exécute avec la version de débogage.

## <a name="start-debugging-in-vs-code"></a>Démarrer le débogage dans Visual Studio Code
1. Accédez à la fenêtre de débogage de Visual Studio Code. Appuyez sur **F5** et sélectionnez **IoT Edge(.NET Core)**.

    ![Capture d’écran de la fenêtre de débogage de Visual Studio Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. Dans `launch.json`, accédez à la section **Debug IoT Edge Custom Module (.NET Core)** (Déboguer le module personnalisé IoT Edge). Sous **pipeArgs**, renseignez `<container_name>`. Il doit s’agir de `filtermodule` dans ce didacticiel.

    ![Capture d’écran de VS Code launch.json](./media/how-to-debug-csharp-module/add-container-name.png)

3. Accédez à **Program.cs**. Ajoutez un point d’arrêt dans `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Appuyez à nouveau sur **F5** et sélectionnez le processus à attacher. Dans ce didacticiel, le nom du processus doit être `FilterModule.dll`.

    ![Capture d’écran de la fenêtre de débogage de Visual Studio Code](./media/how-to-debug-csharp-module/attach-process.png)

5. Dans la fenêtre de débogage de Visual Studio Code, les variables s’affichent dans le volet de gauche. 

> [!NOTE]
> L’exemple précédent montre comment déboguer des modules .NET Core IoT Edge sur des conteneurs. Il repose sur la version de débogage de `Dockerfile.debug`, qui inclut VSDBG (le débogueur en ligne de commande .NET Core) dans votre image de conteneur lors de sa création. Une fois le débogage de vos modules C# terminé, nous vous recommandons d’utiliser ou de personnaliser directement `Dockerfile` sans VSDBG pour les modules IoT Edge prêts à l’emploi.

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge et vous l’avez déployé pour le débogage. Vous avez commencé à le déboguer dans Visual Studio Code. Pour en savoir plus sur les autres scénarios quand vous développez Azure IoT Edge dans Visual Studio Code, consultez : 

> [!div class="nextstepaction"]
> [Développer et déployer un module C# dans Visual Studio Code](how-to-vscode-develop-csharp-module.md)
