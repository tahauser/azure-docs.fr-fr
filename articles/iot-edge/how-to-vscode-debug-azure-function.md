---
title: "Utiliser Visual Studio Code pour déboguer Azure Functions avec Azure IoT Edge | Microsoft Docs"
description: "Déboguer des fonctions Azure Functions C# avec Azure IoT Edge dans VS Code"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4344a450d218a7424cd055cf086c1e4865c9af10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer des fonctions Azure Functions avec Azure IoT Edge | Microsoft Docs

Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour déboguer vos fonctions Azure Functions sur IoT Edge.

## <a name="prerequisites"></a>configuration requise
Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Assurez-vous d’avoir suivi les didacticiels suivants avant de démarrer ce guide.
- [Utiliser Visual Studio Code pour développer et déployer des fonctions Azure Functions sur Azure IoT Edge | Microsoft Docs](how-to-vscode-develop-azure-function.md)

À la fin du didacticiel précédent, vous devez disposer des éléments suivants :
- Un registre Docker local en cours d’exécution sur votre machine de développement. Il est recommandé d’utiliser un registre Docker local pour le prototype et à des fins de test.
- Le fichier `run.csx` avec le dernier code de fonction de filtre.
- Un fichier `deployment.json` mis à jour pour votre module de capteur et le module de fonction du filtre.
- Un runtime Edge en cours d’exécution sur votre machine de développement.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Générer votre module IoT Edge pour le débogage
1. Pour démarrer le débogage, vous devez utiliser **dockerfile.debug** afin de régénérer votre image docker et de redéployer votre solution Edge. Dans l’explorateur de Visual Studio Code, cliquez sur le dossier Docker pour l’ouvrir. Cliquez sur le dossier `linux-x64`, cliquez ensuite avec le bouton droit sur le fichier **Dockerfile.debug** et cliquez sur **Créer image Docker du module IoT Edge**.
3. Dans la fenêtre **Sélectionner le dossier**, accédez au projet **FilterFunction**, puis cliquez sur **Sélectionner un dossier en tant que EXE_DIR**.
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filterfunction:latest`. Si vous effectuez un déploiement dans le registre local, il doit être `localhost:5000/filterfunction:latest`.
5. Distribuez l’image vers votre référentiel Docker. Utilisez la commande **Edge : Envoyer image Docker de module IoT Edge** et entrez l’URL de l’image dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code. Utilisez le même URL d’image que celui utilisé à l’étape précédente.
6. Vous pouvez réutiliser `deployment.json` pour effectuer le redéploiement. Dans la palette de commandes, tapez, puis sélectionnez **Edge : Redémarrer Edge** pour que la fonction de filtre fonctionne avec la version de débogage.

## <a name="start-debugging-in-vs-code"></a>Démarrer le débogage dans Visual Studio Code
1. Accédez à la fenêtre de débogage de Visual Studio Code. Appuyez sur **F5** et sélectionnez **IoT Edge(.Net Core)**
2. Dans `launch.json`, accédez à la section **Déboguer une fonction IoT Edge (.NET Core)** et renseignez `<container_name>` sous `pipeArgs`. Il doit s’agir de `filterfunction` dans ce didacticiel.
3. Accédez à run.csx. Ajoutez un point d’arrêt dans la fonction.
4. Appuyez de nouveau sur **F5**. Puis, sélectionnez le processus à attacher.
5. Dans la fenêtre de débogage de Visual Studio Code, les variables s’affichent dans le volet situé à gauche. 

> [!NOTE]
> L’exemple ci-dessus montre comment déboguer la fonction .Net Core IoT Edge sur les conteneurs. Il repose sur la version de débogage de `Dockerfile.debug`, qui inclut VSDBG (le débogueur de lignes de commande .NET Core) dans votre image de conteneur lors de sa création. Nous vous recommandons d’utiliser ou de personnaliser directement le `Dockerfile` sans VSDBG pour la fonction IoT Edge prête à l’emploi une fois le débogage de votre fonction C# terminé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une fonction Azure Function, vous l’avez déployée dans IoT Edge à des fins de débogage et avez commencé à la déboguer dans Visual Studio Code. Vous pouvez consulter les didacticiels suivants pour en savoir plus sur les autres scénarios pouvant survenir lors du développement d’Azure IoT Edge dans Visual Studio Code. 

> [!div class="nextstepaction"]
> [Développer et déployer un module C# dans VS Code](how-to-vscode-develop-csharp-module.md)

