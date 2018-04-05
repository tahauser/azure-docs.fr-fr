---
title: Traducteur IoT DevKit utilisant une fonction Azure et Cognitive Services | Microsoft Docs
description: Utilisez un microphone sur IoT DevKit pour recevoir le message vocal, et Azure Cognitive Services pour le traiter et afficher un texte traduit en anglais.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: d17f117d71eb0616201df18aea6dc48749ae24a8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Utiliser IoT DevKit AZ3166 avec une fonction Azure et Cognitive Services pour créer un traducteur

Dans cet article, vous découvrez comment transformer IoT DevKit en traducteur à l’aide [d’Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Il enregistre votre voix et la convertit en un texte traduit en anglais affiché sur l’écran du DevKit.

[IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide de l’[extension Visual Studio Code pour Arduino](https://aka.ms/arduino). Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Microsoft Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) pour :

* Connecter votre DevKit au Wi-Fi
* Préparer l’environnement de développement

Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire via l’une de ces deux méthodes :

* Activez un [compte d’évaluation Microsoft Azure gratuit pendant 30 jours](https://azure.microsoft.com/en-us/free/).
* Réclamez votre [crédit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si vous êtes abonné à MSDN ou Visual Studio

## <a name="step-1-open-the-project-folder"></a>Étape 1. Ouvrir le dossier de projet

### <a name="a-start-vs-code"></a>R. Démarrer VS Code

- Assurez-vous que votre DevKit n’est pas connecté à votre ordinateur.
- Démarrer VS Code
- Connectez le kit DevKit à votre ordinateur.

VS Code trouve automatiquement le kit DevKit et ouvre une page de présentation :

![Page d’introduction](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_start.png)

### <a name="b-open-the-arduino-examples-folder"></a>B. Ouvrir le dossier des exemples Arduino

Développez la section à gauche **EXEMPLES ARDUINO > Exemples pour MXCHIP AZ3166 > AzureIoT** et sélectionnez **DevKitTranslator** (DevKitTranslator). Une nouvelle fenêtre VS Code s’ouvre avec le dossier de projet DEVKITTRANSLATOR qu’elle contient.

![Exemples IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

S’il vous arrive de fermer le volet par inadvertance, vous pouvez le rouvrir. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="step-2-provision-azure-services"></a>Étape 2. Approvisionner les services Azure

Dans la fenêtre de la solution, tapez `Ctrl+P` (macOS : `Cmd+P`), puis entrez `task cloud-provision`.

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement de tous les services Azure nécessaires :

![Tâche d’approvisionnement du cloud](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Étape 3. Déployer Azure Functions

Utilisez `Ctrl+P` (macOS : `Cmd+P`) pour exécuter `task cloud-deploy` et déployer le code Azure Functions. Ce processus prend généralement 2 à 5 minutes :

![Tâche de déploiement du cloud](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Une fois que la fonction Azure effectue correctement le déploiement, remplissez le fichier azure_config.h avec le nom de l’application de fonction. Vous pouvez accéder au [portail Azure](https://portal.azure.com/) pour le rechercher :

![Rechercher le nom de l’application de fonction Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Si la fonction Azure ne s’exécute pas correctement, consultez cette section de [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) pour résoudre le problème.

## <a name="step-4-build-and-upload-the-device-code"></a>Étape 4. Générer et charger le code de l’appareil

1. Utilisez `Ctrl+P` (macOS : `Cmd+P`) pour exécuter `task config-device-connection`.

2. Le terminal vous demande si vous souhaitez utiliser la chaîne de connexion qui effectue une opération d’extraction à partir de l’étape `task cloud-provision`. Vous pouvez également entrer votre propre chaîne de connexion d’appareil en sélectionnant **« Créer nouveau... »**

3. Le terminal vous invite à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche l’ID du kit DevKit et « Configuration ».
  ![Vérification et chargement de l’ébauche de projet Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Après avoir terminé `task config-device-connection`, cliquez sur `F1` pour charger les commandes VS Code et sélectionnez `Arduino: Upload`. Ensuite, VS Code démarre la vérification et le chargement de l’ébauche de projet Arduino : ![vérification et chargement de l’ébauche de projet Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

Le DevKit redémarre et commence à exécuter le code.

## <a name="test-the-project"></a>Tester le projet

Après l’initialisation de l’application, suivez les instructions sur l’écran de DevKit. La langue source par défaut est le chinois.

Pour sélectionner une autre langue pour la traduction :

1. Appuyez sur le bouton A pour entrer dans le mode de configuration.
2. Appuyez sur le bouton B pour faire défiler toutes les langues source prises en charge.
3. Appuyez sur le bouton A pour confirmer votre choix de langue source.
4. Appuyez sur le bouton B et maintenez-le enfoncé tout en parlant, puis relâchez-le pour lancer la traduction.
5. Le texte traduit en anglais s’affiche à l’écran.

![Faire défiler pour sélectionner la langue](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Résultat de la traduction](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Sur l’écran affichant le résultat de la traduction, vous pouvez :

- Appuyer sur les boutons A et B pour faire défiler et sélectionner la langue source.
- Appuyer sur le bouton B pour parler, relâcher le bouton pour envoyer le message vocal et obtenir le texte traduit

## <a name="how-it-works"></a>Fonctionnement

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

L’ébauche de projet Arduino enregistre votre voix, puis envoie une requête HTTP pour déclencher Azure Functions. Azure Functions appelle l’API de traducteur de discours de service cognitif pour effectuer la traduction. Une fois qu’Azure Functions obtient le texte traduit, il envoie un message cloud-à-appareil à l’appareil. La traduction s’affiche ensuite sur l’écran.

## <a name="change-device-id"></a>Modifier l’ID de l’appareil

L’ID d’appareil par défaut inscrit dans Azure IoT Hub est **AZ3166**. Si vous voulez le modifier, suivez les [instructions ici](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) si ou contactez-nous via les canaux ci-dessous :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Utilisez maintenant IoT Devkit comme traducteur à l’aide d’une fonction Azure et de Cognitive Services. Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Utiliser une tâche Visual Studio Code pour automatiser les approvisionnements du cloud
> * Configurer la chaîne de connexion d’appareil Azure IoT
> * Déployer une fonction Azure
> * Tester la traduction d’un message vocal

Passez aux autres tutoriels pour apprendre à :

> [!div class="nextstepaction"]
> [Connecter IoT DevKit AZ3166 à Azure IoT Suite pour la surveillance à distance](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)