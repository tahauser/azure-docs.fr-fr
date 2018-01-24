---
title: "Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge | Microsoft Docs"
description: "Déboguer un module C# avec Azure IoT Edge dans Visual Studio Code"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 01d321dce439e153b494dfd0de52c100dab78f39
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour déboguer vos modules IoT Edge.

## <a name="prerequisites"></a>configuration requise
Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Vérifié que vous avez suivi les didacticiels suivants avant de démarrer ce guide.
- [Utiliser Visual Studio Code pour développer un module C# avec Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

À la fin du didacticiel précédent, vous devez disposer des éléments suivants :
- Un registre Docker local en cours d’exécution sur votre machine de développement. Il est recommandé d’utiliser un registre Docker local pour le prototype et à des fins de test.
- Le fichier `Program.cs` avec le code de fonction de filtre le plus récent.
- Un fichier `deployment.json` mis à jour pour votre module de capteur et le module de filtre.
- Un runtime Edge en cours d’exécution sur votre machine de développement.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Générer votre module IoT Edge pour le débogage
1. Pour démarrer le débogage, vous devez utiliser **dockerfile.debug** afin de régénérer votre image docker et de redéployer votre solution Edge. Dans l’explorateur de Visual Studio Code, cliquez sur le dossier Docker pour l’ouvrir. Cliquez sur le dossier `linux-x64`, cliquez ensuite avec le bouton droit sur le fichier **Dockerfile.debug** et cliquez sur **Créer image Docker du module IoT Edge**.
3. Dans la fenêtre **Sélectionner un dossier**, recherchez ou saisissez `./bin/Debug/netcoreapp2.0/publish`. Cliquez sur **Sélectionnez dossier comme EXE_DIR**.
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filtermodule:latest`. Si vous effectuez un déploiement dans le registre local, il doit être `localhost:5000/filtermodule:latest`.
5. Distribuez l’image vers votre référentiel Docker. Utilisez la commande **Edge : Transmettre l’image Docker du module IoT Edge** et entrez l’URL de l’image dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code. Utilisez la même URL d’image qu’à l’étape précédente.
6. Vous pouvez réutiliser `deployment.json` pour effectuer le redéploiement. Dans la palette de commandes, saisissez et sélectionnez **Edge : Redémarrer Edge** pour que le module de filtre fonctionne avec la version de débogage.

## <a name="start-debugging-in-vs-code"></a>Démarrer le débogage dans Visual Studio Code
1. Accédez à la fenêtre de débogage de Visual Studio Code. Appuyez sur **F5** et sélectionnez **IoT Edge(.Net Core)**.
2. Dans `launch.json`, accédez à la section **Déboguer un module personnalisé IoT Edge (.NET Core)** et renseignez `<container_name>` sous `pipeArgs`. Il doit s’agir de `filtermodule` dans ce didacticiel.
3. Accédez au fichier Program.cs. Ajoutez un point d’arrêt dans `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Appuyez de nouveau sur **F5**. Ensuite, sélectionnez le processus à attacher. Dans ce didacticiel, le nom du processus doit être `FilterModule.dll`.
5. Dans la fenêtre de débogage de Visual Studio Code, les variables s’affichent dans le volet situé à gauche. 

> [!NOTE]
> L’exemple ci-dessus montre comment déboguer la fonction .Net Core IoT Edge sur les conteneurs. Il repose sur la version de débogage de `Dockerfile.debug`, qui inclut VSDBG (le débogueur de lignes de commande .NET Core) dans votre image de conteneur lors de sa création. Nous vous recommandons d’utiliser ou de personnaliser directement le `Dockerfile` sans VSDBG pour les modules IoT Edge prêts à l’emploi une fois le débogage de vos modules C# terminé.

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge, vous l’avez déployé à des fins de débogage et vous avez commencé à le déboguer dans Visual Studio Code. Vous pouvez consulter les didacticiels suivants pour en savoir plus sur les autres scénarios pouvant survenir lors du développement d’Azure IoT Edge dans Visual Studio Code. 

> [!div class="nextstepaction"]
> [Développer et déployer un module C# dans Visual Studio Code](how-to-vscode-develop-csharp-module.md)
