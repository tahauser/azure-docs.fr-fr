---
title: Récupérer un message Twitter avec Azure Functions | Microsoft Docs
description: Utilisez le capteur de mouvement pour détecter les secousses et utilisez Azure Functions pour rechercher un tweet aléatoire avec un mot-dièse que vous spécifiez.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: a84393c5c53b8f8e4a8b688a462f433b2d611b0e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Secouez, secouez pour récupérer un tweet - Récupérez un message Twitter avec Azure Functions !

Dans ce projet, vous allez apprendre à utiliser le capteur de mouvement pour déclencher un événement à l’aide d’Azure Functions. L’application récupère un tweet aléatoire avec un #mot-dièse que vous configurez dans votre ébauche de projet Arduino. Le tweet s’affiche sur l’écran DevKit.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) pour :

* Connecter votre DevKit au Wi-Fi.
* Préparer l’environnement de développement.

Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire via l’une de ces méthodes :

* Activez un [compte d’évaluation Microsoft Azure gratuit pendant 30 jours](https://azure.microsoft.com/en-us/free/).
* Réclamez votre [crédit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si vous êtes abonné à MSDN ou Visual Studio

## <a name="open-the-project-folder"></a>Ouvrir le dossier de projet

### <a name="start-vs-code"></a>Démarrer VS Code

- Assurez-vous que votre DevKit n’est **pas** connecté à votre ordinateur.
- Démarrez VS Code.
- Connectez le kit DevKit à votre ordinateur.

VS Code trouve automatiquement votre DevKit et affiche une page de présentation :

![mini-solution-vscode](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_start.png)

> [!NOTE]
> Quand vous lancez VS Code, vous pouvez recevoir un message d’erreur indiquant que l’IDE Arduino ou le package de la carte associée ne peut pas être trouvé. Si cette erreur se produit, fermez VS Code et lancez une nouvelle fois l’IDE Arduino. VS Code devrait maintenant localiser correctement le chemin de l’IDE Arduino.

### <a name="open-arduino-examples-folder"></a>Ouvrir le dossier des exemples Arduino

Développez la section **EXEMPLES ARDUINO** à gauche, accédez à **Exemples pour MXCHIP AZ3166 > AzureIoT** et sélectionnez **ShakeShake**. Une nouvelle fenêtre VS Code s’ouvre avec le dossier de projet qu’elle contient.

![mini-solution-exemples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

S’il vous arrive de fermer le volet par inadvertance, vous pouvez le rouvrir. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="provision-azure-services"></a>Approvisionner les services Azure

Dans la fenêtre de la solution, exécutez votre tâche par `Ctrl+P` (macOS : `Cmd+P`) en entrant `task cloud-provision`.

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement des services Azure nécessaires :

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Si la page se bloque dans l’état de chargement lorsque vous tentez de vous connecter à Azure, consultez cette [étape de FAQ] ({{"/docs/faq/#page-hangs-when-log-in-azure" | 
 
## <a name="modify-the-hashtag"></a>Modifier le #mot-dièse

Ouvrez `ShakeShake.ino` et recherchez cette ligne de code :

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Remplacez la chaîne `iot` entre des accolades par votre mot-dièse préféré. DevKit récupère ultérieurement un tweet aléatoire qui inclut le mot-dièse que vous spécifiez dans cette étape.

## <a name="deploy-azure-functions"></a>Déployer Azure Functions

Utilisez `Ctrl+P` (macOS: `Cmd+P`) pour exécuter `task cloud-deploy` et commencer à déployer le code Azure Functions :

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Parfois, la fonction Azure peut ne pas fonctionner correctement. Pour résoudre ce problème lorsqu’il se produit, consultez cette [étape de FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

### <a name="windows"></a>Windows

1. Utilisez `Ctrl+P` pour exécuter `task device-upload`.
2. Le terminal vous invite à passer en mode de configuration. Pour ce faire :

   * Maintenez enfoncé le bouton A
   * Enfoncez et relâchez le bouton de réinitialisation.

3. L’écran affiche l’ID du kit DevKit et « Configuration ».
4. Cela définit la chaîne de connexion qui est récupérée à l’étape `task cloud-provision`.
5. VS Code commence ensuite à vérifier et charger l’ébauche de projet Arduino sur votre DevKit : ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. Le DevKit redémarre et commence à exécuter le code.

> [!NOTE]
> Vous risquez d’obtenir un message d’erreur « Error: AZ3166: Unknown package » (Erreur : AZ3166 : Package inconnu). Cette erreur se produit lorsque l’index du package de la carte n’est pas actualisé correctement. Pour résoudre ce problème, consultez cette [étape de FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Placez le DevKit en mode de configuration : maintenez enfoncé le bouton A, puis enfoncez et relâchez le bouton de réinitialisation. L’écran affiche « Configuration ».
2. Utilisez `Cmd+P` pour exécuter `task device-upload` afin de définir la chaîne de connexion qui est récupérée à l’étape `task cloud-provision`.
3. VS Code commence ensuite à vérifier et charger l’ébauche de projet Arduino sur votre DevKit : ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. Le DevKit redémarre et commence à exécuter le code.

> [!NOTE]
> Vous risquez d’obtenir un message d’erreur « Error: AZ3166: Unknown package » (Erreur : AZ3166 : Package inconnu). Cette erreur se produit lorsque l’index du package de la carte n’est pas actualisé correctement. Pour résoudre ce problème, consultez cette [étape de FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Tester le projet

Après l’initialisation de l’application, cliquez sur le bouton A, puis relâchez-le et secouez doucement la carte DevKit. Cette action récupère un tweet aléatoire, qui contient le #mot-dièse que vous avez spécifié précédemment. Après quelques secondes, un tweet s’affiche sur votre écran DevKit :

### <a name="arduino-application-initializing"></a>Initialisation de l’application Arduino en cours...
![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Appuyez sur A pour secouer...
![Press-A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Prêt à secouer...
![Ready-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Traitement en cours...
![Traitement en cours](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Appuyez sur B pour lire...
![Press-B-to-read](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Afficher un tweet aléatoire...
![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Appuyez à nouveau sur le bouton A, puis secouez pour récupérer un autre tweet.
- Appuyez sur le bouton B pour parcourir le tweet.

## <a name="how-it-works"></a>Fonctionnement

![diagramme](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

L’ébauche de projet Arduino envoie un événement à Azure IoT Hub. Cet événement déclenche l’application Azure Functions. L’application Azure Functions contient la logique pour se connecter à l’API Twitter et récupérer un tweet. Ensuite, elle inclut dans un wrapper le texte du tweet dans un message cloud-à-appareil et le renvoie à l’appareil.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Facultatif : utiliser votre propre jeton de porteur Twitter

À des fins de test, cet exemple de projet utilise un jeton de porteur Twitter préconfiguré. Toutefois, il existe une [limite de fréquence](https://dev.twitter.com/rest/reference/get/search/tweets) pour chaque compte Twitter. Si vous souhaitez utiliser votre propre jeton, procédez comme suit :

1. Accédez au [portail des développeurs Twitter](https://dev.twitter.com/) pour inscrire une nouvelle application Twitter.

2. [Récupérez la clé du client et les secrets du client](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) de votre application.

3. Utilisez [un utilitaire](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) pour générer un jeton de porteur Twitter à partir de ces deux clés.

4. Dans le [portail Azure](https://portal.azure.com/){:target="_blank"}, accédez au **Groupe de ressources** et recherchez la fonction Azure (Type : App Service) pour votre projet « Shake, Shake ». Le nom contient toujours la chaîne « shake... ».
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Mettez à jour le code de `run.csx` dans **Fonctions > shakeshake-cs** avec votre propre jeton :
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Enregistrez le fichier, puis cliquez sur **Exécuter**.


## <a name="problems-and-feedback"></a>Problèmes et commentaires

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>L’écran affiche « No tweets » (Aucun tweet) alors que chaque étape s’est exécutée correctement

Cette condition se produit normalement la première fois que vous déployez et exécutez l’exemple, car l’application de fonction requiert quelques secondes à une minute pour le démarrage à froid de l’application. Ou bien, lorsque vous exécutez le code, certains blocages entrainent un redémarrage de l’application. Lorsque cette condition se produit, l’application de l’appareil peut obtenir un délai d’expiration pour récupérer le tweet. Dans ce cas, vous pouvez essayer l’une des méthodes suivantes (ou les deux) pour résoudre ce problème :

1. Cliquez sur le bouton de réinitialisation de DevKit pour exécuter à nouveau l’application de l’appareil.

2. Dans le [portail Azure](https://portal.azure.com/), recherchez l’application Azure Functions que vous avez créée et redémarrez-la : ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Commentaires

Si vous rencontrez d’autres problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contactez-nous via les canaux ci-dessous :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un appareil DevKit à votre solution Azure IoT Suite et à récupérer un tweet, nous vous suggérons les étapes suivantes :

* [Vue d’ensemble d’Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)